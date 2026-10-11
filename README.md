# Emulador de CCE MC-1000 para ESP32-VGA32 (FabGL) 🕹️📺

Este projeto é um emulador do clássico computador de 8 bits brasileiro **CCE MC-1000**, desenvolvido para rodar na placa **ESP32-VGA32** (LilyGO VGA32). O emulador utiliza a biblioteca **FabGL** para renderização gráfica nativa via VGA, emulação de som e controle do teclado PS/2, trazendo a experiência do micrão dos anos 80 diretamente para monitores modernos.

## 🚀 Funcionalidades

- **Emulação do Processador Z80:** Execução das instruções e ciclos do MC-1000.
- **Vídeo VGA Nativo via FabGL:** Geração de imagem diretamente para monitores VGA com as cores e modos originais do chip MC6847.
- **Suporte a Teclado PS/2:** Mapeamento do teclado original do MC-1000 para teclados PS/2 modernos.
- **Áudio Integrado:** Emulação do chip de som (PSG) utilizando os recursos de áudio da FabGL através da saída de som de 3.5mm da placa.
- **Menu Gráfico Interativo:** Interface na tela para gerenciar o emulador de forma simples e rápida.
- **Suporte a Cartão MicroSD:** Navegação e carregamento de jogos e programas (.cas) diretamente pelo menu na tela.

## 🛠️ Hardware Necessário

Para rodar este projeto, você precisará de:
1. Uma placa **LilyGO ESP32 VGA32 v1.4** 
2. Um monitor com entrada **VGA**.
3. Um teclado **PS/2** nativo (Revise se o seu modelo USB aceita protocolo PS/2 via adaptador).
4. Um cartão **MicroSD** formatado em **FAT32**.
5. Cabo de áudio P2 (3.5mm) ou fones de ouvido para escutar o som dos jogos.

## 📂 Como Preparar o Cartão SD

1. Formate o seu cartão MicroSD em **FAT32**.
2. Crie uma pasta chamada `/mc1000` na raiz do cartão.
3. Coloque os seus arquivos de jogos (no formato de fita suportado, ex: `.cas`) dentro dessa pasta para que o menu gráfico possa localizá-los.

## 🕹️ Controles e Interface do Menu

O teclado PS/2 é gerenciado pela FabGL e está mapeado para o layout do MC-1000 e funções do emulador:

### Teclas de Atalho do Emulador
- `F1` - **Abre o Menu Gráfico** (Navegação de arquivos do SD, configurações e opções do emulador).

### Mapeamento do Teclado Geral

| Teclado PS/2 Moderno | Teclado MC-1000 Original |
|----------------------|--------------------------|
| `ESC`                | RESET / BREAK            |
| `ENTER`              | RETURN                   |
| `BACKSPACE`          | RUBOUT                   |
| `CTRL Esquerdo`      | CTRL                     |

## 💻 Como Compilar e Gravar

### Pré-requisitos
- [Arduino IDE](https://arduino.cc) ou [PlatformIO](https://platformio.org) (Recomendado).
- Pacote de placas ESP32 instalado na sua IDE.
- Biblioteca **FabGL** instalada (disponível no Gerenciador de Bibliotecas do Arduino ou via `platformio.ini`).

### Passo a Passo

1. **Clone o repositório:**
   ```bash
   git clone https://github.com
   ```
2. Abra o projeto na sua IDE de preferência.
3. Certifique-se de que a biblioteca **FabGL** está ativa no seu ambiente de desenvolvimento.
4. Selecione a placa **ESP32 Dev Module** (ou o perfil específico da LilyGO VGA32).
5. Conecte a placa via USB, selecione a porta COM correspondente e clique em **Upload**.

## 🤝 Contribuições

Contribuições são muito bem-vindas! Sinta-se à vontade para abrir uma **Issue** ou enviar um **Pull Request** para:
- Otimizar o core de emulação do Z80.
- Trazer melhorias visuais ou novas funções para o menu gráfico do usuário.
- Corrigir bugs de timing ou de áudio.

## 📜 Licença

Este projeto está sob a licença [MIT](LICENSE). Veja o arquivo para mais detalhes.

## 🙏 Agradecimentos

- Ao Fabrizio Di Vittorio pelo desenvolvimento da incrível biblioteca [FabGL](https://github.com).

