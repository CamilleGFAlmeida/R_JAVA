
---

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

Esse sistema exemplifica como estruturar um projeto modular, reutilizável e robusto usando conceitos de **POO**, **interfaces**, e **exceções personalizadas** em Java.