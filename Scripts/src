import cv2
import numpy as np
import pickle
import gspread
from oauth2client.service_account import ServiceAccountCredentials
from insightface.app import FaceAnalysis
from datetime import datetime
from collections import deque

# ==========================================
# 1. КОНФИГУРАЦИЯ (ПУТИ И ID)
# ==========================================
# Используем r"..." для путей Windows
PATH_EMBEDDINGS = r"C:\Проект по видеонаблюдению\student_embeddings.pkl"
PATH_CREDS = r"C:\Проект по видеонаблюдению\credentials.json"

# Твой ID из ссылки, которую ты прислал
SPREADSHEET_ID = "1obtxCfcjQARxzaUwH_XTFo_gm5ltZ5MOgyQFLgfWqlo"


# ==========================================
# 2. КЛАССИЧЕСКАЯ ЛОГИКА (ВЕРИФИКАТОР)
# ==========================================
class CosineSimilarityVerifier:
    def __init__(self, gold_embeddings, threshold=0.15):  # Порог как в твоем блокноте
        self.gold_embeddings = gold_embeddings
        self.threshold = threshold

    def verify(self, face_embedding):
        best_name = "Unknown"
        max_sim = -1

        # Скалярное произведение для нормализованных векторов
        for name, gold_vec in self.gold_embeddings.items():
            sim = np.dot(face_embedding, gold_vec)
            if sim > max_sim:
                max_sim = sim
                best_name = name

        if max_sim < self.threshold:
            return "Unknown", max_sim
        return best_name, max_sim


class AttendanceLogger:
    def __init__(self, worksheet):
        self.worksheet = worksheet
        self.detected_today = set()
        self.buffer = {}  # Для сглаживания (Temporal Smoothing)

    def process(self, name, score):
        if name == "Unknown" or name in self.detected_today:
            return

        # Логика подтверждения: 7 из 10 последних кадров
        if name not in self.buffer:
            self.buffer[name] = deque(maxlen=10)

        self.buffer[name].append(1)

        if sum(self.buffer[name]) >= 7:
            timestamp = datetime.now().strftime("%H:%M:%S")
            try:
                # Записываем: Имя, Время, Скор
                self.worksheet.append_row([name, timestamp, f"{score:.2f}"])
                self.detected_today.add(name)
                print(f"✅ ЗАПИСАНО В ТАБЛИЦУ: {name} | Время: {timestamp}")
            except Exception as e:
                print(f"⚠️ Ошибка записи в Google: {e}")


# ==========================================
# 3. ЗАПУСК СИСТЕМЫ
# ==========================================

print("📡 Авторизация в Google Sheets...")
try:
    scope = ["https://spreadsheets.google.com/feeds", "https://www.googleapis.com/auth/drive"]
    creds = ServiceAccountCredentials.from_json_keyfile_name(PATH_CREDS, scope)
    client = gspread.authorize(creds)
    # Используем твой ID напрямую
    worksheet = client.open_by_key(SPREADSHEET_ID).sheet1
    print("✅ Доступ к таблице получен!")
except Exception as e:
    print(f"❌ ОШИБКА АВТОРИЗАЦИИ: {e}")
    exit()

print("🚀 Загрузка нейросети (Buffalo_L)...")
# Если тормозит, замени det_size=(640, 640) на (320, 320)
app = FaceAnalysis(name='buffalo_l', providers=['CUDAExecutionProvider', 'CPUExecutionProvider'])
app.prepare(ctx_id=0, det_size=(640, 640))

print("🧠 Загрузка базы студентов...")
with open(PATH_EMBEDDINGS, 'rb') as f:
    gold_db = pickle.load(f)

verifier = CosineSimilarityVerifier(gold_db)
logger = AttendanceLogger(worksheet)

# Подключаем веб-камеру
cap = cv2.VideoCapture(1)
cap.set(cv2.CAP_PROP_FRAME_WIDTH, 1920)
cap.set(cv2.CAP_PROP_FRAME_HEIGHT, 1080)

print("\n📸 КАМЕРА ЗАПУЩЕНА. Нажми 'q' для выхода.")

while True:
    ret, frame = cap.read()
    if not ret: break

    # Поиск лиц в кадре
    faces = app.get(frame)

    for face in faces:
        name, score = verifier.verify(face.normed_embedding)

        # Обработка записи (сглаживание)
        logger.process(name, score)

        # Отрисовка рамки и имени
        box = face.bbox.astype(int)
        color = (0, 255, 0) if name != "Unknown" else (0, 0, 255)

        cv2.rectangle(frame, (box[0], box[1]), (box[2], box[3]), color, 2)
        label = f"{name}: {score:.2f}"
        cv2.putText(frame, label, (box[0], box[1] - 10),
                    cv2.FONT_HERSHEY_SIMPLEX, 0.6, color, 2)

    # Показываем результат
    cv2.imshow('Attendance System 1080p', frame)

    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

cap.release()
cv2.destroyAllWindows()
print("🏁 Система остановлена.")