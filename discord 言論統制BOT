import discord
from discord.ext import commands
from discord import app_commands
import datetime
import logging
import os
from dotenv import load_dotenv

# 環境変数の読み込み (もし .env ファイルを使用する場合)
load_dotenv()
TOKEN = os.getenv('DISCORD_TOKEN')
LOG_CHANNEL_ID = int(os.getenv('LOG_CHANNEL_ID', 0))  # ログチャンネルID
EXCLUDED_CHANNEL_IDS = [int(id) for id in os.getenv('EXCLUDED_CHANNEL_IDS', '0').split(',')] # 除外チャンネルIDリスト

# Botのプレフィックスとインテント
intents = discord.Intents.all()
bot = commands.Bot(command_prefix='!', intents=intents)
bot.tree = app_commands.CommandTree(bot)

# 禁止ワードリスト (最終的なリスト)
restricted_keywords = [
    "キチガイ", "チョン", "シナ人", "ガイジン死ね", "ハーフざまあ", "オカマはキモい", "レズビアン消えろ", "精神障害者うざい",
    "片輪のくせに", "盲目死ね", "耳聞こえないやつ", "ジンシュサベツ", "エスニッククレンジング", "ジェンダーサベツ", "セクシャルハラスメント",
    "コロス", "ブッコロス", "××に火をつける", "リンチしてやる", "テクトロニクス爆破", "サイタマヤキウチ",
    "ネットリンチ", "SNSで特定して", "住所特定", "家族に危害", "職場に電話",
    "エロ", "スケベ", "ちんこ見せろ", "まんこ舐めたい", "胸揉ませろ", "セクハラハツゲン", "わいせつ画像", "児童ポルノ",
    "ブス", "デブ", "ハゲ", "キモい顔", "ヨウシディス", "スメハラ", "パワハラ",
    "アヘン", "マリファナ栽培", "ケタミン", "ジュウトウホウイハン", "バクダンセイズウ", "イホウDLサイト", "コピー品販売",
    "自殺方法", "首吊り", "飛び降り", "集団自殺", "ヤクブツランヨウ",
    "クソヤロウ", "ゴミイカ", "ボケカス", "アホンダラ", "マヌケシネ", "クズニンゲン", "FUCK YOU", "SHIT HEAD",
    "DIE YOU", "GET LOST", "NOOB", "弱者男性", "老害", "厨房",
    "うんこ味", "ゲロ以下", "小便臭い", "マジ卍", "リア充爆発しろ", "インキャ死ね", "ネトウヨ", "パヨク",
    "ドジン", "コジキ", "きちがい手帳", "知的ショウガイシャ", "精神病院", "LGBTキモチワルイ", "同性愛は病気", "異性愛シジョウシュギ",
    "民族浄化", "人種隔離", "カーストセイド", "身分セイド", "部落差別", "在日特権", "ネトウヨシネ", "パヨクホロベ",
    "を刺す", "を殴る", "に硫酸", "ホウカヨコク", "バクハヨコク", "デントツ", "イヤガラセ", "ストーカー",
    "炎上させろ", "アカウントBAN", "IP特定",
    "ロリコン", "ショタコン", "近親相姦", "レイプ", "チカン", "トウサツ", "下着ドロボー", "セクシャルハラスメントやめろ",
    "容姿弄り", "年齢差別", "学歴差別", "職業差別", "貧乏人", "金持ち自慢",
    "ダッポウハーブ", "キケンハーブ", "裏サイト", "闇バイト", "個人情報売買", "偽造通貨", "児童買春",
    "自殺幇助", "安楽死", "カルトシュウキョウカンユウ", "悪徳ショウホウ",
    "クソイカ", "ゲスノキワミ", "ニンゲンノカス", "ノウミソクサッテル", "の犬", "売国奴", "非国民",
    "FUCKING BITCH", "MOTHERFUCKER", "ASSHOLE", "DUMBASS", "IDIOT",
    "きもいオタク", "腐女子", "喪女", "ツリ", "アオリウンテン", "メイワクコウイ", "ルールイハン", "BANマツリ",
    "アンチカツドウ", "シンジャ", "警察", "ジエン", "ナリスマシ",
    "系", "顔", "低学歴", "高学歴様", "貧乏人根性", "金持ちゲンカ", "人はゴキブリ", "人は害虫",
    "の血を引く", "純血シュギ", "異人", "蛮族", "土着民", "先住民", "植民地", "コンプレックス",
    "に爆破予告", "に無差別テロ", "を人質", "を拉致", "に硫酸かける",
    "サイバーテロ", "DDoSコウゲキ仕掛ける", "個人情報晒して炎上", "特定して凸する",
    "強制ワイセツ", "児童ポルノ製造", "トウサツマ", "下着ドロボー常習犯", "セクハラ社長", "パワハラ上司",
    "ルッキズム", "エイジングハラスメント", "マタニティハラスメント", "スメハラやめて",
    "イホウトバク", "ダツゼイシナン", "インサイダー取引", "マネーロンダリング", "悪質な情報商材", "マルチレベルマーケティング",
    "自殺サークル", "集団安楽死", "センノウ", "カルトカンユウ", "悪質デマ拡散",
    "クソオブクソ", "人間のクズオブクズ", "脳みそ腐肉", "のうんこ", "寄生虫", "ゴキブリ",
    "YOU ARE A DISGRACE", "YOU ARE A FAILURE", "GO TO HELL", "FUCKING MORON",
    "きもいアニメアイコン", "声豚", "ドルオタ", "鉄オタ", "撮り鉄", "迷惑ユーチューバー", "炎上マーケティング",
    "アンチスパイラルサイシュウケイタイ", "シンジャボクメツ", "警察ホンブ", "ジエンオツｗｗｗ", "ナリスマシサギシ",
    "の血", "の末裔", "純粋な", "混血", "アイノコ", "性的少数者は税金の無駄", "障がい者手当不正受給",
    "人は全員泥棒", "人は嘘つき", "の文化は野蛮", "劣等人種", "優生思想", "ヘイトクライム",
    "に爆弾仕掛けた", "を誘拐する", "を監禁", "集団でを襲撃", "の家を燃やす",
    "サイバーコウゲキ", "DoSコウゲキ", "個人情報特定して脅迫", "リベンジポルノ",
    "JK売春", "AV女優になれ", "レイプ未遂", "強制ワイセツ", "チカンエンザイ", "ストーカーコウイヤメロ",
    "容姿差別やめろハゲ", "ブスオブブス", "醜い", "気持ち悪い体", "セクハラハツゲンユルサナイ",
    "麻薬密売", "拳銃密造", "爆弾テロ", "児童ポルノ販売", "個人情報ダダ漏れ", "クレジットカード詐欺",
    "自殺サイト", "練炭自殺", "飛び降り自殺の名所", "カルト集団", "マルチショウホウカンユウ",
    "クソオブクソオブクソ", "人間のクズオブクズオブクズ", "脳みそ腐乱", "のゲロ", "社会の寄生虫", "国賊",
    "YOU ARE A PIECE OF SHIT", "YOU ARE A DISGUSTING CREATURE", "GO ROT IN HELL", "FUCKING RETARDED SCUM",
    "きもい腐れオタク", "声豚死ね", "ドルオタきもい", "鉄オタウザい", "撮り鉄犯罪者", "迷惑系ユーチューバー", "炎上商法オツ",
    "アンチスパイラルサイシュウケイタイ", "シンジャボクメツ", "警察ホンブ", "ジエンオツｗｗｗ", "ナリスマシサギシ",
    "池沼晒し", "糖質", "発達障害", "アスペ", "ADHD", "境界知能",
    "は脳みそ腐ってる", "は売国奴",
    "在日コリアンはゴキブリ", "アイヌは絶滅しろ",
    "ホモは AIDS", "レズは気持ち悪い",
    "女は家で飯作ってろ", "男は稼いでこい",
    "ざまあ",
    "叩き",
    "の信者はキモい",
    "はブラック",
    "土人",
    "はクズ",
    "は老害",
    "はバカ",
    "はデブ",
    "はブス",
    "はキモい",
    "ね",
    "＋侮蔑語",
    "＋侮蔑語",
]

# loggingの設定
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(levelname)s - %(message)s')

@bot.event
async def on_ready():
    logging.info(f'{bot.user.name} が起動しました (ID: {bot.user.id})')
    await bot.tree.sync()
    logging.info('グローバルアプリケーションコマンドを同期しました。')

@bot.event
async def on_message(message):
    if message.author == bot.user:
        return

    if message.channel.id in EXCLUDED_CHANNEL_IDS:
        return

    deleted = False
    for keyword in restricted_keywords:
        if keyword in message.content:
            try:
                await message.delete()
                deleted = True
                logging.info(f'メッセージを削除しました: ユーザー={message.author.name}#{message.author.discriminator} (ID: {message.author.id}), チャンネル={message.channel.name} (ID: {message.channel.id}), キーワード="{keyword}"')
                log_channel = bot.get_channel(LOG_CHANNEL_ID)
                if log_channel:
                    timestamp = datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')
                    log_message = f'[{timestamp}] 削除: {message.author.name}#{message.author.discriminator} (ID: {message.author.id}) - チャンネル: {message.channel.name} (ID: {message.channel.id}) - キーワード: "{keyword}" - 内容非表示'
                    await log_channel.send(log_message)
                else:
                    logging.warning(f'ログチャンネル ({LOG_CHANNEL_ID}) が見つかりません。')
            except discord.Forbidden:
                logging.error(f'メッセージ削除権限がありません: ユーザー={message.author.name}#{message.author.discriminator} (ID: {message.author.id}), チャンネル={message.channel.name} (ID: {message.channel.id})')
            except discord.NotFound:
                logging.error(f'削除しようとしたメッセージが見つかりません: メッセージID={message.id}')
            except Exception as e:
                logging.error(f'メッセージ削除中にエラーが発生しました: {e}')
            break  # 最初のキーワード一致で削除

    await bot.process_commands(message)

@bot.event
async def on_message_delete(message):
    if message.author == bot.user:
        return

    if message.channel.id in EXCLUDED_CHANNEL_IDS:
        return

    deleted_author = message.author
    deleted_channel = message.channel
    deleted_content = message.content

    # ログチャンネルにログを送信
    log_channel = bot.get_channel(LOG_CHANNEL_ID)
    if log_channel:
        timestamp = datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')
        log_message = f'[{timestamp}] 削除: {deleted_author.name}#{deleted_author.discriminator} (ID: {deleted_author.id}) - チャンネル: {deleted_channel.name} (ID: {deleted_channel.id})'
        await log_channel.send(log_message)
    else:
        logging.warning(f'ログチャンネル ({LOG_CHANNEL_ID}) が見つかりません。')

@bot.tree.command(name='stop', description='Botを安全に停止します。')
async def stop_command(interaction: discord.Interaction):
    await interaction.response.send_message('Botを停止します...', ephemeral=True)
    logging.info('Botを停止します。')
    await bot.close()

if __name__ == "__main__":
    if TOKEN:
        bot.run(TOKEN)
    else:
        logging.error('DISCORD_TOKEN が設定されていません。')
