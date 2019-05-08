Это модель, которая симулирует множество A/B тестов, сравнивая две гипотезы с разной конверсией. Мы собираем статистику и оцениваем ситуации с досрочным завершением теста, если появилась статистическая значимость, завершение по с ограниченией по выборке и когда тест идет бесконечно долго, пока не появится статистическая значимость в одном из вариантов.

Для запуска достаточно открыть страницу в браузере локально. Результат покажется спустя какое-то время в консоли браузера.

# Конверсия
Допустим у нас есть две посадочные страницы. На эту страницу приходят люди, далеко не все из которых сконвертятся, но одна из страниц повышает этот шанс — то есть у нее выше конверсия. 

Конверсия для каждой страницы/гипотезы задается через две переменные: 

```
var control_conversion = [1,5];
var test_conversion = [0.3,4];
```

Запись конверсии `[1,5]` означает, что вероятность конверсии для этой страницы для случайного посетителя находится между 1% и 5% и нормально распределена (классический "колокол"). То есть основная масса посетителей будет иметь шанс конверсии в 3%, а например очень малая часть 1% или 5%. 
Запись `[0.3,4]` — означает, что шанс конверсии у посетителей между 0.3% и 4% (у основной массы 2.15%)


# Симуляция одного эксперимента
В рамках симуляции одного A/B эксперимента мы "посылаем" трафик и случайно выбираем один из двух вариантов. 

Потом мы выбираем конверсию для этого посетителя. Она выбирается случайно с учетом нормального распределения (у основной массы ребят все в середке, чем ближе к началу и конца "колокола" — тем ребят меньше туда попадает).

После этого мы еще раз случайным образом проверяем — сконвертился ли этот посетитель или нет (с учетом выбранной конверсии).

Очевидно, что будут ситуации, когда у конкретного посетителя на странице с низкой конвесией [0.3,4] будет выбрана бОльшая конверсия (например 3%) чем для конкретного посетителя с бОльшей общей конверсией [1,5] (например 1.5%). Это так и это часть модели — так оно и бывает. На большом количестве посетителей всё все равно приводится к среднему. 

Для каждого теста мы также передаем размер рекомендованной выборки.

Тест завершается, когда:
- Посетителей стало больше чем размер рекомендованной выборки, то есть мы собрали данные для нее и данные статистически значимы. Послали 1000 посетителей, на 1000-ом данные значимы → завершаемся
или
- Посетителей стало больше чем размер рекомендованной выборки и мы получили статистически значимые данные раньше чем кончилась выборка или позже. Послали 1000 посетителей, на 400-ом данные значимы, ждем 1000-ого, снимаем данные → завершаемся. Или послали 2500 посетителей, на 1000-ом данные не значимы, тест идет дальше на 2500 получили значимые данные → завершаемся.
или
- В тесте больше 1,000,000 посетителей (чтобы браузер не подвисал долго в расчетах при очень одинаковых конверсиях двух версий)

# Много симуляций
Прогоняем каждую симуляцию много раз и собираем данные. 

result_limited_time_notsignificant: 
result_limited_time_significant_error: 
result_limited_time_significant_noerror: 
result_unlimited_time_significant_error: 
result_unlimited_time_significant_error_is_early: 
result_unlimited_time_significant_error_is_late: 
result_unlimited_time_significant_noerror: 
result_unlimited_time_significant_noerror_is_early: 
result_unlimited_time_significant_noerror_is_late: 

# Попробовать самому
Вот тут менять конверсии:
```
var control_conversion = [1,5];	
var test_conversion = [0.3,4];	
```

Пока есть ограничение, что конверсия теста обязана быть меньше чем конверсия контрольной группы.

Тут меняем количество симуляций (10000) и размер рекомендованного сегмента (26000). Важно: размер рекомендованного сегмента должен быть СУММОЙ тестового и контрольного сегментов.

`console.log(run_many_simulations(10000,26000));`
