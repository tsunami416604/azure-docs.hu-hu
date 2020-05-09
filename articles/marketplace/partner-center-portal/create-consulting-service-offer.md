---
title: Tanácsadási szolgáltatási ajánlat létrehozása – Microsoft kereskedelmi piactér
description: Ismerje meg, hogyan tehet közzé egy tanácsadói szolgáltatási ajánlatot Microsoft AppSource vagy Azure Marketplace-en a partner Center használatával.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 626dc5a7d1cd52182e68eb0d217b2ac4c653330e
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2020
ms.locfileid: "82994351"
---
# <a name="create-a-consulting-service-offer"></a>Tanácsadási szolgáltatásajánlat létrehozása

Ez a cikk azt ismerteti, hogyan tehet közzé egy tanácsadói szolgáltatást a [Microsoft AppSource](https://appsource.microsoft.com/) vagy az [Azure Marketplace](https://azuremarketplace.microsoft.com/)-en. A Microsoft [Dynamics 365](https://dynamics.microsoft.com/) -es és a AppSource-on futó Power platformon alapuló tanácsadói szolgáltatási ajánlatokat sorolja fel. Az Azure Marketplace-en Microsoft Azureon alapuló tanácsadói szolgáltatási ajánlatok listázása. A Kezdés előtt [hozzon létre egy kereskedelmi Piactéri fiókot a partner Centerben](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , ha még nem tette meg. Győződjön meg róla, hogy a fiókja regisztrálva van a kereskedelmi piactér programban.

Az ajánlat létrehozása előtt tekintse át az előfeltételeket a [tanácsadási szolgáltatás előfeltételei](https://docs.microsoft.com/azure/marketplace/partner-center-portal/consulting-service-prerequisites)című cikkben.

## <a name="publishing-benefits"></a>Közzétételi előnyök

A kereskedelmi piactéren való közzététel előnyei:

- Népszerűsítse vállalatát a Microsoft Brand használatával.
- Az Azure Marketplace-en több mint 100 000 000 Office 365-és Dynamics 365-felhasználó érhető el AppSource és több mint 200 000 szervezeten keresztül.
- Kiváló minőségű érdeklődőket fogadhat ezekből a Piactérről.
- A szolgáltatásait a Microsoft és a kereskedelmi csapatok népszerűsítik

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
2. A bal oldali navigációs menüben válassza a **kereskedelmi piactér** > **– Áttekintés**lehetőséget.
3. Az Áttekintés lapon válassza az **+ új ajánlat** > -**tanácsadási szolgáltatás**elemet.

    ![A bal oldali navigációs menü ábrázolása.](./media/new-offer-consulting-service.png)

>[!NOTE]
>Az ajánlat közzétételét követően a partner Centerben végzett módosítások csak az ajánlat ismételt közzététele után jelennek meg a kirakatokban. Győződjön meg arról, hogy a módosítások végrehajtása után mindig újra közzé kell tennie a módosításokat.

## <a name="new-offer"></a>Új ajánlat

Adja meg az **ajánlat azonosítóját**. Ez a fiókban található egyes ajánlatok egyedi azonosítója.

- Ez az azonosító a Piactéri ajánlathoz tartozó webcímek ügyfelei számára látható.
- Csak kisbetűket és számokat használjon. Tartalmazhat kötőjeleket és aláhúzásokat, de nem tartalmazhat szóközt, és legfeljebb 50 karakter hosszú lehet. Ha például a **test-Offer-1**értéket adja meg, az ajánlat webes címe lesz `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- Az ajánlat azonosítója a **Létrehozás**gombra kattintva nem módosítható.

Adjon meg egy **ajánlat-aliast**. Ez a partner Centerben az ajánlathoz használt név.

- Ez a név nem használatos a piactéren, és nem egyezik meg az ajánlat nevével és az ügyfelek számára megjelenített egyéb értékekkel.
- Az ajánlat aliasa nem módosítható a **Létrehozás**gombra kattintva.

Válassza a **Létrehozás** lehetőséget az ajánlat létrehozásához és a folytatáshoz.

## <a name="offer-setup"></a>Ajánlat beállítása

### <a name="connect-lead-management"></a>Az érdeklődők felügyeletének összekötése

Ha az ajánlatot a piactéren, a partner centeren keresztül teszi közzé, csatlakoznia _kell_ az Ügyfélkapcsolat-kezelési (CRM) vagy a Marketing Automation rendszerhez. Ez lehetővé teszi az ügyfelek kapcsolattartási adatainak megszerzését, amint valaki érdeklődik a termék iránt, vagy használja a terméket.

1. Válassza a **Kapcsolódás** lehetőséget, és adja meg, hová szeretné elküldeni a felhasználói érdeklődőket. A partner Center a következő rendszereket támogatja:

    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) ügyfél-engagement
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Ha a CRM-rendszer nem szerepel a fentiekben, az [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) vagy a [https-végpont](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) használatával tárolhatja az ügyfelek vezető adatait, majd exportálhatja az adatait a CRM-rendszerbe.

2. Az ajánlat összekötése a vezető célhoz a partner Centerben való közzététel során.
3. Győződjön meg arról, hogy a vezető célhoz való kapcsolódás megfelelően van konfigurálva. Miután közzétette a partner Centerben, érvényesítjük a kapcsolatot, és elküldünk egy tesztelési érdeklődőt. Miközben az ajánlat előzetes verziójának megtekintése előtt megtekinti az ajánlatot, tesztelheti az érdeklődői kapcsolatokat is, ha az előnézeti környezetben próbálja megvásárolni az ajánlatot.
4. Győződjön meg arról, hogy a vezető célhely kapcsolata frissül, így nem veszíti el az érdeklődőket.

Íme néhány további érdeklődő felügyeleti erőforrás:

- [Az érdeklődők kezelése – áttekintés](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Gyakori kérdések az érdeklődők kezeléséről](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Az érdeklődők konfigurálásának gyakori hibái](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#publishing-config-errors)
- Az [érdeklődők kezelése – áttekintés](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (ellenőrizze, hogy ki van-e kapcsolva az előugró ablakok blokkolása)

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

### <a name="properties"></a>Tulajdonságok

Ezen a lapon állíthatja be azt az elsődleges terméket, amelyet a tanácsadási szolgáltatás a legjobban fedez, a tanácsadási szolgáltatások típusának beállítása és a megfelelő termékek kiválasztása.

1. Válasszon ki egy **elsődleges terméket** a legördülő listából.
2. Válassza ki a **tanácsadási szolgáltatás típusát** a legördülő listából:

    - **Értékelés**: az ügyfél környezetének kiértékelése a megoldás alkalmazhatóságának meghatározásához, valamint a költséghatékony és az időzítési becslés megadásához.
    - **Tájékoztatás**: egy megoldás vagy egy tanácsadói szolgáltatás bevezetése, amely az ügyfelek érdeklődését keretrendszerek, bemutatók és felhasználói példák használatával hívja meg.
    - **Implementáció**: teljes telepítés, amely teljesen működőképes megoldást eredményez. Legfeljebb két hétig vagy kevesebbben megvalósítható megoldásokra lehet korlátozni.
    - A **koncepció igazolása**: korlátozott hatókörű implementáció annak megállapítására, hogy A megoldás megfelel-e az ügyfél igényeinek.
    - **Workshop**: az ügyfél telephelyén folytatott interaktív részvétel. Olyan képzéseket, tájékoztatókat, felméréseket és bemutatókat is tartalmazhat, amelyek az ügyfél által tárolt adatszolgáltatásra vagy környezetre épülnek.

3. Ha az **Azure**elsődleges termékét választotta, válasszon ki legfeljebb három **megoldási területet**. Ezek megkönnyítik az Azure Marketplace-en lévő ügyfelek számára az ajánlat megtalálását. Ha nem az Azure-t választotta, hagyja ki ezt a lépést.
4. Ha az Azure-tól _eltérő_ elsődleges terméket jelölt ki, válassza ki a legfeljebb három **alkalmazható terméket**. Ezek megkönnyítik a AppSource ügyfeleinek az ajánlat megtalálását. Részletekért lásd: [Microsoft AppSource Consulting Service listázási irányelvei](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
5. Válasszon ki legfeljebb hat **iparágat** , amelyre az ajánlat vonatkozik. Ez megkönnyíti az ügyfelek számára az ajánlat megtalálását.
6. Akár három olyan **kompetenciát** is felvehet, amelyet a vállalat a tanácsadási szolgáltatás ajánlatában jelenít meg. Legalább egy kompetenciát meg kell adni, kivéve az Azure Expert MSP és az Azure hálózati MSP.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="offer-listing"></a>Ajánlati lista

Itt megadhatja a piactéren megjelenő ajánlat részleteit. Ebbe beletartozik az ajánlat neve, leírása, képei és így tovább. Ügyeljen arra, hogy az ajánlat konfigurálása során kövesse a [kereskedelmi Piactéri minősítési szabályzatok lapon](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) részletezett szabályzatokat.

> [!NOTE]
> Az ajánlat részletei nem szükségesek angolul, ha az ajánlat leírása a kifejezéssel kezdődik, &quot;ez az alkalmazás csak [nem angol nyelven] érhető el. &quot; Azt is megteheti, hogy hasznos hivatkozást biztosít, hogy olyan nyelven kínálja a tartalmat, amely különbözik az ajánlati lista részleteiben használt nyelvtől.

### <a name="name"></a>Name

Az itt megadott név az ajánlat címeként jelenik meg. Ez a mező előre ki van töltve az ajánlat- **alias** mezőben megadott szöveggel az ajánlat létrehozásakor. A nevet később módosíthatja.

A név:

- Védjeggyel is rendelkezhet (és a védjegyek és a szerzői jogi szimbólumok is szerepelhetnek).
- Nem lehet hosszabb 50 karakternél.
- Nem szerepelhetnek hangulatjelek.

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
- Ne használja a szolgáltatásait és funkcióit a termék értékesítéséhez. Ehelyett az ajánlat által biztosított értékre kell összpontosítania.
- Az iparágra jellemző szókincset vagy juttatáson alapuló szövegezést érdemes használni.

Annak érdekében, hogy az ajánlat leírása jobban megtörténjen, használja a Rich Text Editort a formázás alkalmazásához.

![A Rich Text Editor használata](./media/rich-text-editor.png)

| <center>Szöveg formátumának módosítása | <center>Felsorolásjelek vagy számozás hozzáadása | <center>Szöveg behúzásának hozzáadása vagy eltávolítása |
| --- | --- | --- |
| <center>![A Rich Text Editor használata szöveg formátumának módosításához](./media/text-editor3.png) |  <center>![A Rich Text Editor használata a listák hozzáadásához](./media/text-editor4.png) |  <center>![A Rich Text Editor használata a behúzáshoz](./media/text-editor5.png) |

### <a name="keywords"></a>Kulcsszavak

Adjon meg legfeljebb három olyan keresési kulcsszót, amely az elsődleges termékhez és a tanácsadási szolgáltatáshoz kapcsolódik. Így könnyebben megtalálhatja az ajánlatát.

### <a name="duration"></a>Időtartam

Állítsa be a részvétel várható időtartamát az ügyféllel.

### <a name="contact-information"></a>Kapcsolattartási adatok

Meg kell adnia egy **elsődleges** és egy **másodlagos kapcsolat**nevét, e-mail-címét és telefonszámát. Ezek az adatok nem jelennek meg az ügyfelek számára. A Microsoft számára elérhető, és a Cloud Solution Provider (CSP) partnerei számára is biztosítható.

### <a name="supporting-documents"></a>Támogató dokumentumok

Akár három (de legalább egy) támogató PDF-dokumentumot is hozzáadhat az ajánlatához.

### <a name="marketplace-images"></a>Marketplace-rendszerképek

Adja meg az ajánlathoz használni kívánt emblémákat és képeket. Minden képnek. png formátumúnak kell lennie. A rendszer elutasítja a homályos képeket.

>[!Note]
>Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat https://upload.xboxlive.com nem blokkolja a partner központ által használt szolgáltatást.

#### <a name="store-logos"></a>Emblémák tárolása

Adja meg az ajánlat emblémájának PNG-fájlját a következő képpont méretek mindegyikében:

- **Kicsi (48 x 48)**
- **Nagyméretű (216 x 216)**

Az összes emblémát meg kell adni, és a Piactéri lista különböző helyein használják.

#### <a name="screenshots-optional"></a>Képernyőképek (nem kötelező)

Akár öt képernyőképet is felvehet, amelyek bemutatják, hogyan működik az ajánlata. Mindegyiknek 1280 x 720 képpont méretűnek és PNG formátumúnak kell lennie.

#### <a name="videos-optional"></a>Videók (nem kötelező)

Akár négy videót is hozzáadhat, amelyek bemutatják az ajánlatát. Adja meg a videó nevét, a webcímet (URL-címét), valamint a videó miniatűr PNG-képét 1280 x 720 képpont méretűre.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="pricing-and-availability"></a>Díjszabás és rendelkezésre állás

Itt olyan elemeket határozhat meg, mint például a díjszabás, a piac és a titkos kulcs.

1. **Piac**: állítsa be, hogy az ajánlata milyen piacon lesz elérhető. Ajánlat esetén csak egy piacot választhat ki.
    1. A Egyesült Államok vagy a Canada piacain válassza az **állapotok módosítása** (vagy **tartományok**) lehetőséget annak megadásához, hogy az ajánlat hol lesz elérhető.
2. **Előnézet célközönsége**: konfigurálja az ajánlat privát célközönségének beállításához használt **elrejtési kulcsot** .
3. **Díjszabás**: adja meg, hogy az ajánlat **ingyenes** vagy **fizetős** ajánlat-e.

    > [!NOTE]
    > A tanácsadási szolgáltatás ajánlata kizárólag a listázásra szolgál. Minden tranzakció közvetlenül, a kereskedelmi piactéren kívül fog történni.

4. Fizetős ajánlat esetén határozza meg az **árat és a pénznemet** , valamint azt, hogy az ár **rögzített** vagy **becsült**. Ha a becsült érték van megadva, a leírásban meg kell adnia, hogy milyen tényezők befolyásolják a díjszabást.
5. A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

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
