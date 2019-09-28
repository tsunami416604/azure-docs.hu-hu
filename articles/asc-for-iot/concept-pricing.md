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
ms.date: 09/27/2019
ms.author: mlottner
ms.openlocfilehash: dc9dcbfd00b5205fa5c66e334b30c76d549d8a42
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348522"
---
# <a name="pricing-and-associated-costs"></a>Díjszabás és kapcsolódó költségek

Ez a cikk ismerteti Azure Security Center IoT díjszabási modelljét, összegzi az összes kapcsolódó költséget, és ismerteti, hogyan kezelheti őket.

## <a name="pricing"></a>Díjszabás

A IoT díjszabási modell Azure Security Center két részből áll, és akkor számítunk fel díjat, ha IoT Hub [engedélyezve](quickstart-onboard-iot-hub.md) van a IoT-ben Azure Security Centerban:

- A IoT Hub naplók elemzése alapján az eszközök által beépített biztonsági funkciók költséghatékonyak.

- A IoT Edge-vagy Leaf-eszközökről származó biztonsági üzeneteken alapuló, üzenetekkel bővített biztonsági képességek díja.


További információ: [Security Center díjszabása](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Kapcsolódó költségek

A IoT Azure Security Center kapcsolódó költségek, amelyek nem részei a közvetlen díjszabásnak:


- Tárolási költségek Log Analytics

Az egyes megoldási funkciók leválasztásával csökkentheti a kapcsolódó költségeket. A beállítások módosításával letilthatja a beállításokat.

Beállítások módosítása:

1. Nyissa meg IoT Hub.

2. A **Biztonság**területen kattintson az **Áttekintés**elemre.

3. Kattintson a **Beállítások**elemre.

Az alábbi táblázat az egyes lehetőségek kapcsolódó költségeinek és következményeinek összegzését tartalmazza.

|     | Használat | Megjegyzés |
| --- | --- | --- |
| **Log Analytics Storage** |  |
| Eszközökre vonatkozó javaslatok és riasztások| A szolgáltatás által létrehozott biztonsági javaslatok és riasztások | Nem kötelező |
| Nyers biztonsági adatértékek| A biztonsági ügynökök által gyűjtött IoT-eszközökből származó nyers biztonsági adatok | A _nyers eszközök biztonsági eseményeinek tárolása_ letiltva |
|

>[!Important]
> A lemondás jelentős hatással van arra, hogy Azure Security Center a IoT biztonsági funkcióinak rendelkezésre állását. 
  
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
