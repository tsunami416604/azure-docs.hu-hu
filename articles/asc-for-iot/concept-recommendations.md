---
title: Biztonsági javaslatok
description: Ismerje meg a biztonsági javaslatok koncepcióját és használatuk módját az Azure Security Center for IoT-ben.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2019
ms.author: mlottner
ms.openlocfilehash: 213595ac69efc90ec855b2891641e1f00bd1ba92
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311546"
---
# <a name="security-recommendations"></a>Biztonsági javaslatok

Az Azure Security Center for IoT megvizsgálja az Azure-erőforrásokat és az IoT-eszközöket, és biztonsági javaslatokat nyújt a támadási felület csökkentésére.
A biztonsági ajánlások végrehajthatók, és arra irányulnak, hogy segítsék az ügyfeleket a legjobb biztonsági eljárások betartásában.

Ebben a cikkben az IoT Hub és/vagy az IoT-eszközök által aktiválható javaslatok listáját találja.

## <a name="recommendations-for-iot-devices"></a>Javaslatok az IoT-eszközökhöz

Az eszközjavaslatok elemzéseket és javaslatokat nyújtanak az eszköz biztonsági állapotának javításához.

| Severity | Név                                                      | Adatforrás | Leírás                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Közepes   | Portok megnyitása az eszközön                                      | Ügynök       | A készüléken figyelővégpont található.                                                                                                                                                        |
| Közepes   | Az egyik láncban található engedékeny tűzfalházirend. | Ügynök       | A tűzfalházirend engedélyezett (INPUT/OUTPUT). A tűzfalházirendnek alapértelmezés szerint meg kell tagadnia az összes forgalmat, és olyan szabályokat kell definiálnia, amelyek lehetővé teszik az eszközhöz/eszközről történő szükséges kommunikációt.                               |
| Közepes   | A bemeneti láncban egy megengedő tűzfalszabály található     | Ügynök       | A tűzfalban olyan szabály található, amely az IP-címek és portok széles körének megengedő mintáját tartalmazza.                                                                                    |
| Közepes   | A kimeneti láncban megengedő tűzfalszabály található    | Ügynök       | A tűzfalban olyan szabály található, amely az IP-címek és portok széles körének megengedő mintáját tartalmazza.                                                                                   |
| Közepes   | Az üzemi rendszer alapkonfigurációjának érvényesítése sikertelen           | Ügynök       | Az eszköz nem felel meg a [CIS Linux-referenciaértékeknek.](https://www.cisecurity.org/cis-benchmarks/)                                                                                                        |

### <a name="operational-recommendations-for-iot-devices"></a>Működési javaslatok Az IoT-eszközök

A működési javaslatok elemzéseket és javaslatokat nyújtanak a biztonsági ügynök konfigurációjának javításához.

| Severity | Név                                    | Adatforrás | Leírás                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Alacsony      | Az ügynök nem hasznosuló üzeneteket küld          | Ügynök       | A biztonsági üzenetek 10%-a vagy több, mint 4 KB volt az elmúlt 24 órában.  |
| Alacsony      | A biztonsági ikerkonfiguráció nem optimális | Ügynök       | A biztonsági ikerkonfiguráció nem optimális.                                        |
| Alacsony      | Biztonsági iker konfigurációs ütközés    | Ügynök       | Ütközések a biztonsági ikerkonfigurációban voltak azonosítva. |                          |
|

## <a name="recommendations-for-iot-hub"></a>Javaslatok az IoT Hub

Az ajánlási riasztások betekintést és javaslatokat nyújtanak a környezet biztonsági helyzetének javítására irányuló műveletekhez.

| Severity | Név                                                     | Adatforrás | Leírás                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Magasság     | Több eszköz által használt azonos hitelesítési hitelesítő adatok | IoT Hub     | Az IoT Hub hitelesítési hitelesítő adatait több eszköz használja. Ez arra utalhat, hogy egy törvénytelen eszköz megszemélyesít egy törvényes eszközt. A hitelesítő adatok duplikáltságának használata növeli a rosszindulatú szereplő általi eszközmegszemélyesítés kockázatát. |
| Közepes   | Az alapértelmezett IP-szűrőházirendet meg kell tagadni                  | IoT Hub     | Az IP-szűrő konfigurációjának rendelkeznie kell az engedélyezett forgalomra vonatkozó szabályokkal, és alapértelmezés szerint alapértelmezés szerint meg kell tagadnia az összes többi forgalmat.                                                                                                     |
| Közepes   | Az IP-szűrőszabály nagy IP-tartományt tartalmaz                   | IoT Hub     | Az IP-szűrő szabályforrásÁNAK IP-tartománya túl nagy. A túlzottan megengedő szabályok rosszindulatú szereplők nek tehetik ki az IoT-központot.                                                                                       |
| Alacsony      | Diagnosztikai naplók engedélyezése az IoT Hubban                       | IoT Hub     | Engedélyezze a naplókat, és őrizze meg őket legfeljebb egy évig. A naplók megőrzése lehetővé teszi, hogy újra létrehozza a tevékenységnyomvonalakat vizsgálati célokra, ha biztonsági incidens történik, vagy ha a hálózat biztonsága sérül.                                       |
|

## <a name="next-steps"></a>További lépések

- Az Azure Security Center for IoT szolgáltatás [– áttekintés](overview.md)
- További információ [a biztonsági adatok eléréséről](how-to-security-data-access.md)
- További információ az [eszköz vizsgálatáról](how-to-investigate-device.md)
