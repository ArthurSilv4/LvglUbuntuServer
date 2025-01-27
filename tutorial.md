# Iniciando o projeto

[Link projeto github](https://github.com/lvgl/lv_port_pc_vscode)



    ```bash
    git clone --recursive https://github.com/lvgl/lv_port_pc_vscode
    ```

# Configurando o projeto
no projeto mude a configuração do build para suportar a arquitetura da ihm

- no arquivo CMakeList.txt mude para 

    ```c
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -march=atom -mtune=generic")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -march=atom -mtune=generic")
    ```

# Compilar o projeto

    ```bash
    mkdir build
    cd build
    cmake -G Ninja ..

    ```

# Instalar o ubuntu
- baixar a iso e instalar


# Configurar a placa de rede:

### Mandar o arquivo 00-installer para o ubuntu

- copiar o arquivo para o pendrive

- montar o pendriver

    ```bash
    lsblk

    sudo mkdir /mnt/pendrive

    sudo mount /dev/sdb1 /mnt/pendrive
    ```

- copiar o arquivo para o ubuntu
   
   ```bash
   sudo cp /mnt/pendrive/00-installer-config.yaml /etc/netplan/

   sudo netplan apply
   ```

# Instalar as blibiotecas

    
    sudo apt update
    sudo apt install libsdl2-2.0-0 libsdl2-dev
    sudo apt install mesa-utils libgl1-mesa-dev xvfb




----
    sudo apt install openbox xorg
    sudo apt install libsdl2-2.0-0 libsdl2-dev libgl1-mesa-dev mesa-utils
    startx ./bin/main
----




# Instalar o driver de touch

- baixar o driver do touchkit
- descompactar e entrar na pasta

    ```bash
    sudo ./setup.sh
    ```

# Passar o bin e executar

- copiar o arquivo para o pendrive

- montar o pendriver

    ```bash
    lsblk

    sudo mkdir /mnt/pendrive

    sudo mount /dev/sdb1 /mnt/pendrive
    ```
- copie o arquivo para o ubuntu

    ```bash
    sudo mkdir lvgl

    sudo cp /mnt/pendrive/bin /lvgl
    ```

- executar

    ```bash
    startx ./bin/main
    ```



# Depois de executado
- fechar todas abas

    ```bash
    sudo pkill X
    ```