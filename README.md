import discord
from discord import app_commands
from discord.ext import commands
import threading
import http.server
import socketserver

intents = discord.Intents.default()
intents.members = True

bot = commands.Bot(command_prefix="!", intents=intents)

@bot.tree.command(name="dar_cargo", description="Atribui um cargo a um usuário")
@app_commands.describe(user="Usuário para atribuir o cargo", role="Cargo a ser atribuído")
async def dar_cargo(interaction: discord.Interaction, user: discord.Member, role: discord.Role):
    if role.position >= interaction.guild.me.top_role.position:
        await interaction.response.send_message("Não tenho permissão para dar este cargo.", ephemeral=True)
        return

    await user.add_roles(role)
    await interaction.response.send_message(f'O cargo {role.name} foi atribuído a {user.mention} com sucesso!', ephemeral=True)

# Comando para remover cargo
@bot.tree.command(name="remover_cargo", description="Remove um cargo de um usuário")
@app_commands.describe(user="Usuário para remover o cargo", role="Cargo a ser removido")
async def remover_cargo(interaction: discord.Interaction, user: discord.Member, role: discord.Role):
    if role.position >= interaction.guild.me.top_role.position:
        await interaction.response.send_message("Não tenho permissão para remover este cargo.", ephemeral=True)
        return

    await user.remove_roles(role)
    await interaction.response.send_message(f'O cargo {role.name} foi removido de {user.mention} com sucesso!', ephemeral=True)

# Evento on_ready para sincronizar comandos
@bot.event
async def on_ready():
    await bot.tree.sync()  # Sincroniza os comandos
    print(f'{bot.user} está online e pronto para usar comandos Slash!')

# Função para rodar um servidor simples HTTP
def run_http_server():
    handler = http.server.SimpleHTTPRequestHandler
    with socketserver.TCPServer(("", 3000), handler) as httpd:
        print("Servidor HTTP rodando em http://localhost:3000")
        httpd.serve_forever()

# Rodar o servidor HTTP em um thread separado
http_thread = threading.Thread(target=run_http_server)
http_thread.daemon = True  # Faz o thread funcionar como daemon
http_thread.start()

# Rodar o bot do Discord
bot.run('MTM0MDY5NDI3MDYyOTM4NDI2Mg.GvnVKk.CoRPVsbgWVFdTP6AQRWvzaKI6aPWXn4OLpp_Yo')
