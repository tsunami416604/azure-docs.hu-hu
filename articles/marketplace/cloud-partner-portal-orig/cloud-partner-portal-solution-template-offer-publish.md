---
title: A Megoldássablon közzététele |} A Microsoft Docs
description: Megoldás sablon közzététele az Azure Marketplace-en.
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
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: ded952ac6418ae3d9916b3ae8b8dbacb0c9c5a84
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809889"
---
# <a name="publish-a-solution-template-to-azure-marketplace"></a>Egy megoldás-sablont tesz közzé az Azure Marketplace-en

Ez a cikk ismerteti a Megoldássablon közzétételéhez a lépéseket kínálnak az Azure Marketplace-en.

## <a name="prerequisites"></a>Előfeltételek

Azure Marketplace-en megoldássablon listázása a következő műszaki és nem technikai jellegű Előfeltételek vonatkoznak.

### <a name="technical"></a>Műszaki

- [Megismerheti az Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

- Az Azure gyorsindítási sablonok:

    - [Azure gyors üzembe helyezés sablon dokumentációja](https://azure.microsoft.com/documentation/templates/)

    - [Azure rövid útmutató dokumentációját a Githubon](https://github.com/azure/azure-quickstart-templates)

 - [Azure portal felhasználói felületet fájl létrehozása](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

### <a name="non-technical-business-requirements"></a>Nem technikai jellegű (üzleti követelmények)

-   A vállalatnak (vagy az leányvállalatának) egy értékesítési, az Azure piactér által támogatott országban kell működnie.

-   A termék úgy, hogy az Azure piactér által támogatott számlázási modellek kompatibilis licenccel kell rendelkezniük.

-   Ön felelős műszaki támogatást az ügyfeleknek, üzletileg ésszerű módon elérhetővé tétele e ingyenes, fizetős vagy közösségi támogatásán keresztül.

-   Ön felelős a szoftver- és külső függőségek licencelése.

-   Adja meg a tartalmat, amely megfelel az Azure Marketplace-en és az Azure felügyeleti portálon való megjelentetéséhez ajánlat feltételeit.

-   Fogadja el az Azure piactér részvételi szabályzatának és a kiadói szerződés feltételeit.

-   El kell fogadnia a használati feltételeket, a Microsoft adatvédelmi nyilatkozatát és a Microsoft Azure Certified Program Agreement feltételeit.

## <a name="before-you-begin"></a>Előkészületek

Után minden az Előfeltételek teljesítése, elindíthatja a Megoldássablon ajánlat készítése. Mielőtt elkezdené, tekintse át a következő ajánlat és a Termékváltozat-információk.

**Ajánlat**

Az Azure-alkalmazás ajánlat termék, ajánlat való kiadói osztály felel meg. Ha egy új típusú megoldás/alkalmazás, amely érhető el az Azure Marketplace-en szeretne, egy új ajánlatunk, a legjobb módszer. Az ajánlatok SKU-k gyűjteményei. Minden ajánlat az Azure Marketplace-en a saját entitás jelenik meg.

**Termékváltozat**

Az SKU egy ajánlat legkisebb megvásárolható egysége. Az azonos termék osztály (ajánlat) belül SKU-k lehetővé teszik megkülönböztetni a különböző támogatott funkciókkal. Ha például az ajánlat kezeli, vagy nem felügyelt, és eltérő számlázási modellek támogatottak.

Több SKU-k hozzáadása a következő esetekben:
- Különböző számlázási modell, például a Bring Your saját licences (BYOL) vagy a Használatalapú fizetés (Használatalapú) támogatni kíván.
- Minden egyes Termékváltozat támogatja a különböző funkciókészlettel, és minden egyes szolgáltatáskészlet ezekhez különböző árak.

A Termékváltozat a szülő ajánlat az Azure Marketplace-en alatt jelenik meg, és megjelenik-e az Azure Portalon a saját megvásárolható entitásként.

## <a name="to-create-a-new-offer"></a>Az új ajánlat létrehozása

1.  Jelentkezzen be a [Cloud Partner Portalon](http://cloudpartner.azure.com/).

2.  A bal oldali navigációs sávon válassza **+ új ajánlat**, majd válassza ki **Azure-alkalmazások**.

    ![Új ajánlat létrehozása](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  A **új ajánlat**válassza **szerkesztő**.

    ![Új ajánlat szerkesztő](./media/cloud-partner-portal-publish-managed-app/newOffer_OfferSettings.png)

4.  A **szerkesztő**, ehhez meg kell adnia a következő nézetekben található információk:
    - Ajánlat beállításai
    - Termékváltozatok
    - Piactér
    - Támogatás minden egyes nézet tartalmazza a kérik a mezők halmaza alapján. Szükséges, piros csillag jelöl, mezők (\*)

## <a name="to-configure-offer-settings"></a>Az ajánlat beállításainak konfigurálása

1. Adja meg az alábbiakat **ajánlat identitás** ajánlat beállítások mezőket.

    **Ajánlat azonosítója**

     Közzétevő-profilon belül az ajánlat egyedi azonosítója. Ez az azonosító látható termék URL-címek, ARM-sablonokat, és számlázási jelentéseket. Csak kisbetűs alfanumerikus karaktereket és kötőjeleket (-) használhatja. Az azonosító nem végződhet kötőjellel, és legfeljebb 50 karakter hosszú lehet. 
    >[!Note]
    >Ez a mező zárolva van, amikor egy ajánlatot élesíti.

    **Közzétevő azonosítója**

    Egy legördülő lista közzétevő profil. Válassza ki, amely alatt az ajánlatot közzétenni kívánt profilt. 
    >[!Note]
    >Ez a mező zárolva van, amikor egy ajánlatot élesíti.

    **Name (Név)**

    Az ajánlat megjelenített neve. Ez a név jelenik meg az Azure Marketplace-en és az Azure Portalon. Legfeljebb 50 karakterből állhat. Használja az alábbi útmutatót az ajánlat neve:
    -  A termék számára egy felismerhető márkanevet adjon meg. 
    - Itt a vállalat neve ne tartalmazza, kivéve, ha ez az ajánlat hogyan kereskedelmi forgalomba.
    - Ha ez az ajánlat a saját webhelyen található már termékmarketinggel, győződjön meg arról, a név legyen azonos a neve, a webhelyen.

2. Válassza ki **mentése** ajánlat beállítások befejezéséhez.
az ajánlatban.

## <a name="to-create-skus"></a>SKU-k létrehozása
------------------

1. Válassza ki **termékváltozatok**. 

    ![Új Termékváltozat](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

    SKU-azonosítója az ajánlat belül termékváltozat egyedi azonosító. Ez az azonosító látható termék URL-címek, ARM-sablonokat, és számlázási jelentéseket. A Termékváltozat azonosítója:
    - Legfeljebb egy legfeljebb 50 karakter hosszú lehet.
    - Csak lehet a kisbetűs alfanumerikus karaktereket és kötőjeleket (-).
    - Az azonosító nem végződhet kötőjellel.

    >[!Note]
    >A Termékváltozat hozzáadása után megjelenik az SKU-k megtekintése a termékváltozatok listája. A Termékváltozat részletek megtekintéséhez válassza ki a Termékváltozat nevét. 

2. Válassza ki **új Termékváltozat** az alábbi képernyőfelvételen látható információk. 

    ![Termékváltozat részletei](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)


### <a name="sku-settings"></a>Termékváltozat-beállítások

Adja meg a következő SKU-beállításokat.

- **Cím** -termékváltozat címét. Ez a cím a katalógusban, az elem jelenik meg.

- **Összefoglaló** – egy rövid a termékváltozat összefoglaló leírását. (Legfeljebb 100 karakterből állhat.)

- **Leírás** – egy részletes leírást a termékváltozat.

- **Termékváltozatának típusa** – egy legördülő lista ezekkel az értékekkel: "A felügyelt alkalmazás (előzetes verzió)" és "Sablon". A jelen esetben válassza ki a **Megoldássablon**.

- **A felhő rendelkezésre állási** -helyét, a Termékváltozat. Az alapértelmezett érték a nyilvános Azure.

### <a name="package-details"></a>Csomag részletei

Miután elvégezte a Termékváltozat-beállításokat, adja meg a következő csomagra vonatkozó részleteket.

![Csomag részletei](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_ST_package.png)

- **Aktuális verzió** – a csomagot, amely a feltölteni kívánt verzióját. -Formátumúnak kell lennie...

- **Csomagfájl** – Ez a csomag tartalmazza a következő fájlokat, amelyek .zip-fájlként lesznek mentve.

    -   MainTemplate.json – a központi telepítési sablon fájlt, a megoldás/alkalmazások üzembe helyezésére, és a megoldáshoz megadott erőforrások létrehozásához használt. További információkért lásd: [hogyan hozhat létre a központi telepítési sablon fájlok](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)

    -   createUIDefinition.json – Ez a fájl létrehozásához a felhasználói felület ezen megoldás alkalmazása kialakítási használják az Azure Portalon. További információkért lásd: [létrehozása az Azure portal felhasználói felületet a felügyelt alkalmazás](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

    >[!IMPORTANT]
    >Ez a csomagnak tartalmaznia kell a beágyazott sablonokkal vagy az alkalmazás telepítéséhez szükséges parancsprogramok. A mainTemplate.json és createUIDefinition.json a gyökérmappájában lévő mappának kell lennie.

## <a name="to-configure-the-marketplace"></a>A Marketplace-en konfigurálása

Konfigurálja a mezőket, amelyek az ajánlat jelennek meg a Marketplace-en nézete segítségével [Azure Marketplace-en](https://azuremarketplace.microsoft.com) és a [az Azure Portal](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Előfizetés azonosítókat előzetes verzió

Az Azure előfizetés azonosítókat, amelyeket meg szeretne hozzáférése az ajánlat az ajánlat közzétételekor listája. Ezek fehér felsorolt előfizetések lehetővé teszik, hogy a hozzászólásláncnak ajánlat így előtt tesztelje élő. A partner portál lehetővé teszi legfeljebb 100 előfizetést engedélyezési listára.

### <a name="suggested-categories"></a>Javasolt kategóriák

A megadott listáról, hogy az ajánlat társítható legjobb legfeljebb 5 kategóriák kiválasztása A kiválasztott kategóriákra lesz használható az ajánlat leképezése elérhető termékkategóriák [Azure Marketplace-en](https://azuremarketplace.microsoft.com) és [az Azure Portal](https://portal.azure.com/).

Az alábbi példák bemutatják a Piactérrel kapcsolatos információkat az Azure Marketplace-en és az Azure Portalon.

**Az Azure Marketplace-en**

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)


**Azure Portal**


![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)


![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)


### <a name="logo-guidelines"></a>Emblémáinak

Kövesse a Cloud Partner portálra feltöltött emblémák:

-   Az Azure arculata egyszerű színpalettát használ. Tartsa számát az elsődleges és másodlagos színt az embléma alacsony.

-   Az Azure Portal a témák színei fehér, és fekete. Ne használja ezeket a színeket, a emblémák háttérszíne. Használjon színt, amely biztosítja, a emblémák neves az Azure Portalon. Javasoljuk az egyszerű alapszínek használatát.

    >[!Note] 
    >Ha használ áttetsző háttérrel, majd győződjön meg arról, hogy a emblémák szöveg nem fehér, fekete vagy a kék.

-   Ne használjon színátmenetes hátteret az emblémában.

-   Szöveg elkerüli az embléma. Ez magában foglalja az Ön vállalatát vagy márkanév. Az embléma megjelenését és működését kell lennie *egybesimított* és átmenetekhez kerülendő.

-   Az embléma nem archiválhatók.

#### <a name="hero-logo"></a>Hero-embléma

A Hero embléma megadása nem kötelező. A közzétevő nem szeretné Hero emblémát tölthet fel. Azonban az embléma követően nem törölhető. A partner be kell tartaniuk az Azure Marketplace-en irányelveket Hero ikonok.

#### <a name="guidelines-for-the-hero-logo-icon"></a>Útmutató a Hero embléma ikon

-   A közzétevő megjelenített neve, csomag címét és az ajánlat hosszú összegzése jelennek meg a fehér színű betűtípus használatával. Ne használja a bármely világos szín a háttérben. Fekete, a fehér és áttetsző háttérrel Hero ikonok esetén nem engedélyezett.

-   A közzétevő megjelenített név, cím, az ajánlat hosszú összegzése és a Létrehozás gombra vannak beágyazva programozott módon a Hero embléma az ajánlat felsorolt tervet. Ne adja meg a szöveg, amikor az identitásfelügyelet a Hero embléma. Hagyja meg az üres területet a jobb oldalon az embléma. Ez a terület 415 x 100 képpont legyen, és 370 ellensúlyozza a balról képpont.

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

## <a name="to-configure-support"></a>Támogatási konfigurálása

A támogatási nézet használatával adja meg a következő információkat:

- Támogató kapcsolattartók a cégtől, például a mérnöki csapathoz.
- Ügyfél-támogatási kapcsolattartók.

## <a name="to-publish-the-offer"></a>Az ajánlat közzététele

Az utolsó lépés, hogy az ajánlat közzététele. Kattintson a **Publish** (Közzététel) elemre.
