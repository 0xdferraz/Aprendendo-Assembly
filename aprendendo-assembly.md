# Aprendendo Assembly


## Conceitos

### Pilha e Memória RAM
A pilha é uma região de memória alocada na RAM pelo sistema operacional quando um programa é executado. Essa área é usada para armazenar o contexto de execução das funções, como endereços de retorno, registros preservados e variáveis locais. Embora a pilha esteja localizada na memória RAM, ela representa apenas uma parte da memória utilizada pelo programa em execução. A memória RAM abriga outras áreas, como a seção de código (instruções do programa), seção de dados (variáveis globais e estáticas) e outras seções necessárias para a execução do programa.

A pilha geralmente cresce em direção a endereços de memória menores. Isso significa que o topo da pilha está no endereço mais alto e vai diminuindo conforme mais dados são empilhados. Isso significa que toda vez que um valor é inserido nela, push, o valor de RSP (Stack Pointer, que aponta para o topo atual da pilha) é subtraído pelo tamanho em bytes do valor. E na mesma lógica um pop incrementa o valor de ESP.

### Word, Doubleword e Quadword
As palavras "Word", "Doubleword" e "Quadword" são termos usados em programação para descrever o tamanho e a capacidade de armazenamento de dados em memória.

***Word***: Uma "Word" refere-se a um tamanho de dados de 16 bits, o que significa que pode armazenar 2 bytes de informação. É chamada de "Word" porque representa o tamanho natural das palavras processadas pelo processador em muitas arquiteturas. No entanto, é importante observar que o termo "Word" pode variar de acordo com a arquitetura específica, pois algumas arquiteturas podem definir uma "Word" com um tamanho diferente.

***Doubleword***: Um "Doubleword" é um tamanho de dados de 32 bits, o que significa que pode armazenar 4 bytes de informação.

***Quadword***: Um "Quadword" é um tamanho de dados de 64 bits, o que significa que pode armazenar 8 bytes de informação.

## Hello World Simples
```assembly
.intel_syntax noprefix
.section .text
.global main
main:
  push rbp
  mov rbp, rsp
  sub rsp, 16
  lea rdi, [rip + hello_world]
  call printf@plt
  xor rax, rax
  mov rsp, rbp
  pop rbp
  ret
.section .rodata
hello_world:
	.string "Hello world from assembly!\n"
```

### Diretivas
As diretivas em Assembly são instruções especiais que fornecem informações ao assembler, orientando-o sobre como montar o código e como tratar diferentes partes do programa. Essas diretivas não são traduzidas diretamente em instruções de máquina, mas fornecem orientações e metadados para o processo de montagem.

***.intel_syntax***: A diretiva .intel_syntax noprefix é usada em códigos Assembly para definir a sintaxe de escrita das instruções como a sintaxe Intel. Essa diretiva instrui o montador a interpretar e esperar a sintaxe Intel para as instruções subsequentes, em vez da sintaxe AT&T, que é a sintaxe padrão usada por alguns montadores. A opção "noprefix" noprefix é usada para eliminar a necessidade de prefixar os registradores com %. Isso simplifica a escrita do código Assembly e sua legibilidade.

***.global***: Quando um símbolo é declarado como global usando a diretiva ".global", ele se torna visível para outros módulos ou arquivos Assembly que fazem referência a esse símbolo. Essa visibilidade permite que outros módulos acessem e utilizem o símbolo, seja para fazer chamadas a uma função global, acessar uma variável global ou utilizar uma constante global definida em outro arquivo.

***.section***: Essa diretiva é usada para definir seções no código Assembly, ajudando a organizar o código e os dados do programa de maneira estruturada.

***.string***: Aloca espaço para a sequência de caracteres na memória e armazena-os consecutivamente. O caractere nulo (\0) é adicionado automaticamente no final da sequência para indicar o término da string, conforme esperado em muitas linguagens de programação.

### Seções
Ao definir uma seção de código, através da diretiva ***".section"***, é possível separar diferentes partes do programa de acordo com sua finalidade. Por exemplo, é comum ter seções separadas para código, dados, tabelas de símbolos, constantes, entre outros. Isso ajuda a organizar e gerenciar o programa de maneira mais estruturada.

```assembly
.section .text
...
.section .rodata
```

***.text***: É uma seção usada para armazenar o código executável do programa.

***.rodata***: É usada para armazenar dados somente leitura (read-only data). Esses dados são acessíveis durante a execução do programa, mas não podem ser modificados.

### Registradores

***rbp***: Ele é usado para referenciar variáveis locais e *acessar informações na pilha* durante a execução de um programa.
Quando uma função é chamada, o valor atual do registrador RBP é geralmente empurrado para a pilha para preservar seu valor anterior. Em seguida, o valor do registrador RSP (Stack Pointer) é copiado para o RBP, estabelecendo uma "base" para referenciar as variáveis locais e parâmetros da função atual. Ele mantém o endereço do início do frame de ativação de uma função, que contém informações relevantes para a execução da função.

***rsp***: O registrador RSP, Stack Pointer, serve como ponteiro para o topo da pilha podendo ser usado como referência inicial para manipulação de valores na mesma. Onde o "topo" nada mais é que o último valor empilhado. Ou seja, o Stack Pointer está sempre apontando para o último valor na pilha.

***rip***: O registrador RIP (Instruction Pointer Register) é um registrador usado para armazenar o endereço de memória da próxima instrução a ser executada. É automaticamente atualizado pelo processador após cada instrução ser executada, apontando para o próximo endereço de memória onde a próxima instrução está localizada. Em termos simples, o RIP mantém o controle do fluxo de execução do programa, indicando qual instrução será executada em seguida. À medida que as instruções são executadas, o RIP é atualizado automaticamente para apontar para a próxima instrução.

***rdi***: O RDI é conhecido como o primeiro registrador de argumento (First Argument Register) e é usado para passar o primeiro argumento para uma função quando ela é chamada. É comum que o valor de RDI seja usado para passar um ponteiro para uma estrutura de dados ou para passar um valor inteiro ou um endereço de memória.

***rax***: O RAX é frequentemente usado para armazenar o valor de retorno de uma função. Quando uma função é chamada, o valor de retorno é colocado no registrador RAX, para que possa ser acessado pelo código que chamou a função.

### Funções
As funções em Assembly são blocos análogos a sub-rotinas ou subprogramas permitindo modularizar o código, reutilizar blocos de instruções e melhorar a organização e a legibilidade do programa.
```assembly
funcao:
  ...
```
Uma função possui as seguintes características:

1. ***Configuração da pilha***: Antes de começar a executar o corpo da função, é comum configurar a pilha para preservar registros, alocar espaço para variáveis locais e, se necessário, passar argumentos. Isso geralmente envolve o uso de instruções como push e pop para empilhar e desempilhar dados da pilha.

2. ***Parâmetros da função***: Os parâmetros da função podem ser passados para a função através da pilha ou por meio de registros específicos, dependendo da convenção de chamada utilizada. Os parâmetros são acessados dentro da função usando deslocamentos de pilha ou registradores.

3. ***Variáveis locais***: As variáveis locais são armazenadas na pilha ou em registradores de propósito geral. O RBP (Base Pointer) é frequentemente usado como referência para acessar as variáveis locais, utilizando deslocamentos em relação ao RBP.

4. ***Corpo da função***: O corpo da função contém o conjunto de instruções que serão executadas para realizar a tarefa desejada. Isso pode incluir operações aritméticas, manipulação de dados, acesso a variáveis locais, chamadas a outras funções e controle de fluxo.

5. ***Retorno da função***: Uma função Assembly pode retornar um valor através de um registrador específico, como RAX, ou pode armazenar o valor de retorno em um local específico da memória, como a pilha. O valor de retorno é então acessado pelo chamador da função.

6. ***Chamadas de função***: As funções Assembly podem ser chamadas de outras partes do código Assembly usando a instrução de chamada apropriada, como call. As chamadas de função geralmente envolvem o empilhamento dos argumentos na pilha antes da chamada e a recuperação do valor de retorno após a chamada.

### Configurando a Pilha
Essas operações são comumente realizadas no início de uma função para garantir que a pilha seja configurada corretamente e que as variáveis locais possam ser alocadas e acessadas adequadamente durante a execução da função.

```assembly
push rbp
mov rbp, rsp
sub rsp, 16
```

1. ***push rbp***: Essa instrução empurra o valor atual do registrador RBP (Base Pointer) para a pilha. Isso é feito para preservar o valor anterior do RBP, já que ele será atualizado posteriormente. O RBP é usado como uma referência para acessar variáveis locais e parâmetros de função. Ao empurrar o valor atual do RBP para a pilha, ele pode ser restaurado posteriormente, garantindo a consistência ao retornar da função.

2. ***mov rbp, rsp***: Essa instrução copia o valor atual do registrador RSP (Stack Pointer) para o RBP. Agora, o RBP contém o endereço do topo atual da pilha. Isso estabelece uma "base" para referenciar as variáveis locais e os parâmetros da função atual. Ao copiar o valor do RSP para o RBP, é possível acessar esses elementos usando deslocamentos em relação ao RBP.

3. ***sub rsp, 16***: Essa instrução subtrai 16 bytes do valor atual do RSP. Essa operação é usada para reservar espaço na pilha para variáveis locais. Ao subtrair do RSP, o topo da pilha é movido para baixo, criando espaço para as variáveis. O valor "16" aqui é apenas um exemplo. O tamanho exato depende das necessidades da função e da quantidade de espaço necessário para as variáveis locais.

### Instruções

***lea***: A instrução lea (Load Effective Address) carrega o endereço efetivo de um operando no registrador de destino.
```assembly
lea rdi, [rip + hello_world]
```
Nesse caso, o operando é [rip + hello_world] e o registrador *rdi* é usado como destino. O uso de rip (Instruction Pointer) com hello_world significa que o endereço efetivo de hello_world será calculado com base no valor atual do rip. Assumindo que hello_world é uma variável ou uma etiqueta que representa uma sequência de caracteres na seção ".rodata", o valor calculado será o endereço dessa sequência de caracteres.

***call***: A instrução call é usada para chamar uma função. 
```assembly
call printf@plt
```
O destino da chamada é especificado como printf@plt, onde printf é o nome da função e plt significa Procedure Linkage Table, que é uma estrutura usada em sistemas operacionais baseados em UNIX, como Linux, para resolver chamadas de função dinâmicas em tempo de execução.
Nesse caso, o registrador *rdi* que recebeu anteriormente o endereço da string "Hello World" será recebido como argumento da função.

***push***: Empilha um valor na pilha. Esse valor pode ser uma Word, Doubleword ou Quadword.

***mov***: Instrução usada para mover (copiar) dados entre registradores, endereços de memória e valores imediatos.

***sub***: A instrução sub é usada para realizar subtração em Assembly. Ela subtrai o valor de uma origem (operando) de um destino e armazena o resultado no destino.

***xor***: A instrução xor é usada para realizar uma operação de ou-exclusivo (XOR) lógica em Assembly.
|X1|X2|Saída|
|-|-|-|
|1|1|0|
|1|0|1|
|0|1|1|
|0|0|0|

***pop***: A instrução pop é usada para desempilhar (pop) um valor da pilha em Assembly. Ela remove o valor do topo da pilha e o coloca em um destino especificado.

```assembly
pop destino
```

***ret***: A instrução ret é usada para retornar de uma função em Assembly. Ela é usada geralmente no final de uma função para retornar ao ponto de chamada original. A instrução ret geralmente desempilha o endereço de retorno da pilha e salta para esse endereço. o valor de retorno da função geralmente é armazenado no registrador *rax*.

```assembly
ret
```

### Encerrando a Função Main
Processo comumente utilizado para encerrar a Função Main. Note que em outras funções do código não é necessário encerrar a função da mesma forma que a Main.
```assembly
  xor rax, rax
  mov rsp, rbp
  pop rbp
  ret
```

***xor rax, rax***: A instrução xor realiza uma operação lógica de ou-exclusivo (XOR) bit a bit entre o registrador rax e ele mesmo. Essa operação tem o efeito de definir o valor de rax como zero. No contexto de encerramento da função main, isso é frequentemente usado para indicar que a função foi executada com sucesso.

***mov rsp, rbp***: A instrução mov é usada para copiar o valor do registrador rbp (Base Pointer) para o registrador rsp (Stack Pointer). Isso restaura o valor original do rsp antes de a função main ser chamada, movendo o ponteiro de pilha de volta ao seu estado anterior.

***pop rbp***: A instrução pop é usada para desempilhar o valor do registrador rbp da pilha. Essa instrução desfaz o ajuste feito no início da função main, quando o valor de rbp foi salvo na pilha. Desempilhar o valor de rbp permite restaurar o valor original do registrador antes de retornar da função.

***ret***: A instrução ret é usada para retornar da função, transferindo o controle de volta ao ponto de chamada original. Ela desempilha o endereço de retorno da pilha e salta para esse endereço. No contexto da função main, isso efetivamente encerra o programa Assembly.