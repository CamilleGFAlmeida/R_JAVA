
## **1. Classe abstrata `Conta`**
A classe `Conta` é a base para diferentes tipos de contas no banco. Ela contém atributos e métodos comuns, além de algumas regras básicas.

### **Atributos**
- `numeroAgencia`, `numeroConta`, `tipoConta`: Identificam a agência e o número da conta, além de descrever o tipo (ex.: corrente ou poupança).
- `saldo`: Saldo da conta, protegido para permitir acesso direto pelas subclasses.
- `nomeTitular`: Nome do titular da conta.
- `quantidadeContas`: Atributo estático que rastreia o total de contas criadas.

### **Métodos**
- **Construtor:** Inicializa os atributos da conta e incrementa a contagem total de contas (`quantidadeContas`).
- **`sacar(double valor)`:** Método para retirar valores do saldo, com regras gerais:
  1. Verifica se há saldo suficiente.
  2. Garante que o saque não ultrapasse o limite de R$300.
  3. Exige que o valor seja múltiplo de 20.
  - Lança exceções personalizadas (`SaldoInsuficienteException`, `LimiteSaqueException`, `MultiploDeVinteException`) quando as regras não são atendidas.
- **`transferir(Conta contaDestino, double valor)`:** Realiza uma transferência para outra conta:
  1. Tenta sacar o valor da conta atual.
  2. Caso o saque seja bem-sucedido, deposita o valor na conta destino.
- **Métodos Getters/Setters:** Controlam o acesso aos atributos da conta.
- **Métodos abstratos:** Define o contrato para subclasses implementarem:
  - `depositar(double valor)`: Cada tipo de conta define sua lógica de depósito.

---

## **2. Classe `ContaCorrente`**
Especialização da classe `Conta`, representa uma conta corrente, que possui características específicas, como cobrança de tarifas e imposto.

### **Métodos implementados**
1. **`depositar(double valor)`:** Soma o valor ao saldo da conta.
2. **`sacar(double valor)`:** Sobrescreve o método de `Conta` para incluir uma tarifa de 20% sobre o valor sacado:
   - Calcula a tarifa: `tarifa = valor * 0.20`.
   - Adiciona a tarifa ao valor do saque e delega a execução para o método `sacar` da superclasse.
3. **`getValorImposto()`:** Método da interface `Tributavel`:
   - Calcula 1% do saldo como imposto.

---

## **3. Classe `ContaPoupanca`**
Outra especialização de `Conta`, representa uma conta poupança.

### **Métodos implementados**
1. **`depositar(double valor)`:** Simplesmente soma o valor ao saldo.
2. Não sobrescreve o método `sacar`, pois herda diretamente as regras da classe `Conta`.

---

## **4. Interface `Tributavel`**
Define um contrato para objetos que são tributáveis.

- Método abstrato `getValorImposto()`:
  - Qualquer classe que implemente essa interface deve fornecer uma implementação para calcular o valor do imposto.

---

## **5. Classe `SeguroDeVida`**
Simula um seguro de vida, que também é tributável.

### **Implementação**
- Implementa a interface `Tributavel`:
  - Retorna um valor fixo de imposto (`42.0`).

---

## **6. Classe `CalculadorImposto`**
Serve para calcular o total de impostos de objetos tributáveis.

### **Atributos**
- `totalImpostos`: Acumula o valor total dos impostos registrados.

### **Métodos**
1. **`registra(Tributavel tributavel)`:** Recebe um objeto que implementa `Tributavel` e adiciona o valor do imposto ao total.
2. **`getTotalImpostos()`:** Retorna o valor acumulado de impostos.

---

## **7. Exceções personalizadas**
Exceções específicas são criadas para lidar com erros comuns em operações bancárias. Todas elas estendem a classe `Exception`, permitindo seu uso com `try-catch`.

### **Exceções disponíveis**
1. **`SaldoInsuficienteException`:** Lançada quando o saldo da conta é menor que o valor do saque ou transferência.
2. **`LimiteSaqueException`:** Lançada quando o valor do saque excede o limite de R$300.
3. **`MultiploDeVinteException`:** Lançada quando o valor do saque não é múltiplo de 20.

---

## **8. Classe de Teste `TesteBanco`**
Simula diferentes cenários para verificar o funcionamento das classes e regras implementadas.

### **Passos realizados**
1. **Criação das contas:** São criadas uma `ContaCorrente` e uma `ContaPoupanca`.
   - Mostra os dados iniciais de ambas.
2. **Testes com `ContaCorrente`:**
   - Tentativas de saque:
     - Saldo insuficiente.
     - Valor acima do limite.
     - Valor não múltiplo de 20.
     - Saque válido.
   - Depósito e atualização do saldo.
   - Transferência para a conta poupança.
3. **Testes com `ContaPoupanca`:**
   - Tentativas de saque:
     - Saldo insuficiente.
     - Saque válido.
   - Transferência para a conta corrente.

### **Tratamento de erros**
Cada operação sensível (`sacar`, `transferir`) é envolvida em blocos `try-catch`:
- Identifica e trata o tipo específico de erro.
- Exibe mensagens apropriadas ao usuário.

---

## **Funcionamento geral**
### **Fluxo básico**
1. As contas são instanciadas com valores iniciais.
2. Operações de saque, depósito e transferência são realizadas de acordo com as regras.
3. Erros (como saldo insuficiente ou valor inválido) são tratados por exceções.
4. O comportamento específico de cada tipo de conta é definido por suas classes especializadas.

---

### **Exemplo de uso do sistema**
1. Uma `ContaCorrente` tenta sacar R$3000:
   - Lança `SaldoInsuficienteException`.
2. Uma `ContaCorrente` tenta sacar R$30:
   - Lança `MultiploDeVinteException` (valor não é múltiplo de 20).
3. Após depósitos e transferências, os saldos são atualizados corretamente.

---
## Como o programa sabe qual exceção especifica deve ser tratada? 

O programa sabe qual exceção específica deve ser tratada devido ao **mecanismo de hierarquia de exceções** e o uso de blocos `try-catch`. Vamos detalhar como isso funciona no Java:

---

### **1. Lançamento de Exceções**
Quando um método, como o `sacar()` da classe `Conta`, detecta que uma regra foi violada (por exemplo, saldo insuficiente), ele **lança uma exceção específica** usando a palavra-chave `throw`. 

Exemplo:

```java
if (valor > saldo) {
    throw new SaldoInsuficienteException("Saldo insuficiente.");
}
if (valor > 300) {
    throw new LimiteSaqueException("Valor de saque superior ao limite máximo de R$300.");
}
if (valor % 20 != 0) {
    throw new MultiploDeVinteException("Valor do saque não pode ser composto por cédulas de R$20.");
}
```

Cada uma dessas condições lança uma exceção específica com uma mensagem. O método então para sua execução e "sinaliza" que algo deu errado.

---

### **2. Tratamento de Exceções no `try-catch`**
Na classe de teste (`TesteBanco`), os métodos que podem lançar exceções são chamados dentro de um bloco `try-catch`:

```java
try {
    cc.sacar(3000.0); // Tenta realizar o saque
} catch (SaldoInsuficienteException e) { // Captura a exceção de saldo insuficiente
    System.out.println("Erro no saque: " + e.getMessage());
} catch (LimiteSaqueException e) { // Captura a exceção de limite de saque
    System.out.println("Erro no saque: " + e.getMessage());
} catch (MultiploDeVinteException e) { // Captura a exceção de múltiplo de 20
    System.out.println("Erro no saque: " + e.getMessage());
}
```

---

### **3. Como o Java Escolhe Qual Exceção Capturar**
1. Quando uma exceção é lançada, o Java **procura um bloco `catch` compatível** para capturá-la.
   - Compatível significa que o tipo da exceção lançada corresponde ao tipo especificado no bloco `catch`.

2. A correspondência é feita **de cima para baixo**, na ordem em que os blocos `catch` aparecem.

Exemplo:
- Se o saque tenta sacar um valor não múltiplo de 20:
  - O método `sacar()` lança a exceção `MultiploDeVinteException`.
  - O Java procura nos blocos `catch`:
    1. Primeiro verifica `SaldoInsuficienteException` — não é compatível.
    2. Depois verifica `LimiteSaqueException` — também não é compatível.
    3. Por fim, encontra `MultiploDeVinteException` — que é compatível.
  - A exceção é capturada, e o código dentro desse bloco `catch` é executado.

---

### **4. Hierarquia de Exceções**
No Java, as exceções são organizadas em uma hierarquia de classes. Todas as exceções personalizadas no programa (`SaldoInsuficienteException`, `LimiteSaqueException`, `MultiploDeVinteException`) são subclasses de `Exception`.

Isso permite que um bloco `catch` genérico capture múltiplos tipos de exceções:

```java
catch (Exception e) {
    System.out.println("Erro genérico: " + e.getMessage());
}
```

- Nesse caso, **qualquer exceção** que seja uma subclasse de `Exception` será capturada.
- No entanto, esse tipo de bloco deve ser usado com cuidado porque pode impedir o tratamento específico das exceções.

---

### **5. Boa Prática: Ordem dos Blocos `catch`**
Os blocos `catch` devem ser ordenados do mais específico para o mais genérico. Por exemplo:

```java
try {
    cc.sacar(3000.0);
} catch (SaldoInsuficienteException e) {
    System.out.println("Erro no saque: " + e.getMessage());
} catch (Exception e) { // Genérico para capturar outros erros
    System.out.println("Erro desconhecido: " + e.getMessage());
}
```

Se você colocar um bloco genérico antes de um específico, o genérico sempre será executado, e o bloco específico será ignorado.

---

### **Resumo do Processo**
1. O método lança uma exceção específica (`throw`).
2. O Java identifica a exceção lançada e percorre os blocos `catch` em ordem.
3. O primeiro bloco `catch` compatível captura a exceção e executa seu código.
4. Caso não haja nenhum bloco compatível, o programa encerra com um erro (`Uncaught Exception`).

Isso torna o tratamento de erros robusto e adaptável a diferentes cenários no sistema bancário.

##
## Então o JAVA identifica se o teste que eu estou querendo fazer é compativel com uma exceção especifica e se for ele percorre todas as outras até encontrar a que se encaixa na situação? por exemplo: estou fazendo um teste de saque com valor maior do que eu tenho de saldo, ai no caso ele percorre todas as exceções até encontrar SaldoInsuficienteException e o catch é executado?

Quase isso! Vou detalhar para esclarecer melhor o funcionamento:

---

### **Funcionamento Exato**
1. Quando você tenta executar o saque com um valor maior do que o saldo disponível:
   - O método `sacar()` avalia a condição:
     ```java
     if (valor > saldo) {
         throw new SaldoInsuficienteException("Saldo insuficiente.");
     }
     ```
   - Como a condição é verdadeira (valor do saque maior que o saldo), **essa exceção específica é lançada**:
     ```java
     throw new SaldoInsuficienteException("Saldo insuficiente.");
     ```

2. A exceção é lançada no método, interrompendo sua execução normal, e o controle do programa vai para o **bloco `try-catch`** correspondente.

3. Dentro do `try-catch`:
   - O Java **não percorre todas as exceções disponíveis do programa**.
   - Ele compara a **classe da exceção lançada** (`SaldoInsuficienteException`) com as exceções declaradas em cada `catch`, **na ordem em que aparecem**.

4. **Primeira correspondência encontrada:**
   - Se a exceção lançada for compatível com a exceção declarada em um bloco `catch`, o código dentro desse bloco é executado, e o Java **não verifica os próximos blocos**.
   - Nesse caso, o bloco correspondente a `SaldoInsuficienteException` é executado:
     ```java
     catch (SaldoInsuficienteException e) {
         System.out.println("Erro no saque: " + e.getMessage());
     }
     ```

5. **Exceções não correspondentes são ignoradas.**
   - Por exemplo, o bloco `catch` para `LimiteSaqueException` e `MultiploDeVinteException` é ignorado porque essas exceções não são compatíveis com a lançada.

---

### **O Java Não "Testa" Todas as Exceções**
O Java **não testa todas as exceções possíveis no programa**. Ele simplesmente verifica os blocos `catch` na ordem em que aparecem e para na primeira correspondência com a exceção lançada.

- Se a exceção for **compatível** com mais de um bloco `catch` (por exemplo, em caso de herança), o primeiro bloco compatível será executado.
- Se nenhum bloco `catch` for compatível, o programa encerra com um erro de exceção não tratada.

---

### **Exemplo do Fluxo no Seu Caso**
Se o valor do saque for maior que o saldo, o método lança a exceção `SaldoInsuficienteException`. A execução vai para o bloco `try-catch` no código de teste:

```java
try {
    cc.sacar(3000.0); // Tenta sacar R$3000
} catch (SaldoInsuficienteException e) { 
    System.out.println("Erro no saque: " + e.getMessage()); // Este bloco é executado
} catch (LimiteSaqueException e) { 
    // Ignorado, pois a exceção não é LimiteSaqueException
} catch (MultiploDeVinteException e) { 
    // Ignorado, pois a exceção não é MultiploDeVinteException
}
```

Como a exceção lançada é do tipo `SaldoInsuficienteException`, apenas o **primeiro bloco `catch`** compatível é executado. O Java não verifica os outros blocos depois disso.

---

### **Resumo**
1. **Exceção lançada** no método (por exemplo, `SaldoInsuficienteException`).
2. O Java verifica os blocos `catch` **na ordem em que aparecem** no código.
3. O **primeiro bloco compatível** com a exceção lançada é executado.
4. Outros blocos `catch` são ignorados.
5. Se nenhum bloco `catch` for compatível, ocorre um erro de exceção não tratada (`Unhandled Exception`).

Portanto, no seu exemplo, o Java **não percorre "todas as exceções disponíveis" no programa, mas apenas as listadas no `try-catch` onde a exceção foi lançada**.

##
## Então ele identifica qual exceção é a mais compatível com o caso e executa o bloco try catch dela, depois ele nao verifica mais nenhuma caso a primeira por exemplo já for compatível, ela é lançada e o programa encerra?

Exatamente, com uma pequena correção: **o programa não encerra** quando a primeira exceção é tratada; ele continua a execução normalmente após o bloco `try-catch`.

### Como funciona:
1. **Exceção lançada no método**:
   - No exemplo do saque:
     ```java
     if (valor > saldo) {
         throw new SaldoInsuficienteException("Saldo insuficiente.");
     }
     ```
     A exceção `SaldoInsuficienteException` é lançada.

2. **Busca de correspondência no bloco `try-catch`**:
   - O Java verifica os blocos `catch` **na ordem em que aparecem no código**.
   - O primeiro `catch` que **corresponde ao tipo da exceção lançada** é executado.
   - Se `SaldoInsuficienteException` foi lançada:
     - O Java verifica o primeiro bloco `catch`.
     - Se for compatível (`catch (SaldoInsuficienteException e)`), esse bloco é executado.
     - O restante dos blocos `catch` é **ignorado**.

3. **Execução do bloco `catch`**:
   - O código dentro do bloco correspondente é executado:
     ```java
     catch (SaldoInsuficienteException e) {
         System.out.println("Erro no saque: " + e.getMessage());
     }
     ```

4. **Continuação do programa**:
   - Após a execução do bloco `catch`, o programa **continua normalmente** a partir da linha seguinte ao bloco `try-catch`.

---

### O Programa Não Encerra Após Tratar a Exceção

Por exemplo, no seu código:
```java
try {
    cc.sacar(3000.0); // Tentativa de saque com saldo insuficiente
} catch (SaldoInsuficienteException e) { 
    System.out.println("Erro no saque: " + e.getMessage()); // Este bloco é executado
} catch (LimiteSaqueException e) { 
    // Ignorado
} catch (MultiploDeVinteException e) { 
    // Ignorado
}
System.out.println("Continuando a execução do programa...");
```

Se o método `sacar()` lançar `SaldoInsuficienteException`:
- O bloco `catch (SaldoInsuficienteException e)` será executado.
- Após o bloco `catch`, a linha `System.out.println("Continuando a execução do programa...");` será executada.

O programa **não encerra**, a menos que:
- Não haja nenhum bloco `catch` compatível com a exceção lançada.
- Nesse caso, o programa será encerrado com um erro de **exceção não tratada**.

---

### Cenário de Exceção Não Tratada
Se a exceção lançada não corresponder a nenhum bloco `catch`, o programa será encerrado com um erro, e uma mensagem de "Exceção não tratada" será exibida no console. Exemplo:

```java
try {
    cc.sacar(3000.0); // Tentativa de saque
} catch (LimiteSaqueException e) { 
    System.out.println("Erro de limite: " + e.getMessage());
}
```

- Aqui, se `SaldoInsuficienteException` for lançada, não haverá nenhum bloco `catch` compatível.
- O programa exibirá um **erro de runtime** e será encerrado.

---

### Conclusão
- O Java verifica os blocos `catch` na ordem.
- O **primeiro compatível** é executado, e os demais são ignorados.
- Após tratar a exceção, o programa **continua a execução normalmente**.
- O programa só encerra se a exceção lançada **não for tratada** por nenhum bloco `catch`.

##
## Qual a finalidade do PrintStackTrace() presente nas exceções do java  e ele funciona apenas com exceções genéricas como NullPointerException e Arithmetic Exception ou funciona também com exceções criadas pelo desenvolvedor? 

### **Finalidade do `printStackTrace()`**

O método `printStackTrace()` é utilizado para **imprimir o rastreamento da pilha de execução** (stack trace) no momento em que a exceção foi lançada. Ele fornece informações detalhadas sobre:

1. **A classe da exceção lançada**: Qual foi o tipo da exceção (por exemplo, `NullPointerException` ou uma exceção personalizada).
2. **Mensagem associada**: A mensagem passada para o construtor da exceção, caso exista.
3. **A cadeia de chamadas (stack trace)**:
   - Quais métodos foram chamados, em que sequência, até a linha onde a exceção foi lançada.
   - Cada chamada inclui o nome da classe, do método e a linha do código-fonte onde ocorreu.

Isso ajuda a identificar **o ponto exato do erro** e o caminho que levou à falha, sendo muito útil para depuração.

---

### **Funciona com Exceções Personalizadas?**

Sim, o `printStackTrace()` funciona com qualquer exceção, incluindo:
1. **Exceções pré-definidas do Java**, como `NullPointerException`, `ArithmeticException`, etc.
2. **Exceções criadas pelo desenvolvedor**, como as suas (`SaldoInsuficienteException`, `LimiteSaqueException`, etc.).

Isso ocorre porque o método `printStackTrace()` está definido na classe base `java.lang.Throwable`, que é a superclasse de todas as exceções em Java. Qualquer classe de exceção personalizada que você criar herda automaticamente esse método.

---

### **Como ele é usado?**

Normalmente, o `printStackTrace()` é chamado dentro de um bloco `catch` para depuração, como no exemplo:

```java
try {
    // Código que pode lançar uma exceção
    cc.sacar(3000.0);
} catch (SaldoInsuficienteException e) {
    // Imprime o rastreamento da pilha da exceção
    e.printStackTrace();
}
```

Se `SaldoInsuficienteException` for lançada, o seguinte será exibido no console:

```
Banco.SaldoInsuficienteException: Saldo insuficiente.
    at Banco.Conta.sacar(Conta.java:27)
    at Banco.TesteBanco.main(TesteBanco.java:25)
```

Nesse exemplo:
- A mensagem `Saldo insuficiente.` é a mensagem passada no construtor da exceção.
- As linhas indicam:
  - Onde a exceção foi lançada (`Conta.java:27`).
  - Qual método estava sendo executado no momento (`TesteBanco.main` na linha `25`).

---

### **Resumo**

- `printStackTrace()` é usado para depuração, exibindo detalhes da exceção lançada e do caminho até o erro.
- Ele funciona tanto com exceções nativas do Java quanto com exceções personalizadas.
- Embora útil, deve ser substituído por logs estruturados em sistemas mais complexos ou em produção.

##
## Porque no JAVA as Exceções são classificadas como classes

No Java, **exceções são classificadas como classes** porque o sistema de tratamento de exceções segue um modelo orientado a objetos (OO), que é o paradigma central da linguagem.

---

### **1. Exceções São Objetos**
Em Java, as exceções são representadas como objetos que herdam da classe base `java.lang.Throwable`. Isso significa que, quando uma exceção é lançada, um **objeto da classe correspondente** é criado, contendo todas as informações necessárias sobre o erro.

### **2. Herança e Reutilização**
A classificação das exceções como classes permite que elas se beneficiem do mecanismo de **herança**, um dos pilares da OO. Isso traz várias vantagens:
- **Hierarquia de exceções**:
  - Todas as exceções derivam de `Throwable`, o que cria uma estrutura organizada.
  - Há subtipos de `Throwable`:
    - `Error`: Para erros sérios que o programa não pode recuperar (ex.: `OutOfMemoryError`).
    - `Exception`: Para condições de erro que podem ser tratadas.
  - `RuntimeException` é uma subclasse de `Exception`, usada para exceções em tempo de execução (ex.: `NullPointerException`).
- **Especialização**:
  - Desenvolvedores podem criar suas próprias exceções personalizadas simplesmente estendendo uma classe existente.

Exemplo:
```java
public class MinhaExcecao extends Exception {
    public MinhaExcecao(String mensagem) {
        super(mensagem);
    }
}
```

Isso facilita a criação de exceções específicas para problemas de domínio (ex.: `SaldoInsuficienteException` no seu código).

---

### **3. Encapsulamento de Informações**
Como classes, as exceções podem encapsular dados e comportamentos relevantes:
- **Atributos**:
  - Uma exceção pode armazenar informações adicionais sobre o erro (ex.: saldo insuficiente, valor do saque, etc.).
- **Métodos**:
  - Métodos como `getMessage()`, `toString()` e `printStackTrace()` fornecem informações detalhadas sobre a exceção.
- Desenvolvedores podem adicionar métodos personalizados em suas exceções.

Exemplo:
```java
public class SaldoInsuficienteException extends Exception {
    private double saldoAtual;

    public SaldoInsuficienteException(String mensagem, double saldoAtual) {
        super(mensagem);
        this.saldoAtual = saldoAtual;
    }

    public double getSaldoAtual() {
        return saldoAtual;
    }
}
```

---

### **4. Polimorfismo**
Graças à classificação como classes, exceções podem ser tratadas usando o **polimorfismo**, simplificando o código de tratamento de erros:
- Um `catch` pode capturar exceções de uma classe base ou subclasses.

Exemplo:
```java
try {
    // Código que pode lançar várias exceções
} catch (Exception e) { // Captura qualquer exceção que seja uma subclasse de Exception
    System.out.println("Erro genérico: " + e.getMessage());
}
```

Essa abordagem é flexível:
- Permite tratar exceções específicas (`SaldoInsuficienteException`).
- Ou exceções mais amplas (`Exception`).

---

### **5. Extensibilidade**
Como exceções são classes, o sistema é facilmente extensível:
- Desenvolvedores podem adicionar novas exceções sem modificar o código existente.
- Isso segue o princípio de **aberto para extensão, fechado para modificação**.

---

### **6. Integração com o Modelo OO**
O Java é uma linguagem totalmente orientada a objetos. Fazer das exceções classes:
- Garante que o tratamento de erros esteja alinhado com os princípios de design da linguagem.
- Promove consistência e uniformidade no código.

---

### **Resumo das Vantagens**
- **Organização**: Exceções são organizadas em uma hierarquia lógica.
- **Reutilização**: Aproveitam herança e polimorfismo.
- **Flexibilidade**: Permite criar exceções personalizadas.
- **Encapsulamento**: Permite armazenar dados relevantes sobre o erro.
- **Uniformidade**: Segue os princípios da orientação a objetos, em harmonia com o restante do Java.

##
## Explique mais detalhadamente a Hierarquia das Exceções Java 

A hierarquia das exceções em Java é estruturada na classe base **`java.lang.Throwable`**, que serve como a raiz de todas as condições de erro ou eventos excepcionais que podem ser lançados e capturados. A partir dela, a hierarquia se divide em duas principais categorias:

1. **Erros (`Error`)**: Problemas sérios que normalmente não podem ser tratados pelo programa.
2. **Exceções (`Exception`)**: Problemas que podem ser tratados durante a execução do programa.

Abaixo, uma explicação detalhada dessa hierarquia:

---

### **1. Raiz da Hierarquia: `Throwable`**
- **`Throwable`** é a superclasse de todas as classes de exceções e erros.
- Apenas instâncias de `Throwable` (ou suas subclasses) podem ser lançadas pelo comando `throw`.
- Contém os métodos essenciais para manipular exceções e erros:
  - **`getMessage()`**: Retorna a mensagem associada à exceção.
  - **`printStackTrace()`**: Imprime o rastreamento da pilha para depuração.
  - **`getCause()`**: Retorna a causa da exceção, se houver.

```java
public class Throwable {
    private String message;
    private Throwable cause;

    public String getMessage() { /* retorna mensagem */ }
    public void printStackTrace() { /* imprime rastreamento */ }
    public Throwable getCause() { /* retorna causa */ }
}
```

---

### **2. Subclasses de `Throwable`**
#### A hierarquia se divide em:
- **`Error`**: Representa erros sérios no ambiente de execução.
- **`Exception`**: Representa condições excepcionais que o programa pode tratar.

---

### **3. Classe `Error`**
- **Descrição**: Representa problemas graves no ambiente de execução que normalmente não podem ser tratados pelo programa.
- **Exemplos**:
  - `OutOfMemoryError`: Lançado quando a JVM não consegue alocar memória.
  - `StackOverflowError`: Lançado quando ocorre um estouro de pilha (geralmente devido à recursão infinita).
  - `InternalError`: Problemas internos graves na JVM.
- **Uso**: Raramente se deve capturar um `Error`, pois ele indica falhas críticas que não são corrigíveis pelo programa.

```java
try {
    // Código que pode gerar erros graves
} catch (Error e) {
    System.out.println("Erro crítico: " + e.getMessage());
}
```

---

### **4. Classe `Exception`**
- **Descrição**: Representa condições excepcionais que o programa pode prever e tratar.
- Subdivide-se em:
  - **Exceções verificadas (`Checked Exceptions`)**.
  - **Exceções não verificadas (`Unchecked Exceptions`)**.

---

#### **4.1 Exceções Verificadas (`Checked Exceptions`)**
- **Características**:
  - Devem ser declaradas no método que as lança usando `throws`.
  - O compilador força o desenvolvedor a tratá-las, garantindo que o programa lide com condições que podem ocorrer.
- **Exemplos**:
  - `IOException`: Erros relacionados a operações de entrada/saída (ex.: falha ao abrir um arquivo).
  - `SQLException`: Problemas com operações em bancos de dados.
  - `ClassNotFoundException`: Classe requisitada não encontrada durante o tempo de execução.
- **Uso**:
  - Úteis para problemas que o programa pode prever e tratar adequadamente.

```java
public void lerArquivo(String caminho) throws IOException {
    FileReader file = new FileReader(caminho);
    file.read();
}
```

---

#### **4.2 Exceções Não Verificadas (`Unchecked Exceptions`)**
- **Características**:
  - Não precisam ser declaradas no método que as lança.
  - São subclasses de `RuntimeException`.
  - Indicam erros de lógica do programa, como cálculos inválidos ou referências nulas.
- **Exemplos**:
  - `NullPointerException`: Tentativa de acessar um objeto que é `null`.
  - `ArrayIndexOutOfBoundsException`: Índice fora dos limites de um array.
  - `ArithmeticException`: Divisão por zero.
  - `IllegalArgumentException`: Argumento inválido passado a um método.
- **Uso**:
  - Geralmente indicam falhas de lógica que devem ser corrigidas pelo desenvolvedor durante o desenvolvimento.

```java
public void dividir(int a, int b) {
    if (b == 0) {
        throw new ArithmeticException("Divisão por zero.");
    }
    System.out.println(a / b);
}
```

---

### **5. `RuntimeException`: O divisor de águas**
- É uma subclasse de `Exception`, mas suas subclasses são **não verificadas**.
- Exemplos: `NullPointerException`, `IllegalStateException`, `NumberFormatException`.
- **Por que são não verificadas?**
  - Essas exceções frequentemente resultam de erros de programação, como acessar um array fora dos limites ou desreferenciar um objeto nulo. O desenvolvedor deve corrigi-las em vez de tratá-las diretamente.

---

### **6. Exceções Personalizadas**
- Um desenvolvedor pode criar exceções personalizadas estendendo `Exception` (verificada) ou `RuntimeException` (não verificada), dependendo do comportamento desejado.
- Exemplo:
```java
public class SaldoInsuficienteException extends Exception {
    public SaldoInsuficienteException(String mensagem) {
        super(mensagem);
    }
}
```

---

### **7. Hierarquia Completa de `Throwable`**
Aqui está um resumo em forma de diagrama:

```
Throwable
├── Error (ex.: OutOfMemoryError, StackOverflowError)
└── Exception
    ├── RuntimeException (ex.: NullPointerException, ArithmeticException)
    └── Checked Exceptions (ex.: IOException, SQLException)
```

---

### **Resumo**
1. **`Throwable`** é a raiz de todas as exceções e erros.
2. **`Error`** indica problemas graves na JVM, que não devem ser tratados.
3. **`Exception`** é para condições excepcionais tratáveis, subdividida em:
   - **Checked Exceptions**: Precisam ser tratadas ou declaradas no método.
   - **Unchecked Exceptions**: Resultam de erros de programação e não precisam ser declaradas.
4. Exceções personalizadas podem ser criadas para atender requisitos específicos.

Essa estrutura facilita a manipulação de erros, promovendo robustez no código e garantindo que falhas sejam tratadas ou evitadas adequadamente.


##
## Qual a diferença entre Throws e Throw?

### **1. `throws`**
- **Descrição**: 
  O **`throws`** é usado na **declaração do método** para informar que ele pode lançar uma ou mais exceções verificadas. É uma promessa ao compilador de que o método chamador deve lidar com essas exceções.
  
- **Onde é usado**: 
  Na assinatura de um método, seguido pela lista de exceções que ele pode lançar.

- **Finalidade**:
  - Serve para **documentar** as possíveis exceções que o método pode lançar.
  - Obriga o método chamador a tratar ou propagar essas exceções.

- **Exemplo**:
```java
public void abrirArquivo(String caminho) throws IOException {
    FileReader file = new FileReader(caminho);
    file.read();
}
```
- No exemplo acima:
  - O método `abrirArquivo` declara que pode lançar uma `IOException`.
  - Quem chamar esse método será obrigado a tratá-la ou declará-la novamente.

```java
try {
    abrirArquivo("arquivo.txt");
} catch (IOException e) {
    System.out.println("Erro ao abrir o arquivo: " + e.getMessage());
}
```

---

### **2. `throw`**
- **Descrição**:
  O **`throw`** é usado dentro de um método ou bloco de código para **lançar** uma exceção específica no momento da execução.

- **Onde é usado**:
  Dentro do corpo do método ou em blocos `try`.

- **Finalidade**:
  - Serve para **lançar uma instância de uma exceção**.
  - Interrompe o fluxo normal do programa e passa a exceção para o mecanismo de tratamento.

- **Exemplo**:
```java
public void sacar(double valor) {
    if (valor > saldo) {
        throw new IllegalArgumentException("Saldo insuficiente para o saque.");
    }
    saldo -= valor;
}
```
- No exemplo acima:
  - A exceção `IllegalArgumentException` é **lançada** com a palavra-chave `throw` se o valor do saque for maior que o saldo.

---

### **Diferença Principal**
| **Aspecto**          | **`throws`**                                  | **`throw`**                            |
|-----------------------|-----------------------------------------------|----------------------------------------|
| **Finalidade**        | Declara que um método pode lançar exceções.  | Lança efetivamente uma exceção.        |
| **Local de uso**      | Na assinatura de métodos.                    | Dentro do corpo de um método ou bloco. |
| **Requer instância?** | Não. É apenas uma declaração.                | Sim. Lança uma instância de exceção.   |

---

### **Uso Combinado**
Geralmente, `throw` e `throws` são usados juntos. Por exemplo:
```java
public void verificarIdade(int idade) throws IllegalArgumentException {
    if (idade < 18) {
        throw new IllegalArgumentException("Idade insuficiente para cadastro.");
    }
}
```
- **`throws`** declara que o método pode lançar `IllegalArgumentException`.
- **`throw`** lança efetivamente a instância da exceção se a idade for inválida.

---

### **Resumo**
- Use **`throws`** para informar que um método pode lançar exceções.
- Use **`throw`** para realmente lançar uma exceção durante a execução do programa.


##
## Aonde fica o "SUPER" do message dessa exceção? 

```
package Banco;
public class LimiteSaqueException extends Exception {
    public LimiteSaqueException(String message) {
        super(message);
    }
}
```

O **`super(message)`** da classe `LimiteSaqueException` está chamando o construtor da classe **superclasse**, que no caso é a classe **`Exception`**. Isso ocorre porque a classe `LimiteSaqueException` estende a classe `Exception`, e toda classe filha tem acesso ao construtor da classe mãe usando a palavra-chave **`super`**.

### Onde o `super(message)` é processado?

1. **No construtor da classe `Exception`**:
   A classe `Exception` tem um construtor que aceita uma string como parâmetro:
   ```java
   public Exception(String message) {
       super(message);
   }
   ```
   Aqui, o **`super(message)`** invoca o construtor de sua própria classe mãe, que é **`Throwable`**.

2. **No construtor da classe `Throwable`**:
   A classe `Throwable` (que é a ancestral de todas as exceções em Java) tem um construtor semelhante:
   ```java
   public Throwable(String message) {
       this.detailMessage = message;
   }
   ```
   Nesse ponto, o valor de **`message`** é armazenado no campo interno **`detailMessage`** da classe `Throwable`. Esse campo é usado por métodos como **`getMessage()`**, que retorna a mensagem de erro associada à exceção.

---

### Fluxo completo:
1. Quando o construtor de `LimiteSaqueException` é chamado com uma string, ele passa essa string para o **construtor da superclasse** com `super(message)`.
2. O construtor de `Exception` (superclasse direta) é executado.
3. O construtor de `Exception` repassa a string para o construtor de `Throwable` (ancestral de todas as exceções).
4. O construtor de `Throwable` armazena a string no campo `detailMessage`.

---

### Exemplo em ação:
```java
public class LimiteSaqueException extends Exception {
    public LimiteSaqueException(String message) {
        super(message); // Passa a mensagem para Exception -> Throwable
    }
}
```

Teste:
```java
public class Teste {
    public static void main(String[] args) {
        try {
            throw new LimiteSaqueException("Você não pode sacar mais que R$300.");
        } catch (LimiteSaqueException e) {
            System.out.println(e.getMessage()); // Saída: Você não pode sacar mais que R$300.
        }
    }
}
```

---

### Resumo
O **`super(message)`**:
- Faz a chamada ao construtor da classe mãe (no caso, `Exception`).
- O valor da mensagem percorre a hierarquia até a classe `Throwable`, onde é armazenado no campo `detailMessage`.
- Esse campo é usado para exibir ou recuperar a mensagem da exceção via métodos como `getMessage()` ou `printStackTrace()`.


##
## Para que serve o "FINALLY" e ele poderia ser usado nesse código do projeto sistema bancário? faria alguma diferença?

O **`finally`** é um bloco opcional usado em conjunto com `try-catch` no Java. Sua finalidade principal é garantir que determinado trecho de código seja executado **sempre**, independentemente de uma exceção ter sido lançada ou não. Ele é amplamente utilizado para liberar recursos, fechar conexões ou executar ações que devem ocorrer em qualquer circunstância.

---

### **Características do `finally`**
1. **Execução Garantida**:
   - O bloco `finally` sempre será executado, mesmo que:
     - Nenhuma exceção seja lançada.
     - Uma exceção seja lançada e capturada.
     - Uma exceção seja lançada e não capturada.
     - Um `return` seja usado no bloco `try` ou `catch`.

2. **Finalidade Comum**:
   - Fechar arquivos ou fluxos de dados.
   - Liberar conexões com banco de dados.
   - Garantir a limpeza de recursos.

---

### **Estrutura Geral**
```java
try {
    // Código que pode lançar uma exceção
} catch (Exception e) {
    // Tratamento de exceção
} finally {
    // Código que será executado sempre
}
```

---

### **Uso no Projeto "Sistema Bancário"**
O bloco `finally` poderia ser incluído para **garantir que certos recursos sejam sempre liberados ou certas ações sejam executadas**, como:
- Registrar logs de operações, mesmo em caso de erro.
- Garantir que mensagens de transações ou erros sejam finalizadas.
- Executar ações específicas após uma tentativa de operação (como notificar usuários ou limpar buffers).

#### **Exemplo de Aplicação no Projeto**
No método de saque ou transferência, o bloco `finally` poderia ser usado para garantir o registro da tentativa, mesmo que ela falhe.

```java
try {
    cc.sacar(3000.0); // Tenta sacar um valor
    System.out.println("Saque realizado com sucesso!");
} catch (SaldoInsuficienteException e) {
    System.out.println("Erro no saque: " + e.getMessage());
} catch (LimiteSaqueException e) {
    System.out.println("Erro no saque: " + e.getMessage());
} catch (MultiploDeVinteException e) {
    System.out.println("Erro no saque: " + e.getMessage());
} finally {
    // Sempre executado, mesmo que o saque falhe
    System.out.println("Operação de saque finalizada.");
}
```

**Saída possível:**
1. Se o saque for bem-sucedido:
   ```
   Saque realizado com sucesso!
   Operação de saque finalizada.
   ```

2. Se o saque falhar:
   ```
   Erro no saque: Saldo insuficiente.
   Operação de saque finalizada.
   ```

---

### **Vantagens do Uso no Projeto**
1. **Manutenção e Controle**:
   - Centraliza ações que devem ser garantidas em todas as operações, como logs e mensagens.
   
2. **Resiliência do Sistema**:
   - Recursos (como conexões de banco de dados ou arquivos) são sempre liberados, evitando problemas de recursos não utilizados.

---

### **Faria Diferença no Sistema Bancário?**
Depende da lógica que se deseja implementar:
- **Sim, se houver recursos que precisem ser liberados ou ações adicionais que devem ocorrer sempre.**
  - Por exemplo, registrar transações em um log ou banco de dados, mesmo que elas falhem.
  - Garantir que mensagens ou notificações sejam enviadas após operações de saque, depósito ou transferência.
- **Não, se todas as ações necessárias já estiverem contempladas nos blocos `try` ou `catch`.**

---

### **Conclusão**
O bloco `finally` é útil para **ações que devem ocorrer incondicionalmente**. No projeto "Sistema Bancário", ele pode ser usado para melhorar a robustez e a manutenção do sistema, como no registro de logs ou na liberação de recursos. Embora não seja estritamente necessário no código atual, sua inclusão pode aumentar a clareza e a confiabilidade em cenários mais complexos.
