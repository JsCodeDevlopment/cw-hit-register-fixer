# Guia Completo de Otimização (Regedit e Sistema) — Contract Wars (`CWClient.exe`)

Este documento reúne o **passo a passo completo de ajustes no Registro do Windows (`regedit`) e no sistema**, combinando as otimizações avançadas de rede/CPU recomendadas pela moderação do jogo com os ajustes de precisão de mira e fluidez para a engine **Unity 4.1.5f1 (32-bits / DirectX 9)** do [CWClient.exe](file:///d:/Backup/CWClient/CWClient.exe).

---

## Parte 1: Passo a Passo Completo de Alterações no Regedit

> [!IMPORTANT]
> Antes de começar, abra o Editor do Registro como Administrador: pressione `Win + R`, digite `regedit`, pressione **Enter** e confirme a permissão de Administrador.

### Passo 1: Prioridade de Processamento para Jogos (`Tasks\Games`)

1. No `regedit`, navegue até o seguinte caminho (você pode copiar e colar na barra de endereços do Regedit):
   ```text
   HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile\Tasks\Games
   ```
2. No painel direito, dê dois cliques em cada um dos valores abaixo e ajuste conforme indicado:
   * **`Priority`**: Altere os dados do valor para **`6`** (Base Hexadecimal).
   * **`GPU Priority`**: Certifique-se de que está em **`8`** (Base Hexadecimal).
   * **`Scheduling Category`**: Altere o texto de `Medium` para **`High`**.
   * **`SFIO Priority`**: Altere o texto de `Normal` para **`High`**.

---

### Passo 2: Desativar Estrangulamento de Rede e Reserva de CPU (`SystemProfile`)

1. Ainda no `regedit`, suba dois níveis para a pasta `SystemProfile`:
   ```text
   HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile
   ```
2. No painel direito, localize e altere os dois valores abaixo:
   * **`SystemResponsiveness`**: Dê dois cliques, selecione a base **Hexadecimal** e altere o valor para **`0`** (ou `00000000`).
   * **`NetworkThrottlingIndex`**: Dê dois cliques, selecione a base **Hexadecimal** e altere o valor para **`ffffffff`** (oito letras "f").

---

### Passo 3: Otimização de Pacotes TCP/IP e Hitreg (`Interfaces`)

> [!WARNING]
> **Atenção antes do Passo 3.1:** O procedimento de apagar as pastas dentro de `Interfaces` faz o Windows recriar do zero apenas suas placas de rede ativas via **DHCP (IP Automático)**. Caso você utilize **IP fixo manual** configurado diretamente no Windows, anote seu IP/DNS antes de prosseguir.

#### 3.1. Limpeza de Interfaces Antigas/Fantasmas (Método do Moderador)
1. Navegue até a chave:
   ```text
   HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces
   ```
2. Expanda a pasta `Interfaces`. Você verá diversas subpastas com nomes em código no formato `{GUID}` (ex: `{45184fbc-cbd4-43dc-...}`).
3. **Apague todas as subpastas `{GUID}`** que estão dentro de `Interfaces` (clique com o botão direito em cada subpasta `{GUID}` -> **Excluir**).
4. **Reinicie o computador imediatamente.** Ao ligar novamente, o Windows irá recriar apenas as pastas correspondentes aos adaptadores de rede reais e ativos no momento.

#### 3.2. Criação das Chaves de Resposta Instantânea (Disparo sem Atraso)
1. Após reiniciar o computador, abra o `regedit` novamente e volte para:
   ```text
   HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces
   ```
2. Clique nas poucas pastas `{GUID}` que o Windows recriou até identificar a que possui o valor **`DhcpIPAddress`** (ou **`IPAddress`**) com o endereço IP da sua rede local (geralmente `192.168.x.x`).
3. Dentro dessa pasta `{GUID}` da sua placa de rede ativa, clique com o **botão direito em um espaço vazio** no painel direito -> **Novo** -> **Valor DWORD (32 bits)** e crie os **3 arquivos** abaixo (respeitando letras maiúsculas e minúsculas):
   * **Arquivo 1:**
     * Nome: **`TcpAckFrequency`**
     * Valor: **`1`** (Base Hexadecimal)
   * **Arquivo 2:**
     * Nome: **`TCPNoDelay`**
     * Valor: **`1`** (Base Hexadecimal)
   * **Arquivo 3:**
     * Nome: **`TcpDelAckTicks`**
     * Valor: **`0`** (Base Hexadecimal)

---

### Passo 4: Remoção da Aceleração do Mouse no Registro (`Control Panel\Mouse`)

1. No `regedit`, navegue até:
   ```text
   HKEY_CURRENT_USER\Control Panel\Mouse
   ```
2. Altere os seguintes valores (Texto / `REG_SZ`):
   * **`MouseSpeed`**: Altere para **`0`**
   * **`MouseThreshold1`**: Altere para **`0`**
   * **`MouseThreshold2`**: Altere para **`0`**
   * **`MouseSensitivity`**: Certifique-se de que está em **`10`** (equivale ao 6º traço central do Windows, proporção 1:1).
3. *(Alternativa pelo Painel de Controle)*: Pressione `Win + R`, digite `main.cpl`, vá na aba **Opções do Ponteiro**, desmarque a caixa **"Aprimorar precisão do ponteiro"** e deixe a velocidade exatamente no **6º traço (meio)**.
4. **Reinicie o computador** para que todas as alterações do Registro entrem em vigor.

---

### Passo 5: Ajustes Complementares (Jogo, Mouse e Placa de Vídeo)

Para que as alterações do Regedit entreguem 100% do resultado na prática:
* **Polling Rate do Mouse:** Trave em **500Hz** ou **1000Hz** no software do mouse (valores acima de 1000Hz causam engasgos na engine 32-bits da Unity 4).
* **Modo de Exibição:** Jogue sempre em **Tela Cheia (Fullscreen)** para evitar o atraso de quadros do gerenciador de janelas do Windows (DWM).
* **Propriedades do [CWClient.exe](file:///d:/Backup/CWClient/CWClient.exe):** Na aba **Compatibilidade**, marque **"Desabilitar otimizações de tela inteira"** e **"Executar este programa como administrador"**.
* **AMD Software (Adrenalin):** Ative o **Radeon Anti-Lag** e mantenha **V-Sync** e **Enhanced Sync** desativados.

---

## Parte 2: O Que Cada Passo Faz e Como Afeta a Jogabilidade

Abaixo está a explicação técnica detalhada do funcionamento interno de cada ajuste no Windows e o impacto direto dentro do **Contract Wars**.

### 1. Pasta `Tasks\Games` (`Priority = 6`, `GPU Priority = 8`, `Scheduling Category = High`)
* **O que faz no Windows:** O Windows possui um serviço chamado *MMCSS (Multimedia Class Scheduler Service)* que gerencia a fila de execução do processador. Por padrão, o valor `Priority` vem configurado em `2` e a categoria em `Medium`. Ao subir `Priority` para `6` e `Scheduling Category` / `SFIO Priority` para `High`, você instrui o agendador do kernel do Windows a colocar as *threads* do jogo no topo da fila de execução da CPU e da GPU.
* **Impacto na Jogabilidade:**
  * Como o [CWClient.exe](file:///d:/Backup/CWClient/CWClient.exe) roda na Unity 4.1.5 (que depende fortemente de um único núcleo principal da CPU para calcular física, tiros e renderização), qualquer interrupção de segundo plano causa *micro-stuttering* (pequenas travadinhas quando muitos jogadores atiram ou jogam granadas).
  * Com prioridade `6` e agendamento `High`, o FPS mínimo fica muito mais estável e o tempo de resposta entre o clique do mouse e o quadro desenhado na tela (*frame pacing*) fica constante.

---

### 2. Pasta `SystemProfile` (`SystemResponsiveness = 0` e `NetworkThrottlingIndex = ffffffff`)

#### A) `SystemResponsiveness = 0`
* **O que faz no Windows:** Por padrão, o Windows vem com esse valor em `20` (decimal), o que significa que ele reserva obrigatoriamente **20% da capacidade da CPU** para tarefas de fundo do sistema (atualizações, indexação, navegadores abertos). Ao alterar para `0`, o Windows reduz essa reserva ao piso mínimo de segurança do kernel (10%), liberando **90% do tempo de processamento em tempo real** exclusivamente para o jogo.
* **Impacto na Jogabilidade:** Elimina quedas bruscas de FPS causadas por processos do Windows rodando em segundo plano durante a partida.

#### B) `NetworkThrottlingIndex = ffffffff`
* **O que faz no Windows:** O Windows possui um limitador nativo que restringe o processamento de pacotes de rede a no máximo **10 pacotes por milissegundo** quando aplicativos multimídia/jogos estão rodando (para economizar CPU para a placa de som). Ao definir `ffffffff` (hexadecimal para `4294967295`), você **desativa completamente esse estrangulamento de rede**.
* **Impacto na Jogabilidade:**
  * Em momentos de trocação intensa (vários jogadores atirando ao mesmo tempo), o servidor envia dezenas de pacotes por milissegundo.
  * Sem esse ajuste, o Windows enfileira os pacotes excedentes, gerando picos de ping (*jitter*) e atraso na movimentação dos inimigos. Com `ffffffff`, todos os pacotes da partida são processados instantaneamente pela placa de rede.

---

### 3. Pasta `Tcpip\Parameters\Interfaces` (Limpeza + Tríade TCP)

#### A) Apagar as pastas `{GUID}` e Reiniciar
* **O que faz no Windows:** Remove todas as configurações residuais de placas de rede antigas, VPNs desinstaladas e túneis virtuais que acumulam lixo no registro, forçando o sistema operacional a detectar e registrar novamente apenas a sua placa de rede atual.
* **Impacto na Jogabilidade:** Garante uma pilha de rede limpa e evita o erro clássico de aplicar as otimizações de TCP em um adaptador virtual ou inativo.

#### B) `TcpAckFrequency = 1`, `TCPNoDelay = 1` e `TcpDelAckTicks = 0`
* **O que faz no Windows:**
  * Diferente de jogos modernos que usam apenas UDP, a engine antiga da Unity 4 utiliza conexões **TCP/IP**, que por padrão sofrem com duas travas do Windows pensadas para economizar banda em transferências de arquivos: o **Algoritmo de Nagle** e o **Delayed ACK**.
  * **`TCPNoDelay = 1`**: Desativa o Algoritmo de Nagle, que por padrão retém pacotes pequenos na placa de rede esperando juntar mais dados antes de enviar.
  * **`TcpAckFrequency = 1`**: Obriga o Windows a confirmar imediatamente cada pacote recebido (`1` para `1`), em vez de esperar acumular `2` pacotes para responder ao servidor.
  * **`TcpDelAckTicks = 0`**: Zera o cronômetro interno de *Delayed ACK* do Windows (que por padrão espera até **200ms** antes de enviar confirmações pendentes).
* **Impacto na Jogabilidade:**
  * **É o ajuste mais importante para o *Hitreg* (registro de tiros).**
  * Reduz drasticamente o problema de *"nereg"* (quando você atira no inimigo, vê o sangue sair na sua tela, mas morre sem causar dano porque os pacotes dos seus últimos tiros ficaram retidos alguns milissegundos no buffer do Windows). Seus disparos passam a sair da placa de rede no exato milissegundo em que você clica.

---

### 4. Pasta `Control Panel\Mouse` (`MouseSpeed = 0`, `MouseThreshold1/2 = 0`)
* **O que faz no Windows:** Desativa matematicamente a curva de aceleração de ponteiro do Windows.
* **Impacto na Jogabilidade:**
  * O [CWClient.exe](file:///d:/Backup/CWClient/CWClient.exe) não possui entrada bruta de mouse (*Raw Input*) moderna e herda a movimentação direto do Windows.
  * Com a aceleração desligada (`0`), a distância que você move o mouse no mousepad corresponde **sempre ao mesmo ângulo de giro dentro do jogo**, independentemente da velocidade com que você puxa o mouse (*flick shot*), tornando sua memória muscular 100% previsível.

---

### Resumo Rápido dos Valores no Regedit

| Caminho no Regedit | Nome do Valor | Tipo | Valor Recomendado | Efeito Principal no Jogo |
| :--- | :--- | :--- | :--- | :--- |
| `...\Multimedia\SystemProfile\Tasks\Games` | `Priority` | `REG_DWORD` | `6` | Prioridade alta de CPU para o jogo |
| `...\Multimedia\SystemProfile\Tasks\Games` | `GPU Priority` | `REG_DWORD` | `8` | Prioridade máxima de vídeo (GPU) |
| `...\Multimedia\SystemProfile\Tasks\Games` | `Scheduling Category` | `REG_SZ` | `High` | Fila de agendamento rápida no kernel |
| `...\Multimedia\SystemProfile\Tasks\Games` | `SFIO Priority` | `REG_SZ` | `High` | Prioridade alta de leitura/entrada (I/O) |
| `...\Multimedia\SystemProfile` | `SystemResponsiveness` | `REG_DWORD` | `0` | Dedica até 90% da CPU em tempo real ao jogo |
| `...\Multimedia\SystemProfile` | `NetworkThrottlingIndex` | `REG_DWORD` | `ffffffff` | Remove o limite de pacotes de rede por ms |
| `...\Tcpip\Parameters\Interfaces\{GUID}` | `TcpAckFrequency` | `REG_DWORD` | `1` | Confirma cada pacote recebido sem esperar |
| `...\Tcpip\Parameters\Interfaces\{GUID}` | `TCPNoDelay` | `REG_DWORD` | `1` | Desativa o Algoritmo de Nagle (envio imediato) |
| `...\Tcpip\Parameters\Interfaces\{GUID}` | `TcpDelAckTicks` | `REG_DWORD` | `0` | Zera o atraso de confirmação (Delayed ACK) |
| `HKCU\Control Panel\Mouse` | `MouseSpeed` | `REG_SZ` | `0` | Remove a aceleração do mouse (mira 1:1) |
