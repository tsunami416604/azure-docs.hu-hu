---
title: Honosítási támogatás a Azure Mapsban | Microsoft Docs
description: Ismerje meg Azure Maps szolgáltatásainak támogatott nyelveit
author: walsehgal
ms.author: v-musehg
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b3382cffdc41685f8329a640aaf6c6c526375a83
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299838"
---
# <a name="localization-support-in-azure-maps"></a>Honosítási támogatás Azure Maps

A Azure Maps ország/régió alapján támogatja a különböző nyelveket és nézeteket. Ez a cikk a támogatott nyelveket és nézeteket ismerteti, amelyek segítenek a Azure Maps megvalósításában.


## <a name="azure-maps-supported-languages"></a>Azure Maps támogatott nyelvek

A Azure Maps különböző nyelveken lettek honosítva a szolgáltatások között. Az alábbi táblázat az egyes szolgáltatásokhoz támogatott nyelvi kódokat tartalmazza.  
  

| ID (Azonosító)         | Név                   |  Térképek | Search | Útvonaltervezés | Forgalmi incidensek | JS Térkép vezérlőelem | Időzóna |
|------------|------------------------|:-----:|:------:|:-------:|:-----------------:|:--------------:|:---------:|
| AF-ZA      | búr              |       |    ✓   |    ✓    |                   |                |     ✓     |
| AR-SA      | arab                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| EU – ES      | baszk                 |       |    ✓   |         |                   |                |     ✓     |
| BG – BG      | bolgár              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| CA-ES      | katalán                |       |    ✓   |         |                   |                |     ✓     |
| zh-HanS    | Kínai (egyszerűsített)   |       |  zh-CN |         |                   |                |     ✓     |
| zh-HanT    | Kínai (hagyományos)  | zh – TW |  zh – TW |  zh – TW  |                   |      Zh – TW     |     ✓     |
| HR – HR      | horvát               |       |    ✓   |         |                   |                |     ✓     |
| CS-CZ      | cseh                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| da-DK      | dán                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl-NL      | holland                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl-BE      | Holland (belga)        |       |    ✓   |         |                   |                |     ✓     |
| EN-AU      | Angol (ausztráliai)    |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| EN-NZ      | Angol (új-zélandi)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-GB      | Angol (Nagy-Britannia) |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-US      | Angol (USA)          |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| et-EE      | észt               |       |    ✓   |         |         ✓         |                |     ✓     |
| Fi-FI      | finn                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr – FR      | francia                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr – CA      | Francia (kanadai)      |       |    ✓   |         |                   |                |     ✓     |
| GL-ES      | galíciai               |       |    ✓   |         |                   |                |     ✓     |
| de-DE      | német                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| el-GR      | görög                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ő – IL      | héber                 |       |    ✓   |         |         ✓         |                |     ✓     |
| Hi-IN      | hindi                  |       |        |         |                   |                |     ✓     |
| hu – HU      | magyar              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| azonosító-azonosító      | indonéz             |   ✓   |    ✓    |    ✓    |         ✓         |        ✓       |     ✓     |
| informatikai      | olasz                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ja-JP      | japán               |       |        |         |                   |                |     ✓     |
| KK – KZ      | kazak                 |       |    ✓   |         |                   |                |     ✓     |
| ko-KR      | koreai                 |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| es-419     | Latin-amerikai spanyol |       |    ✓   |         |                   |                |     ✓     |
| lv-LV      | lett                |       |    ✓   |         |         ✓         |                |     ✓     |
| lt-LT      | litván             |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| MS-MY      | Maláj (latin betűs)          |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| NB-nem      | Norvég nyelven       |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NGT        | Semleges földelési igazság – hivatalos nyelvek a helyi parancsfájlok minden régiója számára, ha elérhető |   ✓     |        |         |                   |      ✓          |         |
| NGT – Latn   | Semleges földelési igazság – latin exonyms. Ha elérhető, a latin szkriptet fogja használni |   ✓     |        |         |                   |        ✓         |          |
| pl-PL      | lengyel                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| PT-BR      | Portugál (brazíliai)    |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| PT-PT      | Portugál (portugáliai)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| RO-RO      | román               |       |    ✓    |         |         ✓         |                |     ✓     |
| ru-RU      | orosz                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| SR-Cyrl-RS | szerb (cirill betűs)     |       |    Szerb (cirill betűs) (SR-RS)   |         |                   |                |     ✓     |
| SR-Latn-RS | Szerb (latin betűs)        |       |        |         |                   |                |     ✓     |
| sk-SK      | Szlovák              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| SL-SL      | szlovén              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| es-ES      | spanyol                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| es-MX      | Spanyol (Mexikó)       |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| SV-SE     | svéd                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| th-TH      | thai                   |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| TR-TR      | török                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| Egyesült Királyság – UA      | ukrán               |       |    ✓   |         |                   |                |     ✓     |
| VI – VN      | vietnami             |       |    ✓   |         |                   |                |     ✓     |


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
>    * Azure Maps web SDK
>    * Azure Maps Android SDK


Azure Maps **View** paraméter (más néven "felhasználói régió paramétere") egy kétbetűs ISO-3166 országkód, amely megjeleníti az adott ország/régió megfelelő leképezéseit, amelyek meghatározzák, hogy a geopolitikai módon vitatott tartalom mely készletét adja vissza a rendszer Azure Maps a térképen megjelenő szolgáltatások, például a szegélyek és a címkék. 

Alapértelmezés szerint a View paraméter a **Unified**értékre van állítva, még akkor is, ha még nem adta meg azt a kérelemben. Az Ön felelőssége, hogy meghatározza a felhasználók helyét, majd az adott helyen helyesen állítsa be a View paramétert. Azt is megteheti, hogy a "View = automatikus" beállítást is megadja, amely a kérés IP-címe alapján visszaadja a térképi adatmennyiséget.  A Azure Mapsban szereplő View paramétert a vonatkozó törvényeknek megfelelően kell használni, beleértve azokat az országokat, amelyeken a Maps, a images és más adatokat, valamint a harmadik féltől származó, a Azure Maps-on keresztül való hozzáférésre jogosult külső tartalmat is elérhetővé teszi.


A következő táblázat a támogatott nézeteket tartalmazza.

| Megtekintés         | Leírás                            |  Térképek | Search | JS térképkezelés |
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
| Automatikus         | A leképezési adatmennyiséget a kérés IP-címe alapján küldi vissza.|   ✓   |    ✓   |     ✓          |
| Egységes      | Egyesített nézet (egyéb)                  |   ✓   |   ✓     |     ✓          |
