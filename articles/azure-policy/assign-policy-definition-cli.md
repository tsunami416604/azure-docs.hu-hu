---
title: "Az Azure környezetben nem kompatibilis erőforrások azonosítására házirend-hozzárendelés létrehozása az Azure parancssori felület használatával |} Microsoft Docs"
description: "A PowerShell szolgáltatás használatával hozzon létre egy Azure házirend-hozzárendelés nem kompatibilis erőforrások azonosítására."
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark
ms.date: 11/02/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 764554a6afcc7912c53fc5000a6af44abb2adc99
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Nem kompatibilis erőforrások az Azure környezetben az Azure parancssori felület segítségével azonosíthatja a házirend-hozzárendelés létrehozása

Az első lépés az Azure-ban ismertetése megfelelőségi ismerete amennyiben állniuk saját aktuális erőforrásokkal. A gyors üzembe helyezés végigvezeti a házirend-hozzárendelés nem felügyelt lemezeket használó virtuális gépek azonosításához létrehozásának folyamatán.

Ez a folyamat végén sikeresen azonosítja mely virtuális gépek nem használja a felügyelt lemezeket, és ezért *nem megfelelő*.
.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="opt-in-to-azure-policy"></a>Az Azure házirend engedélyezve

Az Azure házirend mostantól nyilvános előzetes verziójában, és a hozzáférés kéréséhez regisztrálnia kell.

1. Keresse fel Azure házirend https://aka.ms/getpolicy, és válassza ki a **regisztráció** a bal oldali ablaktáblán.

   ![Keresse meg a házirend](media/assign-policy-definition/sign-up.png)

2. Az előfizetések kiválasztásával Azure házirend engedélyezve a **előfizetés** lista szeretne együttműködni. Válassza ki **regisztrálása**.

   ![Szeretné használni az Azure-házirend](media/assign-policy-definition/preview-opt-in.png)

   A kérelem automatikus jóváhagyása Preview. Várja meg a regisztrációt feldolgozni a rendszer akár 30 percet.

## <a name="create-a-policy-assignment"></a>Házirend-hozzárendelés létrehozása

A gyors üzembe helyezés, az azt házirend-hozzárendelés létrehozása és a naplózási virtuális számítógépek felügyelt lemezek meghatározása nélkül. Ez a házirend-definíció azonosítja az erőforrásokat, amelyek nem felelnek meg a házirend-definíció a feltételeket.

Kövesse az alábbi lépéseket egy új házirend-hozzárendelést létrehozni.

Megtekintheti az összes házirend-beállítást, és keresse a "Az ellenőrzési virtuális gépek kezelt lemez nélkül" házirend-definíció:

```azurecli
az policy definition list
```

Az Azure házirend mellékelt már beépített házirend-definíciókban használható. Beépített házirend-definíciók például jelenik meg:

- Címke és annak értéke
- Alkalmazza a címke és annak értéke
- Szükséges SQL Server verzió 12.0

Ezután adja meg a következő adatokat, és a házirend-definíció rendelhető hozzá a következő parancsot:

- Megjelenítési **neve** a házirend-hozzárendelés. Ebben az esetben most használja *naplózási virtuális gépek nélkül kezelt lemezek*.
- **Házirend** – Ez a házirend-definíció, alapú ki, amelyek a hozzárendelés létrehozásához segítségével az. Ebben az esetben a házirend-definíció – *naplózási virtuális gépek nélkül kezelt lemezek*
- A **hatókör** - hatókör határozza meg, milyen erőforrásokat, vagy az erőforrások csoportosítása a házirend-hozzárendelés lekérdezi kényszeríti. Ez terjedhet előfizetés erőforráscsoportokhoz.

  Az előfizetés (vagy erőforráscsoport) már korábban regisztrált amikor Azure házirendbe választotta használja ebben a példában használjuk az előfizetés-azonosító - **bc75htn-a0fhsi-349b-56gh-4fghti-f84852** és az erőforráscsoport neve - **FabrikamOMS**. Ne feledje módosítani ezeket az előfizetés Azonosítóját és dolgozunk erőforráscsoport nevét.

Ez az, hogy mi a parancs hasonlóan kell kinéznie:

```azurecli
az policy assignment create --name Audit Virtual Machines without Managed Disks Assignment --policy Audit Virtual Machines without Managed Disks --scope /subscriptions/
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
az policy assignment delete –name  Assignment --scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852 resourceGroups/ FabrikamOMS
```

## <a name="next-steps"></a>Következő lépések

A gyors üzembe helyezés, a házirend-definíció nem kompatibilis erőforrások az Azure környezetben azonosítására, hozzárendelve.

További információt a házirendek, annak érdekében, hogy erőforrásokat létrehozni a **jövőbeli** megfelelő, továbbra is az oktatóanyag:

> [!div class="nextstepaction"]
> [Létrehozás és házirendek kezelése](./create-manage-policy.md)
