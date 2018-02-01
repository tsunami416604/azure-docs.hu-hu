---
title: "Szabályzat-hozzárendelés létrehozása Azure környezetben a nem megfelelő erőforrások azonosításához az Azure CLI használatával | Microsoft Docs"
description: "A PowerShell használatával létrehozhat egy Azure szabályzat-hozzárendelést a nem megfelelő erőforrások azonosításához."
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 01/17/2018
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 76725f3ebeaf5af4f2ab8aadb303d862fa111ecb
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-with-the-azure-cli"></a>Szabályzat-hozzárendelés létrehozása Azure környezetben a nem megfelelő erőforrások azonosításához az Azure CLI használatával

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása. Ez a rövid útmutató végigvezeti Önt a folyamaton, amellyel létrehozhat egy felügyelt lemezeket nem használó virtuális gépek azonosítására szolgáló szabályzat-hozzárendelést.

A folyamat végén sikeresen fogja azonosítani a felügyelt lemezeket nem használó virtuális gépeket. Ezek a szabályzat-hozzárendelés szempontjából *nem megfelelőnek* minősülnek.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ehhez a rövid útmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hozunk létre, és hozzárendeljük a Felügyelt lemezeket nem használó virtuális gépek naplózása definíciót. Ez a szabályzat-definíció olyan erőforrásokat azonosít, amelyek nem felelnek meg a szabályzat-definícióban meghatározott feltételeknek.

Kövesse az alábbi lépéseket egy új szabályzat-hozzárendelés létrehozásához:

1. Regisztrálja a Policy Insights erőforrás-szolgáltatót, hogy az előfizetése együtt tudjon működni az erőforrás-szolgáltatóval. Egy erőforrás-szolgáltató regisztrálásához rendelkeznie kell az erőforrás-szolgáltató regisztrálási műveletének elvégzésére vonatkozó engedéllyel. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják.

    Regisztrálja az erőforrás-szolgáltatót a következő parancs futtatásával:

    ```azurecli
    az provider register --namespace Microsoft.PolicyInsights
    ```

    A parancs egy üzenetet ad vissza, amely tájékoztatja, hogy a regisztráció folyamatban van.

    Amíg az előfizetése tartalmaz az erőforrás-szolgáltatótól származó erőforrástípusokat, addig az erőforrás-szolgáltató regisztrációja nem törölhető. Az erőforrás-szolgáltatók regisztrálásával és megtekintésével kapcsolatos további információért tekintse meg az [erőforrás-szolgáltatókat és típusaikat](../azure-resource-manager/resource-manager-supported-services.md) ismertető cikket.

2. Jelenítse meg az összes szabályzat-meghatározást, majd keresse meg a *Felügyelt lemezeket nem használó virtuális gépek naplózása* című szabályzat-definíciót:

    ```azurecli
az policy definition list
```

    Az Azure Policy beépített szabályzatdefiníciókat tartalmaz, amelyeket felhasználhat. Többek között a következő beépített szabályzat-definíciókat láthatja:

    - Címke és a hozzá tartozó érték kényszerítése
    - Címke és a hozzá tartozó érték alkalmazása
    - SQL Server 12.0-ás verziójának megkövetelése

3. Következő lépésként adja meg az alábbi információkat, majd futtassa a következő parancsot a szabályzat-definíció hozzárendeléséhez:

    - A szabályzatdefinícióhoz tartozó megjelenített **név**. Ebben az esetben a név legyen *Felügyelt lemezeket nem használó virtuális gépek naplózása*.
    - **Szabályzat** – Ez az a szabályzatdefiníció, amely alapján létre fogja hozni a hozzárendelést. Ezúttal a *Felügyelt lemezeket nem használó virtuális gépek naplózása* szabályzatdefiníciót fogjuk használni
    - **Hatókör** – A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve. Ez egyetlen előfizetéstől teljes erőforráscsoportokig terjedhet.

    Használja az előzőleg regisztrált előfizetést (vagy erőforráscsoportot). Ebben a példában a **bc75htn-a0fhsi-349b-56gh-4fghti-f84852** előfizetés-azonosítót és a **FabrikamOMS** erőforráscsoport-nevet használjuk. Ne felejtse el módosítani ezeket az Ön által használt előfizetés-azonosítóra, illetve erőforráscsoport-névre.

    A parancsnak ehhez hasonlóan kell kinéznie:

    ```azurecli
az policy assignment create --name Audit Virtual Machines without Managed Disks Assignment --policy Audit Virtual Machines without Managed Disks --scope /subscriptions/
bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

A szabályzat-hozzárendelés egy olyan szabályzat, amely egy adott hatókörön belül érvényes. Ez a hatókör szintén bármi lehet egy felügyeleti csoporttól egy erőforráscsoportig.

## <a name="identify-non-compliant-resources"></a>Nem megfelelő erőforrások azonosítása

Az új hozzárendelésnek nem megfelelő erőforrások megtekintéséhez:

1. Lépjen vissza az Azure Policy oldalára.
2. Válassza a **Megfelelőség** elemet a bal oldali panelen, majd keresse meg az imént létrehozott **Szabályzat-hozzárendelést**.

   ![Szabályzatmegfelelőség](media/assign-policy-definition/policy-compliance.png)

   Az olyan meglévő erőforrások, amelyek nem felelnek meg az új hozzárendelésnek, a **Nem megfelelő erőforrások** lapon jelennek meg. Az előző képen nem megfelelő erőforrások példái láthatók.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, törölje a létrehozott hozzárendelést a következő parancs futtatásával:

```azurecli
az policy assignment delete –name  Assignment --scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852 resourceGroups/ FabrikamOMS
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban hozzárendelt egy szabályzatdefiníciót az Azure-környezetben megtalálható, nem megfelelő erőforrások azonosítása céljából.

A szabályzatok a **jövőben** létrehozott erőforrások megfelelőségének biztosítása érdekében történő hozzárendeléséről a következő oktatóanyagban találhat több információt:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./create-manage-policy.md)
