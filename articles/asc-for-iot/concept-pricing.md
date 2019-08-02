---
title: A IoT költségeinek Azure Security Center ismertetése | Microsoft Docs
description: További információ a IoT Azure Security Center kapcsolatos költségekről és azok szabályozásáról.
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
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 603df1def011232ad2120c37ad1ba256f2a30526
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596522"
---
# <a name="pricing-and-associated-costs"></a>Díjszabás és kapcsolódó költségek

Ez a cikk ismerteti Azure Security Center IoT díjszabási modelljét, összegzi az összes kapcsolódó költséget, és ismerteti, hogyan kezelheti őket.

## <a name="pricing"></a>Díjszabás

A IoT díjszabási modell Azure Security Center két részből áll, és akkor számítunk fel díjat, ha IoT Hub [engedélyezve](quickstart-onboard-iot-hub.md) van a IoT-ben Azure Security Centerban:

- A IoT Hub naplók elemzése alapján az eszközök által beépített biztonsági funkciók költséghatékonyak.

- A IoT Edge-vagy Leaf-eszközökről származó biztonsági üzeneteken alapuló, üzenetekkel bővített biztonsági képességek díja.

  >[!Note]
  > A biztonsági üzenetekben a IoT Hub kvóta-felhasználás is felmerül.

További információ: [Security Center díjszabása](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Kapcsolódó költségek

A IoT Azure Security Center két típusú társított költséggel rendelkezik, amelyek nem részei a közvetlen díjszabásnak:

- IoT Hub kvóta-felhasználás

- Tárolási költségek Log Analytics

A kapcsolódó költségeket a beállítások módosításával csökkentheti bizonyos szolgáltatások leválasztásával.

Beállítások módosítása:

1. Nyissa meg IoT Hub.

2. A **Biztonság**területen kattintson az **Áttekintés**elemre.

3. Kattintson a **Beállítások**elemre.

Az alábbi táblázat az egyes lehetőségek kapcsolódó költségeinek és következményeinek összegzését tartalmazza.

|     | Használat | Megjegyzés |
| --- | --- | --- |
| **IoT Hub kvóta-felhasználás** |  |
| [Eszköz exportálási](https://docs.microsoft.com/azure/iot-hub/iot-hub-bulk-identity-mgmt#export-devices) feladata (Twin export) | Naponta egyszer | _Twin metadata-gyűjtemény_ letiltása |
| **Log Analytics Storage** |  |
| Eszközökre vonatkozó javaslatok és riasztások| A szolgáltatás által létrehozott biztonsági javaslatok és riasztások | Nem kötelező |
| Nyers biztonsági adatértékek| A biztonsági ügynökök által gyűjtött IoT-eszközökből származó nyers biztonsági adatok | A _nyers eszközök biztonsági eseményeinek tárolása_ letiltva |

>[!Important]
> A kivonás jelentős hatással van az elérhető biztonsági funkciókra.
  
| Elutasítás | Következmények |
| --- | --- |
| _Twin metadata-gyűjtemény_ | [Egyéni riasztások](quickstart-create-custom-alerts.md) letiltása |
| | IoT Edge manifest-javaslatok letiltása |
| | Eszköz-identitás-alapú javaslatok és riasztások letiltása |
| _Nyers eszközök biztonsági eseményeinek tárolása_ | Az eszköz operációsrendszer-alapkonfigurációjának javaslatairól nem érhetők el adatok |
| | A [riasztások](concept-security-alerts.md) és [javaslatok](concept-recommendations.md) vizsgálatának részletei nem érhetők el |
|


## <a name="see-also"></a>Lásd még

- A [nyers biztonsági adataihoz](how-to-security-data-access.md) való hozzáférés
- [Eszköz vizsgálata](how-to-investigate-device.md)
- A [biztonsági javaslatok](concept-recommendations.md) megismerése és megismerése
- A [biztonsági riasztások](concept-security-alerts.md) megismerése és megismerése
