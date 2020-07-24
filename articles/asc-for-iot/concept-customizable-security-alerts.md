---
title: Testreszabható biztonsági riasztások
description: Ismerje meg a testreszabható biztonsági riasztásokat, és ajánlott szervizelést Azure Security Center használatával IoT funkciók és szolgáltatások segítségével.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2020
ms.author: mlottner
ms.openlocfilehash: f676c4129b79499eb9ed524821a336b3859dbb3c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004851"
---
# <a name="azure-security-center-for-iot-customizable-security-alerts"></a>A IoT testreszabható biztonsági riasztások Azure Security Center

Azure Security Center a IoT folyamatosan elemzi a IoT-megoldást a fejlett Analitika és a fenyegetések felderítése révén, hogy figyelmeztessen a rosszindulatú tevékenységekre.

Javasoljuk, hogy hozzon létre egyéni riasztásokat a várt eszköz viselkedésének ismerete alapján, hogy a riasztások a leghatékonyabb mutatók legyenek a lehető leghatékonyabban az egyedi szervezeti üzembe helyezés és a környezet szempontjából.

A IoT-riasztások következő Azure Security Center listáját a várt IoT Hub és/vagy az eszköz viselkedése alapján meghatározhatja. Az egyes riasztások testreszabásával kapcsolatos további információkért lásd: [egyéni riasztások létrehozása](quickstart-create-custom-alerts.md).

## <a name="iot-hub-alerts-available-for-customization"></a>A testreszabáshoz elérhető IoT Hub riasztások

| Severity | Riasztás neve | Adatforrás | Description | Javasolt szervizelés|
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
|

## <a name="agent-alerts-available-for-customization"></a>Az ügynök által elérhető riasztások testreszabhatók

| Severity | Riasztás neve | Adatforrás | Description | Javasolt szervizelés|
|---|---|---|---|---|
| Alacsony      | Egyéni riasztás – az aktív kapcsolatok száma kívül esik az engedélyezett tartományon.  | Ügynök       | A megadott időszakon belüli aktív kapcsolatok száma a jelenleg konfigurált és engedélyezett tartományon kívül esik.|  Vizsgálja meg az eszköz naplóit. Ismerje meg, hogy a kapcsolatok honnan származnak, és hogy milyen jóindulatú vagy rosszindulatú. Ha rosszindulatú, távolítsa el a lehetséges kártevőket, és értse a forrást. Ha jóindulatú, adja hozzá a forrást az engedélyezett kapcsolatok listájához.  |
| Alacsony      | Egyéni riasztás – a kimenő kapcsolatok olyan IP-címhez lettek létrehozva, amely nem engedélyezett                             | Ügynök       | Egy kimenő kapcsolat egy olyan IP-címhez lett létrehozva, amely kívül esik az engedélyezett IP-listán. |Vizsgálja meg az eszköz naplóit. Ismerje meg, hogy a kapcsolatok honnan származnak, és hogy milyen jóindulatú vagy rosszindulatú. Ha rosszindulatú, távolítsa el a lehetséges kártevőket, és értse a forrást. Ha jóindulatú, adja hozzá a forrást az engedélyezett IP-listához.                        |
| Alacsony      | Egyéni riasztás – a sikertelen helyi bejelentkezések száma kívül esik az engedélyezett tartományon.                               | Ügynök       | Egy adott időszakon belül sikertelen helyi bejelentkezések mennyisége a jelenleg konfigurált és engedélyezett tartományon kívül esik. |   |
| Alacsony      | Egyéni riasztás – olyan felhasználó bejelentkezése, amely nem szerepel az engedélyezett felhasználók listáján | Ügynök       | Az engedélyezett felhasználók listáján kívüli helyi felhasználó, amely be van jelentkezve az eszközre.|  Nyers adatmentéskor keresse meg a log Analytics-fiókját, és az adataival vizsgálja meg az eszközt, azonosítsa a forrást, majd javítsa ki az engedélyezési/tiltási listát ezekhez a beállításokhoz. Ha jelenleg nem menti a nyers adatmentést, nyissa meg az eszközt, és javítsa ki az engedélyezési/tiltási listát ezekhez a beállításokhoz.|
| Alacsony      | Egyéni riasztás – a folyamat végrehajtása nem engedélyezett | Ügynök       | Nem engedélyezett folyamat lett végrehajtva az eszközön. |Nyers adatmentéskor keresse meg a log Analytics-fiókját, és az adataival vizsgálja meg az eszközt, azonosítsa a forrást, majd javítsa ki az engedélyezési/tiltási listát ezekhez a beállításokhoz. Ha jelenleg nem menti a nyers adatmentést, nyissa meg az eszközt, és javítsa ki az engedélyezési/tiltási listát ezekhez a beállításokhoz.  |
|

## <a name="next-steps"></a>További lépések

- Tudnivalók a [riasztások testreszabásáról](quickstart-create-custom-alerts.md)
- Azure Security Center a IoT szolgáltatás [áttekintéséhez](overview.md)
- [A biztonsági adatai elérésének](how-to-security-data-access.md) megismerése
- További információ az [eszközök kivizsgálása](how-to-investigate-device.md)
