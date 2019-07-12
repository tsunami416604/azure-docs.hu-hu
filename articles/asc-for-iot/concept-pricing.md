---
title: Megismerheti az Azure Security Center az IoT előzetes költségek |} A Microsoft Docs
description: További információ az Azure Security Center IoT, és hogyan szabályozhatja a költségeket.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ef839708-4574-4a40-bc45-07005f8e9daf
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: dd041cdb1608eab60fa2a5fa756f381656a13a46
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618438"
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

További információkért lásd: [a Security Center díjszabási](https://azure.microsoft.com/pricing/details/security-center/).

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
| [Eszköz exportálása](https://docs.microsoft.com/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices) feladat (ikereszköz exportálás) | Naponta egyszer | Tiltsa le _ikereszköz metaadat-gyűjtemény_ |
| **Log Analytics-tároló** |  |
| Eszköz javaslatok és riasztások| Biztonsági javaslatok és a szolgáltatás által létrehozott riasztások | Nem kötelező |
| Nyers biztonsági adatok| IoT-eszközökről, amelyek biztonsági ügynök által gyűjtött biztonsági nyers adatok | Tiltsa le _nyers eszköz biztonsági eseményeket_ |

>[!Important]
> Az elérhető biztonsági funkciók súlyos következmények engedélyezés rendelkezik.
  
| Kikapcsolás | Következmények |
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
