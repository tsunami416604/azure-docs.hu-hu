---
title: SKU-alkalmazások konfigurálása Azure-alkalmazási ajánlathoz | Azure piactér
description: Az SKU konfigurálása Azure-beli felügyelt alkalmazáshoz és Azure-megoldási sablonhoz.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 2430d7e6fa74438c148d3cb849510be06243faa0
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543154"
---
# <a name="azure-application-skus-tab"></a>Azure-alkalmazások SKU-i lapja

Ez a cikk bemutatja, hogyan hozhat létre SKU-t az Azure-alkalmazáshoz a SKUs lapon. 

> [!IMPORTANT]
> Az SKU konfigurálásának lépései különbözőek a felügyelt alkalmazások és a megoldás-sablonok ajánlata esetében. Ezek a különbségek a jelen cikkben olvashatók. 

## <a name="configure-azure-application-skus"></a>Azure Application SKU-alkalmazások konfigurálása

### <a name="create-a-new-sku"></a>Új SKU létrehozása

Egy új SKU létrehozásához kövesse az alábbi lépéseket:

1. Válassza a **SKUs** fület.
2. Az SKU alatt válassza az **+ új SKU**elemet.

    ![Új SKU-kérés](./media/azureapp-plus-sku.png)

3. Az új SKU előugró ablakban írja be az **SKU azonosítót**. Ez az azonosító legfeljebb 50 karakter hosszúságú lehet, és csak kisbetűket, alfanumerikus karaktereket, kötőjeleket vagy aláhúzásokat tartalmazhat. Az SKU-azonosító nem végződhet kötőjeltel.
4. Az SKU azonosító látható az ügyfelek számára a termék URL-címeiben, a Resource Manager-sablonokban (ha vannak ilyenek) és a számlázási jelentésekben. Ez az azonosító nem módosítható az ajánlat közzétételét követően.

### <a name="sku-details-for-a-solution-template"></a>A megoldási sablon SKU-adatai

A következő képernyőfelvételen a megoldási sablon SKU-részletek űrlapja látható.

![SKU-részletek űrlap a megoldási sablonhoz](./media/azureapp-sku-details-solutiontemplate.png)

Adja meg a következő SKU-értékeket.  A csillaggal hozzáfűzött mezők megadása kötelező.

|    Mező         |       Leírás                                                            |
|  ---------       |     ---------------                                                          |
|  **Cím\***     | Az SKU címe. Ez a cím jelenik meg a katalógusban ehhez az elemhez.   |
| **Összefoglalás\***    | Az SKU rövid összefoglaló leírása. (A maximális hossz 100 karakter.)  |
| **Leírás\*** | Az SKU részletes leírása. Az alapszintű HTML használata támogatott.                 | 
| **SKU típusa\***   | Adja meg az Azure-alkalmazás megoldásának típusát, válassza a ***megoldás sablon** lehetőséget ehhez a forgatókönyvhöz. |
| **Felhő rendelkezésre állása\*** | Az SKU helye. Az alapértelmezett érték a **nyilvános Azure**.  <b/>a **nyilvános Azure** -alkalmazás az összes nyilvános Azure-régióban elérhetővé válik a piactér-integrációval rendelkező ügyfelek számára.  <b/>**Azure Government Cloud** -App a Azure Government felhőben lesz telepítve. A [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)közzétételének megkezdése előtt a Microsoft javasolja a közzétevők tesztelését és a megoldásuk érvényesítését az ebben a környezetben várt módon. A fázis-és tesztelési eljáráshoz igényeljen egy [próbaverziós fiókot](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).  |
| **Ez egy privát SKU?\*** | Válassza az **Igen** lehetőséget, ha ez az SKU csak az ügyfelek kiválasztott csoportja számára érhető el. |
|   |   |

  > [!NOTE] 
  > A Microsoft Azure Government az Egyesült Államok szövetségi, állami, helyi vagy törzsi és partnereink ügyfelei számára szabályozott hozzáféréssel rendelkező kormányzati közösségi felhő.


### <a name="sku-details-for-managed-application"></a>A felügyelt alkalmazás SKU-adatai

A következő képernyőfelvételen a felügyelt alkalmazás SKU-részletek űrlapja látható.

   ![SKU-részletek űrlap felügyelt alkalmazáshoz](./media/azureapp-sku-details-managedapplication.png)

Konfigurálja a következő SKU-beállításokat. A csillaggal hozzáfűzött mezők megadása kötelező.

|    Mező         |       Leírás                                                            |
|  ---------       |     ---------------                                                          |
|  **Cím\***     | Az SKU címe. Ez a cím jelenik meg a katalógusban ehhez az elemhez.   |
| **Összefoglalás\***    | Az SKU rövid összefoglaló leírása. (A maximális hossz 100 karakter.)  |
| **Leírás\*** | Az SKU részletes leírása. Az alapszintű HTML használata támogatott.                 | 
| **SKU típusa\***   | Adja meg az Azure-alkalmazás megoldását, válassza a ***felügyelt alkalmazás** lehetőséget ehhez a forgatókönyvhöz. 
| **Felhő rendelkezésre állása\*** | Az SKU helye. Az alapértelmezett érték a **nyilvános Azure**.  <b/>a **nyilvános Azure** -alkalmazás az összes nyilvános Azure-régióban elérhetővé válik a piactér-integrációval rendelkező ügyfelek számára.  <b/>**Azure Government Cloud** -App a Azure Government felhőben lesz telepítve. A [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)közzétételének megkezdése előtt a Microsoft javasolja a közzétevők tesztelését és a megoldásuk érvényesítését az ebben a környezetben várt módon. A fázis-és tesztelési eljáráshoz igényeljen egy [próbaverziós fiókot](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).   A Microsoft Azure Government az Egyesült Államok szövetségi, állami, helyi vagy törzsi és partnereink ügyfelei számára szabályozott hozzáféréssel rendelkező kormányzati közösségi felhő. |
| **Ez egy privát SKU?\*** | Válassza az **Igen** lehetőséget, ha ez az SKU csak az ügyfelek kiválasztott csoportja számára érhető el. |
| **Ország/régió elérhetősége\*** | Az elérhető országok/régiók listájának megtekintéséhez használja a **régiók kiválasztása lehetőséget** . Jelölje be az egyes országokat/régiókat, majd kattintson **az OK** gombra a kivételezések mentéséhez.  <b/>![ország és régió elérhetőségi listája](./media/azure-app-select-country-region.png)  |
| **Régi díjszabás\*** | A SKU díja (USD/hó). Az árak helyi pénznemben vannak megadva, a konfigurációnál aktuális árfolyamok alapján. Ezeket a beállításokat csak akkor érvényesítheti, ha végső soron a beállításokkal rendelkezik. Ha az egyes országok/régiók árát egyenként szeretné beállítani vagy megtekinteni, akkor exportálja a díjszabási táblázatot, és importálja az egyéni díjszabással.  A díjszabási változások exportálásának és importálásának engedélyezéséhez menteni kell a díjszabási módosításokat.  |
| **Egyszerűsített pénznemek díjszabása\*** | A SKU díja (USD/hó). Ennek meg kell egyeznie a régi díjszabással. További információ: [egyszerűsített pénznemek díjszabása](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer). |
|  |  |


### <a name="package-details-for-solution-template"></a>Csomag részletei a megoldási sablonhoz

![Csomag részletei a megoldási sablonhoz](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

Adja meg a következő **csomag részleteinek** értékét.  A csillaggal hozzáfűzött mezők megadása kötelező.

- **Verzió\*** – a feltöltendő csomag verziószáma. A verziószámnak X. Y. Z formátumúnak kell lennie, ahol az X, az Y és a Z egész számok.
- **Csomagfájl (. zip)\*** – ez a csomag a következő fájlokat tartalmazza, amelyeket egy. zip fájlban mentettek.
  - **mainTemplate. json\*** – a megoldás/alkalmazás üzembe helyezéséhez és a megoldáshoz definiált erőforrások létrehozásához használt telepítési sablon fájlja. További információ: [telepítési sablon fájljainak](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)készítése.
  - **createUIDefinition. json\*** – ezt a fájlt a Azure Portal használja a megoldás/alkalmazás üzembe helyezéséhez szükséges felhasználói felület létrehozásához. További információkért lásd: [Azure Portal felhasználói felület létrehozása a felügyelt alkalmazáshoz](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - Parancsfájlok (ha szükséges) – a sablon futtatásakor esetlegesen szükséges további parancsfájlokat, például `Microsoft.Compute/virtualMachines/extensions`.
  - Beágyazott sablonok (ha szükséges) – bármely további beágyazott sablon.

  > [!IMPORTANT] 
  > Ennek a csomagnak tartalmaznia kell az alkalmazás kiépítéséhez szükséges beágyazott sablonokat vagy parancsfájlokat. A mainTemplate. JSON fájlnak és a createUIDefinition. JSON fájlnak a gyökérkönyvtárban kell lennie. További információ az üzembe helyezési összetevőkről: [Azure Resource Manager sablonok – ajánlott eljárásokat ismertető útmutató](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#deployment-artifacts-nested-templates-scripts).


### <a name="package-details-for-managed-application"></a>A felügyelt alkalmazás csomagjának részletei

   ![A felügyelt alkalmazás csomagjának részletei](./media/azureapp-sku-pkgdetails-managedapplication.png)

Adja meg a következő csomag részleteit.  A csillaggal hozzáfűzött mezők megadása kötelező.

- **Verzió\*** – a feltöltendő csomag verziószáma. A verziószámnak X. Y. Z formátumúnak kell lennie, ahol az X, az Y és a Z egész számok.
- **Csomagfájl (. zip)\*** – ez a csomag a következő fájlokat tartalmazza, amelyeket egy. zip fájlban mentettek.
  - applianceMainTemplate. JSON – a megoldás/alkalmazás telepítéséhez és a definiált erőforrások létrehozásához használt telepítési sablon fájlja. További információ: gyors útmutató [: Azure Resource Manager-sablonok létrehozása és telepítése a Azure Portal használatával](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition. JSON – ezt a fájlt a Azure Portal használja a megoldás/alkalmazás üzembe helyezéséhez szükséges felhasználói felület létrehozásához. További információkért lásd: [Azure Portal felhasználói felület létrehozása a felügyelt alkalmazáshoz](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - mainTemplate. JSON – a sablonfájl, amely csak a Microsoft. Solution/Appliances erőforrást tartalmazza. További információ: [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Jegyezze fel az erőforrás következő főbb tulajdonságait:
    - "Kind" – az értéknek a Marketplace által felügyelt alkalmazás esetében "Marketplace" értékűnek kell lennie.
    - "ManagedResourceGroupId" – az ügyfél-előfizetés azon erőforráscsoport, amelyben a applianceMainTemplate. JSON fájlban definiált összes erőforrás telepítve lesz.
    - "PublisherPackageId" – a csomagot egyedileg azonosító karakterlánc. Ezt az értéket a következőképpen kell kiépíteni: [publisherId]. [OfferId] – előzetes verzió: [SKUID]. [PackageVersion].

  >[!IMPORTANT] 
  >Ennek a csomagnak tartalmaznia kell az alkalmazás kiépítéséhez szükséges beágyazott sablonokat vagy parancsfájlokat. Ezeknek a fájloknak a gyökérkönyvtárban kell lenniük: MainTemplate. JSON, applianceMainTemplate. JSON és applianceCreateUIDefinition. JSON.

- **Bérlői azonosító\*** – a szervezet Azure Active Directory bérlői azonosítója.
- **Engedélyezi a JIT-hozzáférést?\*** – az **Igen** lehetőség kiválasztásával engedélyezheti az ügyfelek központi telepítésének igény szerinti felügyeletét az ajánlat használatával.

  >[!NOTE] 
  >Ha engedélyezi a JIT-t, frissítenie kell a CreateUiDefinition. JSON fájlt a JIT-hozzáférés támogatásához.

Felügyelt alkalmazáshoz konfigurálnia kell az engedélyezési és a házirend-beállításokat.


#### <a name="authorization"></a>Engedélyezés

Adja meg annak a felhasználónak, csoportnak vagy alkalmazásnak Azure Active Directory-azonosítóját, amelyhez engedélyt kíván adni a felügyelt erőforráscsoport számára. A megadott engedély a szerepkör-definíció azonosítója alapján van megadva. Ez lehet egy tulajdonos, közreműködő vagy bármely egyéni szerepkör.


#### <a name="policy-settings"></a>Szabályzatbeállítások

Adja hozzá azokat a házirendeket, amelyeknek a felügyelt alkalmazás megfelel. További információ az Azure erőforrás-házirendekről: [Mi az Azure Policy?](../../../governance/policy/overview.md)

   ![Felügyelt alkalmazás engedélyezési és házirend-beállításai](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Új engedély létrehozása:**

1. Az **Engedélyezés**területen válassza az **+ új engedélyezés**lehetőséget.
2. A **résztvevő azonosítója**mezőbe írja be annak a felhasználónak, csoportnak vagy alkalmazásnak Azure Active Directory azonosítóját, amelyhez engedélyt kíván adni a felügyelt erőforráscsoport számára. A megadott engedély a szerepkör-definícióban szerepel.
3. A **szerepkör-definícióhoz**válassza ki az alábbi lehetőségek egyikét a legördülő listából: tulajdonos vagy közreműködő. További információkért lásd az [Azure-erőforrások beépített szerepköreit](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Több engedély is hozzáadható. Javasoljuk azonban, hogy hozzon létre egy Active Directory felhasználói csoportot, és határozza meg az AZONOSÍTÓját a "PrincipalId" elemben. Ez lehetővé teszi több felhasználó hozzáadását a felhasználói csoporthoz anélkül, hogy frissítenie kellene az SKU-t.

**Új szabályzat létrehozása:**

1. A **házirend-beállítások**területen válassza az **+ új házirend**elemet.
2. A szabályzat **neve**mezőbe írja be a szabályzat nevét. A név legfeljebb 50 karakter hosszú lehet.
3. A **házirendek**területen válassza ki az egyik lehetőséget a legördülő listából. Válassza ki azt a szabályzatot, amelyet az adatszolgáltató engedélyezni szeretne, amikor az alkalmazás az adatfelhasználást használja. További információ: [Azure Policy minták](https://docs.microsoft.com/azure/governance/policy/samples/index).

    ![Felügyelt alkalmazás házirend-beállításai](./media/azureapp-sku-policy-settings.png)

4. A **házirend SKU**esetében válassza az ingyenes vagy a standard lehetőséget a szabályzat SKU típusaként. A naplózási házirendekhez a szabványos SKU szükséges.


## <a name="next-steps"></a>Következő lépések

A [piactér lapon](./cpp-marketplace-tab.md)további leírást talál az ajánlatról és a marketing-eszközökről. 
