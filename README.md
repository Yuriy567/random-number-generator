# random number generator
Данный проект содержит реализацию генератора случайных чисел на языке программирования python.


Данный проект представляет собой генератор случайных чисел, который по заданным входным параметрам, зависящим от вида распределения, генерирует выбранное пользователем распределение.
Нами реализовано приложение с пользовательским интерфейсом, в котором пользователю сначала необходимо выбрать название распределения, а затем указать числовые значения параметров выбранного распределения. 
Приложение написано на языке программирования Python при помощи нескольких модулей.
Среди них наиболее важную роль играет модуль PyQt5, который обеспечивает взаимодествие пользователя с программой.
Нашим приложением генерируются следующие распределения:

1) Равномерное распределение

2) Нормальное распределение

3) Логнормальное распределение

4) Распределение Пуассона

5) Экспоненциальное распределение         

6) Биномиальное распределение

Все распределения генерируются при помощи модуля random

## 1) Взаимодействие с пользовательским интерфейсом
При запуске приложения пользователю предлагается выбрать распределение:

![image](https://github.com/Yuriy567/main_repo/assets/141625927/b9e52a0a-4eda-4509-a33f-f7f3d8895bea)
![image](https://github.com/Yuriy567/main_repo/assets/141625927/721de53c-35d4-4651-8659-2859a0c7c54a)



После нажатия кнопки «Отправить» пользователю необходимо ввести соответствующие данные параматров выбранного распределения. Например для экспоненциального распределения необходимо ввести интенсивность, количество испытаний и правую границу значений:
![image](https://github.com/Yuriy567/main_repo/assets/141625927/472b1b48-88fa-4318-824c-5f7eee2cdd81)

После нажатия кнопки  «Отправить» появится график полученного распределения следующего вида:
![image](https://github.com/Yuriy567/main_repo/assets/141625927/fd1ff2c4-0988-4cbe-a3a4-63a33fbe9afe)



Области графика можно увеличивать, уменьшать, при наведении мыши на график можно смотреть точные значения.
В это же время (сразу после нажатия кнопки «Отправить») все данные занесутся в таблицу excel в лист с соответствующим распределением


## 2) Описание и корректность работы программы
Все параметры сгенерированного распределения совпадают с введёнными пользователем параметрами, необходимыми для генерации распределения. В этом можно убедиться с помощью графика и сгенерированного в таблице распределения. Но как на программном уровне достигается генерация распределения, отвечающая заданным параметрам?
Для этого воспользуемся определением определённого интеграла Римана.
Определение интеграла Римана.
Пусть на отрезке [a, b] определена некоторая функция. f.  Будем считать a < b.
Разбиением (неразмеченным) отрезка  [a, b] назовём конечное множество точек отрезка  [a, b] , в которое входят точки  a и b. Как видно из определения, в разбиение всегда входят хотя бы две точки. Точки разбиения можно расположить по возрастанию.
Точки разбиения, между которыми нет других точек разбиения, называются соседними. Отрезок, концами которого являются соседние точки разбиения, называется частичным отрезком разбиения.
Длина наибольшего из отрезков называется диаметром разбиения. 
Интегралом Римана функции f в пределах от  a до b называется предел интегральных сумм Римана функции f на разбиениях отрезка [a, b]  при диаметре разбиения, стремящемуся к нулю.

Основная идея программы очень похожа на определение определённого интеграла: заданный пользователем диапазон разбивается на маленькие части – частичные отрезки. Для каждого частичного отрезка вычисляется количество чисел сгенерированного распределения, которое будет лежать в нём. Это количество зависит от длины диапазона и количества чисел – параметров, заданных пользователем. Примерная модель программы представлена на рисунке:

![image](https://github.com/Yuriy567/main_repo/assets/141625927/f04b8d8b-6748-4d85-b8d4-abff97b98ef9)

Для дальнейшего объяснения необходимо погрузиться в детали реализации программы.

Введём обозначения:
**quantity** – количество чисел,
**begin** – начало диапазона,
**end** – конец диапазона,
 Суть построения всех распределений состоит в том что заданный диапазон разбивается на равные части — частичные отрезки, длина которых вычисляется по формуле:
 
**(конец диапазона – начало диапазона + 1)/length**,
где **length** – некоторая переменная, полученная в результате следующих вычислений:
**prefix** – дробная часть длины диапазона

введём переменную gcd:

**gcd = НОД(prefix, 10^(len(prefix)))**  				*len - длина
Тогда
**length = ((10^(len(prefix)))/gcd)(end – begin)**
Но если length получается очень большое, то получится слишком много разбиений и вычислительной мощности компьютера не хватит для таких вычислений. Поэтому воспользуемся формулой Стёрджесса, чтобы сократить длину настолько, чтобы вычислительной мощности было достаточно и распределение аппроксимировалось к идеальному:
**length = length/(1 + 3.322lg(length))**
Для каждого частичного отрезка высчитывается величина height, равная количеству чисел, принадлежащих отрезку, которое войдёт в итоговую выборку. В зависимости от вида распределения высчитываться это число будет по-разному.
Как было сказано выше, изначально числа генерируются с помощью модуля random, программа лишь решает для каждого следующего числа, включать ли его в итоговую выборку или нет. Когда количество чисел в некотором частичном отрезке, включённых в выборку, достигает максимума, то есть будет равна height, то для данного частичного отрезка числа более генерироваться не будут. 
Можно заключить, что сгенерированные распределения отвечают заданным параметрам благодаря способу высчитывания частичных отрезков и высоты столбиков — количества чисел, которое войдёт в частичный отрезок.




## 2) Виды распределений и особенности их генерирования

1) Равномерное распределение
Для данного распределения пользователю необходимо указать количество чисел и диапазон чисел, в котором будет генерироваться распределение. В равномерном распределении в каждом разбиении должно лежать количество чисел, равное **quantity/length**. Это достигается с помощью программы. Как только все разбиения полностью заполняются, программа завершается.

2) Нормальное и логнормальное распределения
Для данных распределений пользователю необходимо указать математическое ожидание, среднеквадратическое отклонение, количество чисел и диапазон чисел, в котором будет генерироваться распределение. В каждом разбиении должно лежать количество чисел, равное интегралу от функции плотности вероятности с границами, равными границам разбиения, умноженному на количество чисел в распределении, которое задавалось пользователем.

3) Экспоненциальное распределение
Пользователю необходимо ввести интенсивность (величина обратная математическому ожиданию), количество чисел и диапазон (от 0 до указанного числа). Программа использует метод обратного преобразования. Пусть U – последовательность равномерно распределенных величин на отрезке (0;1), Х – искомая выборка, тогда

Итоговая формула:	![image](https://github.com/Yuriy567/main_repo/assets/141625927/b0f4e525-8949-4bc9-84f5-3c33468a9b5d)


4) Распределение Пуассона
Пользователь вводит математическое ожидание и количество испытаний.

5) Биномиальное распределение
Пользователь вводит вероятность успеха, число испытаний и необходимое количество чисел. Для создания используются испытания Бернулли.

## 3) Вывод в файл
Все созданные распределения сохраняются в excel файл под названием «Distributions.xlsx». Для каждого вида распределения существует свой лист, каждое распределение добавляется в новый столбец листа.
Для удобства была создана кнопка для очистки файла, при нажатии на которую содержимое файла бесследно удаляется.
Данные удобно импортировать в различные системы автоматизированного проектирования.


Чтобы войти в разработку данного проекта, нужно клонировать репозиторий, создать isuue, создать ветку с номером, таким же как номер issue, направить merge request на меня, установить все библиотеки из файлика requirements.txt, и начать разработку на любом интерпретаторе python.

В результате возможна генерация чисел только в определённых пределах и в определённых количествах. Теоретически высчитанно, что если брать диапазон длины 100. то возможна генерация порядка миллиона целых чисел или 15000 дробных чисел.

## Дальнейшие цели по усовершенствованию проекта: 
 - сейчас приложение генерирует выборки порядка 100000 чисел, нужно улучшить производительность программы, особенно для дробных чисел. Один из способов сделать ускорить генерацию выборок - сделать программу многопоточной.










