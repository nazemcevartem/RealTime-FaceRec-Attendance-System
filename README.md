\documentclass[12pt,a4paper]{article}
\usepackage[utf8]{inputenc}
\usepackage[russian]{babel}
\usepackage[left=2cm,right=2cm,top=2cm,bottom=2cm]{geometry}
\usepackage{amsmath}
\usepackage{amsfonts}
\usepackage{tabularx}
\usepackage{booktabs}
\usepackage{hyperref}
\usepackage{graphicx}

\title{FaceStream-ID: Система нейросетевого распознавания лиц в реальном времени}
\author{Наземцев Артем Дмитриевич, 9 «М» класс \\ 
Руководитель: Зайцев Дмитрий Игоревич}
\date{ГБОУ Лицей № 533, Санкт-Петербург, 2026}

\begin{document}

\maketitle

\section{Аннотация}
Данный проект представляет собой программно-аппаратный комплекс биометрической идентификации в видеопотоке для автоматизированного контроля посещаемости. Система интегрирована с облачным сервисом Google Sheets и обеспечивает обработку данных со скоростью до 30 кадров/с[cite: 19, 147].

\section{Основные характеристики системы}
\begin{itemize}
    \item \textbf{Высокая точность}: Архитектура ArcFace (ResNet-100) обеспечивает точность до 99.8\%[cite: 77, 80].
    \item \textbf{Метод центроидов}: Агрегация эмбеддингов в единый вектор $C$ для нивелирования шума[cite: 109, 119].
    \item \textbf{Временное голосование}: Подтверждение личности при идентификации не менее 7 раз из последних 10 кадров[cite: 168].
    \item \textbf{Облачная синхронизация}: Запись в Google Sheets с задержкой менее 1 сек[cite: 148].
\end{itemize}

\section{Математический аппарат}
В основе системы лежат следующие математические преобразования:

\subsection{Функция потерь ArcFace}
Для разделения классов на гиперсфере используется формула с аддитивным угловым зазором $m$[cite: 61]:
\begin{equation}
L = -\frac{1}{N} \sum_{i=1}^{N} \log \frac{e^{s \cdot \cos(\theta_{y_i} + m)}}{e^{s \cdot \cos(\theta_{y_i} + m)} + \sum_{j \neq y_i} e^{s \cdot \cos \theta_j}}
\end{equation}

\subsection{Метод центроидов}
Для создания эталона личности выполняется усреднение нормализованных векторов $v_i$[cite: 111]:
\begin{equation}
C = \text{Norm}\left( \frac{1}{n} \sum_{i=1}^{n} v_{i\_norm} \right)
\end{equation}

\section{Сравнительный анализ моделей}

\subsection{Распознавание лиц (Экстракторы)}
\begin{table}[h!]
\centering
\begin{tabularx}{\textwidth}{l c l X}
\toprule
Модель & Точность & Ресурсы & Особенности \\
\midrule
FaceNet & 92\% & Средние & Устаревшая архитектура [cite: 80] \\
MobileFaceNet & 90\% & Минимальные & Оптимизирована под ARM [cite: 80] \\
\textbf{ArcFace} & \textbf{99.8\%} & Высокие & Лидер по сепарабельности [cite: 80] \\
\bottomrule
\end{tabularx}
\end{table}

\subsection{Детекция лиц}
\begin{table}[h!]
\centering
\begin{tabularx}{\textwidth}{l c l X}
\toprule
Модель & Латентность & Точность & Вердикт \\
\midrule
MTCNN & 15–30 мс & Средняя & Ошибки в профиль [cite: 94] \\
\textbf{RetinaFace} & \textbf{5–10 мс} & \textbf{Очень высокая} & Стабильна при плохом свете [cite: 94] \\
\bottomrule
\end{tabularx}
\end{table}

\section{Технологический стек}
Проект реализован на языке \textbf{Python} с использованием библиотек:
\begin{itemize}
    \item \textbf{InsightFace} — детекция и извлечение признаков[cite: 201].
    \item \textbf{OpenCV} — захват видеопотока 1080p[cite: 166].
    \item \textbf{GSpread} — взаимодействие с Google Sheets API[cite: 208].
    \item \textbf{Kaggle Dataset} — внешние данные для валидации алгоритмов.
\end{itemize}

\section{Заключение и развитие}
Текущий прототип подтвердил эффективность метода временного голосования в реальных условиях[cite: 181]. Дальнейшее развитие включает расширение базы биометрических данных и переход на камеры с более высокой светочувствительностью для устранения деградации изображения при низком освещении[cite: 182, 177].

\end{document}
