---
title: A Cloud Partner portálra, az Azure piactér lapján a virtuális gép |} A Microsoft Docs
description: A piactér lapján az Azure piactér-beli Virtuálisgép-ajánlat létrehozásakor használt ismerteti.
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
ms.openlocfilehash: b1b62c68ef4e18f4d4d36a78078ad7431717b754
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995651"
---
# <a name="virtual-machine-marketplace-tab"></a>Virtuális gép Marketplace lap

A **Marketplace** lapján a **új ajánlat** lap lehetővé teszi, hogy adja meg a leendő ügyfelek-szerződések marketinges, értékesítési és jogi információk és származó érdeklődők kezeléséhez a Marketplace-en. A hosszú űrlap négy szakaszokból áll: **Áttekintés**, **Marketing-összetevők**, **felügyeleti vezethet**, és **jogi**. 

## <a name="overview-section"></a>A szakasz áttekintése
Ebben a szakaszban, az Azure Marketplace-ajánlat kapcsolatos általános információkat adja meg.  A hozzáfűzött csillag (*) az a mező nevét jelzi, hogy szükséges.

![A piactér lapján a virtuális gépek az új ajánlat űrlap szakaszában – áttekintés](./media/publishvm_008.png)

A következő táblázat ismerteti a cél és ezek a mezők tartalmát.

|  **Mező**                |     **Leírás**                                                          |
|  ---------                |     ---------------                                                          |
| **Cím**                 | Az ajánlat, gyakran hosszabb, formális neve címe. Ezzel a beállítással hangsúlyosan a cím fog megjelenni a Marketplace-en.  Legfeljebb 50 karakter hosszúságú. |
| **összegzés**               | Rövid célját vagy függvény megoldás.  A hosszabb 100 karakternél. |
| **Hosszú összegzése**          | Cél vagy függvény megoldás.  Legfeljebb 256 karakter hosszúságú. |
| **Leírás**           | Megoldás leírása.  Legfeljebb 3000 karakter hosszúságú támogatja az egyszerű HTML-formázás. |
| **A Microsoft CSP viszonteladói csatornát** | Cloud Solution Providers (CSP) partner csatorna vehetnek részt már elérhető.  Lásd: [Cloud Solution Providers](../../cloud-solution-providers.md) további tájékoztatást a marketing, az ajánlat keretében a Microsoft CSP partner-csatornákon. |
| **Marketing-azonosító**  | Egyedi URL-címet rendelni ehhez az ajánlathoz, jellemzően a szervezet és a megoldás nevét, legfeljebb 50 karakter hosszú lehet.  Példa: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Előfizetés azonosítókat előzetes verzió** | Adjon hozzá egy és 100 közötti előfizetési azonosítók a dokumentummegjelenítők. Ezek fehér felsorolt előfizetéssel hozzáfér az ajánlatra való közzététel után, mielőtt élő kerül. |
| **Hasznos hivatkozások**          | URL-címek hozzáadása a dokumentációra, kibocsátási megjegyzései, – gyakori kérdések és így tovább. |
| **Javasolt kategóriák (max. 5)** | Lehet, hogy az üzleti és technikai kategóriák kínáló többszörös kijelölés legjobb társítva.  Legfeljebb 5 engedélyezett.  |
|  |  |


## <a name="marketing-artifacts-section"></a>Marketing-összetevők szakasz

Ez a második szakasz három alszakaszok oszlik: **Emblémák**, **képernyőkép**, és **videók**. Emblémák az egyetlen szükséges összetevőket, marketing, azonban az összes ajánlott ügyfél jogorvoslati erősen ajánlott.

![A piactér lapján a virtuális gépek az új ajánlat űrlap összetevők szakaszában marketing](./media/publishvm_009.png)

|  **Mező**                |     **Leírás**                                                          |
|  ---------                |     ---------------                                                          |
| *Emblémát*  |  |
| **Kis**                 | 40 x 40 képpontos .ico bitkép                                                      |
| **Közepes**                | 90 x 90 képpontos .ico bitkép                                                      |
| **Nagy méretű**                 | 115 x 115 képpontos .ico bitkép                                                   |
| **Széles körű**                  | 255 x 115 képpontos .ico bitkép                                                    |
| **Fő kép**                  | 815 x 290 bitkép.  Nem kötelező, azonban egyszer feltöltött a hero ikon nem lehet törölni. |
| *Képernyőképek*  | Nem kötelező, de legfeljebb öt képernyőképek kiszolgálónként Termékváltozat. |
| **Name (Név)**                  | Neve vagy címe <!-- TODO - max char length? none specified in UI -->                               |
| **Rendszerkép**                 | Képernyő lemezképet, 533 x 324 képpont                                         |
| *Videók*  |  |
| **Name (Név)**                  | Neve vagy címe  <!-- TODO - max char length? -->                              |
| **Hivatkozás**                  | Videó URL-címe, YouTube vagy Vimeo tárolt                                        |
| **Miniatűr**             | 533 x 324 bitkép                                                               |
|  |  |


### <a name="logo-guidelines"></a>Emblémával kapcsolatos irányelvek

<!-- TD: It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

A Cloud Partner portálra feltöltött összes emblémák útmutatást követik:

*  Az Azure arculata egyszerű színpalettát használ. Tartsa számát az elsődleges és másodlagos színt az embléma alacsony.
*  Az Azure Portal a témák színei fehér, és fekete. Ezért kerülje ezeket a színeket, a emblémák hátterének színe. Néhány színekkel, amely biztosítja, a emblémák neves az Azure Portalon. Javasoljuk az egyszerű alapszínek használatát. Ha áttetsző hátterű használ, akkor ügyeljen arra, hogy a emblémák szöveg nem lesznek fehér vagy fekete vagy kék.
*  Ne használjon egy színátmenetes hátterének az embléma.
*  Elkerüli a szöveg – a vállalat vagy a márkanév még – az embléma a. Az embléma megjelenését és működését "egyszerű" kell lennie, és kerülje átmenetekhez.
*  Az embléma nem stretch.

#### <a name="hero-logo"></a>Főképembléma

Nem kötelező megadni. a Hero-embléma azonban a feltöltését követően nem lehet törölni a hero ikonra.  A Hero embléma ikon útmutatást követik:

*  Fekete, a fehér és áttetsző háttérrel hero-ikonokat nem engedélyezettek.
*  Ne használja a bármely világos szín a hero ikon háttere.  A közzétevő megjelenített neve, csomag cím és az ajánlat hosszú összegzése fehér betűszínt jelennek meg, és a háttér előtt kell kiemeléséhez.
*  Ne használja a legtöbb szöveget, a hero embléma tervezésekor.  A közzétevő neve, csomag cím, az ajánlat hosszú összegzése és a Létrehozás gombra programozott módon vannak ágyazva a hero ikonra, amikor az ajánlat sorolja fel. 
* Egy nem használt téglalap a jobb oldalon – a hero ikon mérete 415 x 100 képpont közé tartozik, és 370 eltolás bal képpont.  

Tegyük fel a következő hero ikon látható, az Azure Container Service.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Az Azure Container Service példa hero ikon](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Marketing információk példa 

Az alábbi képen láthatja, hogyan Marketinginformáció jelenjen meg a Microsoft Windows Server fő termék lapon.

![A Microsoft Windows Server termékhez (példa)](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Elégtelen területén
<!-- this all should be referenced in a common location for lead management, not in this file. nothing unique for a vm specifically. -->

A harmadik szakasz lehetővé teszi, hogy összegyűjtse az ügyfelek az Azure Marketplace-ajánlat az új potenciális ügyfeleket. A következő tárolási lehetőségek (lehetőséget a legördülő listából) az érdeklődők adatait kínál.

* **Nincs** – az alapértelmezett, az érdeklődők adatait nem gyűjtötte a program.
* Azure Table - kapcsolati karakterlánc által meghatározott Azure-tábla írása.
* Dynamics CRM Online - írni a [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) példányhoz egy URL-cím és a hitelesítést a hitelesítő adatokat a megadott.
* HTTPS-végpont - írása JSON-adatként megadott HTTPS-végpontokat.
* Marketo - írni a megadott [Marketo](https://www.marketo.com/) példány, a kiszolgáló-Azonosítót, a munchkin azonosítója és a képernyő azonosítója. által megadott
* Salesforce - írt egy [Salesforce](https://www.salesforce.com/) adatbázis, az objektum azonosítója által megadott.

Után az ajánlat közzététele sikeresen megtörtént, az érdeklődő kapcsolat ellenőrzése, és a egy teszt érdeklődő automatikusan elküld a konfigurált cél. Elégtelen információk folyamatosan kell kezelni, és ezek a beállítások azonnal frissíteni kell, amikor az ügyfél architektúra végrehajtott módosítások.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Jogi szakasz

Ez a szakasz utolsó lehetővé teszi, hogy a két jogi dokumentumok ajánlatok szükséges: Adatvédelmi szabályzat és a használati feltételeket.

|  **Mező**                |     **Leírás**                                                          |
|  ---------                |     ---------------                                                          |
| **Adatvédelmi szabályzat URL-címe**    | A közzétett adatvédelmi szabályzat URL-címe                                            |
| **Használati feltételek**          | egyszerű szöveg vagy egyszerű HTML házirend.  <!-- TODO - max char length? -->       |
|  |  |

<br/>

A következő [támogatja](./cpp-support-tab.md) lap, Ön biztosítja műszaki és felhasználói támogatási információk az ajánlatban.

