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
==потом можно будет ещё повыискивать сложные примеры==

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
41 (тест) -- хороший пример ...

##### Статистика
###### Распределение доли разбивок предложений
Более чем для половины исходных предложений (всего 2000) как минимум одно упрощение состоит в разбивке предложения на два/несколько:
(по оси X расположены значения процента разбиений от общего числа упрощений, по оси Y -- количество предложений с таким процентом разбиений)

<img src="data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAigAAAGdCAYAAAA44ojeAAAAOXRFWHRTb2Z0d2FyZQBNYXRwbG90bGliIHZlcnNpb24zLjcuMSwgaHR0cHM6Ly9tYXRwbG90bGliLm9yZy/bCgiHAAAACXBIWXMAAA9hAAAPYQGoP6dpAAAciUlEQVR4nO3df3DX9X3A8RchJCjyDYJNAjMMa9ciE9sbTPjWdlttZqZpqyde7ZVjtEd1tcGbcOeElcKqXeGYV509lM21xV11tPZqW8EfpXjitcQfjeWOobI58WBHv0HPkSAeCZDP/tjxvUWo+g1Jvu8kj8fd9858Pu/vN6+vbyDP++T7TUZlWZYFAEBCKso9AADA2wkUACA5AgUASI5AAQCSI1AAgOQIFAAgOQIFAEiOQAEAklNZ7gH6oqenJ/bv3x/jx4+PUaNGlXscAOA9yLIsDh06FFOmTImKine+RjIkA2X//v3R0NBQ7jEAgD7Yt29fnHvuue+4ZkgGyvjx4yPi/55gLpcr8zQAwHvR2dkZDQ0Nxa/j72RIBsqJb+vkcjmBAgBDzHt5eYYXyQIAyREoAEByBAoAkByBAgAkR6AAAMkRKABAcgQKAJAcgQIAJEegAADJESgAQHIECgCQHIECACRHoAAAyREoAEByKss9QIqmLdtc7hFK9uqa5nKPAAD9xhUUACA5AgUASI5AAQCSI1AAgOQIFAAgOQIFAEiOQAEAkiNQAIDkCBQAIDkCBQBIjkABAJIjUACA5AgUACA5AgUASI5AAQCSI1AAgOQIFAAgOQIFAEiOQAEAkiNQAIDkCBQAIDkCBQBIjkABAJIjUACA5AgUACA5AgUASI5AAQCSI1AAgOQIFAAgOQIFAEiOQAEAkiNQAIDkCBQAIDkCBQBIzmkFypo1a2LUqFFx0003FY8dOXIkWlpaYtKkSXHWWWfFvHnzor29vdf99u7dG83NzXHmmWdGbW1t3HzzzXHs2LHTGQUAGEb6HCjPPfdc/NM//VNcdNFFvY4vWbIkHn744XjwwQdj27ZtsX///rj66quL548fPx7Nzc3R3d0d27dvj/vuuy82bNgQK1eu7PuzAACGlT4Fyptvvhnz58+Pe++9N84+++zi8Y6OjvjOd74T3/rWt+LSSy+NWbNmxfe+973Yvn17PP300xER8fOf/zxeeOGF+P73vx8f+chH4vLLL4/bbrst1q1bF93d3f3zrACAIa1PgdLS0hLNzc3R2NjY63hbW1scPXq01/Hp06fH1KlTo7W1NSIiWltbY+bMmVFXV1dc09TUFJ2dnbFr165Tfr6urq7o7OzsdQMAhq/KUu+wcePGeP755+O555476VyhUIiqqqqYMGFCr+N1dXVRKBSKa/5/nJw4f+LcqaxevTq+/vWvlzoqADBElXQFZd++ffHXf/3Xcf/998fYsWMHaqaTLF++PDo6Ooq3ffv2DdrnBgAGX0mB0tbWFgcOHIg/+qM/isrKyqisrIxt27bFXXfdFZWVlVFXVxfd3d1x8ODBXvdrb2+P+vr6iIior68/6V09Jz4+sebtqqurI5fL9boBAMNXSYHyyU9+Mnbu3Bk7duwo3mbPnh3z588v/veYMWNi69atxfvs3r079u7dG/l8PiIi8vl87Ny5Mw4cOFBcs2XLlsjlcjFjxox+eloAwFBW0mtQxo8fHxdeeGGvY+PGjYtJkyYVjy9atCiWLl0aEydOjFwuFzfeeGPk8/mYO3duRERcdtllMWPGjFiwYEGsXbs2CoVCrFixIlpaWqK6urqfnhYAMJSV/CLZd3PHHXdERUVFzJs3L7q6uqKpqSnuvvvu4vnRo0fHpk2b4oYbboh8Ph/jxo2LhQsXxq233trfowAAQ9SoLMuycg9Rqs7OzqipqYmOjo4BeT3KtGWb+/0xB9qra5rLPQIAvKNSvn77XTwAQHIECgCQHIECACRHoAAAyREoAEByBAoAkByBAgAkR6AAAMkRKABAcgQKAJAcgQIAJEegAADJESgAQHIECgCQHIECACRHoAAAyREoAEByBAoAkByBAgAkR6AAAMkRKABAcgQKAJAcgQIAJEegAADJESgAQHIECgCQHIECACRHoAAAyREoAEByBAoAkByBAgAkR6AAAMkRKABAcgQKAJAcgQIAJEegAADJESgAQHIECgCQHIECACRHoAAAyREoAEByBAoAkByBAgAkR6AAAMkRKABAcgQKAJAcgQIAJEegAADJESgAQHIECgCQHIECACRHoAAAyREoAEByBAoAkByBAgAkR6AAAMkRKABAcgQKAJAcgQIAJEegAADJESgAQHIECgCQHIECACRHoAAAyREoAEByBAoAkByBAgAkp6RAueeee+Kiiy6KXC4XuVwu8vl8PProo8XzR44ciZaWlpg0aVKcddZZMW/evGhvb+/1GHv37o3m5uY488wzo7a2Nm6++eY4duxY/zwbAGBYKClQzj333FizZk20tbXFr3/967j00kvjyiuvjF27dkVExJIlS+Lhhx+OBx98MLZt2xb79++Pq6++unj/48ePR3Nzc3R3d8f27dvjvvvuiw0bNsTKlSv791kBAEPaqCzLstN5gIkTJ8Y//MM/xDXXXBPve9/74oEHHohrrrkmIiJeeumluOCCC6K1tTXmzp0bjz76aHzqU5+K/fv3R11dXURErF+/Pm655ZZ47bXXoqqq6j19zs7OzqipqYmOjo7I5XKnM/4pTVu2ud8fc6C9uqa53CMAwDsq5et3n1+Dcvz48di4cWMcPnw48vl8tLW1xdGjR6OxsbG4Zvr06TF16tRobW2NiIjW1taYOXNmMU4iIpqamqKzs7N4FeZUurq6orOzs9cNABi+Sg6UnTt3xllnnRXV1dXx5S9/OR566KGYMWNGFAqFqKqqigkTJvRaX1dXF4VCISIiCoVCrzg5cf7Eud9l9erVUVNTU7w1NDSUOjYAMISUHCgf+tCHYseOHfHMM8/EDTfcEAsXLowXXnhhIGYrWr58eXR0dBRv+/btG9DPBwCUV2Wpd6iqqooPfOADERExa9aseO655+If//Ef49prr43u7u44ePBgr6so7e3tUV9fHxER9fX18eyzz/Z6vBPv8jmx5lSqq6ujurq61FEBgCHqtH8OSk9PT3R1dcWsWbNizJgxsXXr1uK53bt3x969eyOfz0dERD6fj507d8aBAweKa7Zs2RK5XC5mzJhxuqMAAMNESVdQli9fHpdffnlMnTo1Dh06FA888EA8+eST8fjjj0dNTU0sWrQoli5dGhMnToxcLhc33nhj5PP5mDt3bkREXHbZZTFjxoxYsGBBrF27NgqFQqxYsSJaWlpcIQEAikoKlAMHDsRf/uVfxm9/+9uoqamJiy66KB5//PH48z//84iIuOOOO6KioiLmzZsXXV1d0dTUFHfffXfx/qNHj45NmzbFDTfcEPl8PsaNGxcLFy6MW2+9tX+fFVDkbfPAUHTaPwelHPwclJP5B53fxZ9nIBWD8nNQAAAGikABAJIjUACA5AgUACA5AgUASI5AAQCSI1AAgOQIFAAgOQIFAEiOQAEAkiNQAIDkCBQAIDkCBQBIjkABAJIjUACA5FSWewD6x7Rlm8s9QsleXdNc7hEASJQrKABAcgQKAJAcgQIAJEegAADJESgAQHIECgCQHIECACRHoAAAyREoAEByBAoAkByBAgAkR6AAAMkRKABAcgQKAJAcgQIAJEegAADJqSz3ADCUTFu2udwjAIwIrqAAAMkRKABAcgQKAJAcgQIAJEegAADJESgAQHIECgCQHIECACRHoAAAyREoAEByBAoAkByBAgAkR6AAAMkRKABAcgQKAJAcgQIAJEegAADJESgAQHIECgCQHIECACRHoAAAyaks9wCMXNOWbS73CAAkyhUUACA5rqAAyRmKV9deXdNc7hFgWHEFBQBIjkABAJIjUACA5AgUACA5AgUASI5AAQCSI1AAgOQIFAAgOQIFAEiOQAEAklNSoKxevTr++I//OMaPHx+1tbVx1VVXxe7du3utOXLkSLS0tMSkSZPirLPOinnz5kV7e3uvNXv37o3m5uY488wzo7a2Nm6++eY4duzY6T8bAGBYKClQtm3bFi0tLfH000/Hli1b4ujRo3HZZZfF4cOHi2uWLFkSDz/8cDz44IOxbdu22L9/f1x99dXF88ePH4/m5ubo7u6O7du3x3333RcbNmyIlStX9t+zAgCGtFFZlmV9vfNrr70WtbW1sW3btviTP/mT6OjoiPe9733xwAMPxDXXXBMRES+99FJccMEF0draGnPnzo1HH300PvWpT8X+/fujrq4uIiLWr18ft9xyS7z22mtRVVX1rp+3s7MzampqoqOjI3K5XF/H/52G4i8qA8rLLwuEd1fK1+/Teg1KR0dHRERMnDgxIiLa2tri6NGj0djYWFwzffr0mDp1arS2tkZERGtra8ycObMYJxERTU1N0dnZGbt27Trl5+nq6orOzs5eNwBg+OpzoPT09MRNN90Ul1xySVx44YUREVEoFKKqqiomTJjQa21dXV0UCoXimv8fJyfOnzh3KqtXr46ampriraGhoa9jAwBDQJ8DpaWlJf793/89Nm7c2J/znNLy5cujo6OjeNu3b9+Af04AoHwq+3KnxYsXx6ZNm+Kpp56Kc889t3i8vr4+uru74+DBg72uorS3t0d9fX1xzbPPPtvr8U68y+fEmrerrq6O6urqvowKAAxBJV1BybIsFi9eHA899FA88cQTcd555/U6P2vWrBgzZkxs3bq1eGz37t2xd+/eyOfzERGRz+dj586dceDAgeKaLVu2RC6XixkzZpzOcwEAhomSrqC0tLTEAw88ED/96U9j/PjxxdeM1NTUxBlnnBE1NTWxaNGiWLp0aUycODFyuVzceOONkc/nY+7cuRERcdlll8WMGTNiwYIFsXbt2igUCrFixYpoaWlxlQQAiIgSA+Wee+6JiIg/+7M/63X8e9/7XnzhC1+IiIg77rgjKioqYt68edHV1RVNTU1x9913F9eOHj06Nm3aFDfccEPk8/kYN25cLFy4MG699dbTeyYAwLBxWj8HpVz8HBQgNX4OCry7Qfs5KAAAA0GgAADJESgAQHIECgCQHIECACRHoAAAyREoAEByBAoAkByBAgAkR6AAAMkRKABAcgQKAJAcgQIAJEegAADJESgAQHIECgCQHIECACRHoAAAyREoAEByBAoAkByBAgAkR6AAAMkRKABAcgQKAJAcgQIAJEegAADJESgAQHIECgCQHIECACRHoAAAyREoAEByBAoAkByBAgAkR6AAAMkRKABAcgQKAJCcynIPADAcTFu2udwjlOzVNc3lHgF+J1dQAIDkCBQAIDkCBQBIjkABAJIjUACA5AgUACA5AgUASI5AAQCSI1AAgOQIFAAgOQIFAEiOQAEAkiNQAIDkCBQAIDkCBQBIjkABAJIjUACA5AgUACA5AgUASI5AAQCSI1AAgOQIFAAgOQIFAEiOQAEAkiNQAIDkCBQAIDkCBQBIjkABAJIjUACA5AgUACA5AgUASE7JgfLUU0/Fpz/96ZgyZUqMGjUqfvKTn/Q6n2VZrFy5MiZPnhxnnHFGNDY2xn/+53/2WvPGG2/E/PnzI5fLxYQJE2LRokXx5ptvntYTAQCGj5ID5fDhw/HhD3841q1bd8rza9eujbvuuivWr18fzzzzTIwbNy6ampriyJEjxTXz58+PXbt2xZYtW2LTpk3x1FNPxfXXX9/3ZwEADCuVpd7h8ssvj8svv/yU57IsizvvvDNWrFgRV155ZURE/Ou//mvU1dXFT37yk/jc5z4XL774Yjz22GPx3HPPxezZsyMi4tvf/nZcccUVcfvtt8eUKVNO4+kAAMNBv74GZc+ePVEoFKKxsbF4rKamJubMmROtra0REdHa2hoTJkwoxklERGNjY1RUVMQzzzxzysft6uqKzs7OXjcAYPjq10ApFAoREVFXV9freF1dXfFcoVCI2traXucrKytj4sSJxTVvt3r16qipqSneGhoa+nNsACAxQ+JdPMuXL4+Ojo7ibd++feUeCQAYQP0aKPX19RER0d7e3ut4e3t78Vx9fX0cOHCg1/ljx47FG2+8UVzzdtXV1ZHL5XrdAIDhq18D5bzzzov6+vrYunVr8VhnZ2c888wzkc/nIyIin8/HwYMHo62trbjmiSeeiJ6enpgzZ05/jgMADFElv4vnzTffjJdffrn48Z49e2LHjh0xceLEmDp1atx0003xjW98I/7gD/4gzjvvvPja174WU6ZMiauuuioiIi644IL4i7/4i7juuuti/fr1cfTo0Vi8eHF87nOf8w4eACAi+hAov/71r+MTn/hE8eOlS5dGRMTChQtjw4YN8Td/8zdx+PDhuP766+PgwYPxsY99LB577LEYO3Zs8T73339/LF68OD75yU9GRUVFzJs3L+66665+eDoAwHAwKsuyrNxDlKqzszNqamqio6NjQF6PMm3Z5n5/TABO36trmss9AqehlK/fQ+JdPADAyCJQAIDkCBQAIDkCBQBIjkABAJIjUACA5AgUACA5AgUASI5AAQCSI1AAgOQIFAAgOQIFAEiOQAEAkiNQAIDkCBQAIDkCBQBIjkABAJIjUACA5AgUACA5AgUASI5AAQCSI1AAgOQIFAAgOQIFAEiOQAEAkiNQAIDkCBQAIDkCBQBIjkABAJIjUACA5AgUACA5AgUASI5AAQCSI1AAgORUlnsAAHivpi3bXO4RSvbqmuZyjzAkuYICACRHoAAAyREoAEByBAoAkByBAgAkR6AAAMkRKABAcgQKAJAcgQIAJEegAADJESgAQHIECgCQHIECACRHoAAAyREoAEByBAoAkByBAgAkR6AAAMkRKABAcgQKAJAcgQIAJEegAADJESgAQHIECgCQHIECACRHoAAAyREoAEByBAoAkByBAgAkR6AAAMmpLPcAADCcTVu2udwj9Mmra5rL+vldQQEAklPWQFm3bl1MmzYtxo4dG3PmzIlnn322nOMAAIkoW6D84Ac/iKVLl8aqVavi+eefjw9/+MPR1NQUBw4cKNdIAEAiyhYo3/rWt+K6666LL37xizFjxoxYv359nHnmmfHd7363XCMBAIkoy4tku7u7o62tLZYvX148VlFREY2NjdHa2nrS+q6urujq6ip+3NHRERERnZ2d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">
