---
title: Virtuális gép SKU-k lapot a Cloud Partner portálra, az Azure Marketplace-en |} A Microsoft Docs
description: Az SKU-k lapon, az Azure piactéren virtuálisgép-ajánlat létrehozásakor használt ismerteti.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: e05813297707165d59d1b1ed969b54fb0f433277
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57848090"
---
# <a name="virtual-machine-skus-tab"></a>Virtuális gép termékváltozatok lap

A **termékváltozatok** lapján a **új ajánlat** lap lehetővé teszi, hogy hozzon létre egy vagy több termékváltozatok és rendelheti őket hozzá az új ajánlat.  Különböző Termékváltozatai tudja különböztetni szolgáltatáskészleteket, Virtuálisgép-lemezképek típusai, átviteli sebesség vagy a méretezhetőség, számlázási modellek vagy valamilyen más jellemzők szerint megoldást.

## <a name="create-a-sku"></a>A Termékváltozat létrehozása

Kezdetben egy új ajánlat nem fog minden társított SKU-k, így létrehozhat egy kattintva **új Termékváltozat**.

![A virtuális gépek új ajánlat lapon új Termékváltozat gomb](./media/publishvm_005.png)

<br/>

A **új Termékváltozat** párbeszédpanel jelenik meg.  Adja meg az azonosító az új termékváltozat, majd kattintson a **OK**. (Lásd alább azonosító elnevezési konvenciókat.)  A **termékváltozatok** lap megjeleníti a mezők érhető el szerkesztésre.    A hozzáfűzött csillag (*) az a mező nevét jelzi, hogy szükséges.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![A virtuális gépek az új ajánlat űrlap Termékváltozat lapján](./media/publishvm_006.png)

A következő táblázat ismerteti a célja, a tartalmát, és ezek a mezők formázását.

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
|  *Termékváltozat-beállítások*   |  |
| **SKU-AZONOSÍTÓJA**       | Ez a Termékváltozat azonosítója.  Ez a név legfeljebb 50 karakter hosszú lehet, amely rendelkezik kisbetűs alfanumerikus karaktereket és kötőjeleket (-), de nem végződhet kötőjellel.  Az ajánlat közzététele után nem módosítható.  |
|  *Termékváltozat részletei*   |  |
| **Cím**        | Megjelenítés a piactéren az ajánlat rövid neve. Legfeljebb 50 karakter hosszúságú. |
| **összegzés**      | Az ajánlatot a piactéren való megjelenítéshez tömör leírása. A hosszabb 100 karakternél. |
| **Leírás**  | Leíró szöveg, amely biztosít az ajánlat részletes leírását.  <!-- TD: max len/guidance? 3k characters -->  |
| **Ez a Termékváltozat elrejtése** | Azt jelzi, hogy a Termékváltozatot kell látható a piactéren az ügyfelek számára.  Előfordulhat, hogy el szeretné rejteni a Termékváltozat, ha azt szeretné csak akkor érhető el, csak a megoldás sablonok használatával, és nem a vásárlás külön-külön.  Azt is hasznos lehet a kezdeti tesztelés vagy ideiglenes vagy szezonális ajánlatok. |
| **Felhőalapú rendelkezésre állása** | Meghatározza, hogy melyik felhő az SKU elérhetőnek kell lennie.  Az alapértelmezett érték az Azure a nyilvános verzióval.  A Microsoft Azure Government egy kormányzati-közösségi felhő az USA szövetségi, állami, helyi vagy törzsi kormányzati és a hivatalos partnerek által szabályozott hozzáféréssel rendelkező.  További információ a kormányzati felhő: [Üdvözli az Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). |
| **Az egy privát Termékváltozat?** | Azt jelzi, hogy a Termékváltozat nyilvános vagy privát. Az alapértelmezett érték **nem** (nyilvános).  További információkért lásd: [nyilvános és privát termékváltozatok](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). |
| **Elérhetőség ország/régió** | Meghatározza, hogy mely országok vagy régiók, a Termékváltozat lesz megvásárolható. Válassza ki legalább egy ország/régió. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Díjszabás*   |  |
| **License Model**| Szabványos számlázási modellt használja.  Ha **havi használat alapú Termékváltozat számlázása**, egy accordion szakasz nyílik meg, hogy adja meg a részleteket magonként árképzési és a próbaidőszak alatt ingyenes ajánlat szeretné-e.  Ez a szakasz segítségével exportálja és importálja a díjszabási ütemezés Excel is. További információkért lásd: [számlázási lehetőségek az Azure Marketplace-en](../../billing-options-azure-marketplace.md). | 
|  *Virtuálisgép-rendszerképek*   |  |
| **Operációsrendszer-család** | Azt jelzi, hogy a megoldás virtuális gép Windows - vagy Linux-alapú. |
| **Válassza ki az operációs rendszer típusa** | Adott gyártó vagy a megadott operációs rendszer kiadása. |
| **Operációs rendszer rövid neve** | Operációs rendszer neve megjelenik az ügyfelek számára.  |
| **Javasolt Virtuálisgép-méretek** | Lehetővé teszi, hogy legfeljebb hat javasolt Virtuálisgép-méretek egy szabványosított listából választott.  Bár ezek a javaslatok ezzel a beállítással hangsúlyosan jelennek meg a lehetséges ügyfelek, azok bármely Virtuálisgép-méret, amely kompatibilis a megoldás kép megadhat. | 
| **Nyitott portok**| A portok megnyitásához, és támogatja a termékváltozat-protokollt.  Ezek a konfigurációk meg kell egyeznie a virtuális hálózatot, konfigurálta a hálózat VM-megoldás. Ezek a beállítások során a virtuális gép üzembe helyezésének lép érvénybe. Portbeállítások azonban módosítható egy Termékváltozat a közzététel után. További információkért lásd: [hogyan nyithat meg portokat a virtuális gép az Azure Portallal](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal). <br/>A következő alapértelmezett hálózatleképezések kerülnek az összes virtuális gépre. &emsp; Windows: 3389-es 3389-es TCP ->, az 5986-os 5986-os TCP -> &emsp; Linux: 22 -&GT; 22-ES, A TCP-(SSH). |
| **Lemez verziója**  | Kapcsolódó megoldás virtuális Gépen lemez verziószám és lemez URL-cím által megadott. A lemez kell lennie a [sémantická verze](https://semver.org/) formátum: `<major>.<minor>.<patch>`.  Az URL-je a közös hozzáférésű jogosultságkód URI-t az operációs rendszer virtuális Merevlemeze számára létrehozott.  Bár a Termékváltozat száma nyolc lemez verziók adhat hozzá, csak a legmagasabb szintű lemez verziószám-termékváltozat jelennek meg az Azure Marketplace-en. Az egyéb verziói csak akkor látható, API-kon keresztül.  <!--TD: Add more specific link to API --> <br/> A **új adatlemez** accordion szakasz lehetővé teszi, hogy akár 15 adatlemezeket csatlakoztathat a virtuális gép.  Miután egy adott virtuális gép verzióját, és a társított adatlemezek Termékváltozat tesz közzé, amelyet ez a konfiguráció nem módosítható.  Ha további VM-verziók hozzáadja a termékváltozatra, azok azonos számú adatlemezeket is támogatnia kell. <br/> Ha nem hozott létre az Azure-alapú Virtuálisgép-rendszerképeket, hozzáadhat, később frissíteni ezt a mezőt.  A társított virtuális gép erőforrás létrehozásával kapcsolatos információkért lásd: a szakasz [hozzon létre virtuális gép technikai eszközök](./cpp-create-technical-assets.md).  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Kattintson a **mentése** a folyamat mentéséhez. A következő lapon kell adnia az ajánlat támogatja [Test Drive](./cpp-test-drive-tab.md).


## <a name="additional-pricing-considerations"></a>További szempontok díjszabása

A fenti díjszabási modell egy egyszerű leírást.  Alatt áll a módosítások és a helyi és regionális adó szabályzat és a Microsoft díjszabás szabályzatok is hatással lehetnek. 

### <a name="simplified-currency-pricing"></a>Egyszerűsített pénznem díjszabása

2018 szeptember 1-től kezdődően új szakasz nevű **pénznem díjszabás egyszerűsített** megjelenik a portálon. A Microsoft Azure Marketplace-en üzleti van egyszerűsítésével világszerte több kiszámítható díjszabás és a gyűjtemény ügyfelei engedélyezésével. Ez egyszerűsítheti tartalmazza az ügyfelek feléjük azt pénznemek számának csökkentése.  További információkért lásd: [egy meglévő virtuális Gépet kínál az Azure Marketplace-en frissítés](./cpp-update-existing-offer.md).


### <a name="additional-information-on-taxes-and-prices"></a>További tájékoztatást az adók és díjszabás

* A Microsoft osztályozza, egyes országok *országok elengedik adó*.  Az ilyen országokban Microsoft adók gyűjt az ügyfelektől származó, majd a kormányzati (kap) adók fizet.  Más országokban partnerek felelős általában adók gyűjtését az ügyfeleknek, és az adók és a kormány. Válassza ki, hogy az utóbbi országokban, ha kiszámítja és helyi adók kell fizetnie kell rendelkeznie.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Miután egy ajánlatot élesíti, amelyek árak nem módosítható. Azonban, előfordulhat, hogy továbbra is hozzáadhat vagy eltávolíthat támogatott régiók. 
* A Microsoft a díjat az ügyfél standard szintű Azure virtuális gép használati díjak az ütemezett Termékváltozat díjak mellett.
* Az árak vannak beállítva a minden régióban elérhető pénznem díjait a helyi pénznemben számított árak beállítás időpontjában.  <!-- TD: Meaning? - Offer created, published, other? -->
* Külön-külön beállítása az egyes régiókban ár, kérjük exportálása a díjszabási táblázatot, alkalmazza az egyéni díjszabás, majd importálja. 

<!-- TD: The detailed information in the table and supplemental notes should be centralized in another topic, maybe "Billing Options" in AMP tree. Need to include a common section on export/import pricing-->

