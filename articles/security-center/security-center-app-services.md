---
title: App Services védelme Azure Security Centerban | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan védheti meg App Servicesét Azure Security Centerban.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 68f7c47f0a0f56085d632f1c1741318f440b41ee
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202478"
---
# <a name="protect-app-service-with-azure-security-center"></a>App Service védelemmel Azure Security Center
Ebből a cikkből megtudhatja, hogyan figyelheti és megvédheti a App Serviceon futó alkalmazásait a Azure Security Center használatával.

A App Service lehetővé teszi, hogy az infrastruktúra kezelése nélkül hozzon létre és működtessen webalkalmazásokat az Ön által választott programozási nyelven. App Service automatikus méretezést és magas rendelkezésre állást biztosít, a Windows és a Linux támogatását, valamint a GitHub, az Azure DevOps vagy bármely git-tárház automatikus üzembe helyezését. 

A támadók gyakran használják a webalkalmazások biztonsági réseit, mivel ezek közös és dinamikus felülettel rendelkeznek az interneten szinte minden szervezet számára. A App Serviceon futó alkalmazásokra irányuló kérelmek több, az Azure-adatközpontokban üzembe helyezett átjárón keresztül futnak a világ minden részén, az egyes kérelmeknek a megfelelő alkalmazásba való átirányításához. 

Azure Security Center a virtuális gép vagy igény szerinti példányok munkaterületén lévő munkaterületeken App Service-ben futtatott alkalmazásokra vonatkozó értékeléseket és javaslatokat is futtathat. Az Azure felhőalapú szolgáltatóként való láthatóságának kihasználásával Security Center elemzi a App Service belső naplóit, hogy figyelje a gyakori webalkalmazás-támadásokat, amelyek gyakran futnak több cél között.

Security Center kihasználja a felhő méretezését, hogy azonosítsa a támadásokat a App Service alkalmazásaiban, és koncentráljon a feltörekvő támadásokra, míg a támadók a felderítési fázisban vannak, és az Azure-ban üzemeltetett biztonsági rések azonosítására szolgálnak. A Security Center elemzési és gépi tanulási modellekkel fedi le az összes olyan interfészt, amely lehetővé teszi az ügyfelek számára, hogy a HTTP-n keresztül vagy egy felügyeleti módszeren keresztül használhassák az alkalmazásaikat. Továbbá, mivel az Azure-ban az első féltől származó szolgáltatás, Security Center is egyedi helyzetben van, hogy olyan gazdagép-alapú biztonsági elemzést nyújtson, amely a jelen Pásti mögöttes számítási csomópontokra terjed ki, ami lehetővé teszi a Security Center számára a webalkalmazások elleni támadások észlelését már kihasználva.

## <a name="prerequisites"></a>Előfeltételek

Az App Service monitorozásához és védelméhez egy, a dedikált gépekkel társított App Service-csomaggal kell rendelkeznie. A csomagok a következők: alapszintű, standard, prémium, izolált és Linux. Az Azure Security Center nem támogatja az ingyenes, közös és használatalapú csomagokat. További információ: App Service- [csomagok](https://azure.microsoft.com/pricing/details/app-service/plans/).

## <a name="security-center-protection"></a>Security Center védelem

Azure Security Center védi a virtuális gép azon példányát, amelyben a App Service fut, és a felügyeleti felületet. Emellett figyeli a App Service-ban futó alkalmazásaitól érkező és onnan érkező kérelmeket és válaszokat.

Security Center natív módon van integrálva a App Serviceekkel, így nincs szükség az üzembe helyezésre és a bevezetésre – az integráció teljesen átlátszó.



## <a name="enabling-monitoring-and-protection-of-app-service"></a>A App Service monitorozásának és védelmének engedélyezése

1. Az Azure-ban válassza a Security Center lehetőséget.
2. Lépjen a **díjszabás & beállítások** elemre, és válasszon egy előfizetést.
3. Az **árképzési**csomag alatt az **app Service** -sorban állítsa be a csomagot az **engedélyezett**értékre.

![App Service-váltás](./media/security-center-app-services/app-services-toggle.png)

>[!NOTE]
> Az erőforrás-mennyiséghez felsorolt példányok száma a díjszabási rétegek paneljének megnyitásakor aktív app Service-példányok számát jelöli. Mivel ez a szám a kiválasztott skálázási beállítások alapján változhat, a rendszer ennek megfelelően módosítja a felszámított példányok számát.

Ha le szeretné tiltani a App Service figyelését és javaslatait, ismételje meg ezt a folyamatot, és kapcsolja ki a **app Service** tervet a letiltáshoz.



## <a name="see-also"></a>Lásd még
Ebben a cikkben megismerkedhetett az Azure Security Center figyelési funkcióinak használatával. Az Azure Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása a Azure Security Centerban](tutorial-security-policy.md): Megtudhatja, hogyan konfigurálhatja a Azure Security Center biztonsági beállításait.
* [Biztonsági riasztások kezelése és válaszadás a Azure Security Centerban](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelésének és a riasztásokra való válaszadás módját ismertető útmutató.
* [App Services](security-center-virtual-machine-protection.md#app-services):  Tekintse meg az App Service-környezetek listáját az állapottal kapcsolatos összefoglalókkal.
* [Partneri megoldások monitorozása Azure Security Centerekkel](security-center-partner-solutions.md): A partnermegoldások állapotának figyelését ismertető útmutató.
* [Azure Security Center gyakori kérdések](security-center-faq.md): Gyakori kérdések a szolgáltatás használatával kapcsolatban.
* [Azure biztonsági blog](https://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
