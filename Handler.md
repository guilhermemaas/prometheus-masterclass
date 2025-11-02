Um handler é o trecho de código responsável por “lidar” (handle) com um determinado evento, requisição ou ação.

👉 Em outras palavras:

É quem recebe algo (um evento, uma requisição HTTP, uma mensagem, um erro etc.) e executa o que deve ser feito em resposta.

Exemplos práticos

Em APIs (FastAPI, Flask, Express):

@app.get("/usuarios")
def get_usuarios():
    return listar_todos()  # este é o handler da rota GET /usuarios


→ Esse método é o request handler (ou route handler).

Em sistemas de eventos:

def on_click(event):
    print("Botão clicado!")  # handler do evento de clique


Em logging ou erros:
Um error handler ou log handler define como tratar exceções ou salvar logs.

✅ Resumo curto:

Handler = função ou objeto que trata um evento ou requisição específica.