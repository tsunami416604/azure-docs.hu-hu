---
title: A funkciók és a kapacitások bővítése
description: Ismerje meg, hogyan skálázhat egy alkalmazást az Azure App Service-ben. Szerezzen több processzort, memóriát, lemezterületet és további funkciókat.
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.topic: article
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: dfd9297e80836978b8a185df3fc4659676383802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75659899"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Alkalmazás felskálázása az Azure App Service-ben

Ez a cikk bemutatja, hogyan skálázhatja az alkalmazást az Azure App Service-ben. Két munkafolyamat létezik a méretezéshez, a méretezéshez és a horizontális felskálázáshoz, és ez a cikk ismerteti a felskálázási munkafolyamatot.

* [Felskálázás:](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)Több processzor, memória, lemezterület és további funkciók, például dedikált virtuális gépek (VM-ek), egyéni tartományok és tanúsítványok, átmeneti helyek, automatikus skálázás stb. Az alkalmazáshoz tartozó App Service-csomag tarifacsomagjának módosításával skálázhatja.
* [Horizontális felskálázás:](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling)Növelje az alkalmazást futtató virtuálisgép-példányok számát.
  A tarifacsomagtól függően akár 30 példányra is skálázható. [Az App Service-környezetek](environment/intro.md) **az elkülönített** rétegben tovább növeli a horizontális felskálázási szám 100 példányra. A horizontális felskálázásról a [Példányszám manuális vagy automatikus méretezése című témakörben](../monitoring-and-diagnostics/insights-how-to-scale.md)talál további információt. Itt megtudhatja, hogyan használhatja az automatikus skálázást, amely a példányok számának automatikus méretezése előre definiált szabályok és ütemezések alapján.

A méretezési beállítások alkalmazása csak másodpercek alatt történik, és az [Alkalmazásszolgáltatási csomag](../app-service/overview-hosting-plans.md)összes alkalmazására hatással van.
Nem követelik meg, hogy módosítsa a kódot, vagy újratelepítse az alkalmazást.

Az egyes App Service-csomagok díjszabásáról és funkcióiról az [App Service díjszabásának részletei című témakörben talál további](https://azure.microsoft.com/pricing/details/web-sites/)információt.  

> [!NOTE]
> Mielőtt átváltana egy App Service-csomagra az **ingyenes** csomagról, először el kell távolítania az Azure-előfizetéséhez érvényben lévő [költségkereteket.](https://azure.microsoft.com/pricing/spending-limits/) A Microsoft Azure App Service-előfizetés beállításainak megtekintéséhez vagy módosításához olvassa el a [Microsoft Azure-előfizetések][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>A tarifacsomag felskálázása

> [!NOTE]
> A **PremiumV2-szintre** való felskálázásról a [PremiumV2 szint konfigurálása az App Service szolgáltatáshoz](app-service-configure-premium-tier.md)című témakörben látható.
>

1. Nyissa meg az [Azure Portalt][portal] a böngészőjében.

1. Az App Service-alkalmazás lapján a bal oldali menüben válassza a **Felskálázás (App Service-csomag)** lehetőséget.
   
3. Válassza ki a réteget, majd válassza **az Alkalmaz lehetőséget.** Válassza ki a különböző kategóriákat (például **Termelés)** és **tekintse meg a további lehetőségeket** a további szintek megjelenítéséhez.
   
    ![Navigálással felskálázhatja azure-alkalmazását.][ChooseWHP]

    Amikor a művelet befejeződött, megjelenik egy értesítési előugró ablak zöld sikerességi pipával.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Kapcsolódó erőforrások méretezése
Ha az alkalmazás más szolgáltatásoktól függ, például az Azure SQL Database-től vagy az Azure Storage-tól, külön-külön is felskálázhatja ezeket az erőforrásokat. Ezeket az erőforrásokat nem az App Service-csomag kezeli.

1. Az **alkalmazás Áttekintés lapján** válassza az **Erőforráscsoport** hivatkozást.
   
    ![Az Azure-alkalmazás kapcsolódó erőforrásainak nagyméretezése](./media/web-sites-scale/RGEssentialsLink.png)

2. Az **Erőforráscsoport** **lap Összegzés** részében válassza ki a méretezni kívánt erőforrást. A következő képernyőképen egy SQL Database-erőforrás látható.
   
    ![Navigálás az erőforráscsoport-lapra az Azure-alkalmazás méretezéséhez](./media/web-sites-scale/ResourceGroup.png)

    A kapcsolódó erőforrás méretezéséhez tekintse meg az adott erőforrástípus dokumentációját. Ha például egyetlen SQL-adatbázist szeretne felskálázni, olvassa el [az Egyetlen adatbázis-erőforrások méretezése az Azure SQL Database-ben című témakört.](../sql-database/sql-database-single-database-scale.md) A MySQL-erőforrások Azure-adatbázisának méretezéséhez olvassa el [a MySQL-erőforrások méretezése című témakört.](../mysql/concepts-pricing-tiers.md#scale-resources)

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Tarifacsomagok összehasonlítása

Részletes információkért, például az egyes tarifacsomagok virtuálisgép-méreteiről az [App Service díjszabási részletei című témakörben talál](https://azure.microsoft.com/pricing/details/app-service)részletes információkat.

A szolgáltatáskorlátokat, kvótákat és korlátozásokat, valamint az egyes rétegek támogatott szolgáltatásait az [App Service-korlátok táblázatában](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)található.

<a name="Next Steps"></a>

## <a name="more-resources"></a>További erőforrások

[Példányszám manuális vagy automatikus méretezése](../monitoring-and-diagnostics/insights-how-to-scale.md)  
[PremiumV2 szint konfigurálása az App Service szolgáltatáshoz](app-service-configure-premium-tier.md)

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
