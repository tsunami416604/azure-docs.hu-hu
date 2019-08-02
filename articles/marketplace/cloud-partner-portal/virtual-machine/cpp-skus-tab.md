---
title: Az Azure Marketplace Cloud Partner Portal virtuálisgép-SKU-i lapja
description: Az Azure Marketplace-en a virtuálisgép-ajánlat létrehozásához használt SKU-ket ismerteti.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 04/25/2019
ms.author: pabutler
ms.openlocfilehash: dceb82af73061b91676ffb7061c7495995e76667
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868705"
---
# <a name="virtual-machine-skus-tab"></a>Virtuális gépek SKU-i lapja

Az **új ajánlat** oldal **SKU** -i lapján létrehozhat egy vagy több SKU-t, és hozzárendelheti őket az új ajánlathoz.  A különböző SKU-ket a funkciók, a virtuálisgép-képtípusok, az átviteli sebesség vagy a méretezhetőség, a számlázási modellek vagy más jellemzők alapján lehet megkülönböztetni.


## <a name="create-a-sku"></a>SKU létrehozása

Kezdetben az új ajánlathoz nem tartoznak kapcsolódó SKU-ket, ezért az **új SKU**elemre kattintva létre kell hoznia egyet.

![Új SKU gomb a virtuális gépek új ajánlat lapján](./media/publishvm_005.png)

<br/>

Megjelenik az **új SKU** párbeszédpanel.  Adja meg az új SKU azonosítóját, majd kattintson az **OK**gombra. (Lásd alább az azonosító elnevezési konvenciókat.)  A **SKU** -i lapon ekkor megjelennek a szerkesztésre elérhető mezők.    A mező neve mezőben a hozzáfűzött csillag (*) érték azt jelzi, hogy szükség van rá.

<!-- TD: This tab has been updated, now has "Old Pricing" and "Simplified Currency Pricing" sections"! -->

![A virtuális gépek új ajánlat űrlapjának SKU lapja](./media/publishvm_006.png)

A következő táblázat a mezők célját, tartalmát és formázását ismerteti.  A kötelező mezőket csillag (*) alapján vádoljuk.

<!-- TD: I took a new screenshot, and the fields differ somewhat from description in the VM Pub Guide.  Needs review. -->

|  **Mező**       |     **Leírás**                                                          |
|  ---------       |     ---------------                                                          |
|  *SKU-beállítások*   |    |
| **SKU-AZONOSÍTÓ\***       | Az SKU azonosítója.  Ez a név legfeljebb 50 karakterből állhat, amely kisbetűs alfanumerikus karaktereket vagy kötőjelet (-) tartalmaz, de nem végződhet kötőjeltel.  Az ajánlat közzététele után nem módosítható.  |
|  *SKU részletei*   |  |
| **Cím\***        | A piactéren megjelenítendő ajánlat rövid neve. Legfeljebb 50 karakter hosszú lehet. |
| **Összegzése\***      | A piactéren megjelenítendő ajánlat tömör leírása. Legfeljebb 100 karakter hosszú lehet. |
| **Leírás\***  | Leírás szövege, amely részletesebb magyarázatot nyújt az ajánlatról.  <!-- TD: max len/guidance? 3k characters -->  |
| **SKU elrejtése\*** | Azt jelzi, hogy az SKU látható legyen-e a piactéren az ügyfeleknek.  Előfordulhat, hogy el szeretné rejteni az SKU-t, ha csak megoldási sablonokkal szeretné elérni, és nem külön vásárolja meg.  Hasznos lehet a kezdeti teszteléshez, illetve ideiglenes vagy szezonális ajánlatokhoz is. |
| **Felhőbeli rendelkezésre állás\*** | Meghatározza, hogy az SKU milyen felhők számára legyen elérhető.  Az alapértelmezett érték az Azure nyilvános verziója.  A Microsoft Azure Government az Egyesült Államok szövetségi, állami, helyi vagy törzsi kormányzati szerveinek, valamint azok tanúsított partnereinek szabályozott hozzáféréssel rendelkező kormányzati közösségi felhő.  A kormányzati felhővel kapcsolatos további információkért lásd: [Üdvözöljük a Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome). |
| **Ez egy privát SKU?\*** | Azt jelzi, hogy az SKU magán-vagy nyilvános. Az alapértelmezett érték **nem** (nyilvános).  További információ: [nyilvános és privát SKU](../../cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md)-i. |
| **Ország/régió elérhetősége\*** | Meghatározza, hogy az SKU mely országokban és régiókban lesz megvásárolható. Válasszon ki legalább egy régiót/országot. <!-- TD: Is this parameter an AMP visibility control or a contractual one, or both? --> |  
|  *Díjszabás*   |  |
| **Licencelési modell\***| A használni kívánt szabványosított számlázási modell.  Ha kijelöli a **használati alapú havi számlázású SKU**-t, a tangóharmonika szakaszban megadhatja, hogy a rendszer milyen részletes díjszabást adjon meg, és hogy szeretne-e ingyenes próbaidőszakot biztosítani.  Ez a szakasz azt is lehetővé teszi, hogy a díjszabási ütemtervet az Excel programba exportálja és importálja. További információ: [Számlázási lehetőségek az Azure Marketplace-](../../billing-options-azure-marketplace.md)en. | 
|  *VM-lemezképek*   |  |
| **Operációs rendszer családja\*** | Azt jelzi, hogy a megoldás virtuális gépe Windows-vagy Linux-alapú-e. |
| **Operációs rendszer típusának kiválasztása** | A megadott operációs rendszer adott gyártója vagy kiadása. |
| **Operációs rendszer rövid neve\*** | Az ügyfél számára megjelenítendő operációs rendszer neve.  |
| **Ajánlott virtuálisgép-méretek\*** | Egy szabványosított listából legfeljebb hat ajánlott virtuálisgép-méret kiválasztását teszi lehetővé.  A lista a Azure Portal és a Microsoft piactéren is elérhető.  A lista első virtuálisgép-mérete (az ügyfél-előfizetéshez, a régióhoz, a zónához stb.) alapértelmezett értékként van beállítva a potenciális ügyfél számára.  A felhasználó módosíthatja ezt a méretet a megoldás rendszerképével kompatibilisek között. | 
| **Portok megnyitása**| Az SKU támogatásához szükséges portok a megnyitáshoz és a protokollhoz.  Ezeknek a konfigurációknak meg kell egyezniük a megoldás virtuális gépe hálózatához konfigurált virtuális hálózattal. Ezek a beállítások a virtuális gépek üzembe helyezése során lépnek életbe. A portbeállítások azonban a SKU közzététele után módosíthatók. További információkért lásd: [portok megnyitása virtuális géphez a Azure Portal használatával](https://docs.microsoft.com/azure/virtual-machines/windows/nsg-quickstart-portal). <br/>A következő alapértelmezett hálózati hozzárendelések lesznek hozzáadva az összes virtuális géphez. &emsp;Windows 3389 – > 3389 TCP, 5986-> 5986 TCP; &emsp; Linux: 22 – > 22, TCP (SSH). |
| **Lemez verziója**  | Társított megoldás virtuális gép, amely a lemez verziószáma és a lemez URL-címe alapján van megadva. A lemez verziószámának szemantikai [](https://semver.org/) formátumúnak kell lennie: `<major>.<minor>.<patch>`.  Az URL-cím az operációs rendszer virtuális merevlemezéhez létrehozott közös hozzáférési aláírás URI-ja.  Bár az SKU-ban legfeljebb nyolc lemez adható hozzá, az Azure Marketplace-en csak az SKU legmagasabb lemezének verziószáma jelenik meg. A többi verzió csak API-kon keresztül jelenik meg.  <!--TD: Add more specific link to API --> <br/> Az **új adatlemez** -tangóharmonika szakasz lehetővé teszi, hogy legfeljebb 15 adatlemezt csatoljon a virtuális géphez.  Miután közzétett egy SKU-t egy adott VM-verzióval és a kapcsolódó adatlemezekkel, ez a konfiguráció nem módosítható.  Ha további virtuálisgép-verziók válnak elérhetővé az SKU-hoz, akkor ugyanazon számú adatlemezt is támogatnia kell. <br/> Ha nem hozta létre az Azure-alapú virtuálisgép-rendszerképeket, később is hozzáadhatja ezt a mezőt.  A társított virtuálisgép-erőforrás létrehozásával kapcsolatos információkért lásd a [virtuális gépek technikai eszközeinek létrehozása](./cpp-create-technical-assets.md)című szakaszt.  
|  |  |

<!-- TD: The CPP UX warning msg indicates that underscores are also supported in these SKU IDs. I suspect this might be true for other identifiers. --> 

<br/> A folyamat mentéséhez kattintson a **Save (Mentés** ) gombra. A következő lapon megadhatja, hogy az ajánlat támogatja-e a [Test Drive](./cpp-test-drive-tab.md)-t.


## <a name="additional-pricing-considerations"></a>További díjszabási szempontok

A fent ismertetett díjszabási modell egy alapszintű leírás.  A változások a helyi vagy regionális adószabályok és a Microsoft díjszabási szabályzatai által érintettek. 

### <a name="new-core-sizes-added-on-722019"></a>Új alapvető méretek hozzáadva a 7/2/2019

Az új Azure-beli virtuálisgép-méretek (a magok száma alapján) új díjszabást kapott a VM-közzétevők 2019. július 2-án.  Az új árak a következő alapméretekre vonatkoznak: 10, 44, 48, 60, 120, 208 és 416.  A meglévő virtuális gépek esetében a magok méretének új díjait a jelenlegi árak alapján automatikusan kiszámítjuk.  A kiadók 2019 augusztus 1-ig megtekintik a további árakat, és elvégzik a kívánt módosításokat.  Ezt a dátumot követően, ha a közzétevő még nem tette közzé újra, az új alapvető méretek automatikusan kiszámított árai érvénybe lépnek.


### <a name="simplified-currency-pricing"></a>Egyszerűsített pénznemek díjszabása

Szeptember 1 2018-ától kezdődően a portálon megjelenik egy **egyszerűsített valuta díjszabását** tartalmazó új szakasz. A Microsoft egyszerűsíti az Azure Marketplace üzletágat azáltal, hogy lehetővé teszi, hogy több kiszámítható díjszabást és gyűjteményt biztosítson ügyfeleinek szerte a világon. Ez a egyszerűsítés magában foglalja a pénznemek számának csökkentését, amelyekben ügyfeleinket számlázjuk.  További információ: meglévő virtuálisgép- [ajánlat frissítése az Azure Marketplace](./cpp-update-existing-offer.md)-en.


### <a name="additional-information-on-taxes-and-prices"></a>További információk az adókról és az árakról

* A Microsoft egyes országokat és régiókat osztályozni kell az *elhalasztott országokban*.  Ezekben az országokban/régiókban a Microsoft adót gyűjt az ügyfelektől, majd a kormányzatnak fizeti az adókat.  Más országokban/régiókban a partnerek általában a felelősek az ügyfelek adójának beszedéséhez, és adót fizetnek a kormánynak. Ha úgy dönt, hogy az utóbbi országokban/régiókban értékesít, a helyi adókat kell kiszámítani és fizetnie.  <!-- TD: Find a good reference on taxing policies. The best I found was in the UWP section: https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps -->
* Az árak nem változnak, ha egy ajánlat élőben üzemel. Azonban továbbra is hozzáadhat vagy eltávolíthat támogatott régiókat. 
* A Microsoft az ütemezett SKU-díjak mellett a standard szintű Azure-beli virtuális gépek használati díját is kiszámlázza.
* Az árakat a helyi pénznemben lévő összes régióra vonatkozóan a díjszabás beállításakor kell megadni.  <!-- TD: Meaning? - Offer created, published, other? -->
* Ha minden egyes régió árát egyenként szeretné megadni, exportálja a díjszabási táblázatot, alkalmazza az egyéni díjszabást, majd importálja a következőt:. 


## <a name="next-steps"></a>További lépések

Ha támogatja ezt a funkciót, megadhatja a [tesztelési meghajtó](./cpp-test-drive-tab.md) adatait is. Ellenkező esetben a [piactér](./cpp-marketplace-tab.md) adatait adja meg az ajánlatához.
