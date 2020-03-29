---
title: SK-k konfigurálása Azure-alkalmazásajánlathoz | Azure Piactér
description: Az SK-k konfigurálása egy Azure által felügyelt alkalmazáshoz és egy Azure-megoldássablonhoz.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 043394a1303456ce5b209bb84b5afaf09f6beba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289073"
---
# <a name="azure-application-skus-tab"></a>Az Azure alkalmazás ska-jai lap

Ez a cikk ismerteti, hogyan használhatja a Termékalkalmazás lap létrehozása termékalkalmazás az Azure-alkalmazáshoz. 

> [!IMPORTANT]
> A termékváltozat konfigurálásának lépései eltérnek a Felügyelt alkalmazásajánlat és a Megoldássablon-ajánlat esetében. Ezeket a különbségeket ebben a cikkben dokumentálja. 

## <a name="configure-azure-application-skus"></a>Azure-alkalmazás-sk-ek konfigurálása

### <a name="create-a-new-sku"></a>Új termékváltozat létrehozása

Az alábbi lépésekkel új termékváltozatot hozhat létre:

1. Válassza a **SK-k** lapot.
2. A Termékváltozatok csoportban válassza a **+ Új termékváltozat**lehetőséget.

    ![Új termékváltozat-kérdés](./media/azureapp-plus-sku.png)

3. Az Új termékváltozat előugró ablakába írjon be egy **termékváltozat-azonosítót.** Ez az azonosító legfeljebb 50 karakterből állhat, és csak kisbetűs, alfanumerikus karakterekből, kötőjelekből vagy aláhúzásjelekből állhat. A termékváltozat-azonosító nem végződhet kötőjellel.
4. A termékváltozat-azonosító a termékURL-ek, az Erőforrás-kezelő sablonok (ha van) és a számlázási jelentésekben látható az ügyfelek számára. Ezt az azonosítót az ajánlat közzététele után nem módosíthatja.

### <a name="sku-details-for-a-solution-template"></a>Megoldássablon Termékváltozat részletei

A következő képernyőfelvételen látható a Megoldássablon Termékváltozat részletei űrlapja.

![Termékváltozat részletei űrlap a megoldássablonhoz](./media/azureapp-sku-details-solutiontemplate.png)

Adja meg a következő Termékváltozat-értékeket.  A csillaggal hozzáfűzött mezők szükségesek.

|    Mező         |       Leírás                                                            |
|  ---------       |     ---------------                                                          |
|  **Cím\***     | A termékváltozat címe. Ez a cím jelenik meg az elem gyűjteményében.   |
| **Összefoglaló\***    | A termékváltozat rövid összefoglaló leírása. (A maximális hossz 100 karakter.)  |
| **Leírás\*** | A termékváltozat részletes leírása. Az egyszerű HTML támogatott.                 | 
| **Termékváltozat típusa\***   | Az Azure-alkalmazásmegoldás típusa, válassza a ***megoldássablon** ebben a forgatókönyvben. |
| **Felhő elérhetősége\*** | A termékváltozat helye. Az alapértelmezett beállítás a **Nyilvános Azure**.  <b/>   **Nyilvános Azure** – Az alkalmazás minden nyilvános Azure-régióban, amely piactér-integrációval rendelkezik, telepíthető lesz az ügyfelek számára.  <b/>   **Azure Government Cloud** – Az alkalmazás az Azure Government Cloudban lesz telepítve. Az [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)általi közzététel előtt a Microsoft azt javasolja, hogy a közzétevők teszteljék és érvényesítsék a megoldásuk megfelelő működését ebben a környezetben. A szakaszés a teszt, kérjen [próbafiókot](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).  |
| **Ez egy privát termékváltozat?\*** | Válassza az **Igen** lehetőséget, ha ez a termékváltozat csak a vevők kiválasztott csoportja számára érhető el. |
|   |   |

  > [!NOTE] 
  > A Microsoft Azure Government egy kormányzati közösségi felhő, amely ellenőrzött hozzáféréssel rendelkezik az Egyesült Államok szövetségi, állami, helyi vagy törzsi ÉS partnereinek ellenőrzött hozzáférésével, amely jogosult ezen entitások kiszolgálására.


### <a name="sku-details-for-managed-application"></a>A felügyelt alkalmazás Termékváltozatának részletei

A következő képernyőfelvétel en látható a Kezelt alkalmazás Termékváltozat részletei űrlapja.

   ![A felügyelt alkalmazás Termékváltozat ának részletei](./media/azureapp-sku-details-managedapplication.png)

Adja meg a következő Termékváltozat-beállításokat. A csillaggal hozzáfűzött mezők szükségesek.

|    Mező         |       Leírás                                                            |
|  ---------       |     ---------------                                                          |
|  **Cím\***     | A termékváltozat címe. Ez a cím jelenik meg az elem gyűjteményében.   |
| **Összefoglaló\***    | A termékváltozat rövid összefoglaló leírása. (A maximális hossz 100 karakter.)  |
| **Leírás\*** | A termékváltozat részletes leírása. Az egyszerű HTML támogatott.                 | 
| **Termékváltozat típusa\***   | Az Azure-alkalmazásmegoldás típusa, válassza a ***felügyelt alkalmazás** ebben a forgatókönyvben. 
| **Felhő elérhetősége\*** | A termékváltozat helye. Az alapértelmezett beállítás a **Nyilvános Azure**.  <b/>   **Nyilvános Azure** – Az alkalmazás minden nyilvános Azure-régióban, amely piactér-integrációval rendelkezik, telepíthető lesz az ügyfelek számára.  <b/>   **Azure Government Cloud** – Az alkalmazás az Azure Government Cloudban lesz telepítve. Az [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)általi közzététel előtt a Microsoft azt javasolja, hogy a közzétevők teszteljék és érvényesítsék a megoldásuk megfelelő működését ebben a környezetben. A szakaszés a teszt, kérjen [próbafiókot](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).   A Microsoft Azure Government egy kormányzati közösségi felhő, amely ellenőrzött hozzáféréssel rendelkezik az Egyesült Államok szövetségi, állami, helyi vagy törzsi ÉS partnereinek ellenőrzött hozzáférésével, amely jogosult ezen entitások kiszolgálására. |
| **Ez egy privát termékváltozat?\*** | Válassza az **Igen** lehetőséget, ha ez a termékváltozat csak a vevők kiválasztott csoportja számára érhető el. |
| **Ország/régió elérhetősége\*** | A **Régiók kiválasztása** segítségével megtekintheti az elérhető országok/régiók listáját. Ellenőrizze az egyes országokat/régiókat, majd válassza az **OK gombot** a választás mentéséhez.  <b/>   ![Ország- és régióelérhetőségi lista](./media/azure-app-select-country-region.png)  |
| **Régi árak\*** | A Termékváltozat ára havonta USD-ben. Az árak helyi pénznemben vannak beállítva az aktuális árfolyamok alapján a konfiguráció szerint. Érvényesítse ezeket, mivel végső soron ön birtokolja ezeket a beállításokat. Az egyes országok/régiók árának külön-külön történő beállításához vagy megtekintéséhez exportálja az árképzési táblázatot, és importálja az okat egyéni árképzéssel.  Az árképzési adatok exportálásának/importálásának engedélyezéséhez mentenie kell az árképzési módosításokat.  |
| **Egyszerűsített valutaárképzés\*** | A Termékváltozat ára havonta USD-ben. Ez meg kell egyeznie a régi árképzés. További információ: [Simplified Currency Pricing](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer). |
|  |  |


### <a name="package-details-for-solution-template"></a>A megoldássablon csomagrészletei

![A megoldássablon csomagrészletei](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

Adja meg a következő **csomagrészletek** értékeket.  A csillaggal hozzáfűzött mezők szükségesek.

- **Verzió\* ** - A csomag feltöltendő verziója. A verziócímkéknek X.Y.Z formátumúnak kell lenniük, ahol X, Y és Z egész számok.
- **Csomagfájl (.zip)\* ** – Ez a csomag a következő fájlokat tartalmazza, amelyeket .zip fájlban mentett.
  - **mainTemplate.json\* ** – A megoldás/alkalmazás üzembe helyezéséhez és a megoldáshoz definiált erőforrások létrehozásához használt központi telepítési sablonfájl. További információt a [Telepítési sablonfájlok készítése című témakörben talál.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
  - **createUIDefinition.json\* ** – Ezt a fájlt az Azure Portal használja a felhasználói felület létrehozásához a megoldás/alkalmazás kiépítéséhez. További információ: [Azure Portal felhasználói felület létrehozása a felügyelt alkalmazáshoz.](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)
  - Parancsfájlok (ha szükséges) – A sablon futtatásakor esetleg szükséges `Microsoft.Compute/virtualMachines/extensions`további parancsfájlok, például .
  - Beágyazott sablonok (ha szükséges) – További beágyazott sablonok.

  > [!IMPORTANT] 
  > Ennek a csomagnak tartalmaznia kell minden beágyazott sablont vagy parancsfájlt, amely az alkalmazás kikiépítéséhez szükséges. A mainTemplate.json fájlnak és a createUIDefinition.json fájlnak a gyökérmappában kell lennie. A telepítési összetevőkről az [Azure Resource Manager-sablonok – Gyakorlati tanácsok című témakörben olvashat bővebben.](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#deployment-artifacts-nested-templates-scripts)


### <a name="package-details-for-managed-application"></a>A csomag részletei a felügyelt alkalmazáshoz

   ![Csomag részletei a felügyelt alkalmazáshoz](./media/azureapp-sku-pkgdetails-managedapplication.png)

Adja meg a csomag részleteit.  A csillaggal hozzáfűzött mezők szükségesek.

- **Verzió\* ** - A csomag feltöltendő verziója. A verziócímkéknek X.Y.Z formátumúnak kell lenniük, ahol X, Y és Z egész számok.
- **Csomagfájl (.zip)\* ** – Ez a csomag a következő fájlokat tartalmazza, amelyeket .zip fájlban mentett.
  - applianceMainTemplate.json – A megoldás/alkalmazás üzembe helyezéséhez és a definiált erőforrások létrehozásához használt központi telepítési sablonfájl. További információ: [Rövid útmutató: Azure Resource Manager-sablonok létrehozása és üzembe helyezése az Azure Portal használatával.](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal) 
  - applianceCreateUIDefinition.json – Ezt a fájlt az Azure Portal használja a felhasználói felület létrehozásához a megoldás/alkalmazás kiépítéséhez. További információ: [Azure Portal felhasználói felület létrehozása a felügyelt alkalmazáshoz.](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)
  - mainTemplate.json – Az a sablonfájl, amely csak a Microsoft.Solution/appliances erőforrást tartalmazza. További [információ: Az Azure Resource Manager-sablonok szerkezetének és szintaxisának megismerése című témakörben olvashat.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) <br>
Vegye figyelembe az erőforrás következő kulcsfontosságú tulajdonságait:
    - "kind" – Az értéknek "Piactér" kell lennie a Marketplace által felügyelt alkalmazás esetében.
    - "ManagedResourceGroupId" – Az az erőforráscsoport az ügyfél előfizetésében, ahol a MainTemplate.json készülékben definiált összes erőforrás telepítve lesz.
    - "PublisherPackageId"- A csomagot egyedileg azonosító karakterlánc. Ezt az értéket a következőképpen kell megalkotni: ez a [publisherId] összefűzése. [OfferId]-preview[SKUID]. [PackageVersion].

  >[!IMPORTANT] 
  >Ennek a csomagnak tartalmaznia kell minden beágyazott sablont vagy parancsfájlt, amely az alkalmazás kikiépítéséhez szükséges. Ezeknek a fájloknak a gyökérmappában kell lenniük: MainTemplate.json, applianceMainTemplate.json és applianceCreateUIDefinition.json.

- **Bérlői\* azonosító** – a szervezet Azure Active Directory-bérlői azonosítója.
- **Engedélyezi a JIT-hozzáférést? \* ** - Válassza az **Igen** lehetőséget a Just-In-Time felügyeleti hozzáférés engedélyezéséhez az ügyfelek központi telepítések ezzel az ajánlattal.

  >[!NOTE] 
  >Ha engedélyezi a jit-t, frissítenie kell a CreateUiDefinition.json fájlt a JIT-hozzáférés támogatásához.

Felügyelt alkalmazás esetén konfigurálnia kell az engedélyezési és házirend-beállításokat.


#### <a name="authorization"></a>Engedélyezés

Adja hozzá az Azure Active Directory-azonosítót a felhasználó, csoport vagy alkalmazás, amelynek szeretné megadni az engedélyt a felügyelt erőforráscsoport. A megadott engedélyt a szerepkör-definícióazonosító jelzi. Ez lehet tulajdonos, közreműködő vagy bármilyen egyéni szerepkör.


#### <a name="policy-settings"></a>Szabályzat Beállítás

Adja hozzá azokat a szabályzatokat, amelyeknek a Felügyelt alkalmazás megfelel. További információ az Azure Resource-szabályzatokról: [Mi az Azure-szabályzat?](../../../governance/policy/overview.md)

   ![Felügyelt alkalmazás engedélyezési és házirend-beállításai](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Új engedélyezés létrehozása:**

1. Az **Engedélyezés**csoportban válassza a **+ Új engedélyezés**lehetőséget.
2. **Az egyszerű azonosító**mezőbe írja be annak a felhasználónak, csoportnak vagy alkalmazásnak az Azure Active Directory-azonosítóját, amelynek engedélyt szeretne adni a felügyelt erőforráscsoportnak. A megadott engedélyt a szerepkör-definíció jelzi.
3. A **Szerepkör-definíció**listában válasszon az alábbi lehetőségek közül a legördülő listából: Tulajdonos vagy Közreműködő. További információkért lásd az [Azure-erőforrások beépített szerepköreit](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Több engedély is hozzáadható. Azonban ajánlott létrehozni egy Active Directory felhasználói csoportot, és adja meg az azonosítóját a "PrincipalId"-ban. Ez lehetővé teszi, hogy több felhasználót a felhasználói csoport anélkül, hogy a termékváltozat frissítése.

**Új házirend létrehozása:**

1. A **Házirend-beállítások**csoportban válassza a **+ Új házirend**lehetőséget.
2. A **Házirend neve**mezőbe írja be a házirend nevét. A név maximális hossza 50 karakter.
3. A **Házirendek**listában válasszon egyet a legördülő listából. Válassza ki azt a házirendet, amelyet az adatszolgáltató engedélyezni szeretne, amikor az alkalmazás az adatokat használja. További információt az [Azure-szabályzatminták című témakörben talál.](https://docs.microsoft.com/azure/governance/policy/samples/index)

    ![Felügyelt alkalmazás házirend-beállításai](./media/azureapp-sku-policy-settings.png)

4. A **házirend termékváltozat,** válassza szabad vagy standard, mint a házirend Termékváltozat típusát. A standard termékváltozat szükséges a naplózási házirendek.


## <a name="next-steps"></a>További lépések

Az ajánlatát és a marketingeszközöket a [Piactér lapon](./cpp-marketplace-tab.md)is ismertetheti. 
