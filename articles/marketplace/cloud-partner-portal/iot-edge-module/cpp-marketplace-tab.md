---
title: Az Azure IoT Edge-modul Marketplace leírását |} A Microsoft Docs
description: Hozzon létre egy IoT Edge-modul marketplace leírása.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: c119869c47547fd5d91f6ee91fcd772ea5941f46
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49431031"
---
# <a name="iot-edge-module-marketplace-tab"></a>IoT Edge-modul piactér lapján


A **Marketplace** lapján a **új ajánlat** lap lehetővé teszi, hogy adja meg a leendő ügyfelek-szerződések marketinges, értékesítési és jogi információk és származó érdeklődők kezeléséhez a Marketplace-en. A hosszú űrlap négy részből áll: **áttekintése**, **Marketing összetevők**, **vezethet felügyeleti**, és **jogi**.

## <a name="overview"></a>Áttekintés

Ebben a szakaszban, az Azure Marketplace-ajánlat kapcsolatos általános információkat adja meg.  Egy csillag (*) hozzáfűzi a mező nevét jelzi, hogy ez szükséges.

![Áttekintés szakaszban a Marketplace-en lap az új ajánlat űrlapon az IoT Edge-modulok](./media/iot-edge-module-marketplace-tab-overview.png)

A következő táblázat ismerteti a cél és ezek a mezők tartalmát.

|  **Mező**                |     **Leírás**                                                          |
|  ---------                |     ---------------                                                          |
| **Cím**                 | Az ajánlat címe. Megjelenik ezzel a beállítással hangsúlyosan a Marketplace-en.  Legfeljebb 50 karakter hosszúságú. <!--ADD PICTURE IN ACTION-->|
| **Összefoglalás**               | Az ajánlat rövid összefoglalása. A hosszabb 100 karakternél. <!--ADD PICTURE IN ACTION-->|
| **Hosszú összegzése**          | Az ajánlat már összefoglaló (bár ez lehet ugyanaz, mint a **összefoglaló**).  Legfeljebb 256 karakter hosszúságú. <!--ADD PICTURE IN ACTION-->|
| **Leírás**           | Az ajánlat leírása.  Legfeljebb 3000 karakter hosszúságú támogatja az egyszerű HTML-formázás.<br/> Tartalmaznia kell egy *hardverre vonatkozó minimális* bekezdés alján. Példa: <br/> <p><u>Minimális hardverkövetelmények:</u> Linux x64 és arm32 OS, 1 GB RAM, 500 MB-os tárhelyet</p>
| **Marketing-azonosító**  | Egyedi URL-címet rendelni ehhez az ajánlathoz, jellemzően a szervezet és a megoldás nevét, legfeljebb 50 karakter hosszú lehet.  Példa: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleIoTEdgeModule`  |
| **Előfizetés azonosítókat előzetes verzió** | Adjon hozzá egy és 100 közötti előfizetési azonosítók a dokumentummegjelenítők. Ezek fehér felsorolt előfizetéssel hozzáfér az ajánlatra való közzététel után, mielőtt élő kerül. |
| **Hasznos hivatkozások**          | Lehet, hogy az üzleti és technikai kategóriák kínáló többszörös kijelölés legjobb társítva.  Legfeljebb 10 engedélyezett. Ügyeljen arra, hogy legalább egy hivatkozást a dokumentációban és a egy hivatkozást a kompatibilis IoT Edge-eszközök hozzáadása az [Azure IoT-eszközök katalógusának](https://catalog.azureiotsolutions.com/). |
| **Javasolt kategóriák** | Válasszon legfeljebb öt kategóriába. Azok fog megjelenni a termék részletes információját. A Tallózás lapon minden IoT Edge-modulok alatt látható a *IOT- \> IoT Edge-modul* kategória.|

### <a name="offer-example"></a>Az ajánlat példa

 Az alábbi példák mutatják hogyan az ajánlat **cím**, **összefoglalás**, **leírás**, **emblémák**, és **képernyőképek** mező jelenik meg a különböző nézeteket.
 
#### <a name="on-the-azure-marketplace-website"></a>Az Azure Marketplace webhelyén:

- Ajánlatok böngészésekor:

    ![Hogyan marketplace-ajánlat jelennek meg az Azure piactér webhelyén – tallózása](./media/iot-edge-module-ampdotcom-card.png)

- Ha megnézzük az ajánlat részletei:

    ![Hogyan IoT Edge-modul megjelenik-e, amikor a jövőt láthatja a termékek részleteit a webhely](./media/iot-edge-module-ampdotcom-pdp.png)

#### <a name="on-the-azure-portal-website"></a>Az Azure portal webhelyén:

- Ajánlatok böngészésekor:

    ![Hogyan IoT Edge-modul megjelenik-e az Azure Portalon #1 böngészésekor](./media/iot-edge-module-portal-browse.png)

    ![Hogyan IoT Edge-modul megjelenik-e az Azure Portalon #2 böngészésekor](./media/iot-edge-module-portal-product-picker.png)

- Az ajánlat keresésekor:

    ![Hogyan IoT Edge-modul megjelenik-e az Azure Portalon keresésekor](./media/iot-edge-module-portal-search.png)

- Ha megnézzük az ajánlat részletei:

    ![Hogyan IoT Edge-modul megjelenik-e ha a termék adatait a portálon](./media/iot-edge-module-portal-pdp.png)


## <a name="marketing-artifacts"></a>Marketing-összetevők

Ez a szakasz azt a következő alszakaszokat: **emblémák**, **képernyőkép**, és **videók**. 

>[!Note]
>Emblémák az egyetlen szükséges összetevőket, marketing, azonban az összes ajánlott ügyfél jogorvoslati erősen ajánlott.

![A piactér lapján a virtuális gépek az új ajánlat űrlap összetevők szakaszában marketing](./media/publishvm_009.png)

|  **Mező**                |     **Leírás**                                                          |
|  ---------                |     ---------------                                                          |
| *Emblémát*  | Tekintse meg az előző képernyőre megtekintheti, hol és hogyan használható a emblémák rögzíti.  |
| **Kis**                 | 40 x 40 képpontos PNG formátum                                                     |
| **Közepes**                | 90 x 90 képpontos PNG formátum                                                     |
| **Nagy méretű**                 | 115 x 115 képpontos PNG formátum                                                  |
| **Széles körű**                  | 255 x 115 képpontos PNG formátum                                                   |
| **Fő kép**                  | 815 x 290 képpontos PNG formátumú.  Nem kötelező, azonban egyszer feltöltött a hero ikon nem lehet törölni. |
| *Képernyőképek*  | Képernyőképek jelenik meg a termék részletes információját. Azok a jó módszer annak szemléltetésére, az IoT Edge-modul célja, és hogyan működik. Architektúra-diagramok megjelenítése, vagy kis illusztrációk példányhoz használja. Nem kötelező, de legfeljebb öt képernyőképek kiszolgálónként Termékváltozat. |
| **Name (Név)**                  | Neve vagy címe. A hosszabb 100 karakternél.                             |
| **Rendszerkép**                 | Képernyő lemezképet, 533 x 324 képpontos PNG formátum                                         |
| *Videók*  | Videók a termék részletező lapján jelennek meg. Azok a jó módszer annak szemléltetésére, az IoT Edge-modul célja, és hogyan működik. |
| **Name (Név)**                  | Neve vagy címe. A hosszabb 100 karakternél.                              |
| **Hivatkozás**                  | Videó URL-címe, YouTube vagy Vimeo tárolt                                        |
| **Miniatűr**             | 533 x 324 képpontos PNG formátum                                                           |


### <a name="logo-guidelines"></a>Emblémával kapcsolatos irányelvek

<!-- It seems like this section could be better located in some common area, maybe a AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

A Cloud Partner portálra feltöltött összes emblémák útmutatást követik:

*  Az Azure arculata egyszerű színpalettát használ. Tartsa számát az elsődleges és másodlagos színt az embléma alacsony.
*  Az Azure Portal a témák színei fehér, és fekete. Kerülje a színek használata az emblémát, a háttér színe. Használjon színt, amellyel a emblémák neves az Azure Portalon. Javasoljuk az egyszerű alapszínek használatát. Ha áttetsző háttérrel használ, győződjön meg róla, hogy a emblémák szöveg nem fehér, fekete, vagy a kék.
*  Az embléma háttér átmenetének nem használja.
*  Elkerüli a szöveg – a vállalat vagy a márkanév még – az embléma a. Az embléma megjelenését és működését "egyszerű" kell lennie, és kerülje átmenetekhez.
*  Az embléma nem stretch.

#### <a name="hero-logo"></a>Főképembléma

A Hero embléma megadása nem kötelező.

>[!Important]
>Miután feltöltötte a Hero embléma, nem lehet törölni.

Hero emblémát a következőkre használhatja: 

*  Fekete, a fehér és áttetsző háttérrel nem engedélyezett.
*  Ne használja a bármely világos szín, a háttérben az emblémát.  A közzétevő megjelenített név, cím és az ajánlat hosszú összegzése fehér betűszínt jelennek meg, és a háttér előtt kell kiemelkedik csomagot.
*  Ne használja a legtöbb szöveg, amikor az identitásfelügyelet az embléma. A közzétevő neve, csomag cím, az ajánlat hosszú összegzése és a Létrehozás gombra programozott módon vannak ágyazva az embléma esetén az ajánlatot. 
* Tartalmazza a hero embléma a jobb oldalon egy nem használt négyszögletes területet. Az üres helyet 415 x 100 képpont, de eltolás a balról és 370 képpont.  

<!-- P2: woudl be nice to also have an picture int he context of iot edge of the hero image
As an example, the following hero icon is for the Azure Container Service.

![Example hero icon for Azure Container Service](./media/publishvm_010.png)
-->


## <a name="lead-management"></a>Felügyeleti vezethet

Ez a szakasz lehetővé teszi, hogy beállította az ügyfél gyűjtésére vonatkozó beállítások az Azure Marketplace-ajánlat a generált vezet. A következő tárolási lehetőségeket egy legördülő lista közül választhat.

* **Nincs** – az alapértelmezett, az érdeklődők adatait nem gyűjtötte a program.
* Azure Table - kapcsolati karakterlánc által meghatározott Azure-tábla írása.
* Dynamics CRM Online - írni a [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) példányhoz egy URL-cím és a hitelesítést a hitelesítő adatokat a megadott.
* HTTPS-végpont - írása JSON-adatként megadott HTTPS-végpontokat.
* Marketo - írni a megadott [Marketo](https://www.marketo.com/) példány, a kiszolgáló-Azonosítót, a munchkin azonosítója és a képernyő azonosítója. által megadott
* Salesforce - írt egy [Salesforce](https://www.salesforce.com/) adatbázis, az objektum azonosítója által megadott.

Után az ajánlat közzététele sikeresen megtörtént, az érdeklődő kapcsolat ellenőrzése, és a egy teszt érdeklődő automatikusan elküldi a célra konfigurált. 

>[!Note]
>Elégtelen információk folyamatosan kell kezelni, és ezek a beállítások azonnal frissíteni kell, amikor az ügyfél architektúra végrehajtott módosítások.

<!-- there is missing some marketing imagess
1. inside azure portal the wide logo/regular logo
2. inside azure portal the top curation section
3. amp.com the pricing tab
4. amp.com the tile -->

## <a name="legal"></a>Jogi tudnivalók

Ez a szakasz lehetővé teszi, hogy a két jogi dokumentumok, amelyek szükségesek az egyes ajánlatok biztosíthat: adatvédelmi irányelveket és a használati feltételeket.

|  **Mező**                |     **Leírás**                                                          |
|  ---------                |     ---------------                                                          |
| **Adatvédelmi szabályzat URL-címe**    | A közzétett adatvédelmi szabályzat URL-címe                                            |
| **Használati feltételek**          | Használati feltételeket, a beágyazott simple HTML- vagy hivatkozás a feladott használati feltételeket       |

<br/>

## <a name="next-steps"></a>További lépések

Használja a [támogatja](./cpp-support-tab.md) fülre, és adja meg a technikai és felhasználói erőforrások támogatása az ajánlatot.
