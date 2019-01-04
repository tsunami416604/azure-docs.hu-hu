---
title: Vertikális felskálázás funkciók és - kapacitások és az Azure App Service |} A Microsoft Docs
description: Útmutató az Azure App Service kapacitás és funkciókat adhat hozzá alkalmazás vertikális felskálázása.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 98d3d1f6fc0f2f30196f360811808579dfbab312
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53727479"
---
# <a name="scale-up-an-app-in-azure"></a>Az Azure-beli alkalmazás vertikális felskálázása

> [!NOTE]
> Az új **PremiumV2** szint nyújt gyorsabb processzorokat, SSD-tárolóval, és a memória-mag arány, a meglévő tarifacsomagok megduplázódik. A teljesítmény előnye az pénzt sikerült menteni az kevesebb példányt az alkalmazások futtatásával. Méretezését akár **PremiumV2** réteg, lásd: [az App Service konfigurálása PremiumV2 szintjének](app-service-configure-premium-tier.md).
>

Ez a cikk bemutatja, hogyan skálázhatja őket az Azure App Service-ben. Két munkafolyamatok méretezési, méretezhető felfelé és horizontális felskálázás, és ez a cikk ismerteti a vertikális felskálázásos munkafolyamat.

* [Vertikális felskálázás](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Több Processzor, memória, lemezterület és extra funkciók, például dedikált virtuális gépeken (VM), az egyéni tartományok és a tanúsítványok, előkészítési pontok, automatikus skálázást és egyéb beolvasása. A vertikális felskálázáshoz módosítsa az alkalmazáshoz tartozó App Service-csomag.
* [Horizontális felskálázás](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Az alkalmazást futtató Virtuálisgép-példányok számának növelése.
  Horizontálisan akár 20 példány, hogy a tarifacsomagtól függően. [App Service Environment-környezetek](environment/intro.md) a **elkülönített** szint tovább növeli a horizontális felskálázás count 100 példány. További információ a horizontális felskálázás: [példányszám manuális vagy automatikus méretezése](../monitoring-and-diagnostics/insights-how-to-scale.md). Itt megtudhatja, hogyan az automatikus skálázást, amely a példányok száma automatikusan alapján előre meghatározott szabályok és ütemezések méretezését.

A méretezési csoport beállításai csak érvényesek, és hatással vannak az összes alkalmazása másodpercig is eltarthat a [App Service-csomag](../app-service/overview-hosting-plans.md).
Nem kívánnak, hogy módosítania kell a kódot, vagy az alkalmazás újbóli üzembe helyezése.

A díjszabást és az egyes App Service-csomagokkal kapcsolatos további információkért lásd: [App Service díjszabása](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Az App Service-csomagot, mielőtt a **ingyenes** szint, el kell távolítania a [költségkorlátozási](https://azure.microsoft.com/pricing/spending-limits/) helyen az Azure-előfizetéséhez. Beállításainak megtekintése vagy módosítása a Microsoft Azure App Service-előfizetéséhez tartozó, lásd: [a Microsoft Azure-előfizetések][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Vertikális felskálázás tarifacsomag
1. A böngészőben nyissa meg a [az Azure portal][portal].
2. Az App Service alkalmazás lapján a kattintson **minden beállítás**, és kattintson a **vertikális Felskálázás**.
   
    ![Keresse meg a vertikális felskálázása az Azure-alkalmazáshoz.][ChooseWHP]
3. Válassza ki a szintet, és kattintson **alkalmaz**.
   
    A **értesítések** lap egy zöld felvillan **sikeres** a művelet befejezése után.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Kapcsolódó erőforrások méretezése
Ha az alkalmazása függ más szolgáltatások, például az Azure SQL Database vagy az Azure Storage, skálázhatja fel ezeket az erőforrásokat külön-külön. Ezeket az erőforrásokat az App Service-csomag nem felügyel.

1. A **Essentials**, kattintson a **erőforráscsoport** hivatkozásra.
   
    ![Vertikális felskálázás az Azure-alkalmazáshoz kapcsolódó erőforrások](./media/web-sites-scale/RGEssentialsLink.png)
2. Az a **összefoglalás** része a **erőforráscsoport** kattintson egy erőforrás, amely méretezésére. Az alábbi képernyőfelvételen egy SQL-adatbázis-erőforrás és a egy Azure Storage-erőforrás.
   
    ![Keresse meg az erőforráscsoport lapján vertikális felskálázása az Azure-alkalmazáshoz](./media/web-sites-scale/ResourceGroup.png)
3. Kattintson egy SQL-adatbázis-erőforrás **beállítások** > **tarifacsomag** méretezése a tarifacsomagot.
   
    ![Vertikális felskálázás az Azure-alkalmazás az SQL Database háttéralkalmazásának](./media/web-sites-scale/ScaleDatabase.png)
   
    Akkor is bekapcsolhatja [georeplikációs](../sql-database/sql-database-geo-replication-overview.md) az SQL Database-példány számára.
   
    Egy Azure Storage-erőforrások esetében kattintson a **beállítások** > **konfigurációs** vertikális felskálázása a tárolási lehetőségeket.
   
    ![Vertikális felskálázás az Azure-alkalmazás által használt Azure Storage-fiók](./media/web-sites-scale/ScaleStorage.png)

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Árképzési szintek összehasonlítása
Részletes információk, például a virtuális gépek méretei minden egyes tarifacsomagja: [App Service díjszabása](https://azure.microsoft.com/pricing/details/web-sites/).
Szolgáltatási korlátok, kvóták, és a korlátozások és támogatott szolgáltatások, az egyes szintek táblázatát, lásd: [az App Service korlátai](../azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="next-steps"></a>További lépések
* Díjszabás, támogatás és SLA-t kapcsolatos további információért látogasson el az alábbi hivatkozásokat:
  
    [Adatforgalmi díjszabás](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [A Microsoft Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)
  
    [Szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/)
  
    [Az SQL Database szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Virtuális gép és Felhőszolgáltatás-méretek esetében a Microsoft Azure][vmsizes]
  
* További információ az Azure App Service-ben ajánlott eljárások létrehozása egy méretezhető és rugalmas architektúra, beleértve: [ajánlott eljárásokat: Az Azure App Service Web Apps](https://azure.microsoft.com/blog/best-practices-windows-azure-websites-waws/).
* Videók a méretezés App Service-alkalmazások a következő forrásanyagokban talál:
  
  * [Mikor érdemes méretezni az Azure Websites – a Lengyel Schackow](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)
  * [Az automatikus skálázás az Azure Websites, a CPU - lengyel Schackow az ütemezett vagy](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)
  * [Az Azure Websites méretezés – lengyel Schackow](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)

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
