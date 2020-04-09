---
title: Az Azure IoT Edge-modul Marketplace-jének leírása | Azure Piactér
description: Hozzon létre egy IoT Edge-modul piactéri leírását.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: df51a408cc47908a240ceb66bf510fe3df3ce87f
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983398"
---
# <a name="iot-edge-module-marketplace-tab"></a>IoT Edge modul piactér lapja

>[!Important]
>2020. április 13-tól megkezdjük az IoT Edge modulajánlatok kezelését a Partner Centerbe. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Kövesse az [IoT Edge-modulajánlat létrehozása](https://aka.ms/AzureCreateIoT) az áttelepített ajánlatok kezeléséhez kövesse az utasításokat.

Az **Új ajánlat** lap **Piactér** lapja lehetővé teszi, hogy a leendő ügyfelek számára marketinget, értékesítést és jogi információkat és megállapodásokat biztosítson, és kezelje a piactérről generált érdeklődőket. Ez a hosszú forma négy részből áll: **Áttekintés**, **Marketing leletek**, **Ólommenedzsment**és **Jogi**.


## <a name="overview"></a>Áttekintés

Ebben a szakaszban adja meg az Azure Piactér-ajánlat általános adatait.  A mezőnévhez csatolt csillag (*) azt jelzi, hogy szükség van rá.

![Az IoT Edge-modulok új ajánlatűrlapjának Piactér lapjának áttekintése szakasza](./media/iot-edge-module-marketplace-tab-overview.png)

Az alábbi táblázat a mezők célját és tartalmát ismerteti. A kötelező mezőket csillag (*) vádolja.

|  **Mező**                |     **Leírás**                                                          |
|  ---------                |     ---------------                                                          |
| **Cím\***                 | Az ajánlat címe. Ez lesz látható jól látható a piacon.  Legfeljebb 50 karakter hosszú. <!--ADD PICTURE IN ACTION-->|
| **Összefoglaló\***               | Az ajánlat rövid összefoglalása. Legfeljebb 100 karakter hosszú. <!--ADD PICTURE IN ACTION-->|
| **Hosszú összegzés\***          | Hosszabb összefoglaló az ajánlat (bár lehet ugyanaz, mint az **összefoglaló**).  Legfeljebb 256 karakter hosszú. <!--ADD PICTURE IN ACTION-->|
| **Leírás\***           | Az ajánlat leírása.  A maximális hossza 3000 karakter, támogatja az egyszerű HTML-formázást.<br/> Alján tartalmaznia kell egy *minimális hardverkövetelmény-bekezdést.* például: <br/> <p><u>Minimális hardverkövetelmények:</u> Linux x64 és arm32 OS, 1 GB RAM, 500 Mb tárhely</p>
| **Marketing azonosító\***  | Az ajánlathoz társítani rakandó egyedi URL-cím általában tartalmazza a szervezet és a megoldás nevét, legfeljebb 50 karakter hosszú.  Például: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleIoTEdgeModule`  |
| **Előfizetés-azonosítók előzetes verzió** | Adjon hozzá egy-100 előfizetés-azonosítót az előzetesekhez. Ezek a fehérlistán szereplő előfizetések hozzáférhetnek az ajánlathoz, miután közzétették, mielőtt élesben lépne. |
| **Hasznos linkek**          | Az ajánlatban szereplő üzleti és műszaki kategóriák többszörös kiválasztása a legjobban társítható.  Legfeljebb 10 megengedett. Győződjön meg arról, hogy legalább egy hivatkozást a dokumentációhoz, és egy linket a kompatibilis IoT Edge-eszközök az [Azure IoT eszközkatalógus.](https://catalog.azureiotsolutions.com/) |
| **Javasolt kategóriák\*** | Vedd fel öt kategóriában. Ezek megjelennek a termék adatok oldalán. A tallózóoldalakon az összes IoT Edge-modul megjelenik az * \> IoT Edge-modul internetes* modulkategóriában.|
|  |  |


### <a name="offer-example"></a>Példa ajánlat

 Az alábbi példák bemutatják, hogyan jelenik meg az ajánlat **cím,** **összegzés,** **leírás**, **embléma**és **képernyőkép** mező különböző nézetekben.

 
#### <a name="on-the-azure-marketplace-website"></a>Az Azure Marketplace webhelyén:

- Böngészés közben ajánlatok:

    ![Hogyan jelennek meg a marketplace-ajánlatok az Azure Marketplace webhelyén – tallózás](./media/iot-edge-module-ampdotcom-card.png)

- Ha megnézzük ajánlat részleteit:

    ![Hogyan jelenik meg az IoT Edge modul, amikor a termék részleteit a webhelyen keresi?](./media/iot-edge-module-ampdotcom-pdp.png)


#### <a name="on-the-azure-portal-website"></a>Az Azure Portal webhelyén:

- Böngészés közben ajánlatok:

    ![Hogyan jelenik meg az IoT Edge-modul az Azure Portal on-#1](./media/iot-edge-module-portal-browse.png)

    ![Hogyan jelenik meg az IoT Edge-modul az Azure Portal on-#2](./media/iot-edge-module-portal-product-picker.png)

- Ajánlat keresésekor:

    ![Hogyan jelenik meg az IoT Edge-modul az Azure Portalon való kereséskor?](./media/iot-edge-module-portal-search.png)

- Ha megnézzük ajánlat részleteit:

    ![Hogyan jelenik meg az IoT Edge modul, amikor a termék részleteit a portálon keresi](./media/iot-edge-module-portal-pdp.png)


## <a name="marketing-artifacts"></a>Marketing összetevők

Ez a szakasz a következő alszakaszokat ismerteti: **Emblémák**, **Képernyőkép**és **Videók.** 

>[!Note]
>Logók az egyetlen szükséges marketing leletek, de minden erősen ajánlott a legjobb ügyfél fellebbezést.

![A Piactér lap Marketingösszetevők szakasza a virtuális gépek új ajánlatűrlapján](./media/publishvm_009.png)

|  **Mező**                |     **Leírás**                                                          |
|  ---------                |     ---------------                                                          |
| *Logók*  | Az előző képernyőfelvételből megtudhatja, hogyan és hol fogja használni az emblémákat.  |
| **Kicsi\***                 | 40x40 pixeles PNG formátum                                                     |
| **Medium\***                | 90x90 pixeles PNG formátum                                                     |
| **Nagy\***                 | 115x115 pixeles PNG formátum                                                  |
| **Széles\***                  | 255x115 pixeles PNG formátum                                                   |
| **Hős**                  | 815x290 pixelPNG formátumban.  Nem kötelező, azonban a feltöltés után a hős ikon nem törölhető. |
| *Képernyőképek*  | A képernyőképek a termék adatai lapon jelennek meg. Ezek egy jó módja annak, hogy vizuálisan kommunikálni, amit az IoT Edge modul nem, és hogyan működik. Megjelenítheti az architektúradiagramokat, vagy használhat például esetillusztrációkat. Nem kötelező, de termékváltozatonként legfeljebb öt képernyőkép. |
| **Név**                  | Név vagy cím. Legfeljebb 100 karakter hosszú.                             |
| **Kép**                 | Képernyőelfog kép, 533x324 pixelpng formátum                               |
| *Videók*  | A videók a termék adatai oldalon jelennek meg. Ezek egy jó módja annak, hogy vizuálisan kommunikálni, amit az IoT Edge modul nem, és hogyan működik. |
| **Név**                  | Név vagy cím. Legfeljebb 100 karakter hosszú.                             |
| **Hivatkozás**                  | Videó URL, házigazdája a YouTube-on vagy Vimeo                                        |
| **Miniatűr**             | 533x324 pixeles PNG formátum                                                     |
|  |  |


### <a name="logo-guidelines"></a>Emblémával kapcsolatos irányelvek

<!-- It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

A Cloud Partner Portálra feltöltött összes emblémának követnie kell az alábbi irányelveket:

*  Az Azure arculata egyszerű színpalettát használ. Alacsonyan tartsa az embléma elsődleges és másodlagos színeinek számát.
*  Az Azure Portal színszínei fehérek és feketék. Ne használja ezeket a színeket az emblémák háttérszíneként. Használjon olyan színt, amely az emblémákat kiemelkedővé teszi az Azure Portalon. Javasoljuk az egyszerű alapszínek használatát. Ha átlátszó hátteret használ, győződjön meg arról, hogy az emblémák/szövegek nem fehérek, feketék vagy kékek.
*  Ne használjon színátmenetes hátteret az emblémán.
*  Ne helyezzen szöveget – még a vállalatát vagy a márkanevét is – az emblémára. A logó megjelenésének és érzetének "laposnak" kell lennie, és kerülnie kell a színátmeneteket.
*  Ne nyújtsd ki a logót.


#### <a name="hero-logo"></a>Főképembléma

A Hero embléma nem kötelező.

>[!Important]
>A Hero embléma feltöltése után nem törölhető.

A Hero embléma alábbi irányelveit használja: 

*  Fekete, fehér és átlátszó hátterek nem engedélyezettek.
*  Ne használjon semmilyen világos színt az embléma háttereként.  A közzétevő megjelenítendő neve, a terv címe és az ajánlat hosszú összegzése fehér betűszínnel jelenik meg, és a háttérben kell kitűnnie.
*  Ne használja a legtöbb szöveget az embléma tervezésekor. A közzétevő neve, a terv címe, az ajánlat hosszú összegzése és a létrehozás gomb programozott módon vannak beágyazva az emblémába, amikor az ajánlat szerepel. 
* A hősembléma jobb oldalán egy nem használt téglalap alakú szóközt is fel kell tüntetni. Ez az üres terület 415x100 képpont, balról 370 képponttal eltolt.  

<!-- P2: would be nice to also have an picture int he context of iot edge of the hero image
As an example, the following hero icon is for the Azure Container Service.

![Example hero icon for Azure Container Service](./media/publishvm_010.png)
-->


## <a name="lead-management"></a>Érdeklődők kezelése

Ez a szakasz lehetővé teszi, hogy beállítsa az Azure Marketplace-ajánlatokból generált ügyfélérdeklődők gyűjtésére vonatkozó beállításokat. A legördülő listából a következő tárolási lehetőségek közül választhat.

* **Nincs** – az alapértelmezett érdeklődőadatok gyűjtése nem történik meg.
* Azure Table – egy kapcsolati karakterlánc által meghatározott Azure-táblába írva.
* Dynamics CRM Online – a [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) példányba írva, url-cím és hitelesítési hitelesítő adatok által megadva.
* HTTPS-végpont – json-tartalomként a megadott HTTPS-végpontra íródott.
* Marketo - a megadott [Marketo](https://www.marketo.com/) példányra írva, amelyet a kiszolgálóazonosító, a munchkin-azonosító és az űrlapazonosító határoz meg.
* Salesforce – egy objektumazonosítóval megadott [Salesforce-adatbázisba](https://www.salesforce.com/) írva.

Az ajánlat sikeres közzététele után a rendszer ellenőrzi az érdeklődőkapcsolatot, és automatikusan elküldi a tesztérdeklődőt a beállított célhelyre. 

>[!Note]
>Az érdeklődőadatokat folyamatosan kell kezelni, és ezeket a beállításokat azonnal frissíteni kell, amikor az ügyfélkezelési architektúrán módosításokat hajt végre.

<!-- there is missing some marketing imagess
1. inside azure portal the wide logo/regular logo
2. inside azure portal the top curation section
3. amp.com the pricing tab
4. amp.com the tile -->


## <a name="legal"></a>Jogi tudnivalók

Ebben a részben megadhatja az egyes ajánlatokhoz szükséges két jogi dokumentumot: adatvédelmi szabályzatot és a használati feltételeket.

|  **Mező**                    |     **Leírás**                                                          |
|  ---------                    |     ---------------                                                          |
| **Adatvédelmi irányelvek URL-címe\***      | A közzétett adatvédelmi irányelvek URL-címe                                            |
| **Szabványos légkondi használata\***  | A Microsoft szabványos szerződéssablonjának használata.  További információ: [Általános szerződés](https://docs.microsoft.com/azure/marketplace/standard-contract).   |
| **Használati feltételek\***            | *Használati feltételek,* mint inline egyszerű HTML vagy linket a kiküldött használati feltételek oldalon     |
|  |  |


## <a name="next-steps"></a>További lépések

A [Támogatás](./cpp-support-tab.md) lapon technikai és felhasználói támogatási erőforrásokat biztosíthat az ajánlathoz.
