---
title: "Az Azure környezetben nem kompatibilis erőforrások azonosítására házirend-hozzárendelés létrehozása az Azure parancssori felület használatával |} Microsoft Docs"
description: "A PowerShell szolgáltatás használatával hozzon létre egy Azure házirend-hozzárendelés nem kompatibilis erőforrások azonosítására."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark
ms.date: 10/06/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 92b532691986e72eca68d9bc3033e20ff8ffef3b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Nem kompatibilis erőforrások az Azure környezetben az Azure parancssori felület segítségével azonosíthatja a házirend-hozzárendelés létrehozása

Az első lépés az Azure-ban ismertetése megfelelőségi fogják ahol állni az aktuális erőforrásokhoz. A gyors üzembe helyezési lépéseket a folyamatot, amely házirend létrehozása – házirend-definíció nem kompatibilis erőforrások azonosíthatók hozzárendelés *szükséges SQL Server verziója 12.0*. Ez a folyamat végén fog sikeresen azonosította kiszolgálók Mik a verziója, alapvetően nem megfelelő.

Az Azure CLI az Azure-erőforrások parancssorból vagy szkriptekkel történő létrehozására és kezelésére használható. Ez az útmutató részletek azonosításához az Azure-környezethez nem kompatibilis erőforrások házirend-hozzárendelés létrehozása az Azure parancssori felület használatával.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).
 
## <a name="opt-in-to-azure-policy"></a>Az Azure házirend engedélyezve

Azure házirend jelenleg korlátozott előzetes érhető el, a hozzáférés kéréséhez regisztrálnia kell.

1. Keresse fel Azure házirend https://aka.ms/getpolicy, és válassza ki a **regisztráció** a bal oldali ablaktáblán.

   ![Keresse meg a házirend](media/assign-policy-definition/sign-up.png)

2. Az előfizetések kiválasztásával Azure házirend engedélyezve a **előfizetés** lista szeretne együttműködni. Válassza ki **regisztrálása**.

   ![Szeretné használni az Azure-házirend](media/assign-policy-definition/preview-opt-in.png)

   Néhány napot ki, hogy fogadja el a regisztrációs kérelmet, igény szerint is tarthat. Ha a kérés elfogadva lekérdezi, értesítést kap e-mailben, hogy a szolgáltatás segítségével megkezdheti.

## <a name="create-a-policy-assignment"></a>Házirend-hozzárendelés létrehozása

A gyors üzembe helyezés azt házirend-hozzárendelés létrehozása, és rendelje hozzá a szükséges SQL Server verziója 12.0 definícióját. Ez a házirend-definíció azonosítja az erőforrásokat, amelyek nem felelnek meg a házirend-definíció a feltételeket.

Kövesse az alábbi lépéseket egy új házirend-hozzárendelést létrehozni.

Megtekintheti az összes házirend-beállítást, és a "Szükséges az SQL Server verziója 12.0" házirend-definíció található:

```azurecli
az policy definition list
```

Az Azure házirend mellékelt már beépített házirend-definíciókban használható. Beépített házirend-definíciók például jelenik meg:

- Címke és annak értéke
- Alkalmazza a címke és annak értéke
- Szükséges SQL Server verzió 12.0

Ezután adja meg a következő adatokat, és a házirend-definíció rendelhető hozzá a következő parancsot:

- Megjelenítési **neve** a házirend-hozzárendelés. Ebben az esetben most használja *szükséges SQL Server verziója 12.0 hozzárendelés*.
- **Házirend** – Ez a házirend-definíció, alapú ki, amelyek a hozzárendelés létrehozásához segítségével az. Ebben az esetben a házirend-definíció – *szükséges SQL Server verziója 12.0*
- A **hatókör** - hatókör határozza meg, milyen erőforrásokat, vagy az erőforrások csoportosítása a házirend-hozzárendelés lekérdezi kényszeríti. Ez terjedhet előfizetés erőforráscsoportokhoz.

  Az előfizetés (vagy erőforráscsoport) már korábban regisztrált amikor Azure házirendbe választotta használja ebben a példában használjuk az előfizetés-azonosító - **bc75htn-a0fhsi-349b-56gh-4fghti-f84852** és az erőforráscsoport neve - **FabrikamOMS**. Ne feledje módosítani ezeket az előfizetés Azonosítóját és dolgozunk erőforráscsoport nevét. 

Ez az, hogy mi a parancs hasonlóan kell kinéznie:

```azurecli
az policy assignment create --name Require SQL Server version 12.0 Assignment --policy Require SQL Server version 12.0 --scope /subscriptions/ 
bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

Egy házirend-hozzárendelést egy olyan házirend, belül egy adott hatókörhöz van hozzárendelve. Ebben a hatókörben egy erőforráscsoportba is terjedhet egy felügyeleti csoportban.

## <a name="identify-non-compliant-resources"></a>Nem kompatibilis erőforrások azonosítása

Erőforrást, amely nem kompatibilis az új hozzárendeléssel alatt megtekintése:

1. Lépjen vissza az Azure-házirendről.
2. Válassza ki **megfelelőségi** a bal oldali ablaktáblán, és keresse meg a **házirend-hozzárendelés** létrehozott.

   ![Szabályzatoknak való megfelelés](media/assign-policy-definition/policy-compliance.png)

   Ha a meglévő erőforrásokat, amelyek nem ehhez a hozzárendeléshez megfelelnek, akkor jelenik meg a a **nem megfelelő erőforrások** lapon, a fentiek szerint.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény útmutatók a gyors üzembe helyezés épül. Ha azt tervezi, folytassa a következő útmutatókból dolgozni, üríti a gyors üzembe helyezés létrehozott erőforrásokat. Ha nem tervezi a folytatáshoz a hozzárendelést hozott létre a parancs futtatásával:

```azurecli
az policy assignment delete –name Require SQL Server version 12.0 Assignment --scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852 resourceGroups/ FabrikamOMS
```

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezés, a házirend-definíció nem kompatibilis erőforrások az Azure környezetben azonosítására, hozzárendelve.

További információt a házirendek, annak érdekében, hogy erőforrásokat létrehozni a **jövőbeli** megfelelő, továbbra is az oktatóanyag:

> [!div class="nextstepaction"]
> [Létrehozás és házirendek kezelése](./create-manage-policy.md)

