# Torre-de-hanoi
Este es un proyecto donde habia que desarrollar en codigo el juego de la torre de hanoi, donde se implemento la libreria de Python como Tkinder para la vista del usuario

from tkinter import *
import random

# Variables globales
torres = [[], [], []]
disco_seleccionado = None
movimientos = 0
juego_completado = False

def dibujarTorres():
    canvas.delete("all")
    for i in range(3):
        x = 105 + i * 300
        canvas.create_rectangle(x, 500, x + 10, 100, fill="Black")
    for fila, torre in enumerate(torres):
        for col, disco in enumerate(torre):
            if disco > 0:
                x = 105 + fila * 300
                y = 485 - col * 60
                width = 10 * disco
                if disco == torres[fila][-1]:
                    width += 5  # Incrementa el ancho del disco
                color = random.choice(["red", "green", "blue", "yellow", "purple", "orange"])
                if disco_seleccionado and disco_seleccionado[0] == disco and disco_seleccionado[1] == fila:
                    color = "white"
                canvas.create_rectangle(x - width, y, x + width, y + 15, fill=color)
    mov_label.config(text=f"Movimientos: {movimientos}")
    canvas.update()

def puede_mover(disco, columna_destino):
    if len(torres[columna_destino]) == 0:
        return True
    return disco < torres[columna_destino][-1]

def seleccionar_o_mover(event):
    global disco_seleccionado, movimientos, juego_completado
    x, y = event.x, event.y
    columna_clic = x // 300
    if juego_completado:
        return
    if disco_seleccionado:
        disco, fila = disco_seleccionado
        if puede_mover(disco, columna_clic):
            torres[columna_clic].append(disco)
            torres[fila].pop()
            if len(torres[columna_clic]) == 3:
                mostrar_mensaje("¡Juego resuelto!", 2)
                juego_completado = True
            movimientos += 1
            if len(torres[columna_clic]) == 5:
                mostrar_mensaje("¡Juego resuelto!", 2)
                juego_completado = True
            if len(torres[columna_clic]) == 7:
                mostrar_mensaje("¡Juego resuelto!", 2)
                juego_completado = True
            
        else:
            mostrar_mensaje("Movimiento incorrecto", 2)
        disco_seleccionado = None
    else:
        if len(torres[columna_clic]) > 0:
            disco = torres[columna_clic][-1]
            disco_seleccionado = (disco, columna_clic)
    dibujarTorres()

def limpiar_pantalla():
    global torres, movimientos, juego_completado
    torres = [[], [], []]
    movimientos = 0
    juego_completado = False
    dibujarTorres()

def mostrar_mensaje(mensaje, tiempo):
    mensaje_label.config(text=mensaje)
    mensaje_label.update()
    root.after(tiempo * 1000, lambda: mensaje_label.config(text=""))
    dibujarTorres()

def mostrar_sugerencia():
    sugerencia = ""
    for fila, torre in enumerate(torres):
        for col, disco_actual in enumerate(torre):
            for columna_destino in range(3):
                if disco_actual > 0 and puede_mover(disco_actual, columna_destino) and columna_destino != 3:
                    sugerencia = f"Mueve el disco {disco_actual} de la columna {fila + 1} a la columna {columna_destino + 1}."
                    break
            if sugerencia:
                break
        if sugerencia:
            break
    if sugerencia:
        mostrar_mensaje(sugerencia, 2)
    else:
        mostrar_mensaje("No hay sugerencias disponibles en este momento.", 2)

def iniciar_juego(n_discos):
    global torres, movimientos, juego_completado
    torres = [list(range(n_discos, 0, -1)), [], []]
    movimientos = 0
    juego_completado = False
    dibujarTorres()

root = Tk()
root.title("Torre de Hanoi")
root.geometry("800x600")

canvas = Canvas(root, bg="white", height=500, width=800)
canvas.pack(side=TOP)

frame = Frame(root)
frame.pack(side=BOTTOM, pady=10)

facil_button = Button(frame, text="Fácil", command=lambda: iniciar_juego(3))
facil_button.pack(side=LEFT, padx=10)

medio_button = Button(frame, text="Medio", command=lambda: iniciar_juego(5))
medio_button.pack(side=LEFT, padx=10)

dificil_button = Button(frame, text="Difícil", command=lambda: iniciar_juego(7))
dificil_button.pack(side=LEFT, padx=10)

ayuda_button = Button(frame, text="Ayuda", command=mostrar_sugerencia)
ayuda_button.pack(side=RIGHT, padx=10)

mov_label = Label(root, text=f"Movimientos: {movimientos}", font=("Arial", 14))
mov_label.pack(pady=10)

mensaje_label = Label(root, text="", font=("Arial", 18))
mensaje_label.pack(pady=10)

dibujarTorres()

root.bind("<Button-1>", seleccionar_o_mover)
root.mainloop()
