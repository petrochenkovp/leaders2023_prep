# Поиск одинаковых товаров (подготовка к хакатону ЛЦТ 2023)
Проект завершён в мае 2023

## В ходе проекта:
- **Приобретён ценный опыт работы в команде**
- **Прокачаны навыки глубокого обучения и NLP, испробованы десятки идей**
- **Детально изучены и применены такие текстовые модели, как BERT и Word2Vec**
- **Также изучалась мультимодальная модель CLIP, сопоставляющая изображения и текстовые подписи к ним**
- **Для проверки идей и обучения моделей использовался доработанный датасет из предыдущего соревнования от Казань-Экспресс**

<br>

## Описание
График хакатона позволял зарегистрироваться и выбрать задачу заранее, поэтому на момент формирования команды мы уже знали, что будем решать задачу от компании OZON по поиску одинаковых товаров в парах. С момента регистрации и до старта у нас было около месяца на подготовку, в ходе которой каждый участник команды занимался своей частью исследований и делился результатами работы на регулярных общих созвонах

Основной моей задачей было изучение оригинальной модели RTE (Recognizing Textual Entailment) с лексическими и семантическими признаками, представленной авторами [статьи](https://arxiv.org/abs/2210.09723) на сайте arxiv.org, а также генерация признаков модели для использования в задаче по определению одинаковых товаров

## Вкратце о модели RTE
Данная модель из семейства NLP-моделей служит для распознавания причинно-следственной связи между двумя текстами, когда один фрагмент текста, называемый "гипотезой" (H),  может быть выведен из другого фрагмента текста, называемого "текстом" (T). К примеру для текста Т = "Мать кормит ребенка молоком" гипотеза Н = "Младенец пьёт молоко" является истинным утверждением, то есть логическим следствием (Entailment). А гипотеза Н = "Человек ест рис" таковым не является (Neutral)
- Одна из отличительных особенностей метода, описанного в статье, заключается в том, что для выявления причинно-следственной связи между двумя текстами используются и лексические, и семантические признаки, включая такие, как:
  - Поэлементный вектор Манхэттенских расстояний (EMDV)
  - Среднее значение EMDV
  - Оценка сходства по Жаккару (JAC)
  - Сходство на основе мешка слов  (BoW)
  - Оценка семантического сходства (STS) на основе BERT
- Другая особенность метода заключалась в извлечении семантики текста путем поэлементного сложения векторов слов в единый вектор текста на основании порога вхождения. То есть, не каждый, а только значимый элемент вектора слова попадает в результирующий вектор текста

## Цели
На сегодняшний день готовой реализации данной модели в открытом доступе нет, поэтому основными целями было:
- Подробно изучить модель
- Реализовать с нуля метод и идеи авторов статьи
- Сгенерировать все вышеперечисленные признаки для нашей задачи

## Результаты и выводы
Изучена и реализована модель RTE для распознавания причинно-следственной связи между двумя текстами, сгенерированы лексические и семантические признаки:
1. Поэлементный вектор Манхэттенских расстояний (EMDV). Для извлечения векторов слов использовалась модель Word2Vec, предобученная на НКРЯ и русскоязычной Википедии за 2019 год с размерностью выходного вектора 300. Был полностью реализован предложенный авторами статьи алгоритм порогового поэлементного складывания векторов слов в единый вектор текста. В рамках нашей задачи векторы рассчитывались для текстов первого и второго товаров в паре, далее вычислялся модуль их разности – поэлементный вектор Манхэттенского расстояния
2. Среднее значение EMDV. Для каждой записи было вычислено среднее значение элементов вектора Манхэттенского расстояния
3. Оценка сходства по Жаккару (JAC). Данный признак оценивает сходство пары текстов, чтобы определить, какие слова являются общими, а какие уникальными. Фактически это отношение количества слов, общих для обоих текстов, к общему количеству слов в обоих текстах: `len(T1 ∩ T2) / len(T1 ∪ T2)`
4. Сходство на основе мешка слов (BoW). Мешок слов BoW – это векторное представление корпуса из нескольких текстов, где размерность каждого вектора – это количество уникальных слов, существующих в корпусе из нескольких текстов, а значение каждого вектора – частота слов в каждом тексте корпуса. Применительно к нашей задаче, каждая запись датафрейма содержит корпус из двух текстов. Для каждого текста вычисляются векторы, а затем вычисляется косинусная близость этих векторов
5. Оценка семантического сходства (STS) на основе BERT. Для вычисления семантической оценки сходства двух текстов (STS) используется предварительно обученная модель BERT. Семантический вектор каждого текста авторами метода предлагается складывать поэлементно из семантических векторов составляющих его слов. Тогда оценка STS вычисляется как косинусное сходство между векторами двух текстов

На доработанном датасете с прошлого соревнования модель, использующая данные признаки, показывала стабильно высокие показатели качества, в отличие от реальных данных, где часто встречались очень тонкие различия и другие нюансы (например, два с виду одинаковых смартфона могут иметь одинаковые описания и картинки, но отличаться лишь количеством Гб памяти, что, разумеется, делает эти товары разными)

## Стек
python, pandas, numpy, nltk, pymorphy2, sklearn, gensim, torch, transformers

<br><br>

Код проекта: [get_rte_features.ipynb](https://github.com/petrochenkovp/leaders2023_prep/blob/main/get_rte_features.ipynb)

<br><br>

Посмотреть [другие проекты](https://github.com/petrochenkovp/portfolio)

<br><br>
