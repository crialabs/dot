# dotfiles

Meus arquivos de configuração e bootstrap para `bash`, `fish`, `git`, `iTerm2`, `Visual Studio Code` e até mesmo `brew`, `macOS settings` e as configurações de alguns aplicativos. Inclui scripts para automatizar a instalação. Estes são meus dotfiles e fluxo de trabalho personalizados e, portanto, provavelmente não são adequados para todos os outros. Dito isso, fiz o meu melhor para tornar meus dotfiles o mais leves e sensatos possível, então pode valer a pena bifurcá-los como ponto de partida para os seus dotfiles.

## Premissas

- seu shell principal do dia a dia é o shell `fish`
- você usa apenas o macOS
- você mantém seu código em `~/Code`
- você usa o Dropbox para fazer backup das configurações dos seus aplicativos (por exemplo, iTerm)

## Instalando

Ao contrário da maioria dos outros repositórios de dotfiles que usam links simbólicos ou `rsync` para gerenciar seus dotfiles, usaremos o Git e somente o Git. Veja este excelente [artigo da Atlassian](https://www.atlassian.com/git/tutorials/dotfiles) e o [tutorial do DistroTube](https://www.youtube.com/watch?v=tBoLDpTWVOM) para uma introdução, embora esta configuração seja um pouco diferente. A ideia básica é que a estrutura de diretórios deste repositório espelhe a estrutura de diretórios de `$HOME` quando todos os dotfiles estiverem em seus devidos lugares. Configurando este repositório desta forma, seremos capazes de definir nosso diretório de trabalho do git como o diretório inicial e extrair os dotfiles diretamente nos diretórios onde eles devem estar.

```sh
# clonar o repositório no local esperado (~/Code/dotfiles)
cd # iniciar as etapas de instalação a partir do diretório inicial
mkdir -p Code/dotfiles
git clone https://github.com/patrickf1/dotfiles ~/Code/dotfiles

# extrair todos os arquivos em nosso diretório inicial, tornando-o o diretório de trabalho do git
git --git-dir=Code/dotfiles/.git --work-tree=. reset --hard

# ocultar arquivos não rastreados para que a saída do git status não seja sobrecarregada
git --git-dir=Code/dotfiles/.git config --local status.showUntrackedFiles no

# executar todos os scripts de instalação, que estão localizados em .dotfiles_meta
cd Code/dotfiles/.dotfiles_meta
brew bundle install
fish install_shell_utilities.fish
fish configure_macos.fish
```

## Fluxo de trabalho para atualização de dotfiles

Um recurso desta configuração é que mantemos duas cópias dos dotfiles: uma com root em `$HOME` e outra em `~/Code/dotfiles` (este não é um repositório git vazio). A primeira cópia é a nossa versão ativa, lida e usada ativamente pelo nosso sistema. No entanto, normalmente não queremos modificar diretamente os dotfiles ativos, pois um erro pode danificar seu ambiente e dificultar a correção desse erro. Além disso, digamos que queremos modificar vários arquivos de uma só vez para uma alteração transversal. Abrir `$HOME` em nosso editor não é o ideal, pois acabaremos carregando muitos arquivos irrelevantes que não temos interesse em editar. É aí que a última cópia entra em jogo: é um repositório git inerte, seguro para modificar e que contém exatamente os dotfiles com os quais nos importamos.

Para facilitar a execução de operações git no repositório do diretório home, temos um comando especial: `dot`, uma [função fish carregada automaticamente](https://fishshell.com/docs/current/tutorial.html#autoloading-functions) que encapsula `git` com as opções `--git-dir` e `--work-tree` definidas para nós.

Para a maioria das modificações, faça o checkout de uma nova branch, abra o repositório em nosso editor e faça as alterações. Assim que nossas alterações forem confirmadas, navegue de volta ao diretório home e execute `dot reset --hard` para fazer o checkout de nossas alterações no repositório home e, assim, torná-las ativas. Podemos ter que iniciar uma nova sessão do fish se houver alterações em `config.fish`, por exemplo. Se as alterações danificarem nosso ambiente, podemos revertê-las rapidamente executando `dot reset --hard main` novamente.

Tenha muito cuidado para não executar `dot add -A` no diretório inicial. Para evitar o commit acidental de arquivos e pastas confidenciais, certifique-se de adicioná-los a `.gitignore`. Muitas pastas comuns que não fazem sentido rastrear já são ignoradas.

Ao adicionar novos arquivos, lembre-se de que arquivos não rastreados não são exibidos pelo git status.

# Instruções específicas para atualizar aplicativos não automatizados

## Configurações do VSCode

O VSCode não suporta a sincronização automática do arquivo `settings.json` de forma amigável para dotfiles. Como resultado, precisamos mantê-lo sincronizado manualmente. Após atualizar as configurações do VSCode

1. Abra as configurações
2. Clique em Abrir Configurações (JSON) no canto superior direito
3. Classifique o arquivo como JSON
4. Copie para o `settings.json` que está registrado neste repositório
