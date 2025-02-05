# Sumário
1. [Ambiente de Desenvolvimento](#ambiente-de-desenvolvimento)
    1. [Configurando o Ambiente WSL](#configurando-o-ambiente-wsl)
        1. [Instalar o WSL](#instalar-o-wsl)
        2. [Instalar Dependências](#instalar-dependências)
        3. [Configurar o VSCode com WSL](#configurar-o-vscode-com-wsl)
    2. [Iniciando o Projeto](#iniciando-o-projeto)
        1. [Clonar o Repositório](#clonar-o-repositório)
    3. [Configurando o Projeto](#configurando-o-projeto)
        1. [Verificar Submódulos](#verificar-submódulos)
        2. [Ajustar Configurações do Build](#ajustar-configurações-do-build)
    4. [Compilar o Projeto](#compilar-o-projeto)
2. [IHM](#ihm)
    1. [Instalar o Ubuntu Server na IHM](#instalar-o-ubuntu-server-na-ihm)
        1. [Baixar a ISO](#baixar-a-iso)
        2. [Criar um Pendrive Bootável com Rufus](#criar-um-pendrive-bootável-com-rufus)
        3. [Instalar o Ubuntu Server](#instalar-o-ubuntu-server)
    2. [Possíveis Erros](#possíveis-erros)
    3. [Instalar Pacotes Necessários](#instalar-pacotes-necessários)
    4. [Configurar o Touchscreen](#configurar-o-touchscreen)
        1. [Instalar o Driver do Touch](#instalar-o-driver-do-touch)
        2. [Testar o Touch](#testar-o-touch)
        3. [Configurar o Mapeamento do Touch](#configurar-o-mapeamento-do-touch)
    5. [Transferir o Binário do LVGL](#transferir-o-binário-do-lvgl)
    6. [Configurar o LVGL para Iniciar Automaticamente](#configurar-o-lvgl-para-iniciar-automaticamente)
3. [Depois de Executado](#depois-de-executado)

---

## Ambiente de Desenvolvimento

### Configurando o Ambiente WSL

📌 Documentação oficial: [Guia WSL](https://docs.microsoft.com/pt-br/windows/wsl/)

#### Instalar o WSL

**Abrir o Prompt de Comando (CMD)**

Pressione `Windows + R`, digite `cmd` e pressione `Enter`.

**Executar o comando de instalação**

No terminal, digite:

```powershell
wsl --install
```

Isso instalará o WSL 2 e o Ubuntu automaticamente. Após isso, reinicie o PC.

**Configurar o Ubuntu**

Após a instalação, o Ubuntu será iniciado automaticamente. Configure um nome de usuário e senha conforme solicitado.

**Atualizar o sistema**

Após o primeiro login no Ubuntu, execute:

```bash
sudo apt update && sudo apt upgrade -y
```

Isso garantirá que todos os pacotes estejam atualizados.

#### Instalar Dependências

No terminal do Ubuntu, instale os pacotes necessários para compilar o projeto:

```bash
sudo apt install -y build-essential cmake ninja-build git
```

- `build-essential` → Ferramentas básicas de compilação
- `cmake` → Gerenciador de build
- `ninja-build` → Compilador rápido
- `git` → Para clonar o repositório

Para verificar se o Ninja foi instalado corretamente, execute:

```bash
ninja --version
```

Se aparecer um número de versão, significa que está funcionando.

#### Configurar o VSCode com WSL

Abra o VSCode e instale a extensão do WSL:

![WSL Extension](/img/wsl.png)

Reinicie o VSCode para aplicar as mudanças.

### Iniciando o Projeto

#### Clonar o Repositório

Pesquise por Ubuntu na barra de pesquisa do windows e inicie.

Crie uma pasta dentro do Ubuntu e faça o clone do repositório:

```bash
mkdir repo
cd repo
```

Clone o repositório do projeto no GitHub: [Repo oficial](https://github.com/lvgl/lv_port_pc_vscode)

```bash
git clone --recursive https://github.com/lvgl/lv_port_pc_vscode
```

O parâmetro `--recursive` garante que todos os submódulos do projeto também sejam baixados.

Instale o wget
```bash 
sudo apt-get install wget
```

Entre no projeto
```bash
cd lv_port_pc_vscode
```

```bash
code .
```

### Configurando o Projeto

#### Verificar Submódulos

Abra as pastas `lvgl` e `freeRtos` e verifique se elas estão vazias. Se estiverem vazias, apague a pasta do projeto e execute o seguinte comando no terminal:

```bash
sudo apt update
sudo apt install git

git submodule update --remote --recursive

git submodule update --remote lvgl
git submodule update --remote freertos
```

Ou simplesmente copie e cole as pastas `lvgl` e `freertos` de um projeto existente para o seu.

#### Ajustar Configurações do Build

No projeto, altere a configuração do CMake para suportar a arquitetura da IHM.

Edite o arquivo `CMakeLists.txt` e modifique as seguintes linhas:

```cmake
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -march=x86-64 -mtune=generic")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -march=x86-64 -mtune=generic")
```

Isso garantirá que o código seja otimizado para arquitetura x86-64 e compatível com processadores modernos.

### Compilar o Projeto

Agora no terminal, crie um diretório de build, configure o projeto e compile-o usando o Ninja:

```bash
mkdir build
cd build
cmake -G Ninja ..
ninja
```

- `mkdir build` → Cria a pasta de compilação
- `cd build` → Entra na pasta
- `cmake -G Ninja ..` → Configura o build para usar o Ninja
- `ninja` → Compila o código rapidamente

## IHM

### Instalar o Ubuntu Server na IHM

#### Baixar a ISO

Acesse o site oficial do Ubuntu Server e baixe a versão compatível: [Link para download](https://ubuntu.com/download/server)

#### Criar um Pendrive Bootável com Rufus

1. Conecte um pendrive (mínimo 8GB) no PC.
2. Baixe e instale o Rufus.
3. Abra o Rufus e selecione:
    - **Dispositivo** → Escolha o pendrive.
    - **Boot Selection** → Clique em "Selecionar" e escolha a ISO do Ubuntu Server.
4. Clique em "Iniciar" e aguarde a criação do pendrive bootável.

#### Instalar o Ubuntu Server

1. Insira o pendrive na IHM e reinicie.
2. No boot, selecione o pendrive como dispositivo de inicialização.

### Possíveis Erros

<details>
<summary>Erro de rede</summary>

Se ocorrer um erro de rede, siga os passos abaixo para resolver:

1. Copie o arquivo `00-installer-config.yaml` para um pendrive.
2. Insira o pendrive na IHM e monte-o:

```bash
lsblk
sudo mkdir /mnt/pendrive
sudo mount /dev/sdb1 /mnt/pendrive
```

3. Copie o arquivo para o Ubuntu:

```bash
sudo cp /mnt/pendrive/00-installer-config.yaml /etc/netplan/
sudo netplan apply
```

</details>

### Instalar Pacotes Necessários

Atualize e instale os pacotes essenciais:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y build-essential xorg xserver-xorg xinit libgl1-mesa-dev libinput-dev libudev-dev
sudo apt install -y openbox xorg libsdl2-2.0-0 libsdl2-dev mesa-utils
```

✅ **Pacotes importantes:**

- `xorg`, `xserver-xorg` → Servidor gráfico
- `libsdl2-dev` → Biblioteca SDL2 para renderização gráfica

### Configurar o Touchscreen

#### Instalar o Driver do Touch

Baixe o driver do site oficial TouchKit em um PC com internet: [Link do driver](https://www.eeti.com/drivers_Linux.html)

Passe o arquivo para o pendrive e conecte na IHM.

Copie para a IHM e descompacte:

```bash
sudo apt install p7zip-full -y
7z x driver.7z
cd driver
chmod +x ./setup.sh
sudo ./setup.sh
```

#### Testar o Touch

Instale e rode o evtest:

```bash
sudo apt install evtest -y
sudo evtest
```

### Configurar o Mapeamento do Touch

Instale o calibrador e configure os eixos:

```bash
sudo apt install xinput-calibrator -y
export DISPLAY=:0
xinput_calibrator
```

Adicione as configurações em:

```bash
sudo nano /etc/X11/xorg.conf.d/99-calibration.conf
```

E insira:

```plaintext
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

Salve (CTRL + X, Y, Enter).

### Transferir o Binário do LVGL

Monte um pendrive e copie os arquivos:

```bash
sudo mkdir /mnt/usb
sudo mount /dev/sdb1 /mnt/usb
sudo cp /mnt/usb/bin /home/$USER/lvgl/
```

Dê permissão e execute:

```bash
chmod +x /home/$USER/lvgl/bin/main
startx /home/$USER/lvgl/bin/main
```

### Configurar o LVGL para Iniciar Automaticamente

Crie o arquivo `~/.xinitrc`:

```bash
nano ~/.xinitrc
```

Adicione:

```plaintext
xinput set-prop "eGalaxTouch Virtual Device for Single" "Coordinate Transformation Matrix" -0.01 -1 1 1 0.1 0 0 0 1
./main
```

Dê permissão:

```bash
chmod +x ~/.xinitrc
```

Edite o `.bash_profile` para iniciar automaticamente:

```bash
nano ~/.bash_profile
```

Adicione:

```plaintext
if [[ -z $DISPLAY ]] && [[ $(tty) = /dev/tty1 ]]; então
    startx
fi
```

Reinicie a IHM:

```bash
sudo reboot
```

Agora, o LVGL iniciará automaticamente ao ligar! 🚀

## Depois de Executado

Para fechar todas as abas, aperte `CTRL + ALT + F1` ao `F6` para abrir uma nova aba e execute:

```bash
sudo pkill X
```
