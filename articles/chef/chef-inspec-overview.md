---
title: Az InSpec használata az Azure-infrastruktúra megfelelőségi automatizálásához
description: Ismerje meg, hogyan észlelheti az Azure-környezetekben felmerülő problémákat az InSpec segítségével
keywords: az úr, szakács, devops, virtuális gépek, áttekintés, automatizálás, inspec
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: 2531277eb1aa6048c93240031652e09582409e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158226"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Az InSpec használata az Azure-infrastruktúra megfelelőségi automatizálásához

[Az InSpec](https://www.chef.io/inspec/) a Chef nyílt forráskódú nyelve a szoftvermérnökök, műveletek és biztonsági mérnökök között megosztható biztonsági & megfelelőségi szabályok leírásához. Az InSpec úgy működik, hogy összehasonlítja az infrastruktúra tényleges állapotát a kívánt állapottal, amelyet könnyen olvasható és könnyen írható InSpec-kódban fejez ki. Az InSpec észleli a szabálysértéseket, és jelentés formájában jeleníti meg az eredményeket, de ön irányítja a szervizelést.

Az InSpec segítségével ellenőrizheti az erőforrások és erőforráscsoportok állapotát egy előfizetésben, beleértve a virtuális gépeket, a hálózati konfigurációkat, az Azure Active Directory beállításait és egyebeket.

Ez a cikk ismerteti az InSpec használatának előnyeit az Azure biztonságának és megfelelőségének megkönnyítése érdekében.

## <a name="make-compliance-easy-to-understand-and-assess"></a>A megfelelőség könnyű megértésének és értékelésének lehetővé tévő lehetővé tévő lehetővé

A táblázatokban vagy Word-dokumentumokban írt megfelelőségi dokumentáció a követelményeket értelmezésre nyitotta. Az InSpec segítségével verziózott, végrehajtható, ember által olvasható kóddá alakíthatja át a követelményeket. A kód egyértelmű szándékkal helyettesíti a beszélgetéseket arról, hogy mit kell értékelni a kézzelfogható tesztek javára.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>A flottaszintű problémák felderítése és a helyreállítás fontossági sorrendbe állítása

Az InSpec ügynök nélküli észlelési módja lehetővé teszi, hogy gyorsan felmérje - nagy léptékben - az expozíciós szintet. A hatás/súlyosság pontozáshoz beépített metaadatok segítenek meghatározni, hogy mely területekre összpontosítson a szervizeléshez. Az új biztonsági résekre vagy előírásokra adott válaszként gyorsan is írhat szabályokat, és azonnal kivezetheti őket.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Azure-beli virtuális gépek naplózása a házirend vendégkonfigurációjával

Az Azure közvetlenül támogatja a Chef InSpec-definíciók használatát az Azure-alapú virtuális gépek naplózásához az [Azure Policy Vendégkonfiguráció szolgáltatáson](/azure/governance/policy/concepts/guest-configuration)keresztül. Vendég konfiguráció kiértékeli a Linux virtuális gép egy adott Chef InSpec definíciót, és jelenti a megfelelőségi vissza az Azure Policy keresztül. A naplózások eredményeit az Azure Monitor naplói is jelentik; riasztások és egyéb automatizálási forgatókönyvek engedélyezése.

## <a name="satisfy-audits"></a>Auditálások teljesítése

Az InSpec segítségével bármikor válaszolhat a naplózási kérdésekre – nem csak előre meghatározott időközönként, például negyedéves vagy éves időközönként. Az InSpec tesztek folyamatos futtatásával ellenőrzési ciklust adhat meg a pontos megfelelőségi állapot és előzmények ismeretében, ahelyett, hogy meglepődne a könyvvizsgáló megállapításai.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Próbálja ki az InSpec-et az Azure Cloud Shellben](https://shell.azure.com)