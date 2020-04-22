---
title: Az Azure IoT Hub díjszabásának megismerése | Microsoft dokumentumok
description: Fejlesztői útmutató – arról, hogyan működik a mérésés az árképzés az IoT Hubbal, beleértve a bevált példákat is.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 4c7382f84522333b6aae0d79941aae8f2147a12f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729147"
---
# <a name="azure-iot-hub-pricing-information"></a>Az Azure IoT Hub díjszabási adatai

[Az Azure IoT Hub díjszabása](https://azure.microsoft.com/pricing/details/iot-hub) az általános információkat a különböző sk-ek és az IoT Hub díjszabása. Ez a cikk további részleteket tartalmaz arról, hogy a különböző IoT Hub-funkciók az IoT Hub által üzenetekként mért.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Műveletenkénti költségek

| Művelet | Számlázási adatok | 
| --------- | ------------------- |
| Identitásjegyzék műveletei <br/> (létrehozás, beolvasás, lista, frissítés, törlés) | Nincs feltöltve. |
| Az eszközről a felhőbe irányuló üzenetek | A sikeresen küldött üzenetek 4 KB-os adattömbökben kerülnek felszámításra az IoT Hubba való be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- és be- Például egy 6 KB-os üzenet díja 2 üzenet. |
| Felhőből eszközre irányuló üzenetek | A sikeresen elküldött üzenetek et 4 KB-os adattömbökben terheljük, például egy 6 KB-os üzenetet 2 üzenetért. |
| Fájlfeltöltések | Az IoT Hub nem méri az Azure Storage-ba történő fájlátvitelt. A fájlátviteli kezdeményezési és befejezési üzeneteket 4 KB-os lépésekben, üzenetdíjasként kell megterhelni. Például egy 10 MB-os fájl átvitele az Azure Storage költsége mellett két üzenetként kerül felszámolásra. |
| Közvetlen metódusok | A sikeres metóduskérelmek et 4 KB-os adattömbökben számítjuk fel, a válaszokat pedig 4 KB-os adattömbökben további üzenetként. A leválasztott eszközökre irányuló kérelmeket 4 KB-os adattömbökben lévő üzenetként számítjuk fel. Például egy 4 KB-os törzsű módszer, amely az eszközről származó törzs nélküli választ eredményez, két üzenetként kerül felszámításra. A 6 KB-os törzsű, az eszköztől származó 1 KB-os választ eredményező metódust két üzenetként és egy másik üzenetként számítja fel a válasz. |
| Az ikereszköz és modul olvasása | Az eszközről vagy modulból és a megoldás háttértartalékából érkező kettős olvasások 512 bájtos adattömbökben lévő üzenetekként kerülnek felszámításra. Egy 6 KB-os ikerolvasás például 12 üzenetként kerül felszámolásra. |
| Eszköz- és ikereszköz-frissítések (címkék és tulajdonságok) | Az eszközről vagy modulból és a megoldás háttérrendszeréről származó két frissítés 512 bájtos adattömbökben lévő üzenetekként kerül felszámításra. Egy 6 KB-os ikerolvasás például 12 üzenetként kerül felszámolásra. |
| Eszköz- és moduliker-lekérdezések | A lekérdezések díja üzenetként kerül az 512 bájtos adattömbök ben lévő eredménymérettől függően. |
| Feladatműveletek <br/> (létrehozás, frissítés, listázás, törlés) | Nincs feltöltve. |
| Feladatok eszközönkénti műveletek | A feladatműveletek (például a kettős frissítések és a metódusok) a szokásos módon kerülnek felszámolásra. Például egy feladat, ami 1000 metódus hívások 1 KB-os kérelmek és üres törzs válaszok felszámolásra kerül 1000 üzeneteket. |
| Életben tartásról üzenetek | AMQP vagy MQTT protokollok használata esetén a kapcsolat létesítésére cserélt üzenetekés az egyeztetés során kicserélt üzenetek nem kerülnek felszámolásra. |

> [!NOTE]
> Minden méret számítása a bájtban megadott tartalomméretének figyelembevételével történik (a protokollkeretezést figyelmen kívül hagyja). A tulajdonságokkal és testtel rendelkező üzenetek esetében a méret protokollfüggetlen módon kerül kiszámításra. További információ: [IoT Hub üzenetformátum.](iot-hub-devguide-messages-construct.md)

## <a name="example-1"></a>Példa #1

Egy eszköz percenként egy 1 KB-os eszközről felhőbe irányuló üzenetet küld az IoT Hubnak, amelyet az Azure Stream Analytics olvas. A megoldás háttér-vége meghívja a metódust (egy 512 bájtos hasznos adat) az eszközön 10 percenként egy adott művelet aktiválásához. Az eszköz 200 bájt eredménygel reagál a metódusra.

A készülék a következőket fogyasztja:

* Egy üzenet * 60 perc * 24 óra = 1440 üzenetek naponta az eszköz-felhő üzenetek.
* Két kérelem plusz válasz * 6 alkalommal óránként * 24 óra = 288 üzenetek a módszerekhez.

Ez a számítás összesen 1728 üzenetet ad naponta.

## <a name="example-2"></a>Példa #2

Egy eszköz óránként egy 100 KB-os eszközről felhőbe irányuló üzenetet küld. Azt is frissíti az eszköz iker 1-KB hasznos teher négyóránként. A megoldás háttérrendszer, naponta egyszer, beolvassa a 14 KB-os eszköz iker és frissíti azt 512 bájtos hasznos terhelések konfigurációk módosításához.

A készülék a következőket fogyasztja:

* 25 (100 KB / 4 KB) üzenet * 24 óra az eszközről a felhőbe irányuló üzenetek esetében.
* Két üzenet (1 KB / 0,5 KB) * naponta hatszor az eszköz ikerfrissítései számára.

Ez a számítás naponta összesen 612 üzenetet ad meg.

A megoldás háttérrendszer használ 28 üzenetek (14 KB / 0,5 KB), hogy olvassa el a készülék iker, plusz egy üzenetet frissíteni, összesen 29 üzeneteket.

Az eszköz és a megoldás háttértartaléka összesen napi 641 üzenetet használ fel.
