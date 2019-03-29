---
title: ASC megismerheti az IoT-költségek előzetes verzió |} A Microsoft Docs
description: További információ az IoT, és hogyan vezérelheti őket az ASC kapcsolódó költségeket.
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
ms.openlocfilehash: e694bb1a121b172fe67fbde0bd956700a75dfe12
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580658"
---
# <a name="pricing-and-associated-costs"></a>Díjszabás és a kapcsolódó költségek

> [!IMPORTANT]
> Az IoT ASC jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk ismerteti az ASC IOT díjszabási modell, foglalja össze az összes kapcsolódó költségek, és azt ismerteti, hogyan kezelheti azokat.

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
