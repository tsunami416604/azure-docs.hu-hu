---
title: Honosítástámogatása | Microsoft Azure Maps
description: Ebben a cikkben megismerheti a Microsoft Azure Maps szolgáltatásainak támogatott nyelveit.
author: philmea
ms.author: philmea
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3b3733dfc32dae21ddcf4c5f73cddf9ad6b7fc59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334070"
---
# <a name="localization-support-in-azure-maps"></a>Honosítástámogatása az Azure Mapsben

Az Azure Maps az országon és régión alapuló különböző nyelveket és nézeteket támogatja. Ez a cikk a támogatott nyelveket és nézeteket tartalmazza az Azure Maps megvalósításának irányításához.


## <a name="azure-maps-supported-languages"></a>Az Azure Maps által támogatott nyelvek

Az Azure Maps különböző nyelveken van honosítva a szolgáltatásaiban. Az alábbi táblázat az egyes szolgáltatások támogatott nyelvi kódjait tartalmazza.  
  

| ID (Azonosító)         | Név                   |  Maps | Keresés | Útválasztás | Időjárás | Közlekedési események | JS térkép vezérlése |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | búr              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | Arab                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| bn-BD      | Bangla (Banglades)    |       |       |         |     ✓    |                   |                |
| bn-IN      | Bangla (India)         |       |       |         |     ✓    |                   |                |
| bs-BA      | boszniai                 |       |       |         |     ✓    |                   |                |
| eu-ES      | Baszk                 |       |    ✓   |         |         |                   |                |
| bg-BG      | Bolgár              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| ca-ES      | Katalán                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS között    | Kínai (egyszerűsített)   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Kínai (Hongkong KKT)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | Kínai (Tajvan)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| hr-HR      | Horvát               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | Cseh                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | Dán                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| nl-BE      | Holland (Belgium)        |       |    ✓   |         |      ✓    |                   |                |
| nl-NL      | Holland (Hollandia)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-AU      | Angol (Ausztrália)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-NZ      | Angol (Új-Zéland)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| hu-GB      | Angol (Nagy-Britannia) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-US      | Angol (USA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | Észt               |       |    ✓   |         |      ✓    |         ✓         |                |
| fil-PH     | Filipino               |       |       |         |     ✓    |                   |                |
| fi-FI      | Finn                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-FR      | Francia                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| fr-CA      | Francia (Kanada)      |       |    ✓   |         |     ✓     |                   |                |
| gl-ES      | Gallego               |       |    ✓   |         |         |                   |                |
| de-DE      | Német                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el-GR      | Görög                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| gu-IN      | gudzsaráti                |       |       |         |     ✓    |                   |                |
| he-IL      | Héber                 |       |    ✓   |         |     ✓    |         ✓         |                |
| hi-IN      | Hindi                  |       |        |         |     ✓    |                   |                |
| hu-HU      | Magyar              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| is-IS      | Izlandi              |       |       |         |     ✓    |                   |                |
| id-ID      | Indonéz             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | Olasz                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | Japán               |       |        |         |     ✓    |                   |                |
| kn-IN      | kannada                |       |       |         |     ✓    |                   |                |
| kk-KZ      | Kazak                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | Koreai                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | Latin-amerikai spanyol |       |    ✓   |         |         |                   |                |
| lv-LV      | Lett                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | Litván             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| mk-MK      | Macedón             |       |       |         |     ✓    |                   |                |
| ms-MY      | Maláj (latin betűs)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| mr-IN      | marathi                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norvég (bokmål)       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Semleges földi igazság - Hivatalos nyelvek a helyi szkriptek minden régiójában, ha rendelkezésre állnak |   ✓     |        |         |       |        |      ✓          |
| NGT-Latn között   | Semleges Földi Igazság - Latin exonyms. Latin script lesz használva, ha rendelkezésre áll |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | Lengyel                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | Portugál (Brazília)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| pt-PT      | Portugál (Portugália)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| pa-IN      | pandzsábi                 |       |       |         |     ✓    |                   |                |
| ro-RO      | Román               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | Orosz                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | Szerb (cirill betűs)     |       |   sr-RS  |         |    sr-RS     |                   |                |
| sr-Latn-RS | Szerb (latin betűs)        |       |       |         |     sr-latn    |                   |                |
| sk-SK      | Szlovák             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| sl-SL      | Szlovén              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | Spanyol                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | Spanyol (Mexikó)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| sv-SE      | Svéd                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| ta-IN      | Tamil (India)                 |       |       |         |     ✓    |                   |                |
| te-IN      | Telugu (India)                 |       |       |         |     ✓    |                   |                |
| th-TH      | Thai                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | Török                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| uk-UA      | Ukrán               |       |    ✓   |         |     ✓    |                   |                |
| a-PK      | urdu                 |       |       |         |     ✓    |                   |                |
| uz-Latn-UZ | Üzbég                 |       |       |         |     ✓    |                   |                |
| vi-VN      | Vietnami             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Az Azure Maps által támogatott nézetek

> [!Note]
> 2019. augusztus 1-jén megjelent az Azure Maps a következő országokban/régiókban:
>  * Argentína
>  * India
>  * Marokkó
>  * Pakisztán
>
> 2019. augusztus 1-je után a **Nézet** paraméter meghatározza a fent felsorolt új régiók/országok visszaadott térképtartalmát. Az Azure Maps **View** paraméter (más néven "felhasználói régió paraméter") egy kétbetűs ISO-3166 országkód, amely megjeleníti a megfelelő leképezések az adott ország/régió, amely meghatározza, hogy mely geopolitikailag vitatott tartalom vissza az Azure Maps szolgáltatások, beleértve a határok at és címkék jelennek meg a térképen. 

Győződjön meg arról, hogy **beállította** a View paramétert a REST API-k és az SDK-k, amelyek a szolgáltatások használata.
>  
>
>  **Rest API-k:**
>  
>  Győződjön meg arról, hogy szükség szerint beállította a Nézet paramétert. A View paraméter azt határozza meg, hogy a geopolitikailag vitatott tartalom mely készletét adja vissza az Azure Maps-szolgáltatások. 
>
>  Érintett Azure Maps REST-szolgáltatások:
>    
>    * Térképcsempe beszerezése
>    * Térképkép beszerezése 
>    * Keresés fuzzy
>    * Keresési ÉP-ok beszerezni
>    * Keresési ÉP kategória beszereznie
>    * Keresés a közelben
>    * Keresési cím beszerezni
>    * Strukturált keresési cím beszerezni
>    * A keresési cím sztornírozása
>    * Keresési cím beszerezése Fordított keresztutca
>    * Keresés a geometrián belül
>    * Keresési cím közzétételi tételének előnézete
>    * Keresési cím könyvelése fordított kötegelt előnézet
>    * Keresés utáni útvonal
>    * Keresés utáni homályos köteg előnézete
>
>    
>  **SDK-k:**
>
>  Győződjön meg arról, hogy beállította a **Nézet** paramétert, és a Web SDK és az Android SDK legújabb verziójával rendelkezik. Érintett SDK-k:
>
>    * Azure Maps Webes SDK
>    * Azure Maps Android SDK

Alapértelmezés szerint a Nézet paraméter **egységes,** akkor is, ha még nem definiálta azt a kérelemben. Határozza meg a felhasználók helyét. Ezután állítsa be helyesen a **View** paramétert az adott helyhez. Másik lehetőségként beállíthatja a "View=Auto" parancsot, amely a kérelem IP-címe alapján adja vissza a térképadatokat.  Az Azure Maps **Nézet** paraméterét a vonatkozó jogszabályoknak megfelelően kell használni, beleértve azokat az ország leképezésére vonatkozó jogszabályokat, ahol a térképek, a képek és egyéb adatok és a harmadik féltől származó tartalmak elérhetővé válikk az Azure Maps-en keresztül.


Az alábbi táblázat támogatott nézeteket tartalmaz.

| Nézet         | Leírás                            |  Maps | Keresés | JS térképvezérlő |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Egyesült Arab Emírségek (arab nézet)    |   ✓   |        |     ✓          |
| AR           | Argentína (argentin nézet)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrein (arab nézet)                 |   ✓   |        |     ✓          |
| IN           | India (indiai nézet)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Irak (arab nézet)                    |   ✓   |        |     ✓          |
| JO           | Jordánia (arab nézet)                  |   ✓   |        |     ✓          |
| KW           | Kuvait (arab nézet)                  |   ✓   |        |     ✓          |
| LB           | Libanon (arab nézet)                 |   ✓   |        |     ✓          |
| MA           | Marokkó (marokkói nézet)                |   ✓   |   ✓     |     ✓          |
| OM           | Omán (arab nézet)                    |   ✓   |        |     ✓          |
| PK           | Pakisztán (pakisztáni nézet)              |   ✓   |    ✓    |     ✓          |
| PS           | Palesztin Hatóság (arab nézet)    |   ✓   |        |     ✓          |
| QA           | Katar (arab nézet)                   |   ✓   |        |     ✓          |
| SA           | Szaúd-Arábia (arab nézet)            |   ✓   |        |     ✓          |
| SY           | Szíria (arab nézet)                   |   ✓   |        |     ✓          |
| Ti           | Jemen (arab nézet)                   |   ✓   |        |     ✓          |
| Automatikus         | Adja vissza a térképadatokat a kérelem IP-címe alapján.|   ✓   |    ✓   |     ✓          |
| Egységes      | Egyesített nézet (egyéb)                  |   ✓   |   ✓     |     ✓          |
