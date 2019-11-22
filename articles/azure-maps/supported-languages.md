---
title: Honosítási támogatás a Azure Mapsban | Microsoft Docs
description: Ismerje meg Azure Maps szolgáltatásainak támogatott nyelveit
author: walsehgal
ms.author: v-musehg
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 64197cc85822997926a8011af8cb3b981fa9064d
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286421"
---
# <a name="localization-support-in-azure-maps"></a>Honosítási támogatás Azure Maps

A Azure Maps ország/régió alapján támogatja a különböző nyelveket és nézeteket. Ez a cikk a támogatott nyelveket és nézeteket ismerteti, amelyek segítenek a Azure Maps megvalósításában.


## <a name="azure-maps-supported-languages"></a>Azure Maps támogatott nyelvek

A Azure Maps különböző nyelveken lettek honosítva a szolgáltatások között. Az alábbi táblázat az egyes szolgáltatásokhoz támogatott nyelvi kódokat tartalmazza.  
  

| ID (Azonosító)         | Name (Név)                   |  Maps | Keresés | Útválasztás | Időjárás | Forgalmi incidensek | JS Térkép vezérlőelem |
|------------|------------------------|:-----:|:------:|:-------:|:--------:|:-----------------:|:--------------:|
| af-ZA      | afrikaans              |       |    ✓   |    ✓    |         |                   |                |
| ar-SA      | arab                 |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| BN – BD      | Bangla (Banglades)    |       |       |         |     ✓    |                   |                |
| BN – a      | Bangla (India)         |       |       |         |     ✓    |                   |                |
| BS-BA      | boszniai                 |       |       |         |     ✓    |                   |                |
| eu-ES      | baszk                 |       |    ✓   |         |         |                   |                |
| bg-BG      | bolgár              |   ✓   |    ✓   |    ✓    |     ✓     |                   |        ✓       |
| CA-ES      | katalán                |       |    ✓   |         |    ✓      |                   |                |
| zh-HanS    | kínai (egyszerűsített)   |       |  zh-CN |         |     zh-CN   |                   |                |
| zh-HanT    | Kínai (Hongkong KKT)  |  |   |    |    zh-HK   |                   |           |
| zh-HanT    | kínai (tajvani)  | zh-TW |  zh-TW |  zh-TW  |    zh-TW   |                   |      zh-TW     |
| hr-HR      | horvát               |       |    ✓   |         |    ✓      |                   |                |
| cs-CZ      | cseh                  |   ✓   |    ✓   |    ✓    |    ✓      |         ✓         |        ✓       |
| da-DK      | dán                 |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| nl-BE      | Holland (Belgium)        |       |    ✓   |         |      ✓    |                   |                |
| NL-NL      | holland (Hollandia)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-Ausztrália      | angol (Ausztrália)    |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-NZ      | angol (Új-Zéland)  |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-GB      | Angol (Nagy-Britannia) |   ✓   |    ✓   |    ✓    |     ✓     |         ✓         |        ✓       |
| en-US      | Angol (USA)          |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| et-EE      | észt               |       |    ✓   |         |      ✓    |         ✓         |                |
| Fil – PH     | filippínó               |       |       |         |     ✓    |                   |                |
| fi-FI      | finn                |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| FR-FR      | francia                 |   ✓   |    ✓   |    ✓    |      ✓    |         ✓         |        ✓       |
| FR-hitelesítésszolgáltató      | francia (Kanada)      |       |    ✓   |         |     ✓     |                   |                |
| GL-ES      | galíciai               |       |    ✓   |         |         |                   |                |
| de-DE      | német                 |   ✓   |    ✓   |    ✓    |   ✓      |         ✓         |        ✓       |
| el-GR      | görög                  |   ✓   |    ✓   |    ✓    |    ✓     |         ✓         |        ✓       |
| Gu – a      | gudzsaráti                |       |       |         |     ✓    |                   |                |
| He-IL      | héber                 |       |    ✓   |         |     ✓    |         ✓         |                |
| Üdvözöljük – India      | hindi                  |       |        |         |     ✓    |                   |                |
| hu-HU      | magyar              |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| a ()      | izlandi              |       |       |         |     ✓    |                   |                |
| ID-Azonosítóját      | indonéz             |   ✓   |    ✓    |    ✓    |     ✓    |         ✓         |        ✓       |
| it-IT      | olasz                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| ja-JP      | japán               |       |        |         |     ✓    |                   |                |
| KN – a      | kannada                |       |       |         |     ✓    |                   |                |
| kk-KZ      | kazak                 |       |    ✓   |         |     ✓    |                   |                |
| ko-KR      | koreai                 |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| es-419     | Latin-amerikai spanyol |       |    ✓   |         |         |                   |                |
| lv-LV      | lett                |       |    ✓   |         |     ✓    |         ✓         |                |
| lt-LT      | litván             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| MK-MK      | Macedón             |       |       |         |     ✓    |                   |                |
| MS-saját      | Maláj (latin betűs)          |   ✓   |    ✓   |    ✓    |    ✓   |                   |        ✓       |
| Mr-IN      | marathi                 |       |       |         |     ✓    |                   |                |
| nb-NO      | Norvég nyelven       |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| NGT        | Semleges földelési igazság – hivatalos nyelvek a helyi parancsfájlok minden régiója számára, ha elérhető |   ✓     |        |         |       |        |      ✓          |
| NGT – Latn   | Semleges földelési igazság – latin exonyms. Ha elérhető, a latin szkriptet fogja használni |   ✓     |        |         |         |                |        ✓         |
| pl-PL      | lengyel                 |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| pt-BR      | portugál (brazíliai)    |   ✓   |    ✓   |    ✓    |      ✓   |                   |        ✓       |
| PT-PT      | portugál (általános)  |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| PA-IN      | pandzsábi                 |       |       |         |     ✓    |                   |                |
| ro-RO      | román               |       |    ✓    |         |     ✓    |         ✓         |                |
| ru-RU      | orosz                |   ✓   |    ✓   |    ✓    |      ✓   |         ✓         |        ✓       |
| sr-Cyrl-RS | szerb (cirill betűs)     |       |   SR-RS  |         |    SR-RS     |                   |                |
| sr-Latn-RS | szerb (latin betűs)        |       |       |         |     SR-Latn    |                   |                |
| SK-SK      | szlovák             |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| sl-SL      | szlovén              |   ✓   |    ✓   |    ✓    |     ✓    |                   |        ✓       |
| es-ES      | spanyol                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| es-MX      | spanyol (Mexikó)       |   ✓   |        |    ✓    |     ✓    |                   |        ✓       |
| SV-SE      | svéd                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| TA-India      | tamil (India)                 |       |       |         |     ✓    |                   |                |
| Te-India      | telugu (India)                 |       |       |         |     ✓    |                   |                |
| cs-EDIK      | thai                   |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| tr-TR      | török                |   ✓   |    ✓   |    ✓    |     ✓    |         ✓         |        ✓       |
| Egyesült Királyság – UA      | ukrán               |       |    ✓   |         |     ✓    |                   |                |
| az Ön-PK      | urdu                 |       |       |         |     ✓    |                   |                |
| Uz-Latn-UZ | Üzbég                 |       |       |         |     ✓    |                   |                |
| vi-VN      | vietnami             |       |    ✓   |         |      ✓    |                  |                |


## <a name="azure-maps-supported-views"></a>Azure Maps támogatott nézetek

> [!Note]
> A Azure Maps a következő országokban/régiókban jelent meg, 2019. augusztus 1-jén:
>  * Argentína
>  * India
>  * Marokkó
>  * Pakisztán
>
> 2019. augusztus 1-től a paraméterek **megtekintése** beállítás megadja a visszaadott leképezési tartalmat a fent felsorolt új régiókban/országokban. Javasoljuk, hogy gondoskodjon arról, hogy a szolgáltatás által használt REST API-khoz és SDK-khoz szükség szerint állítsa be a View paramétert.
>  
>
>  **REST API-k:**
>  
>  Győződjön meg arról, hogy szükség szerint beállította a View paramétert. A View paraméter azt határozza meg, hogy a rendszer mely geopolitikai jogvitás tartalmat adja vissza Azure Maps szolgáltatásokon keresztül. 
>
>  Érintett Azure Maps REST-szolgáltatások:
>    
>    * Térkép lekérése csempe
>    * Térkép-rendszerkép lekérése 
>    * Keresés – fuzzy
>    * Keresési célobjektum beolvasása
>    * Keresési célobjektum kategóriájának beolvasása
>    * Keresés a közelben
>    * Keresési címek beolvasása
>    * Felépített keresési címek beolvasása
>    * Fordított keresési címek lekérése
>    * A keresési címek fordított kereszt utcájának beolvasása
>    * Keresés közzététele a geometrián belül
>    * A Search-címek batch-előnézetének közzététele
>    * Keresési címe fordított batch előzetes verziójának közzététele
>    * Keresés közzététele az útvonal mentén
>    * A Search fuzzy batch előzetes verziójának közzététele
>
>    
>  **SDK**
>
>  Győződjön meg arról, hogy szükség szerint állította be a View paramétert, és a web SDK és az Android SDK legújabb verziója van telepítve. Érintett SDK-k:
>
>    * Azure Maps Web SDK
>    * Azure Maps Android SDK


Azure Maps **View** paraméter (más néven "felhasználói régió paramétere") egy kétbetűs ISO-3166 országkód, amely az adott országhoz/régióhoz tartozó megfelelő térképeket jeleníti meg, amelyek meghatározzák, hogy a geopolitikai módon vitatott tartalom mely készletét adja vissza Azure Maps szolgáltatásokon keresztül, beleértve a térképen megjelenített szegélyeket és címkéket. 

Alapértelmezés szerint a View paraméter a **Unified**értékre van állítva, még akkor is, ha még nem adta meg azt a kérelemben. Az Ön felelőssége, hogy meghatározza a felhasználók helyét, majd az adott helyen helyesen állítsa be a View paramétert. Azt is megteheti, hogy a "View = automatikus" beállítást is megadja, amely a kérés IP-címe alapján visszaadja a térképi adatmennyiséget.  A Azure Mapsban szereplő View paramétert a vonatkozó törvényeknek megfelelően kell használni, beleértve azokat az országokat, amelyeken a Maps, a images és más adatokat, valamint a harmadik féltől származó, a Azure Maps-on keresztül való hozzáférésre jogosult külső tartalmat is elérhetővé teszi.


A következő táblázat a támogatott nézeteket tartalmazza.

| Nézet         | Leírás                            |  Maps | Keresés | JS térképkezelés |
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
| PS           | Palesztin hatóság (arab nézet)    |   ✓   |        |     ✓          |
| QA           | Katar (arab nézet)                   |   ✓   |        |     ✓          |
| SA           | Szaúd-Arábia (arab nézet)            |   ✓   |        |     ✓          |
| SY           | Szíria (arab nézet)                   |   ✓   |        |     ✓          |
| TI           | Jemeni (arab nézet)                   |   ✓   |        |     ✓          |
| automatikus         | A leképezési adatmennyiséget a kérés IP-címe alapján küldi vissza.|   ✓   |    ✓   |     ✓          |
| Egységes      | Egyesített nézet (egyéb)                  |   ✓   |   ✓     |     ✓          |
