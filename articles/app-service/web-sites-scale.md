---
title: "Vertikális felskálázás egy alkalmazást az Azure-ban |} Microsoft Docs"
description: "Megtudhatja, hogyan vertikális felskálázás az Azure App Service kapacitás és szolgáltatások hozzáadása egy alkalmazáshoz."
services: app-service
documentationcenter: 
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
ms.openlocfilehash: f6244e3f739424be169f1ea117500159bd5e4254
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2018
---
# <a name="scale-up-an-app-in-azure"></a>Vertikális felskálázás egy alkalmazást az Azure-ban

> [!NOTE]
> Az új **PremiumV2** réteg gyorsabb CPU, SSD-tárolón, lehetővé teszi, valamint a meglévő árképzési szinteket a memória-core aránya megduplázódik. A teljesítmény előnyösebb kevesebb példányt az alkalmazások futtatásával lehetett költségtakarékosabb munkavégzésben. Méretezési legfeljebb **PremiumV2** réteg című [az App Service konfigurálása PremiumV2 réteg](app-service-configure-premium-tier.md).
>

Ez a cikk bemutatja, hogyan alkalmazás skálázása az Azure App Service-ben. Két munkafolyamatok méretezési, bővített fel és kibővítési, és ez a cikk azt ismerteti, a munkafolyamat felskálázott.

* [Vertikális felskálázás](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): több Processzor, memória, lemezterület és extra funkciók, például dedikált virtuális gépek (VM), egyedi tartományok és tanúsítványok, előkészítési tárhelyek, automatikus skálázás és több beolvasása. Vertikális felskálázás az alkalmazáshoz tartozó App Service-csomag tarifacsomagját módosításával.
* [Horizontális felskálázás](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): növelje az alkalmazást futtató Virtuálisgép-példányok számát.
  Ki lehet terjeszteni akár 20 példányokhoz, attól függően, hogy a tarifacsomagot. [App Service Environment-környezetek](environment/intro.md) a **elszigetelt** réteg tovább növeli a kibővíthető 100 példányára. Kiterjesztésére vonatkozó további információkért lásd: [méretezése példányszám manuális vagy automatikus](../monitoring-and-diagnostics/insights-how-to-scale.md). Itt megtudhatja, hogyan kell az automatikus skálázást, amely a méretezési példányok száma alapján automatikusan előre meghatározott szabályok és ütemezések.

A skálázási beállításokat másodpercre csak alkalmazásához, és hatással lévő összes alkalmazásra a [App Service-csomag](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
Nem igénylik, hogy módosítania kell a kódot, vagy telepítse újra az alkalmazást.

A díjszabás és az App Service-csomagokról szolgáltatásokkal kapcsolatos további információkért lásd: [App Service díjszabás](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Az App Service-csomagot váltottunk az **szabad** réteg, el kell távolítania a [költségkeretek](https://azure.microsoft.com/pricing/spending-limits/) biztosítani az Azure-előfizetéshez. Beállításainak megtekintése vagy módosítása a Microsoft Azure App Service előfizetési, lásd: [Microsoft Azure-előfizetések][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Vertikális felskálázás tarifacsomag
1. A böngészőben nyissa meg a [Azure-portálon][portal].
2. Az App Service alkalmazás oldalon kattintson **összes beállítás**, és kattintson a **vertikális Felskálázás**.
   
    ![Navigáljon az Azure alkalmazás növelheti.][ChooseWHP]
3. Válassza ki a réteg, és kattintson a **válasszon**.
   
    A **értesítések** lapon villogjon, egy zöld **sikeres** a művelet befejeződése után.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Kapcsolódó erőforrások méretezése
Ha az alkalmazás egyéb szolgáltatások, például az Azure SQL Database vagy az Azure Storage függ külön-külön is növelheti ezeket az erőforrásokat. Ezeket az erőforrásokat az App Service-csomag nem felügyel.

1. A **Essentials**, kattintson a **erőforráscsoport** hivatkozásra.
   
    ![Vertikális felskálázás az Azure alkalmazás kapcsolódó erőforrások](./media/web-sites-scale/RGEssentialsLink.png)
2. Az a **összegzés** része a **erőforráscsoport** lapján kattintson a skálázni kívánt erőforrás. Az alábbi képernyőfelvételen látható egy SQL-adatbázis és egy Azure Storage típusú erőforrást.
   
    ![Navigáljon a erőforrás egy kiszolgálócsoport lapját a vertikális felskálázás az Azure-alkalmazás](./media/web-sites-scale/ResourceGroup.png)
3. Egy SQL-adatbázis erőforrás kattintson **beállítások** > **tarifacsomag** méretezni a tarifacsomag.
   
    ![Vertikális felskálázás az Azure alkalmazás az SQL-adatbázis háttér](./media/web-sites-scale/ScaleDatabase.png)
   
    Is bekapcsolhatja a [georeplikáció](../sql-database/sql-database-geo-replication-overview.md) az SQL Database-példányt.
   
    Egy Azure Storage erőforrás kattintson **beállítások** > **konfigurációs** méretezése a tárolási beállítások konfigurálásával.
   
    ![Vertikális felskálázás az Azure-alkalmazás által használt Azure Storage-fiókban](./media/web-sites-scale/ScaleStorage.png)

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Tarifacsomagok összehasonlítása
Részletes információk, például az egyes tarifacsomagok Virtuálisgép-méretek: [App Service díjszabás](https://azure.microsoft.com/pricing/details/web-sites/).
Az egy táblázatot szolgáltatásra vonatkozó korlátozások, kvóták és korlátozások és támogatott szolgáltatás minden egyes rétegben, [App Service limits](../azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="next-steps"></a>További lépések
* Árképzési, támogatást és szolgáltatásiszint-szerződés kapcsolatos információkért látogasson el a következőket:
  
    [Adatátvitel díjszabása](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [A Microsoft Azure-támogatás ügyfeleknek](https://azure.microsoft.com/support/plans/)
  
    [Szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/)
  
    [SQL adatbázis díjszabása](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Virtuális gépek és Felhőszolgáltatások mérete a Microsoft Azure][vmsizes]
  
* Azure App Service információ gyakorlati tanácsok felépítése méretezhető és rugalmas architektúra, beleértve: [gyakorlati tanácsok: Azure App Service Web Apps](http://blogs.msdn.com/b/windowsazure/archive/2014/02/10/best-practices-windows-azure-websites-waws.aspx).
* App Service apps méretezésével kapcsolatos videók lásd a következőket:
  
  * [Mikor érdemes méretezni az Azure - lengyel Schackow webhelyek](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)
  * [Az automatikus skálázás Azure-webhelyek, CPU vagy ütemezett - rendelkező lengyel Schackow](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)
  * [Az Azure webhelyek méretezéssel -, lengyel Schackow](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:/pricing/details/app-service/
[SQLaccountsbilling]:http://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:http://go.microsoft.com/fwlink/?LinkID=235288
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
