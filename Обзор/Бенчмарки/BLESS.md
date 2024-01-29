[статья](https://arxiv.org/pdf/2310.15773.pdf)
**Предмет оценки**: задача упрощения текста.

___
### Датасеты

#### 1. ASSET
2359 предложений из английской википедии (для каждого 10 упрощённых вариантов)
[[операции упрощения]]:
- замена
- разбивка
- удаление

#### 2. MED-EASI
1979 коротких медицинских текстов (пары "сложный -- попроще")
[[операции упрощения]]:
- замена
- удаление
- вставка
- уточнение
последние два нужны для пояснений ==сложных терминов== -- приём упрощения, очень полезный в научных текстах

#### 3. NEWSELA
1130 объёмных новостных статей; каждая переписана в соответствии с четырьмя уровнями [читаемости](читаемость)

___
### Процесс оценки
#### Тестируемые LLM
44 модели различного размера, архитектуры и предназначений

Характеристики:
- 60-176 млрд параметров
- только трансформеры
- энкодер-декодер / только декодер

#### Промпты
ICL (обучение в контексте) -- модели предоставляется несколько примеров input-output

#### Собственно оценка
бейзлайн -- [[MUSS]]
автоматическая оценка качества (метрики):
- [[SARI]] (простота текста)
- [[BERTScore]] (сохранение смысла)
- [[FKGL]] (читаемость)
- также используется [[LENS]] (семантическое сходство + степень упрощённости)
(метрики рассчитаны с помощью [[EASSE]])

#### Оценка вручную
1) проверить, что упрощение корректно и обозначить типичные ошибки:
	- недопустимые изменения изначального смысла;
	- аграмматичность;
	- галлюцинации;
2) обозначить типичные [[операции упрощения]]:
	- лексическое упрощение;
	- удаление;
	- разбивка;
	- изменение порядка слов;
	- перефразирование;
смотрят 5 лучших моделей по каждой метрике, в каждом случае по 5 случайных пар "сложное -- простое"

___
### Выводы авторов
- промпты с чёткой структурой улучшают результат
- ==to be continued== (про ручную оценку вроде бы интересные выводы)

___
### Примеры из датасетов
#### ASSET
[датасет](https://github.com/facebookresearch/asset/tree/main/dataset)

```
{
    "complex": "One side of the armed conflicts is composed mainly of the Sudanese military and the Janjaweed, a Sudanese militia group recruited mostly from the Afro-Arab Abbala tribes of the northern Rizeigat region in Sudan.", 
    "simple": [
        "On one side of the conflicts are the Sudanese military and the Janjaweed, a Sudanese militia group.  They are mostly recruited from the Afro-Arab Abbala tribes.", 
        "One side of the armed conflicts is composed mainly of the Sudanese military and the Janjaweed. The Janjaweed are a Sudanese militia group recruited mostly from the Afro-Arab Abbala tribes of the northern Rizeigat region in Sudan.", 
        "One side of the armed conflicts is mainly the Sudanese military and the Janjaweed militia group.", 
        "One side of the war is made up of the Sudanese military and the Janjaweed, a Sudanese militia group from the Afro-Arab Abbala tribes of the northern Rizeigat region in Sudan.", 
        "One side of the war is mainly made up of the Sudanese military and the Janjaweed. The Janjaweed is a Sudanese militia group who come mostly from the Afro-Arab Abbala tribes of the northern Rizeigat region in Sudan.", 
        "One side of the fighters is made up of the Sudanese military and the Janjaweed.  The Janjaweed is a Sudanese militia group.  It is recruited mostly from the Afro-Arab Abbala tribes located in the northern Rizeigat region of Sudan.", 
        "One side of the armed conflict includes the Sudanese military and the Janjaweed, a Sudanese militia group made up of Afro-Arab Abbala tribesmen.", 
        "One side of the armed conflicts is composed mainly of the Sudanese military and the Janjaweed.  The Janjaweed are a Sudanese militia group, with recruits from the Afro-Arab Abbala tribes of the northern Rizeigat region in Sudan.", 
        "One side of the war is mainly the Sudanese military and a Sudanese milita group called the Janjaweed. The Janjaweed is mostly from the Afro-Arab Abbala tribes of the northern Rizeigat region in Sudan.", 
        "The Sudanese military and Janjaweed work together. The Janjaweed is a militia group. The members were recruited from Afro-Arab Abbala tribes. These tribes are from the northern region of Sudan."
    ]
}
```

С человеческой точки зрения в ASSET изначальные предложения не особо сложные.

Суть датасета не в том, чтобы взять изначально сложные предложения и упростить их. Она в том, чтобы просто упростить:
```
Complex: He was appointed Companion of Honour (CH) in 1988.
Simple: He was appointed Companion of Honour in 1988.
```

Из чего можно рассчитывать сложность:
- сложность дерева разбора;
- уровень лексики (часто заменяются более простыми синонимами)  // на самом деле даже лексическое упрощение -- это очень объёмная тема же

Часто при упрощении опускают какие-то уточнения:
```
Complex: Representatives of the Professional Hockey Writers' Association vote for the All-Star Team at the end of the regular season.
Simple: The All-Star Team will be voted on at the end of the season.
```
// и вообще, будет ли упрощением замена на пассивную конструкцию?)

#### Med-EASi
[датасет](https://huggingface.co/datasets/cbasu/Med-EASi)
[статья](https://arxiv.org/pdf/2302.09155.pdf)

```
Expert: During childhood, *she suffered from partially collapsed lungs* twice, had pneumonia four *to* five times *a year*, *as well as a ruptured* appendix and *a tonsillar cyst*.
Simple: During childhood, *suffered from collapsed lungs* twice, had pneumonia four *or* five, *(ruptured)* appendix and *a tonsillar cyst*.

Expert: Monozygotic twins have a concordance of about 45 %.
Simple: When comparing monozygotic twins, we found that they had a concordance of about 45 %.
```

#### NEWSELA
[датасет](https://newsela.com/data/)

___
### Анализ датасетов
Общие мысли:
- для моего восприятия предложения сложны главным образом из-за своей длины / нагруженной структуры, в лексическом плане они достаточно просты
- иногда какая-то инфа просто опускается и считается упрощением -- я не согласен)
- полезно отметить [[операции упрощения]]:
	- **лексические**:
		- замена
		- удаление
	- **структурные**:
		- изменение порядка слов
		- разбивка
	- **смысловые**:
		- вставка
		- уточнение
- и вероятно, потом их будет полезно соотнести с метриками сложности (то, что описано в работах именно про оценку сложности саму по себе)

#### ASSET
[colab](https://colab.research.google.com/drive/1WKUaXPzx1MZ8dBwKlhAP1p-ed4CYMN_2?usp=sharing)
##### Ручной анализ
41 (тест) -- хороший пример того, что при упрощении много информации можно уносить в пресуппозицию
```
Complex: It is theoretically possible that the other editors who may have reported you, and the administrator who blocked you, are part of a conspiracy against someone half a world away they've never met in person.
Simple: It is possible the editors and administrators are part of a conspiracy against you.
```

##### Статистика
**Распределение доли разбивок предложений**
Более чем для 3/4 исходных предложений (всего 2000) как минимум одно упрощение состоит в разбивке предложения на два/несколько:
(по оси X расположены значения процента разбиений от общего числа упрощений, по оси Y -- количество предложений с таким процентом разбиений)

==посчитать просто долю разбивок от всех упрощений==

**Вывод**: разбивка -- распространённый способ упростить.

![[sent_split_percentages.png]]

**Распределение разниц в длине**

**Вывод**: длина -- не особо показатель простоты.

==здесь важно в целом распределение длин посмотреть, чтобы понять, для каких рамок это справедливо==
==и разбить на группы, кстати, можно==

![[length_diffs.png]]