# Torre-de-hanoi
Este es un proyecto donde habia que desarrollar en codigo el juego de la torre de hanoi, donde se implemento la libreria de Python como Tkinder para la vista del usuario

from tkinter import *

# Global variables
torres = [[5,4,3, 2, 1], [], []]
disco_seleccionado = None

def dibujarTorres():
    canvas.delete("all")
    for i in range(3):
        x = 105 + i * 300
        canvas.create_rectangle(x, 500, x + 10, 100, fill="black")
    for fila in range(len(torres)):
        for col in range(len(torres[fila])):
            disco = torres[fila][col]
            if disco > 0:
                x = 105 + fila * 300
                y = 495 - col * 60
                width = 10 * disco
                color = "blue"
                if disco_seleccionado and disco_seleccionado[0] == disco and disco_seleccionado[1] == fila:
                    color = "red"
                canvas.create_rectangle(x - width, y, x + width, y + 15, fill=color)
    canvas.update()

def puede_mover(disco, columna_destino):
    if len(torres[columna_destino]) == 0:
        return True
    return disco < torres[columna_destino][-1]

def seleccionar_o_mover(event):
    global disco_seleccionado
    x, y = event.x, event.y
    columna_clic = x // 300

    if disco_seleccionado:
        disco = disco_seleccionado[0]
        fila = disco_seleccionado[1]
        if puede_mover(disco, columna_clic):
            torres[columna_clic].append(disco)
            torres[fila].pop()
            if len(torres[2]) == 5:
                mostrar_mensaje("¡Puzzle resuelto!")
        else:
            mostrar_mensaje("Movimiento incorrecto")
        disco_seleccionado = None
    else:
        if len(torres[columna_clic]) > 0:
            disco = torres[columna_clic][-1]
            disco_seleccionado = (disco, columna_clic)
    dibujarTorres()

def mostrar_mensaje(mensaje):
    mensaje_label.config(text=mensaje)
    mensaje_label.update()

root = Tk()
root.title("Torre de Hanoi")
root.geometry("800x600")

canvas = Canvas(root, bg="white", height=600, width=800)
canvas.pack()
canvas.bind("<Button-1>", seleccionar_o_mover)

start_button = Button(root, text="Iniciar", command=dibujarTorres)
start_button.pack()

mensaje_label = Label(root, text="", font=("Arial", 18))
mensaje_label.pack()

dibujarTorres()
root.mainloop()
