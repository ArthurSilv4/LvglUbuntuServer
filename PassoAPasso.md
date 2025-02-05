



## Configurar o ambiente de desenvolvimeno

### Instale o Wsl

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

**Instale as blibiotecas**

No terminal do Ubuntu, instale os pacotes necessários para compilar o projeto:

```bash
sudo apt install -y build-essential cmake ninja-build git
```

---

### Configure o VSCode com WSL

Abra o VSCode e instale a extensão do WSL:

![WSL Extension](/img/wsl.png)

Feche e abre o vscode para aplicar a extensão.

---

### Inicie um Projeto

**Faça um clone do repositório**

Pesquise por "Ubuntu" na barra de pesquisa do Windows e inicie.

No terminal digite o seguinte codigo para criar uma pasta:
```bash
mkdir repo
cd repo
```

Agora faça o clone do repositório:
```bash
git clone --recursive https://github.com/lvgl/lv_port_pc_vscode
```

**Abra o projeto no vscode**

Instale o wget escrevendo o seguinte codigo no terminal:
```bash
sudo apt-get install wget
```

Escreva o seguinte codigo para entrar no projeto:
```bash
cd lv_port_pc_vscode
```

Abra o projeto no vscode escrevendo o seguinte codigo no terminal:
```bash
code .
```

**Configure o projeto**

No vscode abra as pastas `lvgl` e `freeRtos` e **verifique se elas estão vazias**.

Se estiverem vazias execute o seguinte comando:
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

No vscode aperte `CTRL + SHIFT + "`

No terminal do vscode digite o seguinte comando para fazer o build do programa:

```bash
mkdir build
cd build
cmake -G Ninja ..
ninja
```

---

###




