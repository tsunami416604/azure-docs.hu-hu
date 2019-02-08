---
title: SKU-k konfigurálása az Azure-alkalmazás ajánlat |} A Microsoft Docs
description: Azure-beli a termékváltozatok konfigurálása felügyelt alkalmazás és a egy Azure-megoldás sablont.
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
ms.openlocfilehash: 7f73e71677a700ba8047246addb2d27799c5a616
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55890892"
---
# <a name="azure-application-skus-tab"></a>Az Azure application termékváltozatok lapja

Ez a cikk ismerteti, hogyan használhatja az SKU-k lapon SKU-k létrehozása az Azure-alkalmazásokat. 

>[!IMPORTANT] 
A Termékváltozat konfigurálásának lépései eltérőek egy kezelt alkalmazásra vonatkozó ajánlat és a egy megoldás sablon ajánlat. Ez a cikk ismerteti a különbségeket. 

## <a name="configure-azure-application-skus"></a>Az Azure application SKU-k konfigurálása

### <a name="create-a-new-sku"></a>Hozzon létre egy új Termékváltozatban

Ezeket a lépéseket követve hozzon létre egy új Termékváltozatban:

1. Válassza ki a **termékváltozatok** fülre.
2. SKU-k, válassza a **+ új Termékváltozat**.

    ![Új Termékváltozat kérése](./media/azureapp-plus-sku.png)

3. Az új Termékváltozat előugró ablak, írja be a **SKU-azonosítója**. Ezt az azonosítót legfeljebb 50 karakter hosszúságú lehet, és csak kisbetűket, alfanumerikus karaktereket, kötőjeleket vagy aláhúzásjel, kell állnia. Az SKU-azonosítója nem végződhet kötőjellel.
4. Az SKU-ID látható, az ügyfelek számára a termék URL-címek, Resource Manager-sablonok (ha van ilyen), és számlázási jelentések. Ezt az azonosítót az ajánlat közzétételének után nem módosítható.

### <a name="sku-details-for-a-solution-template"></a>A Megoldássablon Termékváltozatának adatait

Adja meg a következő SKU-beállításokat:

- **Cím** -termékváltozat címét. Ez a cím a katalógusban, az elem jelenik meg.
- **Összefoglaló** – egy rövid a termékváltozat összefoglaló leírását. (Legfeljebb 100 karakterből állhat.)
- **Leírás** – egy részletes leírást a termékváltozat.
- **Termékváltozatának típusa** – egy legördülő lista ezekkel az értékekkel: "Sablon" és "Felügyelt alkalmazás". A jelen esetben válassza ki a **Megoldássablon**.
- **A felhő rendelkezésre állási** -helyét, a Termékváltozat. Az alapértelmezett érték **nyilvános Azure**.
Nyilvános Azure-ban – a virtuális gép lesz az ügyfelek számára minden nyilvános Azure-régióban, amely rendelkezik a Marketplace-integráció üzembe helyezhető.
- **Az Azure Government felhőalapú** – Ez a virtuális gép lesz telepítve, az Azure Government Cloud. Közzététel előtt [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), a Microsoft javasolja a kiadók és hitelesíti a megoldás együttműködik a környezet a vártnak. Előkészítéséhez és tesztelése, a kérelem egy [próbafiókot](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).

  >[!NOTE] 
  >A Microsoft Azure Government egy kormányzati célú felhőalapú rendelkező ügyfeleknek az USA szövetségi, állami, helyi vagy törzsi közösségi és partnerek jelölhetők meg ezeket az entitásokat a szabályozott hozzáférést.

- **Az egy privát Termékváltozat?** – Válassza az Igen lehetőséget, ha ez a Termékváltozat csak felhasználók bizonyos csoportjaira rendelkezésére áll.

    ![Termékváltozat adatai űrlapot a megoldássablon](./media/azureapp-sku-details-solutiontemplate.png)

### <a name="sku-details-for-managed-application"></a>Felügyelt alkalmazás Termékváltozatának adatait

A következő képernyőfelvétel-készítés a Termékváltozat részletek képernyő egy kezelt alkalmazást jeleníti meg.

   ![Felügyelt alkalmazás Termékváltozat részletek képernyő](./media/azureapp-sku-details-managedapplication.png)

Adja meg a következő SKU-beállításokat:

- **Cím** -termékváltozat címét. Ez a cím a katalógusban, az elem jelenik meg.
- **Összefoglaló** – egy rövid a termékváltozat összefoglaló leírását. (Legfeljebb 100 karakterből állhat.)
- **Leírás** – egy részletes leírást a termékváltozat.
- **Termékváltozatának típusa** – egy legördülő lista ezekkel az értékekkel: "Sablon" és "Felügyelt alkalmazás". A jelen esetben válassza ki a **felügyelt alkalmazás**.
- **A felhő rendelkezésre állási** -helyét, a Termékváltozat. Az alapértelmezett érték **nyilvános Azure**.
- **Nyilvános Azure** – a virtuális gép üzembe helyezhető, az ügyfelek számára minden nyilvános Azure-régióban, amely rendelkezik a Marketplace-integráció lesz.
- **Az Azure Government felhőalapú** – Ez a virtuális gép lesz telepítve, az Azure Government Cloud. Közzététel előtt [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), a Microsoft javasolja a kiadók és hitelesíti a megoldás együttműködik a környezet a vártnak. Előkészítéséhez és tesztelése, a kérelem egy [próbafiókot](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).

  >[!NOTE] 
  >A Microsoft Azure Government egy kormányzati célú felhőalapú rendelkező ügyfeleknek az USA szövetségi, állami, helyi vagy törzsi közösségi és partnerek jelölhetők meg ezeket az entitásokat a szabályozott hozzáférést.

- **Az egy privát Termékváltozat?** – Válassza az Igen lehetőséget, ha ez a Termékváltozat csak felhasználók bizonyos csoportjaira rendelkezésére áll.
- **Ország/régió rendelkezésre állási** – használata **régiók kiválasztása** országok/régiók, rendelkezésre álló listájának megtekintéséhez. Ellenőrizze minden ország/régió, és válassza ki **OK** a ajánlásai mentéséhez. 

   ![Ország és régió rendelkezésre állási listája](./media/azure-app-select-country-region.png)

- **Régi díjszabás** – adja meg az ár a Termékváltozatot USD havonta. Az árak a helyi pénznemben aktuális átváltási árfolyamokat konfiguráció esetén használatával vannak beállítva. Ellenőrizze ezeket, mivel Ön irányíthatja a ezeket a beállításokat. Állítsa be, vagy külön-külön mindegyik ország/régió ár megtekintése,. exportálja a díjszabási táblázatot, és importálja az egyéni díjszabás.

  >[!NOTE]
  >Exportálás/importálás díjszabási adatok engedélyezéséhez a díjszabási módosítások mentéséhez.

- **Pénznem díjszabás egyszerűsített** – adja meg az ár a Termékváltozatot USD havonta. Ez lehet ugyanaz, mint a régi díjszabás. További információkért lásd: [pénznem díjszabás egyszerűsített](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer).

### <a name="package-details-for-solution-template"></a>A Megoldássablon csomag részletei

Adja meg a következő csomag részletei:

- **Verzió** – a csomagot, amely a feltölteni kívánt verzióját. Verzió címkéket az űrlap X.Y.Z, ha X, Y és Z egész számoknak kell lennie.
- **Csomagfájl (.zip)** – Ez a csomag tartalmazza a következő fájlokat, a .zip-fájlként mentve.
  - MainTemplate.json – a központi telepítési sablon fájlt, a megoldás/alkalmazások üzembe helyezésére, és a megoldáshoz megadott erőforrások létrehozásához használt. További információkért lásd: [hogyan hozhat létre a központi telepítési sablon fájlok](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).
  - createUIDefinition.json – Ez a fájl létrehozásához a felhasználói felület ezen megoldás alkalmazása kialakítási használják az Azure Portalon. További információkért lásd: [létrehozása az Azure portal felhasználói felületet a felügyelt alkalmazás](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

  >[!IMPORTANT] 
  >Ez a csomag kell tartalmaz beágyazott sablonok vagy az alkalmazás telepítéséhez szükséges parancsprogramok. A MainTemplate.json és createUIDefinition.json fájlt a gyökérmappájában lévő mappának kell lennie.

   ![A megoldássablon csomag részletei](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

### <a name="package-details-for-managed-application"></a>Felügyelt alkalmazás csomag részletei

Adja meg a következő csomag részletei:

- **Verzió** – a csomagot, amely a feltölteni kívánt verzióját. Verzió címkéket az űrlap X.Y.Z, ha X, Y és Z egész számoknak kell lennie.
- **Csomagfájl (.zip)** – Ez a csomag tartalmazza a következő fájlokat, a .zip-fájlként mentve.
  - applianceMainTemplate.json – a központi telepítési sablon fájlt, amely a megoldás/alkalmazások üzembe helyezésére, és hozza létre a meghatározott erőforrásokat használja. További információkért lásd: [a rövid útmutató: Létrehozása és üzembe helyezése Azure Resource Manager-sablonok az Azure portal használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition.json – Ez a fájl létrehozásához a felhasználói felület ezen megoldás alkalmazása kialakítási használják az Azure Portalon. További információkért lásd: [létrehozása az Azure portal felhasználói felületet a felügyelt alkalmazás](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - mainTemplate.json – a sablon fájlt, amely csak a Microsoft.Solution/appliances erőforrást tartalmazza. További információkért lásd: [struktúra és az Azure Resource Manager-sablonok szintaxisát](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Vegye figyelembe a következő kulcstulajdonságokat ehhez az erőforráshoz:
    - "kind" – az érték lehet "Marketplace" piactér által felügyelt alkalmazások esetén.
    - "ManagedResourceGroupId" – az erőforráscsoport, ahol a applianceMainTemplate.json definiált összes erőforrást üzembe helyezve az ügyfél-előfizetés.
    - "PublisherPackageId" – a karakterlánc, amely egyedileg azonosítja a csomagot. Ezt az értéket meg kell összetevődni: [publisherId] összefűzésével. [OfferId]-[SKUID] előnézetét. [PackageVersion.]

  >[!IMPORTANT] 
  >Ez a csomag kell tartalmaz beágyazott sablonok vagy az alkalmazás telepítéséhez szükséges parancsprogramok. Ezeket a fájlokat a gyökérmappájában lévő mappának kell lennie:  MainTemplate.json, applianceMainTemplate.json, and applianceCreateUIDefinition.json.

- **Bérlőazonosító** – a szervezet az Azure Active Directory-bérlő azonosítója.
- **Igény szerinti hozzáférés engedélyezéséhez?** – Select **Igen** ügyfél központi telepítésekhez célból Just-In-Time felügyeleti hozzáférés engedélyezéséhez.

  >[!NOTE] 
  >Ha engedélyezi az igény szerinti, frissítenie kell a CreateUiDefinition.json fájl igény szerinti hozzáférés támogatásához.

   ![Felügyelt alkalmazás csomag részletei](./media/azureapp-sku-pkgdetails-managedapplication.png)

Egy felügyelt alkalmazás konfigurálnia kell a hitelesítési és a házirend-beállításokat.

#### <a name="authorization"></a>Engedélyezés

Adja hozzá az Azure Active Directory azonosítója felhasználó, csoport vagy alkalmazás, amely engedélyt ad a kezelt erőforráscsoport szeretné. Az engedély biztosított, adott jelzői közé tartozik a szerepkör-definíció azonosítóját. Annak oka az lehet, a tulajdonos, közreműködő vagy bármely egyéni szerepkört.

#### <a name="policy-settings"></a>Házirend-beállítások

Adja hozzá a házirendekben, amelyek a felügyelt alkalmazás megfelel-e az. További információ az Azure erőforrás-házirendek, lásd: [Mi az Azure Policy?](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) 


   ![Engedélyezési és a házirendek beállításai a felügyelt alkalmazás](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Új engedélyezési létrehozása:**

1. A **engedélyezési**válassza **+ új engedélyezési**.
2. A **a résztvevő-azonosító**, írja be az Azure Active Directory azonosítója felhasználó, csoport vagy alkalmazás, amely engedélyt ad a kezelt erőforráscsoport szeretné. A szerepkör-definíció a biztosított, adott engedélyt jelöli.
3. A **szerepkör-definíció**, a legördülő listából válassza az alábbi lehetőségek egyikét:  Tulajdonos vagy közreműködő szerepkörrel. További információkért lásd: [beépített szerepkörök az Azure-erőforrások](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Több engedélyt adhat hozzá. Azonban azt javasoljuk, hogy hozzon létre egy Active Directorybeli felhasználói csoportnak, és adja meg az azonosítója a "PrincipalId." Ez lehetővé teszi több felhasználó a felhasználói csoport hozzáadása a Termékváltozat frissítése nélkül.

**Hozzon létre egy új házirendet:**

1. A **Szabályzatbeállítások**válassza **+ új szabályzat**.
2. A **házirendnév**, adjon meg egy nevet a házirend. A név hossza legfeljebb 50 karakter hosszú lehet.
3. A **házirendek**, a legördülő listából válassza a beállítások közül. Válassza ki a szabályzatot, amely az adatszolgáltató szeretne engedélyezhető, ha az alkalmazás használ az adatok. További információkért lásd: a [Azure házirend minták](https://docs.microsoft.com/azure/governance/policy/samples/index).

    ![Házirend-beállítások a felügyelt alkalmazás](./media/azureapp-sku-policy-settings.png)

4. A **Alkalmazásszabályzat Termékváltozata**, válassza ki az ingyenes vagy standard szintű, a szabályzat Termékváltozatának típusa. A Standard Termékváltozat megadása kötelező a naplózási házirendeket.

## <a name="next-steps"></a>További lépések

[Marketplace lap](./cpp-marketplace-tab.md)
