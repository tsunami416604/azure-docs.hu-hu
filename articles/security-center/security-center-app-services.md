---
title: Az Azure App Service-alapú webalkalmazások és API-k védelme
description: Ez a cikk segítséget nyújt az Azure App Service-webalkalmazások és API-k védelmének megkezdéséhez az Azure Security Centerben.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 2d81e1a1218add504e1e35015276b6924da0e3e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616483"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Az Azure App Service-alapú webalkalmazások és API-k védelme

Az Azure App Service egy teljes körűen felügyelt platform a webalkalmazások és API-k létrehozásához és üzemeltetéséhez anélkül, hogy aggódnia kellene az infrastruktúra kezelése miatt. Felügyeleti, figyelési és működési elemzéseket biztosít a nagyvállalati szintű teljesítmény-, biztonsági és megfelelőségi követelmények teljesítéséhez. További információ: [Azure App Service](https://azure.microsoft.com/services/app-service/).

Az Azure App Service-csomag speciális veszélyforrások elleni védelmének engedélyezéséhez a következőket kell tennie:

* Előfizetés az Azure Security Center standard díjszabási csomagra
* Engedélyezze az App Service-csomagot az alábbiak szerint. A Security Center natív módon integrálva van az App Service szolgáltatással, így nincs szükség üzembe helyezésre és bevezetésre – az integráció átlátható.
* Rendelkeznie kell egy App Service-csomag, amely dedikált gépek társított. Támogatott csomagok: Alapszintű, Standard, Prémium, Elkülönített vagy Linux. A Security Center nem támogatja az ingyenes, megosztott vagy a felhasználási csomagokat. További információ: [App Service Plans](https://azure.microsoft.com/pricing/details/app-service/plans/).

Ha az App Service-csomag engedélyezve van, a Security Center felméri az App Service-csomag által lefedett erőforrásokat, és a megállapításai alapján biztonsági javaslatokat készít. A Security Center védi a virtuális gép példányát, amelyben az App Service fut, és a felügyeleti felület. Emellett figyeli az App Service-ben futó alkalmazásokba küldött kérelmeket és válaszokat.

A Security Center kihasználja a felhő méretét, valamint az Azure felhőszolgáltatóként való láthatóságát a gyakori webalkalmazás-támadások figyeléséhez. A Security Center felderítheti az alkalmazások elleni támadásokat, és azonosíthatja a felmerülő támadásokat – még akkor is, ha a támadók a felderítési fázisban vannak, és több Azure által üzemeltetett alkalmazás biztonsági résének azonosítását is vizsgálja. Azure-natív szolgáltatásként a Security Center is egyedülálló helyzetben van, hogy gazdagép-alapú biztonsági elemzéseket kínáljon a PaaS alapjául szolgáló számítási csomópontokra, lehetővé téve a Security Center számára a már kiaknázott webalkalmazások elleni támadások észlelését. További részletek: [Fenyegetésvédelem az Azure App Service.for](threat-protection.md#app-services)more details, see Threat Protection for Azure App Service.


## <a name="enabling-monitoring-and-protection-of-app-service"></a>Az App Service figyelésének és védelmének engedélyezése

1. Az Azure Portalon válassza a Security Center lehetőséget.
2. Nyissa meg **az Árképzési & beállításokat,** és válasszon egy előfizetést.
3. A **Tarifacsomag**csoportban az **App Service-sorban** kapcsolja be a csomagot **engedélyezve.**

    [![Alkalmazásszolgáltatások engedélyezése a standard szintű előfizetésben](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> Az **erőforrás-mennyiséghez** felsorolt példányok száma az előfizetés összes App Service-csomagjában a számítási példányok teljes számát jelenti, amely abban a pillanatban fut, amikor megnyitotta a tarifacsomag panelt.
>
> Az Azure App Service számos csomaglehetőséget kínál. Az App Service-csomag határozza meg a webalkalmazás futtatásához szükséges számítási erőforrások készletét. Ezek egyenértékűek a szerver gazdaságok hagyományos web hosting. Egy vagy több alkalmazás konfigurálható úgy, hogy ugyanazon a számítási erőforráson (vagy ugyanabban az App Service-csomagban) fusson.
>
>A számláló érvényesítéséhez tekintse meg az "App Service-csomagok" az Azure Portalon, ahol láthatja az egyes csomagok által használt számítási példányok számát. 






Ha le szeretné tiltani az App Service figyelését és ajánlásait, ismételje meg ezt a folyamatot, és kapcsolja be az **App Service-csomagot** **letiltva.**



## <a name="see-also"></a>Lásd még
Ebben a cikkben megismerkedhetett az Azure Security Center figyelési funkcióinak használatával. Ha többet szeretne megtudni az Azure Security Centerről, olvassa el az alábbi cikkeket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md): Ez a cikk bemutatja, hogyan konfigurálhat biztonsági beállításokat az Azure Security Centerben.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelése és az azokra való reagálás.
* [Alkalmazásszolgáltatások:](security-center-virtual-machine-protection.md#app-services)Tekintse meg az Alkalmazásszolgáltatási környezetek listáját az állapotösszefoglalókkal.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md): Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
