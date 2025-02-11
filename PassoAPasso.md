## Configurar o ambiente de desenvolvimento

### Instale o WSL

**Abra o prompt de comando (CMD)**

Pressione `Windows + R`, digite `cmd` e pressione `Enter`.

**Execute o comando de instalação**

No terminal, digite:

```bash
wsl --install
```

Isso instalará o WSL 2 e o Ubuntu automaticamente.

Após isso, reinicie o PC.

**Configure o Ubuntu**

Após a instalação, o Ubuntu será iniciado automaticamente.

Configure um nome de usuário e senha conforme solicitado.

**Atualize o sistema**

Após o primeiro login no Ubuntu:

Caso esteja conectado a uma VPN, desconecte-se e execute o seguinte código:

```bash
sudo apt update
```

```bash
sudo apt upgrade
```

Isso garantirá que todos os pacotes estejam atualizados.

**Instale as bibliotecas**

No terminal do Ubuntu, instale os pacotes necessários para compilar o projeto:

```bash
sudo apt install -y build-essential cmake ninja-build git libsdl2-dev
```

---

### Configure o VSCode com WSL

Baixe o Vscode: [Link de download](https://code.visualstudio.com/)

Abra o VSCode e instale a extensão do WSL:

![WSL Extension](/img/wsl.png)

Feche e abra o VSCode para aplicar a extensão.

Verifique se a extensão foi instalada corretamente:

![WSL Extension2](/img/wslExtensao.png)

---

### Inicie um Projeto

**Faça um clone do repositório**

Pesquise por "Ubuntu" na barra de pesquisa do Windows e inicie.

No terminal, digite o seguinte código para criar uma pasta:

```bash
mkdir repo
cd repo
```

Agora faça o clone do repositório:

```bash
git clone --recursive https://github.com/lvgl/lv_port_pc_vscode
```

**Abra o projeto no VSCode**

Instale o wget escrevendo o seguinte código no terminal:

```bash
sudo apt-get install wget
```

Escreva o seguinte código para entrar no projeto:

```bash
cd lv_port_pc_vscode
```

Abra o projeto no VSCode escrevendo o seguinte código no terminal:

```bash
code .
```

**Configure o projeto**

No VSCode, abra as pastas `lvgl` e `freeRtos` e **verifique se elas estão vazias**.

Se estiverem vazias, execute o seguinte comando:

```bash
git submodule update --remote --recursive
```

Verifique se as pastas `lvgl` e `freeRtos` estão com seus arquivos agora.

Caso os arquivos não sejam carregados, o problema pode estar na sua conexão com a internet. Tente conectar-se a outra rede e rodar o comando novamente.

**Ajuste a configuração de build**

No projeto, altere a configuração do CMake para suportar a arquitetura da IHM.

Edite o arquivo `CMakeLists.txt` e modifique as seguintes linhas:

Apague o código:

```cmake
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -march=native")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -march=native")
```

Digite o código a seguir no lugar do que foi apagado:

```cmake
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -march=x86-64 -mtune=generic")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -march=x86-64 -mtune=generic")
```

**Ajuste a resolução do projeto**
 
No arquivo `main.c` altera a seguinte linha:

 ```C
hal_init(320, 480);
 ```
Para:

```C
hal_init(1024, 768);
```

**Compile o projeto**

No VSCode, aperte `CTRL + SHIFT + "`.

No terminal do VSCode, digite o seguinte comando para fazer o build do programa:

```bash
mkdir build
cd build
cmake -G Ninja ..
ninja
```

---

## Configurando IHM

### Instale o Ubuntu Server na IHM

**Baixe a ISO**

Acesse o site oficial do Ubuntu Server e baixe a ISO: [Link para download](https://ubuntu.com/download/server).

Crie um Pendrive Bootável com Rufus

1. Conecte um pendrive (mínimo 8GB) no PC.
2. Baixe e instale o Rufus: [Link para download](https://rufus.ie/pt_BR/).
3. Abra o Rufus e selecione:
    - **Dispositivo** → Escolha o pendrive.
    - **Boot Selection** → Clique em "Selecionar" e escolha a ISO do Ubuntu Server.
4. Clique em "Iniciar" e aguarde a criação do pendrive bootável.

**Instale o Ubuntu Server na IHM**

Após terminar de criar o pendrive bootável, conecte o pendrive na IHM desligada e a ligue apertando `Delete` até abrir a tela da BIOS. Após isso, vá até boot e coloque o pendrive em primeiro lugar para que ele inicie o sistema com o Ubuntu Server.

Durante a instalação, certifique-se de que a IHM está com o modem de internet conectado e ligado.

Siga a instalação padrão do Ubuntu Server: [Video Tutorial](https://www.youtube.com/watch?v=AWFLGuvClcM).

### Instale as bibliotecas
Após instalar o Ubuntu Server, execute os seguintes comandos no terminal da IHM:

Verifique se está no caminho correto, por exemplo:
```bash
Usuario@usuario:~$
```
Caso não esteja, você pode apertar o atalho `Ctrl + C` para voltar.

Execute o comando:
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y build-essential xorg xserver-xorg xinit libgl1-mesa-dev libinput-dev libudev-dev
sudo apt install -y openbox xorg libsdl2-2.0-0 libsdl2-dev mesa-utils
```

Obs: A IHM deve estar conectada à internet.

### Configure o Driver de Touch

Baixe o driver do site oficial TouchKit em um PC que não seja a IHM: [Link do driver](https://www.eeti.com/drivers_Linux.html).

Verifique se é a versão mais recente:

![imageDriver](/img/driver.png)


Formate o pendriver novamente e passe o arquivo.

Conecte o pendrive na IHM ligada e execute o seguinte comando:

```bash
lsblk
```

A saída vai ser algo parecido com:

```markdown
| NAME | MAJ:MIN | RM | SIZE | RO | TYPE | MOUNTPOINT |
|------|---------|----|------|----|------|------------|
| sda  | 8:0     | 0  | 100G | 0  | disk |            |
| ├─sda1 | 8:1   | 0  | 512M | 0  | part | /boot      |
| ├─sda2 | 8:2   | 0  | 99.5G| 0  | part | /          |
| sdb  | 8:16    | 1  | 16G  | 0  | disk |            |
| └─sdb1 | 8:17  | 1  | 16G  | 0  | part |            |
```

Monte o pendrive executando o seguinte código:

```bash
sudo chmod 777 /mnt
```

```bash
mkdir /mnt/pendrive
```

```bash
sudo mount /dev/sdb1 /mnt/pendrive
````

Copie o arquivo para a IHM:

**Substitua o `nomeDoArquivo` pelo nome do arquivo do touch baixado no pendrive**

Você pode digitar o início do nome do arquivo e apertar a tecla `Tab` para completar o nome.

```bash
sudo cp /mnt/pendrive/nomeDoArquivo /home/$USER
```

Descompacte o arquivo e execute os seguintes comandos:

```bash
sudo apt install p7zip-full -y
```

```bash
7z x nomeDoArquivo
```

```bash
cd nomeDoArquivo
```

```bash
sudo apt install lightdm
```

```bash
chmod +x ./setup.sh
```

```bash
sudo ./setup.sh
```
Faça a instalação padrão do driver.

![install](/img/install.jpeg)
Escolha a primeira opção e confirme as demais

Depois de instalar o driver teste se o touch está funcionando escrevendo o seguinte comando:

```bash
sudo apt install evtest -y
sudo evtest
```

Após escrever o comando, o sistema vai perguntar qual driver você pretende testar. Identifique qual é o número do driver do touch e digite-o.

Após isso, dê um clique na tela e veja se as coordenadas do touch aparecem.

<!--
**Configure o mapeamento do touch**

Instale a biblioteca:
```bash
sudo apt install xinput-calibrator -y
```

Execute o X11:
```bash
startx
```

Aperte `CTRL + ALT + F2`, faça login e escreva o seguinte comando:
```bash
export DISPLAY=:0
```

```bash
xinput_calibrator
```

Volte para a tela inicial apertando `CTRL + ALT + F1` e calibre o touch clicando nos alvos.

Após a calibração, adicione a configuração no arquivo digitando o seguinte comando:

Volte para `CTRL + ALT + F2` e digite:
```
sudo nano /etc/X11/xorg.conf.d/99-calibration.conf
```

No arquivo, digite a seguinte função:

```bash
Section "InputClass"
    Identifier "calibration"
    MatchProduct "eGalaxTouch Virtual Device for Single"
    Option "MinX" "23293"
    Option "MaxX" "23468"
    Option "MinY" "46570"
    Option "MaxY" "46741"
    Option "SwapXY" "1"
    Option "InvertX" "0"
    Option "InvertY" "0"
EndSection
```

Salve apertando `CTRL + O`, depois aperte `ENTER` e em seguida aperte `CTRL + X`. -->

**Transfira o binário para o LVGL**

No PC de desenvolvimento fora da IHM, passe a pasta `bin` do projeto para o pendrive e monte o pendrive na IHM:

Para passar o arquivo, você pode digitar o seguinte comando no terminal do VSCode:
```bash
explorer.exe .
```
Isso abrirá o explorador de arquivos, nele você pode copiar a pasta `bin` e colar no seu pendrive.

Conecte o pendrive na IHM e escreva os seguintes comandos:

```bash
lsblk
```

A saída vai ser algo parecido com:

```markdown
| NAME | MAJ:MIN | RM | SIZE | RO | TYPE | MOUNTPOINT |
|------|---------|----|------|----|------|------------|
| sda  | 8:0     | 0  | 100G | 0  | disk |            |
| ├─sda1 | 8:1   | 0  | 512M | 0  | part | /boot      |
| ├─sda2 | 8:2   | 0  | 99.5G| 0  | part | /          |
| sdb  | 8:16    | 1  | 16G  | 0  | disk |            |
| └─sdd1 | 8:17  | 1  | 16G  | 0  | part |            |
```

Monte o pendrive executando o seguinte código:
```bash
sudo mount /dev/sdd1 /mnt/pendrive
```

---

Caso sua saída esteja diferente, substitua o comando pela sua saída como no exemplo:

```markdown
| sdb  | 8:16    | 1  | 16G  | 0  | disk |            |
| └─sdc1 | 8:17  | 1  | 16G  | 0  | part |            |
```

```bash
mkdir /mnt/pendrive
sudo mount /dev/sdc1 /mnt/pendrive
```

---

```bash
sudo cp /mnt/pendrive/bin /home/$USER
```

Dê permissão para o binário:
```bash
chmod +x /home/$USER/bin/main
```

```bash
startx /home/$USER/bin/main
```

**Configure o LVGL para iniciar automaticamente**

Crie o arquivo `~/.xinitrc`:

```bash
nano ~/.xinitrc
```

Adicione o seguinte comando:

```plaintext
xinput set-prop "eGalaxTouch Virtual Device for Single" "Coordinate Transformation Matrix" -0.01 -1 1 1 0.1 0 0 0 1
./main
```
Salve apertando `CTRL + O`, depois aperte `ENTER` e em seguida aperte `CTRL + X`.

Dê permissão:

```bash
chmod +x ~/.xinitrc
```

Edite o `.bash_profile` para iniciar automaticamente:

```bash
nano ~/.bash_profile
```

Escreva o comando:

```plaintext
if [[ -z $DISPLAY ]] && [[ $(tty) = /dev/tty1 ]]; then
    startx
fi
```

Salve apertando `CTRL + O`, depois aperte `ENTER` e em seguida aperte `CTRL + X`.

Reinicie a IHM:

```bash
sudo reboot
```

Agora, o LVGL iniciará automaticamente ao ligar! 

### Depois de executar

Para fechar todas as abas, aperte `CTRL + ALT + F1` ao `F6` para abrir uma nova aba e execute:

```bash
sudo pkill X
```
