import sqlite3

conexao = sqlite3.connect('suporte.db')
cursor = conexao.cursor()

cursor.execute('''
CREATE TABLE IF NOT EXISTS chamados (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    usuario TEXT,
    suporte TEXT,
    mensagem TEXT,
    resposta TEXT
)
''')

conexao.commit()

def enviar_mensagem():
    usuario = input("Digite seu nome: ")
    mensagem = input("Digite sua dúvida ou problema: ")

    cursor.execute("""
    INSERT INTO chamados (usuario, mensagem)
    VALUES (?, ?)
    """, (usuario, mensagem))
    
    conexao.commit()
    print("Mensagem enviada com sucesso!")

def responder_mensagem():
    cursor.execute("SELECT * FROM chamados WHERE resposta IS NULL")
    mensagens = cursor.fetchall()

    if not mensagens:
        print("Nenhuma mensagem pendente.")
        return

    for msg in mensagens:
        print(f"\nID: {msg[0]}")
        print(f"Usuário: {msg[1]}")
        print(f"Mensagem: {msg[3]}")

        resposta = input("Digite a resposta: ")

        cursor.execute("""
        UPDATE chamados
        SET resposta = ?, suporte = ?
        WHERE id = ?
        """, (resposta, "Admin", msg[0]))

        conexao.commit()

def visualizar_respostas():
    usuario = input("Seu nome: ")

    cursor.execute("""
    SELECT mensagem, resposta FROM chamados
    WHERE usuario = ?
    """, (usuario,))

    dados = cursor.fetchall()

    if not dados:
        print("Nenhuma mensagem encontrada para este usuário.")
        return

    for msg, resposta in dados:
        print(f"\nPergunta: {msg}")
        print(f"Resposta: {resposta if resposta else 'Aguardando resposta...'}")

def menu():
    while True:
        print("\n1. Enviar mensagem")
        print("2. Responder mensagens (suporte)")
        print("3. Ver respostas")
        print("0. Sair")

        opcao = input("Escolha uma opção: ")

        if opcao == '1':
            enviar_mensagem()

        elif opcao == '2':
            responder_mensagem()

        elif opcao == '3':
            visualizar_respostas()
        
        elif opcao == '0':
            print("Encerrando o sistema de suporte.")
        break

menu()
