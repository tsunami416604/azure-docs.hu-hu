---
title: Azure-alkalmazás ajánlata – piactér lap
description: A piactér lapon azonosíthatók az Azure-alkalmazások értékesítési eszközei.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 967b66a67d51b3a79bcf930ce977af48acc3dd63
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827566"
---
# <a name="azure-application-marketplace-tab"></a>Az Azure Application Marketplace lapja

A piactér lapon ismertetheti az Azure-alkalmazást, és biztosíthatja a marketing-eszközök használatát. Ezen a lapon a következő formák találhatók: áttekintés, marketing-összetevők, érdeklődői felügyelet és jogi.

## <a name="overview-form"></a>Áttekintő űrlap

Az áttekintő űrlapon a kötelező és választható mezők láthatók a következő képernyőfelvételen. A kötelező mezőket csillag (*) alapján vádoljuk.

![Áttekintő űrlap](./media/azureapp-marketplace-overview.png)

Az alábbi táblázat az ajánlat kirakatának létrehozásához használt beállításokat ismerteti.   A csillaggal hozzáfűzött mezők megadása kötelező.

|      Mező         |    Leírás    |
|  ---------------   |  ---------------  |
| **Cím\***        | Az ajánlat címe. Ez kiemelten jelenik meg a piactéren. A maximális hossz 50 karakter. |
| **Összefoglalás\***      | Az ajánlat rövid összefoglalása. A maximális hossz 100 karakter.           |
| **Hosszú összefoglalás\*** | Az ajánlat összefoglalása (bár lehet, hogy megegyezik az összegzéssel). A maximális hossz 256 karakter.           |
| **Leírás\***  | Az ajánlat leírása. A maximális hossz 3000 karakter. Az egyszerű HTML-formázás megengedett, beleértve a &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;Li&gt;, &lt;ol&gt; és a fejléc címkéit.  |
| **Marketing-azonosító\*** | Az ajánlathoz társítandó egyedi URL-cím, amely jellemzően a szervezet és a megoldás nevét, a maximális hossz 50 karaktert tartalmazza. Válasszon egy rövid, felhasználóbarát marketing-azonosítót a szolgáltatáshoz. Ezt az ajánlatot a piactér URL-címeiben fogjuk használni. Ha például a közzétevő azonosítója "contoso", a marketing azonosítója pedig "PéldaAlkalmazás", akkor az Azure Marketplace-en elérhető ajánlatának URL-címe https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp  
| **Előfizetési azonosítók\*** | Adja hozzá az előnézetek egy-100 előfizetés-azonosítóját. Ezek a felsorolt előfizetések hozzáférhetnek az ajánlathoz, miközben elérhetők az előzetes verzióban a közzététel után.          |
| **Hasznos hivatkozások**    | Igény szerint különféle forrásokra mutató hivatkozásokat is megadhat az ajánlat felhasználói számára, például a támogatást, a dokumentációt, a fórumokat stb.  Javasoljuk, hogy adjon hozzá legalább egy hivatkozást a dokumentációhoz.            |
| **Javasolt kategóriák (max. 5)\*** | Válasszon egy-öt kategóriát. A kiválasztott kategóriák az ajánlatnak az Azure Marketplace-en és az Azure Portalon elérhető termékkategóriák szerinti leképezésére szolgálnak. Ezek a böngészési lapokon és a termék részletei lapon jelennek meg. |
|  |  |


## <a name="marketing-artifacts"></a>Marketing-összetevők

A marketing-összetevők űrlapján a szükséges és választható mezők láthatók a következő képernyőfelvételen. A kötelező mezőket csillag (*) alapján vádoljuk.

![Marketing-összetevők űrlapja](./media/azureapp-marketplace-artifacts.png)

A következő táblázat ismerteti a marketing összetevőit.

|      Mező         |    Leírás    |
|  ---------------   |  ---------------  |
| **Kis\***        | Kis embléma: 40x40cm képpontok PNG formátumban     |
| **Közepes\***       | Közepes embléma: 90x90 képpontok PNG formátumban    |
| **Nagyméretű\***        | Nagyméretű embléma: 115x115 képpontok PNG formátumban   |
| **Széles\***         | Széles embléma: 255x115 képpontok PNG formátumban    |
| **Hero**           | Opcionális hős emblémája: 815x290 képpontok PNG formátumban. **Megjegyzés:** A Hero ikon nem törölhető a feltöltés után. |
| **Képernyőképek (max. 5)** |        A Képernyőképek a termék részletei lapon jelennek meg. Jól szemléltetik, hogy milyen módon kommunikálnak az alkalmazással, és hogyan működik. Megjelenítheti például az architektúra-diagramokat, vagy használhat esettanulmányokat. A képernyőképek nem kötelezőek, és az SKU-ra legfeljebb 5. Képernyőkép hozzáadása:<ul><li>A képernyőfelvétel ablak megnyitásához válassza a **+ képernyőfelvétel hozzáadása** elemet.</li><li>**Név** – adjon meg egy nevet és egy címet (legfeljebb 100 karakter hosszúságú lehet.)</li><li>**Feltöltés** – feltöltheti a rendszerképet. A méretnek PNG formátumban kell lennie, a mérete pedig 533 x 324 képpont.</li></ul>           |
| **Videó hozzáadása**      | Nem kötelező, a videók a termék részletei lapon jelennek meg. Jól szemléltetik, hogy milyen módon kommunikálnak az alkalmazással, és hogyan működik. Videó hozzáadása: <ul><li>Válassza a **+ videó hozzáadása** elemet a videó ablak megnyitásához</li><li>**Név** – adjon meg egy nevet és egy címet (legfeljebb 100 karakter hosszúságú lehet.)</li><li>**Hivatkozás** – adja meg a videót üzemeltető webhely URL-címét (YouTube vagy Vimeo)</li><li>**Miniatűr** – miniatűrt tölthet fel. A méretnek PNG formátumban kell lennie, a mérete pedig 533 x 324 képpont.</li></ul>          |
|  |  |


### <a name="artifact-examples-in-azure-marketplace"></a>Példa az Azure Marketplace-en

A következő képernyőfelvételen egy példa látható a piactér keresési eredményeire.

![Marketplace-ajánlat keresési eredménye](./media/azureapp-marketplace-example-browse.png)

Az alábbi képen látható, hogy az ajánlat hogyan jelenik meg a piactéren, miután az ügyfél az ajánlat csempére kattint a keresési eredményben.

![Marketplace-ajánlat keresési eredményének részletei](./media/azureapp-marketplace-example-details.png)


### <a name="artifact-examples-in-azure-portal"></a>Összetevő-példák az Azure Portalon

Az alábbi képernyőfelvételek azt mutatják be, hogyan jelennek meg az ajánlatok az Azure Portalon. Ebben a példában az alkalmazás ajánlata a Marketplace-en **> minden > dev + Test > Jenkins**használatával érhető el. A Jenkins-ajánlat emblémát, címet és kiadói megjelenítendő nevet jelenít meg.

![Ajánlatok tallózása Azure Portal](./media/azureapp-portalbrowse-artifacts-jenkins.png)

A következő képernyőfelvétel részletes információkat jelenít meg az alkalmazásról, amikor a felhasználó kiválasztja a Jenkinst.

![Ajánlat részletei Azure Portal](./media/azureapp-portal-artifacts-jenkins-details.png)


### <a name="logo-guidelines"></a>Emblémával kapcsolatos irányelvek

A Cloud Partner Portal feltöltött összes emblémának a következő irányelveket kell követnie:

- Az Azure arculata egyszerű színpalettát használ. Tartsa meg az elsődleges és a másodlagos színek számát a logón.
- Az Azure Portal téma színei fehérek és feketeok. Ne használja ezeket a színeket a logók háttérszínéhez. Használjon olyan színt, amely a Azure Portalban Kiemelt emblémát tesz elérhetővé. Javasoljuk az egyszerű alapszínek használatát. Ha transzparens hátteret használ, ügyeljen arra, hogy a logók/szöveg ne legyen fehér, fekete vagy kék.
- Ne használjon színátmenetes hátteret az emblémán.
- A logón ne helyezzen el szöveget, még a vállalat vagy a márka nevét is. Az embléma megjelenésének és működésének "Flat" típusúnak kell lennie, és el kell kerülnie a színátmeneteket.
- Ne nyújtsa be az emblémát.


#### <a name="hero-logo"></a>Főképembléma

A Hero embléma nem kötelező.

>[!IMPORTANT]
>A Hero embléma a feltöltés után nem törölhető.

Kövesse az alábbi irányelveket a Hero emblémához:

- A fekete, fehér és transzparens hátterek nem engedélyezettek.
- Ne használjon világos színt az embléma háttereként. A közzétevő megjelenítendő neve, a terv címe és az ajánlat hosszú összegzése fehér színű betűtípussal jelenik meg, és a háttérben kell állnia.
- Ne használja a legtöbb szöveget a logó tervezésekor. A közzétevő neve, a csomag címe, az ajánlat hosszú összegzése és a létrehozás gomb programozott módon van beágyazva az emblémán belül, ha az ajánlat fel van sorolva.
- Vegyen fel egy nem használt téglalap alakú helyet a hős emblémájának jobb oldalán. Ez az üres terület 415x100 képpont, a bal oldali eltolás pedig 370 képpont.


## <a name="lead-management"></a>Érdeklődők kezelése

Az érdeklődő felügyeletének konfigurálásához a vezető felügyeleti űrlap nem kötelező mezőt tartalmaz. Az érdeklődők felügyeletének konfigurálásához a legördülő listából válassza ki az érdeklődő célhelyét. A következő képernyőfelvételen az elérhető célhelyek láthatók.

![Érdeklődői felügyelet célhelyének kiválasztása](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>Az üzenet megjelenítéséhez válassza ki az információs ikont: "válassza ki a rendszer helyét, ahol az érdeklődőket tárolni fogja. Ismerje meg, hogyan csatlakozhat a [CRM rendszerhez](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) . "

További információ: az [ügyfél-érdeklődők konfigurálása](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads).


## <a name="legal"></a>Jogi tudnivalók

A jogi űrlap használatával megadhatja az összes ajánlathoz szükséges jogi dokumentációt.

Adja meg az alábbi információkat:

- **Adatvédelmi szabályzat URL-címe\*** – adja meg az alkalmazás adatvédelmi szabályzatára mutató hivatkozást.
- **Használati feltételek\*** – adja meg az alkalmazás használati feltételeit. Az ügyfeleknek el kell fogadniuk ezeket a feltételeket az alkalmazás kipróbálása előtt.

![Jogi forma](./media/azureapp-marketplace-legal.png)


## <a name="next-steps"></a>További lépések

[Támogatási lap](./cpp-support-tab.md)
