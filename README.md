# redactor
from tkinter import *
import tkinter as tk
from tkinter import ttk
from PIL import Image, ImageTk, ImageDraw, ImageGrab
from tkinter import messagebox, colorchooser
from tkinter import filedialog
import math


def draw(event):
    x1, y1 = (event.x - brush_size), (event.y - brush_size) # Получаем координаты верхнего левого угла овала
    x2, y2 = (event.x + brush_size), (event.y + brush_size) # Получаем координаты нижнего правого угла овала
    canvas.create_oval((x1, y1, x2, y2), fill=color, width=0)  # Создаем овал на холсте с указанными координатами и цветом
    draw_img.ellipse((x1, y1, x2, y2), fill=color, width=0)  # Рисуем овал в рисунке с указанными координатами и цветом

def chooseColor():
    global color
    (rgb, hx) = colorchooser.askcolor() # Открываем диалог выбора цвета и получаем выбранный цвет
    color = hx # Присваиваем переменной color выбранный цвет
    color_lab['bg'] = hx # Изменяем фон метки color_lab на выбранный цвет


# фун-ция, которая обновляет размер кисти на основе выбранного значения
def select(value):
    global brush_size
    brush_size = int(value)

# фун-ция, которая очищает холст и заполняет его выбранным цветом.
def pour():
    canvas.delete('all')
    canvas['bg'] = color
    draw_img.rectangle((0, 0, 1000, 500), width=0, fill=color)
    draw_img.rectangle((0, 0, 1000, 500), width=0, fill=color)

# фун-ция, которая очищает холст и устанавливает фоновый цвет на белый
def clear_canvas():
    canvas.delete('all')
    canvas['bg'] = 'white'
    draw_img.rectangle((0, 0, 1000, 500), width=0, fill='white')


#фун-ция сохранения файла в формате png
def save_as_png():
    file_path = filedialog.asksaveasfilename(defaultextension=".png", filetypes=[("PNG files", "*.png")])
    if file_path:  # Убедимся, что пользователь выбрал путь для сохранения файла
        x = root.winfo_rootx() + canvas.winfo_x()
        y = root.winfo_rooty() + canvas.winfo_y()
        x1 = x + canvas.winfo_width()
        y1 = y + canvas.winfo_height()
        ImageGrab.grab().crop((x, y, x1, y1)).save(file_path)

#фун-ция открытия файла в формате png
def open_png_file():
    file_path = filedialog.askopenfilename(filetypes=[("PNG files", "*.png")])
    if file_path:
        img = Image.open(file_path)

        window = tk.Toplevel()
        canvas = tk.Canvas(window, width=img.width, height=img.height)
        canvas.pack()

        img_tk = ImageTk.PhotoImage(img)

        canvas.create_image(0, 0, image=img_tk, anchor='nw')
        # фун-ция, которая позволяет при открытие файла рисовать чёрной кистью
        def draw(event):
            x, y = event.x, event.y
            draw_img = ImageDraw.Draw(img)
            draw_img.rectangle((x, y, x + 10, y + 10), fill=color)
            canvas.create_rectangle(x, y, x + 10, y + 10, fill=color)

        canvas.bind('<B1-Motion>', draw)

        window.mainloop()

# эта фун-ция обрабатывает событие щелчка мыши и отображает всплывающее меню в указанных координатах event.x и event.y
def popup(event):
    global x, y
    x = event.x
    y = event.y
    menu.post(event.x_root, event.y_root)

# подзаголовок создание фигур

# создание шестиугольника
def hexagon():
    radius = brush_size
    vertices = []
    for i in range(6):
        angle = i * (2 * 3.14159) / 6
        vertex_x = x + int(radius * math.cos(angle))
        vertex_y = y + int(radius * math.sin(angle))
        vertices.append((vertex_x, vertex_y))

    canvas.create_polygon(vertices, fill=color, width=0)
    draw_img.polygon(vertices, fill=color)

# создание треугольника
def triangle():
    points = [x, y, x + brush_size, y, x + brush_size / 2, y + brush_size]
    canvas.create_polygon(points, fill=color, width=0)
    draw_img.polygon(points, fill=color)

# создание квадрата
def square():
    canvas.create_rectangle(x, y, x + brush_size, y + brush_size, fill=color, width=0)
    draw_img.polygon((x, y, x + brush_size, y, x + brush_size, y + brush_size, x, y + brush_size), fill=color)

# создание круга
def circle():
    canvas.create_oval(x, y, x + brush_size, y + brush_size, fill=color, width=0)
    draw_img.ellipse((x, y, x + brush_size, y + brush_size), fill=color)

# создание линий
def line():
    canvas.create_line(x, y, x + brush_size, y + brush_size, fill=color, width=0)
    draw_img.line((x, y, x + brush_size, y + brush_size), fill=color)

# переменные для выполнения функций
x = 0
y = 0

# создание окна
root = Tk()
root.title("GraphInter")
root.geometry("1200x700")
root.iconbitmap(default="tiger.ico")
root.resizable(True, True)

# cоздаём переменную selected_theme типа StringVar(), которая будет использоваться для хранения выбранной темы
selected_theme = StringVar()
# cоздаём объект стиля style из модуля ttk, который позволяет настраивать внешний вид виджетов
style = ttk.Style()

# устанавливает начальный размер кисти равным 10
brush_size = 10
color = "black"

# настраиваем конфигурацию столбца 6 в окне root с весом 1, чтобы столбец мог растягиваться при изменении размера окна
root.columnconfigure(6, weight=1)
# настраиваем конфигурацию строки 2 в окне root с весом 1, чтобы строка могла растягиваться при изменении размера окна
root.rowconfigure(2, weight=1)


canvas = Canvas(root, bg="white")
# размещаем холст canvas в строке 2 и столбцах с 0 по 6 в окне root. padx и pady задают отступы по горизонтали и вертикали соответственно. sticky указывает, как холст будет растягиваться при изменении размера ячейки
canvas.grid(row=2, column=0, columnspan=7, padx=5, pady=5, sticky=E + W + S + N)

# привязываем функцию draw к событию движения мыши с зажатой левой кнопкой на холсте canvas
canvas.bind('<B1-Motion>', draw)
# привязываем функцию popup к событию щелчка правой кнопкой мыши на холсте canvas
canvas.bind('<Button-3>', popup)

# создаем объект меню menubar для окна root
menubar = Menu(root)

# создаем выпадающее меню file_menu в меню menubar. Параметр tearoff=0 отключает возможность отрывать меню от окна
file_menu = Menu(menubar, tearoff=0)
# добавляем команду "Сохранить как PNG" в меню file_menu и привязывает функцию save_as_png к этой команде
file_menu.add_command(label="Сохранить как PNG", command=save_as_png)
# добавляем команду "Открыть PNG файл" в меню file_menu и привязывает функцию open_png_file к этой команде
file_menu.add_command(label="Открыть PNG файл", command=open_png_file)
#  добавляем каскадное меню с названием "Файл" в меню menubar и связывает его с выпадающим меню file_menu
menubar.add_cascade(label="Файл", menu=file_menu)
# назначаем меню menubar в качестве главного меню для окна root
root.config(menu=menubar)

# создание скрытого меню для фигур
menu = Menu(tearoff=0)
menu.add_command(label="Шестиугольник", command=hexagon)
menu.add_command(label="Треугольник", command=triangle)
menu.add_command(label="Квадрат", command=square)
menu.add_command(label="Круг", command=circle)
menu.add_command(label="Линия", command=line)

#  создаем новое изображение image1 с размером 1000x500 пикселей и белым фоном
image1 = Image.new("RGB", (1000, 500), "white")
# создаем объект draw_img для рисования на изображении image1
draw_img = ImageDraw.Draw(image1)
# создаем метку с текстом "Параметры" и размещает ее в окне root в строке 0, столбце 0 с отступом по горизонтали 6 пикселей
Label(root, text='Параметры: ').grid(row=0, column=0, padx=6)
# создаем кнопку с текстом "Выбрать цвет" и размещает ее в окне root в строке 0, столбце 1 с отступом по горизонтали 6 пикселей. При нажатии на кнопку будет вызываться функция chooseColor
Button(root, text='Выбрать цвет: ', width=11, command=chooseColor).grid(row=0, column=1, padx=6)
# создаем метку color_lab с фоновым цветом color и шириной 10 пикселей.
color_lab = Label(root, bg=color, width=10)
# размещаем метку color_lab в окне root в строке 0, столбце 2 с отступом по горизонтали 6 пикселей
color_lab.grid(row=0, column=2, padx=6)
# создаем переменную v типа IntVar() со значением по умолчанию равным 10
v = IntVar(value=10)
# создаем ползунок Scale в окне root со значениями от 1 до 100
Scale(root, variable=v, from_=1, to=100, orient=HORIZONTAL, command=select).grid(row=0, column=3, padx=6)

# создаем метку с текстом "Действия" и размещает ее в окне root в строке 1, столбце 0 с отступом по горизонтали 6 пикселей
Label(root, text='Действия: ').grid(row=1, column=0, padx=6)
# создаем кнопку с текстом "Заливка" и размещает ее в окне root в строке 1, столбце 1. При нажатии на кнопку будет вызываться функция pour
Button(root, text='Заливка', width=10, command=pour).grid(row=1, column=1)
# создаем кнопку с текстом "Очистить" и размещает ее в окне root в строке 1, столбце 2. При нажатии на кнопку будет вызываться функция clear_canvas
Button(root, text='Очистить', width=10, command=clear_canvas).grid(row=1, column=2)
# запускаем главный цикл окна root, который обрабатывает события и отображает окно до его закрытия
root.mainloop()
