---
title: Biztonsági javaslatok
description: Ismerje meg a biztonsági javaslatok fogalmát, valamint azt, hogyan használják őket a Defender a IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 0eccab6c3d59ad68ddc8f96c3d84c57dc1bbeeca
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936287"
---
# <a name="security-recommendations"></a>Biztonsági javaslatok

A Defender for IoT megvizsgálja az Azure-erőforrásokat és IoT eszközöket, és biztonsági javaslatokat tesz a támadási felület csökkentése érdekében.
A biztonsági javaslatok működésre készek, és célja, hogy az ügyfelek számára segítséget nyújtsanak az ajánlott biztonsági eljárásoknak való megfelelésben.

Ebben a cikkben a IoT Hub és/vagy IoT-eszközökön aktiválható javaslatok listáját találhatja meg.

## <a name="recommendations-for-iot-devices"></a>Javaslatok a IoT-eszközökhöz

Az eszközökre vonatkozó javaslatok betekintést és javaslatokat nyújtanak az eszközök biztonsági helyzetének javítására.

| Súlyosság | Name                                                      | Adatforrás | Leírás                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Közepes   | Portok megnyitása az eszközön                                      | Ügynök       | Figyelő végpont található az eszközön.                                                                                                                                                        |
| Közepes   | Az egyik láncban megtalálhatók a megengedhető tűzfal-szabályzatok. | Ügynök       | Engedélyezett tűzfal-házirend található (bemenet/kimenet). A tűzfal házirendjének alapértelmezés szerint el kell utasítania az összes forgalmat, és meg kell határoznia azokat a szabályokat, amelyek lehetővé teszik a szükséges kommunikációt az eszközön                               |
| Közepes   | A bemeneti láncban található engedékeny tűzfalszabályok találhatók     | Ügynök       | A rendszer olyan szabályt észlelt a tűzfalon, amely az IP-címek vagy portok széles tartományára vonatkozó megengedő mintát tartalmaz.                                                                                    |
| Közepes   | A rendszer a kimeneti láncban található engedékeny tűzfalszabály    | Ügynök       | A rendszer olyan szabályt észlelt a tűzfalon, amely az IP-címek vagy portok széles tartományára vonatkozó megengedő mintát tartalmaz.                                                                                   |
| Közepes   | Az operációs rendszerek alapkonfigurációjának ellenőrzése nem sikerült           | Ügynök       | Az eszköz nem felel meg a [CIS Linux-referenciaértékeknek](https://www.cisecurity.org/cis-benchmarks/).                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Működési javaslatok a IoT-eszközökhöz

A működési javaslatok betekintést és javaslatokat nyújtanak a biztonsági ügynökök konfigurációjának javítására.

| Súlyosság | Name                                    | Adatforrás | Leírás                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Alacsony      | Az ügynök nem használt üzeneteket küld          | Ügynök       | az elmúlt 24 órában 10% vagy több biztonsági üzenet kisebb volt, mint 4 KB.  |
| Alacsony      | A biztonsági Twin konfiguráció nem optimális | Ügynök       | A biztonsági Twin konfiguráció nem optimális.                                        |
| Alacsony      | Biztonsági kettős konfiguráció ütközése    | Ügynök       | Ütközések voltak azonosítva a biztonsági Twin konfigurációban. |                          |
|

## <a name="recommendations-for-iot-hub"></a>Javaslatok a IoT Hub

Az ajánlási riasztások betekintést és javaslatokat nyújtanak a környezet biztonsági helyzetének javítására szolgáló műveletekhez.

| Súlyosság | Name                                                     | Adatforrás | Leírás                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Magas     | Több eszköz által használt azonos hitelesítési hitelesítő adatok | IoT Hub     | IoT Hub hitelesítési hitelesítő adatokat több eszköz is használja. Ez arra utalhat, hogy egy törvénytelen eszköz megszemélyesít egy legitim eszközt. Az ismétlődő hitelesítő adatok használata növeli az eszköz megszemélyesítésének kockázatát egy rosszindulatú színész által. |
| Közepes   | Az alapértelmezett IP-szűrési házirendet meg kell tagadni                  | IoT Hub     | Az IP-szűrési konfigurációnak meg kell határoznia az engedélyezett forgalomra vonatkozó szabályokat, és alapértelmezés szerint meg kell tagadnia az összes többi forgalmat.                                                                                                     |
| Közepes   | Az IP-szűrési szabály nagyméretű IP-címtartományt tartalmaz                   | IoT Hub     | Az IP-szűrési szabály forrás IP-tartományának engedélyezése túl nagy. A túlzottan megengedhető szabályok az IoT-hubot kártékony szereplőkkel tehetik ki.                                                                                       |
| Alacsony      | Diagnosztikai naplók engedélyezése IoT Hub                       | IoT Hub     | Engedélyezheti a naplókat, és akár egy évig is megtarthatja őket. A naplók megőrzése lehetővé teszi, hogy a tevékenységi nyomvonalak újbóli létrehozása a biztonsági incidensek bekövetkezésekor vagy a hálózat biztonsága szempontjából.                                       |
|

## <a name="next-steps"></a>Következő lépések

- Defender a IoT szolgáltatáshoz [– Áttekintés](overview.md)
- [A biztonsági adatai elérésének](how-to-security-data-access.md) megismerése
- További információ az [eszközök kivizsgálása](how-to-investigate-device.md)
