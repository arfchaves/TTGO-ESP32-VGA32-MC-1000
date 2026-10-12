# Emulador CCE MC-1000 para TTGO ESP32 VGA32 🕹️📺

Emulador do computador brasileiro **CCE MC-1000** (1985) para a placa
**LilyGO / TTGO ESP32 VGA32 v1.4**, com imagem VGA, teclado PS/2, saída de
áudio, cartão microSD e interface gráfica de gerenciamento.

O objetivo é preservar a experiência e promover o estudo do hardware e do
software brasileiro dos anos 1980. Projeto comunitário **não comercial**,
independente da CCE e dos titulares dos componentes de hardware e software.

> **Versão 0.0.4 — port experimental.** A rotina CAS foi corrigida para seguir
> o protocolo em dois estágios da ROM (cabeçalho em `$C162`, dados em `$C188`).
> O SAVE e LOAD passaram em testes com periféricos simulados. A compatibilidade
> completa ainda requer testes na placa e no monitor Aitek. Não se anunciam funcionalidades não verificadas como
> totalmente compatíveis.

> **Licenciamento:** o código autoral/derivado de fontes MIT incluído aqui está
> sob **MIT**, mas a biblioteca externa **FabGL 1.0.9 é GPLv3**, e a ROM do
> MC-1000 não está licenciada para redistribuição neste repositório.
> Portanto, **não declare o firmware completo como 100% MIT**. Leia
> [Licenciamento e ROM](LICENSING_AND_ROM.md) antes de publicar binários.

## 🚀 Funcionalidades

- **Z80 (3,579545 MHz):** emulação em nível de instrução com contabilização
  de ciclos; núcleo independente (MIT) introduzido na versão 0.0.3.
- **Vídeo MC6847:** texto, semigráficos e modos gráficos MC-1000; VGA por
  FabGL, resolução padrão **640×480 @ 60 Hz**, pixels do MC6847 duplicados
  exatamente 2×2 para preservar a proporção dos caracteres.
- **Teclado PS/2:** mapeamento de contatos do MC-1000, com adaptações para
  teclado EN-US e combinação SHIFT/CTRL, baseado nas referências do jsMC1000.
- **Áudio:** tom, ruído compartilhado e envelopes AY-3-8910 com saída pelo
  conector de áudio do TTGO VGA32; a ROM determina o clique de teclado.
- **OSD:** menu de configuração F1, navegador de fitas F9 e indicador de
  FPS/velocidade do Z80.
- **Cartão microSD:** arquivos de fita `.CAS` no diretório raiz do cartão.
  Carregamento em dois estágios conforme a ROM; SAVE/LOAD já testados
  com sistema de arquivos simulado, porém **pendentes de validação no TTGO**.
- **Modo de RAM expandida:** configuração de emulação com 64 KiB de espaço RAM
  (conforme os bancos mapeados no modelo do projeto).

## 🛠️ Hardware necessário

1. Placa **LilyGO TTGO VGA32 v1.4**, com ESP32-WROVER.
2. Monitor com entrada **VGA** (preferencialmente 640×480/60 Hz; o ajuste automático do Aitek
   ainda necessita confirmação).
3. Teclado **PS/2 real** ou teclado USB que efetivamente suporte protocolo PS/2.
4. Cartão **microSD FAT32**, para arquivos `.CAS` (opcional para a inicialização).
5. Cabo/fone P2 de 3,5 mm para saída do áudio da placa.
6. Cabo USB para alimentação e gravação do ESP32.

## 🔒 ROM do MC-1000: fornecida pelo próprio usuário

**Este repositório NÃO contém a ROM/BASIC original da CCE.** O encerramento da
empresa não comprova ingresso da ROM em domínio público. Para compilar, você
precisa possuir sua própria cópia legalmente obtida de **16.384 bytes**.

No terminal, a partir da raiz `ttgo-vga32-mc1000/`:

```bash
python tools/prepare_rom.py caminho/para/minha_mc1000.rom
```

Isso cria **somente na sua máquina** `src/mc1000_rom.c`. Esse arquivo está em
`.gitignore` e **não deve ser enviado ao GitHub** nem redistribuído sem
permissão. Sem esse arquivo gerado, a compilação completa não estará pronta.

Consulte [LICENSING_AND_ROM.md](LICENSING_AND_ROM.md) para detalhes e fontes
jurídicas.

## 📂 Como preparar o cartão SD

1. Formate o cartão em **FAT32**.
2. Coloque os arquivos de fita com extensão **`.CAS` diretamente na raiz**.
   A versão atual não exige a pasta `/mc1000`.
3. Com o emulador funcionando, use **F9** para selecionar o arquivo;
   em seguida, no BASIC, execute **`LOAD`** conforme as rotinas da ROM.


**SAVE/LOAD:** ao salvar, o emulador cria primeiro um arquivo `.TMP`, confere
o tamanho e tenta renomeá-lo para `.CAS`. Em falhas, examine o cartão, as
mensagens na tela e o monitor serial a **115200 baud**. Durante LOAD, o
cabeçalho é lido antes da comparação do nome; os dados só são transferidos
na segunda chamada da ROM. Um CAS incompatível/truncado deve ser recusado
sem escrever parcialmente no BASIC.


## 🕹️ Teclado e menus

| Tecla PS/2 | Ação |
| --- | --- |
| `F1` | Abre/fecha o menu principal |
| `F9` | Abre o navegador de arquivos `.CAS` do cartão SD |
| `F10` | Reset do MC-1000 emulado |
| `F12` | Liga/desliga o indicador de FPS e MHz Z80 |
| `ESC` | Fecha menus; fora deles segue mapeamento específico do emulador |
| `Enter` | Return/confirmar seleção |
| `Backspace` | RUBOUT |
| `Ctrl` e `Shift` | Modificadores do teclado do MC-1000 |

Alguns caracteres especiais dependem da disposição **EN-US** do teclado. O
mapeamento é inspirado no `PC_FRIENDLY_MAPPING` do emulador jsMC1000 criado pelo amigo Emerson Costa.

## 💻 Compilação e gravação

**Ambiente de referência dos testes anteriores:**

- Arduino CLI ou Arduino IDE;
- Pacote **Espressif ESP32 2.0.17**;
- Biblioteca externa **FabGL 1.0.9** (**GPL-3.0**);

Depois de criar `src/mc1000_rom.c` com o script de ROM acima, execute a partir
da pasta pai do sketch:

```bash
arduino-cli compile --fqbn esp32:esp32:esp32wrover:PartitionScheme=huge_app ttgo-vga32-mc1000/
```

Use a porta/placa apropriada para gravar o firmware. **Não compartilhe o
firmware compilado publicamente** antes de verificar os direitos de ROM e as
obrigações GPL aplicáveis à FabGL.

## 🧩 Estrutura do projeto

```text
ttgo-vga32-mc1000/
├── ttgo-vga32-mc1000.ino        # Inicialização e tarefas ESP32
├── src/
│   ├── mc1000.cpp/.h           # Máquina, memória, teclado, SD/CAS
│   ├── z80/Z80.cpp/.h          # Novo núcleo Z80, MIT
│   ├── ay38910.cpp/.h          # Novo PSG AY-3-8910, MIT
│   ├── mc6847.cpp/.h           # Vídeo e molduras em pixels
│   ├── mc6847_font_mit.inc     # Tabela de caracteres do jsMC1000 (MIT)
│   ├── osd.cpp/.h              # Menus e avisos
│   └── mc1000_rom.h            # Declaração da ROM gerada localmente
├── tools/prepare_rom.py        # Conversor ROM -> C (sem bytes de ROM)
├── tools/audit_public_release.py # Verificação automatizada de vazamentos no pacote público
├── LICENSE                     # MIT para os componentes aplicáveis
├── licenses/jsMC1000-MIT.txt   # Aviso MIT original do autor da fonte
├── licenses/FabGL-GPLv3-NOTICE.md # Aviso GPL da dependência externa
├── THIRD_PARTY_NOTICES.md      # Origem, créditos e licenças
├── LICENSING_AND_ROM.md        # Limitações da MIT/GPL e política de ROM
└── tests/                     # Testes isolados Z80, PSG, CAS e protocolo da ROM
```

## 🤝 Contribuições

Pull requests e issues são bem-vindos para compatibilidade CAS, testes de
vídeo, temporização do Z80, precisão do AY, documentação e qualidade das
interfaces F1/F9. Não envie ROMs, dumps, CAS de terceiros sem permissão ou
bibliotecas GPL copiados ao diretório do projeto sem revisão da licença.

## 📜 Licenças, créditos e agradecimentos

Agradecimento especial aos autores e projetos cujas implementações,
referências e estudos contribuíram para este port:

- **Jindor — TTGO VGA32 SPC-1000:** base Arduino/FabGL, plataforma TTGO,
  arquivo/cartão SD e arquitetura inicial de menus. Código base MIT,
  copyright 2024 preservado.
- **Kue-Hwan Sihn (ionique):** emulador do SPC-1000 e contribuição histórica
  à evolução da base. O núcleo antigo com restrições foi retirado deste port.
- **Emerson José Silveira da Costa (Ensjo) — jsMC1000:** descrição do teclado
  MC-1000, referência de mapeamento de teclas, documentação de periféricos e
  tabela de caracteres do MC6847. **Fonte MIT reutilizada com atribuição**.
- **Fabrizio Di Vittorio — [FabGL](https://github.com/fdivitto/FabGL):** VGA,
  PS/2 e áudio no ESP32. **Dependência GPL-3.0**, não incluída no repositório.
- **Reinaldo Torres / CoCo Byte Club — [TTGO VGA32 CoCo 2/3](https://github.com/reyco2000/TTGO-VGA32-COCO):** referências sobre MC6847 e geração VGA.
  O código GPL desse emulador **não** integra esta versão.
- **Víctor Iborra, David Crespo e colaboradores — [ESPectrum](https://github.com/EremusOne/ESPectrum):** inspiração visual dos menus e estudos de
  temporização/áudio; código GPL **não incorporado**.
- **[MAME](https://github.com/mamedev/mame):** informações de hardware,
  mapeamento dos bancos de memória, modos do VDG e referência da paleta.
- **Comunidade [CCE MC-1000](https://sites.google.com/site/ccemc1000/p%C3%A1gina-inicial):** documentação histórica, guias de BASIC e formato de fita.
- **Marat Fayzullin:** reconhecemos historicamente suas contribuições aos
  emuladores Z80/AY; seus módulos antigos **não são distribuídos** nesta versão.
- Aos autores de utilitários, pesquisadores e usuários que preservam a memória
  da computação brasileira.

Leia também [PUBLICATION_CHECKLIST.md](PUBLICATION_CHECKLIST.md) e
[LEGAL_REVIEW_STATUS.md](LEGAL_REVIEW_STATUS.md) antes de abrir o repositório.

Leia [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) para especificar o que
foi efetivamente usado de cada projeto. A licença [MIT](LICENSE) alcança os
**componentes de código apropriados**, não a ROM e não a dependência externa
GPLv3. Não há endosso, patrocínio ou filiação aos projetos citados.
