---
title: Az Azure virtuális gépek használatának megértése |} Microsoft Docs
description: Virtuális gép használat részleteiről ismertetése
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
ms.openlocfilehash: 9980650008e2d2c2f768dcb3256ea5344119fa13
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="understanding-azure-virtual-machine-usage"></a>Az Azure virtuális gépek használatának ismertetése
Az Azure használati adatok elemzésével, hatékony felhasználása insights is szerzett – elemzések, engedélyezhet jobban költségeket, felügyeleti és kiosztását a szervezetben. Ez a dokumentum egy részletes bemutatója a fogyasztás Azure számítási részleteinek nyújt. Általános Azure használati olvashat, navigáljon a [ismertetése a számlázási](../../billing/billing-understand-your-bill.md).

## <a name="download-your-usage-details"></a>A használat részleteiről letöltése
Első lépésként [töltse le a használat részleteiről](../../billing/billing-download-azure-invoice-daily-usage-date.md). Az alábbi táblázat a definíció- és példa értékek használati biztosít az Azure Resource Manager használatával telepített virtuális gépeket. Ez a dokumentum nem tartalmaz részletes információkat a klasszikus modellt keresztül telepített virtuális gépek.


| Mezők             | Jelentés                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       | Példaértékek                                                                                                                                                                                                                                                                                                                                                   |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Usage Date (Használat dátuma)         | A dátum az erőforrás használatakor.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |  “11/23/2017”                                                                                                                                                                                                                                                                                                                                                     |
| Meter ID (Mérési azonosító)           | Azonosítja a legfelső szintű szolgáltatást, amelynek használata tartozik.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | "Virtual Machines"                                                                                                                                                                                                                                                                                                                                               |
| Meter Sub-Category (Mérési alkategória) | A kiszámlázott mérő azonosítója. <ul><li>Számítási óra használatra van egy mérni minden egyes Virtuálisgép-méretet + az operációs rendszer (Windows, nem Windows) + régióban.</li><li>Prémium szintű szoftverhasználatról nincs a mérő az egyes szoftver. A legtöbb prémium szoftver képeknek különböző mérőszámok minden core méret. További tudnivalókért keresse fel a [számítási árképzést ismertető oldalra.](https://azure.microsoft.com/pricing/details/virtual-machines/)</li></ul>                                                                                                                                                                                                                                                                                                                                         | "2005544f-659d-49c9-9094-8e0aea1be3a5"                                                                                                                                                                                                                                                                                                                           |
| Meter Name (Mérés neve)         | Ez az adott Azure-ban minden egyes szolgáltatás. A számítási esetén mindig "Számítási Hours".                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Számítási órák"                                                                                                                                                                                                                                                                                                                                                  |
| Meter Region (Mérési régió)       | Az igénybe vett vagy üzemeltető adatközpont elhelyezkedése, ha a szolgáltatás díjszabása az adatközpontok elhelyezkedésétől is függ.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |  "Kelet-japán"                                                                                                                                                                                                                                                                                                                                                       |
| Unit (Egység)               | A szolgáltatás fel van töltve a időegység azonosítja. A számítási erőforrások óraalapú díjszabással rendelkeznek.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | "Hours"                                                                                                                                                                                                                                                                                                                                                          |
| Consumed (Felhasznált mennyiség)           | Az erőforrás, amely felhasználta az adott nap összege. Számítás számlázás minden perce, a virtuális gép lefutott egy adott egy óra (akár 6 tizedesjegy pontossággal).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    “1”, “0.5”                                                                                                                                                                                                                                                                                                                                                    |
| Resource Location (Erőforrás helye)  | Az az adatközpont, amelyen az erőforrás fut.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | "Kelet-japán"                                                                                                                                                                                                                                                                                                                                                        |
| Consumed Service (Használt szolgáltatás)   | Az Azure platform szolgáltatás használt.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     | "Microsoft.Compute"                                                                                                                                                                                                                                                                                                                                              |
| Erőforráscsoport     | Az az erőforráscsoport, amelyben az üzembe helyezett erőforrás fut. További információkért lásd: [Azure Resource Manager áttekintése.](../../azure-resource-manager/resource-group-overview.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |    "MyRG"                                                                                                                                                                                                                                                                                                                                                        |
| Instance ID (Példányazonosító)        | Az erőforrás azonosítóját. Az azonosító tartalmazza az erőforrás számára a létrehozáskor megadott nevet. Virtuális gépek Példányazonosítója fogja tartalmazni az előfizetés-azonosító, erőforráscsoport-név és VMName (vagy a méretezési méretezési használatának nevét).                                                                                                                                                                                                                                                                                                                                                                                                                    | "/ előfizetések/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx / resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM1"<br><br>vagy<br><br>"/ előfizetések/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx / resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachineScaleSets/MyVMSS1"                                                                                           |
| Címkék               | Az erőforráshoz hozzárendelt címkét. Számlázási rekordcsoportjának címkék használata. Megtudhatja, hogyan [a virtuális gépek címkét.](tag.md) Ez az erőforrás-kezelő virtuális gépekhez elérhető csak.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                | "{"myDepartment":" A távoli asztali","myUser":"Nev"}"                                                                                                                                                                                                                                                                                                                        |
| Additional Info (További információ)    | Szolgáltatással kapcsolatos metaadatok. Virtuális gépek a mezőben további információ a következő feltöltünk: <ul><li>Kép típusra vonatkozó lemezképe, amely futtatta. Az alábbi típusú kép a támogatott sztringek teljes listáját találja.</li><li>Szolgáltatás típusa: a megadott méret telepítette.</li><li>VMName: a virtuális gép nevét. Ez csak akkor van feltöltve, a virtuális gépek méretezési csoportjának virtuális gépek. Ha a virtuális gép nevét, a méretezés beállítása a virtuális gépek, található, amely a fenti Példányazonosító karakterláncban.</li><li>UsageType: Ez határozza meg a használati jelképez.<ul><li>ComputeHR a számítási óra használatát az alapul szolgáló virtuális gép, például a standard D1 v2 típusú.</li><li>ComputeHR_SW a prémium szintű szoftver kell fizetni, ha a virtuális gép prémium szoftverek, például Microsoft R Server használ.</li></ul></li></ul>    | Virtuális gépek {"ImageType": "Kanonikus", az "ServiceType": "Standard_DS1_v2", "VMName": "", "UsageType": "ComputeHR"}<br><br>Virtuálisgép-méretezési csoportok {"ImageType": "Kanonikus", az "ServiceType": "Standard_DS1_v2", "VMName": "myVM1", "UsageType": "ComputeHR"}<br><br>Prémium szintű szoftvert {"ImageType": "","ServiceType": "Standard_DS1_v2", "VMName": "", "UsageType": "ComputeHR_SW"} |

## <a name="image-type"></a>Lemezkép típusa
Néhány képet, az Azure katalógusában, az a kép típusa a további információ a mező nem üres. Ez lehetővé teszi a felhasználók megismeréséhez, és nyomon követheti, mi ezek telepítette a virtuális gépen. Az értékeket, amelyeket a rendszer feltölti a mezőt, a telepített lemezkép alapján a következők:
  - BitRock 
  - Canonical 
  - FreeBSD 
  - Nyissa meg logika 
  - Oracle 
  - Az SAP SLES 
  - SQL Server 14 előnézete a Windows Server 2012 R2 Preview-ban 
  - SUSE
  - SUSE Premium
  - StorSimple Cloud Appliance 
  - Red Hat
  - Red Hat SAP üzleti alkalmazások     
  - Red Hat SAP Hana 
  - A Windows ügyfél BYOL 
  - Windows Server BYOL 
  - Windows Server Preview-ban 

## <a name="service-type"></a>Szolgáltatás típusa
A szolgáltatás mezőben a további adatok mezőben felel meg a pontos telepített Virtuálisgép-méretet. Prémium szintű storage virtuális gépek (SSD-alapú) és a nem prémium szintű storage (HDD-alapú) virtuális gépek árú azonos. Ha telepít egy SSD-alapú mérete, például a Standard\_DS2\_2-es nem SSD méretének megtekintéséhez ("szabványos\_D2\_v2 virtuális gép") a mérési alkategória oszlop és az SSD-méret ("szabványos\_DS2\_ v2 ") a további adatok mezőben.

## <a name="region-names"></a>Régiók neve
A régió nevét, feltöltve a használat részleteiről erőforrás helye mezőjében a régió nevét, az Azure Resource Manager használt változik. Ez a régió értékek közötti leképezéseket:

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
|    westcentralus         |    USA nyugati középső régiója                       |
|    westeurope            |    Nyugat-Európa                           |
|    WestIndia             |    Nyugat-India                               |
|    westus                |    USA nyugati régiója                               |
|    westus2               |    USA 2. nyugati régiója                             |


## <a name="virtual-machine-usage-faq"></a>Virtuális gépek használatának – gyakori kérdések
### <a name="what-resources-are-charged-when-deploying-a-vm"></a>Milyen erőforrásokat egy virtuális gép telepítésekor van szó?    
Virtuális gépek beszereznie költségek a virtuális gépért, minden prémium szoftvert a virtuális Gépre, a virtuális gép társított account\managed tárolólemez fut, és a hálózati sávszélesség viszi át a virtuális gép.
### <a name="how-can-i-tell-if-a-vm-is-using-azure-hybrid-benefit-in-the-usage-csv"></a>Hogyan állapítható meg, hogy egy virtuális Gépet használja-e Azure hibrid juttatás használatának megosztott fürtkötet?
Ha választhat szabályzatsablonokat a [Azure hibrid juttatás](https://azure.microsoft.com/pricing/hybrid-benefit/), nem Windows virtuális gép sebessége van szó, mivel a rendszer állapotba hozása a saját licenc a felhőbe. A számlázási a úgy azonosíthatjuk, amely erőforrás-kezelő virtuális gépek futnak a Azure hibrid előnye, mert vagy "Windows\_Server BYOL" vagy "Windows\_ügyfél BYOL" ImageType oszlopában.
### <a name="how-are-basic-vs-standard-vm-types-differentiated-in-the-usage-csv"></a>Hogyan történik az alapszintű Visual Studio. Virtuálisgép-típusokon megkülönböztetett a használati CSV-ben?
Basic és Standard A-sorozatú virtuális gépek is érhető el. Ha telepít egy alapszintű virtuális Gépet, a mérési Sub kategória lejárt, a karakterlánc "a Basic". Ha telepít egy szabványos A-sorozatú virtuális Gépet, majd a Virtuálisgép-méretet jelenik meg "VM A1 csomag" óta Standard az alapértelmezett beállítás. Basic és Standard közötti különbségekről további tudnivalókért tekintse meg a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/virtual-machines/).
### <a name="what-are-extrasmall-small-medium-large-and-extralarge-sizes"></a>Mik azok a: ExtraSmall, kicsi, Medium, Large és ExtraLarge mérete?
ExtraSmall - ExtraLarge vannak Standard örökölt nevek\_A0 – Standard\_A4. A klasszikus virtuális gép rekordok láthatja az egyezmény használható, ha telepítette a méretét.
### <a name="what-is-the-difference-between-meter-region-and-resource-location"></a>Mi az a különbség a mérési régió és erőforrás helye?
A mérési régió társítva a mérő. Egyes Azure-szolgáltatások egy ár használó összes régiókhoz a mérési mező lehet üres. Azonban mivel a virtuális gépek régiónként árak dedikált virtuális gépekhez, ez a mező fel van töltve. Hasonlóképpen az erőforrás a virtuális gépek helye az a virtuális gép telepítési helyét. Az Azure-régió, mindkét mezőt megegyeznek, annak ellenére, hogy előfordulhat, hogy egy másik karakterláncot egyezmény a régió nevét.
### <a name="why-is-the-imagetype-value-blank-in-the-additional-info-field"></a>Miért érdemes az ImageType értéke üres a további adatok mezőben?
A ImageType mező csak fel van töltve a képek egy része számára. Ha Ön nem sikerült telepíteni egy újabb lemezképet, a ImageType érték üres.
### <a name="why-is-the-vmname-blank-in-the-additional-info"></a>Miért van a további információ az üres a VMName?
A VMName csak a telepítéskor a további információ a mező a virtuális gépek méretezési csoportban lévő. Az InstanceID mezőben a virtuális gép neve nem méretezési csoport virtuális gépek számára.
### <a name="what-does-computehr-mean-in-the-usagetype-field-in-the-additional-info"></a>Mit ComputeHR jelent a további adatok UsageType mezőjében?
Számítási óra, amely képviseli az alapul szolgáló infrasturcture költség használati eseménye ComputeHR jelöli. Ha a UsageType ComputeHR\_SW, a használati esemény jelenti. a virtuális gép prémium szoftver kell fizetni.
### <a name="how-do-i-know-if-i-am-charged-for-premium-software"></a>Hogyan állapítható meg, ha szeretnék vagyok szó, a prémium szintű szoftver?
Amikor mely Virtuálisgép-lemezkép felfedezése leginkább megfelel az igényeinek, ügyeljen arra, hogy tekintse meg a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute). A kép rendelkezik a szoftverfrissítési csomag sebességét. Ha "Ingyenes" jelennek meg a sebesség, akkor további költség nélkül a szoftver. 
### <a name="what-is-the-difference-between-microsoftclassiccompute-and-microsoftcompute-in-the-consumed-service"></a>Mi az a különbség Microsoft.ClassicCompute és Microsoft.Compute a felhasznált szolgáltatásban?
Microsoft.ClassicCompute hagyományos erőforrások az Azure Service Manager használatával telepített jelöli. Ha az erőforrás-kezelő használatával telepít, majd Microsoft.Compute fel van töltve a felhasznált szolgáltatásban. További információ a [Azure üzembe helyezési modellel](../../azure-resource-manager/resource-manager-deployment-model.md).
### <a name="why-is-the-instanceid-field-blank-for-my-virtual-machine-usage"></a>Miért esetén a virtuális gépek használatának üres InstanceID mező?
Ha a klasszikus üzembe helyezési modellel keresztül, az InstanceID karakterlánc nem érhető el.
### <a name="why-are-the-tags-for-my-vms-not-flowing-to-the-usage-details"></a>Miért van a címkéket a virtuális gépek nem halad a használat részleteiről?
Címkék csak flow Önnek a használati CSV erőforrás-kezelő virtuális gépek csak. Klasszikus erőforráscímkék nem érhetők el a használat részleteiről.
### <a name="how-can-the-consumed-quantity-be-more-than-24-hours-one-day"></a>Hogyan lehet több mint 24 órája egy napot a felhasznált mennyiség?
A klasszikus modellben az erőforrások számlázási a felhőalapú szolgáltatás szintjén összesített értéket. Ha egynél több virtuális gép található egy Felhőszolgáltatás, amely a azonos számlázási mérő használ, a használati együtt összesíti. Resource Manager használatával telepített virtuális gépek számlázása a virtuális gép szinten, ezért ez az összesítés nem lesznek érvényesek.
### <a name="why-is-pricing-not-available-for-dsfsgsls-sizes-on-the-pricing-page"></a>Ezért az árazás nem érhető el az DS/FS/GS LS-méretek a tarifákat tartalmazó oldalt?
Prémium szintű storage képes a virtuális gépek nem prémium szintű storage azonos mértékű amelyekért képes a virtuális gépek. Csak a tárolási költségeket térnek el egymástól. Látogasson el a [árképzést ismertető oldalra tárolási](https://azure.microsoft.com/pricing/details/storage/unmanaged-disks/) további információt.

## <a name="next-steps"></a>További lépések
További tájékoztatás a használat részleteiről lásd: [a Microsoft Azure a számlázási ismertetése.](../../billing/billing-understand-your-bill.md)

