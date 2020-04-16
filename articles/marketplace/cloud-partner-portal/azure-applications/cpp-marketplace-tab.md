---
title: Az Azure-alkalmazás ajánlata a Piactér lap
description: A Piactér lapon azonosíthatja az Azure-alkalmazásajánlat marketingeszközeit.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 6113759cc68d07af4c22edf03b3274d92dcbe780
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416298"
---
# <a name="azure-application-marketplace-tab"></a>Az Azure alkalmazás piactere lapja

A Piactér lapon leírhatja az Azure-alkalmazást, és marketingeszközöket biztosít. Ez a lap a következő űrlapokat tartalmazza: Áttekintés, Marketingösszetevők, Érdeklődőkezelés és Jogi.

## <a name="overview-form"></a>Áttekintő űrlap

Az Áttekintő űrlapon a szükséges és választható mezők láthatók a következő képernyőfelvételen. A kötelező mezőket csillag (*) vádolja.

![Áttekintő űrlap](./media/azureapp-marketplace-overview.png)

Az alábbi táblázat az ajánlat kirakatának létrehozásához használt beállításokat ismerteti.   A csillaggal hozzáfűzött mezők szükségesek.

|      Mező         |    Leírás    |
|  ---------------   |  ---------------  |
| **Cím\***        | Az ajánlat címe. Ez lesz látható jól látható a piacon. A maximális hossz 50 karakter. |
| **Összefoglalás\***      | Az ajánlat rövid összefoglalása. A maximális hossz 100 karakter.           |
| **Hosszú összegzés\*** | Hosszabb összefoglaló az ajánlat (bár lehet ugyanaz, mint az összefoglaló). A maximális hossz 256 karakter.           |
| **Leírás\***  | Az ajánlat leírása. A maximális hossz 3000 karakter. Egyszerű HTML formázás megengedett, beleértve &lt;a&gt; &lt;p&gt;&gt; &lt;,&gt; &lt;em&gt;, ul , &lt;li , ol és fejléc címkéket.  |
| **Marketing azonosító\*** | Az ajánlathoz társítani rakandó egyedi URL-cím általában tartalmazza a szervezet és a megoldás nevét, legfeljebb 50 karakter hosszú. Válasszon egy rövid, rövid, rövid marketingazonosítót a szolgáltatásához. Ezt fogja használni a piactéri URL-címek ebben az ajánlatban. Ha például a közzétevő azonosítója "contoso", és a marketingazonosítója "sampleApp", az Azure Marketplace-en az ajánlat URL-címe`https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  
| **Előfizetés-azonosítók előzetes verzió\*** | Adja hozzá egy-100 előfizetés-azonosítók előnézetek. Ezek a fehérlistán szereplő előfizetések hozzáférhetnek az ajánlathoz, amíg az előzetes verzióban érhető el a közzététel után, mielőtt élesben lépne.          |
| **Hasznos linkek**    | Opcionálisan különböző erőforrásokra mutató hivatkozásokat is megadhat az ajánlat felhasználóinak, például támogatást, dokumentációt, fórumokat stb.  Javasoljuk, hogy legalább egy hivatkozást adjon a dokumentációhoz.            |
| **Javasolt kategóriák (max. 5)\*** | Válasszon egy-öt kategóriát. A kiválasztott kategóriák segítségével az azure-piactéren és az Azure Portalon elérhető termékkategóriákhoz rendelheti az ajánlatot. Ezek a böngészési oldalakon és a termékadatok oldalon jelennek meg. |
|  |  |


## <a name="marketing-artifacts"></a>Marketing összetevők

A Marketingösszetevők képernyőn a szükséges és választható mezők a következő képernyőfelvételen láthatók. A kötelező mezőket csillag (*) vádolja.

![Marketingösszetevők képernyő](./media/azureapp-marketplace-artifacts.png)

Az alábbi táblázat a marketingösszetevőket ismerteti.

|      Mező         |    Leírás    |
|  ---------------   |  ---------------  |
| **Kicsi\***        | Kis embléma: 40x40 pixel PNG formátumban     |
| **Medium\***       | Közepes embléma: 90x90 képpont PNG formátumban    |
| **Nagy\***        | Nagy logó: 115x115 képpont PNG formátumban   |
| **Széles\***         | Széles embléma: 255x115 pixel PNG formátumban    |
| **Hős**           | Választható hősembléma: 815x290 pixel PNG formátumban. **Megjegyzés:** A hős ikon nem törölhető a feltöltés után. |
| **Képernyőképek (max. 5)** |        A képernyőképek a termék adatai lapon jelennek meg. Ezek jó módot jelentenek arra, hogy vizuálisan kommunikálja, mit csinál az alkalmazása, és hogyan működik. Megjeleníthetpéldáj például architektúradiagramokat, vagy használhat esetillusztrációkat. A képernyőképek nem kötelezőek, és termékváltozatonként legfeljebb 5 lehet. Képernyőkép hozzáadása:<ul><li>A Képernyőkép ablak megnyitásához válassza **a + Képernyőkép hozzáadása lehetőséget**</li><li>**Név** – Név/cím megadása (legfeljebb 100 karakter hosszú.)</li><li>**Feltöltés** - Töltse fel a képet. Meg kell PNG formátumban, és a méret 533 x 324 pixel.</li></ul>           |
| **Videó hozzáadása**      | Nem kötelező, a videók megjelennek a termék adatai oldalon. Ők egy jó módja annak, hogy vizuálisan kommunikálni, amit az alkalmazás nem, és hogyan működik. Videó hozzáadása: <ul><li>A Videó ablak megnyitásához válassza **a + Videó hozzáadása** lehetőséget</li><li>**Név** – Név/cím megadása (legfeljebb 100 karakter hosszú.)</li><li>**Link** - Adja meg a videót üzemeltető webhely URL-címét (YouTube vagy Vimeo)</li><li>**Miniatűr** – Miniatűr feltöltése. Meg kell PNG formátumban, és a méret 533 x 324 pixel.</li></ul>          |
|  |  |


### <a name="artifact-examples-in-azure-marketplace"></a>Műtermék-példák az Azure Piactéren

A következő képernyőfelvétel egy Marketplace-keresési eredmény példáját mutatja be.

![Marketplace ajánlat keresési eredmény](./media/azureapp-marketplace-example-browse.png)

Az alábbi képen látható, hogyan jelenik meg az ajánlat a Marketplace-en, miután az ügyfél rákattint az ajánlat csempéjére a keresési eredményben.

![Marketplace ajánlat keresési eredmény részletei](./media/azureapp-marketplace-example-details.png)


### <a name="artifact-examples-in-azure-portal"></a>Műtermékek– példák az Azure Portalon

A következő képernyő-felvétel bemutatja, hogyan jelenik meg egy ajánlat az Azure Portalon. Az ebben a példában szereplő alkalmazásajánlat a Marketplace>Mindent>**Fejlesztési + Teszt>Jenkins**oldalon találja. A Jenkins-ajánlat egy emblémát, címet és közzétevő megjelenítendő nevét jeleníti meg.

![Ajánlatok böngészése az Azure Portalon](./media/azureapp-portalbrowse-artifacts-jenkins.png)

A következő képernyőfelvétel részletes információkat jelenít meg az alkalmazásról, amikor a felhasználó a Jenkins t választja.

![Ajánlat részletei az Azure Portalon](./media/azureapp-portal-artifacts-jenkins-details.png)


### <a name="logo-guidelines"></a>Emblémával kapcsolatos irányelvek

A Cloud Partner Portálra feltöltött összes emblémának követnie kell az alábbi irányelveket:

- Az Azure arculata egyszerű színpalettát használ. Alacsonyan tartsa az embléma elsődleges és másodlagos színeinek számát.
- Az Azure Portal színszínei fehérek és feketék. Ne használja ezeket a színeket az emblémák háttérszíneként. Használjon olyan színt, amely az emblémákat kiemelkedővé teszi az Azure Portalon. Javasoljuk az egyszerű alapszínek használatát. Ha átlátszó hátteret használ, győződjön meg arról, hogy az emblémák/szövegek nem fehérek, feketék vagy kékek.
- Ne használjon színátmenetes hátteret az emblémán.
- Kerülje a szöveg, még a cég vagy a márkanév, az embléma. A logó megjelenésének és érzetének "laposnak" kell lennie, és kerülnie kell a színátmeneteket.
- Ne nyújtsd ki a logót.


#### <a name="hero-logo"></a>Főképembléma

A Hero embléma nem kötelező.

>[!IMPORTANT]
>A Hős embléma nem törölhető a feltöltés után.

A Hero embléma alábbi irányelveit használja:

- Fekete, fehér és átlátszó hátterek nem engedélyezettek.
- Ne használjon semmilyen világos színt az embléma háttereként. A közzétevő megjelenítendő neve, a terv címe és az ajánlat hosszú összegzése fehér betűszínnel jelenik meg, és a háttérben kell kitűnnie.
- Ne használja a legtöbb szöveget az embléma tervezésekor. A közzétevő neve, a terv címe, az ajánlat hosszú összegzése és a létrehozás gomb programozott módon vannak beágyazva az emblémába, amikor az ajánlat szerepel.
- A hősembléma jobb oldalán egy nem használt téglalap alakú szóközt is fel kell tüntetni. Ez az üres terület 415x100 képpont, balról 370 képponttal eltolva.


## <a name="lead-management"></a>Érdeklődőkezelés

Az Érdeklődőkezelés űrlapon van egy választható mező az érdeklődőkezelés konfigurálásához. Az érdeklődőkezelés konfigurálásához válassza ki az Érdeklődő célt a legördülő listából. A következő képernyőfelvétel en láthatók a rendelkezésre álló úti célok.

![Érdeklődőkezelési cél kiválasztása](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>Az üzenet megtekintéséhez válassza az információs ikont: "Válassza ki azt a rendszert, amelyben az érdeklődőket tárolni fogja. Itt megtudhatja, hogyan csatlakozhat [here](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) a CRM-rendszerhez."

További információt az [Ügyfélérdeklődők konfigurálása című témakörben talál.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)


## <a name="legal"></a>Jogi tudnivalók

Használja a Jogi űrlapot, hogy a jogi dokumentáció szükséges minden ajánlatot.

Adja meg az alábbi információkat:

- **Adatvédelmi\* irányelvek URL-címe** – Adjon meg egy hivatkozást az alkalmazás adatvédelmi irányelveire.
- Használati feltételek – Adja meg az alkalmazás használati feltételeit. **\* ** Az ügyfeleknek el kell fogadniuk ezeket a feltételeket, mielőtt kipróbálhatják az alkalmazást.

![Jogi forma](./media/azureapp-marketplace-legal.png)


## <a name="next-steps"></a>További lépések

[Támogatási lap](./cpp-support-tab.md)
