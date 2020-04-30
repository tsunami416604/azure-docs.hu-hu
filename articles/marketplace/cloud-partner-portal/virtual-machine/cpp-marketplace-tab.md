---
title: A virtuális gép Marketplace lapja az Azure Marketplace Cloud Partner Portal
description: Ismerteti az Azure Marketplace virtuálisgép-ajánlat létrehozásához használt piactér lapot.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: dsindona
ms.openlocfilehash: 2c3d316d0d2810cb2a25ffd3bc4e34cf3454c10d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146860"
---
# <a name="virtual-machine-marketplace-tab"></a>A virtuális gép Marketplace lapja

> [!IMPORTANT]
> 2020. április 13-ától kezdődően megkezdjük az Azure-beli virtuálisgép-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Kövesse az Azure-beli [virtuális gép létrehozása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) az áttelepített ajánlatok kezeléséhez című témakör utasításait.

Az **új ajánlat** oldal **piactér** lapján megadhatja a potenciális ügyfeleket marketing-, értékesítési és jogi információkkal és szerződésekkel, és kezelheti a piactéren generált érdeklődőket. Ez a hosszú űrlap négy szakaszra oszlik: **Áttekintés**, **marketing**-összetevők, **vezető felügyelet**és **jogi**.


## <a name="overview-section"></a>Áttekintő szakasz
Ebben a szakaszban az Azure Marketplace-ajánlat általános adatait adja meg.  A mező neve mezőben a hozzáfűzött csillag (*) érték azt jelzi, hogy szükség van rá.

![A virtuális gépek Marketplace lapjának áttekintés szakasza](./media/publishvm_008.png)

A következő táblázat a mezők célját és tartalmát ismerteti. A kötelező mezőket csillag (*) alapján vádoljuk.

|  **Mező**                |     **Leírás**                                                          |
|  ---------                |     ---------------                                                          |
| **Cím\***                 | Az ajánlat címe, gyakran a hosszú, formális név. Ez a cím kiemelten jelenik meg a piactéren.  Legfeljebb 50 karakter hosszú lehet. |
| **Összefoglalás\***               | A megoldás rövid célja vagy funkciója.  Legfeljebb 100 karakter hosszú lehet. |
| **Hosszú összefoglalás\***          | A megoldás célja vagy funkciója.  Legfeljebb 256 karakter hosszú lehet. |
| **Leírás\***           | A megoldás leírása.  Legfeljebb 3000 karakter hosszú lehet, és támogatja az egyszerű HTML-formázást. |
| **Microsoft CSP viszonteladói csatorna\*** | A Cloud Solution Providers (CSP) Partner Channel opt-in mostantól elérhető.  Az ajánlat Microsoft CSP-partneri csatornákon keresztüli forgalmazásával kapcsolatos további információkért tekintse meg a [Cloud Solution Providers](../../cloud-solution-providers.md) című témakört. |
| **Marketing azonosítója\***  | Az ajánlathoz társítandó egyedi URL-cím, amely jellemzően a szervezet és a megoldás nevét, a maximális hossz 50 karaktert tartalmazza.  Például: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Előfizetési azonosítók előnézete\*** | Adja hozzá az előnézetek egy 100 előfizetés-azonosítóját. Ezek a felsorolt előfizetések a közzétételük előtt hozzáférhetnek az ajánlathoz, mielőtt a szolgáltatás elérhetővé válik. |
| **Hasznos hivatkozások**          | URL-címeket adhat hozzá a dokumentumokhoz, a kibocsátási megjegyzésekhez, a GYIK-hoz stb. |
| **Javasolt kategóriák\*** | Válasszon legfeljebb két (2) kategóriát, beleértve az elsődleges és a másodlagos kategóriát is (opcionális). Válasszon legfeljebb két (2) alkategóriát az egyes elsődleges és/vagy másodlagos kategóriákhoz. Ha nincs kiválasztva alkategória, akkor az ajánlat csak a kiválasztott kategóriában lesz felderíthető. |
|  |  |


## <a name="marketing-artifacts-section"></a>Marketing-összetevők szakasz

Ez a második szakasz három alszakaszra oszlik: **logók**, **képernyőképek**és **videók**. A logók az egyetlen szükséges marketing-összetevők, azonban a legjobb ügyfelek számára ajánlott. 

![Marketing-összetevők szakasz a piactér lap új ajánlat űrlapján a virtuális gépekhez](./media/publishvm_009.png)

A következő táblázat a mezők célját és tartalmát ismerteti. A kötelező mezőket csillag (*) alapján vádoljuk.

|  **Mező**                |     **Leírás**                                                          |
|  ---------                |     ---------------                                                          |
| *Logók*  |  |
| **Kicsi\***                 | 40x40cm képpont. ico bitkép                                                      |
| **Medium\***                | 90x90 képpont. ico bitkép                                                      |
| **Nagy\***                 | 115x115 képpont. ico bitkép                                                   |
| **Széles\***                  | 255x115 képpont. ico bitkép                                                    |
| **Hero**                  | 815x290 bitkép.  Nem kötelező, de feltöltés után a Hero ikon nem törölhető. |
| *Képernyőképek*  | Nem kötelező, de SKU-onként legfeljebb öt képernyőkép. |
| **Név**                  | Név vagy cím <!-- TODO - max char length? none specified in UI -->                               |
| **Kép**                 | Képernyőfelvétel, 533x324 képpont                                         |
| *Videók*  |  |
| **Név**                  | Név vagy cím  <!-- TODO - max char length? -->                              |
| **Hivatkozás**                  | Videó URL-címe, amelyet a YouTube vagy a Vimeo tárol                                        |
| **Miniatűr**             | 533x324 bitkép                                                               |
|   |   |

### <a name="logo-guidelines"></a>Emblémával kapcsolatos irányelvek

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

A Cloud Partner Portal feltöltött összes emblémának a következő irányelveket kell követnie:

*  Az Azure arculata egyszerű színpalettát használ. Tartsa meg az elsődleges és a másodlagos színek számát a logón.
*  A Azure Portal téma színei fehérek és feketeok. Ezért ne használja ezeket a színeket a logók háttérszínéhez. Használjon olyan színt, amely a Azure Portalban Kiemelt emblémát tesz elérhetővé. Javasoljuk az egyszerű alapszínek használatát. Ha transzparens hátteret használ, ügyeljen arra, hogy a logók/szöveg ne legyen fehér vagy fekete vagy kék.
*  Ne használjon színátmenetes hátteret az emblémán.
*  A logón ne helyezzen el szöveget – még a vállalat vagy a márka nevét is –. Az embléma megjelenésének és működésének "Flat" típusúnak kell lennie, és el kell kerülnie a színátmeneteket.
*  Ne nyújtsa az emblémát.

#### <a name="hero-logo"></a>Főképembléma

A Hero embléma nem kötelező; feltöltés után azonban a Hero ikon nem törölhető.  A Hero embléma ikonjának a következő irányelveket kell követnie:

*  A fekete, fehér és transzparens hátterek nem engedélyezettek a Hero-ikonokhoz.
*  Ne használjon világos színeket a Hero ikon hátterének.  A közzétevő megjelenítendő neve, a terv címe és az ajánlat hosszú összegzése fehér színű betűtípussal jelenik meg, és a háttérben kell állnia.
*  Kerülje a legtöbb szöveg használatát a Hero embléma tervezésekor.  A közzétevő neve, a csomag címe, az ajánlat hosszú összegzése és a létrehozás gomb programozott módon van beágyazva a Hero ikonon belül az ajánlatok listájának megjelenítésekor. 
* Vegyen fel egy fel nem használt téglalapot a hős ikon jobb oldalán, a méret 415x100 pixel és az eltolás 370 px a bal oldalon.  

A következő hős ikon például a Azure Container Service.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Példa a Azure Container Service hős ikonjára](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Marketing információ – példa 

Az alábbi képen látható, hogyan jelennek meg a marketing adatai a Microsoft Windows Server fő termék lapján.

![A Microsoft Windows Server termékének példája](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Érdeklődői felügyelet szakasz

A harmadik szakasz lehetővé teszi, hogy összegyűjtse az Azure Marketplace-ajánlatokból előállított ügyfeleket. A következő tárolási lehetőségeket kínálja (legördülő listából) ehhez az érdeklődői információhoz.

* **Nincs** – az alapértékek nem lesznek begyűjtve.
* Azure Table – a kapcsolódási karakterlánc által megadott Azure-táblába írva.
* Dynamics CRM Online – az URL-cím és a hitelesítő adatok által megadott [Microsoft Dynamics 365 online](https://dynamics.microsoft.com/) példányra íródott.
* HTTPS-végpont – a megadott HTTPS-végpontba írt JSON-adattartalomként.
* Marketo – a megadott [Marketo](https://www.marketo.com/) -példányra íródik, amely a kiszolgáló azonosítója, a Munchkin azonosítója és az űrlap azonosítója alapján van megadva.
* Salesforce – egy objektumazonosító által megadott [Salesforce](https://www.salesforce.com/) -adatbázisba írt.

Az ajánlat sikeres közzététele után a rendszer érvényesíti az érdeklődői kapcsolatokat, és automatikusan elvégzi a tesztelési érdeklődők küldését a konfigurált célhelyre. Az érdeklődői adatokat folyamatosan kell felügyelni, és ezeket a beállításokat azonnal frissíteni kell, amikor módosításokat végeznek az ügyfél-felügyeleti architektúrán.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Jogi szakasz

Ez az utolsó szakasz lehetővé teszi az egyes ajánlatokhoz szükséges jogi dokumentumok megadását.  

|  **Mező**                    |     **Leírás**                                        |
|  ---------                    |     ---------------                                        |
| **Adatvédelmi szabályzat URL-címe\***      | A közzétett adatvédelmi szabályzat URL-címe                          |
| **Standard szintű szerződést használ?\***  |   |
| **Használati feltételek\***            | házirend egyszerű szövegként vagy egyszerű HTML-ként.                       |
|  |  |


## <a name="next-steps"></a>További lépések

A következő [támogatás](./cpp-support-tab.md) lapon technikai és felhasználói támogatási forrásokat fog biztosítani az ajánlatához.
