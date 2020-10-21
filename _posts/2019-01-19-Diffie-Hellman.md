---
layout: post
title: "WTF is Diffie-Hellman"
description: "Uma rápida explicação sobre Diffie-Hellman"
tags: crypto EN
---


# WTF is Diffie-Hellman


Antes de partirmos para o assunto propriamente dito, é importante entendermos alguns conceitos básicos da criptografia.

### 1. Criptografia
Para nos comunicarmos com alguém na Internet de forma privada, podemos fazer uso de várias formas de proteger a informação que queremos enviar para a outra pessoa: Podemos criar um idioma que só as pessoas envolvidas conhecem, procurar meios não-convencionais de troca de informações... Ou também podemos criptografar a informação, e essa última que será nosso foco.
Para criptografar um dado, precisamos fazer uso de uma Chave Criptográfica. Essa Chave é responsável, através da Encriptação, de tornar nosso texto legível(plaintext) em ilegível(ciphertext).
OBS1: Na Criptografia Simétrica, a mesma Chave Criptográfica é utilizada para Criptografar e Descriptografar. Diferente da Criptografia Assimétrica, onde existe uma Chave Criptográfica Pública e uma Privada, para garantir a confidencialidade da informação.

### 2. Funcionamento da Criptografia Simétrica
Imaginemos o seguinte exemplo: Precisamos passar as coordenadas do ponto de resgate para um Oficial da nossa tropa, sabendo que o inimigo tem acesso ao nossos meios de comunicação.
``` python
import base64
from Crypto.Cipher import AES
obj = AES.new('chave secreta 42', AES.MODE_CBC, 'This is an IV456')
msg = "Bletchley Park Memorial 15 horas"
ciphertext = obj.encrypt(msg)
encoded64 = base64.b64encode(ciphertext)
```

A saída será

    nh01bshgzGhL2L3nnwoessDciilkS6d95tr6D1Nl45c=

Agora, o problema da Chave Simétrica:
```
-Cap.Martins: Tenente Nunes, na escuta?
-Ten.Nunes: Na escuta, pode falar.
-Cap.Martins: Parabéns pela missão, estarei enviando as coordenadas para seu resgate.
-Ten.Nunes: Entendido.
-Cap.Martins: nh01bshgzGhL2L3nnwoessDciilkS6d95tr6D1Nl45c=
-Ten.Nunes: Recebido! Agora preciso da chave pra descriptografar a informação.
-Cap.Martins: Vish...
```
A informação só pode ser decifrada se a chave utilizada na Encriptação ("chave secreta 42") também for enviada para a outra pessoa, mas nesse caso não temos um meio seguro de enviar essa chave. O Exército Inimigo já tem acesso à mensagem encriptada, se tiverem acesso à chave também, provavelmente vão decifrar a mensagem e chegar no local antes do Tenente. 
E agora?

## Diffie-Hellman 101
Graças a nossa querida Matemática, podemos fazer uso de Logaritmos Discretos para gerarmos resultados iguais com cálculos diferentes, de forma que os observadores que não estejam envolvidos no cálculo, não consigam de imediato a resposta, mesmo possuindo uma parte das variáveis que foram utilizadas. Dividirei esse tópico em duas partes para facilitar o entendimento, a primeira explicando a teoria matemática e a segunda mostrando a implementação.

### Teoria por trás do Método Diffie-Hellman
O funcionamento desse método, é uma combinação de **Aritmética Modular**, **Logaritmo Discreto**, **Números Primos** e da **Função de Euler**, pois fornecem uma função numérica que é "fácil" de ser calculada, mas difícil de ser revertida a partir do resultado obtido e possuem uma distribuição uniforme de resultados.

 1.  A **Aritmética Modular** é um sistema matemático aplicável aos números inteiros, em que a contagem do valor decresce quando atinge um certo valor, chamado de _Módulo_.
Um Exemplo facilmente encontrado na internet, é o do Relógio de ponteiro que temos em casa:

> se o relógio começa em 12:00 (meio dia) e 22 horas passam, então o resultado será 10:00 do dia seguinte, e não 34:00. Como o número de horas começa de novo depois que atinge 12, esta aritmética é chamada aritmética módulo 12.


2. Os **Logaritmos Discretos** são utilizados de forma parecida com os Logaritmos comuns, são cálculos aplicados de forma objetiva, em um conjunto numérico específico. Por Exemplo:

>  Imaginemos que G é um grupo cíclico(gerado a partir de um único elemento), finito e com _n_ elementos. 
>  Assumimos que este grupo está escrito multiplicativamente a partir de um gerador. 
>  Seja _b_ um [gerador](https://pt.wikipedia.org/wiki/Conjunto_gerador_de_um_grupo "Conjunto gerador de um grupo") de _G_; então todo elemento _g_ de _G_ pode ser escrito na forma _g_ = _b^k^_ para algum inteiro _k_. Além disso, quaisquer dois inteiros _k1_ e _k2_ representando _g_ serão [congruentes](https://pt.wikipedia.org/wiki/Congru%C3%AAncia_(%C3%A1lgebra)) ao módulo _n_.

3.  Os **Números Primos** são números específicos, que são divisíveis somente por 1 e por eles mesmos. São eles que garantem uma distribuição uniforme dos possíveis resultados de cálculo quando são utilizados como _Módulo_.

4. A **Função de Euler** **_Φ (n)_** Representa o número de inteiros positivos menores que _n_, que são relativamente primos com _n_.


Sabendo dessas características, podemos construir a Função de Diffie-Hellman da seguinte forma:
1. Capitão Martins e Tenente Nunes concordam em utilizar por exemplo, o _Módulo_ _p_ = 23 e a base _g_ = 5 (que é uma raiz primitiva do modulo 23).
2.  Capitão Martins escolhe como segredo o valor _**a**_ = 4, e envia ao Tenente Nunes o resultado do cálculo _A_ =  _g^a_ mod _p_:
    -   _A_ = 5^4^ mod 23 = 4
3.  Tenente Nunes escolhe o número secreto _**b**_ = 3, e envia para o Capitão Martins o resultado de _B_ = _g^b^_ mod _p_:
    -   _B_ = 5^3^ mod 23 = 10
4.   Capitão Martins faz o cálculo de _**s**_ = _B^a^_ mod _p_:
      -   _**s**_ = 10^4^ mod 23 = 18
5.  Tenente Nunes calcula o valor de _**s**_ = _A^b_ mod _p_:
    -   _**s**_ = 4^3^ mod 23 = 18
  
O Capitão Martins e o Tenente Nunes chegaram no mesmo resultado, e que somente eles sabem, o número 18.
Isso acontece, pois quando as características de construção do algoritmo são respeitadas, elas resultam na seguinte propriedade:

A^b^ _mod_ p = g^ab^ _mod_ p = g^ba^ _mod_ p = B^a^ _mod_ p

**OBS 1**: valores maiores nas variáveis _a_, _b_ e _p_ são fundamentais para fazer com que o algorítmo se torne mais seguro. Utilizando esses valores do exemplo, o Inimigo infiltrado na conversa só precisaria calcular **_n_ mod 23**, e esse calculo só tem 23 possíveis resultados, permitindo que o número secreto seja facilmente encontrado com um *bruteforce*.

**OBS 2**:  O inimigo teve acesso a quase todos os valores do cálculo, exceto às variáveis escolhidas secretamente pelos dois militares. É manter _a_ e _b_ em segredo, que garante que o inimigo terá trabalho pra fazer caso queira descobrir a chave secreta.

### Exemplo de implementação
Melhor do que apenas implementar o código, deixarei esse [site](https://asecuritysite.com/encryption/diffie_py) que foi fundamental para que eu compreendesse o assunto. Nele é possível inclusive testar resultados com diferentes números. Recomendo que brinquem nele pra entender de verdade o conteúdo.

## Conclusão
Pra você que leu até aqui, deixo meu agradecimento e a continuação da história de resgate do nosso oficial utilizando Diffie-Hellman para a troca de chaves.
```
...
-Ten.Nunes: Capitão, estou aguardando a chave..
-Cap.Martins: Nosso inimigo também. Vamos estabelecer uma chave através do método Diffie-Hellman, conhece?
-Ten.Nunes: Afirmativo.
-Cap.Martins: utilizaremos o gerador 163 e o valor primo 100699.
-Ten.Nunes: Entendido! Agora preciso do resultado do seu cálculo, Capitão.
-Cap.Martins: 97024
-Ten.Nunes: Recebido. O meu foi 11255.
-Cap.Martins: Certo. Te enviarei as coordenadas criptografadas utilizando a chave gerada pelo resultado 
do nosso cálculo, que é secreto, mas que devem ter dado o mesmo valor.
Ten.Nunes: Entendido!
...
```
Em Segredo, Capitão Martins escolheu o número 700, para gerar o valor que enviou ao Tenente. Assim também o Tenente o fez, mas com o número 1337.
Ambos os cálculos resultaram no valor secreto 51053, que foi utilizado para gerar chaves iguais para os dois militares.
O inimigo nunca descobriu a coordenada, e nem uma forma eficiente de fazer a tempo um _bruteforce_ de **n mod 100699**  nos valores que os militares trocaram. O Tenente foi resgatado e a missão concluída com sucesso(enquanto você aprendia umas coisas legais :D).
### Referências
1. http://matematicadainformacao.blogspot.com/2010/09/criptografia-logaritmo-discreto.html
2. https://pt.khanacademy.org/computing/computer-science/cryptography/modern-crypt/v/diffie-hellman-key-exchange-part-2
3. https://lume.ufrgs.br/handle/10183/118185
4. https://www.ime.usp.br/~rt/crPrimosRSA.prn.pdf
5. https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange
6. https://null-byte.wonderhowto.com/how-to/generate-private-encryption-keys-with-diffie-hellman-key-exchange-0180269/