---
title: Díjszabás és kapcsolódó költségek
description: Ismerje meg az Azure Security Center for IoT-val kapcsolatos költségeket, és hogyan szabályozhatja azokat.
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
ms.date: 09/27/2019
ms.author: mlottner
ms.openlocfilehash: fe117cf8d05ba3392b71858acf94d1fc88c1a527
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311566"
---
# <a name="pricing-and-associated-costs"></a>Díjszabás és kapcsolódó költségek

Ez a cikk ismerteti az Azure Security Center for IoT díjszabási modell, összegzi az összes kapcsolódó költségeket, és elmagyarázza, hogyan kezelheti őket.

## <a name="pricing"></a>Díjszabás

Az Azure Security Center for IoT díjszabási modell két részből áll, és a számlázás, ha egy IoT Hub [engedélyezve](quickstart-onboard-iot-hub.md) van az Azure Security Center for IoT:

- Ár eszközönként – beépített biztonsági képességek az IoT Hub-naplók elemzése alapján.

- Költség üzenetszerint – továbbfejlesztett biztonsági képességek az IoT Edge-ről vagy a levéleszközökről érkező biztonsági üzenetek alapján.

További információt a [Security Center díjszabása](https://azure.microsoft.com/pricing/details/security-center/)című témakörben talál.

## <a name="associated-costs"></a>Kapcsolódó költségek

Az Azure Security Center for IoT kapcsolódó költségekkel rendelkezik, amelyek nem részei a közvetlen díjszabásnak:

- A Log Analytics tárolási költségei

Csökkentheti a kapcsolódó költségeket, ha leiratkozik bizonyos megoldási funkciókról. A beállítások módosításával leiratkozhat.

A beállítások módosítása:

1. Nyissa meg az IoT hubot.

1. A **Biztonság**csoportban kattintson **az Áttekintés gombra.**

1. Kattintson a **Beállítások** elemre.

Az alábbi táblázat az egyes lehetőségek kapcsolódó költségeit és következményeit tartalmazza.

|     | Használat | Megjegyzés |
| --- | --- | --- |
| **Log Analytics-tárhely** |  |
| Eszközajánlás és riasztások| A szolgáltatás által létrehozott biztonsági ajánlás és riasztások | Nem választható |
| Nyers biztonsági adatok| Belső biztonsági adatok IoT-eszközökről, biztonsági ügynökök által gyűjtött adatok | _A nyers eszköz biztonsági eseményeinek letiltása_ |
|

>[!Important]
> A leiratkozás súlyos következményekkel jár az Azure Security Center for IoT biztonsági funkciók elérhetőségére nézve.

| Leiratkozás | Következmények |
| --- | --- |
| _Kettős metaadat-gyűjtés_ | [Egyéni riasztások letiltása](quickstart-create-custom-alerts.md) |
| | IoT Edge-jegyzékjavaslatok letiltása |
| | Eszközidentitás-alapú javaslatok és riasztások letiltása |
| _Nyers eszközbiztonsági események tárolása_ | Az eszköz operációs rendszerére vonatkozó alapjavaslatok részletei nem érhetők el |
| | A [riasztási](concept-security-alerts.md) és [ajánlási](concept-recommendations.md) vizsgálatokkal kapcsolatos részletek nem állnak rendelkezésre |
|

## <a name="see-also"></a>Lásd még

- A [nyers biztonsági adatok](how-to-security-data-access.md) elérése
- [Eszköz vizsgálata](how-to-investigate-device.md)
- A [biztonsági javaslatok](concept-recommendations.md) ismertetése és feltárása
- A [biztonsági riasztások ismertetése](concept-security-alerts.md) és feltárása
