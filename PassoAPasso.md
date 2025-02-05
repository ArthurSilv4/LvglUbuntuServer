
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

Após o primeiro login no Ubuntu, execute:

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
sudo apt install -y build-essential cmake ninja-build git
```

---

### Configure o VSCode com WSL

Abra o VSCode e instale a extensão do WSL:

![WSL Extension](/img/wsl.png)

Feche e abra o VSCode para aplicar a extensão.

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

Caso os arquivos não sejam carregados, o problema pode ser na sua conexão com a internet.

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

Siga a instalação padrão do Ubuntu Server: [Video Tutorial](https://www.youtube.com/watch?v=AWFLGuvClcM).

### Instale as bibliotecas

Após fazer a instalação do Ubuntu Server, escreva os seguintes comandos no terminal da IHM:

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y build-essential xorg xserver-xorg xinit libgl1-mesa-dev libinput-dev libudev-dev
sudo apt install -y openbox xorg libsdl2-2.0-0 libsdl2-dev mesa-utils
```

Obs: A IHM deve estar conectada à internet.

### Configurando Driver de Touch

Baixe o driver do site oficial TouchKit em um PC que não seja a IHM: [Link do driver](https://www.eeti.com/drivers_Linux.html).

Passe o arquivo para um pendrive formatado.

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
mkdir /mnt/pendrive
sudo mount /dev/sdb1 /mnt/pendrive
```

Copie o arquivo para a IHM:
```bash
sudo cp /mnt/pendrive/nomeDoArquivo /home/$USER
```

