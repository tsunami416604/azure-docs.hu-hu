---
title: ASC megismerheti az IoT biztonsági javaslatok előzetes verzió |} A Microsoft Docs
description: Ismerje meg a biztonsági javaslatok és a használatuk az ASC IOT fogalmát.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: 1e4582d93d1e3380ecdabdb241f27839d4da4565
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541857"
---
# <a name="security-recommendations"></a>Biztonsági javaslatok

> [!IMPORTANT]
> Az IoT ASC jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Folyamatban lévő megoldás elemzés alapján az IoT ASC javítása és az eszközök, működési állapot és az IoT Hub-környezet teljes védelme érdekében szükség esetén az alábbi javaslatokat biztosít. 


## <a name="device-recommendations"></a>Eszköz-javaslatok

Eszköz megnövelhető elemzések és javaslatok eszközbiztonság és -viselkedés javítása érdekében. 

| Severity | Name (Név)                                                      | Adatforrás | Leírás                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Közepes   | Nyitott portok az eszközön                                      | Ügynök       | A rendszer figyelő végpontot talált az eszközön                                                                                                                                                          |
| Közepes   | Megengedő tűzfal-házirend található a minősített főtanúsítványhoz kapcsolódik. | Ügynök       | Tűzfal-házirend található (bemeneti/kimeneti) engedélyezett. Tűzfalházirend kell alapértelmezés szerint az összes forgalom megtagadásához, és engedélyezi a szükséges kommunikációt és-tárolókról az eszköz szabályokat határozhat meg.                               |
| Közepes   | A bemeneti lánc megengedő tűzfalszabály található.     | Ügynök       | Egy szabály a tűzfalon talált, amely számos különböző IP-címek és portok megengedő mintát tartalmaz.                                                                                    |
| Közepes   | Megengedő tűzfalszabály kimeneti láncában található.    | Ügynök       | Egy szabály a tűzfalon talált, amely számos különböző IP-címek és portok megengedő mintát tartalmaz.                                                                                   |
| Közepes   | Művelet rendszer alapkonfiguráció érvényesítése sikertelen volt           | Ügynök       | Eszköz nem kompatibilis [CIS Linux rendszerek vonatkozásában](https://www.cisecurity.org/cis-benchmarks/)                                                                                                         |

### <a name="operational-recommendation"></a>Működési javaslat

Működési megnövelhető insights és az ügynök konfigurációjának javítására vonatkozó javaslatokat.

| Severity | Name (Név)                                    | Adatforrás | Leírás                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Alacsony      | Az ügynök unutilized üzeneteket küld.          | Ügynök       | biztonsági üzeneteket legalább 10 % az elmúlt 24 órában történt 4kb-nál kisebb.  |
| Alacsony      | Biztonsági ikereszköz konfiguráció nem optimális | Ügynök       | Biztonsági ikereszköz-konfiguráció nem optimális.                                        |
| Alacsony      | Biztonsági ikereszköz konfigurációs ütközés    | Ügynök       | A biztonsági ikereszköz konfigurációban meghatározott ütközések.                           |


## <a name="iot-hub-recommendations"></a>Az IoT Hub-javaslatok

Javaslat riasztások adja meg, az insight and javaslatok a műveletek javítása a környezet biztonsági állapotát.  

| Severity | Name (Név)                                                     | Adatforrás | Leírás                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Magas     | Több eszköz által használt azonos típusú hitelesítő adatok | IoT Hub     | Az IoT Hub hitelesítő adatok több eszközön használja. Ez azt jelezheti egy megbízható eszköz megszemélyesítésekor illegitimate eszközt. Duplikált hitelesítő adatok használata növeli annak kockázatát, az eszköz megszemélyesítési rosszindulatú szereplő szerint. |
| Közepes   | Célszerű lehet tiltani az alapértelmezett IP-szűrő házirend                  | IoT Hub     | IP-szűrő konfigurációjának rendelkezik engedélyezett forgalmat, és alapértelmezés szerint kell definiált szabályokkal, alapértelmezés szerint az összes többi a forgalom megtagadásához.                                                                                                     |
| Közepes   | IP-szűrési szabály nagy IP-címtartományt is tartalmaz.                   | IoT Hub     | Egy engedélyezési IP szűrési szabály forrás IP-címtartományt mérete túl nagy. Túlzottan megengedő az IoT hub rosszindulatú tehetők közzé.                                                                                       |
| Alacsony      | Diagnosztikai naplók engedélyezése az IoT Hubban                       | IoT Hub     | Naplók engedélyezése és megőrzi őket a legfeljebb egy évig. Naplók megőrzése lehetővé teszi a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül.                                       |
|