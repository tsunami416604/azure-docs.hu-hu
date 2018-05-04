---
title: Házirend karbantartási Azure verem |} Microsoft Docs
description: További tudnivalók az Azure verem karbantartási házirend, és hogyan kell fenntartani az integrált rendszer támogatott állapotban.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: brenduns
ms.reviewer: harik
ms.openlocfilehash: 77cc2f80588a104880e8149daccc6debd1ec43bc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="azure-stack-servicing-policy"></a>Az Azure verem karbantartása házirend
Ez a cikk ismerteti a karbantartási házirend integrált Azure verem rendszerekhez, és hogy a rendszer egy támogatott állapotban kell tennie. 

## <a name="update-package-types"></a>A csomagok frissítése

A frissítési csomagok integrált rendszerekhez két típusa van: 

- **A Microsoft**. A Microsoft a Microsoft a szoftverfrissítési csomagokat a végpont karbantartási életciklusa felelős. A csomagok tartalmazhatnak, a Windows Server legújabb biztonsági frissítéseket, nem biztonsági frissítések és Azure verem szolgáltatás-frissítéseket. Közvetlenül a Microsoft letöltheti ezek frissítési csomagokat.

- **OEM szállító által biztosított hardverfrissítések**. Az Azure verem hardverszállító partnerétől felelősek a végpont karbantartási életciklusa (beleértve az útmutató) a hardverrel kapcsolatos belső vezérlőprogram és illesztőprogram-csomagok frissítése. Emellett Azure verem hardverszállító partnerétől tulajdonosai, és minden szoftver- és a hardver életciklus gazdagépen útmutatást karbantartása. Az OEM forgalmazó futtatja ezeket a saját letöltési helyen-csomagok frissítése.


## <a name="update-package-release-cadence"></a>Frissítési csomag kiadás ütemben történik
Microsoft havi ütemben történik a szoftverfrissítési csomagokat a kibocsátási vár. Azonban akkor lehetséges, hogy több, vagy nincs frissítés kiadásokban hónap. OEM hardverszállítók a frissítéseket igény szerint alapon. 

Tervezze meg és kezelheti a frissítéseket, és hogyan állapítható meg a jelenlegi verziót a dokumentációval [kezelése frissítések – áttekintés](azure-stack-updates.md). Egy adott frissítés, hogyan lehet letölteni, beleértve információt talál a kibocsátási megjegyzéseket, amelyek frissítése: 
- [Az Azure verem 1803 frissítés](azure-stack-update-1803.md)
- [Az Azure verem 1802 frissítés](azure-stack-update-1802.md)
- [Az Azure verem 1712 frissítés](azure-stack-update-1712.md)



## <a name="hotfixes"></a>Gyorsjavítások
Alkalmanként a Microsoft biztosít gyorsjavítások Azure verem, hogy egy adott probléma megoldásához, de gyakran megelőző időérzékeny cím.  Minden gyorsjavítás megjelenik a megfelelő Microsoft Tudásbázis, amely leírja, a probléma, OK és megoldás. 

Gyorsjavítások letöltődnek és Azure verem hasonlóan a rendszeres teljes frissítési csomagok telepítése. Azonban ellentétben a teljes frissítés gyorsjavítások perc is telepíthető. Azt javasoljuk, hogy Azure verem operátorok karbantartási időszakok beállítása, gyorsjavítások telepítésekor. Gyorsjavítások az Azure-verem felhő verziójának frissítését, hogy könnyen megállapíthatja, hogy ha a gyorsjavítás telepítve van-e. Külön gyorsjavítás még a támogatási Azure verem verziói biztosított. Egy adott közelítés minden javítás összesítő jellegű, és ugyanazt a verziót az előző frissítését is tartalmazza. További információk egy adott gyorsjavítást a Tudásbázis megfelelő javításokat alkalmazhatóságát a következő cikket.  


## <a name="keep-your-system-under-support"></a>A rendszer a támogatás
Továbbra is támogatja, az Azure Alkalmazásveremben üzembe kell tartani, aktuális. A kizárási házirend frissítések: az Azure-verem üzembe helyezés támogatására biztosítása csak a legutóbb kiadott frissített verzió vagy futtassa az előző két frissítési verziók egyikét. Gyorsjavítások nem tekinthetők fő frissítési verziók. Ha az Azure-verem felhő mögött *kettőnél több frissítések*, nem megfelelő minősül, és a frissíteniük kell legalább a legrégebbi támogatott verzió a fiókrendszergazdától támogatást. 

Például ha a rendelkezésre álló legfrissebb frissített verzióra 1805, és az előző két frissítési csomagokat volt 1804 és 1803, 1803 és a 1804 maradnak támogatása. 1802 azonban nem támogatott. A házirend igaz, ha az egy vagy két nincs felszabadítás. Például ha a jelenlegi kiadásban 1805, és nem 1804 kiadás történt, az előző két frissítési csomagokat 1803 és 1802 továbbra is támogatott.

A Microsoft a szoftverfrissítési csomagokat nem összesítő és a korábbi csomag előfeltételként igényel. Ha úgy dönt, hogy egy vagy több frissítés késleltetésének, fontolja meg a teljes futásidejű, ha le szeretné kérdezni a legújabb verzióra. 

## <a name="get-support"></a>Támogatás kérése
Az Azure verem, az Azure támogatási folyamatot követi. A vállalati ügyfelek ismertetett folyamatot követve [létrehozása az Azure támogatási kérelmet](/azure/azure-supportability/how-to-create-azure-support-request). Ha egy ügyfél az egy Felhőszolgáltató (CSP), lépjen kapcsolatba a kriptográfiai Szolgáltató támogatásához.  További információkért lásd: a [Azure támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/). 


## <a name="next-steps"></a>További lépések

- [Azure-készletben frissítések kezelése](azure-stack-updates.md)


