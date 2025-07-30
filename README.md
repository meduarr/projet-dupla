# projet-dupla

Este projeto foi realizado por mim e pelo Gabriel durante o primeiro período da faculdade. A ideia foi simular um sistema de atendimento para restaurantes, aplicando os conhecimentos e conceitos de programação de Python em prática.

```Python
import tkinter as tk

# Definir as dimensões das janelas:
AUTURA, LARGURA = 700, 600 # para visualização.
AUTURA2,LARGURA2 = 600,500 # para edição.

#denifir as cores: braco, verde e azul
cores = ["white","#0de5a8", "#506ee5"]  # PRONTO, PREPARANDO.

# Dados globais :
itens_preparando = [] # pedidos que estão em preparação.
itens_pronto = [] # pedidos que já estão prontos.

# ========================== Função janela_visualizacao() ==========================

def janela_visualizacao(): # Definir janela
    janela = tk.Tk() # Criar a janela principal do aplicativo Tkinter.
    janela.geometry(f"{AUTURA}x{LARGURA}") # Colocar altura e largura.
    janela.title("Pedidos - Visualização") # Título da janela.
    janela.configure(bg=cores[0]) # Cor de fundo da janela.

    frame_principal = tk.Frame(janela, bg=cores[0]) # Cria um container (frame) dentro da janela janela. Este frame funciona como um organizador para agrupar e posicionar outros widgets, e também define a cor desse frame.
    frame_principal.pack(pady=20) # Posiciona o frame dentro da janela e adiciona um espaço de 20 pixels.

    def criar_coluna(titulo, cor, col):
        frame = tk.Frame(frame_principal, bg=cores[0]) # Criar frame.
        frame.grid(row=0, column=col, padx=40) # Posicionar frame.

        tk.Label(frame, text=titulo, font=("Arial", 30, "bold"), fg=cor, bg=cores[0]).pack()
        #Cria um label no topo do frame com o título, com fonte grande e negrito, cor do texto conforme 'cor', e fundo branco.

        scroll = tk.Scrollbar(frame, orient=tk.VERTICAL) # Cria uma barra de rolagem vertical dentro do frame.
        scroll.pack(side=tk.RIGHT, fill=tk.Y) # Posiciona a barra de rolagem à direita do frame, preenchendo verticalmente.

        lista = tk.Listbox(
            frame, width=20, height=15, font=("Arial", 20), fg=cor,
            borderwidth=0, highlightthickness=0, yscrollcommand=scroll.set
        )
        lista.pack(side=tk.LEFT)
        # Cria uma Listbox para listar os itens, com largura 20, altura 15 linhas, fonte tamanho 20, cor do texto conforme 'cor', sem bordas, e vinculada à scrollbar.

        scroll.config(command=lista.yview) # Configura a scrollbar para controlar a rolagem vertical da Listbox.
        return lista # Retorna a Listbox para que possa ser manipulada fora da função.

    lista_preparando = criar_coluna("PREPARANDO", cores[2], 0) # Texto,cor e coluna 0.
    lista_pronto = criar_coluna("PRONTO", cores[1], 2) # Texto,cor e coluna 1.

    def atualizar_listas():
        lista_preparando.delete(0, tk.END)
        lista_pronto.delete(0, tk.END)
        for item in itens_preparando:
            lista_preparando.insert(tk.END, item)
        for item in itens_pronto:
            lista_pronto.insert(tk.END, item)

    def atualizar_periodico():
        atualizar_listas()
        janela.after(500, atualizar_periodico)

    atualizar_periodico()

    return janela # No final retorna toda a janela de vizualização.

# ============================= Função para a janela_edicao() =============================

def janela_edicao():
    edicao = tk.Toplevel() # Criar a janela principal do aplicativo Tkinter.
    edicao.geometry(f"{AUTURA2}x{LARGURA2}") # Colocar altura e largura.
    edicao.title("Editar Pedidos") # Título da janela.
    edicao.configure(bg=cores[0]) # Cor de fundo da janela.

    tk.Label(edicao, text="Adicionar pedidos:", font=("Arial", 14, "bold"), bg=cores[0]).pack(pady=8) # Cria um texto pra ficar em cima.

    entrada_pedido = tk.Entry(edicao, font=("Arial", 12), width=30)
    entrada_pedido.pack(pady=5) # Cria tipo um "input" para adicionar o números dos pedidos.

    frame_listas = tk.Frame(edicao, bg=cores[0])
    frame_listas.pack(pady=10) # Frame das listas.

    def criar_lista(frame, titulo, cor): # Títulos "PREPARANDO" e "PRONTO".
        f = tk.Frame(frame, bg=cores[0])
        f.pack(side=tk.LEFT, padx=20)
        tk.Label(f, text=titulo, font=("Arial", 14, "bold"), fg=cor, bg=cores[0]).pack()
        lista = tk.Listbox(f, width=25, height=10, font=("Arial", 12), fg=cor)
        lista.pack()
        return lista

    lista_preparando_ed = criar_lista(frame_listas, "PREPARANDO", cores[2])
    lista_pronto_ed = criar_lista(frame_listas, "PRONTO", cores[1])

    def atualizar_listas_edicao():
        lista_preparando_ed.delete(0, tk.END)
        for item in itens_preparando:
            lista_preparando_ed.insert(tk.END, item)

        lista_pronto_ed.delete(0, tk.END)
        for item in itens_pronto:
            lista_pronto_ed.insert(tk.END, item)

    def adicionar_pedido(): # Função de adicionar pedidos.
        pedido = entrada_pedido.get().strip()
        if pedido and pedido not in itens_preparando and pedido not in itens_pronto: # Se o pedido não estiver em itens_preparando ou em itens_pronto ele coloca em itens preparando.
            itens_preparando.append(pedido)
            entrada_pedido.delete(0, tk.END)
            atualizar_listas_edicao()

    def mover_para_pronto():# Função de mover pedidos de preparando para pronto.
        selecionados = lista_preparando_ed.curselection() # O produto precisa ser selecionado.
        for i in reversed(selecionados):
            itens_pronto.append(itens_preparando.pop(i))
        atualizar_listas_edicao()

    def remover_de_pronto(): # Função de remover pedidos de pronto.
        selecionados = lista_pronto_ed.curselection() # O produto precisa ser selecionado.
        for i in reversed(selecionados):
            itens_pronto.pop(i)
        atualizar_listas_edicao()

    # botões:
    tk.Button(edicao, text="Pedido realizado", font=("Arial", 12), command=adicionar_pedido).pack(pady=8)
    tk.Button(edicao, text="Mover para PRONTO →", font=("Arial", 12), command=mover_para_pronto).pack(pady=6)
    tk.Button(edicao, text="← Remover de PRONTO", font=("Arial", 12), command=remover_de_pronto).pack(pady=4)

    atualizar_listas_edicao()

    return edicao # No final retorna toda a janela de edição.

# ============================ Inicialização das Janelas ===========================

if __name__ == "__main__":
    janela_visual = janela_visualizacao()
    janela_edicao1 = janela_edicao()
    janela_visual.mainloop()

´´´
