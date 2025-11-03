import logging
from telegram import Update, ReplyKeyboardMarkup, ReplyKeyboardRemove
from telegram.ext import Application, CommandHandler, MessageHandler, filters, ContextTypes, ConversationHandler
from datetime import datetime
import os

# Configuración

logging.basicConfig(format=’%(asctime)s - %(name)s - %(levelname)s - %(message)s’, level=logging.INFO)
logger = logging.getLogger(**name**)

# Estados

MENU, NOMBRE, TELEFONO, DIRECCION, INTERES, NOTAS = range(6)

# Base de datos

prospectos_db = []

# Scripts

SCRIPT_LLAMADA = “”“📞 SCRIPT - LLAMADA FRÍA

👋 APERTURA:
“Buenos días, ¿hablo con [NOMBRE]?
Soy [TU NOMBRE] de SAFTI.
Le llamo porque buscamos propiedades en [ZONA].”

🎯 PREGUNTA:
“¿Ha pensado en vender su propiedad?”

💬 SI NO INTERESA:
“¿Al menos le gustaría conocer el valor actual?”

📅 CIERRE:
“¿Le parece bien si paso mañana para valoración gratuita?”
“””

SCRIPT_PUERTA = “”“🚪 SCRIPT - PUERTA A PUERTA

👋 PRESENTACIÓN:
“Hola, soy [NOMBRE] de SAFTI.
Visito esta zona buscando propiedades.”

🎯 PREGUNTA:
“¿Es usted el propietario?”

💡 TÉCNICA:
“Su vecino ya nos dio datos. El mercado está activo aquí.”

📋 CIERRE:
“¿Me permite anotar su teléfono? Sin compromiso.”
“””

def guardar_prospecto(datos):
prospecto = {
‘id’: len(prospectos_db) + 1,
‘nombre’: datos.get(‘nombre’, ‘’),
‘telefono’: datos.get(‘telefono’, ‘’),
‘direccion’: datos.get(‘direccion’, ‘’),
‘tipo’: datos.get(‘tipo’, ‘’),
‘interes’: datos.get(‘interes’, ‘’),
‘fecha’: datetime.now().strftime(’%d/%m/%Y %H:%M’),
‘notas’: datos.get(‘notas’, ‘’)
}
prospectos_db.append(prospecto)
logger.info(f”Prospecto guardado: {prospecto[‘nombre’]}”)
return prospecto

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
keyboard = [
[‘📞 Llamada’, ‘🚪 Visita’],
[‘📋 Prospectos’, ‘📊 Resumen’],
[‘📝 Script Llamada’, ‘🚪 Script Puerta’]
]
reply_markup = ReplyKeyboardMarkup(keyboard, resize_keyboard=True)

```
mensaje = """🏠 *ASISTENTE SAFTI*
```

¡Hola! ¿Qué quieres hacer?

📞 *Llamada* - Registrar llamada
🚪 *Visita* - Registrar visita
📋 *Prospectos* - Ver lista
📊 *Resumen* - Estadísticas
📝 *Scripts* - Ver guiones”””

```
await update.message.reply_text(mensaje, parse_mode='Markdown', reply_markup=reply_markup)
return MENU
```

async def menu_handler(update: Update, context: ContextTypes.DEFAULT_TYPE):
texto = update.message.text

```
if '📞 Llamada' in texto:
    context.user_data['tipo'] = 'Llamada'
    await update.message.reply_text("📞 *REGISTRAR LLAMADA*\n\n¿Nombre del prospecto?", parse_mode='Markdown')
    return NOMBRE

elif '🚪 Visita' in texto:
    context.user_data['tipo'] = 'Visita'
    await update.message.reply_text("🚪 *REGISTRAR VISITA*\n\n¿Nombre del propietario?", parse_mode='Markdown')
    return NOMBRE

elif '📋 Prospectos' in texto:
    if not prospectos_db:
        await update.message.reply_text("📋 No hay prospectos registrados aún.")
    else:
        mensaje = "📋 *LISTA DE PROSPECTOS*\n\n"
        for p in prospectos_db[-10:]:
            mensaje += f"━━━━━━━━━━━━━━━━\n"
            mensaje += f"🆔 *#{p['id']}* {p['interes']}\n"
            mensaje += f"👤 {p['nombre']}\n"
            mensaje += f"📞 {p['telefono']}\n"
            mensaje += f"📍 {p['direccion']}\n"
            mensaje += f"🎯 {p['tipo']}\n"
            mensaje += f"📅 {p['fecha']}\n\n"
        mensaje += f"*Total: {len(prospectos_db)} prospectos*"
        await update.message.reply_text(mensaje, parse_mode='Markdown')
    return MENU

elif '📊 Resumen' in texto:
    hoy = datetime.now().strftime('%d/%m/%Y')
    prospectos_hoy = [p for p in prospectos_db if p['fecha'].startswith(hoy)]
    llamadas = len([p for p in prospectos_hoy if p['tipo'] == 'Llamada'])
    visitas = len([p for p in prospectos_hoy if p['tipo'] == 'Visita'])
    muy_interesados = len([p for p in prospectos_hoy if '🔥' in p['interes']])
    
    mensaje = f"""📊 *RESUMEN DEL DÍA*
```

📅 {hoy}

━━━━━━━━━━━━━━━━
📞 Llamadas: {llamadas}
🚪 Visitas: {visitas}
🔥 Muy interesados: {muy_interesados}
━━━━━━━━━━━━━━━━
📋 Total hoy: {len(prospectos_hoy)}
📈 Total general: {len(prospectos_db)}

*¡Sigue así!* 💪”””
await update.message.reply_text(mensaje, parse_mode=‘Markdown’)
return MENU

```
elif 'Script Llamada' in texto:
    await update.message.reply_text(SCRIPT_LLAMADA)
    return MENU

elif 'Script Puerta' in texto:
    await update.message.reply_text(SCRIPT_PUERTA)
    return MENU
```

async def registrar_nombre(update: Update, context: ContextTypes.DEFAULT_TYPE):
context.user_data[‘nombre’] = update.message.text
await update.message.reply_text(f”✅ Nombre: *{update.message.text}*\n\n¿Teléfono?”, parse_mode=‘Markdown’)
return TELEFONO

async def registrar_telefono(update: Update, context: ContextTypes.DEFAULT_TYPE):
context.user_data[‘telefono’] = update.message.text
await update.message.reply_text(f”✅ Teléfono: *{update.message.text}*\n\n¿Dirección de la propiedad?”, parse_mode=‘Markdown’)
return DIRECCION

async def registrar_direccion(update: Update, context: ContextTypes.DEFAULT_TYPE):
context.user_data[‘direccion’] = update.message.text

```
keyboard = [
    ['🔥 Muy Interesado', '👍 Interesado'],
    ['🤔 Dudoso', '❌ No Interesado']
]
reply_markup = ReplyKeyboardMarkup(keyboard, resize_keyboard=True, one_time_keyboard=True)

await update.message.reply_text(
    f"✅ Dirección: *{update.message.text}*\n\n¿Nivel de interés?",
    parse_mode='Markdown',
    reply_markup=reply_markup
)
return INTERES
```

async def registrar_interes(update: Update, context: ContextTypes.DEFAULT_TYPE):
context.user_data[‘interes’] = update.message.text
await update.message.reply_text(
f”✅ Interés: *{update.message.text}*\n\nNotas adicionales (o escribe ‘Ninguna’):”,
parse_mode=‘Markdown’,
reply_markup=ReplyKeyboardRemove()
)
return NOTAS

async def registrar_notas(update: Update, context: ContextTypes.DEFAULT_TYPE):
context.user_data[‘notas’] = update.message.text
prospecto = guardar_prospecto(context.user_data)

```
keyboard = [
    ['📞 Llamada', '🚪 Visita'],
    ['📋 Prospectos', '📊 Resumen']
]
reply_markup = ReplyKeyboardMarkup(keyboard, resize_keyboard=True)

mensaje = f"""✅ *PROSPECTO REGISTRADO*
```

━━━━━━━━━━━━━━━━
🆔 *ID:* {prospecto[‘id’]}
👤 *Nombre:* {prospecto[‘nombre’]}
📞 *Teléfono:* {prospecto[‘telefono’]}
📍 *Dirección:* {prospecto[‘direccion’]}
🎯 *Tipo:* {prospecto[‘tipo’]}
💡 *Interés:* {prospecto[‘interes’]}
📅 *Fecha:* {prospecto[‘fecha’]}
📝 *Notas:* {prospecto[‘notas’]}
━━━━━━━━━━━━━━━━

*¡Guardado exitosamente!* 🎉”””

```
await update.message.reply_text(mensaje, parse_mode='Markdown', reply_markup=reply_markup)
context.user_data.clear()
return MENU
```

def main():
TOKEN = os.getenv(‘TOKEN’)

```
if not TOKEN:
    logger.error("ERROR: No se encontró TOKEN en variables de entorno")
    print("❌ ERROR: Configura la variable TOKEN en Render")
    return

logger.info("Iniciando bot SAFTI...")

application = Application.builder().token(TOKEN).build()

conv_handler = ConversationHandler(
    entry_points=[CommandHandler('start', start)],
    states={
        MENU: [MessageHandler(filters.TEXT & ~filters.COMMAND, menu_handler)],
        NOMBRE: [MessageHandler(filters.TEXT & ~filters.COMMAND, registrar_nombre)],
        TELEFONO: [MessageHandler(filters.TEXT & ~filters.COMMAND, registrar_telefono)],
        DIRECCION: [MessageHandler(filters.TEXT & ~filters.COMMAND, registrar_direccion)],
        INTERES: [MessageHandler(filters.TEXT & ~filters.COMMAND, registrar_interes)],
        NOTAS: [MessageHandler(filters.TEXT & ~filters.COMMAND, registrar_notas)],
    },
    fallbacks=[CommandHandler('start', start)],
)

application.add_handler(conv_handler)

logger.info("✅ Bot SAFTI iniciado correctamente")
print("🤖 Bot SAFTI activo - Esperando mensajes...")

application.run_polling(allowed_updates=Update.ALL_TYPES)
```

if **name** == ‘**main**’:
main()
