# 🚌 BusJob – Developer Documentation

Vollständige Entwickler-Dokumentation für den ESX BusJob.
Diese README ist komplett in Dropdowns aufgebaut und eignet sich perfekt für GitHub.

Showcase: [Youtube](https://youtu.be/d4hCd5t_ssc)


---

<details>
<summary><strong>📘 Overview</strong></summary>

## Was ist der BusJob?
Der BusJob ist ein ESX-basierter Job, bei dem Spieler als Busfahrer arbeiten
und feste Routen mit mehreren Haltestellen abfahren.

## Features
- Mehrere Routen
- Haltestellen-System
- Bezahlung pro Halt
- Abschlussbonus
- Navigations-Blips
- Vollständig konfigurierbar

## Voraussetzungen
- ESX Legacy
- Empfohlen: OneSync
- Funktionierendes Job-System

</details>

---

<details>
<summary><strong>⚙️ Installation</strong></summary>

## Schritte
1. Script in den resources Ordner legen
2. In der server.cfg eintragen:

    ensure busjob

3. Job in der Datenbank anlegen:

    INSERT INTO jobs (name, label) VALUES ('bus', 'Busfahrer');

4. Server neu starten

</details>

---

<details>
<summary><strong>📁 File Structure</strong></summary>

    busjob/
    │
    ├── client/
    │   ├── main.lua
    │   ├── blips.lua
    │   └── routes.lua
    │
    ├── server/
    │   └── main.lua
    │
    ├── config.lua
    ├── fxmanifest.lua
    └── README.md

client:
- Routen
- Blips
- Marker
- UI & Spieler-Logik

server:
- Bezahlung
- Sicherheit
- Job-Checks

</details>

---

<details>
<summary><strong>🛠️ Configuration (config.lua)</strong></summary>

## Allgemein

    Config = {}
    Config.JobName = "bus"
    Config.Locale = "de"
    Config.Debug = false

Optionen:
- JobName → ESX Jobname
- Locale → Sprache
- Debug → Debug-Ausgaben

## Payment

    Config.PayPerStop = 120
    Config.FinishBonus = 500
    Config.PayAccount = "money" -- money | bank

</details>

---

<details>
<summary><strong>🛣️ Routes</strong></summary>

## Routen definieren

    Config.Routes = {
        [1] = {
            label = "Stadtzentrum",
            difficulty = "Einfach",
            stops = {
                vector3(435.1, -645.2, 28.7),
                vector3(210.4, -1020.5, 29.3),
                vector3(-305.7, -890.2, 31.1)
            }
        },

        [2] = {
            label = "Flughafen Express",
            difficulty = "Mittel",
            stops = {
                vector3(-1032.6, -2734.8, 20.1),
                vector3(-1150.4, -2850.2, 13.9)
            }
        }
    }

Hinweise:
- Reihenfolge der Stops = Fahrreihenfolge
- Beliebig viele Routen möglich
- Jede Route ist unabhängig

</details>

---

<details>
<summary><strong>👤 NPC & Locations</strong></summary>

## NPC

    Config.BusNPC = {
        model = "s_m_m_gentransport",
        coords = vector3(450.2, -630.3, 28.5),
        heading = 90.0
    }

## Bus Spawn

    Config.BusSpawn = {
        coords = vector3(462.3, -620.8, 28.4),
        heading = 180.0,
        model = "bus"
    }

</details>

---

<details>
<summary><strong>🚌 Vehicle System</strong></summary>

    ESX.Game.SpawnVehicle("bus", Config.BusSpawn.coords, Config.BusSpawn.heading, function(vehicle)
        TaskWarpPedIntoVehicle(PlayerPedId(), vehicle, -1)
        SetVehicleEngineOn(vehicle, true, true, false)
        SetVehicleNumberPlateText(vehicle, "BUSJOB")
        SetEntityAsMissionEntity(vehicle, true, true)
    end)

Sicherheit:
- Nur Job-Fahrzeug erlaubt
- Fahrzeug wird getrackt
- Despawn bei Job-Ende

</details>

---

<details>
<summary><strong>💰 Payment System</strong></summary>

## Bezahlung pro Halt

    TriggerServerEvent("busjob:payStop")

## Server

    RegisterNetEvent("busjob:payStop", function()
        local xPlayer = ESX.GetPlayerFromId(source)
        xPlayer.addAccountMoney(Config.PayAccount, Config.PayPerStop)
    end)

## Abschlussbonus

    xPlayer.addAccountMoney(Config.PayAccount, Config.FinishBonus)

</details>

---

<details>
<summary><strong>🗺️ Blips & UI</strong></summary>

    local blip = AddBlipForCoord(stopCoords)
    SetBlipSprite(blip, 513)
    SetBlipColour(blip, 22)
    SetBlipRoute(blip, true)
    SetBlipAsShortRange(blip, false)

</details>

---

<details>
<summary><strong>🔌 Events & Exports</strong></summary>

Client Events:
- busjob:startRoute
- busjob:nextStop
- busjob:finishRoute

Server Events:
- busjob:payStop
- busjob:payFinish

Export:

    exports("IsBusJobActive", function()
        return isOnRoute
    end)

</details>

---

<details>
<summary><strong>🔐 Permissions & Job Checks</strong></summary>

    if ESX.PlayerData.job.name ~= Config.JobName then
        ESX.ShowNotification("Du bist kein Busfahrer")
        return
    end

</details>

---

<details>
<summary><strong>🧩 Customization</strong></summary>

## Trinkgeld

    local tip = math.random(10, 50)
    xPlayer.addMoney(tip)

## XP System

    PlayerXP = PlayerXP + 1

## Strafen

    xPlayer.removeMoney(50)

</details>

---

<details>
<summary><strong>🛠️ Troubleshooting</strong></summary>

Häufige Fehler:
- Kein Geld → Server Event fehlt
- Blip weg → Referenz überschrieben
- NetID Fehler → MissionEntity fehlt

Debug:

    if Config.Debug then
        print("DEBUG: BusJob aktiv")
    end

</details>

---

<details>
<summary><strong>📄 Changelog</strong></summary>

v1.0.0
- Initial Release
- Routen-System
- Payment-System
- Blips

</details>

---

<details>
<summary><strong>👑 Credits & License</strong></summary>

Author: TheP4nda(Walter David)  
Framework: ESX  
Usage: Public

</details>

