---
title: Egy rendszerkép-előállító létrehozása az Azure DevTest Labs szolgáltatásban |} A Microsoft Docs
description: Ismerje meg, hogy egy egyéni rendszerkép-előállító létrehozása az Azure DevTest Labs szolgáltatásban.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: c7923282396481dcfd6aa19b75d110fa03e50931
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439902"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Egy egyéni rendszerkép-előállító létrehozása az Azure DevTest Labs szolgáltatásban
Ez a cikk bemutatja, hogyan állítható be egy egyéni rendszerkép gyári elérhető minta parancsfájlok használatával a [Git-tárház](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>Mi az, hogy egy kép gyári?
Egy rendszerkép factory az olyan konfiguráció, a kód megoldás, amely építésével és értékesítésével a kívánt konfigurációval rendszeres időközönként automatikusan-rendszerképek. A lemezképek lemezkép-előállító mindig naprakészek legyenek, és a folyamatos karbantartás szinte nullára után a teljes folyamat automatikus. És mivel a szükséges konfigurációk már a képen, a manuális konfigurálását a rendszer, az alap operációs rendszerrel egy virtuális gép létrehozása után az időt takaríthat.

A jelentős accelerator beolvasni a DevTest Labs szolgáltatásban létrehozott üzemkész állapotba fejlesztői asztali egyéni lemezképeket használ. Az egyéni lemezképek hátránya, hogy valami nincs extra fenntartása a tesztkörnyezetben. Például termékek próbaverziók lejárati idővel (vagy) újonnan kiadott biztonsági frissítések nem lesznek alkalmazva, amelyek kényszerítése számunkra, hogy rendszeresen frissíteni az egyéni rendszerképet. Egy rendszerkép-előállító rendelkező egy definíciót a kép a definíció alapján egyéni rendszerképek létrehozásához egy automatizált folyamattal és verziókövetési beadva.

A megoldás lehetővé teszi, hogy a virtuális gépek létrehozása az egyéni rendszerképekből nyújthat hatékony további folyamatos karbantartási költségek sebessége. Ez a megoldás is automatikusan egyéni rendszerképek létrehozása, ossza ki őket a többi DevTest Labs szolgáltatásban, és a régi képeket kivonni. A következő videót megismerkedhet a lemezkép gyári, és hogyan történik a DevTest Labs szolgáltatással.  Az Azure Powershell-parancsfájlok is szabadon elérhető, és található itt: [ http://aka.ms/dtlimagefactory ](http://aka.ms/dtlimagefactory).

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>A megoldás magas szintű nézetét
A megoldás lehetővé teszi, hogy a virtuális gépek létrehozása az egyéni rendszerképekből nyújthat hatékony további folyamatos karbantartási költségek sebessége. Ez a megoldás is automatikusan egyéni rendszerképek létrehozása és ossza ki őket a többi DevTest Labs szolgáltatásban. Az összes műveletet a DevTest Labs szolgáltatásban automatizálásához vezénylési motorként használhatja az Azure DevOps (korábbi nevén Visual Studio Team Services).

![A megoldás magas szintű nézetét](./media/create-image-factory/high-level-view-of-solution.png)

Van egy [DevTest Labs VSTS-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) , amely lehetővé teszi az egyes lépések végrehajtásához: 

- Egyéni kép létrehozása
- Virtuális gép létrehozása
- Virtuális gép törlése
- Környezet létrehozása
- Környezet törlése
- Töltse fel a környezetben

A DevTest Labs-bővítmény használata egyszerű módja, automatikusan a DevTest Labs szolgáltatásban az egyéni lemezképek létrehozásának megkezdéséhez.

Van egy másik végrehajtása egy összetettebb forgatókönyv PowerShell-parancsfájl használatával. PowerShell használatával, teljesen automatizálható egy lemezkép-előállító alapján a DevTest Labs szolgáltatásban a folyamatos integráció és folyamatos Készregyártás (CI/CD) eszközlánc használható. A másik megoldás követett elvek a következők:

- Gyakori frissítések nem változtak meg a lemezkép gyári van szükség. (például egy új típusú egyéni rendszerképet, automatikus kivonása a régi lemezképek hozzáadása egy új "végpont" DevTest Labs-egyéni rendszerképek kap, és így tovább.)
- A szokásos élvezik verziókövetési (infrastruktúra mint kód)
- Egyéni rendszerképek fogadása DevTest Labs szolgáltatásban nem lehet ugyanabban az előfizetésben az Azure (labs span előfizetések)
- PowerShell-parancsfájlokat kell lennie újrafelhasználható, ezért azt is üzembe helyezése további előállítók igény szerint

## <a name="next-steps"></a>További lépések
Helyezze át ebben a szakaszban szereplő következő cikket: [Egy rendszerkép-előállító Futtatás az Azure DevOps](image-factory-set-up-devops-lab.md)
