---
title: Az Azure-infrastruktúra megfelelőségi automatizálásához használja az Inspect
description: Ismerje meg, hogyan használhatja az Inspect az Azure-beli üzemelő példányokkal kapcsolatos problémák észleléséhez
keywords: Azure, Chef, devops, Virtual Machines, áttekintés, automatizálás, Inspect
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: 2531277eb1aa6048c93240031652e09582409e56
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158226"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Az Azure-infrastruktúra megfelelőségi automatizálásához használja az Inspect

Az [Inspec](https://www.chef.io/inspec/) a Chef nyílt forráskódú nyelve, amely leírja a biztonsági & megfelelőségi szabályokat, amelyek megoszthatók a szoftverfejlesztők, a műveletek és a biztonsági mérnökök között. Az Inspect úgy működik, hogy összehasonlítja az infrastruktúra aktuális állapotát a kívánt állapottal, amelyet könnyen olvasható és könnyen írható inspec-kódban használhat. Az inspec észleli a szabálysértéseket, és egy jelentés formájában jeleníti meg az eredményeket, de a szervizelés irányítását is lehetővé teszi.

Az Inspect használhatja az erőforrások és erőforráscsoportok állapotának ellenőrzéséhez egy előfizetésben, beleértve a virtuális gépeket, a hálózati konfigurációkat, a Azure Active Directory beállításokat és egyebeket.

Ez a cikk a inspec használatának előnyeit ismerteti az Azure-ban az adatvédelem és a megfelelőség egyszerűbbé tétele érdekében.

## <a name="make-compliance-easy-to-understand-and-assess"></a>A megfelelőség egyszerű megismerése és értékelése

A táblázatokban vagy a Word-dokumentumokban írt megfelelőségi dokumentáció a követelményeket az értelmezésnek megfelelően megnyitva hagyja. Az inspec-val a követelményeket verzióra, végrehajtható, emberi olvashatóságú kódra alakíthatja át. A kód lecseréli a beszélgetéseket arról, hogy mit kell értékelni a világos szándékkal rendelkező kézzelfogható tesztek mellett.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>A teljes flottára kiterjedő problémák észlelése és szervizelésük rangsorolása

Az inspec ügynök nélküli észlelési módja lehetővé teszi, hogy gyorsan mérje fel – az expozíciós szintet. A Impact/súlyossági pontozáshoz beépített metaadatok segítenek meghatározni, hogy mely területeken kell összpontosítani a szervizelésre. Az új biztonsági rések vagy rendeletek alapján gyorsan és azonnal is írhat szabályokat.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Azure-beli virtuális gépek naplózása házirend vendég konfigurációval

Az Azure közvetlenül támogatja a Chef inspec-definíciók használatát az Azure-beli virtuális gépek [Azure Policy vendég konfiguráción](/azure/governance/policy/concepts/guest-configuration)keresztüli naplózásához. A vendég konfigurációja kiértékel egy linuxos virtuális gépet egy megadott Chef inspec-definícióra, és visszaküldi a megfelelőségi jelentést Azure Policy. A naplózások eredményeit Azure Monitor naplókon keresztül is jelenteni kell; riasztások és egyéb automatizálási forgatókönyvek engedélyezése.

## <a name="satisfy-audits"></a>Megfelel a naplózásoknak

Az Inspect használatával bármikor válaszolhat a naplózási kérdésekre – nem csak előre meghatározott időközönként, például negyedévente vagy évente. Ha folyamatosan futtatja az inspec-teszteket, akkor egy auditálási ciklust kell megadnia, amely ismeri a megfelelőségi testtartást és az előzményeket, nem pedig a könyvvizsgáló megállapításai alapján.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"] 
> [Az Inspect kipróbálása a Azure Cloud Shellban](https://shell.azure.com)