---
title: Ismerkedjen meg az eszköz szimuláció megoldás - Azure |} Microsoft Docs
description: Az IoT Suite szimuláció megoldás olyan eszköz, amely segít a fejlesztési és tesztelési egy IoT-megoldás is használható. A szimuláció szolgáltatás az önálló ajánlat, amely más előkonfigurált megoldásokat együtt használják, vagy a saját egyéni megoldások használni.
services: iot device simulation
suite: iot-suite
author: troyhopwood
manager: timlt
ms.author: troyhop
ms.service: iot-suite
ms.date: 12/15/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 1452508822f08d4554f419a72f7e9e6018a52469
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="device-simulation-walkthrough"></a>Eszköz szimuláció forgatókönyv

Azure IoT-eszközök szimuláció olyan eszköz, amely segít a fejlesztési és tesztelési egy IoT-megoldás is használható. Eszköz szimuláció használható együtt más előkonfigurált megoldásokat, illetve saját egyéni megoldásokat kínál önálló.

Ez az oktatóanyag bemutatja, hogyan néhány eszköz szimuláció szolgáltatást. Ez a bemutatja, hogyan akkor működik, és lehetővé teszi a saját IoT-megoldások teszteléséhez használandó.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

>[!div class="checklist"]
> * A szimuláció konfigurálása
> * Indítása és leállítása a szimuláció
> * Nézet telemetriai metrikák

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szükséges Azure IoT eszköz szimuláció telepített példányát az Azure-előfizetéshez.

Ha az eszköz szimuláció még nem telepítette még, el kell végeznie a [telepítése Azure IoT eszköz szimuláció](iot-suite-device-simulation-deploy.md) oktatóanyag.

## <a name="configuring-device-simulation"></a>Eszköz szimuláció konfigurálása

Konfigurálja, és teljesen az eszköz szimuláció futtatni az irányítópulton. Az irányítópult megnyitása a IoT Suite [kiépített megoldások](https://www.azureiotsuite.com/) lap. Kattintson a **indítása** az új eszköz szimuláció központi telepítés alatt.

### <a name="target-iot-hub"></a>Cél IoT-központ

Eszköz szimuláció előtti ponton aktívvá vált IoT-központ vagy bármely más IoT-központ használhatja:

![Cél IoT-központ](media/iot-suite-device-simulation-explore/targethub.png)

> [!NOTE]
> Egy előre kiosztott IoT-központ használandó megkeresésének lehetősége csak akkor érhető el, ha úgy döntött, hogy hozzon létre egy új IoT Hub eszköz szimuláció üzembe helyezésekor. Ha nem rendelkezik az IoT-központ, létrehozhat egy újat mindig a [Azure-portálon](https://portal.azure.com).

Adja meg, amelyekre egy adott IoT-központ, a **iothubowner** kapcsolati karakterláncot. A kapcsolati karakterláncnak a következőről kaphat a [Azure-portálon](https://portal.azure.com):

1. Az Azure-portálon az IoT-központ konfigurációs lapján, kattintson a **megosztott elérési házirendek**.
1. Kattintson a **iothubowner**.
1. Másolja, vagy az elsődleges vagy másodlagos kulcsot.
1. Ez az érték illessze be a cél az IoT-központ a szövegmezőben.

![Cél IoT-központ](media/iot-suite-device-simulation-explore/connectionstring.png)

### <a name="device-model"></a>Eszközmodell

Az eszköz típusa szimulálása eszköz típusának teszi lehetővé. Válasszon az előre konfigurált eszközmodellek közül, vagy adja meg egy egyéni eszköz modell érzékelők listáját:

![Eszközmodell](media/iot-suite-device-simulation-explore/devicemodel.png)

#### <a name="pre-configured-device-models"></a>Előre konfigurált eszközök esetében

Eszköz szimuláció három előre konfigurált eszköz modellt biztosít. Eszközmodellek a hőmérsékletű felvonók és teherautók érhetők el.

Előre konfigurált eszközmodellek több érzékelők speciális viselkedések definiált JavaScript-fájlt tartalmaz. Egyéni viselkedést a webes felhasználói felület nem támogatottak. 

Az alábbi táblázat a minden előre konfigurált eszköz modell konfigurációk listáját:

| Eszközmodell | Érzékelő | Unit (Egység) | 
| -------------| ------ | -----| 
| Hűtő | nedvességtartalma | % |
| | pressure | psig | 
| | Hőmérséklet | F | 
| Foglalhatja | Emelet | 
| | vibráció | mm | 
| | Hőmérséklet | F | 
| Teherautó | Szélesség | |
| | Hosszúság | | 
| | Sebesség | mph | 
| | cargotemperature | F | 

#### <a name="custom-device-model"></a>Egyéni eszköz típusa

Egyéni eszközmodellek lehetővé teszi, hogy a saját eszközök jobban képviselő modell érzékelők. Egy egyéni eszköz legfeljebb 10 egyéni érzékelők lehet.

Az egyéni eszköz modelltípus kiválasztásakor kattintva adhat hozzá új érzékelők **+ Hozzáadás érzékelő**.

![Adja hozzá az érzékelők](media/iot-suite-device-simulation-explore/customsensors.png)

Egyéni érzékelők a következő jellemzőkkel rendelkezik:

| Mező | Leírás |
| ----- | ----------- |
| Érzékelő neve | Egy rövid nevet, például a érzékelő **hőmérséklet** vagy **sebesség**. |
| Viselkedés | Viselkedéshez engedélyezése egy üzenetből származó valós adatok szimulálása a következő eltérő telemetriai adatokat. **Növekvő** értéke az egyes üzenetsorokra küldött üzeneteket a minimális értékkel kezdődő eggyel növeli. Amennyiben az a maximális érték teljesül, majd azt ismét újra a minimális értéknél. **Csökkentést** azonos módon viselkedik **növekmény** , de száma. A **véletlenszerű** viselkedés állít elő, a minimális és maximális értékek közötti véletlenszerű értéket. |
| Minimális értéke | A legalacsonyabb az elfogadható tartományon jelölő számot. |
| A maximális érték | A jelölő az elfogadható tartományon legnagyobb számot. |
| Unit (Egység) | A mértékegységet például ° F vagy MPH érzékelő. |

### <a name="number-of-devices"></a>Eszközök száma

Eszköz szimuláció jelenleg teszi szimulálása legfeljebb 20 000 eszköz.

![Eszközök száma](media/iot-suite-device-simulation-explore/numberofdevices.png)

### <a name="telemetry-frequency"></a>Telemetria gyakorisága

Telemetria gyakoriságát adja meg, milyen gyakran a szimulált eszköz küldjön adatokat az IoT hub teszi lehetővé. Gyakran mint 10 másodpercenként vagy szerint ritkán küldhet adatokat minden 99 óra, 59 perc és 59 másodpercig.

![Telemetria gyakorisága](media/iot-suite-device-simulation-explore/frequency.png)

> [!NOTE]
> Ha az IoT-központ előtti ponton aktívvá vált az IoT-központ nem használ, majd érdemes üzenet korlátok a cél az IoT hub. Például ha van S1 hubhoz 10 másodpercenként telemetriai adatok küldését 1000 szimulált eszköz elérte a telemetriai korlátját a központ most akár egy óráig.

### <a name="simulation-duration"></a>Szimuláció időtartama

Kiválaszthatja a szimuláció egy meghatározott ideig vagy addig, amíg explicit módon leállítani. Ha úgy dönt, hogy a megadott futási idejét, bármely időtartam választhat 59 másodperc, 99 üzemideje (óra) és 59 perc legfeljebb 10 perc.

![Szimuláció időtartama](media/iot-suite-device-simulation-explore/duration.png)

### <a name="start-and-stop-the-simulation"></a>Elindítása és leállítása a szimuláció

Ha adott hozzá az összes szükséges konfigurációs adatokat az űrlap a **indítsa el a szimuláció** gomb engedélyezve van. Indítsa el a szimuláció, kattintson erre a gombra kattintva.

![Szimuláció indítása](media/iot-suite-device-simulation-explore/start.png)

Ha meg van adva egy adott időtartamig a szimuláció, majd automatikusan befejeződik az idő elérésekor. Mindig leállíthatja a szimuláció korai kattintva **szimuláció leállítása.**

Ha úgy döntött, hogy a szimuláció határozatlan ideig futnak, akkor fut, amíg nem kattint **leállítása szimuláció**. Zárja be a böngészőt, és térjen vissza az eszköz szimuláció oldalra bármikor a szimulációjának leállításához.

![Szimuláció leállítása](media/iot-suite-device-simulation-explore/stop.png)

> [!NOTE]
> Csak egy szimulátor is futtatható egyszerre. Egy új szimuláció megkezdése előtt le kell állítania a jelenleg futó szimuláció.
