---
title: Testreszabható biztonsági riasztások
description: Ismerje meg a testreszabható biztonsági riasztásokat és az ajánlott szervizelést az Azure Security Center for IoT-funkciók és -szolgáltatások használatával.
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
ms.openlocfilehash: 3b39d70c60a4c9701d0a8bafde17b241fe01cc46
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311643"
---
# <a name="azure-security-center-for-iot-security-alerts"></a>Azure Security Center for IoT – biztonsági riasztások

Az Azure Security Center for IoT folyamatosan elemzi az IoT-megoldást fejlett elemzési és fenyegetési intelligencia használatával, hogy figyelmeztesse Önt a rosszindulatú tevékenységekre.

Javasoljuk, hogy hozzon létre egyéni riasztásokat a várható eszközviselkedés ismerete alapján, hogy a riasztások az egyedi szervezeti üzembe helyezés és a környezet lehetséges veszélyeztetésének leghatékonyabb mutatóiként működjenek.

Az alábbi lista az Azure Security Center for IoT riasztások az Ön által meghatározható a várt IoT Hub és/vagy eszköz viselkedését. Az egyes riasztások testreszabásáról az [Egyéni riasztások létrehozása című](quickstart-create-custom-alerts.md)témakörben talál további információt.

## <a name="iot-hub-alerts-available-for-customization"></a>Az IoT Hub riasztásai elérhetők a testreszabáshoz

| Severity | Riasztás neve | Adatforrás | Leírás | Javasolt szervizelés|
|---|---|---|---|---|
| Alacsony      | Egyéni riasztás - az AMQP protokollban a felhőről az eszközre irányuló üzenetek száma kívül esik a megengedett tartományon          | IoT Hub     | Az eszközről az eszközre irányuló üzenetek száma (AMQP protokoll) egy adott időablakon belül kívül esik az aktuálisan konfigurált és engedélyezett tartományon.||
| Alacsony      | Egyéni riasztás – az AMQP protokollban az elutasított felhőről eszközre irányuló üzenetek száma kívül esik a megengedett tartományon | IoT Hub     | Az eszköz által elutasított felhőről eszközre irányuló üzenetek (AMQP protokoll) száma, egy adott időablakon belül kívül esik az aktuálisan konfigurált és engedélyezett tartományon.||
| Alacsony      | Egyéni riasztás – az AMQP protokollban az eszközről a felhőbe irányuló üzenetek száma kívül esik a megengedett tartományon      | IoT Hub     | Az eszköz és a felhő közötti üzenetek (AMQP protokoll) mennyisége egy adott időablakon belül kívül esik az aktuálisan konfigurált és engedélyezett tartományon.|   |
| Alacsony      | Egyéni riasztás - a közvetlen metódus-meghívottak száma kívül esik a megengedett tartományon | IoT Hub     | Egy adott időablakon belüli közvetlen metódusmeghívók mennyisége kívül esik az aktuálisan konfigurált és engedélyezett tartományon.||
| Alacsony      | Egyéni riasztás - a fájlfeltöltések száma kívül esik az engedélyezett tartományon | IoT Hub     | Egy adott időablakon belüli fájlfeltöltések mennyisége kívül esik az aktuálisan konfigurált és engedélyezett tartományon.| |
| Alacsony      | Egyéni riasztás - a HTTP protokollban lévő felhőről eszközre irányuló üzenetek száma kívül esik a megengedett tartományon | IoT Hub     | Az időablakban lévő felhőről eszközre irányuló üzenetek (HTTP protokoll) mennyisége nem a konfigurált engedélyezett tartományban van                                  |
| Alacsony      | Egyéni riasztás – a HTTP protokollban lévő elutasított felhő-eszköz üzenetek száma nem a megengedett tartományban van | IoT Hub     | Az eszközről az eszközre irányuló felhőüzenetek (HTTP protokoll) mennyisége egy adott időablakon belül kívül esik a jelenleg konfigurált és engedélyezett tartományon. |
| Alacsony      | Egyéni riasztás – a HTTP protokollban lévő, a felhőbe irányuló eszközök száma kívül esik a megengedett tartományon | IoT Hub| Az eszköz ről a felhőbe irányuló üzenetek (HTTP protokoll) mennyisége egy adott időablakon belül kívül esik az aktuálisan konfigurált és engedélyezett tartományon.|    |
| Alacsony      | Egyéni riasztás - az MQTT protokollban a felhőről az eszközre irányuló üzenetek száma kívül esik a megengedett tartományon | IoT Hub     | Az eszközről az eszközre irányuló felhőüzenetek (MQTT protokoll) mennyisége egy adott időablakon belül kívül esik az aktuálisan konfigurált és engedélyezett tartományon.|   |
| Alacsony      | Egyéni riasztás - az MQTT protokollban az elutasított felhőről eszközre irányuló üzenetek száma kívül esik a megengedett tartományon | IoT Hub     | Az eszköz által egy adott időablakon belül elutasított felhőről eszközre irányuló üzenetek (MQTT protokoll) mennyisége kívül esik az aktuálisan konfigurált és engedélyezett tartományon. |
| Alacsony      | Egyéni riasztás - az MQTT protokollban lévő eszközről a felhőbe irányuló üzenetek száma kívül esik a megengedett tartományon          | IoT Hub     | Az eszközről a felhőbe irányuló üzenetek (MQTT protokoll) mennyisége egy adott időablakon belül kívül esik az aktuálisan konfigurált és engedélyezett tartományon.|
| Alacsony      | Egyéni riasztás - a parancsvárólista-kiürítések száma kívül esik a megengedett tartományon                               | IoT Hub     | Egy adott időablakon belül a parancsvárólista kiürítésének mennyisége kívül esik az aktuálisan konfigurált és engedélyezett tartományon.||
| Alacsony      | Egyéni riasztás - a modul ikerfrissítéseinek száma kívül esik a megengedett tartományon                                       | IoT Hub     | Az adott időablakon belül a modul ikerfrissítéseinek mennyisége kívül esik az aktuálisan konfigurált és engedélyezett tartományon.|
| Alacsony      | Egyéni riasztás - a jogosulatlan műveletek száma kívül esik a megengedett tartományon  | IoT Hub     | Egy adott időablakon belüli jogosulatlan műveletek mennyisége kívül esik az aktuálisan konfigurált és engedélyezett tartományon.|
|

## <a name="agent-alerts-available-for-customization"></a>A testreszabáshoz rendelkezésre álló ügyintézői riasztások

| Severity | Riasztás neve | Adatforrás | Leírás | Javasolt szervizelés|
|---|---|---|---|---|
| Alacsony      | Egyéni riasztás – az aktív kapcsolatok száma kívül esik az engedélyezett tartományon  | Ügynök       | Egy adott időablakon belüli aktív kapcsolatok száma kívül esik az aktuálisan konfigurált és engedélyezett tartományon.|  Vizsgálja meg az eszköznaplókat. További információ akapcsolat létrejöttéről, és annak megállapítása, hogy az jóindulatú vagy rosszindulatú.Learn where the connection originated and determine if it is benign or malicious. Ha rosszindulatú, távolítsa el a lehetséges rosszindulatú programokat, és ismerje meg a forrást. Ha jóindulatú, adja hozzá a forrást az engedélyezett kapcsolatlistához.  |
| Alacsony      | Egyéni riasztás – kimenő kapcsolat, amely nem engedélyezett IP-címhez jött létre                             | Ügynök       | Kimenő kapcsolat jött létre egy olyan IP-címhez, amely kívül esik az engedélyezett IP-listán. |Vizsgálja meg az eszköznaplókat. További információ akapcsolat létrejöttéről, és annak megállapítása, hogy az jóindulatú vagy rosszindulatú.Learn where the connection originated and determine if it is benign or malicious. Ha rosszindulatú, távolítsa el a lehetséges rosszindulatú programokat, és ismerje meg a forrást. Ha jóindulatú, adja hozzá a forrást az engedélyezett IP-listához.                        |
| Alacsony      | Egyéni riasztás – a sikertelen helyi bejelentkezések száma kívül esik az engedélyezett tartományon                               | Ügynök       | Egy adott időablakon belül a sikertelen helyi bejelentkezések mennyisége kívül esik az aktuálisan konfigurált és engedélyezett tartományon. |   |
| Alacsony      | Egyéni riasztás - olyan felhasználó bejelentkezése, aki nem szerepel az engedélyezett felhasználói listán | Ügynök       | Az engedélyezett felhasználói listán kívül lévő helyi felhasználó, bejelentkezve az eszközre.|  Nyers adatok mentésekor keresse meg a naplóelemzési fiókját, és használja az adatokat az eszköz vizsgálatához, azonosítsa a forrást, majd javítsa ki az engedélyezett/letiltási listát ezekhez a beállításokhoz. Ha jelenleg nem ment nyers adatokat, lépjen az eszközre, és javítsa ki a beállítások engedélyezési/tiltólistáját.|
| Alacsony      | Egyéni riasztás – olyan folyamat lett végrehajtva, amely nem engedélyezett | Ügynök       | Az eszközön nem engedélyezett folyamat végrehajtása történt. |Nyers adatok mentésekor keresse meg a naplóelemzési fiókját, és használja az adatokat az eszköz vizsgálatához, azonosítsa a forrást, majd javítsa ki az engedélyezett/letiltási listát ezekhez a beállításokhoz. Ha jelenleg nem ment nyers adatokat, lépjen az eszközre, és javítsa ki a beállítások engedélyezési/tiltólistáját.  |
|

## <a name="next-steps"></a>További lépések

- Útmutató a [riasztások testreszabásához](quickstart-create-custom-alerts.md)
- Az Azure Security Center for IoT szolgáltatás [– áttekintés](overview.md)
- További információ [a biztonsági adatok eléréséről](how-to-security-data-access.md)
- További információ az [eszköz vizsgálatáról](how-to-investigate-device.md)
