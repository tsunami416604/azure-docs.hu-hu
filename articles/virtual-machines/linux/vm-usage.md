---
title: Az Azure virtuális gép használatának megismerése |} A Microsoft Docs
description: Virtuális gép használatának részletes ismertetése
services: virtual-machines
documentationcenter: ''
author: mmccrory
manager: jeconnoc
editor: ''
tags: azure-virtual-machine
ms.assetid: ''
ms.service: ''
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/04/2017
ms.author: memccror
ms.openlocfilehash: ab56ad07bd3da17f105bd09f2484173207d30015
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2018
ms.locfileid: "44025064"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Az Azure virtuális gép használatának megismerése
Az Azure-használati adatok elemzésével hatékony használati adatokba kaphatunk – insights, ami lehetővé teszi a jobban költségek kezelése és a szervezeten belül foglalási. Ez a dokumentum tartalmaz egy részletes ismertetése az Azure Compute használati adatait. Általános Azure használatáról a további részletekért lépjen [a számla értelmezéséhez](https://docs.microsoft.com/azure/billing/billing-understand-your-bill).

## <a name="download-your-usage-details"></a>A használati adatok letöltése
Első lépésként [a használati adatok letöltése](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-from-the-account-center-csv). Az alábbi táblázat biztosít az Azure Resource Manageren keresztül üzembe helyezett virtuális gépek használati definíció- és példa értékeit. Ez a dokumentum nem tartalmaz részletes információkat a klasszikus modellen keresztül telepített virtuális gépek.


| Mezők             | Jelentés                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Példaértékek                                                                                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Usage Date (Használat dátuma)         | A dátum, amikor az erőforrás lett megadva.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |  “11/23/2017”                                                                                                                                                                                                                                                                                                                                                     |
| Meter ID (Mérési azonosító)           | A legfelső szintű szolgáltatás, amelynek használati adatait, hogy azonosítja.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | "Virtual Machines"                                                                                                                                                                                                                                                                                                                                               |
| Meter Sub-Category (Mérési alkategória) | A kiszámlázott mérő azonosítója. <ul><li>A számítási órák a használatot, nincs a mérőszám az egyes Virtuálisgép-méretet + az operációs rendszer (Windows, a nem-Windows) + régióban.</li><li>Prémium szintű szoftverhasználatra nincs a mérőszám az egyes szoftverek. A legtöbb prémium rendszereit tartalmazó rendszerképek rendelkezik különböző mérőszámok core méreteire vonatkoztatva. További információért látogasson el a [számítási díjszabását ismertető weblapon.](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>                                                                                                                                                                                                                                                                                                                                         | "2005544f-659d-49c9-9094-8e0aea1be3a5"                                                                                                                                                                                                                                                                                                                           |
| Meter Name (Mérés neve)         | Ez az adott, az egyes szolgáltatások az Azure-ban. A számítási kapacitás, mindig "Compute Hours".                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Számítási óraszám"                                                                                                                                                                                                                                                                                                                                                  |
| Meter Region (Mérési régió)       | Az igénybe vett vagy üzemeltető adatközpont elhelyezkedése, ha a szolgáltatás díjszabása az adatközpontok elhelyezkedésétől is függ.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |  "Kelet-japán"                                                                                                                                                                                                                                                                                                                                                       |
| Unit (Egység)               | Igénybe vett szolgáltatás, amely a szolgáltatás. Számítási erőforrások számlázása óránként történik.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Hours"                                                                                                                                                                                                                                                                                                                                                          |
| Consumed (Felhasznált mennyiség)           | Felhasználta az adott napra erőforrás mérete. A számítási számlázunk az adott órában (legfeljebb 6 tizedesjegy pontossággal) futtatta a virtuális gép percenként.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    “1”, “0.5”                                                                                                                                                                                                                                                                                                                                                    |
| Resource Location (Erőforrás helye)  | Az az adatközpont, amelyen az erőforrás fut.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | "Kelet-japán"                                                                                                                                                                                                                                                                                                                                                        |
| Consumed Service (Használt szolgáltatás)   | Az Azure platformszolgáltatás, amely használt.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | "Microsoft.Compute"                                                                                                                                                                                                                                                                                                                                              |
| Erőforráscsoport     | Az az erőforráscsoport, amelyben az üzembe helyezett erőforrás fut. További információkért lásd: [Azure Resource Manager áttekintése.](https://docs.microsoft.com/azure/virtual-machines/linux/vm-usage)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |    "MyRG"                                                                                                                                                                                                                                                                                                                                                        |
| Instance ID (Példányazonosító)        | Az erőforrás azonosítója. Az azonosító tartalmazza az erőforrás számára a létrehozáskor megadott nevet. A virtuális gépek esetében a példány Azonosítóját fogja tartalmazni, a SubscriptionId, ResourceGroupName és VMName (vagy a méretezési csoport skálázási készlet használati neve).                                                                                                                                                                                                                                                                                                                                                                                                                    | "/ előfizetés/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx / resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1"<br><br>vagy<br><br>"/ előfizetés/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx / resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1"                                                                                           |
| Címkék               | Az erőforráshoz hozzárendelt címkét. Használjon címkéket, hogy a számlázási rekordok csoportosítására. Ismerje meg, hogyan [címkézése a virtuális gépek.](tag.md) Ez a tulajdonság erőforrás-kezelő virtuális gép esetében elérhető csak.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | "{"myDepartment":" A távoli asztali","Sajátfelhasználó":"E-mail"}"                                                                                                                                                                                                                                                                                                                        |
| Additional Info (További információ)    | Szolgáltatásspecifikus metaadatok láthatók. A virtuális gépek esetében feltöltünk a mezőben további információkat a következő adatokat: <ul><li>Lemezkép típusa-specifikus kép, amely futtatta. Keresse meg a teljes listát a támogatott karakterláncokat alábbi lemezképek típusai alapján.</li><li>Szolgáltatás típusa: mérete központilag.</li><li>VMName: a virtuális gép nevét. Ez a mező csak akkor van feltöltve, a méretezési csoport virtuális gépeinek. Ha a virtuális gép nevét, a méretezési csoport beállítása virtuális gépekhez, találhatja meg, hogy a fenti Példányazonosító karakterlánc.</li><li>UsageType: Ez határozza meg használati Ez jelöli.<ul><li>ComputeHR a számítási óra felhasználás a mögöttes virtuális gép, például a standard D1 v2.</li><li>ComputeHR_SW a prémium szintű szoftverhasználati díj, ha a virtuális gép által használt premium szoftvereit, például a Microsoft R Server.</li></ul></li></ul>    | Virtuális gépek {"ImageType": "Kanonikus", az "ServiceType": "Standard_DS1_v2", "VMName": "", "UsageType": "ComputeHR"}<br><br>Virtual Machine Scale Sets {"ImageType": "Kanonikus", az "ServiceType": "Standard_DS1_v2", "VMName": "myVM1", "UsageType": "ComputeHR"}<br><br>Prémium szintű szoftvert {"ImageType": "","ServiceType": "Standard_DS1_v2", "VMName": "", "UsageType": "ComputeHR_SW"} |

## <a name="image-type"></a>Lemezkép típusa
Az egyes rendszerképek az Azure katalógusában a kép típusa töltse be a további információ a mező. Ez lehetővé teszi a felhasználók és nyomon követheti, mi azok telepített a virtuális gépen. A következő értékeket, amely fel van töltve, ebben a mezőben a rendszerkép alapján telepített:
  - BitRock 
  - Canonical 
  - FreeBSD 
  - Nyissa meg a logikai 
  - Oracle 
  - SLES for SAP 
  - Az SQL Server 14 Preview-ban a Windows Server 2012 R2 Preview-ban 
  - SUSE
  - SUSE Premium
  - StorSimple Cloud Appliance 
  - Red Hat
  - Red Hat for SAP Business Applications     
  - Red Hat, az SAP Hana-hoz 
  - BYOL Windows ügyfél 
  - A Windows Server BYOL 
  - A Windows Server előzetes verzió 

## <a name="service-type"></a>Szolgáltatás típusa
A szolgáltatás típusú mező a további adatok mezőben felel meg a pontos üzembe helyezett Virtuálisgép-méretet. Prémium szintű tárolós virtuális gépek (SSD-alapú) és a nem prémium szintű tárolós virtuális gépek (HDD-alapú) díjszabása megegyezik. Telepít egy SSD-alapú mérete, mint például a Standard\_DS2\_v2-ben nem SSD méretének megtekintéséhez ("Standard\_D2\_v2 virtuális gép") a mérőszám alkategóriája oszlop és az SSD-méret ("szabványos\_DS2\_ v2 ") a további adatok mezőben.

## <a name="region-names"></a>Régió neve
A régió neve, töltse be a használat részleteiről az erőforrás helyének mezője a régió neve, az Azure Resource Managerben használt változik. Itt látható a régió érték közötti leképezéseket:

|    **Erőforrás-kezelő régió neve**       |    **A használat részleteiről erőforrás helye**    |
|--------------------------|------------------------------------------|
|    australiaeast         |    Ausztrália keleti régiója                               |
|    australiasoutheast    |    Délkelet-Ausztrália                          |
|    brazilsouth           |    Dél-Brazília                              |
|    CanadaCentral         |    Közép-Kanada                            |
|    CanadaEast            |    Kelet-Kanada                               |
|    CentralIndia          |    Közép-India                            |
|    centralus             |    USA középső régiója                            |
|    chinaeast             |    Kelet-Kína                            |
|    chinanorth            |    Észak-Kína                           |
|    eastasia              |    Kelet-Ázsia                             |
|    eastus                |    USA keleti régiója                               |
|    eastus2               |    USA 2. keleti régiója                             |
|    GermanyCentral        |    Közép-Németország                            |
|    GermanyNortheast      |    Északkelet-Németország                          |
|    japaneast             |    Kelet-Japán                               |
|    japanwest             |    Nyugat-Japán                               |
|    KoreaCentral          |    Korea középső régiója                            |
|    KoreaSouth            |    Korea déli régiója                              |
|    northcentralus        |    USA északi középső régiója                      |
|    northeurope           |    Észak-Európa                          |
|    southcentralus        |    USA déli középső régiója                      |
|    southeastasia         |    Délkelet-Ázsia                        |
|    SouthIndia            |    Dél-India                              |
|    UKNorth               |    USA északi régiója                              |
|    uksouth               |    Az Egyesült Királyság déli régiója                              |
|    UKSouth2              |    Egyesült Királyság 2. déli régiója                            |
|    ukwest                |    Az Egyesült Királyság nyugati régiója                               |
|    USDoDCentral          |    US DoD – Középső régió                        |
|    USDoDEast             |    US DoD – Kelet                           |
|    USGovArizona          |    USA-beli államigazgatás – Arizona                         |
|    usgoviowa             |    USGov Iowa                            |
|    USGovTexas            |    USA-beli államigazgatás – Texas                           |
|    usgovvirginia         |    USGov Virginia                        |
|    régiója         |    USA nyugati középső régiója                       |
|    westeurope            |    Nyugat-Európa                           |
|    WestIndia             |    Nyugat-India                               |
|    westus                |    USA nyugati régiója                               |
|    westus2               |    USA 2. nyugati régiója                             |


## <a name="virtual-machine-usage-faq"></a>Virtuálisgép-használat – gyakori kérdések
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Milyen erőforrások számlázzuk, egy virtuális géphez?    
Virtuális gépek költségeit beszerezni a virtuális gép, a virtuális gépen a tároló account\managed lemez a virtuális gép társított minden prémium szintű szoftvert, és a hálózati sávszélesség átviszi a virtuális gépről.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Hogyan állapítható meg, hogy ha egy virtuális Gépet a használati Megosztott fürtköteten használja az Azure Hybrid Benefit?
Ha segítségével központilag a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/), számítunk fel díjat a nem-Windows-gépek díjszabásához óta, amelyek saját licence a felhőbe. Szerepel a számlán, hogy megkülönböztethesse amely Resource Manager virtuális gépek futnak az Azure Hybrid Benefit, mert vagy "Windows\_kiszolgáló BYOL" vagy "Windows\_ügyfél BYOL" ImageType oszlopában.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Hogyan történik az alapszintű vs. Standard szintű virtuális gépek típusai különbözteti meg a használati CSV-ben?
Sem az alapszintű, Standard A sorozatú virtuális gépekről érhető el. Telepít egy egyszerű virtuális Gépet, az a fogyasztásmérő alkategória, hogy rendelkezik-e a karakterlánc "Alapszintű". Ha egy Standard A sorozatú virtuális Gépet telepít, majd a Virtuálisgép-méret jelenik meg "A1 VM" mivel a standard csomag az alapértelmezett. Alapszintű és standard szintű közti különbségekkel kapcsolatos további tudnivalókért tekintse meg a [díjszabását ismertető weblapon](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Mik azok a ExtraSmall, kicsi, közepes, Large és ExtraLarge méretek?
ExtraSmall – ExtraLarge vannak a régebbi nevek, a standard szintű\_A0 – Standard\_A4. A klasszikus virtuális gép rekordok jelenhet meg ennek az egyezménynek használja, ha telepítette ezeket a méreteket.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Mi a különbség mérőszám régiója és az erőforrás helye?
A mérőszám régiója társítva a mérőszáma. Az egyes Azure-szolgáltatásokat használó árú minden régió esetében a mérőszám régiója mező üres lehet. Azonban mivel a virtuális gépek a virtuális gépek rendelkezhetnek dedikált árak régiónként, ez a mező fel van töltve. Ehhez hasonlóan a virtuális gépek erőforrás helye a hely, ahol a virtuális gép üzemel. Az Azure-régiók mindkét mezőbe azonosak, annak ellenére, hogy előfordulhat, hogy egy másik karakterláncot egyezmény a régió neve.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Miért van a ImageType érték üres a mezőben további információkat?
A ImageType mező kitöltése csak képek egy részét. Ha nem telepítette rendszerképeket a fenti, a ImageType érték üres.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Miért nem üres, a további adatainak a VMName?
A VMName csak töltse be a további információ a mező a virtuális gépek egy méretezési csoportban. A InstanceId azonosító mezőben a virtuális gép neve nem – méretezési csoport virtuális gépeinek esetében.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Mit ComputeHR jelent a további adatokat UsageType mezőjében?
ComputeHR a számítási óra, amely jelöli az alapul szolgáló infrasturcture költségeket a használat esemény rövidítése. Ha a UsageType ComputeHR\_SW, a használati esemény jelenti. a prémium szintű szoftverhasználati díj a virtuális gép számára.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Honnan tudhatom meg, ha a prémium szintű szoftver fizetni?
Ha milyen Virtuálisgép-lemezkép legjobb felfedezése a legjobban az igényeinek, mindenképpen tekintse meg a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute). A rendszerképre a szoftverfrissítési csomag sebességét. "Ingyenes" jelennek meg a sebesség, van-e a szoftverhez további költség nélkül. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Mi a különbség Microsoft.ClassicCompute és Microsoft.Compute a Consumed szolgáltatásban?
Microsoft.ClassicCompute jelöli az Azure Service Manageren keresztül üzembe helyezett klasszikus erőforrások. Ha az erőforrás-kezelő használatával telepít, majd Microsoft.Compute töltse be az igénybe vett szolgáltatás. Tudjon meg többet a [Azure üzembe helyezési modellek](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Miért van a InstanceId azonosító mező üres, a virtuális gép a használat?
Ha a klasszikus üzemi modell használatával telepít, a InstanceID karakterlánc nem érhető el.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Miért találhatók a címkék nem folyik a használat részleteiről a virtuális gépek?
A címkék csak áramlását, a használati CSV-t Resource Manager-beli virtuális gépek csak. Klasszikus az erőforráscímkék nem érhetők el a használati adatok között.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Hogyan lehet több mint 24 órával egy napot a felhasznált mennyiség?
A klasszikus modellben számlázás az erőforrások összesített értéket jelenít meg a Felhőszolgáltatás szintjén. Ha egynél több virtuális gép egy Felhőszolgáltatás, amely ugyanazon változni használ, a használat összesített értéket jelenít meg együtt. Resource Manageren keresztül üzembe helyezett virtuális gépek számlázása a virtuális gép szintjén, így ez az összesítés nem lesznek érvényesek.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Ezért a díjszabás nem érhető el DS/FS vagy GS/LS-méretek esetében a díjszabási lapon?
A Premium storage képes a virtuális gépek számlázása költsége megegyezik a nem prémium szintű storage képes a virtuális gépeket. Csak a tárolási költségek eltérőek. Látogasson el a [tárolási díjszabást ismertető weblapon](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) további információt.

## <a name="next-steps"></a>További lépések
További információk a használat részleteiről lásd: [Microsoft Azure-hoz kapcsolódó számlák magyarázata.](https://docs.microsoft.com/azure/billing/billing-understand-your-bill
)

