---
title: Egyéni riasztások létrehozása a IoT-hez Azure Security Centerhoz | Microsoft Docs
description: Egyéni eszközökhöz tartozó riasztások létrehozása és társítása a IoT-hez Azure Security Center.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: ed10cbf89f878f8d27b43476d26ac93dd373ed66
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597010"
---
# <a name="quickstart-create-custom-alerts"></a>Gyors útmutató: Egyéni riasztások létrehozása


Az egyéni biztonsági csoportok és riasztások használata révén teljes mértékben kihasználhatja a teljes körű biztonsági információkat és a kategorikus eszköz ismereteit, így biztosítva a jobb biztonságot a IoT-megoldáson belül. 

## <a name="why-use-custom-alerts"></a>Miért érdemes egyéni riasztásokat használni? 

Ismeri a IoT-eszközeit a legjobban.

Azon ügyfelek esetében, akik teljes mértékben értik a várt eszköz viselkedését, Azure Security Center a IoT lehetővé teszi, hogy az eszköz viselkedési házirendjében és a várttól, a normál működéstől való eltéréstől függően lefordítsa ezt az értelmezést.

## <a name="security-groups"></a>Biztonsági csoportok

A biztonsági csoportok lehetővé teszik, hogy az eszközök logikai csoportjait definiálja, és központi módon kezelhesse a biztonsági állapotukat.

Ezek a csoportok az adott hardverrel rendelkező eszközöket, az adott helyen üzembe helyezett eszközöket, vagy bármely más, az adott igényeknek megfelelő csoportot jelenthetnek.

A biztonsági csoportokat a **SecurityGroup**nevű Device Twin tag tulajdonság határozza meg. Alapértelmezés szerint a IoT Hub minden IoT-megoldása egy **alapértelmezett**nevű biztonsági csoporttal rendelkezik. Módosítsa a **SecurityGroup** tulajdonság értékét egy eszköz biztonsági csoportjának megváltoztatásához.
 
Példa:

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  }, 
```

Biztonsági csoportok használatával csoportosíthatja az eszközöket logikai kategóriákba. A csoportok létrehozása után hozzárendelheti azokat a választott egyéni riasztásokhoz a leghatékonyabb végpontok közötti IoT biztonsági megoldáshoz. 

## <a name="customize-an-alert"></a>Riasztás testreszabása

1. Nyissa meg a IoT Hub. 
2. Kattintson az **egyéni riasztások** elemre a **Biztonság** szakaszban. 
3. Válassza ki azt a biztonsági csoportot, amelyre alkalmazni kívánja a testreszabást. 
4. Kattintson **az egyéni riasztás hozzáadása**lehetőségre.
5. Válasszon ki egy egyéni riasztást a legördülő listából. 
6. Szerkessze a szükséges tulajdonságokat, majd kattintson **az OK**gombra.
7. Győződjön meg arról, hogy a **Mentés**gombra kattint. Az új riasztás mentése nélkül a rendszer törli a riasztást, amikor legközelebb IoT Hub.

 
## <a name="alerts-available-for-customization"></a>Testreszabáshoz elérhető riasztások

Az alábbi táblázat a testreszabáshoz elérhető riasztások összegzését tartalmazza.


| severity | Name (Név) | Adatforrás | Leírás | Javasolt szervizelés|
|---|---|---|---|---|
| Alacsony      | Egyéni riasztás – az AMQP-protokollban található, Felhőbeli üzenetek száma kívül esik az engedélyezett tartományon.          | IoT Hub     | A megadott időtartományon belüli felhőből az eszközre irányuló üzenetek (AMQP protokoll) száma a jelenleg konfigurált és engedélyezett tartományon kívül esik.||
| Alacsony      | Egyéni riasztás – az elutasított Felhőbeli üzenetek száma az AMQP protokollon kívül esik az engedélyezett tartományon. | IoT Hub     | Az eszköz által visszautasított Felhőbeli üzenetek (AMQP protokoll) száma egy adott időszakon belül a jelenleg konfigurált és engedélyezett tartományon kívül esik.||
| Alacsony      | Egyéni riasztás – az eszköz Felhőbeli üzeneteinek száma az AMQP protokollon kívül esik az engedélyezett tartományon.      | IoT Hub     | Az eszközről a felhőbe irányuló üzenetek (AMQP protokoll) egy adott időintervallumon belül a jelenleg konfigurált és engedélyezett tartományon kívül esnek.|   |
| Alacsony      | Egyéni riasztás – a közvetlen metódus meghívásának száma kívül esik az engedélyezett tartományon. | IoT Hub     | A megadott időszakon belül meghívott közvetlen metódusok mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik.||
| Alacsony      | Egyéni riasztás – a fájlfeltöltés száma kívül esik az engedélyezett tartományon. | IoT Hub     | Egy adott időtartományon belüli fájlfeltöltés mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik.| |
| Alacsony      | Egyéni riasztás – a HTTP-protokollban lévő üzenetek Felhőbeli üzeneteinek száma a megengedett tartományon kívül esik | IoT Hub     | Egy időablakban a felhőből az eszközre irányuló üzenetek (HTTP-protokoll) mennyisége nem a konfigurált engedélyezett tartományba esik.                                  |
| Alacsony      | Egyéni riasztás – az elutasított Felhőbeli üzenetek száma a HTTP protokollban nem a megengedett tartományba esik. | IoT Hub     | A megadott időintervallumon belül a felhőből az eszközre irányuló üzenetek (HTTP-protokoll) mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |
| Alacsony      | Egyéni riasztás – az eszköz Felhőbeli üzeneteinek száma a HTTP protokollon kívül esik az engedélyezett tartományon. | IoT Hub| Egy adott időintervallumon belül a Felhőbeli üzenetek (HTTP-protokoll) mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik.|    |
| Alacsony      | Egyéni riasztás – az MQTT-protokollban található, Felhőbeli üzenetek száma kívül esik az engedélyezett tartományon. | IoT Hub     | Egy adott időszakon belül a felhőből az eszközre irányuló üzenetek (MQTT protokoll) mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik.|   |
| Alacsony      | Egyéni riasztás – az elutasított Felhőbeli üzenetek száma az MQTT protokollon kívül esik az engedélyezett tartományon. | IoT Hub     | Az eszköz által az adott időszakon belül visszautasított felhő-eszköz üzenetek (MQTT protokoll) mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |
| Alacsony      | Egyéni riasztás – az eszköz Felhőbeli üzeneteinek száma az MQTT protokollon kívül esik az engedélyezett tartományon.          | IoT Hub     | Az eszközről a felhőbe irányuló üzenetek (MQTT protokoll) egy adott időintervallumon belül a jelenleg konfigurált és engedélyezett tartományon kívül esnek.|
| Alacsony      | Egyéni riasztás – a parancssori törlések száma kívül esik az engedélyezett tartományon.                               | IoT Hub     | Egy adott időszakon belül a parancssori törlések mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik.||
| Alacsony      | Egyéni riasztás – a modul különálló frissítéseinek száma kívül esik az engedélyezett tartományon.                                       | IoT Hub     | Egy adott időszakon belül a modulhoz tartozó dupla frissítések mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik.|
| Alacsony      | Egyéni riasztás – a jogosulatlan műveletek száma kívül esik az engedélyezett tartományon.  | IoT Hub     | Egy adott időszakon belül nem engedélyezett műveletek mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik.|
| Alacsony      | Egyéni riasztás – az aktív kapcsolatok száma kívül esik az engedélyezett tartományon.  | Ügynök       | A megadott időszakon belüli aktív kapcsolatok száma a jelenleg konfigurált és engedélyezett tartományon kívül esik.|  Vizsgálja meg az eszköz naplóit. Ismerje meg, hogy a kapcsolatok honnan származnak, és hogy milyen jóindulatú vagy rosszindulatú. Ha rosszindulatú, távolítsa el a lehetséges kártevőket, és értse a forrást. Ha jóindulatú, adja hozzá a forrást az engedélyezett kapcsolatok listájához.  |
| Alacsony      | Egyéni riasztás – a kimenő kapcsolatok olyan IP-címhez lettek létrehozva, amely nem engedélyezett                             | Ügynök       | Egy kimenő kapcsolat egy olyan IP-címhez lett létrehozva, amely kívül esik az engedélyezett IP-listán. |Vizsgálja meg az eszköz naplóit. Ismerje meg, hogy a kapcsolatok honnan származnak, és hogy milyen jóindulatú vagy rosszindulatú. Ha rosszindulatú, távolítsa el a lehetséges kártevőket, és értse a forrást. Ha jóindulatú, adja hozzá a forrást az engedélyezett IP-listához.                        |
| Alacsony      | Egyéni riasztás – a sikertelen helyi bejelentkezések száma kívül esik az engedélyezett tartományon.                               | Ügynök       | Egy adott időszakon belül sikertelen helyi bejelentkezések mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |   |
| Alacsony      | Egyéni riasztás – olyan felhasználó bejelentkezése, amely nem szerepel az engedélyezett felhasználók listáján | Ügynök       | Az engedélyezett felhasználók listáján kívüli helyi felhasználó, amely be van jelentkezve az eszközre.|  Nyers adatmentéskor keresse meg a log Analytics-fiókját, és az adataival vizsgálja meg az eszközt, azonosítsa a forrást, majd javítsa ki az engedélyezési/tiltási listát ezekhez a beállításokhoz. Ha jelenleg nem menti a nyers adatmentést, nyissa meg az eszközt, és javítsa ki az engedélyezési/tiltási listát ezekhez a beállításokhoz.|
| Alacsony      | Egyéni riasztás – a folyamat végrehajtása nem engedélyezett | Ügynök       | Nem engedélyezett folyamat lett végrehajtva az eszközön. |Nyers adatmentéskor keresse meg a log Analytics-fiókját, és az adataival vizsgálja meg az eszközt, azonosítsa a forrást, majd javítsa ki az engedélyezési/tiltási listát ezekhez a beállításokhoz. Ha jelenleg nem menti a nyers adatmentést, nyissa meg az eszközt, és javítsa ki az engedélyezési/tiltási listát ezekhez a beállításokhoz.  |
|


## <a name="next-steps"></a>További lépések

A következő cikkből megtudhatja, hogyan telepíthet biztonsági ügynököt...

> [!div class="nextstepaction"]
> [Biztonsági ügynök üzembe helyezése](how-to-deploy-agent.md)
