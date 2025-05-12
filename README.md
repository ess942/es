import discord
from discord.ext import commands
import os

intents = discord.Intents.default()
intents.members = True
intents.guilds = True

bot = commands.Bot(command_prefix="!", intents=intents)

@bot.event
async def on_ready():
    print(f"✅ Logged in as {bot.user}")

@bot.event
async def on_member_update(before, after):
    target_role_name = "VIP"  # اسم الرول اللي نتابعه
    prefix = "『VH』"          # البادئة اللي نضيفها أو نشيلها

    had_role_before = target_role_name in [role.name for role in before.roles]
    has_role_now = target_role_name in [role.name for role in after.roles]

    # الحالة 1: أخد الرول (نضيف البادئة لو مش موجودة)
    if has_role_now and not had_role_before:
        if not after.display_name.startswith(prefix):
            new_name = f"{prefix}{after.display_name}"
            try:
                await after.edit(nick=new_name)
                print(f"✔️ Nickname updated to: {new_name}")
            except discord.Forbidden:
                print("❌ البوت معندوش صلاحيات لتغيير الاسم.")
            except Exception as e:
                print(f"⚠️ Error: {e}")

    # الحالة 2: اتشال منه الرول (نشيل البادئة لو كانت موجودة)
    elif not has_role_now and had_role_before:
        if after.display_name.startswith(prefix):
            new_name = after.display_name[len(prefix):]
            try:
                await after.edit(nick=new_name)
                print(f"↩️ Nickname reverted to: {new_name}")
            except discord.Forbidden:
                print("❌ البوت معندوش صلاحيات لتغيير الاسم.")
            except Exception as e:
                print(f"⚠️ Error: {e}")

bot.run(os.environ["TOKEN"])
