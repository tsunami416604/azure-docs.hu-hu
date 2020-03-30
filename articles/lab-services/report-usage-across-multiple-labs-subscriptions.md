---
title: Az Azure DevTest Labs használata több laborban és előfizetésben
description: Ismerje meg, hogyan jelentheti az Azure DevTest Labs használatát több laborban és előfizetésben.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: takamath
ms.openlocfilehash: 912f510f6380c0ba1eb92b7c485091801123558e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169187"
---
# <a name="report-azure-devtest-labs-usage-across-multiple-labs-and-subscriptions"></a>Az Azure DevTest Labs használatának jelentése több laborban és előfizetésben

A legtöbb nagy szervezet szeretné nyomon követni az erőforrás-használat, hogy hatékonyabb legyen ezekkel az erőforrásokkal a trendek megjelenítése és kiugró a használatban. Az erőforrás-használat alapján a labortulajdonosok vagy -kezelők testre szabhatják a laborokat az [erőforrás-használat és a költségek javítása érdekében.](https://docs.microsoft.com/azure/billing/billing-getting-started) Az Azure DevTest Labs, letöltheti erőforrás-használat laboronként, amely lehetővé teszi a mélyebb történelmi pillantást a használati mintákat. Ezek a használati minták segíthetnek a hatékonyság növelése érdekében a módosítások azonosításában. A legtöbb vállalat egyéni laboratóriumi használatot és általános használatot szeretne [több laborban és előfizetésben.](https://docs.microsoft.com/azure/architecture/cloud-adoption/decision-guides/subscriptions/) 

Ez a cikk ismerteti, hogyan kezelje az erőforrás-használati adatok több laborok és előfizetések között.

![Használat jelentése](./media/report-usage-across-multiple-labs-subscriptions/report-usage.png)

## <a name="individual-lab-usage"></a>Egyéni laborhasználat

Ez a szakasz bemutatja, hogyan exportálhatja az erőforrás-használat egyetlen tesztkörnyezetben.

A DevTest Labs erőforrás-használatának exportálása előtt be kell állítania egy Azure Storage-fiókot, amely lehetővé teszi a használati adatokat tartalmazó különböző fájlok tárolását. Az adatok exportálásának két gyakori módja van:

* [DevTest Labs REST API](https://docs.microsoft.com/rest/api/dtl/labs/exportresourceusage) 
* A PowerShell Az.Resource modul [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction?view=azps-2.5.0&viewFallbackFrom=azps-2.3.2) a `exportResourceUsage`művelet, a labor erőforrás-azonosító, és a szükséges paramétereket. 

    A [személyes adatok exportálása vagy törlése](personal-data-delete-export.md) cikk tartalmaz egy minta PowerShell-parancsfájl részletes információkat az exportált adatokat tartalmaz. 

    > [!NOTE]
    > A dátumparaméter nem tartalmaz időbélyegzőt, így az adatok tartalmaz mindent éjféltől az időzóna, ahol a labor található.

Miután az exportálás befejeződött, több CSV-fájl lesz a blob storage-ban a különböző erőforrásadatokkal.
  
Jelenleg két CSV fájlokat:

* *virtualmachines.csv* - információkat tartalmaz a virtuális gépek a laborban
* *disks.csv* - információkat tartalmaz a különböző lemezek a laborban 

Ezek a fájlok a *labresourceusage* blob tárolóban tárolódnak a labor neve alatt, a Lab egyedi azonosítója, a végrehajtás dátuma, és teljes vagy az exportálási kérelemben alapuló kezdő dátum alatt. Egy példa blob struktúra lenne:

* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD26-MM6-2019YYYY/full/virtualmachines.csv`
* `labresourceusage/labname/1111aaaa-bbbb-cccc-dddd-2222eeee/<End>DD-MM-YYYY/26-6-2019/20-6-2019<Start>DD-MM-YYYY/virtualmachines.csv`

## <a name="exporting-usage-for-all-labs"></a>Használat exportálása az összes laboratóriumba

Több labor használati adatainak exportálásához fontolja meg a 

* [Az Azure Functions](https://docs.microsoft.com/azure/azure-functions/)számos nyelven elérhető, például a PowerShellben, vagy 
* [Az Azure Automation runbook](https://docs.microsoft.com/azure/automation/), használja a PowerShell, Python, vagy egy egyéni grafikus tervező az exportkód írásához.

Ezekkel a technológiákkal az egyes laborok exportálása az összes laborok egy adott napon és időpontban. 

Az Azure-függvény nek le kell adnia az adatokat a hosszabb távú tárolásra. Több laboratórium adatainak exportálásakor az exportálás eltarthat egy ideig. A teljesítmény csökkentése és az információk megkettőzésének csökkentése érdekében javasoljuk, hogy minden egyes tesztkörnyezetet párhuzamosan hajtson végre. A párhuzamosság érdekében futtassa aszinkron módon az Azure Functions szolgáltatást. Is kihasználják az azure Functions által kínált időzítő eseményindító.

## <a name="using-a-long-term-storage"></a>Hosszú távú tárolás használata

A hosszú távú tárolás egyetlen adatforrásba egyesíti a különböző laborokból származó exportadatokat. A hosszú távú tárolás használatának másik előnye, hogy a párhuzamosságok és a költségek csökkentése érdekében el tudja távolítani a fájlokat a tárfiókból. 

A hosszú távú tárolás bármilyen szövegmanipulációhoz használható, például: 

* hozzáadás, rövid nevek
* létrehozás, összetett csoportok
* az adatok összesítése.

Néhány gyakori tárolási megoldás: [SQL Server](https://azure.microsoft.com/services/sql-database/), Azure [Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/)és [Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). A választott hosszú távú tárolási megoldás kiválasztása a preferenciától függ. Az adatok megjelenítésekor érdemes lehet az eszközt kiválasztani attól függően, hogy mit kínál az interakció kontitúdulása szempontjából.

## <a name="visualizing-data-and-gathering-insights"></a>Adatok megjelenítése és elemzési adatok gyűjtése

A választott adatvizualizációs eszközzel csatlakozhat a hosszú távú tárhelyhez a használati adatok megjelenítéséhez és a használat hatékonyságának ellenőrzéséhez. A [Power BI](https://docs.microsoft.com/power-bi/power-bi-overview) például a használati adatok rendszerezésére és megjelenítésére használható. 

Az [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) segítségével egyetlen helyillesztőn keresztül hozhatja létre, csatolhatja és kezelheti az erőforrásokat. Ha nagyobb vezérlésre van szükség, az egyes erőforrások egyetlen erőforráscsoporton belül hozhatók létre, és a Data Factory szolgáltatástól függetlenül kezelhetők.  

## <a name="next-steps"></a>Következő lépések

Miután a rendszer be van állítva, és az adatok a hosszú távú tárolóba kerülnek, a következő lépés az, hogy előálljanak azokkal a kérdésekkel, amelyeket az adatoknak meg kell válaszolniuk. Példa: 

-   Mi a virtuális gép méretének használata?

    A felhasználók nagy teljesítményű (drágább) virtuális gépméreteket választanak?
-   Mely Marketplace-képek et használja?

    Az egyéni képek a leggyakoribb virtuális gép bázis, ha egy közös képáruház épül, mint [a megosztott képtár](../virtual-machines/windows/shared-image-galleries.md) vagy [képgyár](image-factory-create.md).
-   Mely egyéni képeket használja a rendszer, vagy nem használja?
