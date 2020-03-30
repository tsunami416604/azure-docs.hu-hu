---
title: Virtuálisgép-piactér lap az Azure Piactér felhőpartneri portálján
description: Ismerteti az Azure Marketplace virtuális gép ajánlatának létrehozásához használt Piactér lapon.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 567dc83e36020ef9af328f52dae89ef37b6e4908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277721"
---
# <a name="virtual-machine-marketplace-tab"></a>Virtuálisgép Piactér lap

Az **Új ajánlat** lap **Piactér** lapja lehetővé teszi, hogy a leendő ügyfelek számára marketinget, értékesítést és jogi információkat és megállapodásokat biztosítson, és kezelje a piactérről generált érdeklődőket. Ez a hosszú forma négy részből áll: **Áttekintés**, **Marketing leletek**, **Ólommenedzsment**és **Jogi**.


## <a name="overview-section"></a>Áttekintő szakasz
Ebben a szakaszban adja meg az Azure Piactér-ajánlat általános adatait.  A mezőnévhez csatolt csillag (*) azt jelzi, hogy szükség van rá.

![A Virtuális gépek Piactér lapjának áttekintése szakasza](./media/publishvm_008.png)

Az alábbi táblázat a mezők célját és tartalmát ismerteti. A kötelező mezőket csillag (*) vádolja.

|  **Mező**                |     **Leírás**                                                          |
|  ---------                |     ---------------                                                          |
| **Cím\***                 | Az ajánlat címe, gyakran a hosszú, hivatalos név. Ez a cím jól láthatóan jelenik meg a piacon.  Legfeljebb 50 karakter hosszú. |
| **Összefoglaló\***               | A megoldás rövid célja vagy funkciója.  Legfeljebb 100 karakter hosszú. |
| **Hosszú összegzés\***          | A megoldás célja vagy funkciója.  Legfeljebb 256 karakter hosszú. |
| **Leírás\***           | A megoldás leírása.  A maximális hossza 3000 karakter, támogatja az egyszerű HTML-formázást. |
| **Microsoft CSP viszonteladói csatorna\*** | A felhőszolgáltatók (CSP) partnercsatorna-opt-in már elérhető.  Az ajánlat microsoftos CSP-partnercsatornákon keresztül történő marketingről további információt a [felhőszolgáltatók](../../cloud-solution-providers.md) ban talál. |
| **Marketing azonosító\***  | Az ajánlathoz társítani rakandó egyedi URL-cím általában tartalmazza a szervezet és a megoldás nevét, legfeljebb 50 karakter hosszú.  Példa: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Előfizetés-azonosítók előzetes verzió\*** | Adjon hozzá egy-100 előfizetés-azonosítót az előzetesekhez. Ezek a fehérlistán szereplő előfizetések hozzáférhetnek az ajánlathoz, miután közzétették, mielőtt élesben lépne. |
| **Hasznos linkek**          | Url-címeket adhat a dokumentációkhoz, a kibocsátási megjegyzésekhez, a gyakori kérdésekhez és így tovább. |
| **Javasolt kategóriák\*** | Legfeljebb két (2) kategóriát választhat ki, beleértve az elsődleges és a másodlagos kategóriát (nem kötelező). Minden elsődleges és/vagy másodlagos kategóriához legfeljebb két (2) alkategóriát választhat ki. Ha nincs kiválasztva alkategória, akkor az ajánlat csak a kiválasztott kategóriában lesz felderíthető. |
|  |  |


## <a name="marketing-artifacts-section"></a>Marketingösszetevők szakasz

Ez a második rész három alrészből áll: **Emblémák**, **Képernyőkép**és **Videók**. Logók az egyetlen szükséges marketing leletek, de minden erősen ajánlott a legjobb ügyfél fellebbezést. 

![A Piactér lap Marketingösszetevők szakasza a virtuális gépek új ajánlatűrlapján](./media/publishvm_009.png)

Az alábbi táblázat a mezők célját és tartalmát ismerteti. A kötelező mezőket csillag (*) vádolja.

|  **Mező**                |     **Leírás**                                                          |
|  ---------                |     ---------------                                                          |
| *Logók*  |  |
| **Kicsi\***                 | 40x40 pixeles .ico bitkép                                                      |
| **Közepes\***                | 90x90 pixeles .ico bitkép                                                      |
| **Nagy\***                 | 115x115 pixeles .ico bitkép                                                   |
| **Széles\***                  | 255x115 pixeles .ico bitkép                                                    |
| **Hős**                  | 815x290 bitkép.  Nem kötelező, azonban a feltöltés után a hős ikon nem törölhető. |
| *Képernyőképek*  | Nem kötelező, de termékváltozatonként legfeljebb öt képernyőkép. |
| **Név**                  | Név vagy cím <!-- TODO - max char length? none specified in UI -->                               |
| **Kép**                 | Képernyőfelvétel kép, 533x324 képpont                                         |
| *Videók*  |  |
| **Név**                  | Név vagy cím  <!-- TODO - max char length? -->                              |
| **Hivatkozás**                  | Videó URL, házigazdája a YouTube-on vagy Vimeo                                        |
| **Miniatűr**             | 533x324 bites kép                                                               |
|   |   |

### <a name="logo-guidelines"></a>Emblémával kapcsolatos irányelvek

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

A Cloud Partner Portálra feltöltött összes emblémának követnie kell az alábbi irányelveket:

*  Az Azure arculata egyszerű színpalettát használ. Alacsonyan tartsa az embléma elsődleges és másodlagos színeinek számát.
*  Az Azure Portal témaszínei fehérek és feketék. Ezért ne használja ezeket a színeket, mint a háttér színe a logók. Használjon olyan színt, amely az emblémákat kiemelkedővé tenné az Azure Portalon. Javasoljuk az egyszerű alapszínek használatát. Ha átlátszó hátteret használ, győződjön meg arról, hogy az emblémák/szövegek nem fehérek, nem feketék vagy kékek.
*  Ne használjon színátmenetes hátteret az emblémán.
*  Ne helyezzen szöveget – még a vállalatát vagy a márkanevét is – az emblémára. A logó megjelenésének és érzetének "laposnak" kell lennie, és kerülnie kell a színátmeneteket.
*  Ne nyújtsa ki a logót.

#### <a name="hero-logo"></a>Főképembléma

A Hero embléma nem kötelező; a feltöltés után azonban a hős ikonja nem törölhető.  A Hero embléma ikonnak az alábbi irányelveket kell követnie:

*  A hősikonok nem feltétlenül szerepelnek fekete, fehér és átlátszó hátterek.
*  Ne használjon semmilyen világos színt a hős ikonjának háttereként.  A Publisher megjelenítendő neve, a terv címe és az ajánlat hosszú összegzése fehér betűszínnel jelenik meg, és a háttérben kell kitűnnie.
*  A legtöbb szöveg et ne használja a hősembléma tervezése közben.  A közzétevő neve, a terv címe, az ajánlat hosszú összegzése és a létrehozás gomb programozott módon van beágyazva a hős ikonba, amikor az ajánlat felsorolja. 
* A hős ikonjobb oldalán egy 415x100 képpont méretű, balról eltolású, 370 px-es eltolású téglalapot is felkell tüntetni.  

Például a következő hős ikon az Azure Container Service.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Példa az Azure Container Service hősikonjára](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Példa marketinginformációkra 

Az alábbi kép bemutatja, hogyan jelennek meg a marketingadatok a Microsoft Windows Server fő terméklapján.

![Példa terméklap a Microsoft Windows Server rendszerhez](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Érdeklődőkezelés szakasz

A harmadik szakasz lehetővé teszi, hogy összegyűjtse az Azure Marketplace-ajánlatokból generált ügyfelek érdeklődőket. A következő tárolási lehetőségeket kínálja (legördülő listából) az érdeklődők adatait.

* **Nincs** – az alapértelmezett érdeklődőadatok gyűjtése nem történik meg.
* Azure Table – egy kapcsolati karakterlánc által meghatározott Azure-táblába írva.
* Dynamics CRM Online – a [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) példányba írva, url-cím és hitelesítési hitelesítő adatok által megadva.
* HTTPS-végpont – json-tartalomként a megadott HTTPS-végpontra íródott.
* Marketo - a megadott [Marketo](https://www.marketo.com/) példányra írva, amelyet a kiszolgálóazonosító, a munchkin-azonosító és az űrlapazonosító határoz meg.
* Salesforce – egy objektumazonosítóval megadott [Salesforce-adatbázisba](https://www.salesforce.com/) írva.

Az ajánlat sikeres közzététele után a rendszer érvényesíti az érdeklődőkapcsolatot, és automatikusan elküldi a tesztérdeklődőt a beállított célhelyre. Az érdeklődőadatokat folyamatosan kell kezelni, és ezeket a beállításokat azonnal frissíteni kell, amikor az ügyfélkezelési architektúrán módosításokat hajt végre.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Jogi szakasz

Ez az utolsó szakasz lehetővé teszi, hogy az egyes ajánlathoz szükséges jogi dokumentumokat biztosítsa.  

|  **Mező**                    |     **Leírás**                                        |
|  ---------                    |     ---------------                                        |
| **Adatvédelmi irányelvek URL-címe\***      | A közzétett adatvédelmi irányelvek URL-címe                          |
| **Használja a normál szerződést?\***  |   |
| **Használati feltételek\***            | egyszerű szövegként vagy egyszerű HTML-kódként.                       |
|  |  |


## <a name="next-steps"></a>További lépések

A következő [Támogatás](./cpp-support-tab.md) lapon technikai és felhasználói támogatási erőforrásokat biztosít az ajánlathoz.
