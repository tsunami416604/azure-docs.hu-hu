---
title: Az Azure virtuális gép használatának ismertetése
description: A virtuális gépek használatának részletei
services: virtual-machines
documentationcenter: ''
author: mmccrory
manager: gwallace
editor: ''
tags: azure-virtual-machine
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 12/04/2017
ms.author: memccror
ms.openlocfilehash: 2aa175d97787d82aae062a95ed519f35ff65816b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982257"
---
# <a name="understanding-azure-virtual-machine-usage"></a>Az Azure virtuális gép használatának ismertetése
Az Azure-használati adatok elemzésével hatékony felhasználási információk nyerhetők – olyan információk, amelyek lehetővé teszik a jobb költségkezelést és -elosztást a szervezeten belül. Ez a dokumentum részletesen bemutatja az Azure Compute-felhasználás részleteit. Az Azure általános használatával kapcsolatos további részletekért keresse fel [a Számla ismertetése című témakört.](../../cost-management-billing/understand/review-individual-bill.md)

## <a name="download-your-usage-details"></a>Használati adatok letöltése
Először [töltse le a használati adatait.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md) Az alábbi táblázat az Azure Resource Manageren keresztül üzembe helyezett virtuális gépek használatának definícióját és használati példaértékeit tartalmazza. Ez a dokumentum nem tartalmaz részletes információkat a klasszikus modellen keresztül üzembe helyezett virtuális gépekről.


| Mezők             | Jelentés                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Példa értékek                                                                                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Usage Date (Használat dátuma)         | Az erőforrás használatának dátuma.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |  "11/23/2017"                                                                                                                                                                                                                                                                                                                                                     |
| Meter ID (Mérési azonosító)           | Azt a legfelső szintű szolgáltatást azonosítja, amelyhez ez a használat tartozik.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | "Virtuális gépek"                                                                                                                                                                                                                                                                                                                                               |
| Meter Sub-Category (Mérési alkategória) | A kiszámlázott mérő azonosítója. <ul><li>A számítási óra használata, minden virtuális gép mérete + operációs rendszer (Windows, nem Windows) + régió egy mérő.</li><li>A prémium szoftverhasználat, van egy mérő minden szoftver típus. A legtöbb prémium szoftverlemezkép különböző mérőműszerekkel rendelkezik az egyes magméretekhez. További információért látogasson el a [Számítási árképzési oldalra.](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>                                                                                                                                                                                                                                                                                                                                         | "2005544f-659d-49c9-9094-8e0aea1be3a5"                                                                                                                                                                                                                                                                                                                           |
| Meter Name (Mérés neve)         | Ez az Azure-ban minden egyes szolgáltatásra vonatkozik. A számítási, mindig "Számítási órák".                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Számítási órák"                                                                                                                                                                                                                                                                                                                                                  |
| Meter Region (Mérési régió)       | Az igénybe vett vagy üzemeltető adatközpont elhelyezkedése, ha a szolgáltatás díjszabása az adatközpontok elhelyezkedésétől is függ.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |  "JA Kelet"                                                                                                                                                                                                                                                                                                                                                       |
| Unit (Egység)               | A szolgáltatás számlázási egységét jelöli. A számítási erőforrások számlázása óránként.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Órák"                                                                                                                                                                                                                                                                                                                                                          |
| Consumed (Felhasznált mennyiség)           | Az adott napra felhasznált erőforrás mennyisége. A Compute esetében minden egyes percet számlázunk, amikor a virtuális gép egy adott órában (legfeljebb 6 tizedes pontosság) futott.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    "1", "0.5"                                                                                                                                                                                                                                                                                                                                                    |
| Resource Location (Erőforrás helye)  | Az az adatközpont, amelyen az erőforrás fut.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | "JA Kelet"                                                                                                                                                                                                                                                                                                                                                        |
| Consumed Service (Használt szolgáltatás)   | Az Azure platform szolgáltatás, amelyet használt.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | "Microsoft.Compute"                                                                                                                                                                                                                                                                                                                                              |
| Erőforráscsoport     | Az az erőforráscsoport, amelyben az üzembe helyezett erőforrás fut. További információ: [Azure Resource Manager overview.](../../azure-resource-manager/management/overview.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |    "MyRG"                                                                                                                                                                                                                                                                                                                                                        |
| Instance ID (Példányazonosító)        | Az erőforrás azonosítója. Az azonosító tartalmazza az erőforrás számára a létrehozáskor megadott nevet. Virtuális gépek esetén a példányazonosító tartalmazza a SubscriptionId, ResourceGroupName és VMName (vagy méretezési csoport nevét méretezési csoport használata).                                                                                                                                                                                                                                                                                                                                                                                                                    | "/előfizetések/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1"<br><br>vagy<br><br>"/előfizetések/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxxxx/ resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1"                                                                                           |
| Címkék               | Az erőforráshoz rendelt címke. Címkék használatával csoportosíthatja a számlázási rekordokat. További információ [a virtuális gépek címkézése.](tag.md) Ez csak az Erőforrás-kezelő virtuális gépei számára érhető el.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | "{"myDepartment":"RD","myUser":"myName"}"                                                                                                                                                                                                                                                                                                                        |
| Additional Info (További információ)    | Szolgáltatásspecifikus metaadatok. A virtuális gépek esetében a következőadatokat használjuk fel a további információmezőben: <ul><li>Képtípus- adott kép, amelyet futtatott. Keresse meg a támogatott karakterláncok teljes listáját az alábbi Képtípusok csoportban.</li><li>Szolgáltatás típusa: a telepített méret.</li><li>VMName: a virtuális gép neve. Ez csak a méretezési készlet virtuális gépek. Ha szüksége van a virtuális gép nevét a méretezési csoport virtuális gépek, megtalálja, hogy a fenti példányazonosító-karakterlánc.</li><li>UsageType: Ez határozza meg, hogy ez milyen típusú használatot jelent.<ul><li>A számítási környezet az alapul szolgáló virtuális gép számítási órájának használata, például Standard_D1_v2.</li><li>ComputeHR_SW a prémium szoftver díj, ha a virtuális gép prémium szoftvert használ, például a Microsoft R Server.</li></ul></li></ul>    | Virtuális gépek {"ImageType":"Kanonikus","ServiceType":"Standard_DS1_v2","VMName":"", "UsageType":"ComputeHR"}<br><br>Virtuálisgép-méretezési készletek {"ImageType":"Kanonikus","ServiceType":"Standard_DS1_v2","VMName":"myVM1", "UsageType":"ComputeHR"}<br><br>Prémium szoftver {"ImageType":"""ServiceType":"Standard_DS1_v2","VMName":"","UsageType":"ComputeHR_SW"} |

## <a name="image-type"></a>Rendszerkép típusa
Az Azure-katalógusegyes képek esetén a rendszerkép típusa a További információ mezőben van feltöltve. Ez lehetővé teszi a felhasználók számára, hogy megértsék és nyomon kövessék, mit telepítettek a virtuális gépen. A mezőben az üzembe helyezett lemezkép alapján a következő értékek kerülnek feltöltésre:
  - BitRock (BitRock) 
  - Canonical 
  - FreeBSD 
  - Logika megnyitása 
  - Oracle 
  - SLES sap-hoz 
  - SQL Server 14 előzetes verzió Windows Server 2012 R2 előzetes verzióban 
  - SUSE
  - SUSE Prémium
  - StorSimple felhőberendezés 
  - Red Hat
  - Red Hat SAP üzleti alkalmazásokhoz     
  - Red Hat az SAP HANA-hoz 
  - Windows-ügyfél BYOL 
  - Windows Server BYOL 
  - Windows Server előzetes verzió 

## <a name="service-type"></a>Szolgáltatástípus
A További információ mező szolgáltatástípus-mezője a telepített virtuális gép pontos méretének felel meg. Prémium szintű tárolási virtuális gépek (SSD-alapú) és a nem prémium szintű tárolási virtuális gépek (HDD-alapú) ára megegyezik. Ha ssd-alapú méretet telepít,\_például\_a Standard DS2 v2-t,\_a\_nem SSD-mérete ("Standard D2 v2 VM")\_a mérőalkategória oszlopban, az SSD-méret ("Standard DS2\_v2") pedig a További információ mezőben jelenik meg.

## <a name="region-names"></a>Régiónevek
A használat részleteiben az Erőforrás helye mezőben feltöltődő régiónév az Azure Resource Managerben használt régiónévtől függ. Az alábbiakban a régióértékek közötti leképezést olvashatja:

|    **Erőforrás-kezelő régióneve**       |    **Erőforrás helye a használat részleteiben**    |
|--------------------------|------------------------------------------|
|    ausztrália         |    Ausztrália keleti régiója                               |
|    australiasoutheast    |    Délkelet-Ausztrália                          |
|    brazilsouth           |    Dél-Brazília                              |
|    KanadaKözponti         |    Közép-Kanada                            |
|    KanadaKelet            |    Kelet-Kanada                               |
|    Közép-India          |    Közép-India                            |
|    centralus             |    USA középső régiója                            |
|    kína-kelet             |    Kelet-Kína                            |
|    chinanorth között            |    Észak-Kína                           |
|    eastasia              |    Kelet-Ázsia                             |
|    eastus                |    USA keleti régiója                               |
|    eastus2               |    USA 2. keleti régiója                             |
|    NémetországKözponti        |    DE Központi                            |
|    NémetországÉszakkelet      |    DE Északkelet                          |
|    japaneast             |    Kelet-Japán                               |
|    japánnyugat             |    Nyugat-Japán                               |
|    KoreaKözponti          |    Korea középső régiója                            |
|    KoreaDél            |    Korea déli régiója                              |
|    northcentralus között        |    USA északi középső régiója                      |
|    northeurope           |    Észak-Európa                          |
|    southcentralus        |    USA déli középső régiója                      |
|    southeastasia         |    Délkelet-Ázsia                        |
|    Dél-India            |    Dél-India                              |
|    UKNorth között               |    USA északi                              |
|    uksouth               |    Az Egyesült Királyság déli régiója                              |
|    UKSouth2              |    Az Egyesült Királyság 2. déli régiója                            |
|    ukwest között                |    Az Egyesült Királyság nyugati régiója                               |
|    USDoDCentral között          |    US DoD – Középső régió                        |
|    USDoDEast között             |    US DoD – Kelet                           |
|    USGovArizona között          |    USGov Arizona                         |
|    usgoviowa között             |    USGov Iowa                            |
|    USGovTexas között            |    USGov Texas                           |
|    usgovvirginia között         |    USGov Virginia                        |
|    USA nyugati középső régiója         |    USA nyugati középső régiója                       |
|    westeurope            |    Nyugat-Európa                           |
|    Nyugat-India             |    Nyugat-India                               |
|    westus                |    USA nyugati régiója                               |
|    westus2               |    USA 2. nyugati régiója                             |


## <a name="virtual-machine-usage-faq"></a>Virtuális gép használatáról – gyakori kérdések
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Milyen erőforrásokat kell fizetni a virtuális gép üzembe helyezésekor?    
Virtuális gépek a virtuális gép költségeit szerzi meg, a virtuális gépen futó prémium szintű szoftverek, a virtuális géphez társított tárfiók\felügyelt lemez, valamint a virtuális gép hálózati sávszélesség-átvitele.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Hogyan állapítható meg, hogy egy virtuális gép használja-e az Azure Hybrid Benefit-et a használati CSV-ben?
Ha az [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)használatával telepíti, akkor a nem Windows virtuális gép díja, mivel saját licencet hoz a felhőbe. A számlán meg lehet különböztetni, hogy mely Erőforrás-kezelő virtuális\_gépek futnak az\_Azure Hybrid Benefit, mert az ImageType oszlopban "Windows Server BYOL" vagy "Windows Client BYOL" van.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Hogyan különböztethető meg az alapszintű és a szabványos virtuálisgép-típusok a használati CSV-ben?
Alapszintű és standard A-sorozatú virtuális gépek is rendelkezésre állnak. Ha egy egyszerű virtuális gép üzembe helyezése, a mérő alkategóriában, azt a karakterlánc "Basic.". Ha üzembe helyez egy szabványos A-sorozatú virtuális gép, majd a virtuális gép mérete jelenik meg "A1 virtuális gép", mivel a standard az alapértelmezett. Ha többet szeretne megtudni az Alapszintű és a Normál közötti különbségekről, olvassa el az [Árképzési oldalt.](https://azure.microsoft.com/pricing/details/virtual-machines/)
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Mik azok az ExtraSmall, Small, Medium, Large és ExtraNagy méretek?
ExtraSmall - ExtraLarge az örökölt\_nevek Standard\_A0 - Standard A4. A klasszikus virtuálisgép-használati rekordok, előfordulhat, hogy ez a konvenció, ha telepítette ezeket a méreteket.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Mi a különbség a mérőterület és az erőforrás helye között?
A mérőrégió a mérőhöz van társítva. Egyes Azure-szolgáltatások, akik egy árat minden régióban, a mérőrégió mező üres lehet. Mivel azonban a virtuális gépek régiónként dedikált árakat rendelkeznek a virtuális gépekhez, ez a mező ki van töltve. Hasonlóképpen a virtuális gépek erőforrás-helye az a hely, ahol a virtuális gép telepítve van. Az Azure-régió mindkét területen megegyezik, bár előfordulhat, hogy egy másik karakterlánc-konvenció a régió neve.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Miért üres az ImageType érték a További információ mezőben?
Az ImageType mező csak a képek egy részhalmazához van feltöltve. Ha nem a fenti lemezképek egyikét telepítette, az ImageType üres.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Miért üres a VMName a További információ?
A VMName csak a méretezési készlet ben lévő virtuális gépek további információ mezőjében van feltöltve. A InstanceID mező tartalmazza a virtuális gép nevét a nem méretezési csoport virtuális gépek.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Mit jelent a ComputeHR a További információ UsageType mezőjében?
A Számítási HR a Számítási óra rövidítése, amely az alapul szolgáló infrastruktúraköltség használati eseményét jelöli. Ha a UsageType a\_ComputeHR SW, a használati esemény a virtuális gép prémium szintű szoftverdíját jelöli.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Honnan tudhatom, hogy prémium szoftverért kell fizetnem?
Ha azt vizsgálja, hogy melyik virtuálisgép-lemezkép felel meg leginkább az igényeinek, mindenképpen tekintse meg az [Azure Piacteret.](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute) A kép rendelkezik a szoftverterv díja. Ha az "Ingyenes" lehetőséget látja az árhoz, a szoftvernek nincs további költsége. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Mi a különbség a Microsoft.ClassicCompute és a Microsoft.Compute között a Felhasznált szolgáltatásban?
A Microsoft.ClassicCompute az Azure Service Manageren keresztül üzembe helyezett klasszikus erőforrásokat jelöli. Ha az Erőforrás-kezelőn keresztül telepíti a központi telepítést, akkor a Microsoft.Compute a felhasznált szolgáltatásban van feltöltve. További információ az [Azure deployment-modelljeiről.](../../azure-resource-manager/management/deployment-models.md)
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Miért üres a InstanceID mező a virtuális gép használatához?
Ha a klasszikus üzembe helyezési modellen keresztül telepíti, a InstanceID-karakterlánc nem érhető el.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Miért nem a virtuális gépek címkéi nem áramlanak a használati részletekbe?
A címkék csak a használati CSV csak erőforrás-kezelő virtuális gépek hez. Klasszikus erőforrás-címkék nem érhetők el a használati részletekben.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Hogyan lehet a felhasznált mennyiség több mint 24 óra egy nap?
A klasszikus modellben az erőforrások számlázása a felhőszolgáltatás szintjén összesítve történik. Ha egynél több virtuális gép egy felhőalapú szolgáltatás, amely ugyanazt a számlázási mérő, a használat összesítése. Az Erőforrás-kezelőn keresztül telepített virtuális gépek számlázása a virtuális gép szintjén van számlázva, így ez az összesítés nem lesz érvényes.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Miért nem érhető el az árak a DS/FS/GS/LS méreteknél az árképzési oldalon?
A prémium szintű storage-kompatibilis virtuális gépek számlázása ugyanolyan ütemben van számlázva, mint a nem prémium szintű storage-képes virtuális gépek. Csak a tárolási költségek különböznek. További információkért látogasson el a [tárolási díjszabási oldalra.](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/)

## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a használati adatokról, [olvassa el A Microsoft Azure-ra vonatkozó számla ismertetése.](../../cost-management-billing/understand/review-individual-bill.md)

