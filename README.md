# Предсказание выживания на Титанике
### Постановка задачи
Задана информация о 1309 пассажирах Титаника. Необходимо решить задачу бинарной классификации: для каждого человека научиться предсказывать, выживет ли он при крушении Титаника.


### Обработка данных
Данные загруженны со [страницы соревнования на Kaggle](https://www.kaggle.com/c/titanic). Обучающая выборка состоит из 891 объекта, а тестовая - из 418 объектов. Классы приблизительно сбалансированы - соотношение выживших к утонувшим: 38.4 к 61.6%. Для объектов из тестовой выборки предсказывается целевая переменная: 1 - пассажир выживет, 0 - пассажир утонет.

### Обработка признаков
Для каждого пассажира указаны следующие признаки:
* Номер каюты - значение: строка (пропущено более 75% значений в обучающей и тестовой выборках) - не используется из-за большого количества пропусков.
* Класс билета - значения: 1, 2, 3 (пропусков нет).
* Полное имя - значение: строка (пропусков нет) - не используется, на основе имени сформирован титул.
* Пол - значения: male, female (пропусков нет).
* Количество родственников своего поколения (братья, сестры, супруг/супруга) - значения: 0 - 8 (пропусков нет).
* Количество родственников поколений старше и младше (мать, отец, дети) - значения: 0 - 9 (пропусков нет).
* Стоимость билета - значения: 0 - 512 (пропущено одно значение в тестовой выборке, пропуск заполнен на основе медианной стоимости билета по соответствующему значению признака титул_класс билета) - значения  прологарифмированы, чтобы приблизить распределение признака к нормальному.
* Порт посадки - значения: Cherbourg, Queenstown, Southampton (пропущено два значения в обучающей выборке, пропуски заполнены модой - значением Southampton).
* Возраст - значения: 0 - 80 (пропущено примерно по 20% значений в обучающей и тестовой выборках, для предсказания пропущенных значений используется [случайный лес](http://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestRegressor.html) из библиотеки [Scikit-learn](http://scikit-learn.org/stable/index.html) с рандомизированным подбором параметров по сетке).

Дополнительно сгененированы следующие признаки:
* Титул (на основе имени) - значения: Mr, Master, Miss, Mrs (пропусков нет).
* Титул_класс билета (на основе титула и класса билета) - значения: Mr1, Mr2, Mr3, Master1, Master2, Master3, Miss1, Miss2, Miss3, Mrs1, Mrs2, Mrs3 (пропусков нет).
* Количество родственников (суммарное количество родственников своего поколения и поколений старше и младше) - значения: 0 - 10 (пропусков нет).

### Построение модели
Метрикой качества является доля верных ответов. Реализовано пять моделей предсказания целевой переменной:
* Гендерная модель:
	* Все мужчины утонут.
	* Все женщины выживут.
	* Значение метрики качества: 0.76555.
* Простая модель:
	* Все мужчины утонут.
	* Все женщины и дети из 1 и 2 классов выживут.
	* Среди женщин и детей из 3 класса выживут те, у кого меньше 4 родственников на борту; остальные утонут.
	* Значение метрики качества: 0.78947.
* Гибридная модель:
	* Все мужчины из 2 и 3 классов утонут.
	* Все женщины и дети из 1 и 2 классов выживут.
	* Среди детей мальчиков из 3 класса выживут те, у кого меньше 4 родственников на борту; остальные утонут.
	* Для предсказания выживания мужчин из 1 класса, детей девочек из 3 класса и женщин из 3 класса используется [cлучайный лес](http://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html) из библиотеки [Scikit-learn](http://scikit-learn.org/stable/index.html).
	* Значение метрики качества: 0.77990.
* Модель решающего дерева:
	* Для предсказания выживания всех пассажиров используется [решающее дерево](http://scikit-learn.org/stable/modules/generated/sklearn.tree.DecisionTreeClassifier.html) из библиотеки [Scikit-learn](http://scikit-learn.org/stable/index.html).
	* Значение метрики качества: 0.78947.
* Модель XGBoost:
	* Для предсказания выживания всех пассажиров используется [градиентный бустинг над решающими деревьями](http://xgboost.readthedocs.io/en/latest/python/python_api.html#xgboost.XGBClassifier) из библиотеки [XGBoost](http://xgboost.readthedocs.io/en/latest/).
	* Подобранные параметры: n_estimators=43, learning_rate=0.05, max_depth=6, min_child_weight=6.
	* Значение метрики качества: 0.81818.
