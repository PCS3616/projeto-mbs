# Monitor _Batch_ Simples

## Introdução

O monitor batch, nesta disciplina, é um programa responsável por executar uma
sequência de tarefas, ou _jobs_. Estes _jobs_ são lidos de um arquivo de texto -
um dispositivo do tipo disco que deverá estar na Unidade Lógica `0` da MVN -
que possui um formato específico. O "monitor" foi o embrião dos sistemas
operacionais como os conhecemos hoje, e você pode ler mais a respeito dessa
evolução [aqui](https://www.cs.gordon.edu/courses/cs322/lectures/history.html).

Os _jobs_ são descritos por meio de uma linguagem formal que define qual é o
formato dos arquivos que são compreendidos pelo MBS. Se um arquivo não obedece
precisamente a este formato, ele é inválido, e o seu MBS deverá usar a
instrução `OS` da MVN para imprimir as mensagens de erro apropriadas.

Uma explicação mais detalhada da motivação por trás do desenvolvimento do
monitor, além de referências sobre os detalhes de implementação da linguagem
de descrição de _jobs_ e dos comandos do MBS, podem ser encontrador nos
materiais de aula, respectivamente
[aqui](https://drive.google.com/file/d/1t1W5kQbf-xqDGgLzBsGRl6fTTUYVHiAv/view?usp=share_link)
e [aqui](https://drive.google.com/file/d/1d9jjfXHXVJSUdxYtLUPOlde-YIICDXt7/view?usp=share_link)
(os arquivos estão disponíveis somente para emails do domínio USP).

## O que será fornecido

Serão fornecidas diversas sub-rotinas que devem facilitar o desenvolvimento do
monitor, fornecendo funcionalidades básicas de tratamento de caracteres ASCII,
além de funções para realizar _dump_ e _load_ da memória. Isso permite que este
projeto seja desenvolvido mesmo que o laboratório de _dumper_ e _loader_ não tenha
sido realizado.

Em vez de arquivos ASM, será fornecido um único arquivo `libmbs.int` com o resultado
da ligação parcial das sub-rotinas, todas relocáveis.

As rotinas fornecidas, suas funcionalidades e interfaces estão descritas
a seguir:

* `DUMPER`: realiza _dump_ de uma região da memória com parâmetros determinados
   por seus argumentos
   * Argumentos
        | Rótulo        | Descrição                                 |
        |---------------|-------------------------------------------|
        | `DUMP_INI`    | Endereço inicial do _dump_                |
        | `DUMP_TAM`    | Tamanho total da imagem em palavras       |
        | `DUMP_UL`     | Número da unidade lógica                  |
        | `DUMP_BL`     | Comprimento do bloco em palavras          |
        | `DUMP_EXE`    | Endereço da primeira instrução executável |
    * Resultados
        * Não possui


* `LOADER`: realiza _load_ de uma unidade lógica para a região de memória
   especificada no arquivo
    * Argumentos: rótulo `LOADER_UL` contendo unidade lógica da qual realizar
      _load_
    * Resultados: no acumulador, valor da primeira instrução executável em caso
      de sucesso, ou código de erro em caso de fracasso
        | Código de erro | Significado                      |
        |----------------|----------------------------------|
        | `0xFFFE`       | Erro no tamanho total da imagem  |
        | `0xFFFC`       | Erro no _checksum_               |

* `CHTOI`: Converte uma palavra em hexadecimal codificado em ASCII para o o número
   inteiro correspondente (`CHTOI("0010") = 0x0010` por exemplo)
    * Argumentos
        | Rótulo    | Descrição |
        |-----------|-----------|
        | `CH_IN_A` | 2 "bytes" mais significativos em ASCII
        | `CH_IN_B` | 2 "bytes" menos significativos em ASCII
    * Resultados: rótulo `CH_ANS` com valor hexadecimal decodificado do ASCII

* `UNPACK`: extrai os bytes de uma palavra contida no acumulador, colocando-os
  em dois endereços da memória
    * Argumentos: palavra a ser dividida no acumulador
    * Resultados
        | Rótulo   | Descrição |
        |----------|-----------|
        | `UNP_B1` | MSB do AC |
        | `UNP_B2` | LSB do AC |

* `RSHIFT2`: aplica um _right shift_ (`>>`) de 2 nibbles (8 bits) ao valor no
  acumulador e retorna resultado no acumulador

* `IS_HEX`: Retorna, no acumulador, 1 se o valor passado no acumulador for um
  caractere ASCII hexadecimal, e -1 (`0xFFFF` em complemento de dois) caso contrário

## O trabalho

Neste trabalho, o MBS deve suportar até três tipos de _job_: _load_ (`LO`),
_dump_ (`DU`) e _execute_ (`EX`). O _job_ de _load_ lê os parâmetros do arquivo
de texto e invoca a sub-rotina do _loader_, que segue a especificação do
_loader_ apresentado em aula e desenvolvido em laboratórios anteriores;
analogamente, o _job_ de _dump_ lê os parâmetros de _dump_ do arquivo de texto
e invoca a sub-rotina do _dumper_.

O seu MBS deve tratar as 4 situações de erro apresentadas em aula e descritos no
material de referência Se, durante a leitura do arquivo de jobs, o MBS encontrar
alguma dessas situações, ele deve usar a instrução `OS` para emitir o erro de
código especificado.

Ainda que empregue rotinas relocáveis, o código principal de seu MBS deve ser
desenvolvido entre os endereços absolutos `0x0` e `0x600`, e a base de relocação
para gerar o executável final deve ser `0x700`. É permitido desenvolver outras
rotinas (preferencialmente relocáveis), mas é exigido que o código principal
seja absoluto.

## Perguntas

Seguem algumas perguntas a serem respondidas depois da codificação:

1.  Os algoritmos propostos podem ser mais eficientes? Como? Se sim, por
    que a forma menos eficiente foi escolhida?

2.  Os códigos escritos podem ser mais eficientes? Como? Se sim, por que
    a forma menos eficiente foi escolhida?

3.  Qual foi a maior dificuldade em implementar a biblioteca?

4.  Forneça uma visão de alto nível das etapas necessárias para executar um
    _job_, desde a leitura do _job_ até o seu processamento e a leitura do
    _job_ seguinte (ou do final do arquivo).

5.  Explique quais sub-rotinas foram implementadas durante o desenvolvimento e
    justifique a escolha por tornar esses trechos de código sub-rotinas.

6.  Aponte pontos positivos e negativos de a rotina principal do MBS não ser
    relocável


## Entrega

Dois ou mais arquivos devem ser entregues:

1.  **Monitor:** um arquivo em ASM chamado `monitor.asm` contendo o código da
    rotina principal do MBS.

2.  **Relatório:** um arquivo chamado `relatorio_<NUSP1>_<NUSP2>.pdf` para
    trabalhos realizados em dupla, ou `relatorio_<NUSP>.pdf` para individuais
    contendo uma descrição resumida do problema e dos conceitos e uma descrição
    detalhada das etapas de resolução, da estratégia utilizada em cada módulo
    e outras informações que julgarem úteis. O relatório pode conter imagens
    das execuções de teste;

3.  Outros arquivos que julgar necessário.
