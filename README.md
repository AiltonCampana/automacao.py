import json
import requests
import os

# CONFIGURAÇÕES DO TELEGRAM (Armazenadas de forma segura no GitHub)
TELEGRAM_BOT_TOKEN = os.environ.get("8842538086:AAHGdTvyvz_c_W_ZxvFKAXYB4QWvnVD9Iwk")
TELEGRAM_CHAT_ID = os.environ.get("6548574334")  # ID do canal (ex: @meucanaldeachados)

# LISTA DE PRODUTOS PARA POSTAR (Você atualiza ou preenche via arquivo JSON)
PRODUTOS = [
    {
        "titulo": "Fone de Ouvido Bluetooth Sem Fio",
        "categoria": "Tecnologia",
        "preco_de": "229,90",
        "preco_por": "149,90",
        "desconto": "35% OFF",
        "imagem": "https://images.unsplash.com/photo-1505740420928-5e560c06d30e?w=500&q=80",
        "link_afiliado": "https://link.amazon/B05hkEqBH"
    }
]

def gerar_html():
    """Gera o arquivo index.html atualizado com os produtos"""
    cards_html = ""
    for prod in PRODUTOS:
        cards_html += f'''
      <div class="bg-white rounded-2xl shadow-sm border border-gray-100 overflow-hidden hover:shadow-md transition flex flex-col justify-between">
        <div>
          <div class="relative">
            <span class="absolute top-3 left-3 bg-red-500 text-white text-xs font-bold px-2 py-1 rounded-md">{prod['desconto']}</span>
            <img src="{prod['imagem']}" alt="{prod['titulo']}" class="w-full h-48 object-cover">
          </div>
          <div class="p-5">
            <span class="text-xs font-semibold text-gray-400 uppercase">{prod['categoria']}</span>
            <h3 class="text-lg font-bold text-slate-900 mt-1 line-clamp-2">{prod['titulo']}</h3>
            <div class="mt-3 flex items-baseline gap-2">
              <span class="text-2xl font-extrabold text-slate-900">R$ {prod['preco_por']}</span>
              <span class="text-sm text-gray-400 line-through">R$ {prod['preco_de']}</span>
            </div>
            <p class="text-xs text-emerald-600 font-semibold mt-1"><i class="fa-solid fa-truck-fast"></i> Frete Grátis Prime</p>
          </div>
        </div>
        <div class="p-5 pt-0">
          <a href="{prod['link_afiliado']}" target="_blank" class="w-full bg-amber-400 hover:bg-amber-500 text-slate-900 font-bold py-3 rounded-xl transition text-center block shadow-sm">
            Ver Oferta na Amazon <i class="fa-solid fa-arrow-up-right-from-square text-xs ml-1"></i>
          </a>
        </div>
      </div>
        '''

    html_completo = f'''<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Achadinhos da Semana | Seleção de Ofertas Amazon</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <style>
    @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700;800&display=swap');
    body {{ font-family: 'Inter', sans-serif; }}
  </style>
</head>
<body class="bg-gray-50 text-gray-800">
  <header class="bg-amber-400 text-slate-900 text-sm font-semibold py-2 px-4 text-center">
    🔥 Ofertas verificadas diariamente! Entre no nosso 
    <a href="https://t.me/SEU_CANAL" target="_blank" class="underline hover:text-white font-bold ml-1">
      <i class="fa-brands fa-telegram"></i> Canal do Telegram
    </a>
  </header>

  <section class="max-w-5xl mx-auto px-4 pt-8 pb-6 text-center">
    <div class="inline-block bg-orange-100 text-orange-800 text-xs font-bold px-3 py-1 rounded-full mb-3 uppercase tracking-wide">
      Ofertas Verificadas na Amazon 🛒
    </div>
    <h1 class="text-3xl md:text-5xl font-extrabold text-slate-900 tracking-tight leading-tight">
      Os Melhores Achadinhos e Promoções
    </h1>
    <p class="mt-3 text-base md:text-lg text-gray-600 max-w-2xl mx-auto">
      Produtos selecionados com maiores descontos e frete rápido.
    </p>
  </section>

  <main class="max-w-6xl mx-auto px-4 py-8">
    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-6">
      {cards_html}
    </div>
  </main>

  <footer class="bg-slate-900 text-gray-400 text-xs py-8 px-4 mt-12 border-t border-slate-800 text-center">
    <p>Achadinhos & Ofertas © 2026. Como participante do Programa de Associados da Amazon, posso receber comissões por compras qualificadas.</p>
  </footer>
</body>
</html>'''

    with open("index.html", "w", encoding="utf-8") as f:
        f.write(html_completo)

def enviar_telegram(produto):
    """Envia a oferta automaticamente para o Canal do Telegram"""
    if TELEGRAM_BOT_TOKEN and TELEGRAM_CHAT_ID:
        mensagem = f'''🔥 **ACHADINHO DA AMAZON**

📌 *{produto['titulo']}*
💰 De ~R$ {produto['preco_de']}~ por apenas **R$ {produto['preco_por']}** ({produto['desconto']})
🚚 Frete Grátis Prime

🛒 **Compre pelo link oficial:**
{produto['link_afiliado']}'''

        url = f"https://api.telegram.org/bot{TELEGRAM_BOT_TOKEN}/sendMessage"
        payload = {
            "chat_id": TELEGRAM_CHAT_ID,
            "text": mensagem,
            "parse_mode": "Markdown"
        }
        requests.post(url, json=payload)

if __name__ == "__main__":
    gerar_html()
    if PRODUTOS:
        enviar_telegram(PRODUTOS[0])
