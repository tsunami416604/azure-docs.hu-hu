---
title: A virtuálisgép-skuk lap az Azure Piactér felhőpartneri portálján
description: Ismerteti a virtuális gép ajánlat az Azure Piactéren létrehozott termékkészletlap.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 49f1de5128325b2884ea76b010727be45f1b195d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288886"
---
# <a name="virtual-machine-skus-tab"></a>Virtuálisgép-ska lap

Az **Új ajánlat** lap **SK-ja** lehetővé teszi egy vagy több skus létrehozását, és társíthatja őket az új ajánlathoz.  A különböző terméktájékoztatók szolgáltatáskészletek, virtuálisgép-lemezképtípusok, átviteli vagy méretezhetőség, számlázási modellek vagy más jellemzők szerint különböztethetimeg a megoldásokat.


## <a name="create-a-sku"></a>Termékváltozat létrehozása

Kezdetben egy új ajánlatnem rendelkezik társított termékváltozatokkal, ezért az **Új termékváltozat**elemre kattintva hozhat létre egyet.

![Az Új ajánlat lap Új Termékváltozat gombja a virtuális gépekhez](./media/publishvm_005.png)

<br/>

Megjelenik az **Új termékváltozat** párbeszédpanel.  Adja meg az új termékváltozat azonosítóját, majd kattintson az **OK**gombra. (Az azonosítóelnevezési konvenciókat lásd alább.)  A **SK-k** lapon megjelennek a szerkesztésre rendelkezésre álló mezők.    A mezőnévhez csatolt csillag (*) azt jelzi, hogy szükség van rá.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![A Virtuális gépek új ajánlatűrlapjának Termékváltozat lapja](./media/publishvm_006.png)

Az alábbi táblázat a mezők célját, tartalmát és formázását ismerteti.  A kötelező mezőket csillag (*) vádolja.

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
|  *Termékváltozat beállításai*   |    |
| **Termékváltozat-azonosító\***       | A termékváltozat azonosítója.  Ez a név legfeljebb 50 karakterből áll, amely kisméretű alfanumerikus karakterekből vagy kötőjelekből (-) áll, de nem végződhet kötőjellel.  Az ajánlat közzététele után nem módosítható.  |
|  *SKU részletek*   |  |
| **Cím\***        | A piacon megjelenendő ajánlat rövid neve. Legfeljebb 50 karakter hosszú. |
| **Összefoglaló\***      | A piacon való megjelenítésre vonatkozó ajánlat tömör leírása. Legfeljebb 100 karakter hosszú. |
| **Leírás\***  | Leírás az ajánlat részletesebb magyarázatát tartalmazó szöveg.  <!-- TD: max len/guidance? 3k characters -->  |
| **A termékváltozat elrejtése\*** | Azt jelzi, hogy a termékváltozat látható legyen-e a piactéren az ügyfelek számára.  Érdemes lehet elrejteni a termékváltozat, ha csak azt szeretné, hogy csak a megoldás sablonokon keresztül érhető el, és nem a vásárlás külön-külön.  Ez is hasznos lehet a kezdeti tesztelés, illetve az ideiglenes vagy szezonális ajánlatok. |
| **Felhő elérhetősége\*** | Meghatározza, hogy a termékváltozat mely felhőkön legyen elérhető.  Az alapértelmezett az Azure nyilvános verziója.  A Microsoft Azure Government egy kormányzati közösségi felhő, amely ellenőrzött hozzáféréssel rendelkezik az Egyesült Államok szövetségi, állami, helyi vagy törzsi kormányai és azok minősített partnerei számára.  A kormányzati felhőről további információt az [Üdvözli az Azure Government.](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) |
| **Ez egy privát termékváltozat?\*** | Azt jelzi, hogy a termékváltozat magán- vagy nyilvános. Az alapértelmezett érték a **Nem** (nyilvános).  További információ: [Public and Private SKUs](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). |
| **Ország/régió elérhetősége\*** | Meghatározza, hogy a termékváltozat mely országokban vagy világrégiókban lesz megvásárolható. Jelöljön ki legalább egy régiót/országot. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Díjszabás*   |  |
| **Licencmodell\***| Szabványos számlázási modell.  Ha a **Használat alapú havi számlázott termékváltozatlehetőséget választja,** megnyílik egy harmonika szakasz, amely lehetővé teszi a magonkénti árképzés részleteinek megadását, valamint azt, hogy szeretne-e ingyenes próbaidőszakot kínálni.  Ez a szakasz azt is lehetővé teszi, hogy exportálja és importálja ezt a díjszabási ütemezést az Excelbe. További információt a [Számlázási lehetőségek az Azure Piactéren című témakörben talál.](../../billing-options-azure-marketplace.md) | 
|  *Virtuálisgép-rendszerképek*   |  |
| **Operációs rendszer család\*** | Azt jelzi, hogy a megoldás virtuális gép e Windows- vagy Linux-alapú. |
| **Operációs rendszer típusának kiválasztása** | Adott szállító vagy a megadott operációs rendszer kiadása. |
| **Operációs rendszer rövid neve\*** | Az operációs rendszer neve megjelenik a vevők számára.  |
| **Ajánlott virtuális gép méretei\*** | Lehetővé teszi legfeljebb hat ajánlott virtuálisgép-méret kiválasztását egy szabványosított listából.  Ez a lista az Azure Portalon és a Microsoft piacterein található.  A listában szereplő első virtuális gépméret érvényes (az adott ügyfél-előfizetéshez, régióhoz, zónához stb.) az adott potenciális ügyfél alapértelmezett beállítása.  A felhasználó ezt a méretet a megoldásképpel kompatibilisre módosíthatja. | 
| **Nyitott portok**| A termékváltozat megnyitásához és protokollhoz való portjai.  Ezeknek a konfigurációknak meg kell egyezniük a megoldás virtuális géphálózatához konfigurált virtuális hálózattal. Ezek a beállítások a virtuális gép üzembe helyezése során lépnek érvénybe. A portbeállítások azonban a termékváltozat közzététele után módosíthatók. További információért olvassa el [a portok a virtuális gépek számára az Azure Portallal való megnyitását](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal) ismertető cikket. <br/>A következő alapértelmezett hálózati hozzárendelések kerülnek az összes virtuális gépre. &emsp;Windows: 3389 -> 3389 TCP, 5986 -> 5986 TCP; &emsp; Linux: 22 -> 22, TCP (SSH). |
| **Lemez verziója**  | Társított megoldás virtuális gép, lemez verziószáma és a lemez URL-címe által megadott. A lemezverziónak [szemantikai](https://semver.org/) verzióformátumúnak kell lennie: `<major>.<minor>.<patch>`.  Az URL-cím az operációs rendszer virtuális merevlemezéhez létrehozott megosztott hozzáférésű aláírás, amelyet az operációs rendszerhez hoztak létre.  Bár termékváltozatonként legfeljebb nyolc lemezverziót adhat hozzá, csak a termékváltozat legmagasabb lemezverziószáma jelenik meg az Azure Marketplace-en. A többi verzió csak API-kon keresztül lesz látható.  <!--TD: Add more specific link to API --> <br/> Az **új adatlemez** harmonika szakasz lehetővé teszi, hogy legfeljebb 15 adatlemezeket csatoljon a virtuális géphez.  Miután közzétett egy termékváltozatot egy adott virtuális gép verzióval és a kapcsolódó adatlemezekkel, ez a konfiguráció nem módosítható.  Ha további virtuálisgép-verziók kerülnek a termékváltozatba, akkor ugyanennyi adatlemezt is támogatniuk kell. <br/> Ha még nem hozta létre az Azure-alapú virtuálisgép-lemezkép(ek) , később hozzáadhatja ezt a mezőt.  A társított virtuálisgép-erőforrás létrehozásáról a [Virtuálisgép-technikai eszközök létrehozása](./cpp-create-technical-assets.md)című szakaszban talál további információt.  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> Kattintson a **Mentés** gombra a folyamat mentéséhez. A következő lapon megadhatja, hogy az ajánlat támogatja-e a [Test Drive-ot.](./cpp-test-drive-tab.md)


## <a name="additional-pricing-considerations"></a>További árképzési szempontok

A fent leírt árképzési modell alapvető leírás.  Változásokon megy keresztül, és a helyi vagy regionális adószabályok és a Microsoft árképzési irányelvei hatással lehetnek rá. 

### <a name="new-core-sizes-added-on-722019"></a>Új magméretek hozzáadva a 7/2/2019-en

2019. július 2-án a virtuális gép közzétevői értesítést kaptak az új Azure virtuálisgép-méretek új árainak hozzáadásáról (a magok száma alapján).  Az új árak a 10, 44, 48, 60, 120, 208 és 416 magméretekre vannak.  A meglévő virtuális gép kínál új árak at ezek a magok méretek automatikusan számított a jelenlegi árak alapján.  2019. augusztus 1-ig a kiadóknak át kell tekinteniük a további árakat, és el kell készíteniük a kívánt módosításokat.  Ezen időpont után, ha a kiadó még nem tette közzé újra, az új alapméretek automatikusan kiszámított árai lépnek érvénybe.


### <a name="simplified-currency-pricing"></a>Egyszerűsített valutaárképzés

2018. szeptember 1-jétől egy egyszerűsített **valutaárképzés** nevű új szakasz kerül a portálra. A Microsoft egyszerűsíti az Azure Marketplace-üzletágat azáltal, hogy világszerte kiszámíthatóbb árakat és gyűjteményeket engedélyez ügyfeleitől. Ez a racionalizálás magában foglalja azoknak a pénznemekszámnak a csökkentését, amelyekben az ügyfeleket számlázunk.  További információ: [Frissítés egy meglévő virtuális gép ajánlat az Azure Marketplace-en.](./cpp-update-existing-offer.md)


### <a name="additional-information-on-taxes-and-prices"></a>További információk az adókról és árakról

* A Microsoft egyes országokat/régiókat *adóelengedtési országként*osztályosít.  Ezekben az országokban/régiókban a Microsoft beszedi az adókat az ügyfelektől, majd megfizeti (utalja) az adókat a kormánynak.  Más országokban/régiókban a partnerek jellemzően azért felelősek, hogy beszedjék az ügyfeleiktől az adókat, és adót fizessenek a kormánynak. Ha úgy dönt, hogy az utóbbi országokban/régiókban értékesít, képesnek kell lennie a helyi adók kiszámítására és megfizetésére.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Az árak nem változékonyak, ha egy ajánlat éles. A támogatott régiókat azonban továbbra is hozzáadhatja vagy eltávolíthatja. 
* A Microsoft az ütemezett termékváltozat-díjakon felül díjat számít fel az ügyfél szabványos Azure-virtuális géphasználati díjaiért.
* Az árak minden régióra helyi pénznemben vannak beállítva a rendelkezésre álló valutaárfolyamokon az árak beállításakor.  <!-- TD: Meaning? - Offer created, published, other? -->
* Az egyes régiók árának külön-külön történő beállításához exportálja a díjtáblázatot, alkalmazzon egyéni árképzést, majd importáljon. 


## <a name="next-steps"></a>További lépések

Ha támogatja ezt a funkciót, megadhatja a Test Drive-adatokat; [Test Drive](./cpp-test-drive-tab.md) ellenkező esetben az ajánlathoz [marketplace-adatokat](./cpp-marketplace-tab.md) ad meg.
