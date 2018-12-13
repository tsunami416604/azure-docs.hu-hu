---
title: Az Azure application ajánlat piactér lapján |} A Microsoft Docs
description: A piactér lap segítségével egy Azure-alkalmazás ajánlat marketing eszközök azonosítása.
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
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 74b21a9dd8faa8eb35aa8a2ef738bd6303e4cdd7
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53196948"
---
# <a name="azure-application-marketplace-tab"></a>Az Azure application Marketplace lapja

Használja a piactér lapján be az Azure-alkalmazásokat, és adja meg a marketing eszközök. Ezen a lapon a következő űrlapok tartalmazza: Áttekintés, Marketing-összetevők, érdeklődői felügyeleti és jogi.

## <a name="overview-form"></a>Áttekintő űrlap

Az Áttekintés képernyő rendelkezik a szükséges és választható mezőket a következő képernyőfelvételen látható. Kötelező mezőt csillag (*) rendszer feltehetően.

![Áttekintő űrlap](./media/azureapp-marketplace-overview.png)

A következő táblázat ismerteti a egy az ajánlatban kirakatban létrehozásához használni kívánt beállításokat.

|    **Mező**       |  **Leírás**  |
|  ---------------   |  ---------------  |
|        Beosztás            |        Az ajánlat címe. Megjelenik ezzel a beállítással hangsúlyosan a Marketplace-en. A maximális hossz 50 karakter hosszú lehet.           |
|         Összegzés           |        Az ajánlat rövid összefoglalása. A hossza legfeljebb 100 karakterből állhat.           |
|         Hosszú összegzése           |        Már összefoglaló az ajánlat (bár ez lehet ugyanaz, mint az összegzés). A maximális hossz 256 karakter.           |
|       Leírás            |         Az ajánlat leírása. A maximális hossza 3000 karakter lehet. Egyszerű HTML-formázás engedélyezett, beleértve a &lt;p&gt;, &lt;em&gt;, &lt;ul&gt;, &lt;li&gt;, &lt;ol&gt; és fejléc címkék.           |
|        Marketing-azonosító            |         Egyedi URL-címet rendelni ehhez az ajánlathoz, jellemzően a szervezet és a megoldás nevét, legfeljebb 50 karakter hosszú lehet. Válasszon egy rövid, könnyen megjegyezhető marketing azonosítót a szolgáltatáshoz. Ez használható a Marketplace-en URL-címek ezt az ajánlatot. Például ha a Gyártóazonosítóval "contoso", és a marketing azonosítója "PéldaAlkalmazás", az URL-cím, az ajánlat az Azure Marketplace-en lesz https://azuremarketplace.microsoft.com/en-us/marketplace/apps/contoso.sampleApp          |
|        Előfizetés azonosítókat előzetes verzió            |         Adjon hozzá egy és, dokumentummegjelenítők 100 előfizetés azonosítókat. Ezek fehér felsorolt előfizetéssel hozzáfér az ajánlatra bár előzetes verzióban érhető el, hogy közzététele után, mielőtt élő kerül.          |
|         Hasznos oldalak           |        Megadhat különböző forrásokra mutató hivatkozásokat a felhasználók számára az Ön ajánlatát, például a támogatási, dokumentáció, fórumokat, stb. Ügyeljen arra, hogy legalább egy hivatkozást a dokumentációt.            |
|         Javasolt kategóriák (max. 5)           |         Válasszon legfeljebb öt kategóriába. A kiválasztott kategóriákra az ajánlat leképezése az Azure Marketplace-en és az Azure Portalon elérhető termékkategóriák szolgálnak. Ezek megjelenik, Tallózás oldalak és a termék részletes információját.          |

## <a name="marketing-artifacts"></a>Marketing-összetevők

A Marketing összetevők képernyő rendelkezik a szükséges és választható mezőket a következő képernyőfelvételen látható. Kötelező mezőt csillag (*) rendszer feltehetően.

![Marketing-összetevők űrlap](./media/azureapp-marketplace-artifacts.png)

A következő táblázat ismerteti a marketing-összetevőket.

|    **Mező**       |  **Leírás**  |
|  ---------------   |  ---------------  |
|       Kicsi             |        40 x 40 képpontos PNG formátumban           |
|       Közepes             |        90 x 90 képpontos PNG formátumban           |
|       Nagy             |        115 x 115 képpontos PNG formátumban         |
|       Széles körű             |        255 x 115 képpontos PNG formátumban           |
|       Fő kép             |        815 x 290 képpontos PNG formátumban. Választható. **Megjegyzés:** Nem lehet törölni a hero ikonra, a feltöltés után.           |
|       Képernyőképek (max. 5)             |        Képernyőképek jelenik meg a termék részletes információját. Azok a jó módszer annak szemléltetésére, az alkalmazás működésével, és hogyan működik. Például architektúra-diagramok megjelenítése vagy használata megkülönbözteti a kis illusztrációk. Képernyőképek nem kötelező, és Ön legfeljebb 5 / SKU. Képernyőkép hozzáadása:<ul><li>Válassza ki **+ képernyőfelvétel hozzáadása** a képernyőképen ablak megnyitása</li><li>**Név** -név/nevezze (maximális hossza 100 karakternél.)</li><li>**Töltse fel** -feltöltheti a rendszerképet. PNG formátumúnak kell lennie, mérete pedig 533 x 324 képpont.</li></ul>           |
|        Videó hozzáadása            |        Videók a termék részletező lapján jelennek meg. Azok a jó módszer annak szemléltetésére, az alkalmazás működésével, és hogyan működik. Videó hozzáadása: <ul><li>Válassza ki **+ Hozzáadás videó** a videó ablak megnyitása</li><li>**Név** -név/nevezze (maximális hossza 100 karakternél.)</li><li>**Hivatkozás** – adja meg az URL-címet a webhely, amelyen a videó (YouTube vagy Vimeo)</li><li>**Miniatűr** – miniatűr feltöltése. PNG formátumúnak kell lennie, mérete pedig 533 x 324 képpont.</li></ul>          |

### <a name="artifact-examples-in-azure-marketplace"></a>Összetevő-példák az Azure Marketplace-en

A következő képernyőfelvétel egy Marketplace keresési eredmény egy példát mutat be.

![Marketplace-ajánlat keresési eredmény ](./media/azureapp-marketplace-example-browse.png)

Az alábbi képen látható, hogyan az ajánlat jelenjen meg a Marketplace-en, miután egy ügyfél a keresési eredményekben az ajánlat csempére kattint.

![Piactéri ajánlat keresési eredmény részletei](./media/azureapp-marketplace-example-details.png)

### <a name="artifact-examples-in-azure-portal"></a>Összetevő-példák az Azure Portalon

Az alábbi képernyőn rögzíti a show, hogyan jelenjen meg az ajánlat az Azure Portalon. Ebben a példában az alkalmazásra vonatkozó ajánlat található megkeresve **Marketplace > minden > fejlesztés + tesztelés > Jenkins**. A Jenkins az ajánlat mutatja egy emblémát, a jogcímre és a közzétevő megjelenített neve.

![Tallózási biztosít az Azure Portalon](./media/azureapp-portalbrowse-artifacts-jenkins.png)

A következő képernyőfelvétel-készítés az alkalmazással kapcsolatos részletes információkat jelenít meg, amikor egy felhasználó kijelöli a Jenkins.

![Az ajánlat részletei az Azure Portalon](./media/azureapp-portal-artifacts-jenkins-details.png)

### <a name="logo-guidelines"></a>Emblémával kapcsolatos irányelvek

A Cloud Partner portálra feltöltött összes emblémák útmutatást követik:

- Az Azure arculata egyszerű színpalettát használ. Tartsa számát az elsődleges és másodlagos színt az embléma alacsony.
- Az Azure Portal a témák színei fehér, és fekete. Kerülje a színek használata az emblémát, a háttér színe. Használjon színt, amellyel a emblémák neves az Azure Portalon. Javasoljuk az egyszerű alapszínek használatát. Ha áttetsző háttérrel használ, győződjön meg róla, hogy a emblémák szöveg nem fehér, fekete, vagy a kék.
- Az embléma háttér átmenetének nem használja.
- Szöveg-, akár a vállalat vagy a márkanév, elkerüli az embléma. Az embléma megjelenését és működését "egyszerű" kell lennie, és kerülje átmenetekhez.
- Az embléma nem stretch.

#### <a name="hero-logo"></a>Főképembléma

A Hero embléma megadása nem kötelező.

>[!IMPORTANT]
>A Hero embléma nem lehet törölni, a feltöltés után.

Hero emblémát a következőkre használhatja:

- Fekete, a fehér és áttetsző háttérrel nem engedélyezett.
- Ne használja a bármely világos szín, a háttérben az emblémát. A közzétevő megjelenített név, cím és az ajánlat hosszú összegzése fehér betűszínt jelennek meg, és a háttér előtt kell kiemelkedik csomagot.
- Ne használja a legtöbb szöveg, amikor az identitásfelügyelet az embléma. A közzétevő neve, csomag cím, az ajánlat hosszú összegzése és a Létrehozás gombra programozott módon vannak ágyazva az embléma esetén az ajánlatot.
- Tartalmazza a hero embléma a jobb oldalon egy nem használt négyszögletes területet. Az üres hely 415 x 100 képpont és 370 képpontos bal eltolását.

## <a name="lead-management"></a>Felügyeleti vezethet

A felügyeleti vezethet űrlapja lead felügyelet konfigurálása választható mező. Lead felügyelet konfigurálásához a legördülő listából válassza ki az érdeklődő cél. A következő képernyőfelvételen látható az elérhető célok.

![Válassza ki érdeklődő felügyeleti cél](./media/azureapp-marketplace-leadmgmt.png)

>[!TIP]
>Válassza ki az információ ikonra az üzenet jelenik meg: "Válassza ki az érdeklődőket a rendszer hol tárolja a rendszer. Ismerje meg, hogyan csatlakozhat a CRM-rendszerbe [Itt](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) . "


További információkért lásd: [ügyfélérdeklődések konfigurálása](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads).

## <a name="legal"></a>Jogi tudnivalók

A jogi képernyőn adja meg a szükséges minden egyes ajánlathoz jogi dokumentációját.

Adja meg az alábbi információkat:

- **Adatvédelmi szabályzat URL-címe** – adjon meg egy hivatkozást az alkalmazás adatvédelmi szabályzathoz.
- **Használati feltételek** – adja meg a használati feltételeket az alkalmazáshoz. Fogadja el ezeket a feltételeket, mielőtt az alkalmazás próbálkozhatnak ügyfelek szükségesek.

![Jogi űrlap](./media/azureapp-marketplace-legal.png)

## <a name="next-steps"></a>További lépések

[Támogatási lap](./cpp-support-tab.md)