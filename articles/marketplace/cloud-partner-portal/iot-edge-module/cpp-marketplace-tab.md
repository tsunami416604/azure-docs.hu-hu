---
title: A piactér leírása Azure IoT Edge modulhoz | Azure piactér
description: Hozza létre a piactér leírását egy IoT Edge modulhoz.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: b4a3aa4e92fcabf354de7d550ec53ead5872596d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144779"
---
# <a name="iot-edge-module-marketplace-tab"></a>IoT Edge modul Marketplace lapja

>[!Important]
>2020. április 13-ától kezdődően megkezdjük a IoT Edge modul-ajánlatok felügyeletének áthelyezését a partneri központba. Az áttelepítés után létrehozhatja és kezelheti az ajánlatokat a partner Centerben. Az áttelepített ajánlatok kezeléséhez kövesse az [IoT Edge modul létrehozása](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) című témakör utasításait.

Az **új ajánlat** oldal **piactér** lapján megadhatja a potenciális ügyfeleket marketing-, értékesítési és jogi információkkal és szerződésekkel, és kezelheti a piactéren generált érdeklődőket. Ez a hosszú űrlap négy szakaszra oszlik: **Áttekintés**, **marketing**-összetevők, **vezető felügyelet**és **jogi**.


## <a name="overview"></a>Áttekintés

Ebben a szakaszban az Azure Marketplace-ajánlat általános adatait adja meg.  A mezőhöz hozzáfűzni kívánt csillag (*) azt jelzi, hogy a név megadása kötelező.

![A IoT Edge-modulok új ajánlat űrlapjának piactér lapjának áttekintés szakasza](./media/iot-edge-module-marketplace-tab-overview.png)

A következő táblázat a mezők célját és tartalmát ismerteti. A kötelező mezőket csillag (*) alapján vádoljuk.

|  **Mező**                |     **Leírás**                                                          |
|  ---------                |     ---------------                                                          |
| **Cím\***                 | Az ajánlat címe. Ez kiemelten jelenik meg a piactéren.  Legfeljebb 50 karakter hosszú lehet. <!--ADD PICTURE IN ACTION-->|
| **Összefoglalás\***               | Az ajánlat rövid összefoglalása. Legfeljebb 100 karakter hosszú lehet. <!--ADD PICTURE IN ACTION-->|
| **Hosszú összefoglalás\***          | Az ajánlat összefoglalása (bár lehet, hogy megegyezik az **összegzéssel**).  Legfeljebb 256 karakter hosszú lehet. <!--ADD PICTURE IN ACTION-->|
| **Leírás\***           | Az ajánlat leírása.  Legfeljebb 3000 karakter hosszú lehet, és támogatja az egyszerű HTML-formázást.<br/> Tartalmaznia kell a *minimális hardverkövetelmények* bekezdést az alján. például: <br/> <p><u>Minimális hardverkövetelmények:</u> Linux x64 és arm32 OS, 1 GB RAM, 500 MB tárterület</p>
| **Marketing azonosítója\***  | Az ajánlathoz társítandó egyedi URL-cím, amely jellemzően a szervezet és a megoldás nevét, a maximális hossz 50 karaktert tartalmazza.  Például: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleIoTEdgeModule`  |
| **Előfizetési azonosítók előnézete** | Adja hozzá az előnézetek egy 100 előfizetés-azonosítóját. Ezek a felsorolt előfizetések a közzétételük előtt hozzáférhetnek az ajánlathoz, mielőtt a szolgáltatás elérhetővé válik. |
| **Hasznos hivatkozások**          | Többek között az üzleti és technikai kategóriák közül választhat, melyeket a legjobban társíthat.  Legfeljebb 10 engedélyezett. Ügyeljen arra, hogy legalább egy hivatkozást adjon hozzá a dokumentációhoz, és egy hivatkozást az [Azure IoT-eszköz](https://catalog.azureiotsolutions.com/)kompatibilis IoT Edge eszközeire. |
| **Javasolt kategóriák\*** | Válasszon legfeljebb öt kategóriát. A termék részletei lapon jelennek meg. A Tallózás lapokon az összes IoT Edge modul a * \> eszközök internetes hálózata IoT Edge modul* kategóriában látható.|
|  |  |


### <a name="offer-example"></a>Példa ajánlatra

 Az alábbi példák bemutatják, hogyan jelennek meg az ajánlat **címe**, **összegzése**, **leírása**, **emblémája**és **képernyőképei** mezők különböző nézetekben.

 
#### <a name="on-the-azure-marketplace-website"></a>Az Azure Marketplace webhelyén:

- Böngészési ajánlatok:

    ![Hogyan jelennek meg a piactéren elérhető ajánlatok az Azure Marketplace webhelyén – Tallózás](./media/iot-edge-module-ampdotcom-card.png)

- Az ajánlat részleteit itt tekintheti meg:

    ![Hogyan jelenik meg IoT Edge modul a termék részleteinek a webhelyen való megkeresése során](./media/iot-edge-module-ampdotcom-pdp.png)


#### <a name="on-the-azure-portal-website"></a>A Azure Portal webhelyén:

- Böngészési ajánlatok:

    ![Hogyan jelenik meg IoT Edge modul a Azure Portal böngészése során #1](./media/iot-edge-module-portal-browse.png)

    ![Hogyan jelenik meg IoT Edge modul a Azure Portal böngészése során #2](./media/iot-edge-module-portal-product-picker.png)

- Ajánlat keresésekor:

    ![Hogyan jelenik meg IoT Edge modul a Azure Portal keresésekor](./media/iot-edge-module-portal-search.png)

- Az ajánlat részleteit itt tekintheti meg:

    ![Hogyan jeleníti meg IoT Edge modul a termék részleteit a portálon](./media/iot-edge-module-portal-pdp.png)


## <a name="marketing-artifacts"></a>Marketing-összetevők

Ez a szakasz a következő alszakaszokat tartalmazza: **logók**, **képernyőfelvétel**és **videók**. 

>[!Note]
>A logók az egyetlen szükséges marketing-összetevők, azonban a legjobb ügyfelek számára ajánlott.

![Marketing-összetevők szakasz a piactér lap új ajánlat űrlapján a virtuális gépekhez](./media/publishvm_009.png)

|  **Mező**                |     **Leírás**                                                          |
|  ---------                |     ---------------                                                          |
| *Logók*  | Tekintse meg az előző képernyőfelvételeket, ahol megtekintheti, hogyan és hol fogja használni a logókat.  |
| **Kicsi\***                 | 40x40cm képpont PNG formátum                                                     |
| **Medium\***                | 90x90 képpont PNG formátum                                                     |
| **Nagy\***                 | 115x115 képpont PNG formátum                                                  |
| **Széles\***                  | 255x115 képpont PNG formátum                                                   |
| **Hero**                  | 815x290 képpont PNG formátuma  Nem kötelező, de feltöltés után a Hero ikon nem törölhető. |
| *Képernyőképek*  | A Képernyőképek a termék részletei lapon jelennek meg. Jól szemléltetik a IoT Edge-modul működését és működésének módját. Megtekintheti az architektúrához tartozó diagramokat, vagy használhat példákat az esettanulmányok használatára. Nem kötelező, de SKU-onként legfeljebb öt képernyőkép. |
| **Név**                  | Név vagy cím. Legfeljebb 100 karakter hosszú lehet.                             |
| **Kép**                 | Képernyőfelvétel kép, 533x324 képpont PNG formátum                               |
| *Videók*  | A videók a termék részletei lapon jelennek meg. Jól szemléltetik a IoT Edge-modul működését és működésének módját. |
| **Név**                  | Név vagy cím. Legfeljebb 100 karakter hosszú lehet.                             |
| **Hivatkozás**                  | Videó URL-címe, amelyet a YouTube vagy a Vimeo tárol                                        |
| **Miniatűr**             | 533x324 képpont PNG formátum                                                     |
|  |  |


### <a name="logo-guidelines"></a>Emblémával kapcsolatos irányelvek

<!-- It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

A Cloud Partner Portal feltöltött összes emblémának a következő irányelveket kell követnie:

*  Az Azure arculata egyszerű színpalettát használ. Tartsa meg az elsődleges és a másodlagos színek számát a logón.
*  A Azure Portal téma színei fehérek és feketeok. Ne használja ezeket a színeket a logók háttérszínéhez. Használjon olyan színt, amely a Azure Portalban Kiemelt emblémát tesz elérhetővé. Javasoljuk az egyszerű alapszínek használatát. Ha transzparens hátteret használ, ügyeljen arra, hogy a logók/szöveg ne legyen fehér, fekete vagy kék.
*  Ne használjon színátmenetes hátteret az emblémán.
*  A logón ne helyezzen el szöveget – még a vállalat vagy a márka nevét is –. Az embléma megjelenésének és működésének "Flat" típusúnak kell lennie, és el kell kerülnie a színátmeneteket.
*  Ne nyújtsa be az emblémát.


#### <a name="hero-logo"></a>Főképembléma

A Hero embléma nem kötelező.

>[!Important]
>A Hero embléma feltöltése után nem törölhető.

Kövesse az alábbi irányelveket a Hero emblémához: 

*  A fekete, fehér és transzparens hátterek nem engedélyezettek.
*  Ne használjon világos színt az embléma háttereként.  A közzétevő megjelenítendő neve, a terv címe és az ajánlat hosszú összegzése fehér színű betűtípussal jelenik meg, és a háttérben kell állnia.
*  Ne használja a legtöbb szöveget a logó tervezésekor. A közzétevő neve, a csomag címe, az ajánlat hosszú összegzése és a létrehozás gomb programozott módon van beágyazva az emblémán belül, ha az ajánlat fel van sorolva. 
* Vegyen fel egy nem használt téglalap alakú helyet a hős emblémájának jobb oldalán. Ez az üres terület 415x100 képpont, a bal oldali eltolás pedig 370 képpont.  

<!-- P2: would be nice to also have an picture int he context of iot edge of the hero image
As an example, the following hero icon is for the Azure Container Service.

![Example hero icon for Azure Container Service](./media/publishvm_010.png)
-->


## <a name="lead-management"></a>Érdeklődők kezelése

Ez a szakasz lehetővé teszi az Azure Marketplace-ajánlatokból előállított ügyfelek-érdeklődők összegyűjtési lehetőségeinek beállítását. A következő tárolási lehetőségek közül választhat legördülő listából.

* **Nincs** – az alapértékek nem lesznek begyűjtve.
* Azure Table – a kapcsolódási karakterlánc által megadott Azure-táblába írva.
* Dynamics CRM Online – az URL-cím és a hitelesítő adatok által megadott [Microsoft Dynamics 365 online](https://dynamics.microsoft.com/) példányra íródott.
* HTTPS-végpont – a megadott HTTPS-végpontba írt JSON-adattartalomként.
* Marketo – a megadott [Marketo](https://www.marketo.com/) -példányra íródik, amely a kiszolgáló azonosítója, a Munchkin azonosítója és az űrlap azonosítója alapján van megadva.
* Salesforce – egy objektumazonosító által megadott [Salesforce](https://www.salesforce.com/) -adatbázisba írt.

Az ajánlat sikeres közzététele után a rendszer ellenőrzi az érdeklődői kapcsolatokat, és automatikusan elvégzi a tesztelési érdeklődők küldését a konfigurált célhelyre. 

>[!Note]
>Az érdeklődői adatokat folyamatosan kell felügyelni, és ezeket a beállításokat azonnal frissíteni kell, amikor módosításokat végeznek az ügyfél-felügyeleti architektúrán.

<!-- there is missing some marketing imagess
1. inside azure portal the wide logo/regular logo
2. inside azure portal the top curation section
3. amp.com the pricing tab
4. amp.com the tile -->


## <a name="legal"></a>Jogi tudnivalók

Ebben a szakaszban megadhatja az egyes ajánlatokhoz szükséges két jogi dokumentumot: az adatvédelmi szabályzatot és a használati feltételeket.

|  **Mező**                    |     **Leírás**                                                          |
|  ---------                    |     ---------------                                                          |
| **Adatvédelmi szabályzat URL-címe\***      | A közzétett adatvédelmi szabályzat URL-címe                                            |
| **Szabványos Constract használata\***  | Azt határozza meg, hogy a Microsoft szabványos szerződési sablonját használja-e.  További információ: [standard szintű szerződés](https://docs.microsoft.com/azure/marketplace/standard-contract).   |
| **Használati feltételek\***            | *Használati feltételek* a beágyazott egyszerű HTML-ként, vagy a használati feltételek oldalára mutató hivatkozás     |
|  |  |


## <a name="next-steps"></a>További lépések

A [támogatás](./cpp-support-tab.md) lapon megadhatja az ajánlat technikai és felhasználói támogatási erőforrásait.
