import discord
from discord.ext import commands
from dotenv import load_dotenv
import os
import time
from gtm import get_class

load_dotenv()
DISCORD_TOKEN = os.getenv('DISCORD_TOKEN')
intents = discord.Intents.default()
intents.message_content = True

bot = commands.Bot(command_prefix='/', intents=intents, help_command=None)

@bot.event
async def on_ready():
    print(f'Бот {bot.user} запущен и готов к работе')

@bot.command()
async def photo(ctx):
    if ctx.message.attachments:
        for attachment in ctx.message.attachments:
            if attachment.filename.endswitch('.jpg') or \
            attachment.filename.endswitch('.jpeg') or \
            attachment.filename.endswitch('.png'):
                image_path = f'./images/{attachment.filename}'    
                await attachment.save(image_path)
                time.sleep(8)
                msg = await ctx.send('Обработка фото...')
                class_name, confidence_score = get_class(image_path, 
                                                         './gtm_model/keras_model.h5', 
                                                         './gtm_model/labels.txt', )
                await msg.delete()
                await ctx.send(f'С вероятностью {confidence_score}% на фото {class_name}')

                os.remove(image_path)
            else:
                await ctx.send('Можете прислать только jpg, jpeg, png') 
                return   
    else:
        await ctx.send('Кажется, вы забыли прикрепить фото')

@bot.command()
async def heh(ctx, count_heh = 5):
    await ctx.send("he" * count_heh)

bot.run(DISCORD_TOKEN)
