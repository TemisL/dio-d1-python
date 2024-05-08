```
saque = None
saldo = 0
extrato = ""
numero_saques = 0
LIMITE_SAQUES = 3
limite = 500

while True:

print("\n")

print("(s) Ver Saldo \n(d) Depositar \n(q) Sacar \n(e) Ver Extrato \n(f) Sair do Programa")

menu = str(input("Digite uma das opções: ")).lower()

print("\n")

if menu == "s":
print(f"O seu saldo é: R${saldo:.2f} ")

elif menu == "d":
deposito = float(input("Digite o valor que deseja depositar: "))

    if deposito > 0:
      saldo += deposito
      extrato += f"Depósito de R${deposito:.2f}\n"

    else:
      print("Deposite um valor maior que zero...")

elif menu == "q":
saque = float(input("Digite o valor que deseja sacar: "))

    excedeu_saldo = saque > saldo
    excedeu_limite = saque > limite
    excedeu_limite_saques = numero_saques >= LIMITE_SAQUES

    if excedeu_saldo:
      print("Você não possui saldo suficiente para sacar..")

    elif excedeu_limite:
      print("O seu saque ultrapassou o limite, tente novamente.")

    elif excedeu_limite_saques:
      print("A quantidade de vezes permitida para sacar foi excedida..")

    elif saque > 0:
      saldo -= saque
      numero_saques += 1
      extrato += f"Saque de R${saque:.2f}\n"

    else:
      print("Operação falhou, tente novamente.")

elif menu == "e":
print("-=-" _ 10)
print("Histórico de transações na sua conta: \n")
print("Não ocorreram movimentações.." if not extrato else extrato)
print("-=-" _ 10)

elif menu == "f":
break

else:
print("Digite uma das operações indicadas..")
```
