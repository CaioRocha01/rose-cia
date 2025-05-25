# Sistema de Loja de Roupas Femininas com estoque por tamanho

clientes = {}
carrinho = []

produtos_femininos = ['saia', 'vestido', 'blusa', 'calça', 'short', 'bota', 'salto', 'sandália']

produtos = {
    "saia": {
        "P": {"quantidade": 5, "preco": 59.90},
        "M": {"quantidade": 10, "preco": 59.90},
        "G": {"quantidade": 2, "preco": 59.90}
    },
    "vestido": {
        "M": {"quantidade": 3, "preco": 129.90},
        "G": {"quantidade": 5, "preco": 129.90}
    },
    "blusa": {
        "P": {"quantidade": 15, "preco": 39.90},
        "M": {"quantidade": 8, "preco": 39.90}
    }
}

def cadastrar_cliente():
    print("\n--- Cadastre-se ---")
    login = input("Digite o login: ")
    if login == 'admin':
        print("Esse login é reservado para o dono da loja.")
        return
    if login in clientes:
        print("Login já existe. Tente outro.")
        return
    senha = input("Digite a senha: ")
    clientes[login] = senha
    print("Cliente cadastrado com sucesso!")

def fazer_login():
    print("\n--- Login ---")
    login = input("Login: ")
    senha = input("Senha: ")
    if (login == 'admin' and senha == 'admin123') or clientes.get(login) == senha:
        print(f"Bem-vindo(a), {login}!")
        return login
    else:
        print("Login ou senha incorretos.")
        return None

def cadastrar_produto():
    print("\n--- Cadastro de Produto ---")
    nome = input("Nome do produto: ").lower()
    if nome not in produtos_femininos:
        print("Produto não permitido nesta loja feminina!")
        return
    tamanho = input("Tamanho (P/M/G ou número): ").upper()
    quantidade = int(input("Quantidade: "))
    preco = float(input("Preço (R$): "))

    if nome not in produtos:
        produtos[nome] = {}

    if tamanho in produtos[nome]:
        produtos[nome][tamanho]['quantidade'] += quantidade
    else:
        produtos[nome][tamanho] = {"quantidade": quantidade, "preco": preco}

    print("Produto cadastrado/atualizado com sucesso!")

def listar_produtos():
    print("\n--- Produtos Disponíveis ---")
    if not produtos:
        print("Nenhum produto disponível no momento.")
        return
    idx = 1
    for nome, tamanhos in produtos.items():
        for tam, info in tamanhos.items():
            status = " (ESGOTADO)" if info['quantidade'] == 0 else ""
            print(f"{idx}. {nome.capitalize()} - Tam: {tam} - Qtd: {info['quantidade']} - R$ {info['preco']:.2f}{status}")
            idx +=1

def adicionar_ao_carrinho():
    listar_produtos()
    if not produtos:
        return
    nome = input("Digite o nome do produto que deseja: ").lower()
    if nome not in produtos:
        print("Produto não encontrado.")
        return
    tamanhos_disponiveis = [t for t,q in produtos[nome].items() if q['quantidade'] > 0]
    if not tamanhos_disponiveis:
        print("Nenhum tamanho disponível para esse produto.")
        return
    print("Tamanhos disponíveis:", ", ".join(tamanhos_disponiveis))
    tamanho = input("Escolha o tamanho desejado: ").upper()
    if tamanho not in tamanhos_disponiveis:
        print("Tamanho inválido ou indisponível.")
        return
    try:
        qtd = int(input("Quantidade desejada: "))
    except ValueError:
        print("Quantidade inválida.")
        return
    estoque = produtos[nome][tamanho]['quantidade']
    if qtd <= 0:
        print("Quantidade deve ser maior que zero.")
        return
    if qtd > estoque:
        print(f"Quantidade indisponível. Temos apenas {estoque} em estoque.")
        return
    preco = produtos[nome][tamanho]['preco']
    carrinho.append({"nome": nome, "tamanho": tamanho, "quantidade": qtd, "preco": preco})
    produtos[nome][tamanho]['quantidade'] -= qtd
    print(f"{qtd}x {nome} tamanho {tamanho} adicionado ao carrinho.")

def ver_carrinho():
    print("\n--- Carrinho ---")
    if not carrinho:
        print("Carrinho vazio.")
        return
    total = 0
    for idx, item in enumerate(carrinho):
        subtotal = item['quantidade'] * item['preco']
        print(f"{idx+1}. {item['quantidade']}x {item['nome']} (Tam: {item['tamanho']}) - R$ {item['preco']:.2f} - Subtotal: R$ {subtotal:.2f}")
        total += subtotal
    print(f"Total a pagar: R$ {total:.2f}")

def remover_do_carrinho():
    ver_carrinho()
    if not carrinho:
        return
    try:
        escolha = int(input("Digite o número do item que deseja remover: ")) - 1
        if 0 <= escolha < len(carrinho):
            item = carrinho.pop(escolha)
            # Repor estoque
            produtos[item['nome']][item['tamanho']]['quantidade'] += item['quantidade']
            print(f"{item['quantidade']}x {item['nome']} removido do carrinho.")
        else:
            print("Item inválido.")
    except ValueError:
        print("Entrada inválida.")

def finalizar_compra():
    if not carrinho:
        print("Carrinho vazio.")
        return
    ver_carrinho()
    confirm = input("Deseja finalizar a compra? (s/n): ")
    if confirm.lower() == 's':
        print("Compra finalizada com sucesso! Obrigado pela preferência.")
        carrinho.clear()
    else:
        print("Compra não finalizada.")

def menu():
    while True:
        print("\n=== Loja de Roupas Femininas ===")
        print("1. Cadastre-se")
        print("2. Fazer Login")
        print("3. Sair")
        opcao = input("Escolha uma opção: ")
        if opcao == '1':
            cadastrar_cliente()
        elif opcao == '2':
            usuario = fazer_login()
            if usuario:
                menu_cliente(usuario)
        elif opcao == '3':
            print("Saindo da loja. Até mais!")
            break
        else:
            print("Opção inválida.")

def menu_cliente(usuario):
    while True:
        print(f"\n--- Menu ({usuario}) ---")
        if usuario == 'admin':
            print("1. Cadastrar Produto")
            print("2. Listar Produtos")
            print("3. Logout")
            opcao = input("Escolha uma opção: ")
            if opcao == '1':
                cadastrar_produto()
            elif opcao == '2':
                listar_produtos()
            elif opcao == '3':
                print("Logout efetuado.")
                break
            else:
                print("Opção inválida.")
        else:
            print("1. Listar Produtos")
            print("2. Adicionar Produto ao Carrinho")
            print("3. Ver Carrinho")
            print("4. Remover Produto do Carrinho")
            print("5. Finalizar Compra")
            print("6. Logout")
            opcao = input("Escolha uma opção: ")
            if opcao == '1':
                listar_produtos()
            elif opcao == '2':
                adicionar_ao_carrinho()
            elif opcao == '3':
                ver_carrinho()
            elif opcao == '4':
                remover_do_carrinho()
            elif opcao == '5':
                finalizar_compra()
            elif opcao == '6':
                print("Logout efetuado.")
                break
            else:
                print("Opção inválida.")

menu()
