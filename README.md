# Validador do OBS Studio para Linux Mint

Aplicativo gráfico para coletar informações de diagnóstico do OBS Studio, do sistema gráfico, dos dispositivos de áudio, dos monitores e do driver NVIDIA em Linux Mint e ambientes baseados em Ubuntu.

A ferramenta foi criada para ajudar a entender problemas de configuração e sintomas como falhas de renderização, quadros atrasados, problemas de áudio e erros registrados pelo OBS. Ela reúne informações que normalmente exigiriam abrir vários arquivos de configuração e executar comandos diferentes no terminal.

> **Versão documentada:** 1.1.0  
> **Interface:** GTK 3  
> **Idioma da interface e dos relatórios:** português brasileiro  

## Conteúdo

- [Objetivo do projeto](#objetivo-do-projeto)
- [Público-alvo](#público-alvo)
- [Principais funcionalidades](#principais-funcionalidades)
- [O que o programa verifica](#o-que-o-programa-verifica)
- [O que o programa não faz](#o-que-o-programa-não-faz)
- [Como funciona a auditoria](#como-funciona-a-auditoria)
- [Requisitos e compatibilidade](#requisitos-e-compatibilidade)
- [Instalação pelo pacote DEB](#instalação-pelo-pacote-deb)
- [Construção do pacote a partir do código-fonte](#construção-do-pacote-a-partir-do-código-fonte)
- [Como utilizar](#como-utilizar)
- [Exportação de relatórios](#exportação-de-relatórios)
- [Como interpretar os resultados](#como-interpretar-os-resultados)
- [Privacidade e segurança](#privacidade-e-segurança)
- [Limitações conhecidas](#limitações-conhecidas)
- [Solução de problemas](#solução-de-problemas)
- [Estrutura do projeto](#estrutura-do-projeto)
- [Contribuições](#contribuições)

## Objetivo do projeto

O Validador do OBS Studio é uma ferramenta de diagnóstico local. Seu objetivo é facilitar a coleta e a organização de informações úteis para analisar uma instalação do OBS em sistemas Linux, especialmente no Linux Mint Cinnamon.

O programa reúne informações sobre:

- instalação e versão do OBS, quando identificáveis pelos gerenciadores de pacotes conhecidos;
- perfis de saída e configurações de gravação salvas;
- codificadores de vídeo e áudio configurados;
- resolução, taxa de quadros e alguns parâmetros de cor;
- faixas de áudio marcadas para gravação;
- diretório de gravação e espaço disponível;
- GPU NVIDIA, versão do driver e métricas disponibilizadas por `nvidia-smi`;
- servidor e dispositivos de áudio disponíveis no sistema;
- monitores conectados e modos de vídeo informados por `xrandr`;
- ocorrências relevantes encontradas no log mais recente do OBS.

A ferramenta gera um relatório que pode ser consultado localmente, salvo em arquivo ou compartilhado para facilitar um diagnóstico técnico. Os resultados são evidências para investigação, não uma garantia de que determinado componente esteja funcionando corretamente.

## Público-alvo

O programa é destinado principalmente a:

- **Criadores de conteúdo** que gravam tutoriais, aulas, demonstrações técnicas, podcasts ou vídeos usando o OBS no Linux.
- **Usuários do Linux Mint Cinnamon** que precisam reunir informações de áudio, vídeo, monitores e GPU sem consultar manualmente diversos arquivos.
- **Administradores de sistemas e profissionais de suporte** que ajudam a diagnosticar problemas de gravação ou de configuração do OBS.
- **Educadores e estudantes de TI** que trabalham com Linux, captura de tela, produção de conteúdo ou suporte a estações de trabalho.
- **Usuários de GPUs NVIDIA** que desejam confirmar o que o sistema reconhece sobre a placa e o driver e verificar se o OBS está configurado para utilizar um codificador NVENC.
- **Mantenedores e contribuidores de software** que precisam de um relatório reproduzível para investigar problemas e comparar configurações.

O usuário não precisa conhecer todos os arquivos internos do OBS nem dominar o terminal para utilizar a interface gráfica. Conhecimento básico do OBS é útil para interpretar os resultados e decidir quais alterações devem ser feitas manualmente.

## Principais funcionalidades

### Interface gráfica integrada ao Cinnamon

- Interface construída com GTK 3.
- Utiliza o tema GTK ativo do sistema, incluindo as opções de tema claro ou escuro configuradas no ambiente.
- Botões para executar a auditoria, exportar TXT, exportar HTML, copiar o relatório, limpar a tela e fechar o aplicativo.
- Janela de relatório com rolagem e mensagens de estado.
- Diálogos gráficos para escolher o destino de exportação.
- Atalho no menu de aplicativos na categoria Multimídia.

A aparência depende do tema GTK e dos ícones instalados e configurados no sistema. O programa não instala nem modifica o tema do Cinnamon.

### Auditoria do OBS Studio

- Procura instalações identificáveis por executável no `PATH`, DPKG/APT, Flatpak e Snap.
- Localiza diretórios de configuração conhecidos do OBS para a sessão do usuário.
- Lê os arquivos de configuração dos perfis encontrados.
- Tenta identificar o perfil e a coleção de cenas selecionados nos arquivos globais do OBS.
- Inspeciona parâmetros registrados em `basic.ini`, `recordEncoder.json` e `streamEncoder.json`, quando presentes.
- Informa quando algum dado não foi encontrado ou não pôde ser interpretado.

A presença de um perfil na lista não significa necessariamente que ele esteja ativo na instância do OBS que está aberta. O programa indica quando não conseguiu confirmar essa relação.

### Verificação de vídeo e gravação

Quando as informações estão disponíveis nos arquivos, o relatório pode incluir:

- modo de saída Simple ou Advanced;
- codificador de vídeo configurado para gravação;
- formato de gravação, como MKV;
- resolução base e resolução de saída;
- FPS ou fração equivalente;
- filtro de redução de escala;
- formato, espaço e faixa de cor;
- opções encontradas nos arquivos de configuração do codificador, como controle de taxa, CQP/CQ, predefinição, perfil e intervalo de keyframes;
- diretório configurado para gravação e estimativa de espaço livre.

O programa pode sinalizar que o perfil aponta para NVENC ou para x264. Isso é uma leitura da configuração salva, não um teste de desempenho ou uma medição contínua do codificador.

### Verificação de áudio

O relatório consulta, quando disponíveis, informações fornecidas pelo servidor de áudio do sistema e apresenta dispositivos de entrada e saída conhecidos pelo PulseAudio/PipeWire através de `pactl`. Se `pactl` não estiver disponível, tenta consultar o estado com `wpctl`.

Também lê dos perfis do OBS parâmetros de áudio como:

- codificador de áudio configurado para gravação;
- taxa global de amostragem;
- quantidade de canais;
- máscara das faixas de gravação, quando identificável.

Esses dados ajudam a investigar a configuração, mas não comprovam que o microfone esteja captando áudio, que a fonte correta esteja selecionada em cada cena ou que não haja ruído, cortes ou dessincronização. Isso exige um teste real de gravação e a audição do arquivo resultante.

### Verificação da GPU NVIDIA e do sistema gráfico

Quando os utilitários estão instalados e a GPU é compatível com eles, o programa consulta:

- modelo da GPU;
- versão do driver;
- temperatura e utilização reportadas;
- memória gráfica utilizada e total;
- resumo gráfico fornecido pelo `inxi`, incluindo driver e informações OpenGL quando disponíveis.

A ferramenta também tenta listar os monitores conectados e seus modos atuais com `xrandr`. Essa parte depende do acesso à sessão gráfica e do suporte fornecido pelo ambiente.

### Análise limitada dos logs do OBS

O programa procura o log mais recente nos diretórios de configuração detectados e seleciona linhas que correspondam a termos relacionados, entre outros, a:

- NVENC e codificação;
- atraso de renderização ou codificação;
- quadros perdidos ou ignorados;
- buffer, underrun e eventos relacionados a áudio;
- falhas de inicialização;
- falhas ao criar fontes;
- mensagens que mencionem áudio, vídeo, captura, gráficos ou codificadores.

Para limitar o tamanho do relatório, são exibidas até 18 das últimas linhas correspondentes encontradas no log mais recente. A pesquisa depende das palavras e padrões implementados no código; ela não representa uma análise completa de todos os eventos possíveis do OBS.

### Exportação e compartilhamento

O relatório pode ser:

- visualizado na própria janela;
- copiado para a área de transferência;
- salvo em **TXT**, para leitura simples, terminal, fóruns ou chamados de suporte;
- salvo em **HTML**, com seções formatadas, cores de status e suporte ao tema claro ou escuro do navegador, além de estilos para impressão.

A exportação abre um diálogo para escolher o nome e a pasta de destino. O aplicativo não envia o relatório automaticamente para a internet.

## O que o programa verifica

A tabela resume o escopo atual da versão 1.1.0.

| Área | O que é consultado | Exemplos de informações |
|---|---|---|
| Sistema | Dados locais do sistema operacional e sessão | Versão do Linux Mint, kernel, tipo de sessão e versão do Cinnamon |
| Instalação do OBS | Executável e metadados dos gerenciadores conhecidos | Caminho do executável, versão do pacote APT, informações de Flatpak ou Snap |
| Perfis do OBS | Arquivos de configuração encontrados nos diretórios conhecidos | Modo de saída, codificadores, formato, resolução e FPS |
| Codificação de vídeo | Campos salvos nos perfis e JSONs de codificador | NVENC, x264, CQP/CQ, predefinição, perfil e keyframes, quando registrados |
| Áudio | Configuração salva e inventário exposto pelo servidor de áudio | Codificador, 48 kHz/estéreo, dispositivos de entrada e saída |
| GPU NVIDIA | Saída de `nvidia-smi` e resumo do `inxi` | GPU, driver, temperatura, utilização e memória, quando disponíveis |
| Monitores | Saída de `xrandr` | Monitores conectados e modos atuais, quando acessíveis |
| Logs | Log mais recente encontrado nos diretórios pesquisados | Linhas relacionadas a erros, atrasos, áudio, vídeo e captura |
| Armazenamento | Diretório de gravação configurado em cada perfil | Existência do diretório e espaço disponível aproximado |

## O que o programa não faz

É importante entender o limite entre uma ferramenta de coleta de informações e uma ferramenta que modifica ou repara o sistema.

O Validador do OBS Studio **não**:

- modifica arquivos de configuração do OBS;
- altera perfis, cenas, fontes, filtros, dispositivos de áudio ou parâmetros de codificação;
- instala, remove, atualiza ou reconfigura o OBS Studio;
- instala, remove, troca ou atualiza drivers NVIDIA;
- reinicia o OBS, o Cinnamon, o servidor de áudio ou o sistema operacional;
- inicia ou encerra gravações e transmissões;
- faz uma gravação de teste automaticamente;
- mede de forma contínua o desempenho do OBS durante a gravação;
- garante que NVENC, microfones, câmeras ou dispositivos estejam funcionando perfeitamente apenas porque aparecem no relatório;
- identifica com certeza a causa de glitches, quadros perdidos, ruído, cortes ou dessincronização;
- corrige automaticamente problemas encontrados;
- examina profundamente o conteúdo de todas as cenas, fontes e filtros;
- verifica a validade de chaves de transmissão ou credenciais de serviços;
- lê `service.json` ou arquivos de credenciais/chaves de transmissão;
- envia dados, logs ou relatórios automaticamente para servidores externos;
- substitui um diagnóstico humano ou a análise direta do log completo quando necessária.

O programa opera em modo de leitura em relação às configurações do sistema e do OBS. A única escrita realizada por iniciativa do usuário é a criação de um arquivo de relatório TXT ou HTML no destino escolhido.

## Como funciona a auditoria

A aplicação usa Python 3 para ler arquivos locais e executar consultas a utilitários do sistema. A interface gráfica é implementada com GTK 3 e usa o tema GTK configurado no ambiente de trabalho.

As consultas externas ao Python são chamadas de forma não interativa, com tempo limite para evitar que um comando fique bloqueado indefinidamente. O programa não solicita privilégios de administrador para fazer a auditoria.

### Diretórios de configuração pesquisados

A versão atual procura configurações nos caminhos usuais abaixo, se existirem:

- `$XDG_CONFIG_HOME/obs-studio`;
- `~/.config/obs-studio`;
- `~/.var/app/com.obsproject.Studio/config/obs-studio`, normalmente associado ao Flatpak;
- `~/snap/obs-studio/current/.config/obs-studio`, normalmente associado ao Snap.

A pesquisa cobre esses caminhos conhecidos, não qualquer diretório personalizado que o usuário possa ter definido fora deles.

### Arquivos consultados

Dependendo da instalação e da versão do OBS, o programa pode ler:

- `global.ini` e `user.ini`, para tentar obter perfil e coleção selecionados;
- `basic.ini`, para configurações salvas do perfil;
- `recordEncoder.json` e `streamEncoder.json`, para algumas opções detalhadas do codificador;
- arquivos `.txt` de log, para pesquisar mensagens relevantes.

Os campos e formatos internos do OBS podem variar entre versões. Por isso, alguns valores podem aparecer como **não informado** mesmo quando a opção existe na interface do OBS. Nesses casos, confira a configuração diretamente no OBS.

## Requisitos e compatibilidade

### Ambiente recomendado

A versão 1.1.0 foi preparada principalmente para:

- Linux Mint 22.3 Cinnamon;
- sistemas baseados em Ubuntu com pacotes Debian compatíveis;
- Python 3.12 ou superior;
- GTK 3;
- uma sessão de desktop capaz de exibir a interface gráfica.

O diagnóstico de monitores usa `xrandr` e depende do acesso à sessão gráfica. Em Wayland, máquinas remotas ou ambientes sem `DISPLAY`, parte das informações sobre monitores pode não estar disponível. A ferramenta pode exibir avisos quando utilitários ou dispositivos não forem encontrados.

A consulta à NVIDIA é opcional. Se `nvidia-smi` não estiver instalado ou o driver não estiver funcionando, o restante da auditoria pode continuar, mas os dados da GPU serão limitados.

### Dependências do pacote Debian

O pacote declara as dependências abaixo. O APT deve instalá-las automaticamente durante a instalação do `.deb`, se os repositórios configurados fornecerem os pacotes correspondentes.

- `python3 (>= 3.12)`;
- `python3-gi`;
- `gir1.2-gtk-3.0`;
- `inxi`;
- `x11-xserver-utils`;
- `pulseaudio-utils`;
- `desktop-file-utils`.

A disponibilidade de `nvidia-smi`, `flatpak`, `snap` e de alguns outros comandos depende da configuração da máquina. A ausência de um deles pode limitar uma seção do relatório, sem necessariamente impedir a execução do aplicativo.

## Instalação pelo pacote DEB

1. Abra a página de **Releases** do repositório e baixe o arquivo `.deb` da versão desejada, se ele estiver publicado como um artefato da versão.
2. Abra um terminal na pasta em que o arquivo foi salvo.
3. Instale o pacote com o APT. Substitua o nome do arquivo pelo nome exato baixado.

```bash
sudo apt install ./validar-obs-linux-mint_1.1.0_all.deb
```

Depois da instalação, abra o menu de aplicativos e procure **Validador do OBS Studio** na categoria **Multimídia**.

O programa também pode ser iniciado pelo terminal:

```bash
validar-obs-linux-mint
```

Para executar somente a auditoria em modo texto, sem abrir a interface GTK:

```bash
validar-obs-linux-mint --audit
```

O modo `--audit` é útil para diagnóstico por terminal e automação simples. Ele imprime o relatório na saída padrão e não exporta automaticamente os arquivos TXT ou HTML.

### Remover o pacote

```bash
sudo apt remove validar-obs-linux-mint
```

A remoção do pacote não se propõe a apagar nem modificar os perfis ou as cenas do OBS existentes na pasta pessoal do usuário.

## Construção do pacote a partir do código-fonte

O arquivo de fontes inclui o script `build-package.sh`, a pasta `src/` com o aplicativo e a estrutura `pkg/` usada para montar o pacote Debian.

### Ferramentas necessárias

Em uma distribuição baseada em Debian ou Ubuntu, instale a ferramenta de construção de pacotes se ela ainda não estiver presente:

```bash
sudo apt update
sudo apt install dpkg-dev
```

Extraia o arquivo de fontes, entre no diretório do projeto e execute:

```bash
chmod +x build-package.sh
./build-package.sh
```

O script copia o código da pasta `src/` para a estrutura de empacotamento e usa `dpkg-deb` para gerar o arquivo:

```text
validar-obs-linux-mint_1.1.0_all.deb
```

O pacote é gerado no diretório do projeto. A construção não instala o software na máquina. Para instalar o pacote gerado, use o comando de instalação da seção anterior.

> Ao preparar uma nova versão, atualize a versão nos metadados do pacote Debian e no script de construção, e valide novamente o pacote antes de publicar uma Release.

## Como utilizar

1. Abra o **Validador do OBS Studio** pelo menu Multimídia ou pelo comando `validar-obs-linux-mint`.
2. Para obter informações úteis dos logs, abra o OBS e, se possível, reproduza o comportamento que deseja investigar. Por exemplo, faça uma gravação de teste curta se o problema ocorre durante a gravação.
3. Volte ao Validador e clique em **Executar auditoria**.
4. Aguarde o término da coleta. O relatório aparecerá na janela.
5. Revise os itens classificados como `AVISO` ou `FALHA`. Eles indicam pontos para conferência, e não necessariamente a causa raiz do problema.
6. Use **Exportar TXT**, **Exportar HTML** ou **Copiar** para guardar os resultados.
7. Antes de publicar ou enviar o relatório a terceiros, revise os caminhos locais, nomes de dispositivos, nomes de fontes e demais detalhes do ambiente.

Para investigar um problema intermitente, execute uma gravação de teste enquanto observa as estatísticas do próprio OBS. Em seguida, execute a auditoria novamente para que ela possa consultar o log atualizado. O Validador não substitui a janela **Exibir > Estatísticas** do OBS nem grava métricas em tempo real.

## Exportação de relatórios

### TXT

O formato TXT mantém o relatório em texto simples. Ele é apropriado para:

- anexar a chamados de suporte;
- compartilhar em fóruns técnicos;
- comparar resultados de duas execuções;
- pesquisar palavras-chave com ferramentas de texto;
- arquivar a saída de uma auditoria.

### HTML

O formato HTML organiza a saída em seções e destaca os marcadores `OK`, `AVISO`, `INFO` e `FALHA`. O estilo acompanha a preferência de tema claro ou escuro do navegador e inclui uma folha de estilo para impressão.

O HTML é um documento estático. Ele não executa nova auditoria e não atualiza os dados automaticamente após ser exportado.

### Nomes e local de saída

Por padrão, a janela de salvamento começa na pasta pessoal do usuário. O usuário pode escolher outro diretório e nome de arquivo. O programa pergunta antes de sobrescrever um arquivo existente por meio do diálogo de salvamento.

## Como interpretar os resultados

O relatório usa quatro marcadores principais:

| Marcador | Significado |
|---|---|
| `OK` | O dado foi identificado e passou por uma verificação simples implementada pela ferramenta. Não é uma certificação completa de funcionamento. |
| `AVISO` | Há uma informação ausente, uma configuração que merece conferência ou uma condição potencialmente relevante. |
| `INFO` | Informação coletada para contextualizar a análise. Não representa necessariamente sucesso ou falha. |
| `FALHA` | Uma condição específica reconhecida pelo código foi detectada e precisa ser examinada. |

Exemplos:

- O reconhecimento de um codificador NVENC indica que o perfil salvo faz referência a esse tipo de codificador. Para confirmar que está funcionando em gravação, faça um teste e confira o log.
- A mensagem de que um dispositivo de áudio está disponível não garante que ele seja o dispositivo selecionado na fonte correta do OBS.
- Uma ocorrência `Failed to create source` no log pode apontar para fonte ou plugin indisponível, mas a causa exata exige a análise da mensagem completa e da configuração correspondente.
- A ausência de correspondências no log pesquisado significa apenas que os padrões implementados não encontraram linhas correspondentes no log consultado. Não garante ausência de problemas.

## Privacidade e segurança

O aplicativo foi projetado para executar a auditoria localmente e não enviar os resultados para servidores externos.

Cuidados importantes:

- O relatório pode conter nome de usuário no caminho de arquivos, nomes de dispositivos USB, modelo de hardware, versão de driver, endereços ou detalhes de configuração que identifiquem parcialmente a máquina.
- O relatório pode incluir linhas de logs do OBS com nomes de fontes, plugins ou caminhos locais.
- Antes de publicar um relatório em issue, fórum, chat ou repositório público, revise o conteúdo e remova informações que não deseja divulgar.
- A ferramenta não lê `service.json` nem arquivos com credenciais ou chaves de transmissão, conforme a implementação atual.
- Não é necessário executar o aplicativo com `sudo`. A instalação do pacote Debian exige privilégios administrativos, mas a auditoria deve ser executada como usuário normal.

Embora o código evite coletar arquivos completos de configuração de transmissão e selecione somente algumas linhas dos logs, sempre revise o relatório antes de compartilhá-lo.

## Limitações conhecidas

- A estrutura e os nomes dos campos de configuração do OBS podem mudar entre versões. Nesses casos, alguns campos podem não ser identificados.
- O software lê configurações salvas, que podem não representar o estado efetivo de uma sessão em execução se as mudanças ainda não tiverem sido gravadas nos arquivos.
- Podem existir várias instalações e pastas de configuração do OBS na mesma máquina. O relatório lista os caminhos encontrados, mas nem sempre consegue determinar qual instância está sendo usada naquele momento.
- A seleção do perfil é inferida a partir dos arquivos globais encontrados. O programa não controla a interface do OBS nem confirma a seleção visual atual.
- A análise de logs usa padrões de texto e não interpreta todos os eventos nem todas as mensagens possíveis.
- Somente o log mais recente encontrado é analisado, e a ferramenta limita a quantidade de linhas exibidas.
- A identificação de monitores depende de `xrandr` e do acesso à sessão gráfica.
- A consulta à NVIDIA depende dos utilitários e do driver instalados.
- O aplicativo não valida transmissão ao vivo, conectividade de rede, ingest do serviço de streaming, qualidade do áudio percebida nem sincronização audiovisual por análise de mídia.
- O aplicativo não corrige configurações automaticamente.
- A versão 1.1.0 foi preparada com foco no Linux Mint 22.3 Cinnamon e não deve ser considerada testada em todas as distribuições, versões do OBS, placas gráficas ou ambientes de desktop.

## Solução de problemas

### O aplicativo não abre

Verifique se as dependências foram instaladas:

```bash
sudo apt install python3-gi gir1.2-gtk-3.0
```

Tente iniciar pelo terminal para visualizar eventuais mensagens de erro:

```bash
validar-obs-linux-mint
```

### O OBS não é encontrado

Confirme que o OBS está instalado e já foi executado pelo menos uma vez no usuário que está rodando o Validador. A ferramenta procura os locais de configuração mais comuns e pode não encontrar diretórios personalizados.

### Algumas opções aparecem como `não informado`

Abra as configurações correspondentes diretamente no OBS. O formato dos arquivos pode variar, e a ferramenta só reconhece os campos implementados na versão atual. Não interprete um campo ausente automaticamente como uma configuração desativada.

### O relatório não mostra erros de renderização

Se o problema ocorrer durante a gravação, reproduza-o enquanto o OBS estiver aberto. Depois execute uma nova auditoria para consultar o log mais recente. Para diferenciar atraso de renderização e atraso de codificação, consulte também **Exibir > Estatísticas** dentro do OBS.

### Os monitores não aparecem corretamente

Confirme que está executando o aplicativo dentro da sessão gráfica do usuário. A consulta usa `xrandr`, que pode não fornecer a mesma informação em todas as sessões Wayland ou ambientes remotos.

## Estrutura do projeto

A distribuição de fontes da versão 1.1.0 inclui os principais arquivos abaixo:

```text
validar-obs-linux-mint-1.1.0/
├── README.md
├── README-instalado.md
├── LEIA-ME-INSTALACAO.txt
├── build-package.sh
├── src/
│   └── validar_obs_linux_mint_gui.py
└── pkg/
    ├── DEBIAN/
    │   ├── control
    │   ├── postinst
    │   └── postrm
    └── usr/
        ├── bin/
        │   └── validar-obs-linux-mint
        ├── share/
        │   ├── applications/
        │   │   └── validar-obs-linux-mint.desktop
        │   ├── icons/hicolor/scalable/apps/
        │   │   └── validar-obs-linux-mint.svg
        │   └── doc/validar-obs-linux-mint/
```

## Contribuições

Contribuições são bem-vindas. Antes de abrir uma issue, informe, quando possível:

- distribuição e versão do Linux;
- ambiente gráfico e tipo de sessão, por exemplo Cinnamon/X11 ou Cinnamon/Wayland;
- versão do OBS Studio e forma de instalação;
- modelo da GPU e versão do driver, se for relevante para o problema;
- trecho pertinente do relatório ou do log;
- passos mínimos para reproduzir o problema.

Remova chaves, credenciais, caminhos privados e outras informações sensíveis antes de publicar logs. Para mudanças no código, procure manter a auditoria somente de leitura, limitar a coleta ao necessário e documentar quando novos comandos ou diretórios forem consultados.



## Aviso

O Validador do OBS Studio é uma ferramenta auxiliar da comunidade. Não é um componente oficial do OBS Project, do Linux Mint ou da NVIDIA. Os nomes OBS Studio, Linux Mint e NVIDIA são usados apenas para indicar compatibilidade e contexto técnico.
