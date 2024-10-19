# Discord Bot

This is a Discord bot for managing server roles and channels.

## Features
- Setup server categories and channels
- Assign roles to members
- Manage channel permissions

## Installation
1. Clone the repository:
   ```bash
   git clone https://github.com/valuecoding/discord-bot.git

   
## Navigate to the project directory:
cd discord-bot

## Install the dependencies:
pip install -r requirements.txt

## Usage
!setup: Setup the server categories and channels.

!assign_roles: Assign the ClubMember role to all current members.

## Code
```python
import discord
from discord.ext import commands

intents = discord.Intents.default()
intents.members = True
intents.guilds = True
intents.messages = True
intents.message_content = True

bot = commands.Bot(command_prefix='!', intents=intents)

@bot.event
async def on_ready():
    await bot.change_presence(status=discord.Status.online, activity=discord.Game('WoW Cataclysm'))
    print(f"{bot.user.name} has started")

@bot.command(name='setup', help='Setup the server categories and channels')
async def setup(ctx):
    try:
        guild = ctx.guild
        if guild is None:
            await ctx.send("Dieser Befehl kann nur in einem Server ausgeführt werden.")
            return

        # Rolle ClubMember überprüfen
        club_member_role = discord.utils.get(guild.roles, name="ClubMember")
        if not club_member_role:
            club_member_role = await guild.create_role(name="ClubMember", colour=discord.Colour(0x1abc9c))

        # Berechtigungen setzen
        overwrite = {
            guild.default_role: discord.PermissionOverwrite(view_channel=False),
            club_member_role: discord.PermissionOverwrite(view_channel=True)
        }

        # MAIN ZONE Category
        main_zone_category = discord.utils.get(guild.categories, name="MAIN ZONE")
        if not main_zone_category:
            main_zone_category = await guild.create_category("MAIN ZONE", overwrites=overwrite)

        voice_channels = {'😴 AFK': 'AFK', '🗣️ talk-1': 'talk-1', '🗣️ talk-2': 'talk-2', '🗣️ talk-3': 'talk-3', '⚔️ rbg-arena': 'rbg-arena'}
        for emoji_name, channel_name in voice_channels.items():
            if not discord.utils.get(guild.voice_channels, name=channel_name, category=main_zone_category):
                await guild.create_voice_channel(emoji_name, category=main_zone_category, overwrites=overwrite)

        if not discord.utils.get(guild.text_channels, name='chat', category=main_zone_category):
            await guild.create_text_channel('💬 chat', category=main_zone_category, overwrites=overwrite)

        # Guide Kategorie
        guide_categories = {'Druid': '🦌', 'Hunter': '🏹', 'Mage': '🔮', 'Paladin': '🛡️', 'Priest': '✝️', 'Rogue': '🗡️', 'Shaman': '🌀', 'Warlock': '📜', 'Warrior': '⚔️'}
        guide_category = discord.utils.get(guild.categories, name="Guide")
        if not guide_category:
            guide_category = await guild.create_category("Guide", overwrites=overwrite)

        for guide_category_name, emoji in guide_categories.items():
            if not discord.utils.get(guild.text_channels, name=guide_category_name, category=guide_category):
                await guild.create_text_channel(f"{emoji} {guide_category_name}", category=guide_category, overwrites=overwrite)

        await ctx.send("Setup completed successfully.")
    except Exception as e:
        await ctx.send(f"Error during setup: {e}")

@bot.command(name='assign_roles', help='Assign ClubMember role to all current members')
async def assign_roles(ctx):
    try:
        guild = ctx.guild
        if guild is None:
            await ctx.send("Dieser Befehl kann nur in einem Server ausgeführt werden.")
            return
        
        role = discord.utils.get(guild.roles, name="ClubMember")
        if not role:
            role = await guild.create_role(name="ClubMember", colour=discord.Colour(0x1abc9c))

        for member in guild.members:
            if role not in member.roles and not member.bot:
                await member.add_roles(role)
        
        await ctx.send("ClubMember role assigned to all members.")
    except Exception as e:
        await ctx.send(f"Error during assigning roles: {e}")

bot_token = 'YOUR_BOT_TOKEN_HERE'

if bot_token:
    bot.run(bot_token)
else:
    print("Bot-Token nicht gesetzt. Bitte überprüfen.")
```

## License
This project is licensed under the MIT License.

## Developed
Coded by peter/darkwingsuck (git: valuecoding)
Coding time: 2-3 hours


