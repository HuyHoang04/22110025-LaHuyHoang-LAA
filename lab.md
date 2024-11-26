# Task 1: Public-key based authentication

## Installation OSSHand set up evironment

1. Goto [https://github.com/PowerShell/Win32-OpenSSH/releases](https://github.com/PowerShell/Win32-OpenSSH/releases)
2. Download the OpenSSH-Win64.zip file from the latest release
3. Extract the zip file contents to the folder `C:\Program Files\OpenSSH`

- Open a command prompt as Administrator and use the following command to change to openssh directory
  `cd "C:\Program Files\OpenSSH"`
- Run the following command
  `powershell.exe -ExecutionPolicy Bypass -File install-sshd.ps1`

![1732585226426](images/lab/1732585226426.png)

- Change the startup type to Automatic from Manual and start both the `sshd` and `sshd-agent` services. Since we have set the startup type as automatic, both the services will start automatically upon system startup.

![1732585313830](images/lab/1732585313830.png)

![1732585937942](images/lab/1732585937942.png)

## Setup key pairs

**Open 2 terminal 1 Client 1 as Sever side**

### On client side

Make private_key

```
openssl genpkey -algorithm RSA -out client_private_key.pem
```

Write public Key

```
openssl rsa -pubout -in client_private_key.pem -out client_public_key.pem
```

![1732591930461](images/lab/1732591930461.png)

**On server side**

Make private_key

```
openssl genpkey -algorithm RSA -out server_private_key.pem
```

Write public Key

```
openssl rsa -pubout -in client_private_key.pem -out server_public_key.pem
```

![1732592061521](images/lab/1732592061521.png)

## Send Message from Server to Client

On server side

Make message

```
echo "This is a happy message" > message.txt
```

![1732592219914](images/lab/1732592219914.png)

- Encrypt the challenge message with the client's public key:

  ```
  openssl pkeyutl -encrypt -inkey client_public_key.pem -pubin -in message.txt -out encrypted_message.bin
  ```

  ![1732592300796](images/lab/1732592300796.png)

## Client Decrypts the Message

**Client Side**

- Decrypt the encrypted_challenge.bin file:

```
openssl pkeyutl -decrypt -inkey client_private_key.pem -in encrypted_message.bin -out decrypted_message.txt
```

![1732592449150](images/lab/1732592449150.png)

- View the decrypted content:

![1732592921512](images/lab/1732592921512.png)

## Client Signs the Decrypted Message

On Client:

- Sign the file decrypted_challenge.txt:

```
openssl dgst -sha256 -sign client_private_key.pem -out signed_message.bin decrypted_message.txt
```

![1732593045127](images/lab/1732593045127.png)

## Server Verifies the Signature

On Server:

- Verify the signature:

```
openssl dgst -sha256 -verify client_public_key.pem -signature signed_message.bin decrypted_message.txt
```

![1732593154567](images/lab/1732593154567.png)

Output is: Verified OK then COMPLETE
