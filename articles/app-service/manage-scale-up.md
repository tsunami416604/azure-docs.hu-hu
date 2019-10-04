---
title: Szolgáltatások és kapacitások vertikális felskálázása – Azure App Service | Microsoft Docs
description: Megtudhatja, hogyan méretezheti fel Azure App Service alkalmazásait a kapacitás és a szolgáltatások hozzáadásához.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/19/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: d2c3b09277963781b90f65705e03f936f81b14ee
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232358"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Alkalmazás vertikális felskálázása Azure App Service

Ez a cikk bemutatja, hogyan méretezheti az alkalmazást Azure App Serviceban. Két munkafolyamat áll rendelkezésre a skálázáshoz, a vertikális felskálázáshoz és a horizontális felskálázáshoz, és ez a cikk a vertikális Felskálázási munkafolyamatot ismerteti.

* [](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)Vertikális felskálázás: Nagyobb CPU-, memória-, lemezterület-és extra funkciók, például dedikált virtuális gépek (VM-EK), egyéni tartományok és tanúsítványok, átmeneti tárolóhelyek, automatikus skálázás és egyéb szolgáltatások. A vertikális felskálázáshoz módosítsa az alkalmazáshoz tartozó App Service terv díjszabási szintjét.
* [](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)Vertikális felskálázás: Növelje az alkalmazást futtató virtuálisgép-példányok számát.
  A díjszabási szintjétől függően akár 20 példányra is kibővíthető. Az **elszigetelt** szinten [app Service környezetek](environment/intro.md) tovább növelik a kibővíthető darabszámot 100 példányra. További információ a horizontális felskálázásról: [Példányszám manuális vagy automatikus skálázása](../monitoring-and-diagnostics/insights-how-to-scale.md). Itt megtudhatja, hogyan használhatja az automatikus skálázást, amely az előre meghatározott szabályok és ütemtervek alapján automatikusan méretezi a példányszámot.

A méretezési beállítások csak másodperceket vesznek igénybe, és hatással lesznek a [app Service-csomag](../app-service/overview-hosting-plans.md)összes alkalmazására.
Nem igénylik a kód módosítását vagy az alkalmazás újbóli üzembe helyezését.

Az egyes App Service csomagok díjszabásával és funkcióival kapcsolatos információkért tekintse meg a [app Service díjszabási részleteit](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Mielőtt átvált egy App Service csomagot az **ingyenes** csomagból, először el kell távolítania az Azure-előfizetéshez tartozó költségkeret- [korlátokat](https://azure.microsoft.com/pricing/spending-limits/) . A Microsoft Azure App Service-előfizetés beállításainak megtekintéséhez vagy módosításához lásd: [Microsoft Azure][azuresubscriptions]előfizetések.
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Az árképzési szintek vertikális felskálázása

> [!NOTE]
> A **PremiumV2** szintjére való vertikális felskálázáshoz lásd: [PremiumV2-szintek konfigurálása app Servicehoz](app-service-configure-premium-tier.md).
>

1. Nyissa meg az [Azure Portalt][portal] a böngészőjében.

1. A App Service alkalmazás lapjának bal oldali menüjében válassza a vertikális **felskálázás (App Service terv)** lehetőséget.
   
3. Válassza ki a szintet, majd válassza az **alkalmaz**lehetőséget. Válassza ki a különböző kategóriákat (például **éles**környezetben), és **további lehetőségeket is láthat** a további szintek megjelenítéséhez.
   
    ![Navigáljon az Azure-alkalmazás vertikális felskálázásához.][ChooseWHP]

    Ha a művelet befejeződött, egy értesítés jelenik meg, amely zöld sikerrel jelöli meg.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Kapcsolódó erőforrások méretezése
Ha az alkalmazás más szolgáltatásokból (például Azure SQL Database vagy Azure Storage) függ, akkor ezeket az erőforrásokat külön is felhasználhatja. Ezeket az erőforrásokat nem a App Service-csomag kezeli.

1. Az alkalmazás **Áttekintés** lapján válassza ki az **erőforráscsoport** hivatkozást.
   
    ![Az Azure-alkalmazás kapcsolódó erőforrásainak vertikális felskálázása](./media/web-sites-scale/RGEssentialsLink.png)

2. Az **erőforráscsoport** lap **Összefoglalás** részében válassza ki a méretezni kívánt erőforrást. Az alábbi képernyőfelvételen egy SQL Database erőforrás látható.
   
    ![Az Azure-alkalmazás vertikális felskálázásához navigáljon az erőforráscsoport oldalra](./media/web-sites-scale/ResourceGroup.png)

    A kapcsolódó erőforrás vertikális felskálázásához tekintse meg az adott erőforrástípus dokumentációját. Egy SQL Database vertikális felskálázásához például lásd: [önálló adatbázis-erőforrások méretezése Azure SQL Databaseban](../sql-database/sql-database-single-database-scale.md). Azure Database for MySQL erőforrások vertikális felskálázását lásd: [MySQL-erőforrások skálázása](../mysql/concepts-pricing-tiers.md#scale-resources).

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Árképzési szintek összehasonlítása

A részletes információkat, például a virtuálisgép-méreteket az egyes díjszabási szinteknél tekintse meg a [app Service díjszabását](https://azure.microsoft.com/pricing/details/app-service).

A szolgáltatási korlátok, kvóták és megkötések, valamint az egyes rétegek támogatott szolgáltatásai esetében lásd: [app Service korlátok](../azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="more-resources"></a>További források

[Példányszám manuális vagy automatikus méretezése](../monitoring-and-diagnostics/insights-how-to-scale.md)  
[App Service PremiumV2-szintjeinek konfigurálása](app-service-configure-premium-tier.md)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
