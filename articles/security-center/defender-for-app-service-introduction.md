---
title: Azure Defender for App Service – az előnyök és funkciók
description: Ismerje meg a App Service Azure Defender előnyeit és funkcióit.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: d15e881ca1246d463d8054fe5bd094efaff0cdd5
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977173"
---
# <a name="introduction-to-azure-defender-for-app-service"></a>A App Service Azure Defender bemutatása

A Azure App Service egy teljes körűen felügyelt platform a webalkalmazások és API-k létrehozásához és üzemeltetéséhez anélkül, hogy az infrastruktúrát kellene kezelnie. Felügyeleti, monitorozási és üzemeltetési megállapításokat biztosít a vállalati szintű teljesítmény-, biztonsági és megfelelőségi követelmények teljesítéséhez. További információ: [Azure app Service](https://azure.microsoft.com/services/app-service/).

Az **Azure Defender for app Service** a felhő méretezését használja a app Serviceon futó alkalmazások megcélzására irányuló támadások azonosítására. A támadók webes alkalmazásokat kereshetnek a gyengeségek megtalálásához és kiaknázásához. Az Azure-ban futó alkalmazásokra irányuló kérelmek az adott környezetbe való átirányításuk előtt több átjárón keresztül futnak, ahol megvizsgálják és naplózzák azokat. Ezeket az információkat a rendszer felhasználja a biztonsági rések és a támadók azonosítására, valamint a később felhasználható új mintázatok megismerésére.

Az Azure felhőalapú szolgáltatóként való láthatóságának használatával Security Center elemzi App Service belső naplókat, hogy azonosítsa a támadási módszereket több célponton. A módszertan például kiterjedt keresési és elosztott támadásokat tartalmaz. Ez a típusú támadás általában az IP-címek egy kis részhalmazát mutatja be, és a hasonló végpontokra való bejárási mintákat jeleníti meg több gazdagépen. A támadások egy sebezhető oldalra vagy beépülő modulra keresnek, és egyetlen gazdagép szempontjából nem azonosíthatók.


## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Előnézet|
|Árképzési|Az [Azure Defender for app Service](azure-defender.md) számlázása [a díjszabási oldalon](security-center-pricing.md) látható|
|Támogatott App Service csomagok:|![Igen, ](./media/icons/yes-icon.png) alapszintű, standard, prémium, elkülönített vagy linuxos<br>![Nincs ](./media/icons/no-icon.png) ingyenes, közös vagy felhasználási<br>[További információ a App Service csomagokról](https://azure.microsoft.com/pricing/details/app-service/plans/)|
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![No](./media/icons/no-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov)|
|||

## <a name="what-does-azure-defender-for-app-service-protect"></a>Mit jelent az Azure Defender App Service védelem?

Ha a App Servicei terv engedélyezve van, Security Center értékeli a App Service-csomag által érintett erőforrásokat, és a megállapításai alapján biztonsági javaslatokat hoz létre. Security Center védi a virtuális gép azon példányát, amelyben a App Service fut, és a felügyeleti felületet. Emellett figyeli a App Service-ban futó alkalmazásaitól érkező és onnan érkező kérelmeket és válaszokat.

Ha Windows-alapú App Service csomagot futtat, Security Center a mögöttes munkaterületeket és virtuális gépeket is elérheti. A fent említett naplózási adatokkal együtt az infrastruktúra megtudhatja a történetet egy olyan új támadástól, amely a vadonban kering, és megsérül az ügyfél-gépeken. Ezért még akkor is, ha a webalkalmazás kihasználása után Security Center van telepítve, lehetséges, hogy észlelni tudja a folyamatos támadásokat.


## <a name="protect-your-azure-app-service-web-apps-and-apis"></a>Azure App Service-webalkalmazások és -API-k védelme
A Azure App Service-csomag a App Service Azure Defenderrel való ellátásához:

- Győződjön meg arról, hogy rendelkezik olyan támogatott App Service-csomaggal, amely dedikált gépekhez van társítva. A fent felsorolt támogatott csomagok a [rendelkezésre állás](#availability)alatt találhatók.

- Engedélyezze az **Azure Defendert** az előfizetésében (a app Service csomaghoz csak az **Azure Defendert** engedélyezheti) a [Azure Security Center díjszabása](security-center-pricing.md) szerint

A Security Center natív módon van integrálva a App Serviceval, így nincs szükség az üzembe helyezésre és a bevezetésre – az integráció átlátható.

>[!NOTE]
> A díjszabás és beállítások lap felsorolja az adott **erőforrás mennyiségére**vonatkozó példányok számát. Ez a számítási példányok teljes számát jelöli az előfizetéshez tartozó összes App Service csomag esetében, amely akkor fut, amikor megnyitotta a díjszabási szintet tartalmazó lapot.
>
> Azure App Service számos különböző csomagot kínál. A App Service terv meghatározza a futtatandó webalkalmazás számítási erőforrásainak készletét. Ezek egyenértékűek a hagyományos webes üzemeltetésű kiszolgáló-farmokkal. Egy vagy több alkalmazás úgy konfigurálható, hogy ugyanazon a számítási erőforrásokon (vagy ugyanabban a App Service tervben) fusson.
>
>A darabszám ellenőrzéséhez lépjen az Azure Portalon a "App Service csomagok" elemre, ahol megtekintheti az egyes csomagokban használt számítási példányok számát. 



## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan App Service Azure Defendert. 

A kapcsolódó anyagokkal kapcsolatban tekintse meg a következő cikkeket: 

- Azt határozza meg, hogy a riasztást a Security Center hozza-e létre, vagy egy másik biztonsági terméktől Security Center fogadta-e, exportálhatja. Ha a riasztásokat az Azure Sentinelbe, a harmadik féltől származó SIEM-re vagy más külső eszközre szeretné exportálni, kövesse a [riasztások a Siem](continuous-export.md)-ben való exportálásának utasításait.
- A Azure App Service riasztások listáját a [riasztások hivatkozási táblájában](alerts-reference.md#alerts-azureappserv)tekintheti meg.
- App Service csomagokkal kapcsolatos további információkért lásd: [app Service csomagok](https://azure.microsoft.com/pricing/details/app-service/plans/).
- > [!div class="nextstepaction"]
    > [Az Azure Defender engedélyezése](security-center-pricing.md)