---
title: Azure App Service-webalkalmazások és -API-k védelme
description: Ez a cikk segít megkezdeni a Azure App Service webalkalmazások és API-k védelmét Azure Security Centerban.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: ec3fb8609612f3920e330da7922fdd1eb8883305
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89459776"
---
# <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Azure App Service-webalkalmazások és -API-k védelme

A Azure App Service egy teljes körűen felügyelt platform a webalkalmazások és API-k létrehozásához és üzemeltetéséhez anélkül, hogy az infrastruktúrát kellene kezelnie. Felügyeleti, monitorozási és üzemeltetési megállapításokat biztosít a vállalati szintű teljesítmény-, biztonsági és megfelelőségi követelmények teljesítéséhez. További információ: [Azure app Service](https://azure.microsoft.com/services/app-service/).

A Azure App Service-csomag komplex veszélyforrások elleni védelmének engedélyezéséhez a következőket kell tennie:

* Előfizetés a Azure Security Center Standard díjszabási szintjére
* Engedélyezze a App Service tervet az alább látható módon. A Security Center natív módon van integrálva a App Serviceval, így nincs szükség az üzembe helyezésre és a bevezetésre – az integráció átlátható.
* A dedikált gépekhez társított App Service terv. A támogatott csomagok a következők: alapszintű, standard, prémium, elkülönített vagy Linux. A Security Center nem támogatja az ingyenes, közös vagy felhasználási terveket. További információ: App Service- [csomagok](https://azure.microsoft.com/pricing/details/app-service/plans/).

Ha a App Servicei terv engedélyezve van, Security Center értékeli a App Service-csomag által érintett erőforrásokat, és a megállapításai alapján biztonsági javaslatokat hoz létre. Security Center védi a virtuális gép azon példányát, amelyben a App Service fut, és a felügyeleti felületet. Emellett figyeli a App Service-ban futó alkalmazásaitól érkező és onnan érkező kérelmeket és válaszokat.

A Security Center kihasználja a felhő méretezését, és azt, hogy az Azure hogyan rendelkezik felhőalapú szolgáltatóként, hogy figyelje a gyakori webalkalmazás-támadásokat. A Security Center képes észlelni az alkalmazásai támadásait, és azonosítani a feltörekvő támadásokat – még a felderítési fázisban is, miközben a támadók a biztonsági réseket több Azure által üzemeltetett alkalmazásban azonosítják. Az Azure-natív szolgáltatásként a Security Center egy egyedi pozícióban is elérhető, amely lehetővé teszi, hogy a gazdagépen alapuló biztonsági elemzések is elérhetők legyenek, amelyek a kihasználatlan számítási csomópontokra vonatkoznak, így Security Center a már kiaknázott webalkalmazások elleni támadások észlelésére. További részletek: [Azure app Service veszélyforrások elleni védelme](threat-protection.md#app-services).


## <a name="enable-monitoring-and-protection-of-app-service"></a>A App Service monitorozásának és védelmének engedélyezése

1. A Azure Portal válassza a Security Center lehetőséget.
2. Lépjen a **díjszabás & beállítások** elemre, és válasszon egy előfizetést.
3. Az **árképzési**csomag alatt az **app Service** -sorban állítsa be a csomagot az **engedélyezett**értékre.

    [![Az App Services engedélyezése a standard szintű előfizetésben](media/security-center-app-services/app-services-toggle.png)](media/security-center-app-services/app-services-toggle.png#lightbox)


>[!NOTE]
> Az **erőforrás-mennyiséghez** felsorolt példányok száma a számítási példányok teljes számát jelöli az előfizetés összes app Service csomagjában, amely az árképzési rétegek paneljének megnyitásakor fut.
>
> Azure App Service számos különböző csomagot kínál. A App Service terv meghatározza a futtatandó webalkalmazás számítási erőforrásainak készletét. Ezek egyenértékűek a hagyományos webes üzemeltetésű kiszolgáló-farmokkal. Egy vagy több alkalmazás úgy konfigurálható, hogy ugyanazon a számítási erőforrásokon (vagy ugyanabban a App Service tervben) fusson.
>
>A darabszám ellenőrzéséhez lépjen az Azure Portalon a "App Service csomagok" elemre, ahol megtekintheti az egyes csomagokban használt számítási példányok számát. 






Ha le szeretné tiltani a App Service figyelését és javaslatait, ismételje meg ezt a folyamatot, és kapcsolja ki a **app Service** tervet a **letiltáshoz**.



## <a name="see-also"></a>Lásd még
Ebben a cikkben megismerkedhetett az Azure Security Center figyelési funkcióinak használatával. Ha többet szeretne megtudni a Azure Security Centerről, tekintse meg a következő cikkeket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md): Ez a cikk bemutatja, hogyan konfigurálhat biztonsági beállításokat az Azure Security Centerben.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md): A biztonsági riasztások kezelése és az azokra való reagálás.
* [App Services](security-center-virtual-machine-protection.md#app-services): Tekintse meg az App Service-környezetek listáját az állapot összegzésével.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md): Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security blog](https://docs.microsoft.com/archive/blogs/azuresecurity/): Blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.
