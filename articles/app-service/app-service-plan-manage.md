---
title: Az App Service-csomag kezelése
description: Ismerje meg, hogyan hajthat végre különböző feladatokat egy App Service-csomag kezeléséhez, például létrehozás, áthelyezés, méretezés és törlés.
keywords: app szolgáltatás, azure app service, méretezés, app service terv, változás, létrehozás, kezelés, kezelés
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: 68cabbd00dd0b738590109cc39d8df82f5b7362d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280754"
---
# <a name="manage-an-app-service-plan-in-azure"></a>App Szolgáltatási csomag kezelése az Azure-ban

Az [Azure App Service-csomag](overview-hosting-plans.md) biztosítja az erőforrásokat, amelyek egy App Service-alkalmazás futtatásához szükséges. Ez az útmutató bemutatja, hogyan kezelheti az App Service-csomagot.

## <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

> [!TIP]
> Ha rendelkezik Egy App Service-környezettel, olvassa [el az App Service-csomag létrehozása App Service-környezetben című témakört.](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)

Létrehozhat egy üres App Service-csomagot, vagy létrehozhat egy tervet az alkalmazás létrehozása részeként.

1. Az [Azure Portalon](https://portal.azure.com)válassza az **Erőforrás létrehozása**lehetőséget.

   ![Hozzon létre egy erőforrást az Azure Portalon.][createResource] 

1. Válassza az **Új** > **webalkalmazás** vagy más típusú App Service-alkalmazás lehetőséget.

   ![Hozzon létre egy alkalmazást az Azure Portalon.][createWebApp] 

2. Konfigurálja a **példány részletei** szakaszt az App Service-csomag konfigurálása előtt. Az olyan beállítások, mint **a Közzététel** és az **operációs rendszerek** módosíthatják az App Service-csomag elérhető tarifacsomagjait. **A régió** határozza meg, hogy hol jön létre az App Service-csomag. 
   
3. Az **App Service-terv** csoportban jelöljön ki egy meglévő tervet, vagy hozzon létre egy tervet az **Új létrehozása**lehetőség kiválasztásával.

   ![Hozzon létre egy App Service-csomagot.][createASP] 

4. A terv létrehozásakor kiválaszthatja az új csomag tarifacsomagját. A **Sku és a méret,** válassza **a Méret módosítása** a tarifacsomag módosításához. 

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Alkalmazás áthelyezése másik App Service-csomagba

Az alkalmazást áthelyezheti egy másik App Service-csomagba, feltéve, hogy a forrásterv és a célterv ugyanabban az _erőforráscsoportban és földrajzi régióban_van.

> [!NOTE]
> Az Azure minden új App Service-csomagot üzembe helyez egy központi telepítési egységbe, amelyet belsőleg webtárhelynek neveznek. Minden régióban számos webtárhely lehet, de az alkalmazás csak az ugyanabban a tárhelyben létrehozott tervek között válthat. Az App Service-környezet egy elkülönített tárhely, így az alkalmazások áthelyezhetők az ugyanabban az App Service-környezetben lévő csomagok között, de a különböző App Service-környezetekben lévő csomagok között nem.
>
> Terv létrehozásakor nem adhatja meg a kívánt tárhelyet, de biztosítható, hogy a terv ugyanabban a tárhelyben legyen létrehozva, mint egy meglévő terv. Röviden, az azonos erőforráscsoport- és régiókombinációval létrehozott összes terv ugyanabban a webtérben van telepítve. Ha például létrehozott egy tervet az A és a B régió erőforráscsoportban, akkor az A és B régióban később létrehozott tervek ugyanabba a tárhelybe kerülnek. Vegye figyelembe, hogy a tervek nem helyezhetők át a webterek létrehozása után, így nem helyezhet át egy tervet "ugyanabba a tárhelybe", mint egy másik terv, ha áthelyezi egy másik erőforráscsoportba.
> 

1. Az [Azure Portalon](https://portal.azure.com)keresse meg és válassza ki az **alkalmazásszolgáltatásokat,** és válassza ki az áthelyezni kívánt alkalmazást.

2. A bal oldali menüben válassza az **App Service-csomag módosítása**lehetőséget.

3. Az **App Service-csomag** legördülő menüben válasszon ki egy meglévő csomagot, amelybe át szeretné helyezni az alkalmazást. A legördülő menü csak azokat a terveket jeleníti meg, amelyek ugyanabban az erőforráscsoportban és földrajzi régióban találhatók, mint az aktuális App Service-csomag. Ha ilyen terv nem létezik, akkor alapértelmezés szerint létrehozhat egy tervet. Új tervet manuálisan is létrehozhat, ha az Új létrehozása lehetőséget **választja.**

4. Ha létrehoz egy tervet, kiválaszthatja az új csomag tarifacsomagját. A **Tarifacsomagban**válassza ki a meglévő réteget a módosításához. 
   
   > [!IMPORTANT]
   > Ha egy alkalmazást magasabb szintű csomagról alacsonyabb szintű csomagra helyez át, például **D1-ről** **F1-re,** az alkalmazás elveszítheti a célterv bizonyos képességeit. Ha például az alkalmazás SSL-tanúsítványokat használ, a következő hibaüzenet jelenhet meg:
   >
   > `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

5. Ha végzett, válassza **az OK gombot.**
   
   ![App Service-csomag választó.][change] 

## <a name="move-an-app-to-a-different-region"></a>Alkalmazás áthelyezése másik régióba

Az a régió, amelyben az alkalmazás fut, az Az App Service-csomag régiója, amelyben található. Az App Service-csomag régiója azonban nem módosítható. Ha az alkalmazást egy másik régióban szeretné futtatni, az egyik alternatíva az alkalmazásklónozás. A klónozás az alkalmazás egy új vagy meglévő App Service-csomagban készít másolatot bármely régióban.

A **Clone App** alkalmazást a menü **Fejlesztői eszközök** szakaszában találja.

> [!IMPORTANT]
> A klónozásnak vannak korlátai. Ezekről az [Azure App App App App-klónozás](app-service-web-app-cloning.md)ban olvashat.

## <a name="scale-an-app-service-plan"></a>App Szolgáltatási csomag méretezése

Az App Service-csomag díjszabási szintjének skálázása az [Azure-beli alkalmazások felskálázása.](manage-scale-up.md)

Az alkalmazások példányszámának horizontális felskálázásához olvassa el a [Példányszám manuális vagy automatikus méretezése témakört.](../monitoring-and-diagnostics/insights-how-to-scale.md)

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>App Szolgáltatási csomag törlése

A váratlan díjak elkerülése érdekében, amikor törli az utolsó alkalmazást egy App Service-csomagban, az App Service alapértelmezés szerint törli a csomagot is. Ha úgy dönt, hogy megtartja a csomagot, akkor módosítsa a csomagot **ingyenes** szintre, hogy ne számítson fel díjat.

> [!IMPORTANT]
> Az App Service-csomagok, amelyek nem rendelkeznek alkalmazásokkal társítva továbbra is díjat, mert továbbra is fenntartja a konfigurált virtuálisgép-példányok továbbra is.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazás vertikális felméretezése az Azure-ban](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[createResource]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-a-resource.png
