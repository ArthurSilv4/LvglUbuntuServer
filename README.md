# Sumário
1. [Ambiente de Desenvolvimento](#ambiente-de-desenvolvimento)
    1. [Iniciando o Projeto](#iniciando-o-projeto)
    2. [Configurando o Projeto](#configurando-o-projeto)
    3. [Compilar o Projeto](#compilar-o-projeto)
2. [IHM](#ihm)
    1. [Instalar o Ubuntu](#instalar-o-ubuntu)
    2. [Possíveis erros](#possiveis-erros)
    3. [Instalar as Bibliotecas](#instalar-as-bibliotecas)
    4. [Instalar o Driver de Touch na IHM](#instalar-o-driver-de-touch-na-ihm)
    5. [Configurar Mapeamento de Touch](#configurar-mapeamento-de-touch)
    6. [Passar o Binário e Executar](#passar-o-binário-e-executar)
    7. [Iniciar o lvgl ao iniciar o sistema](#iniciar-o-lvgl-ao-iniciar-o-sistema)
3. [Depois de Executado](#depois-de-executado)

---

# Ambiente de Desenvolvimento

## Iniciando o Projeto

Clone o repositório do projeto no GitHub:

```bash
git clone --recursive https://github.com/lvgl/lv_port_pc_vscode
```

## Configurando o Projeto

No projeto, mude a configuração do build para suportar a arquitetura da IHM. Edite o arquivo `CMakeList.txt` e altere as seguintes linhas:

```c
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -march=x86-64 -mtune=generic")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -march=x86-64 -mtune=generic")
```

## Compilar o Projeto

Crie um diretório de build, configure e compile o projeto usando Ninja:

```bash
mkdir build
cd build
cmake -G Ninja ..
ninja
```

# IHM

## Instalar o Ubuntu

Baixe a ISO do Ubuntu e instale no dispositivo.

## Possíveis erros

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

## Instalar as Bibliotecas

Instale as bibliotecas necessárias:

```bash
sudo apt install build-essential xorg xserver-xorg xinit libgl1-mesa-dev libinput-dev libudev-dev
sudo apt install openbox xorg
sudo apt install libsdl2-2.0-0 libsdl2-dev mesa-utils
```

## Instalar o Driver de Touch na IHM

1. Baixe o driver do TouchKit: [Link do driver](https://www.eeti.com/drivers_Linux.html)
2. Copie o arquivo para a IHM, descompacte, entre na pasta e execute o arquivo `.sh`:

```bash
sudo apt install p7zip-full -y

cp arquivo /home/branqs
7z x arquivo.7z

cd arquivo
chmod +x ./setup.sh
sudo ./setup.sh
```

3. Instale o `evtest` para verificar se o touch está funcionando:

```bash
sudo apt install evtest
sudo evtest
```

## Configurar Mapeamento de Touch

1. Instale o `xinput-calibrator`:

```bash
sudo apt install xinput-calibrator
```

2. Configure o mapeamento de touch:

```bash
startx ./bin/main
```

3. Aperte `CTRL + Alt + F2` e execute:

```bash
export DISPLAY=:0
xinput_calibrator
```

4. Edite o arquivo de configuração:

```bash
sudo nano /etc/X11/xorg.conf.d/99-calibration.conf
```

Adicione o seguinte conteúdo:

```plaintext
Section "InputClass"
    Identifier "calibration"
    MatchProduct "eGalaxTouch Virtual Device for Single"
    Option "MinX" "23293"
    Option "MaxX" "23468"
    Option "MinY" "46570"
    Option "MaxY" "46741"
    Option "SwapXY" "1" # Use 1 para trocar os eixos, 0 para não trocar
    Option "InvertX" "0" # Use 1 para inverter o eixo X, 0 para manter normal
    Option "InvertY" "0" # Use 1 para inverter o eixo Y, 0 para manter normal
EndSection
```

5. Configure a matriz de transformação:

```bash
xinput set-prop "eGalaxTouch Virtual Device for Single" "Coordinate Transformation Matrix" -0.01 -1 1 1 0.1 0 0 0 1
```

### Matrizes de Transformação

Normal:

```bash
1 0 0
0 1 0
0 0 1
```

90 graus:

```bash
0 -1 1 
1 0 0
0 0 1
```

180 graus (invertido):

```bash
-1 0 1
0 -1 1
0 0 1
```

270 graus (rotacionado):

```bash
0 1 0
-1 0 1
0 0 1
```

## Passar o Binário e Executar

1. Copie o arquivo para o pendrive e monte o pendrive:

```bash
lsblk
sudo mkdir /mnt/pendrive
sudo mount /dev/sdb1 /mnt/pendrive
```

2. Copie o arquivo para o Ubuntu:

```bash
sudo mkdir lvgl
sudo cp /mnt/pendrive/bin /lvgl
```

3. Execute o binário:

```bash
chmod +x ./bin/main
startx ./bin/main
```

## Iniciar o lvgl ao iniciar o sistema

1. Edite o arquivo `~/.xinitrc`:

```bash
nano ~/.xinitrc
```

Adicione os comandos:

```bash
xinput set-prop "eGalaxTouch Virtual Device for Single" "Coordinate Transformation Matrix" -0.01 -1 1 1 0.1 0 0 0 1
./main
```

Salve e saia. Dê permissão de execução:

```bash
chmod +x ~/.xinitrc
```

2. Edite o arquivo `~/.bash_profile`:

```bash
nano ~/.bash_profile
```

Adicione os comandos, salve e saia:

```bash
if [[ -z $DISPLAY ]] && [[ $(tty) = /dev/tty1 ]]; then
    startx 
fi
```

3. Reinicie o sistema e teste com `CTRL + ALT + F2`:

```bash
sudo reboot
```

# Depois de Executado

Para fechar todas as abas, aperte `CTRL + ALT + F1` ao `F6` para abrir uma nova aba e execute:

```bash
sudo pkill X
```
