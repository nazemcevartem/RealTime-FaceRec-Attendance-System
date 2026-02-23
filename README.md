\documentclass[12pt,a4paper]{article}

% --- Настройка шрифтов и языка ---
\usepackage[T2A]{fontenc}
\usepackage[utf8]{inputenc}
\usepackage[russian]{babel}

% --- Геометрия страницы и таблицы ---
\usepackage[left=2.5cm,right=1.5cm,top=2cm,bottom=2cm]{geometry}
\usepackage{amsmath}
\usepackage{amsfonts}
\usepackage{booktabs} % Для красивых линий в таблицах
\usepackage{tabularx} % Для адаптивных таблиц
\usepackage{hyperref}

\begin{document}

% --- Титульный лист (упрощенно) ---
\begin{center}
    \large ГБОУ Лицей № 533 \\
    \vspace{2cm}
    \Large \textbf{Индивидуальный проект} \\
    \large по информатике (направление: искусственный интеллект) \\
    \vspace{1.5cm}
    \huge \textbf{FaceStream-ID: Система нейросетевого распознавания лиц в реальном времени} \\
\end{center}

\vspace{3cm}

\begin{flushright}
    \textit{Выполнил:} \\
    ученик 9 «М» класса \\
    \textbf{Наземцев Артем Дмитриевич} \\
    \vspace{0.5cm}
    \textit{Руководитель:} \\
    \textbf{Зайцев Дмитрий Игоревич}
\end{flushright}

\vfill

\begin{center}
    Санкт-Петербург, 2026
\end{center}

\newpage

% --- Основное содержание ---

\section{Описание системы}
Система FaceStream-ID предназначена для автоматизированного контроля посещаемости. В основе решения лежит использование современных архитектур глубокого обучения для детекции и идентификации лиц.



\section{Математические методы}

\subsection{Аддитивный угловой зазор (ArcFace)}
Для максимизации межклассового расстояния используется функция потерь ArcFace:
\begin{equation}
L = -\frac{1}{N} \sum_{i=1}^{N} \log \frac{e^{s \cdot \cos(\theta_{y_i} + m)}}{e^{s \cdot \cos(\theta_{y_i} + m)} + \sum_{j \neq y_i} e^{s \cdot \cos \theta_j}}
\end{equation}

\subsection{Метод центроидов}
Для создания стабильного биометрического шаблона (эталона) применяется усреднение нормализованных векторов признаков:
\begin{equation}
C = \text{Norm}\left( \frac{1}{n} \sum_{i=1}^{n} v_{i} \right)
\end{equation}

\section{Сравнительный анализ моделей}

\subsection{Детекция лиц}
\begin{table}[h!]
\centering
\small
\begin{tabularx}{\textwidth}{l c c X}
\toprule
\textbf{Модель} & \textbf{Латентность} & \textbf{Точность} & \textbf{Вердикт} \\
\midrule
MTCNN & 15--30 мс & Средняя & Ошибки при поворотах головы \\
BlazeFace & 2--5 мс & Низкая & Только для мобильных устройств \\
\textbf{RetinaFace (ResNet-50)} & \textbf{5--10 мс} & \textbf{Очень высокая} & \textbf{Оптимальный выбор} \\
\bottomrule
\end{tabularx}
\end{table}

\subsection{Экстракция признаков (Распознавание)}
\begin{table}[h!]
\centering
\small
\begin{tabularx}{\textwidth}{l c l X}
\toprule
\textbf{Архитектура} & \textbf{Точность} & \textbf{Ресурсы} & \textbf{Особенности} \\
\midrule
FaceNet & 92\% & Средние & Базовая архитектура \\
MobileFaceNet & 90\% & Минимальные & Для встраиваемых систем \\
\textbf{ArcFace (ResNet-100)} & \textbf{99.8\%} & \textbf{Высокие} & \textbf{Максимальная точность} \\
\bottomrule
\end{tabularx}
\end{table}

\section{Алгоритм принятия решения}
Для минимизации ложных срабатываний (False Positive) внедрен метод \textbf{временного голосования}:
\begin{itemize}
    \item Сбор данных за окно из 10 кадров.
    \item Подтверждение личности только при наличии $\ge 7$ совпадений.
    \item Асинхронная отправка данных в Google Sheets через API.
\end{itemize}

\section{Заключение}
Разработанный прототип обеспечивает стабильную работу при 25--30 кадрах в секунду. В качестве дальнейшего развития планируется переход на использование высокопроизводительных камер для работы в условиях сложного освещения.

\end{document}
