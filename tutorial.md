# Ambiente de Desenvolvimento

## Iniciando o Projeto

[Link para o projeto no GitHub](https://github.com/lvgl/lv_port_pc_vscode)

```bash
git clone --recursive https://github.com/lvgl/lv_port_pc_vscode
```

## Configurando o Projeto

No projeto, mude a configuração do build para suportar a arquitetura da IHM:

- No arquivo `CMakeList.txt`, mude para:

```c
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -march=x86-64 -mtune=generic")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -march=x86-64 -mtune=generic")
```

## Compilar o Projeto

```bash
mkdir build
cd build
cmake -G Ninja ..
ninja
```

# IHM

## Instalar o Ubuntu

- Baixar a ISO e instalar.

## Configurar a Placa de Rede

### Mandar o Arquivo `00-installer` para o Ubuntu

- Copiar o arquivo para o pendrive, inserir na IHM e montar o pendrive:

```bash
lsblk
sudo mkdir /mnt/pendrive
sudo mount /dev/sdb1 /mnt/pendrive
```

- Copiar o arquivo para o Ubuntu:

```bash
sudo cp /mnt/pendrive/00-installer-config.yaml /etc/netplan/
sudo netplan apply
```

## Instalar as Bibliotecas

```bash
sudo apt install build-essential xorg xserver-xorg xinit libgl1-mesa-dev libinput-dev libudev-dev
sudo apt install openbox xorg
sudo apt install libsdl2-2.0-0 libsdl2-dev libgl1-mesa-dev mesa-utils
```

## Instalar o Driver de Touch na IHM

- Baixar o driver do TouchKit:
[Link do driver](https://www.eeti.com/drivers_Linux.html)

- Descompactar, entrar na pasta e executar o arquivo `.sh`:

```bash
sudo ./setup.sh
```

- Instalar o `evtest` para verificar se o touch está funcionando:

```bash
sudo apt install evtest
sudo evtest
```

## Configurar Mapeamento de Touch

- Instalar:

```bash
sudo apt install xinput-calibrator
```

- Configurar:

```bash
startx
```

- Apertar `CTRL + Alt + F2`:

```bash
export DISPLAY=:0
xinput_calibrator
```

## Passar o Binário e Executar

- Copiar o arquivo para o pendrive e montar o pendrive:

```bash
lsblk
sudo mkdir /mnt/pendrive
sudo mount /dev/sdb1 /mnt/pendrive
```

- Copiar o arquivo para o Ubuntu:

```bash
sudo mkdir lvgl
sudo cp /mnt/pendrive/bin /lvgl
```

- Executar:

```bash
chmod +x ./bin/main
startx ./bin/main
```

# Depois de Executado

- Fechar todas as abas, apertar `F1` ao `F6` para abrir nova aba:

```bash
sudo pkill X
```
