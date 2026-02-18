import csv
from typing import List, Tuple
import matplotlib.pyplot as plt
import tkinter as tk
from tkinter import filedialog
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg

Point = Tuple[float, float]


def leer_puntos_csv(ruta_csv: str) -> List[Point]:
    """Lee un CSV con encabezados x,y y regresa una lista de tuplas (x,y)."""
    puntos: List[Point] = []
    with open(ruta_csv, "r", encoding="utf-8") as f:
        reader = csv.DictReader(f)
        for row in reader:
            x = float(row["x"])
            y = float(row["y"])
            puntos.append((x, y))
    return puntos


def punto_mas_izquierdo(puntos: List[Point]) -> int:
    """
    Regresa el índice del punto más a la izquierda.
    En empate de x, escoger el de menor y (para hacerlo determinista).
    """
    idx = 0
    for i in range(1, len(puntos)):
        if puntos[i][0] < puntos[idx][0] or (puntos[i][0] == puntos[idx][0] and puntos[i][1] < puntos[idx][1]):
            idx = i
    return idx


def orientacion(a: Point, b: Point, c: Point) -> float:
    """
    Regresa el valor del producto cruz (cross product).
    """
    return (b[0] - a[0]) * (c[1] - a[1]) - (b[1] - a[1]) * (c[0] - a[0])


def distancia2(a: Point, b: Point) -> float:
    """Distancia al cuadrado (evita usar sqrt, no hace falta para comparar)."""
    dx = a[0] - b[0]
    dy = a[1] - b[1]
    return dx * dx + dy * dy


def convex_hull(puntos: List[Point]) -> List[Point]:
    """
    Algoritmo Jarvis March.
    """
    if len(puntos) < 3:
        return puntos[:]

    hull: List[Point] = []
    start_idx = punto_mas_izquierdo(puntos)
    p_idx = start_idx

    while True:
        hull.append(puntos[p_idx])
        q_idx = (p_idx + 1) % len(puntos)

        for r_idx in range(len(puntos)):
            if r_idx == p_idx:
                continue

            o = orientacion(puntos[p_idx], puntos[q_idx], puntos[r_idx])
            if o > 0:
                q_idx = r_idx
            elif o == 0:
                if distancia2(puntos[p_idx], puntos[r_idx]) > distancia2(puntos[p_idx], puntos[q_idx]):
                    q_idx = r_idx

        p_idx = q_idx
        if p_idx == start_idx:
            break

    return hull


def dibujar(puntos: List[Point], hull: List[Point], ax, canvas, titulo: str = "Convex Hull"):
    """Dibuja puntos y el polígono del hull en el canvas de Tkinter."""
    ax.clear()
    xs = [p[0] for p in puntos]
    ys = [p[1] for p in puntos]
    ax.scatter(xs, ys)

    if len(hull) >= 2:
        hx = [p[0] for p in hull] + [hull[0][0]]
        hy = [p[1] for p in hull] + [hull[0][1]]
        ax.plot(hx, hy, color='red')

    ax.set_title(titulo)
    ax.set_xlabel("x")
    ax.set_ylabel("y")
    canvas.draw()


def seleccionar_archivo(ax, canvas):
    ruta = filedialog.askopenfilename(filetypes=[("CSV files", "*.csv")])
    if ruta:
        puntos = leer_puntos_csv(ruta)
        hull = convex_hull(puntos)
        dibujar(puntos, hull, ax, canvas)


def main():
    root = tk.Tk()
    root.title("Convex Hull GUI")

    fig, ax = plt.subplots(figsize=(6, 5))
    canvas = FigureCanvasTkAgg(fig, master=root)
    
    btn = tk.Button(root, text="Seleccionar Archivo CSV", command=lambda: seleccionar_archivo(ax, canvas))
    btn.pack(pady=10)
    
    canvas.get_tk_widget().pack()

    root.mainloop()


if __name__ == "__main__":
    main()
