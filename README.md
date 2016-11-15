Cifrado Asimétrico y Firma Digital
========================================================

# Aclaratoria Noviembre 2016

El siguiente laboratorio fue realizado en la Escuela de Computación de la Universidad Central de Venezuela en el año 2014 como parte de mis estudios de maestría.

A petición del profesor Wilmer Pereira, aclaro que el mismo responde a unas preguntas específicas de un laboratorio de aquel año y **NO** debe ser usado en períodos actuales de su curso.

Más aún, los contenidos útiles para realizar laboratorio pueden ser encontrados en manuales oficiales de cada una de las herramientas. 

# Contexto

Para el desarrollo de este laboratorio usé dos máquinas del laboratorio de redes móviles e inalámbricas. **ICARO**

### Máquina A
  * **Dirección IP:** 190.169.74.238
  * **Usuario de Clave:** Fernando Crema
  * **Correo de la clave:** fernando.crema@ciens.ucv.ve
  * **Passphrase:** Máquina B

### Máquina B
  * **Dirección IP:** 190.169.74.237
  * **Usuario de Clave:** Wilmer Pereira
  * **Correo de la clave:** wilmer.pereira@ciens.ucv.ve
  * **Passphrase:** Máquina A

# Desarrollo

### Genere un par de claves (pública y privada) con gpg. 
  * **Cómo es el comando?**
  
  ```
  gpg –-gen-key
  ```
  * **De que sirve el passphrase?**
  
  Permite tener seguridad adicional sobre la clave que hemos creado. De tal manera que para modificarla necesitamos utilizar el passphrase.
  
  * **Por qué es necesaria la fecha de vencimiento?**
  
  La fecha de vencimiento es necesaria para obligar al usuario a renovar las claves. Un posible uso sería evitar ataques que permitan deducir la clave en el tiempo (ataques de fuerza bruta). El hecho de que las claves expiren disminuye la efectividad de estos métodos.

  * **Adicional**

  ```
  Comando: 
    gpg –-gen-key
  
  Salida: 
    trgpg: /home/icaro/.gnupg/trustdb.gpg: trustdb created
    gpg: key 5D7B866D marked as ultimately trusted
    public and secret key created and signed.
    
    gpg: checking the trustdb
    gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
    gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
    gpg: next trustdb check due at 2014-06-10
    pub   2048R/5D7B866D 2014-06-09 [expires: 2014-06-10]
          Key fingerprint = F5B6 9E5F C4B4 9051 6653  0C15 5306 11E2 5D7B 866D
    uid  Fernando Crema (Maquina A) <fernando.crema@ciens.ucv.ve>
    sub  2048R/F6C46CE0 2014-06-09 [expires: 2014-06-10]
  
  ```

### Para intercambiar con su compañero mensajes seguros debe antes enviar y agregar lo necesario a su keyring público. 
  * **¿Cuáles son los comandos para configurar adecuadamente el  keyring público en los roles de emisor y receptor?**
  
  1. Primero exportamos las claves públicas de las máquinas A y B 
  
    En Máquina A:
    ```
    gpg –output fernando_crema.gpg –export fernando.crema@ciens.ucv.ve 
    ```  
    En Máquina B:
    
    ```
    gpg –output wilmer_pereira.gpg –export wilmer.pereira@ciens.ucv.ve  
    ```
  1. Desde la Máquina A hacemos una conexión sftp para llevar la clave pública de A a la de B y obtener la de B y colocarla en A.
  
    En Máquina A:
    ```
    sftp icaro@190.169.74.237
    sftp> put fernando_crema.gpg
    sftp> get wilmer_pereira.gpg  
    ```
  1. Ahora tenemos las claves públicas donde queremos. Necesitamos importarlas en cada máquina.
  
    En Máquina A importamos la clave de B.
    ```
    Comando: 
      gpg --import wilmer_pereira.gpg
    Salida:
      gpg: key BBAF3500: public key "Wilmer Pereira (Maquina B) <wilmer  .pereira@ciens.ucv.ve>" imported
      gpg: Total number processed: 1
      gpg: imported: 1  (RSA: 1)
    ```
    
    En Máquina B importamos la clave de A.
    ```
    Comando:
      gpg --import fernando_crema.gpg
    Salida:
      gpg: key 5D7B866D: public key "Fernando Crema (Maquina A) <fernando.crema@ciens.ucv.ve>" imported
      gpg: Total number processed: 1
      gpg: imported: 1  (RSA: 1)
    ```
    En este momento ambas Máquinas tienen las claves públicas de cada una en el keyring de cada una.
    
  * **¿Cómo es posible extraer la clave pública descomprimida y poder visualizarla en claro?**
  
    En Máquina A:
  
    ```
      gpg --armor --export fernando.crema@ciens.ucv.ve
    ```
    
    Si quisiéramos guardarlo en un archivo, hacemos:
    ```
      gpg --armor --export fernando.crema@ciens.ucv.ve > archivo.key
    ```
    
    Salida en Máquina A:
    ```
      -----BEGIN PGP PUBLIC KEY BLOCK-----
      Version: GnuPG v1.4.11 (GNU/Linux)
      
      mQENBFOV94sBCADHY7GidNATkYrwatycRQuuXQOGLKSVukiLdwetzXaY0klRxVql
      BHYU2WcPB5LgrMRp4R+0vSRsnid3eWljfzwDVPAL/GH1ZxwGJllYPotR2VRMR6p9
      5y69FShkcDNb1u9ClUbfvRorlADuI+zecnAxjtAYXbDlMVNxs0pSac60oPR6HlaG
      5gNbt5K1XF+oX5z9UGg1XHCTrQkQPc55sHasecv1Sw2gDiHQSWrRwjXZXQ5uPIgg
      DX+zb6mTzR/xreqRFT4Z5KkIomkIx/kuer0JZETBh0lFvLUOUbyelVpn7g6aYHO1
      dv6+tpYbHMY2mXte4RSfrMClifUPZCoFvdK/ABEBAAG0OEZlcm5hbmRvIENyZW1h
      IChNYXF1aW5hIEEpIDxmZXJuYW5kby5jcmVtYUBjaWVucy51Y3YudmU+iQE+BBMB
      AgAoBQJTlfeLAhsDBQkAAVGABgsJCAcDAgYVCAIJCgsEFgIDAQIeAQIXgAAKCRBT
      BhHiXXuGbfm7CACrdabttbhUH4qO09Gt6xPNaTrjTGAPk23DVPkUGmrB4FiqZ8TQ
      /KOkQQp0wCsHjI3e7F2K9/pqffjcYwGjJIqeW7uC6tTiN0t1zeNWq6uaBOsxaqvC
      V+mt/0aWuMHbvRJ5hbBPa5WAFuJarJj+Pv1O3JCu0KbPrD/wtAM6AS3lMwPGDmLx
      RaVgU2RrsmXxeD3CaPsKxl9QycCXYqe4JbAiTxNVQBF6qdZC7Lw29OWbudacH9pT
      4BD1d4LwRxM9eGY5CePFzb9f/Rm1+dZpuew+sZb5y8HjV3TDMdfCDFygfX966yy4
      ce1UV3nAaxx8JHUjpa/E+Bz3i/oVU5cZ8uweuQENBFOV94sBCADecOtnMt9VGUW/
      w4XooEaV9D0/SJBDtCSGTQxHU5f/cYNzut7sQmVk37tHY+lur9kh6rWntMlCspB7
      Gaa4Q+dWIGQ5cj/0gxpACBIEJnyQtNlwSCo+Xii9Foi7X8T1b1px3WLwnti8+lun
      5uQbXnApgBN9Rusd54yMbC3AHcUTiTz7QVeSAbwwLfD+izJu5/3wWcvTGIyu0p6R
      cmnNPM/4tFwwIMnlSkawO8/MZZCfJp9MuZtjQzXlBv0GDoSFbIxa1Kl0J4y0d/gA
      SRSCXgpt+Gg9OtWXe0DfIq/QOi+8mf0n1ZYnT62yqjz0AvDVTD2VEXp7omRi5/b1
      bivCFetnABEBAAGJASUEGAECAA8FAlOV94sCGwwFCQABUYAACgkQUwYR4l17hm3v
      Lgf/cSa/J23N5P5YUgF+SuzAhYMlsmvjq8MNCCEjekzbchxtH8zBTLY0gzmjmShe
      7Fxr/51qn2zhhxkqiEEZlgWf7GQ5SYVXyJwvvEIKirppU0MyFmzANLy/P4YSVNsq
      dyjl5HF6bDcKLg5lCzrtubsRtJ3U8ISxeqdnj4msQ2346IHZYQtasW2jnf5O8SK3
      6/FhQWsQER9jCUCThcaxejLumCfRYzWUVNkCmhyqlriawTwas6yrKD90GOwVnYQo
      JlsEf6n8LSjV5XBGdYahcgykWQE7yc2R5p/CouPkAQQzEFtLbQxgMiYKxR2lZCoR
      MjUF+aJtDTAVYihHlDBioqPyNA==
      =L3R5
      -----END PGP PUBLIC KEY BLOCK-----
    ```
    
### ¿Cómo puede ver las claves guardadas tanto en su keyrings privado como el público?
  * Para ver el keyring privado
    
    ```
      gpg --list-secret-keys
    ```
  
  * Para ver el keyring público
   
    ```
      gpg --list-keys
    ```

### Cifre un archivo de texto con la clave adecuada usando un algoritmo de clave pública para enviar a su compañero. 
  
* **Comando para cifrar**
 
    En Máquina A:
    
    ```
    touch prueba1.txt
    gedit prueba1.txt & (se añade texto a prueba1.txt: Mensaje de prueba hacia Maquina B desde Maquina A)
    gpg --output prueba.gpg --encrypt --recipient wilmer.pereira@ciens.ucv.ve prueba1.txt
    ```
    Se cifra prueba1.txt con la clave pública de wilmer.pereira@ciens.ucv.ve (Máquina B) en prueba.gpg usando un algoritmo de clave pública.
  
* **Comando para Descifrar**

    En Máquina B
    
    ```
    gpg --decrypt prueba.gpg
    ```
    
    Cuando se decripta pide el passphrase de Máquina B al usar la privada de B.
    
    ```
    You need a passphrase to unlock the secret key for
    user: "Wilmer Pereira (Maquina B) <wilmer.pereira@ciens.ucv.ve>"
    2048-bit RSA key, ID AB8CF5B6, created 2014-06-09 (main key ID BBAF3500)
    
    gpg: encrypted with 2048-bit RSA key, ID AB8CF5B6, created 2014-06-09
          "Wilmer Pereira (Maquina B) <wilmer.pereira@ciens.ucv.ve>"
          
    Mensaje de prueba hacia Maquina B desde Maquina A
    ```
    
### Fírme el archivo de texto antes creado usando gpg. Debe enviar a su compañero y a su vez debe verificar el archivo formado que él le envíe.

 * **¿Qué comandos utilizó para firmar y para verificar lo firmado?**
  
  En máquina A para firmar: (Importante: sign comprime el archivo)
 
  ```
  gpg --output prueba1.sig --sign prueba1.txt
  ```
  
  Tenemos dos opciones para verificar la firma
  
  * Para verificar Y extraer (En Máquina B):
   
    ```
    gpg --output prueba --decrypt prueba1.sig
    ```
    
  * Solo para verificar:
  
    
    ```
    gpg --verify doc.sig
    ```
    
### Cada clave pública puede firmarse y esto es conocido como Web Trust.  Verifique las firmas que tienen las claves públicas que están en su keyring público. ¿Qué observa?

Se observan las claves públicas de los usuarios con las respectivas firmas que tiene asociada la misma de acuerdo a quiénes firmaron la clave. Esta experimentación se hizo con las dos máquinas.


### Indique el comando para firmar una de las claves públicas que están en su keyring público

  * Si quisiéramos firmar la clave de A en la Máquina B
    
    ```
    gpg --edit-key fernando.crema@ciens.ucv.ve
    ```
  * Para la clave fernando.crema preguntamos el fingerprint de la misma, validamos con sign y verificamos son check.
  
    ```
    fpr (Se debe preguntar el fingerprint)
    sign (valido yo la clave)
    check (verifico)
    ```

### ¿Cómo cambiar la confianza del propietario de una firma?

Se usó el siguiente comando:
```
gpg --edit-key nombre_usuario
```

Resultando un conjunto de opciones para determinar el trust colocando el comando ``` trust ```

Esto es:

```
Command> trust
pub  1024D/8B927C8A  expires: never      trust: q/f
sub  1024g/C19EA233  expires: never 

Please decide how far you trust this user to correctly
verify other users' keys (by looking at passports,
checking fingerprints from different sources...)?

 1 = Don't know
 2 = I do NOT trust
 3 = I trust marginally
 4 = I trust fully
 s = please show me more information
 m = back to the main menu
  ```
### Actividad Extra: Cifrado Simétrico
  ```
gpg --output simetrico.gpg --symmetric prueba_simetrico.txt
  ```
  
En este caso, al cifrar simétrico te pide el Passphrase para usarla como clave privada de simetrico.gpg. Luego en la máquina B ingresas el Passphrase que ingresaste antes.
