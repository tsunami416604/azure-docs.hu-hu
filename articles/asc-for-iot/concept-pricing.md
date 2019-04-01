---
title: Megismerheti az Azure Security Center az IoT előzetes költségek |} A Microsoft Docs
description: További információ az Azure Security Center IoT, és hogyan szabályozhatja a költségeket.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: e2822b0f2f665220f215b85800081fa2fa7943f9
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758042"
---
# <a name="pricing-and-associated-costs"></a>Díjszabás és kapcsolódó költségek

> [!IMPORTANT]
> Az Azure Security Center az IoT jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk ismerteti az Azure Security Center (ASC) IoT díjszabási modell, foglalja össze az összes kapcsolódó költségek, és azt ismerteti, hogyan kezelheti azokat.

## <a name="pricing"></a>Díjszabás

Az ASC IOT díjszabási modell áll két részből és történik, ha az IoT hub [engedélyezve](quickstart-onboard-iot-hub.md) az ASC IOT:

- Költség eszköz – beépített biztonsági funkciókat az IoT Hub-naplók elemzése alapján.

- Költség-ben – fokozott biztonsági képességeket biztonsági üzeneteket az IoT Edge vagy a levél eszközök alapján.

  >[!Note]
  > Biztonsági üzeneteket az IoT hub kvóta fogyasztás is számítunk fel.

További információkért lásd: [a Security Center díjszabási](https://azure.microsoft.com/en-us/pricing/details/security-center/).

## <a name="associated-costs"></a>Kapcsolódó költségek

Az IoT ASC kapcsolódó költségek, amelyek nem részei a közvetlen díjszabás két típusa van:

- Az IoT Hub kvóta fogyasztás

- Log Analytics tárolási költségek

Bizonyos szolgáltatások megtagadja a beállítások módosításával csökkentheti kapcsolódó költségek.

A beállítások módosításához:

1. Open IoT Hub.

2. A **biztonsági**, kattintson a **áttekintése**.

3. Kattintson a **beállítások**.

A következő táblázat összegzését a kapcsolódó költségek és az egyes lehetőségek következményeit.

|     | Használat | Megjegyzés |
| --- | --- | --- |
| **Az IoT Hub kvóta fogyasztás** |  |
| [Eszköz exportálása](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices) feladat (ikereszköz exportálás) | Naponta egyszer | Tiltsa le _ikereszköz metaadat-gyűjtemény_ |
| **Log Analytics-tároló** |  |
| Eszköz javaslatok és riasztások| Biztonsági javaslatok és a szolgáltatás által létrehozott riasztások | Nem kötelező |
| Nyers biztonsági adatok| IoT-eszközökről, amelyek biztonsági ügynök által gyűjtött biztonsági nyers adatok | Tiltsa le _nyers eszköz biztonsági eseményeket_ |

>[!Important]
> Az elérhető biztonsági funkciók súlyos következmények engedélyezés rendelkezik.
  
| Elutasítás | Következmények |
| --- | --- |
| _Ikereszköz metaadat-gyűjtemény_ | Tiltsa le [egyéni riasztások](quickstart-create-custom-alerts.md) |
| | IoT Edge-jegyzékfájl javaslatok letiltása |
| | Tiltsa le az eszköz azonosító-alapú javaslatok és riasztások |
| _Store nyers eszköz biztonsági események_ | Az eszköz operációs rendszer általános javaslatok részletei nem érhetők el. |
| | A részletek [riasztás](concept-security-alerts.md) és [javaslat](concept-recommendations.md) vizsgálatok során nem érhetők el. |


## <a name="see-also"></a>Lásd még

- Hozzáférés a [nyers biztonsági adatok](how-to-security-data-access.md)
- [Vizsgálja meg az eszköz](how-to-investigate-device.md)
- Ismertetés és felfedezés [biztonsági javaslatok](concept-recommendations.md)
- Ismertetés és felfedezés [biztonsági riasztások](concept-security-alerts.md)
