import cv2
import os

def cargar_imagen(ruta):
    if not os.path.exists(ruta):
        raise FileNotFoundError(f"No se encontró la imagen en: {ruta}")
    img = cv2.imread(ruta)
    if img is None:
        raise ValueError("No se pudo cargar la imagen")
    return img

def guardar_imagen(img, ruta_salida):
    cv2.imwrite(ruta_salida, img)
    print(f"✅ Imagen guardada en: {ruta_salida}")

def efecto_acuarela(img):
    return cv2.stylization(img, sigma_s=60, sigma_r=0.6)

def efecto_cartoon(img):
    gris = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    gris = cv2.medianBlur(gris, 5)

    bordes = cv2.adaptiveThreshold(
        gris, 255,
        cv2.ADAPTIVE_THRESH_MEAN_C,
        cv2.THRESH_BINARY,
        9, 9
    )

    color = cv2.bilateralFilter(img, 9, 300, 300)
    cartoon = cv2.bitwise_and(color, color, mask=bordes)
    return cartoon

def efecto_oleo(img):
    # Simulación de óleo con suavizado fuerte
    return cv2.edgePreservingFilter(img, flags=1, sigma_s=150, sigma_r=0.3)

def aplicar_estilo(ruta_imagen, estilo="acuarela"):
    img = cargar_imagen(ruta_imagen)

    if estilo == "acuarela":
        resultado = efecto_acuarela(img)
    elif estilo == "cartoon":
        resultado = efecto_cartoon(img)
    elif estilo == "oleo":
        resultado = efecto_oleo(img)
    else:
        raise ValueError("Estilo no válido. Usa: acuarela, cartoon o oleo")

    nombre_salida = f"resultado_{estilo}.jpg"
    guardar_imagen(resultado, nombre_salida)

def main():
    print("🎨 Generador de imágenes estilo pintura")
    ruta = input("📷 Nombre de la imagen (ej: flores.jpg): ").strip()
    
    print("Elige estilo:")
    print("1 - Acuarela")
    print("2 - Cartoon")
    print("3 - Óleo")

    opcion = input("👉 Opción: ").strip()

    estilos = {
        "1": "acuarela",
        "2": "cartoon",
        "3": "oleo"
    }

    estilo = estilos.get(opcion)

    if not estilo:
        print("❌ Opción inválida")
        return

    aplicar_estilo(ruta, estilo)

if __name__ == "__main__":
    main()
