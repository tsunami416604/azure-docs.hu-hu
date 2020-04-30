---
title: Tanácsadási szolgáltatási ajánlat létrehozása a partner Centerben – Azure Marketplace
description: Ismerje meg, hogyan tehet közzé egy tanácsadói szolgáltatást az Azure Marketplace-en vagy a AppSource a partner Center használatával.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: eff37750f0580a28c9644ee1ffb7fe4e95038709
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81869797"
---
# <a name="create-a-consulting-service-offer"></a>Tanácsadási szolgáltatási ajánlat létrehozása

> [!IMPORTANT]
> A tanácsadási szolgáltatási ajánlatok felügyeletét a Cloud Partner Portalról a partneri központba helyezi át. Az ajánlatok migrálása előtt kövesse az [Azure-ban és a Dynamics 365 Consulting Service-ajánlatban](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/consulting-services/cloud-partner-portal-consulting-services-publishing-offer) található utasításokat az ajánlatok kezeléséhez Cloud Partner Portal.

Ez a cikk azt ismerteti, hogyan tehet közzé egy tanácsadói szolgáltatást az [Azure Marketplace](https://azuremarketplace.microsoft.com/) -en vagy a [AppSource](https://appsource.microsoft.com/). A Microsoft [Dynamics 365](https://dynamics.microsoft.com/) -es és a AppSource-on futó Power platformon alapuló tanácsadói szolgáltatási ajánlatokat sorolja fel. Az Azure Marketplace-en Microsoft Azureon alapuló tanácsadói szolgáltatási ajánlatok listázása.

Ha az Azure Marketplace-en vagy a AppSource Consulting Servicesben szeretne tanácsadói szolgáltatási ajánlatot létrehozni, először [rendelkeznie kell egy kiadói fiókkal a partner Centerben](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account), és a fiókját regisztrálni kell a kereskedelmi piactér programban. Az ajánlat létrehozása előtt tekintse át az előfeltételeket a [tanácsadási szolgáltatás előfeltételei](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites)című cikkben.

## <a name="publishing-benefits"></a>Közzétételi előnyök

A kereskedelmi piactéren való közzététel előnyei:

- Népszerűsítse vállalatát a Microsoft Brand használatával.
- Az Azure Marketplace-en több mint 100 000 000 Office 365-és Dynamics 365-felhasználó érhető el AppSource és több mint 200 000 szervezeten keresztül.
- Kiváló minőségű érdeklődőket fogadhat ezekből a Piactérről.
- A szolgáltatásait a Microsoft és a kereskedelmi csapatok népszerűsítik

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

A fent ismertetett követelmények teljesítése után kövesse az alábbi lépéseket a tanácsadói szolgáltatási ajánlat létrehozásához.

1. Jelentkezzen be a [partner Center](https://partner.microsoft.com)webhelyre, majd válassza az **irányítópult** lehetőséget a felső menüben.
2. A bal oldali navigációs sávon válassza a **kereskedelmi piactér**lehetőséget, majd az **Áttekintés**lehetőséget.

    :::image type="content" source="media/cs-menu-overview.png" alt-text="A kereskedelmi Piactéri menü bemutatása":::

3. Válassza az **+ új ajánlat**, majd a **tanácsadási szolgáltatás**elemet.

    :::image type="content" source="media/cs-menu-newoffer.png" alt-text="Egy új ajánlat létrehozásához ábrázolja a gombot.":::

4. Adja meg az **ajánlat azonosítóját**. Ez a fiókban található egyes ajánlatok egyedi azonosítója.

    - Ez az azonosító a Piactéri ajánlathoz tartozó webcímek ügyfelei számára látható.
    - Csak kisbetűket, számokat, kötőjeleket és aláhúzást használjon, de nem tartalmazhat szóközt. A hossz legfeljebb 50 karakter hosszú lehet. Ha például a **test-Offer-1**értéket adja meg, az ajánlat URL-címe `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`a következő lesz:.
    - Az ajánlat azonosítója a **create (létrehozás**) gombra kattintva módosítható&#39;t.

5. Adjon meg egy **ajánlat-aliast**. Ez az ajánlat a partner Centerben való hivatkozásához használt név.

    - Ezt a nevet nem&#39;t használja a piactéren. Az ajánlat neve és az ügyfelek számára megjelenített egyéb értékek eltérnek.&#39; Ennek a mezőnek a használatával olyan nevet rendelhet az ajánlathoz, amely hasznos lehet az ajánlat belső azonosításához; nem jelenik meg az ügyfelek számára.
    - Az ajánlat aliasa&#39;t a **create (létrehozás**) lehetőség kiválasztása után módosítható.

A két érték megadása után a **Létrehozás** gombra kattintva folytathatja az **ajánlat beállítása** lapot.

## <a name="offer-setup"></a>Ajánlat beállítása

Az ajánlat-azonosító és az ajánlati alias megadása után a partner Center létrehoz egy draft-ajánlatot, és megjeleníti az **ajánlat beállítása** lapot. Az ajánlat beállításához kövesse az alábbi lépéseket.

### <a name="connect-lead-management"></a>Az érdeklődők felügyeletének összekötése

Ha az ajánlatot a piactéren, a partner centeren keresztül teszi közzé, csatlakoznia _kell_ az Ügyfélkapcsolat-kezelési (CRM) vagy a Marketing Automation rendszerhez. Ez lehetővé teszi az ügyfelek kapcsolattartási adatainak megszerzését, amint valaki érdeklődik a termék iránt, vagy használja a terméket.

1. Válassza a **Kapcsolódás** lehetőséget, és adja meg, hová szeretné elküldeni a felhasználói érdeklődőket. A partner Center a következő rendszereket támogatja:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) ügyfél-engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Ha a CRM rendszer nem a fent felsorolt&#39;t, az [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) vagy a [https-végpont](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) használatával tárolhatja az ügyfelek vezető adatait, majd exportálhatja az adatait a CRM rendszerbe.

2. Az ajánlat összekötése a vezető célhoz a partner Centerben való közzététel során.
3. Győződjön meg arról, hogy a vezető célhoz való kapcsolódás megfelelően van konfigurálva. Miután közzétette a partner Centerben,&#39;ll érvényesíteni a kapcsolatot, és elküld egy tesztelési érdeklődőt. Miközben az ajánlat előzetes verziójának megtekintése előtt megtekinti az ajánlatot, tesztelheti az érdeklődői kapcsolatokat is, ha az előnézeti környezetben próbálja megvásárolni az ajánlatot.
4. Győződjön meg arról, hogy a vezető célhely kapcsolata frissítve marad, így nem&#39;el az érdeklődőket.

Íme néhány további érdeklődő felügyeleti erőforrás:

- [Az érdeklődők kezelése – áttekintés](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Gyakori kérdések az érdeklődők kezeléséről](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Az érdeklődők konfigurálásának gyakori hibái](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- Az [érdeklődők kezelése – áttekintés](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (ellenőrizze, hogy ki van-e kapcsolva az előugró ablakok blokkolása)

A következő szakaszra, a Tulajdonságok elemre kattintva válassza a **Piszkozat mentése** elemet.

### <a name="properties"></a>Tulajdonságok

Ezen a lapon állíthatja be azt az elsődleges terméket, amelyet a tanácsadási szolgáltatás a legjobban fedez, a tanácsadási szolgáltatások típusának beállítása és a megfelelő termékek kiválasztása.

1. Válasszon ki egy **elsődleges terméket** a legördülő listából.
2. Válassza ki a **tanácsadási szolgáltatás típusát** a legördülő listából:

    - **Értékelés** : egy ügyfél&#39;s környezet kiértékelése a megoldás alkalmazhatóságának meghatározásához, valamint a költséghatékony és az időzítési becslés megadásához.
    - **Tájékoztatás** : egy megoldás vagy egy tanácsadói szolgáltatás bevezetése, amely az ügyfelek érdeklődését keretrendszerek, bemutatók és felhasználói példák használatával hívja meg.
    - **Implementáció** : teljes telepítés, amely teljesen működőképes megoldást eredményez. Legfeljebb két hétig vagy kevesebbben megvalósítható megoldásokra lehet korlátozni.
    - A **koncepció igazolása** : korlátozott hatókörű implementáció annak megállapítására, hogy A megoldás megfelel-e az ügyfél igényeinek.
    - **Workshop** : az ügyfél&#39;s telephelyén folytatott interaktív részvétel. Olyan képzéseket, tájékoztatókat, értékeléseket és bemutatókat tartalmazhat, amelyek az ügyfél&#39;s-ra vagy környezetre épülnek.

1. Ha az **Azure**elsődleges termékét választotta, válasszon ki legfeljebb három **megoldási területet**. Ezek megkönnyítik az Azure Marketplace-en lévő ügyfelek számára az ajánlat megtalálását. Ha az Azure-t választja&#39;helytelen, hagyja ki ezt a lépést.
2. Ha az Azure-tól _eltérő_ elsődleges terméket jelölt ki, válassza ki a legfeljebb három **alkalmazható terméket**. Ezek megkönnyítik a AppSource ügyfeleinek az ajánlat megtalálását. Részletekért lásd: [Microsoft AppSource Consulting Service listázási irányelvei](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
3. Válasszon ki legfeljebb hat **iparágat** , amelyre az ajánlat vonatkozik. Ez megkönnyíti az ügyfelek számára az ajánlat megtalálását.
4. Akár három olyan **kompetenciát** is felvehet, amelyet a vállalat a tanácsadási szolgáltatás ajánlatában jelenít meg. Legalább egy kompetenciára van szükség, kivéve az Azure szakértői MSP&#39;s és az Azure hálózati MSP&#39;s-t.

Válassza a **Piszkozat mentése** lehetőséget, mielőtt továbblépne a következő szakaszra.

## <a name="offer-listing"></a>Ajánlati lista

Itt&#39;ll megadnia a piactéren megjelenő ajánlat részleteit. Ebbe beletartozik az ajánlat neve, leírása, képei és így tovább. Győződjön meg arról, hogy az ajánlat konfigurálása során a [Microsoft&#39;s Policy oldalon](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) részletesen ismertetett szabályzatok jelennek meg.

> [!NOTE]
> Az ajánlat részleteit nem&#39;t kell angol nyelven megadnia, ha az ajánlat leírása a &quot;kifejezéssel kezdődik, ez az alkalmazás csak [nem angol nyelven] érhető el. &quot; A&#39;s is rendben van, hogy hasznos hivatkozást biztosítson, hogy olyan nyelven kínálja a tartalmat,&#39;amely különbözik az ajánlati lista részleteiben használt nyelvtől.

### <a name="name"></a>Name (Név)

Az itt megadott név az ajánlat címeként jelenik meg. Ez a mező előre ki van töltve az ajánlat- **alias** mezőben megadott szöveggel az ajánlat létrehozásakor. A nevet később módosíthatja.

A név:

- Védjeggyel is rendelkezhet (és a védjegyek és a szerzői jogi szimbólumok is szerepelhetnek).
- Legfeljebb 50 karakter hosszú lehet&#39;.
- A&#39;hangulatjelek is tartalmazhat.

### <a name="search-results-summary"></a>Keresési eredmények összegzése

Adja meg az ajánlat rövid leírását. Ez akár 100 karakter hosszú lehet, és a piactér keresési eredményeiben használható.

### <a name="description"></a>Leírás

Adja meg az ajánlat hosszabb leírását, amely legfeljebb 3 000 karakter hosszú lehet. Ez jelenik meg az ügyfelek számára a Piactéri listák áttekintésében.

A leírásban vegyen fel egyet vagy többet a következők közül:

- Az ajánlat által biztosított érték és kulcs előnye
- Kategória-vagy iparági társulások, vagy mindkettő
- Alkalmazáson belüli vásárlási lehetőségek
- Minden szükséges közzététel

Íme néhány tipp a Leírás írásához:

- A Leírás első néhány mondatában egyértelműen ismertesse az ajánlat értékét. Adja meg a következő elemeket:
  - Az ajánlat leírása.
  - Az ajánlat által előnyben részesülő felhasználó típusa.
  - Az ügyfélnek szüksége van vagy kiadja az ajánlat címét.
- Ne feledje, hogy az első néhány mondat a keresési eredmények között jelenhet meg.
- Nem&#39;t a termék értékesítéséhez szükséges funkciókra és funkciókra támaszkodhat. Ehelyett az ajánlat által biztosított értékre kell összpontosítania.
- Az iparágra jellemző szókincset vagy juttatáson alapuló szövegezést érdemes használni.

Annak érdekében, hogy a Leírás jobban megtörténjen, a Rich Text Editor használatával formázhatja a leírást. A Rich Text Editor lehetővé teszi számok, felsorolásjelek, félkövér, dőlt és behúzások hozzáadását, hogy a Leírás olvashatóbb legyen.

:::image type="content" source="media/cs-rich-text-editor.png" alt-text="Bemutatja a Rich Text Editort az ajánlat leírásának írásához." border="false":::

### <a name="keywords"></a>Kulcsszavak

Adjon meg legfeljebb három olyan keresési kulcsszót, amely az elsődleges termékhez és a tanácsadási szolgáltatáshoz kapcsolódik. Így könnyebben megtalálhatja az ajánlatát.

### <a name="duration"></a>Időtartam

Állítsa be a részvétel várható időtartamát az ügyféllel.

### <a name="contact-information"></a>Kapcsolattartási adatok

Meg kell adnia egy **elsődleges** és egy **másodlagos kapcsolat**nevét, e-mail-címét és telefonszámát. Ez az információ nem&#39;t mutat az ügyfeleknek. A Microsoft számára elérhető, és a Cloud Solution Provider (CSP) partnerei számára is biztosítható.

### <a name="supporting-documents"></a>Támogató dokumentumok

Akár három (de legalább egy) támogató PDF-dokumentumot is hozzáadhat az ajánlatához.

### <a name="marketplace-images"></a>Marketplace-rendszerképek

Adja meg az ajánlathoz használni kívánt emblémákat és képeket. Minden képnek. png formátumúnak kell lennie. A rendszer elutasítja a homályos képeket.

>[!Note]
>Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat https://upload.xboxlive.com nem blokkolja a partner központ által használt szolgáltatást.

#### <a name="store-logos"></a>Emblémák tárolása

Adja meg az ajánlathoz tartozó. png fájlokat&#39;s emblémát a következő képpont méretek mindegyikében:

- **Kicsi (48 x 48)**
- **Nagyméretű (216 x 216)**

Az összes emblémát meg kell adni, és a Piactéri lista különböző helyein használják.

#### <a name="screenshots-optional"></a>Képernyőképek (nem kötelező)

Akár öt képernyőképet is felvehet, amelyek bemutatják, hogyan működik az ajánlata. Mindegyiknek 1280 x 720 képpont méretűnek és. png formátumúnak kell lennie.

#### <a name="videos-optional"></a>Videók (nem kötelező)

Akár négy videót is hozzáadhat, amelyek bemutatják az ajánlatát. Adja meg a videó&#39;s nevét, a webcímet (URL) és a videó miniatűr. png képét 1280 x 720 képpont méretben.

A következő szakasz, a díjszabás és a rendelkezésre állás folytatása előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="pricing-and-availability"></a>Díjszabás és rendelkezésre állás

Itt olyan elemeket határozhat meg, mint például a díjszabás, a piac és a titkos kulcs.

1. **Piac**: állítsa be, hogy az ajánlata milyen piacon lesz elérhető. Ajánlat esetén csak egy piacot választhat ki.
    1. A Egyesült Államok vagy a Canada piacain válassza az **állapotok módosítása** (vagy **tartományok**) lehetőséget annak megadásához, hogy az ajánlat hol lesz elérhető.
2. **Előnézet célközönsége**: konfigurálja az ajánlat privát célközönségének beállításához használt **elrejtési kulcsot** .
3. **Díjszabás**: adja meg, hogy az ajánlat **ingyenes** vagy **fizetős** ajánlat-e.

    > [!NOTE]
    > A tanácsadási szolgáltatás ajánlata kizárólag a listázásra szolgál. Minden tranzakció közvetlenül, a kereskedelmi piactéren kívül fog történni.

4. Fizetős ajánlat esetén határozza meg az **árat és a pénznemet** , valamint azt, hogy az ár **rögzített** vagy **becsült**. Ha a becsült érték van megadva, a leírásban meg kell adnia, hogy milyen tényezők befolyásolják a díjszabást.
5. Válassza a **Piszkozat mentése**lehetőséget.

## <a name="review-and-publish"></a>Áttekintés és közzététel

Miután elvégezte az ajánlat összes szükséges részét, elküldheti ajánlatát az áttekintéshez és a közzétételhez.

1. Ha készen áll a tanácsadási szolgáltatás ajánlatának közzétételére, kattintson **a felülvizsgálat és közzététel**elemre.
2. Tekintse át a végső beküldési oldal részleteit.
3. Ha szükséges, jegyezze fel a minősítési csapatot, ha úgy véli, hogy az ajánlat bármely részlete magyarázatot kér.
4. Ha elkészült, válassza a **Küldés**lehetőséget.
5. Az **ajánlat áttekintése** oldalon látható, hogy az ajánlat mely közzétételi fázisban található.

Ha további információra van szüksége arról, hogy mennyi ideig számíthat az ajánlat az egyes közzétételi fázisokban való használatra, tekintse [meg a kereskedelmi Piactéri ajánlat közzétételi állapotának ellenőrzését](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status)ismertető szakaszt.

## <a name="update-your-existing-consulting-service-offers"></a>Meglévő tanácsadói szolgáltatási ajánlatok frissítése

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
