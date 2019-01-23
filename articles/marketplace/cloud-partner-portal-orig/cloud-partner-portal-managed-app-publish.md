---
title: Az Azure Marketplace-en az Azure felügyelt alkalmazás közzététele
description: Az Azure Marketplace-en az Azure felügyelt alkalmazás közzététele
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 6e5bcd6d9923b4051d44d51ff1a2534bc5e02f41
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54445660"
---
<a name="publish-an-azure-managed-application-to-azure-marketplace"></a>Az Azure Marketplace-en az Azure által felügyelt alkalmazás közzététele 
========================================================

Ez a cikk egy kezelt alkalmazásra vonatkozó ajánlat Azure Marketplace-en való közzétételének különféle lépéseit sorolja fel.

<a name="pre-requisites-for-publishing-a-managed-application"></a>Előfeltétele egy felügyelt alkalmazás közzététele 
---------------------------------------------------

Azure Marketplace-en való felkerüléshez előfeltételei

1.  Műszaki

    -   [Szerzői Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

    -   Az Azure gyorsindítási sablonok:

        -   <https://azure.microsoft.com/documentation/templates/>

        -   <https://github.com/azure/azure-quickstart-templates>

    -   UI-definíció létrehozása

        -   [Felhasználói felület csomagdefiníciós fájl létrehozása](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

2.  Nem technikai jellegű (üzleti követelmények)

    -   A vállalatnak (vagy az leányvállalatának) kell működnie az Azure piactér által támogatott vevői származási országot

    -   A termék licenccel kell rendelkezniük, amely kompatibilis a számlázási modellek az Azure piactér által támogatott módon

    -   Ön felelős műszaki támogatást biztosít az ügyfelek számára: ingyenes, fizetős, vagy közösségi támogatásán keresztül.

    -   Ön felelős a szoftver- és a külső függőségek licencelése.

    -   Meg kell adnia a tartalmat, amely megfelel a feltételeknek az Azure Marketplace-en és az Azure felügyeleti portálon való megjelentetéséhez ajánlata

    -   El kell fogadnia a használati az Azure piactér részvételi szabályzatának és a Kiadókra vonatkozó szerződése

    -   El kell fogadnia a használati feltételeket, a Microsoft adatvédelmi nyilatkozata és a Microsoft Azure Certified Program szerződését.

<a name="how-to-create-a-new-azure-application-offer"></a>Egy új Azure-alkalmazás ajánlat létrehozása 
-------------------------------------------

Miután az összes előfeltételek teljesültek, készen áll a kezelt alkalmazásra vonatkozó ajánlat szerzői műveleteket. Mielőtt az első lépések az ajánlat és a egy Termékváltozat rövid áttekintése

**Ajánlat**

Az Azure-alkalmazás ajánlat termék, ajánlat való kiadói osztály felel meg. Ha egy új megoldás/alkalmazás közzé kell tenni az Azure Marketplace-en, egy új ajánlat módja a megnyitásához. Az ajánlatok SKU-k gyűjteményei. Minden ajánlat az Azure Marketplace-en a saját entitás jelenik meg.

**Termékváltozat**

A Termékváltozat-ajánlat, a legkisebb megvásárolható egység. Az azonos termék osztály (ajánlat) belül SKU-k lehetővé teszik támogatott különböző funkcióit, hogy az ajánlat felügyelt vagy nem felügyelt, és számlázási modellek közötti különbséget.

Adjon hozzá több termékváltozatok, ha szeretne eltérő számlázási modellekkel támogatják: például a Bring Your saját licences (BYOL), Használatalapú fizetés (PAYG), stb. 

Adjon hozzá több termékváltozatok, minden egyes Termékváltozat támogatja egy másik szolgáltatást, állítsa be, és eltérően díjszabása.

A Termékváltozat alatt jelenik meg a szülő ajánlat az Azure Marketplace-en közben az Azure.com-on, a saját megvásárolható entitásként megjelenik.

1.  Jelentkezzen be a [Cloud Partner Portalon](http://cloudpartner.azure.com).

2.  A bal oldali navigációs sávban kattintson a \"+ új ajánlat\" válassza \"Azure-alkalmazások\".

    ![Új ajánlat](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  Egy új ajánlat \"szerkesztő\" megtekintése most megnyílik, és elkezdheti a szerzői.

4.  A \"űrlapok\" meg kell adni, hogy szükség belül a bal oldalon láthatók a \"szerkesztő\" megtekintése. Minden egyes \"űrlap\" automatikusan kitölti a mezőket egy készlete áll. Szükséges, piros csillaggal jelölt mezők (\*).

    > Nincsenek a kezelt alkalmazás készítéséhez 4 fő űrlapok

    -   Ajánlat beállításai

    -   Termékváltozatok

    -   Piactér

    -   Támogatás

<a name="how-to-fill-out-the-offer-settings-form"></a>Az ajánlat beállításainak űrlap kitöltésére. 
---------------------------------------

Az ajánlat beállítások képernyő művelet egy alapszintű az ajánlat beállításainak megadásához.
A különböző mezők az alábbiakban tekintheti át.

**Ajánlat azonosítója**

Közzétevő-profilon belül az ajánlat egyedi azonosítója.
A termék URL-címek, Resource Manager-sablonok láthatók lesznek, és a számlázás jelentések. Csak kisbetűs alfanumerikus karaktereket vagy kötőjeleket (-) használata engedélyezett. Azt nem végződhet kötőjellel, és legfeljebb 50 karakter lehet. Ez a mező után egy ajánlatot élesíti zárolva van.

**Közzétevő azonosítója**

A legördülő listából válassza ki ezt az ajánlatot a közzétenni kívánt közzétevő profilt teszi lehetővé. Ez a mező után egy ajánlatot élesíti zárolva van.

**Name (Név)**

Az ajánlat megjelenített neve. A név fog megjelenni, az Azure Marketplace-en és az Azure Portalon. Legfeljebb 50 karakterből állhat. Itt útmutatást, hogy a termék felismerhető márkanév tartalmazza. Itt a vállalat neve nem tartalmazzák, hacsak nem értékesített hogyan. Ha ez az ajánlat a saját webhelyen található vannak marketing, ellenőrizze, hogy a név pontosan hogyan megjelenik a webhelyen.

Kattintson a \"mentése\" a folyamat mentéséhez. Következő lépés az SKU-k hozzáadása az ajánlatban lenne.

<a name="how-to-create-skus"></a>SKU-k létrehozása 
------------------

Kattintson a \"termékváltozatok\" űrlap. Itt láthatja a lehetőség \"adjon hozzá egy Termékváltozatot\" kattint, amely lehetővé teszi adja meg a egy \"SKU-azonosítója\".

![Új ajánlat termékváltozatok](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

\"SKU-azonosítója\" termékváltozat belül egy ajánlatot az az egyedi azonosító. Ezt az Azonosítót meg fognak jelenni a termék URL-címek, ARM-sablonokat, és a számlázási jelentések. Csak kisbetűs alfanumerikus karakterekből és kötőjelekből (-) állhat.
Ez nem végződhet kötőjellel, és legfeljebb 50 karakter hosszú lehet. Ez a mező után egy ajánlatot élesíti zárolva van. Több SKU-val is rendelkezhet egy ajánlaton belül. Minden rendszerkép közzététele kíván Termékváltozat van szükség.

Miután hozzáadta a Termékváltozat, meg fog jelenni belül termékváltozatok listájában a \"termékváltozatok\" űrlap. Kattintson a kerülnek a részleteit, hogy adott termékváltozat a Termékváltozat neve. Az alábbiakban néhány adatát egyes mezőit.

Miután rákattintanak az \"új Termékváltozat\", töltse ki a következő formában kell:

![Új ajánlat – új Termékváltozat](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)

### <a name="how-to-fill-sku-details-section"></a>Hogyan Termékváltozatának adatait szakasz 

**Cím** – Ez a termékváltozat címet biztosít. Ez az mit fog megjelenni ehhez az elemhez a katalógusban.

**Összefoglaló** – Ez a termékváltozat rövid összesíti. Ez a szöveg jobb a cím alatt fog megjelenni.

**Leírás** -Termékváltozat kapcsolatos részletes leírását tartalmazza.

**Termékváltozatának típusa** – az engedélyezett értékek a következők \"felügyelt alkalmazás\" és \"Megoldássablonok\". Ebben az esetben kiválaszthatja \"felügyelt alkalmazás\".

### <a name="how-to-fill-package-details-section"></a>Hogyan töltse ki a csomag részletei szakasz 

A csomag szakasz tartalmaz, meg kell adni a következő mezők

![Új ajánlat – új Termékváltozat-csomag](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_package.png)

**Aktuális verzió** -verziót biztosít a feltölteni kívánt csomagot.
Ez a következő formátumban kell lennie.

**Csomagfájl** – a csomag tartalmazza a következő fájlokat a tömörített .zip fájllá.

applianceMainTemplate.json – a központi telepítési sablon fájlt, a megoldás/alkalmazások üzembe helyezésére, és azt meghatározott erőforrások létrehozásához használt. További részleteket talál a szerző központi telepítési sablon fájlok ide- <https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template>

applianceCreateUIDefinition.json – Ez a fájl létrehozásához a felhasználói felület üzembe helyezés a megoldás alkalmazás az Azure.com webhelyen Portalra használják. További részleteket talál a ide - e a fájl létrehozása <https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview>

mainTemplate.json - the template file that contains only the Microsoft.Solution/appliances resource. A érdemes figyelembe vennie az erőforrás-tulajdonságok a következők:

-   \"milyen\" -az érték lehet \"Marketplace-en\" piactér által felügyelt alkalmazás forgatókönyv esetén
-   \"ManagedResourceGroupId\": az erőforráscsoport, az ügyfél\'s-előfizetés, ahol a applianceMainTemplate.json definiált összes erőforrást üzembe helyezve.
-   \"PublisherPackageId\": a karakterlánc, amely egyedileg azonosítja a csomagot. 

Az értéknek kell összetevődni – összefűzésével \[publisherId\].\[ OfferId\]– előzetes verzió\[SKUID\].\[ PackageVersion\].
Ezek az értékek mindegyike szerezhető be alább látható módon.

A csomagnak tartalmaznia kell minden olyan beágyazott sablon vagy parancsfájlok szükséges sikerült üzembe helyezni az alkalmazást. A mainTemplate.json, applianceMainTemplate.json és applianceCreateUIDefinition.json a gyökérmappában jelen kell lennie.

**Engedélyek** – Ez a tulajdonság megadja számára hozzáférést kap, és milyen szintű hozzáférést az erőforrásokhoz az ügyfeleknek az előfizetések. Ez lehetővé teszi a közzétevő a vásárló nevében az alkalmazás kezelése céljából.

-   PrincipalId - felhasználó, felhasználói csoportot vagy alkalmazást, amely megkapja bizonyos engedélyeket (a szerepkör-definíció által ismertetett) ügyfél-előfizetés erőforrásait az Azure Active directory azonosítóját.

-   Szerepkör-definíció – minden a beépített RBAC-szerepkörök az Azure AD által támogatott listáját. Kiválaszthatja a leginkább megfelelő szerepkör, amely lehetővé teszi, hogy az erőforrások kezelése a vásárló nevében.

Több engedélyt adhat hozzá. Azonban javasoljuk, hogy hozzon létre egy Active Directorybeli felhasználói csoportnak, és adja meg az azonosító a \"PrincipalId\."  Ez lehetővé teszi több felhasználó a felhasználói csoport hozzáadása a Termékváltozat frissítése nélkül.

További részleteket az RBAC - itt található <https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is>

<a name="marketplace-form"></a>Marketplace-en űrlap
----------------

A piactér képernyőn belül az azure-alkalmazás ajánlat kéri a mezőkkel, amelyek automatikusan megjelennek az [Azure Marketplace-en](https://azuremarketplace.microsoft.com) és a [az Azure Portal](https://portal.azure.com/). Az alábbiakban néhány adatát egyes mezőit.

#### <a name="preview-subscription-ids"></a>Előfizetés azonosítókat előzetes verzió

Itt adhatja meg, amely hozzáfér az ajánlatra való közzététel után szeretné az Azure előfizetési azonosítók listáját. Ezek fehér felsorolt előfizetések lehetővé teszi, hogy a hozzászólásláncnak ajánlat így előtt tesztelje élő. A partner portál lehetővé teszi legfeljebb 100 előfizetést engedélyezési listára.

#### <a name="suggested-categories"></a>Javasolt kategóriák

A megadott listáról, hogy az ajánlat társítható legjobb legfeljebb öt kategóriák kiválasztása A kiválasztott kategóriákra lesz használható az ajánlat leképezése elérhető termékkategóriák [Azure Marketplace-en](https://azuremarketplace.microsoft.com) és [az Azure Portal](https://portal.azure.com/).

Íme néhány a helyeken, amely megjelenik az űrlapon megadott adatok.

##### <a name="azure-marketplace"></a>Azure Piactér

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)

![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)

![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)

##### <a name="portal-at-azurecom"></a>Portálon, az Azure.com webhelyen

![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)

![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)

##### <a name="logo-guidelines"></a>Emblémáinak

A Cloud Partner portálra feltöltött összes emblémák kövesse az alábbi irányelveket:

-   Az Azure arculata egyszerű színpalettát használ. Tartsa számát az elsődleges és másodlagos színt az embléma alacsony.

-   Az Azure.com webhelyen Portalra téma színei fehér, és fekete. Ne használja ezeket a színeket, a emblémák háttérszíne. Néhány színekkel, amely biztosítja, a emblémák neves a portálon, az Azure.com webhelyen.
    Javasoljuk az egyszerű alapszínek használatát. **Ha használ áttetsző hátterű, majd ellenőrizze, hogy az emblémát és a szöveg nem lesznek fehér, a fekete vagy a kék.**

-   Ne használjon egy színátmenetes hátterének az embléma.

-   Elkerüli a szöveg, még akkor is a vállalat vagy a neve, az embléma a saját arculat.
    Az embléma megjelenését és működését kell lennie \'egybesimított\' és átmenetekhez kerülendő.

-   Kerülje az embléma kiterjesztését.

##### <a name="hero-logo"></a>Hero-embléma

A Hero embléma megadása nem kötelező. A közzétevő nem szeretné Hero emblémát tölthet fel. Azonban Miután feltöltött a hero ikon nem lehet törölni. Ebben az esetben a partner a Hero-ikonokat az Azure Marketplace-en irányelveket kell követnie.

###### <a name="guidelines-for-the-hero-logo-icon"></a>Útmutató a Hero embléma ikon

-   A közzétevő megjelenített neve, csomag cím és az ajánlat hosszú összegzése fehér a betűk színe megjelenik. Kerülje bármely világos szín a Hero ikon a háttérben. Fekete, a fehér és áttetsző háttérrel Hero-ikonokat nem engedélyezettek.

-   Az ajánlat lekérdezi a listában, ha a közzétevő megjelenített neve, csomag cím, az ajánlat hosszú összegzése és a Létrehozás gombra programozott módon ágyazott a Hero embléma belül. Adja meg a szöveg a Hero embléma tervezésekor nincs szükségünk. Üres tárolóhelyek hagyja ezt a mezőt a jobb oldali közzétevő megjelenítendő neve, csomag cím, az ajánlat hosszú összegzése és stb. Nem programozott módon foglalja azokat.
    Tárolóhelyek ürítse ki a szöveget kell 415 x 100, a jobb oldalon, és 370 eltolva a balról képpont.

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

<a name="support-form"></a>Támogatási űrlap
------------

A következő képernyő kitöltéséhez a támogatási űrlap. Az űrlap vállalata támogatási kér.  Néhány példa a következők: a mérnöki kapcsolattartási adatokat és ügyfél-támogatási kapcsolattartási adatokat.

<a name="how-to-publish-an-offer"></a>Ajánlat közzététele
-----------------------

Most, hogy az ajánlat megfogalmazni van, a következő lépés az Azure Marketplace-en az ajánlat közzététele. Kattintson a \"közzététel\" gombbal elindíthatja a folyamatot, az, hogy az ajánlat élő.
