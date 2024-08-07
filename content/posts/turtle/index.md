---
title: "Черепашья графика"
date: 2024-05-15
draft: false
description: ""
tags: ["python", "modules", "turtle"]
series: ["Python"]
series_order: 6
---

{{< lead >}}
Графическая библиотека, встроенная в язык Python.
{{< /lead >}}

## **Модуль turtle**
Для использования: `import turtle`

Чтобы открыть графическое окно и держать его активным, нужно использовать следующий код:
```py
import turtle
window = turtle.Screen()
turtle.showturtle()
# Какой-нибудь код
window.mainloop()  # Пишется в конце программы
```

---
### Методы
* `forward(p)` – перемещает "черепашку" вперёд по направлению взгляда на `p` пикселей; `backword(p)` – перемещает черепашку назад на `p` пикселей.
*  `left(d)` и `right(d)` поворачивают черепашку влево и вправо соответственно на `d` градусов; `setheading(d)` – устанавливает угол `d`; `heading(d)` – вдобавок к функционалу `setheading()` возвращает текущий угол в градусах.
* `circle(p)` – рисует круг с радиусом `p`; `dot()` – рисует точку; `stamp()` – оставляет штамп черепашки на графике.
* `shape('name')` – изменяет внешний вид черепашки (square, arrow, circle, turtle, triangle, classic); с помощью `turtle.Screen().addshape('file')` можно установить кастомную черепашку; `hideturtle()` прячет черепашку; а `showturtle()` вновь отображает её.
* `speed(n)` – устанавливает скорость анимации `n`, принимающую значения от 0 до 10, при этом 0 отключает анимацию и черепашка перемещается мгновенно.
* `penup()` – поднимает перо; а `pendown()` – опускает его; `pensize(p)` – устанавливает ширину пера в `p` пикселей; `pencolor('color')` – устанавливает цвет пера (если хотим использовать формат RGB, т.е. `pencolor(130, 240, 200)`, то нужно использовать команду `turtle.Screen().colormode(255)`).
* `turtle.Screen().bgcolor('color')` позволяет установить цвет фона (также можно задать RGB формат); с помощью `turtle.Screen().bgpic('file')` можно установить фоновое изображение.
* `clear()` – стирает все в графическом окне (положение черепашки, заданные цвета рисунка и фона остаются неизменными); `reset()` – стирает рисунки и сбрасывает положение черепашки и заданный цвет рисунка (цвет фона остаётся неизменным); `clearscreen()` – возвращает экран в исходное состояние.
* `turtle.Screen().setup(w, h)` – устанавливает размер окна в ширину `w` и высоту `h` в пикселях.
* `goto(x, y)` – перемещает черепашку в координаты (x, y) на экране (при опущенном пере линии будут рисоваться); `pos()` или `position()` возвращает кортеж координат черепашки; `xcor()` возвращает только координату по x; а `ycor()` – по y.