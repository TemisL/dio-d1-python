```

from abc import abstractmethod, ABC

class Conta:
    def __init__(self, numero, cliente):
        self._saldo = 0
        self._numero = numero
        self._agencia = '0001'
        self._cliente = cliente
        self._historico = Historico()
    
    @classmethod
    def nova_conta(cls, numero, cliente):
       return cls(numero, cliente)
    
    @property
    def saldo(self):
       return self._saldo

    @property
    def numero(self):
       return self._numero
    
    @property
    def agencia(self):
       return self._agencia
    
    @property
    def cliente(self):
       return self._cliente

    @property
    def historico(self):
       return self._historico
    
    def sacar(self, valor):

        excedeu_saldo = valor > self._saldo

        if excedeu_saldo:
            print("O valor do seu saque é maior que o saldo existente..")

        elif valor > 0:
           self._saldo -= valor
           print("Saque foi bem sucedido!")
           self._historico.adicionar_transacao(Saque(valor))
           return True
        
        else:
           print("Tente Novamente..")
        
        return False

    def depositar(self, valor):
        if valor > 0:
            self._saldo += valor
            print("Deposito foi bem sucedido!")
            self._historico.adicionar_transacao(Deposito(valor))
            return True
        
        else:
            print("Tente Novamente..")
            return False

class Conta_Corrente(Conta):
    def __init__(self, numero, cliente,limite=500, limite_saques=3):
        super().__init__(numero, cliente)
        self._limite = limite
        self._limite_saques = limite_saques

    def sacar(self, valor):
        numero_saques = len(
            [transacao for transacao in self.historico.transacoes if transacao["tipo"] == "Saque"]
        )
        
        excedeu_limite = valor > self._limite
        excedeu_limite_saques = numero_saques >= self._limite_saques

        if excedeu_limite_saques:
            print("O limite de saques por hoje foi atingido, tente novamente amanhã!")
        elif excedeu_limite:
            print("O seu limite permitido por saque é R$500..")
        else:
            return super().sacar(valor)
        
        return False
    
    def __str__(self):
        return f"""\
            Agência:\t{self.agencia}
            C/C:\t\t{self.numero}
            Titular:\t{self.cliente.nome}
        """

class Historico:
    def __init__(self):
        self._transacoes = []

    @property
    def transacoes(self):
        return self._transacoes
    
    def adicionar_transacao(self, transacao):
        self._transacoes.append({
            "tipo": transacao.__class__.__name__,
            "valor": transacao.valor
        })

class Transacao(ABC):

    @property
    @abstractmethod
    def valor(self):
        pass
    
    @abstractmethod
    def registrar(self, conta):
        pass

class Saque(Transacao):
    def __init__(self, valor):
        self._valor = valor

    @property
    def valor(self):
        return self._valor
    
    def registrar(self, conta):
        conta.sacar(self.valor)

class Deposito(Transacao):
    def __init__(self, valor):
        self._valor = valor

    @property
    def valor(self):
        return self._valor
    
    def registrar(self, conta):
        conta.depositar(self.valor)

class Cliente:
    def __init__(self, endereco):
        self._contas = []
        self.endereco = endereco

    @property
    def contas(self):
        return self._contas
    
    def realizar_transacao(self, conta, transacao):
        transacao.registrar(conta)

    def adicionar_conta(self, conta):
        self._contas.append(conta)

class Pessoa_Fisica(Cliente):
    def __init__(self, cpf, nome, data_nascimento, endereco):
        super().__init__(endereco)
        self.cpf = cpf
        self.nome = nome
        self.data_nascimento = data_nascimento

def menu():
    print("""\n
    \t-=-=-=-=-=-=-Menu-=-=-=-=-=-=-

    \tDepositar(d)
    \tSacar(s)
    \tExtrato(e)
    \tNovo Usuário(nu)
    \tNova Conta(nc)
    \tListar Contas(lc)
    \tFinalizar Progama(f)

""")
    opcao = str(input("Digite uma das opcões seguintes: ")).lower()

    return opcao

def filtrar_cliente(clientes, cpf):
    filtrando_cliente = [cliente for cliente in clientes if cliente.cpf == cpf ]
    return filtrando_cliente[0] if filtrando_cliente else None

def depositar(clientes):
    cpf = input("Digite o seu CPF (APENAS DIGITOS): ")
    cliente = filtrar_cliente(clientes,cpf)

    if not cliente:
        print("Cliente não registrado..")
        return

    valor = float(input("Digite o valor desejado para o depósito: "))
    transacao = Deposito(valor)

    cliente.realizar_transacao(cliente.contas[0], transacao)

def sacar(clientes):
    cpf = input("Digite o seu CPF (APENAS DIGITOS): ")
    cliente = filtrar_cliente(clientes, cpf)

    if not cliente:
        print("Cliente não registrado..")
        return
    
    valor = float(input("Digite o valor do saque: "))
    transacao = Saque(valor)

    cliente.realizar_transacao(cliente.contas[0], transacao)

def exibir_historico(clientes):
    cpf = input("Digite o seu CPF (APENAS DIGITOS): ")
    cliente = filtrar_cliente(clientes, cpf)
    extrato = ""

    if not cliente:
        print("Cliente não registrado..")
        return
    
    conta = cliente.contas[0]
    if not conta:
        return
    
    transacoes = conta.historico.transacoes

    print("-=-=-=-=-=-=-=-EXTRATO-=-=-=-=-=-=-=-")
    if not transacoes:
        extrato = 'Não ocorreram transações na sua conta..'

    else:
        for transacao in transacoes:
            extrato += f"{transacao['tipo']}: \n{transacao['valor']}\n"

    print(extrato)
    print(f"\nSaldo: \nR${conta.saldo}")    
    print("-=-"*10)

def novo_usuario(clientes):
    cpf = input("Digite o seu CPF (APENAS DIGITOS): ")
    cliente = filtrar_cliente(clientes,cpf)

    if cliente:
        print("Já possui usuário criado...")
        return    
    
    nome = str(input("Digite o seu nome completo: "))
    data_nascimento = input("Digite a sua data de nascimento: (dd/mm/aaaa) ")
    endereco = input("Digite o nome do seu bairro/rua e numero: ")

    cliente = Pessoa_Fisica(cpf=cpf, nome=nome, data_nascimento=data_nascimento, endereco=endereco)
    clientes.append(cliente)

    print("\nUsuário criado com sucesso!!")

def nova_conta(numero_contas, clientes, contas):
    cpf = input("Digite o seu CPF (APENAS DIGITOS): ")
    cliente = filtrar_cliente(clientes,cpf)

    if not cliente:
        print("Cliente não registrado..")
        return
    
    conta = Conta_Corrente.nova_conta(numero=numero_contas, cliente=cliente)
    contas.append(conta)
    cliente.contas.append(conta)

def listar_contas(contas):
    for conta in contas:
        print("-=-"*10)
        print(str(conta))

def main():
    clientes = []
    contas = []

    while True:
        opcoes = menu()
        
        if opcoes == "d":
            depositar(clientes)
        
        elif opcoes == "s":
            sacar(clientes)

        elif opcoes == "e":
            exibir_historico(clientes)

        elif opcoes == "nu":
            novo_usuario(clientes)

        elif opcoes == "nc":
            numero_contas = len(contas) + 1
            nova_conta(numero_contas, clientes, contas)

        elif opcoes == "lc":
            listar_contas(clientes)

        elif opcoes == "f":
            break

        else:
            print("Digite uma das opcões seguintes...")
main()


```
