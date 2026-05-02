<div align="center">

# Fast Tunnel VPN

**Стабильный, быстрый VPN-клиент для Windows.**

[![Latest Release](https://img.shields.io/github/v/release/DuminAndrew/fasttunnel-installers?label=latest&style=for-the-badge&color=2EA043)](https://github.com/DuminAndrew/fasttunnel-installers/releases/latest)
[![Platform](https://img.shields.io/badge/platform-Windows%2010%20%7C%2011-0078D6?style=for-the-badge&logo=windows&logoColor=white)](#системные-требования)
[![Signed](https://img.shields.io/badge/signature-Ed25519-1C412F?style=for-the-badge&logo=letsencrypt&logoColor=white)](#проверка-подписи)
[![Downloads](https://img.shields.io/github/downloads/DuminAndrew/fasttunnel-installers/total?style=for-the-badge&color=171717)](https://github.com/DuminAndrew/fasttunnel-installers/releases)

</div>

---

## Что это

Это **зеркало с установщиками** для Fast Tunnel VPN. Здесь живут только бинарники — `.exe`, его Ed25519-подпись и `latest.json` для авто-обновления. Сам исходный код **остаётся приватным** в основном репозитории.

Если ты пользователь — просто [скачай свежий релиз](https://github.com/DuminAndrew/fasttunnel-installers/releases/latest) и установи.

## Скачать

<table>
<tr>
<th>Платформа</th>
<th>Файл</th>
<th>Установка</th>
</tr>
<tr>
<td>🪟 Windows 10 / 11 (x64)</td>
<td><a href="https://github.com/DuminAndrew/fasttunnel-installers/releases/latest"><code>fasttunnel-windows-X.Y.Z.exe</code></a></td>
<td>Двойной клик → согласиться на UAC → Next → Install</td>
</tr>
</table>

> 🛡️ **При первом запуске Windows покажет UAC-промпт** — это нормально и обязательно. VPN-клиент создаёт TUN-устройство и настраивает firewall-правила, для этого нужны права администратора.

## Системные требования

| Компонент | Минимум |
|---|---|
| ОС | Windows 10 (1809+) или Windows 11 |
| Архитектура | x64 |
| RAM | 256 MB свободной |
| Диск | 60 MB |
| Интернет | Любой (Ethernet / Wi-Fi / 4G) |
| Права | Администратор для установки и работы |

## Возможности

- ⚡ **Быстрое подключение** — `< 8 секунд` от клика до защищённого туннеля.
- 🔄 **Auto-reconnect** — автоматически восстанавливает соединение при смене Wi-Fi или возврате из сна.
- 🌐 **Split tunneling** — выбор приложений, доменов или IP-диапазонов, идущих мимо VPN.
- 🏢 **Поддержка корпоративных сетей** — Active Directory и `*.local` домены автоматически идут на ваш локальный DNS, не ломая рабочий доступ.
- 🛡️ **Kill switch** — если туннель упал, ни один пакет наружу не уйдёт.
- 🔐 **VLESS + Reality** — современный protocol-stack, устойчивый к DPI.
- 📡 **Pre-connect health probe** — клиент проверяет сервер до подключения, не тратит время на заведомо-битый.
- 🌍 **Server picker с латенцией** — выбирай ближайший сервер, и Auto-режим сам найдёт лучший.
- 📊 **Прозрачная диагностика** — на любой ошибке кнопка `Diagnose` собирает структурированный отчёт.
- 🔄 **OTA-обновления** — установка обновляется сама, проверяя Ed25519-подпись каждого билда.

## Авто-обновления (OTA)

Установщик настраивает приложение так, чтобы оно проверяло обновления при каждом запуске и фоном раз в час.

Канал доставки:

1. **Основной** — Control Plane сервер (HTTPS).
2. **Резерв** — этот публичный mirror (если CP недоступен).
3. **Локальный кэш** — последний успешно скачанный установщик (если оба верхних канала упали).

Каждое обновление проверяется по **Ed25519-подписи** перед запуском. Подмена `.exe` на пути к пользователю — невозможна.

## Проверка подписи

Каждый релиз содержит:

```
fasttunnel-windows-X.Y.Z.exe       ← установщик
fasttunnel-windows-X.Y.Z.exe.sig   ← Ed25519-подпись SHA-256(exe)
latest.json                         ← discovery-файл для OTA
```

Проверить вручную (если параноишь — это нормально):

```powershell
# Хеш установщика
$exe = "fasttunnel-windows-0.6.2.exe"
$sig = "$exe.sig"
$hash = [System.IO.File]::ReadAllBytes($exe) | ForEach-Object { [System.Security.Cryptography.SHA256]::Create().ComputeHash($_) }

# Verify через openssl (нужен публичный ключ из docs/)
openssl pkeyutl -verify -pubin -inkey ota-public-key.pem -rawin -in hash.bin -sigfile $sig
# → Signature Verified Successfully
```

## Если что-то не работает

<details>
<summary><b>«Connection Error / All servers unreachable»</b></summary>

В приложении нажми <b>Diagnose</b> на экране ошибки — сгенерируется отчёт с версией, последними логами, статусом сети и AD-доменом. Скопируй и пришли в поддержку.

Частые причины:
<ul>
<li><b>DNS на сервере недоступен</b> — попробуй другой сервер из списка.</li>
<li><b>Firewall режет HTTPS-443</b> — типично для корпоративных сетей. Открой 443 наружу.</li>
<li><b>Антивирус мешает Wintun</b> — добавь Fast Tunnel в исключения.</li>
</ul>
</details>

<details>
<summary><b>«Update failed: Download failed…»</b></summary>

Скачай свежий установщик [вручную](https://github.com/DuminAndrew/fasttunnel-installers/releases/latest), установи поверх — настройки и токены сохранятся. Это разовый workaround для версий ≤ v0.6.x; начиная с v0.7.0 OTA сам падает на этот mirror.
</details>

<details>
<summary><b>«Administrator privileges required»</b></summary>

Перезапусти приложение от имени администратора (правый клик → «Запуск от имени администратора»). Без admin-прав VPN физически не может создать TUN-устройство.

Если регулярно — проверь манифест: начиная с v0.5.1 установщик сам запрашивает elevation, UAC промпт появляется автоматически.
</details>

<details>
<summary><b>VPN подключился, но интернет не работает</b></summary>

Скорее всего DNS-туннель сломан на конкретном сервере. В версиях v0.7.0+ клиент сам это детектит и переключается на другой сервер за пару секунд. Если ты на старой версии — выбери другой сервер вручную или обнови приложение.
</details>

<details>
<summary><b>В корпоративной сети ломается доступ к рабочим ресурсам</b></summary>

С v0.7.0 включена авто-bypass для AD-доменов и RFC1918 диапазонов: всё, что заканчивается на `<your-ad-domain>`, `*.local`, или попадает в `10.x`, `192.168.x`, `172.16-31.x` — идёт мимо VPN на твой системный DNS.

Проверь в `Settings → Split Tunneling` чип «Auto-bypass: …» — если виден, значит работает. Если нет — обнови до v0.7.0+.
</details>

## Старые версии

Все релизы здесь: https://github.com/DuminAndrew/fasttunnel-installers/releases

Если откатываешься на старую версию — учти, что **OTA не понизит тебя обратно** (downgrade prevention). Установщик старой версии нужно запустить вручную.

## Конфиденциальность

- Источники, инфраструктура и операционные данные — в **приватном** репозитории.
- Здесь только установочные артефакты, которые и так попадают на твой компьютер.
- Никаких токенов, ключей, конфигов сервера в этом репо нет и не будет.
- Отчёт `Diagnose` не отправляется автоматически — только если ты сам нажмёшь «Send to support».

## Поддержка

- 📧 Поддержка в приложении: `Settings → Support`
- 🐛 Баг-репорт по приложению: пиши в поддержку из приложения с прикреплённым отчётом `Diagnose`
- 🌐 Документация по архитектуре: только для разработчиков (приватный repo)

## Лицензия

Установочные файлы распространяются «как есть». Использование Fast Tunnel VPN регулируется внутренним пользовательским соглашением, доступным в приложении (`Settings → About → User Agreement`).

---

<div align="center">

**Fast Tunnel** — потому что туннель должен быть быстрым.

</div>
