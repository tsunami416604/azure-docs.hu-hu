---
title: Tanácsadási szolgáltatási ajánlat létrehozása – Microsoft kereskedelmi piactér
description: Ismerje meg, hogyan tehet közzé egy tanácsadói szolgáltatási ajánlatot Microsoft AppSource vagy Azure Marketplace-en a partner Center használatával.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 03fa302d2e8aa173419912b99d60734c7a4e0356
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814264"
---
# <a name="create-a-consulting-service-offer"></a>Tanácsadási szolgáltatásajánlat létrehozása

Ez a cikk azt ismerteti, hogyan tehet közzé egy tanácsadói szolgáltatást a [Microsoft AppSource](https://appsource.microsoft.com/) vagy az [Azure Marketplace](https://azuremarketplace.microsoft.com/)-en. A Microsoft [Dynamics 365](https://dynamics.microsoft.com/) -es és a AppSource-on futó Power platformon alapuló tanácsadói szolgáltatási ajánlatokat sorolja fel. Az Azure Marketplace-en Microsoft Azureon alapuló tanácsadói szolgáltatási ajánlatok listázása. A Kezdés előtt [hozzon létre egy kereskedelmi Piactéri fiókot a partner Centerben](create-account.md) , ha még nem tette meg. Győződjön meg róla, hogy a fiókja regisztrálva van a kereskedelmi piactér programban.

Az ajánlat létrehozása előtt tekintse át az előfeltételeket a [tanácsadási szolgáltatás előfeltételei](consulting-service-prerequisites.md)című cikkben.

## <a name="publishing-benefits"></a>Közzétételi előnyök

A kereskedelmi piactéren való közzététel előnyei:

- Népszerűsítse vállalatát a Microsoft Brand használatával.
- Az Azure Marketplace-en több mint 100 000 000 Office 365-és Dynamics 365-felhasználó érhető el AppSource és több mint 200 000 szervezeten keresztül.
- Kiváló minőségű érdeklődőket fogadhat ezekből a Piactérről.
- A szolgáltatásait a Microsoft és a kereskedelmi csapatok népszerűsítik

## <a name="create-a-new-offer"></a>Új ajánlat létrehozása

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).
2. A bal oldali navigációs menüben válassza a **kereskedelmi piactér**  >  **– Áttekintés**lehetőséget.
3. Az Áttekintés lapon válassza az **+ új ajánlat**-  >  **tanácsadási szolgáltatás**elemet.

    ![A bal oldali navigációs menü ábrázolása.](./media/new-offer-consulting-service.png)

>[!NOTE]
>Az ajánlat közzétételét követően a csak az ajánlat újbóli közzététele után az online áruházakban megjelenő szerkesztési lehetőségek jelennek meg a partner Centerben. Győződjön meg arról, hogy a módosítások végrehajtása után mindig újra közzé kell tennie a módosításokat.

## <a name="new-offer"></a>Új ajánlat

Adja meg az **ajánlat azonosítóját**. Ez a fiókban található egyes ajánlatok egyedi azonosítója.

- Ez az azonosító a Piactéri ajánlathoz tartozó webcímek ügyfelei számára látható.
- Csak kisbetűket és számokat használjon. Tartalmazhat kötőjeleket és aláhúzásokat, de nem tartalmazhat szóközt, és legfeljebb 50 karakter hosszú lehet. Ha például a **test-Offer-1**értéket adja meg, az ajánlat webes címe lesz `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- Az ajánlat azonosítója a **Létrehozás**gombra kattintva nem módosítható.

Adjon meg egy **ajánlat-aliast**. Ez a partner Centerben az ajánlathoz használt név.

- Ez a név nem használatos a piactéren, és nem egyezik meg az ajánlat nevével és az ügyfelek számára megjelenített egyéb értékekkel.
- Az ajánlat aliasa nem módosítható a **Létrehozás**gombra kattintva.

Válassza a **Létrehozás** lehetőséget az ajánlat létrehozásához és a folytatáshoz.

## <a name="offer-setup"></a>Ajánlat beállítása

### <a name="connect-lead-management"></a>Az érdeklődők felügyeletének összekötése

Ha az ajánlatot a piactéren, a partner centeren keresztül teszi közzé, csatlakoznia _kell_ az Ügyfélkapcsolat-kezelési (CRM) vagy a Marketing Automation rendszerhez. Ez lehetővé teszi az ügyfelek kapcsolattartási adatainak megszerzését, amint valaki érdeklődik a termék iránt, vagy használja a terméket.

1. Válassza a **Kapcsolódás** lehetőséget, és adja meg, hová szeretné elküldeni a felhasználói érdeklődőket. A partner Center a következő rendszereket támogatja:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) ügyfél-engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Ha a CRM-rendszer nem szerepel a fentiekben, az [Azure Table](commercial-marketplace-lead-management-instructions-azure-table.md) vagy a [https-végpont](commercial-marketplace-lead-management-instructions-https.md) használatával tárolhatja az ügyfelek vezető adatait, majd exportálhatja az adatait a CRM-rendszerbe.

2. Az ajánlat összekötése a vezető célhoz a partner Centerben való közzététel során.
3. Győződjön meg arról, hogy a vezető célhoz való kapcsolódás megfelelően van konfigurálva. Miután közzétette a partner Centerben, érvényesítjük a kapcsolatot, és elküldünk egy tesztelési érdeklődőt. Miközben az ajánlat előzetes verziójának megtekintése előtt megtekinti az ajánlatot, tesztelheti az érdeklődői kapcsolatokat is, ha az előnézeti környezetben próbálja megvásárolni az ajánlatot.
4. Győződjön meg arról, hogy a vezető célhely kapcsolata frissül, így nem veszíti el az érdeklődőket.

Íme néhány további érdeklődő felügyeleti erőforrás:

- [Az érdeklődők kezelése – áttekintés](commercial-marketplace-get-customer-leads.md)
- [Érdeklődői felügyelet – gyakori kérdések](../lead-management-for-cloud-marketplace.md#frequently-asked-questions)
- [Gyakori vezető konfigurációs hibák](../lead-management-for-cloud-marketplace.md#publishing-config-errors))
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

3. Ha az **Azure** -t elsődleges termékként választotta, akkor legfeljebb három **megoldási területet**választhat ki. Ezek megkönnyítik az Azure Marketplace-en lévő ügyfelek számára az ajánlat megtalálását. Ha nem az Azure-t választotta, hagyja ki ezt a lépést.

    - Elemzés
    - Alkalmazások modernizálása
    - Archívum
    - Mesterséges intelligencia és gépi tanulás
    - Backup
    - Big Data
    - Adatplatform
    - Adatközpont-kezelés
    - DevOps
    - Vészhelyreállítás
    - Identitás
    - Eszközök internetes hálózata
    - Áttelepítés
    - Hálózatkezelés
    - Biztonság
    - Tárolás

1. Ha az **Azure** -t elsődleges termékként választotta, lehetősége van legfeljebb hat **iparág**kiválasztására. Ezek megkönnyítik az Azure Marketplace-en lévő ügyfelek számára az ajánlat megtalálását. Tekintse meg az iparági listán az ajánlott [eljárásokat felsoroló](../gtm-offer-listing-best-practices.md)témakört. Ha nem az Azure-t választotta, hagyja ki ezt a lépést.
1. Ha az Azure-tól *eltérő* elsődleges terméket jelölt ki, válassza ki a legfeljebb három **alkalmazható terméket**. Ezek megkönnyítik a AppSource ügyfeleinek az ajánlat megtalálását. Részletekért lásd: [Microsoft AppSource Consulting Service listázási irányelvei](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409) (PDF).
1. Ha az **Azure-tól** *eltérő* elsődleges terméket jelölt ki, akkor lehetősége van legfeljebb két iparág és két **vertikális** beállítás kiválasztására minden iparágban. Ezek megkönnyítik a AppSource ügyfeleinek az ajánlat megtalálását. Tekintse meg az iparági és vertikális tudnivalókat az [ajánlott eljárások](../gtm-offer-listing-best-practices.md)listájában.
1. Akár három olyan **kompetenciát** is felvehet, amelyet a vállalat a tanácsadási szolgáltatás ajánlatában jelenít meg. Legalább egy kompetenciát meg kell adni, kivéve az Azure Expert MSP és az Azure hálózati MSP.

A folytatás előtt válassza a **Piszkozat mentése** lehetőséget.

## <a name="offer-listing"></a>Ajánlati lista

Itt megadhatja a piactéren megjelenő ajánlat részleteit. Ebbe beletartozik az ajánlat neve, leírása, képei és így tovább. Ügyeljen arra, hogy az ajánlat konfigurálása során kövesse a [kereskedelmi Piactéri minősítési szabályzatok lapon](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services) részletezett szabályzatokat.

> [!NOTE]
> Az ajánlat részletei nem szükségesek angolul, ha az ajánlat leírása a kifejezéssel kezdődik, &quot; Ez az alkalmazás csak [nem angol nyelven] érhető el. &quot; Azt is megteheti, hogy hasznos hivatkozást biztosít, hogy olyan nyelven kínálja a tartalmat, amely különbözik az ajánlati lista részleteiben használt nyelvtől.

Íme egy példa arra, hogyan jelennek meg az ajánlati információk az Azure Marketplace-en (a felsorolt árak kizárólag a tényleges költségekkel kapcsolatos célokat szolgálják):

:::image type="content" source="media/example-consulting-service-offer.png" alt-text="Bemutatja, hogyan jelenik meg az ajánlat az Azure piactéren.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Embléma
2. Ár
3. Megoldási területek
4. Iparágak
5. Ajánlat neve
6. Összefoglalás
7. Leírás
8. Képernyőképek/videók

<br>Íme egy példa arra, hogyan jelennek meg az ajánlati információk a Microsoft AppSourceban (a felsorolt árak kizárólag a tényleges költségekkel kapcsolatos célokat szolgálnak):

:::image type="content" source="media/example-consulting-service-offer-appsource.png" alt-text="Bemutatja, hogyan jelenik meg az ajánlat a Microsoft AppSourceban.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Embléma
2. Ár
3. Termékek
4. Iparágak
5. Ajánlat neve
6. Összefoglalás
7. Leírás
8. Képernyőképek/videók
9. Dokumentumok

### <a name="name"></a>Név

Az itt megadott név az ajánlat címeként jelenik meg. Ez a mező előre ki van töltve az ajánlat- **alias** mezőben megadott szöveggel az ajánlat létrehozásakor. A nevet később módosíthatja.

A név:

- Védjeggyel is rendelkezhet (és a védjegyek és a szerzői jogi szimbólumok is szerepelhetnek).
- Nem lehet hosszabb 50 karakternél.
- Nem szerepelhetnek hangulatjelek.

### <a name="search-results-summary"></a>Keresési eredmények összegzése

Adja meg az ajánlat rövid leírását. Ez akár 100 karakter hosszú lehet, és a piactér keresési eredményeiben használható.

### <a name="description"></a>Leírás

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

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

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!Note]
>Ha probléma merül fel a fájlok feltöltésekor, győződjön meg arról, hogy a helyi hálózat nem blokkolja a https://upload.xboxlive.com partner központ által használt szolgáltatást.

#### <a name="store-logos"></a>Emblémák tárolása

Adjon meg egy PNG-fájlt a **nagy** méretű emblémához. A partner Center ezt fogja használni egy **kis** embléma létrehozásához. Ezt később is lecserélheti egy másik rendszerképpel.

- **Nagyméretű** (216 x 216 és 350 x 350 px, kötelező)
- **Kicsi** (48 x 48 px, opcionális)

Ezeket az emblémákat a lista különböző helyein használják.

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

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

## <a name="review-and-publish"></a>Felülvizsgálat és közzététel

Miután elvégezte az ajánlat összes szükséges részét, elküldheti ajánlatát az áttekintéshez és a közzétételhez.

1. Ha készen áll a tanácsadási szolgáltatás ajánlatának közzétételére, kattintson **a felülvizsgálat és közzététel**elemre.
2. Tekintse át a végső beküldési oldal részleteit.
3. Ha szükséges, jegyezze fel a minősítési csapatot, ha úgy véli, hogy az ajánlat bármely részlete magyarázatot kér.
4. Ha elkészült, válassza a **Küldés**lehetőséget.
5. Az **ajánlat áttekintése** oldalon látható, hogy az ajánlat mely közzétételi fázisban található.

Ha további információra van szüksége arról, hogy mennyi ideig számíthat az ajánlat az egyes közzétételi fázisokban való használatra, tekintse [meg a kereskedelmi Piactéri ajánlat közzétételi állapotának ellenőrzését](publishing-status.md)ismertető szakaszt.

## <a name="update-your-existing-consulting-service-offers"></a>Meglévő tanácsadói szolgáltatási ajánlatok frissítése

- [Meglévő ajánlat frissítése a kereskedelmi piactéren](update-existing-offer.md)
