```
def menu():
  print("\nMENU: \n\n Depósito(d) \n Saque(s) \n Extrato(e) \n Criar Novo Usuário(nu) \n Criar Conta(cc) \n Finalizar o Progama(f)")
  opcao = str(input("\n\nEscolha uma opção: ")).lower()
  return opcao

def deposito(saldo, valor, extrato, /):
  if valor > 0:
    saldo += valor
    extrato += f"Depósito de R${valor:.2f}\n"
    print("O depósito foi bem-sucedido! ")
  else:
    print("Tente novamente..")
  return saldo, extrato

def saque(*, saldo, valor, extrato, numero_saques, limite_saques, limite):
  excedeu_saldo = valor > saldo
  excedeu_limite_saque = numero_saques >= limite_saques
  excedeu_limite = valor > limite

  if excedeu_limite_saque:
    print("O limite de saques por hoje foi atingido, tente novamente amanhã!")
  elif excedeu_saldo:
    print("O valor do seu saque é maior que o saldo existente..")
  elif excedeu_limite:
    print("O seu limite permitido por saque é R$500..")
  elif valor > 0:
    saldo -= valor
    extrato += f"Saque de R${valor:.2f}\n"
    numero_saques += 1
    print("O saque foi bem-sucedido!!")
    print(f"\n\n   Saldo: {saldo:.2f}")
  
  else:
    print("Tente novamente..")
    
  return saldo, extrato, numero_saques

def exibir_extrato(saldo , /, *, extrato):
  print("\n\tTransações Registradas.\n\n")
  print("-=-" * 12)
  print("\tNão há transações..." if not extrato else extrato) 
  print("-=-" * 12)
  print(f"\n\nSaldo existente: R${saldo:.2f}")

def criar_usuario(usuarios):
  nome = input("Digite o seu nome: ")
  data_de_nascimento = input("Digite a sua data de nascimento (dd/mm/aaaa): ")
  endereco = input("Digite o seu endereço (Rua/Cidade): ")
  cpf = input("Digite o seu cpf, sem (-/.): ")

  usuarios.append ({"nome": nome, "Data_de_nascimento": data_de_nascimento, "endereço": endereco, "cpf": cpf})

def filtrar_usuario(cpf, usuarios):
  usuarios_filtrados = [usuario for usuario in usuarios if usuario["cpf"] == cpf]
  return usuarios_filtrados[0] if usuarios_filtrados else None

def criar_conta(cpf, usuarios):
  usuario = filtrar_usuario(cpf, usuarios)

  if usuario:
    print("Conta criada com sucesso!!")
    return {"usuario":usuario}
  else:
    print("Usuário não encontrado..")
    return None

def main():
  saldo = 0
  extrato = ""
  numero_saques = 0
  LIMITE_SAQUES = 3
  LIMITE = 500
  usuarios = []
  contas = []

  while True:
    opcao = menu()

    if opcao == "d":
      deposito_valor = float(input("Digite o valor que deseja depositar: "))
      saldo, extrato = deposito(saldo, deposito_valor, extrato)

    elif opcao == "s":
      saque_valor = float(input("Digite o valor que deseja sacar: "))
      saldo, extrato, numero_saques = saque(
        saldo=saldo,
        valor=saque_valor,
        numero_saques=numero_saques,
        limite_saques=LIMITE_SAQUES,
        limite=LIMITE,
        extrato=extrato,
      )
    
    elif opcao == "e":
      exibir_extrato(saldo, extrato=extrato)

    elif opcao == "nu":
      criar_usuario(usuarios)
    
    elif opcao == "cc":
      cpf = input("Digite o seu cpf: ")
      conta = criar_conta(cpf, usuarios)
      if conta:
        contas.append(conta)

    elif opcao == "f":
      print("Progama encerrado..")
      break
    
    else:
      print("Operação inválida, tente novamente..")

main()
```
