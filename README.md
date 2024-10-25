# MKI_Viagens
import customtkinter as ctk
from tkinter import messagebox, simpledialog
import json
import os
from datetime import datetime

class AplicativoViagens(ctk.CTk):
    def __init__(self):
        super().__init__()
        
        self.title("Gerenciador de Viagens")
        self.geometry("800x600")
        
        self.usuarios = {}
        self.viagens = []
        self.carregar_usuarios()
        self.carregar_dados()
        self.criar_tela_login()

    def criar_tela_login(self):
        self.limpar_tela()
        
        self.frame_login = ctk.CTkFrame(self)
        self.frame_login.pack(pady=20, padx=60, fill="both", expand=True)
        
        self.label_login = ctk.CTkLabel(self.frame_login, text="Gerenciador MKI ")
        self.label_login.pack(pady=12, padx=10)
        
        self.entrada_usuario = ctk.CTkEntry(self.frame_login, placeholder_text="Usuário")
        self.entrada_usuario.pack(pady=12, padx=10)
        
        self.entrada_senha = ctk.CTkEntry(self.frame_login, placeholder_text="Senha", show="*")
        self.entrada_senha.pack(pady=12, padx=10)
        
        self.botao_login = ctk.CTkButton(self.frame_login, text="Login", command=self.login_usuario)
        self.botao_login.pack(pady=12, padx=10)
        
        self.botao_registro = ctk.CTkButton(self.frame_login, text="Registrar", command=self.registrar_usuario)
        self.botao_registro.pack(pady=12, padx=10)
        
    def criar_tela_principal(self):
        self.limpar_tela()
        
        self.frame_principal = ctk.CTkFrame(self)
        self.frame_principal.pack(pady=20, padx=60, fill="both", expand=True)
        
        self.label_principal = ctk.CTkLabel(self.frame_principal, text="Bem-vindo ao Gerenciamento de Viagens MKI!")
        self.label_principal.pack(pady=12, padx=10)
        
        self.botao_adicionar_viagem = ctk.CTkButton(self.frame_principal, text="Adicionar Nova Viagem", command=self.adicionar_viagem)
        self.botao_adicionar_viagem.pack(pady=12, padx=10)
        
        self.botao_listar_viagens = ctk.CTkButton(self.frame_principal, text="Listar Todas as Viagens", command=self.listar_viagens)
        self.botao_listar_viagens.pack(pady=12, padx=10)
        
        self.botao_atualizar_viagem = ctk.CTkButton(self.frame_principal, text="Atualizar Viagem", command=self.atualizar_viagem)
        self.botao_atualizar_viagem.pack(pady=12, padx=10)
        
        self.botao_deletar_viagem = ctk.CTkButton(self.frame_principal, text="Deletar Viagem", command=self.deletar_viagem)
        self.botao_deletar_viagem.pack(pady=12, padx=10)
        
        self.botao_filtrar_viagens = ctk.CTkButton(self.frame_principal, text="Filtrar Viagens", command=self.filtrar_viagens)
        self.botao_filtrar_viagens.pack(pady=12, padx=10)
        
        self.botao_voltar_login = ctk.CTkButton(self.frame_principal, text="Voltar para Login", command=self.voltar_tela_login)
        self.botao_voltar_login.pack(pady=12, padx=10)
        
        self.botao_sair = ctk.CTkButton(self.frame_principal, text="Sair do Aplicativo", command=self.sair_aplicativo)
        self.botao_sair.pack(pady=12, padx=10)

    def limpar_tela(self):
        for widget in self.winfo_children():
            widget.destroy()
    
    def carregar_usuarios(self):
        if os.path.exists("usuarios.json"):
            with open("usuarios.json", "r") as arquivo:
                self.usuarios = json.load(arquivo)
                
    def salvar_usuarios(self):
        with open("usuarios.json", "w") as arquivo:
            json.dump(self.usuarios, arquivo)

    def login_usuario(self):
        usuario = self.entrada_usuario.get()
        senha = self.entrada_senha.get()
        
        if usuario in self.usuarios and self.usuarios[usuario] == senha:
            self.usuario_logado = usuario
            self.carregar_dados_usuario()
            self.criar_tela_principal()
        else:
            messagebox.showerror("Erro", "Usuário ou senha inválidos")
    
    def registrar_usuario(self):
        usuario = self.entrada_usuario.get()
        senha = self.entrada_senha.get()
        
        if not usuario or not senha:
            messagebox.showerror("Erro", "Usuário e senha não podem estar em branco")
            return
        
        if usuario in self.usuarios:
            messagebox.showerror("Erro", "Usuário já existe")
        else:
            self.usuarios[usuario] = senha
            self.salvar_usuarios()
            os.makedirs(f"viagens_{usuario}", exist_ok=True)
            messagebox.showinfo("Sucesso", "Usuário registrado com sucesso")

    def adicionar_viagem(self):
        self.limpar_tela()
        
        self.frame_adicionar_viagem = ctk.CTkFrame(self)
        self.frame_adicionar_viagem.pack(pady=20, padx=60, fill="both", expand=True)
        
        self.entrada_destino = ctk.CTkEntry(self.frame_adicionar_viagem, placeholder_text="Destino")
        self.entrada_destino.pack(pady=12, padx=10)
        
        self.entrada_data_inicio = ctk.CTkEntry(self.frame_adicionar_viagem, placeholder_text="Data de Início (DD/MM/AAAA)")
        self.entrada_data_inicio.pack(pady=12, padx=10)
        
        self.entrada_data_fim = ctk.CTkEntry(self.frame_adicionar_viagem, placeholder_text="Data de Fim (DD/MM/AAAA)")
        self.entrada_data_fim.pack(pady=12, padx=10)
        
        self.entrada_atividades = ctk.CTkEntry(self.frame_adicionar_viagem, placeholder_text="Atividades (separadas por vírgula)")
        self.entrada_atividades.pack(pady=12, padx=10)
        
        self.botao_salvar_viagem = ctk.CTkButton(self.frame_adicionar_viagem, text="Salvar Viagem", command=self.salvar_viagem)
        self.botao_salvar_viagem.pack(pady=12, padx=10)
        
        self.botao_voltar = ctk.CTkButton(self.frame_adicionar_viagem, text="Voltar", command=self.criar_tela_principal)
        self.botao_voltar.pack(pady=12, padx=10)
    
    def salvar_viagem(self):
        destino = self.entrada_destino.get()
        data_inicio = self.entrada_data_inicio.get()
        data_fim = self.entrada_data_fim.get()
        atividades = self.entrada_atividades.get()
        
        if not destino or not data_inicio or not data_fim or not atividades:
            messagebox.showerror("Erro", "Todos os campos são obrigatórios")
            return
        
        try:
            data_inicio = datetime.strptime(data_inicio, "%d/%m/%Y")
            data_fim = datetime.strptime(data_fim, "%d/%m/%Y")
            
            if data_fim < data_inicio:
                messagebox.showerror("Erro", "Data de término não pode ser anterior à data de início")
                return
            
            viagem = {
                "destino": destino,
                "data_inicio": data_inicio.strftime("%d/%m/%Y"),
                "data_fim": data_fim.strftime("%d/%m/%Y"),
                "atividades": atividades.split(",")
            }
            
            self.viagens.append(viagem)
            self.salvar_dados_usuario()
            messagebox.showinfo("Sucesso", "Viagem adicionada com sucesso")
            self.criar_tela_principal()
        except ValueError:
            messagebox.showerror("Erro", "Formato de data inválido. Por favor, use DD/MM/AAAA.")
    
    def listar_viagens(self):
        self.limpar_tela()
        
        self.frame_listar_viagens = ctk.CTkFrame(self)
        self.frame_listar_viagens.pack(pady=20, padx=60, fill="both", expand=True)
        
        if self.viagens:
            for viagem in self.viagens:
                detalhes_viagem = (
                    f"Destino: {viagem['destino']}\n"
                    f"Data de Início: {viagem['data_inicio']}\n"
                    f"Data de Fim: {viagem['data_fim']}\n"
                    f"Atividades: {', '.join(viagem['atividades'])}\n"
                    "------------------------------"
                )
                label_viagem = ctk.CTkLabel(self.frame_listar_viagens, text=detalhes_viagem, anchor="w", justify="left")
                label_viagem.pack(pady=6, padx=10)
        else:
            label_sem_viagens = ctk.CTkLabel(self.frame_listar_viagens, text="Nenhuma viagem encontrada.")
            label_sem_viagens.pack(pady=12, padx=10)
        
        self.botao_voltar = ctk.CTkButton(self.frame_listar_viagens, text="Voltar", command=self.criar_tela_principal)
        self.botao_voltar.pack(pady=12, padx=10)
    
    def atualizar_viagem(self):
        self.limpar_tela()
        
        self.frame_atualizar_viagem = ctk.CTkFrame(self)
        self.frame_atualizar_viagem.pack(pady=20, padx=60, fill="both", expand=True)
        
        self.lista_viagens = ctk.CTkOptionMenu(self.frame_atualizar_viagem, values=[viagem['destino'] for viagem in self.viagens])
        self.lista_viagens.pack(pady=12, padx=10)
        
        self.botao_selecionar_viagem = ctk.CTkButton(self.frame_atualizar_viagem, text="Selecionar Viagem", command=self.selecionar_viagem_atualizar)
        self.botao_selecionar_viagem.pack(pady=12, padx=10)
        
        self.botao_voltar = ctk.CTkButton(self.frame_atualizar_viagem, text="Voltar", command=self.criar_tela_principal)
        self.botao_voltar.pack(pady=12, padx=10)
    
    def selecionar_viagem_atualizar(self):
        destino_selecionado = self.lista_viagens.get()
        self.viagem_atualizar = next((viagem for viagem in self.viagens if viagem['destino'] == destino_selecionado), None)
        
        if self.viagem_atualizar:
            self.limpar_tela()
            
            self.frame_editar_viagem = ctk.CTkFrame(self)
            self.frame_editar_viagem.pack(pady=20, padx=60, fill="both", expand=True)
            
            self.entrada_destino = ctk.CTkEntry(self.frame_editar_viagem, placeholder_text="Destino")
            self.entrada_destino.insert(0, self.viagem_atualizar['destino'])
            self.entrada_destino.pack(pady=12, padx=10)
            
            self.entrada_data_inicio = ctk.CTkEntry(self.frame_editar_viagem, placeholder_text="Data de Início (DD/MM/AAAA)")
            self.entrada_data_inicio.insert(0, self.viagem_atualizar['data_inicio'])
            self.entrada_data_inicio.pack(pady=12, padx=10)
            
            self.entrada_data_fim = ctk.CTkEntry(self.frame_editar_viagem, placeholder_text="Data de Fim (DD/MM/AAAA)")
            self.entrada_data_fim.insert(0, self.viagem_atualizar['data_fim'])
            self.entrada_data_fim.pack(pady=12, padx=10)
            
            self.entrada_atividades = ctk.CTkEntry(self.frame_editar_viagem, placeholder_text="Atividades (separadas por vírgula)")
            self.entrada_atividades.insert(0, ", ".join(self.viagem_atualizar['atividades']))
            self.entrada_atividades.pack(pady=12, padx=10)
            
            self.botao_salvar_alteracoes = ctk.CTkButton(self.frame_editar_viagem, text="Salvar Alterações", command=self.salvar_alteracoes)
            self.botao_salvar_alteracoes.pack(pady=12, padx=10)
            
            self.botao_voltar = ctk.CTkButton(self.frame_editar_viagem, text="Voltar", command=self.atualizar_viagem)
            self.botao_voltar.pack(pady=12, padx=10)
        else:
            messagebox.showerror("Erro", "Viagem não encontrada")
    
    def salvar_alteracoes(self):
        destino = self.entrada_destino.get()
        data_inicio = self.entrada_data_inicio.get()
        data_fim = self.entrada_data_fim.get()
        atividades = self.entrada_atividades.get()
        
        if not destino or not data_inicio or not data_fim or not atividades:
            messagebox.showerror("Erro", "Todos os campos são obrigatórios")
            return
        
        try:
            data_inicio = datetime.strptime(data_inicio, "%d/%m/%Y")
            data_fim = datetime.strptime(data_fim, "%d/%m/%Y")
            
            if data_fim < data_inicio:
                messagebox.showerror("Erro", "Data de término não pode ser anterior à data de início")
                return
            
            self.viagem_atualizar.update({
                "destino": destino,
                "data_inicio": data_inicio.strftime("%d/%m/%Y"),
                "data_fim": data_fim.strftime("%d/%m/%Y"),
                "atividades": atividades.split(",")
            })
            
            self.salvar_dados_usuario()
            messagebox.showinfo("Sucesso", "Viagem atualizada com sucesso")
            self.criar_tela_principal()
        except ValueError:
            messagebox.showerror("Erro", "Formato de data inválido. Por favor, use DD/MM/AAAA.")
    
    def deletar_viagem(self):
        self.limpar_tela()
        
        self.frame_deletar_viagem = ctk.CTkFrame(self)
        self.frame_deletar_viagem.pack(pady=20, padx=60, fill="both", expand=True)
        
        self.lista_viagens = ctk.CTkOptionMenu(self.frame_deletar_viagem, values=[viagem['destino'] for viagem in self.viagens])
        self.lista_viagens.pack(pady=12, padx=10)
        
        self.botao_deletar_viagem = ctk.CTkButton(self.frame_deletar_viagem, text="Deletar Viagem", command=self.confirmar_delecao_viagem)
        self.botao_deletar_viagem.pack(pady=12, padx=10)
        
        self.botao_voltar = ctk.CTkButton(self.frame_deletar_viagem, text="Voltar", command=self.criar_tela_principal)
        self.botao_voltar.pack(pady=12, padx=10)
    
    def confirmar_delecao_viagem(self):
        destino_selecionado = self.lista_viagens.get()
        self.viagens = [viagem for viagem in self.viagens if viagem['destino'] != destino_selecionado]
        self.salvar_dados_usuario()
        messagebox.showinfo("Sucesso", "Viagem deletada com sucesso")
        self.criar_tela_principal()
    
    def filtrar_viagens(self):
        destino = simpledialog.askstring("Filtro", "Digite o destino para filtrar as viagens:")
        
        if destino:
            self.limpar_tela()
            
            self.frame_listar_viagens = ctk.CTkFrame(self)
            self.frame_listar_viagens.pack(pady=20, padx=60, fill="both", expand=True)
            
            viagens_filtradas = [viagem for viagem in self.viagens if destino.lower() in viagem['destino'].lower()]
            
            if viagens_filtradas:
                for viagem in viagens_filtradas:
                    detalhes_viagem = (
                        f"Destino: {viagem['destino']}\n"
                        f"Data de Início: {viagem['data_inicio']}\n"
                        f"Data de Fim: {viagem['data_fim']}\n"
                        f"Atividades: {', '.join(viagem['atividades'])}\n"
                        "------------------------------"
                    )
                    label_viagem = ctk.CTkLabel(self.frame_listar_viagens, text=detalhes_viagem, anchor="w", justify="left")
                    label_viagem.pack(pady=6, padx=10)
            else:
                label_sem_viagens = ctk.CTkLabel(self.frame_listar_viagens, text="Nenhuma viagem encontrada.")
                label_sem_viagens.pack(pady=12, padx=10)
            
            self.botao_voltar = ctk.CTkButton(self.frame_listar_viagens, text="Voltar", command=self.criar_tela_principal)
            self.botao_voltar.pack(pady=12, padx=10)
    
    def voltar_tela_login(self):
        self.usuario_logado = None
        self.viagens = []
        self.criar_tela_login()

    def sair_aplicativo(self):
        self.destroy()
        
    def carregar_dados(self):
        if os.path.exists("viagens.json"):
            with open("viagens.json", "r") as arquivo:
                self.viagens = json.load(arquivo)
    
    def carregar_dados_usuario(self):
        caminho = f"viagens_{self.usuario_logado}/viagens.json"
        if os.path.exists(caminho):
            with open(caminho, "r") as arquivo:
                self.viagens = json.load(arquivo)
        else:
            self.viagens = []
    
    def salvar_dados_usuario(self):
        caminho = f"viagens_{self.usuario_logado}/viagens.json"
        with open(caminho, "w") as arquivo:
            json.dump(self.viagens, arquivo)

if __name__ == "__main__":
    app = AplicativoViagens()
    app.mainloop()
