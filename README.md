# Algorithmic-data-analysis
Disclaimer: мои симпатии на этой [стороне](https://t.me/grey_zone).

Привет. Я решил ещё раз устроиться на работу DS и для этого я расскажу вам о части моего опыта, полученного в топ1 предприятии отрасли. Будет немного кода, много картинок, несколько графиков. Это скорее презентация, чем  гайд, но возможно кто-то сможет научиться здесь чему то полезному. Markdown я уже подзабыл, поэтому особо красиво не будет.

## [Cаундтрек](https://youtu.be/kcS-xmd4MMQ) для перезентации, часть 0.

В далёких годах в одной из конференций энтузиастов, которая сложились вокруг первого запуска Mlopen (все тогда горели желанием познать новую технологию, которую принёс нам Yorko и co и собирались по интересам в рамках броуновского движения в группы, которые грызли гранит науки сообща) людей, желающих научиться было много.

В настоящем из почти 20 человек кто начал этот путь в нашей конфе, в России остался работать только я, последние уехали недавно. Плюс кто-то не вкатился или не смог устроиться на работу джуном и плюнул. 

И это показатель, потому что наша конфа по качеству специалистов одна из лучших. Дефицит кадров будет усугубляться, но что более важно будет происходить снижение качества этих кадров. Человек, который умеет в t-sql и немного в pandas это не DS, это дата инженера пытаются натянуть на глобус.

Дальше будет хуже. Учебные курсы последних лет по специальности DS я видел - херня, выпускников ШАД с гит под копирку и пустым взглядом на собеседованиях тоже наблюдал, пожалуй, стоит показать, где заканчивается код, который вы можете нагуглить и начинается профессия.

А у вас часто офис говно и большая чать начальников серьёзно думает что считать нужно с 1, исправляйтесь.

# Многовекторныме графики анализа процессов.
## Plotly который DS забыли, потому что его надо писать на python.

Давным-давно в далёкой-далёкой галактике, в одном из первых уроков MLopen нам всем показали plotly из богоподобного фреймворка DASH, на котором написаны почти все приложения по "аналитике" на этой планете. Dashboard analytics отлично бьётся по любым поисковикам в разделе картинки, вот только доступность готовых решений напрочь отбила у общественности желание писать, что то сложное самому, зря. 

![Альтернативный текст](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/sync_or_die.jpg)

Это многовекторный график синхронизации процессов, который можно выжать из plotly. Необходимый для него import и ядро графика смотреть [здесь](https://github.com/HorusHeresyHeretic/Algorithmic-data-analysis/blob/main/syncrhonize_me.ipynb). Копипастом без служебной функции работать не будет. Остальное под свои данные сами напишите, если сможете.

Положения осей в графике не являються константой, в самых пиковых случаях линии тренда (синия и оранжевая) просто расходятся по вертикали как два сплошных потока событий. Также график отлично визуализирует все выбросы, которые выбиваються из общего ряда. Как и всё в dash он интерактивный и его можно рассылать по почте как самодостаточный html файл.

Бесценная вещь. Пошупать можно [здесь](https://raw.githubusercontent.com/HorusHeresyHeretic/Algorithmic-data-analysis/main/Относительно%20хорошо.html), но поскольку git глупый файл надо скачать и открыть в браузере.




Представьте себе, что у вас есть завод, на заводе автоматическая линия, в линии контроллеры, контроллеры управляют операциями, в которых есть заданные интервалы и это линия сбоит. А контроллеров штук 50-100 или 500.  

Или как вариант вы пытаетесь рассмотреть бизнес-процесс, в данном случае я визуализировал процесс эксплуатации ТС в масштабах одной из компаний Холдинга - всё как на ладони. В любой среде, в которой существует точка отчёта по времени этот график маст хев.

Чем ближе друг к другу линии тренда - тем более ритмично работает то, что вы рассматриваете, чем больше совпадений по вертикали выше линии тренда - тем выше общий процент операцией, завершение которых происходит одномоментно и наоборот.








