# Algorithmic-data-analysis
Disclaimer: мои симпатии на этой [стороне](https://www.youtube.com/watch?v=kcS-xmd4MMQ).

Привет. Я решил ещё раз устроиться на работу DS и для этого я расскажу вам о части моего опыта, полученного в топ1 предприятии отрасли. Будет немного кода, много картинок, несколько графиков. Это скорее презентация, чем  гайд, но возможно кто-то сможет научиться здесь чему то полезному. Markdown я уже подзабыл, поэтому особо красиво не будет.

Для примера я буду использовать датасет полученный из корпоративной 1С, первичный документ учёта ремонта ТС в РММ крупного предприятия. Принято считать, что данные это новая нефть, возможно, но в России цена добычи - золото.  1Ска (помимо того что она пишет даты по госту, а не в стандарте iso) не самый приятный источник данных для DS, но другого у нас нет. 

Из плюсов именно 1Ска хранит неисчерпаемый источник raw data и подчиняется учётной политике. Из минусов даже если учётная политика глобальна, как правило каждое предприятие обычно ведёт учёт `привычным` им образом, до того как встретит DS. 

Пример алгоритма, который можно написать вокруг данных из 1С можно посмотреть [здесь](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/i7%2B64GB%2Bruntime%2B3%2Bhours.ipynb). Может ругаться на unable to render - открыть в raw, потом снова открыть нормально, заработает. Поскольку в данных используются временные ряды, фразу `пример для 1С` можно зачеркнуть.

На входе 8 датасетов из 8 разных предприятий (пример файла исходника ниже на картинке), на выходе 300+ файлов. 90% людей которым я показывал это код, кричали что то про инквизицию (концепция dataframe как кеш не для всех), поэтому объяснять буду слайдами. 

Остальные считают, что мне нужно брать по мульту в месяц с заказчиков, поскольку концепция существенно облегчает работу смежных DBA и вообще это больше похоже на готовое приложение, которые осталось обернуть в web, чем на обычную тетрадку наполненную `идеями`.

Исходник данных скачать не дам, на входе алгоритма 8 таких датасетов. 

![Альтернативный текст](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/1С%20он%20такой%20мог%20бы%20быть%20и%20получше.jpg)

На слайде типичный вывод из 1ски глазами DS. Что удобно - у каждого документа в 1ске всегда есть номер документа, а также как правило дополнительный уникальный id, то к чему этот номер относится, плюс справочники которые оператор 1с использует для заполнения документа. 

Документы с датами для DS всегда имеют большую ценность, остальное можно смело отдавать в отдел `ананлитики` (тем людям, которые пользуются sql, excel и считают себя аналитиками данных) они быстрее и лучше дадут результат там, где он есть.

Всё остальное на нас. 

## Вступление, часть 0.

В далёких годах в одной из конференций энтузиастов, которая сложились вокруг первого запуска Mlopen (все тогда горели желанием познать новую технологию, которую принёс нам Yorko и co и собирались по интересам в рамках броуновского движения в группы, которые грызли гранит науки сообща) людей, желающих научиться было много.

В настоящем из почти 20 человек кто начал этот путь в нашей конфе, в России остался работать только я, последние уехали недавно. Плюс кто-то не вкатился или не смог устроиться на работу джуном и плюнул. 

И это показатель, потому что наша конфа по качеству специалистов одна из лучших. Дефицит кадров будет усугубляться, но что более важно будет происходить снижение качества этих кадров. Человек, который умеет в t-sql и немного в pandas это не DS, это дата инженера пытаются натянуть на глобус.

Дальше будет хуже. Учебные курсы последних лет по специальности DS я видел - херня, выпускников ШАД с гит под копирку и пустым взглядом на собеседованиях тоже наблюдал, пожалуй, стоит показать, где заканчивается код, который вы можете нагуглить и начинается профессия.

А у вас часто офис говно и большая чать начальников серьёзно думает что считать нужно с 1, исправляйтесь.

[Трек](https://youtu.be/MkgR0SxmMKo) для чтения гайда.

# Многовекторные графики анализа, часть 1.
## Plotly который DS забыли, потому что его надо писать на python.

Давным-давно в далёкой-далёкой галактике, в одном из первых уроков MLopen нам всем показали plotly из богоподобного фреймворка DASH, на котором написаны почти все приложения по "аналитике" на этой планете. Dashboard analytics отлично бьётся по любым поисковикам в разделе картинки, вот только доступность готовых решений напрочь отбила у общественности желание писать, что то сложное самому, зря. 

![Альтернативный текст](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/sync_or_die.jpg)

Это многовекторный график синхронизации процессов, который можно выжать из plotly. Необходимый для него import и ядро графика смотреть [здесь](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/syncrhonize_me.ipynb). Копипастом без служебной функции работать не будет. Остальное под свои данные сами напишите, если сможете.

Положения осей в графике не являються константой, в самых пиковых случаях линии тренда (синия и оранжевая) просто расходятся по вертикали как два сплошных потока событий. Также график отлично визуализирует все выбросы, которые выбиваються из общего ряда. Как и всё в dash он интерактивный и его можно рассылать по почте как самодостаточный html файл.

Бесценная вещь. Пошупать можно [здесь](https://raw.githubusercontent.com/HorusHeresyHeretic/Algorithmic-data-analysis/main/Относительно%20хорошо.html), но поскольку git глупый файл надо скачать и открыть в браузере.




Представьте себе, что у вас есть завод, на заводе автоматическая линия, в линии контроллеры, контроллеры управляют операциями, в которых есть заданные интервалы и это линия сбоит. А контроллеров штук 50-100 или 500.  

Или как вариант вы пытаетесь рассмотреть бизнес-процесс, в данном случае я визуализировал процесс эксплуатации ТС в масштабах одной из компаний Холдинга - всё как на ладони. В любой среде, в которой существует точка отчёта по времени этот график маст хев.

Чем ближе друг к другу линии тренда - тем более ритмично работает то, что вы рассматриваете, чем больше совпадений по вертикали выше линии тренда - тем выше общий процент операций, завершение которых происходит одномоментно и наоборот.

# Потоковые вычисления, часть 2.
## Перестаньте писать классы, если не умеете писать функции.

Pandas хорош, но кое чего в нём не хватает: встроенного интератора, который бы позволял применять потоковые вычисления к уникальным объектам внутри датафрейма. Каждый DS решает эту проблему как умеет, но я считаю что мой вариант лучший. 

Для тех кто ничего не понял - объясняю: в Pandas нельзя просто так взъять и сказать, а нука примени 57 функций, 17 встроенных методов и 117 математических выражений к КАЖДОМУ уникальному объекту внутри датафрейма, который я, человек вижу.

Например у вас есть логи клиентов контакт-центра, у каждого клиента есть ID и десяток колонок справа, а вы хотите к каждому ID применить десяток математических методов, но так, чтобы при вычислении учитывалась вся дополнительная информация привязанная к этому ID (в колонках справа от него). 

Обычно пишут векторизацию - методы применяются к строке которую выдернули из датафрейма по индексу, а потом к следующей. Но есть метод проще (и уже давно, я придумал это ещё в 2018 гг). Поэтому встречайте - функция которая использует КЛЮЧ.

hard_nn это датафрейм который прогнали через фильтры и прочие радости жизни.

warp_field_generator это (ох лол) это генератор, который мы передаём в функцию.

Фактически это косплей векторизации по ключу в датафрейме, благодоря которому мы не только экономим ресурсы, но и..
![Альтернативный текст](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/мой%20старый%20костыль.jpg)

можем применить любые выражения, методы, другие функции и встроенные классы к объекту внутри датафрейма.
![Альтернативный текст](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/считай%20меня%20считай%20меня%20полностью.jpg)

и посмотреть что получилось в принтах дёрнув функцию, которая сама дёрнет генератор.
![Альтернативный текст](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/дебаг%20на%20принтах%20наше%20всё.jpg)

С одной стороны это удобно: можно написать большой блок различных операций, который вы хотите применить к объектам внутри датафрейма большой функцией, а потом гонять её генератором по всему датафрейму.  С другой стороны генератор который мы передали в функцию вычисления глупый, остановится как только засбоит одна из операций счисления в функции.

В более поздней версии другого алгоритма, я управляю процедурой генератора более простым способом, но иногда это лучше делать через try и использовать соглашение с переменной. см.пример.

![Альтернативный текст](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/помогаем%20генератору%20работать.jpg)

# Управление выводом потоковых вычислений, часть 3.
## Что за ужас написан в последней картинке? 

Вывод функции test = pony_simple_pony(soft_df) представленный выше и отображенный на принтах, будет выглядеть так.
![Альтернативный текст](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/няш%20мяш%20крым%20наш.jpg)

Поэтому вывод фукнции надо сложить заново в датафрейм, для этого надо использоать кеш, пример: Кеш это one = pd.DataFrame, складывать вывод функции будем туда.
![Альтернативный текст](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/больше%20функций%20богу%20функций.jpg)

Благодоря вшитому в python сборщику мусора костыль с генератором и функцией сборки можно повторять бесчисленное количество раз в рамках одного алгоритма. Т.е можно написать несколько больших функций счисления и дернуть потом всё вместе. 

Технически мы зашили yield в функцию, которая проводит вычисления, когда мы дёргаем эту функцию, она перебирает объекты, по ключу, который мы указали и к каждому объекту применяет указанный блок операций. Для того что бы получить из этого полноценный датафрейм, просто нужно указать:

- куда складывать вывод из функции и как. 
- что с ним сделать потом в другом блоке.

# Не надо так, зачем? и почему не по pep8, часть 4.
## Потому что так есть место для мультивывода, if else и try except.

1. Если писать такие вещи через pep8 то с 4 пробелами и таким уровнем вложенности код станет совсем нечитабельным, поэтому пробелов должно быть 3.
2. Нейминг не ваше дело, я ведущий специалист, как хочу так и обзываю ссылки. А если серьёзно то такие вещи пишутся по потоку, при таком подходе действительно можно воткнуть наушники и засесть на пару недель в блок счисления сголовой, поскольку во-первых не нужно перескакивать с вычислительной части на init,а во вторых всегда можно дернуть кусок алгоритма, и погонять блок счисления безотносительно от общей массы. 
3. Возможность сохранять промежуточный вывод блоков счисления. Если вы заметили, в какой то момент я стал передавать в последующие функции вывод уровня (name[n][n][n],name[n][n][n],name[n][n][n],name[n][n][n] еtc) и обращаться к ним по индексу. Поскольку после каждого блока счиления есть функция сборки результатов счисления в датафрейм, мы фактически жонглируем датафреймами. И всегда можем их сохранить.
![Альтернативный текст](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/больше%20датафреймоф%20богу%20датафреймоф.jpg)

5. При таком подходе в блоках счисления можно писать if else (люди которые не пишут их видимо всегда работают с чистыми данными и никогда не ошибаются) во избежании череды неприятностей. Поскольку каждый раз мы пересобираем датафрейм из результатов счисления - неприятности, весьма распространённая вещь. Ведь заранее вы не знаете, будет ли работать ваш блок счисления или нет. Например 50-100 раз одна из операций спотнулась на счислении, ветвление позволит, например, задать дефолтные значения в случае неудачи. Иначе никак - нельзя отдавать в pd.DataFrame() что-то с ошибками, засбоит.  

6. Не бывает маленьких или больших данных, бывают кривые ркуи и отсутствие воображения. В примере который я привел, на входе 8 датафреймов, на выходе 305, из них более половины не являються служебными и так или иначе были использованы. Также стоит отметить что не всегда конечный результат, который вы наметили себе в разработке зайдёт заказчику. 

7. Сам заказчик иногда толком не знает что он хочет получить, может ухватится за промежуточный результат и изменить ТЗ, но в данном случае это имеет смысл. Имеет смысл показывать промежуточные таблицы руководителям (кто захочет - разберётся). Примерно треть вывода в этом алгоритме это то, что увидели люди в промежуточных таблицах и попросили допилить, своей рукой.

8. Так степень свободы выше. Возможность управлять процессом вычислений в отношении одного объекта, а потом собирать результат в витрину позволяет управлять логикой алгоритма `вглубине`. Если писать на витрину, то код имеет общий вид: сделай `вот это` с данными в витрине, если писать на объект то код имеет предметный вид: сделай с объектом в витрине `вот это`, а потом собери в витрину. 

9. Недостатки: времени на разработку надо вагон.

# Machine Learning и алгоритмы, часть 5.
## try как инструмент управления.

Последняя и пожалуй самая важная часть. Большинство из вас просто рандомно перебирают параметры на кроссвалидации, а зачем? когда машина сама может это сделать? [Код](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/December_coub.ipynb) подобного алгоритма вокруг Холта-Винтерса.

![Альтернативный текст](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/machine%20learning%20глупое%20модное%20слово.jpg)

В том же потоке запускаем функцию кеширования для формирования вывода. 
![Альтернативный текст](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/кеш%20в%20который%20не%20умеют%20почти%20все.jpg)

И учим алгоритм самостоятельно принимать решения через try.
![Альтернативный текст](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/компьютер%20попробуй%20подумать%20за%20меня.jpg)

Для любителей кегли, могу показать значения среднеквадратичной ошибки на мусоре с кооф.вариации выше 50%, это дебаг на принтах 16 моделей, которые создаются в рамках алгоритма на один объект с двумя разными типами предсказаний и вариативностью суждений.
![Альтернативный текст](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/это%20всё%20тот%20же%20алгоритм%20с%20дебагом%20на%20принтах.jpg)

Читать как model_name наименьшее значение среднеквадратичной ошибки на типах сглаживания [1],[2],[3] при кроссвалидации. Кто найдёт в коде остальный хинты, с помощью которых алгоримт самостоятельно настраивает параметры модели - станет великим программистом, а потом захочет бросить всё это.

Конечно, с точки зрения книжки Дрейка выглядит всё это не красиво, Python это компактные читаемые классы, а не нагромаждение фукнций, но! с точки зрения DS `таков путь`. На том же Mlopen учат как писать код в тетрадках, не более. А как вы собиратетесь работать с этими тетрадками? выбросите?

Вы отдадите только модель в pipeline, например так как написано [здесь](https://habr.com/ru/post/591873/). Модель будет работать по метрикам и отклонениям в проде, всё. А когда все возможные методы, дающие приемлемый результат кончатся, вы просто скажите: `данные плохие - это невозможно`.

А потом кто нибудь из IBM напишет полноценную программу, которая полностью заменит этот процесс на алгоритм и вы пойдёте писать web. В статье, ссылку на которую я дал есть хорошая картинка.

![Альтернативный текст](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/ml%20code%20or%20ML%20CODE.jpg)

Человек, который её нарисовал, не смог написать алгоритм, в котором `ML code` сам решает, какую модель отдать в прод заранее перебрав несколько опций и сравнив их по значению среднеквадратичной ошибки. 

# Перспективы алгоритмического подхода, часть 6.
## Второй круг исследований - дорого и долго.

Я не планировал городить такой огород, как в первом примере, мне просто нужны были метрики, которые можно было бы приложить к паре временных рядов для того что бы выработать правила сцены. Но очень быстро мой компьютер стал окном в новый мир для пачки директоров.

В результате мы шлим по схеме:

- первичный ананлиз данных.
- алгоритм.
- суждение компетентного человека.
- новый алгоритм.
- принятие решений человеком по результатам чтения.
- ds который хочет закончить писать `это` и писать другое.

Большинство данных на этой планете - мусор, мусор, который накапливался задолго до того как пришли DS со своими правилами оценки данных. И есть много компетентных людей, которые знают что стоит за этим мусором - учёные и управленцы.

Люди которые мыслят в рамках незаконченных теорий или суждений с погрешностью, о размерах которой они не знают (я имею ввиду принятие решений в условиях многих неизвестных). Этим людям нужно решить задачу, отталкиваясь уже от известного,  что бы начать решать следующую.

Если вы работаете в тандеме с людьми, которые мыслят, не являются программистами и которые владеют компетенциями по предметной области - пишите алгоритмы и носите людям распечатки вывода, возможно вас попросят написать ещё один :)

Бизнес уже готов к этому, я работал в тандеме с MBI последней волны (второй половины 10ых годов) и он отлично дополнял своей головой анализ процессов по временным рядам и радостно рисовал что ещё ему такого дать. 

Ну и не стоит забывать, что рано или поздно мы посчитаем всё, что в теории можно было посчитать и останется такая малость как всё остальное, что мы считать не хотели - вот эти вот терриконы пентабайт-гигабайт всего и вся и про всё. И в первую очередь и в науке. Наука будет радо тому, что кто-то придёт и по второму кругу прогонит все модели ещё раз во всех местах с более глубокой настройкой, чем раньше. 

# Заключение, слабонервным просьба не читать.
## disclaimer, я серьёзно гайд кончился, ниже будет имхо.

Я написал гайд для того что бы не попасть в неловкую ситуацию, когда на собеседовании сталкиваешься с адекватным человеком и возможно будущим начальником, который не понял твой код, для того что бы этот человек не исполнял всякое, ввиду расстройства.

Я в гробу видал доцентов и профессоров всего и вся, которые сейчас иногда местами сидят начальниками в DS подразделениях и из моего кода понимают только строку коммита, а ещё при этом пытаются задавать вопросы по теории, своего сына школьника гоняй по теории.

Моё лицо ( лицо человека который закрывал [Mlopen](https://habr.com/ru/company/ods/blog/322626/_) в 2017-18 году ) когда я вижу этих людей с претензией на превосходство по причине того, что у них корочка из прошлого века или доцентов МФТИ толкающие курсы по скулю (как они пишут по DS) за 120К в месяц.

![Альтернативный текст](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/моё%20лицо%20когда%20я%20читаю%20ваши%20ТЗ.jpg)

В сложившихся реялиях работу за 150-200К я себе легкой найду, мне по кайфу, просто как то нелогично получится - столко лет учиться и прогрессировать что бы потом выбросить без малого 5-6 лет жизни. 

![Альтернативный текст](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/if30VRsOKWc.jpg)

1. Я хочу сделать активные наушники, которые будут через нейросеть слушать сцену и семафорить бойцу о том откуда и что летит. Раньше такое было сделать нельзя, но сейчас есть лайфак. Но в одного я такую вещь не напишу. Могу возглавить проект.
2. Целиком и полностью признаю что есть люди лучше меня как DS, я вот например умеют писать алгоритмы и помимо всего того что давали на Mlopen знаю sarima модель, кто-то хорошо пишет регрессии, другие просто ассы в свёрточных сетях.
3. Из России я уезжать не собираюсь, пожалуйста прекратите мне с этим надоедать. Задолбали, Питер тоже не предлагать. Девушки красивые, погода говно.
4. Я не военнообязанный и мой работодателей может быть уверен в том, что меня не загребут под призыв резервистов, но легко найду дорогу сам. Особой разницы между здоровым зданием Сбера с маленькой столовой и местами дислокации V и Z не вижу. Опенспейс, туалет где-то далеко, еду с собой и при этом заметьте, в сбере нельзя курить :)
5. Я бы хотел поработать с geo pandas или послушать сильных математиков на предмет допиливания моей реализации Холта-Винтерса, также легко впишусь в большой бизнес, предыдущая компания считала ярдами.
6. тестовое задание можете начинать делать прям щас - код в этом репозитории вполне подойдёт.
7. по деньгам хз, я думаю меньше чем 300К моветон, лучше 400, однако, раз уж вы это прочитали, то по ситуации (вдруг попадётся интересный проект, например бпла) можно меньше. У меня другие критерии “много/мало”, складываются из совокупности факторов: люди, офис, задача, мощный комп на халяву, годовые премии, дмс и т.д.
8. концерн сименс будет делать беспилотное метро без меня.
9. пункт 8 в моём резюме на hh, я серьёзно. Мой батя запускал Буран от кб Салют, теперь, когда я овладел новыми технологиями, думаю есть пара теоретических вариантов, куда можно посмотреть.
10. про t-sql:  

- нахрена мне скуль, если у меня есть [import sqlite3](https://pythonworld.ru/obrabotka-dannyx/pandas-cookbook-8.html) 
- у меня брат DBA и он всегда может взять вторую работу на полставки, от 100К.
- я DS, который пишет на python и растит скилл на python, всё остальное не интересно.
- лично мне всегда хватало csv`шек и хранимых процедур на сервере, которые уже были кем-то написаны.
- в случае если вы используете 1С то скуль вообще не нужен (проверенно) 1c`ник и DS сами договорятся между собой, ибо у 1c`ника функционал по выгрузке данных огого.

11. про холивары "скуль не нужен, питонисты не нужны и т.д". Не холиварю, для того что бы запустить ракету в космос нужны все, никогда этого не понимал - зачем один it-специалист холиварит на работе с другим только потом что работают в разных предметных областях, пишут на разных языках и т.д.


И как следует из анимешных картинок в моём гит, с людьми, которые плюнули на всё и в  уютном ламповом месте просто ради удовольствия пишут код, потому что так самовыражаются в этом мире - я всегда найду общий язык.
