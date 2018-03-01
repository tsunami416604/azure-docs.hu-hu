---
title: "Szabályozhatja az Azure virtuális gépek Azure parancssori felülettel |} Microsoft Docs"
description: "Útmutató – az Azure virtuális gépek kezelése a Szerepalapú alkalmazásával, házirendekkel, zárolások és címkék az Azure parancssori felület"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: tomfitz
ms.openlocfilehash: 86ac3ec390c7aa9bc24a90ef2ee582f97f8b5407
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="virtual-machine-governance-with-azure-cli"></a>Virtuális gép irányítás Azure parancssori felülettel

[!include[Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Telepítéséhez, és a parancssori felület helyileg használatához, tekintse meg a [Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

## <a name="understand-scope"></a>Hatókör ismertetése

[!include[Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

Ebben az oktatóanyagban alkalmaz minden beállításokat az erőforráscsoporthoz, egyszerűen távolítsa el ezeket a beállításokat, amikor hajtja végre.

Ennek az erőforráscsoportnak hozzon létre.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

Az erőforráscsoport jelenleg üres.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Azt szeretné, győződjön meg róla a szervezethez tartozó felhasználók a megfelelő szintű ezekhez az erőforrásokhoz való hozzáférés. Nem kívánja korlátlan hozzáférést a felhasználók számára, de azt is meg kell győződnie arról tehetik munkájukat. [Szerepköralapú hozzáférés-vezérlés](../../active-directory/role-based-access-control-what-is.md) segítségével kezelheti a jogosult felhasználók hatókörre konkrét műveletek elvégzéséhez.

Hozzon létre, és távolítsa el a szerepkör-hozzárendelések, felhasználónak rendelkeznie kell `Microsoft.Authorization/roleAssignments/*` hozzáférést. A hozzáférés a tulajdonos vagy a felhasználói hozzáférés adminisztrátora szerepkörök keresztül.

A virtuális gép megoldások kezelése, számos három erőforrás-specifikus szerepköröket, amelyek gyakran szükséges hozzáférést biztosítanak.

* [Virtuális gép közreműködő](../../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)
* [Hálózati közreműködő](../../active-directory/role-based-access-built-in-roles.md#network-contributor)
* [Tárolási fiók közreműködői](../../active-directory/role-based-access-built-in-roles.md#storage-account-contributor)

Szerepkörök hozzárendelése az egyéni felhasználók számára, helyett célszerűbb gyakran [Azure Active Directory-csoport létrehozása](../../active-directory/active-directory-groups-create-azure-portal.md) a felhasználók, akik hasonló műveletekre. Adott csoporthoz, majd rendelje hozzá a megfelelő szerepkörhöz. Ez a cikk leegyszerűsítése hoz létre egy Azure Active Directory-csoport tagjai nélkül. Ez a csoport továbbra is hozzárendelése szerepkörhöz hatókörhöz. 

Az alábbi példa létrehoz egy Azure Active Directory csoport nevű *VMDemoContributors* és a levelezési becenév *vmDemoGroup*. A levelezési becenév szolgálja ki a csoporthoz tartozó aliast.

```azurecli-interactive
adgroupId=$(az ad group create --display-name VMDemoContributors --mail-nickname vmDemoGroup --query objectId --output tsv)
```

A parancssor propagálása egész Azure Active Directory csoport után néhány percet vesz igénybe. Miután 20-30 másodpercet várakozik, használja a [az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az_role_assignment_create) parancsot az új Azure Active Directory-csoport hozzárendelése a virtuális gép közreműködő szerepkört ahhoz az erőforráscsoporthoz.  Ha azt propagálása előtt futtassa a következő parancsot, akkor kap egy hiba szerint **egyszerű <guid> nem szerepel a directory**. Próbálja újra futtatni a parancsot.

```azurecli-interactive
az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

Általában, ismételje meg a folyamatot *hálózat közreműködő* és *tárolási fiók közreműködői* való győződjön meg arról, hogy a felhasználók vannak hozzárendelve a telepített erőforrások kezelése. A cikkben kihagyhatja ezeket a lépéseket.

## <a name="azure-policies"></a>Az Azure házirendek

[!include[Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Házirendek alkalmazása

Az előfizetés már rendelkezik több házirend-definíciók. A rendelkezésre álló házirend-definíciók megjelenítéséhez használja a [az házirend-definíció lista](/cli/azure/policy/definition#az_policy_definition_list) parancs:

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

Megjelenik a meglévő házirend-definíciók. A házirend típusát vagy a **beépített** vagy **egyéni**. Nézze át a definícióit, hozzárendelése kívánt feltétel leírása. Ebben a cikkben hozzárendelt házirendek, amelyek:

* Korlátozza a helyek, az összes erőforrást.
* Korlátozza a termékváltozat virtuális gépekhez.
* Virtuális gépek, amelyek nem kezelt lemezek naplózása.

A következő példában három csoportházirend-definíciók a megjelenített név alapján beolvasása. Használja a [az házirend-hozzárendelés létrehozására](/cli/azure/policy/assignment#az_policy_assignment_create) parancs definíciók hozzárendelése az erőforráscsoportot. Egyes szabályzatokat az engedélyezett értékek megadásához paraméter értékeket ad meg.

```azurecli-interactive
# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
locationDefinition=$(az policy definition list --query "[?displayName=='Allowed locations'].name | [0]" --output tsv)
skuDefinition=$(az policy definition list --query "[?displayName=='Allowed virtual machine SKUs'].name | [0]" --output tsv)
auditDefinition=$(az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks'].name | [0]" --output tsv)

# Assign policy for allowed locations
az policy assignment create --name "Set permitted locations" \
  --resource-group myResourceGroup \
  --policy $locationDefinition \
  --params '{ 
      "listOfAllowedLocations": {
        "value": [
          "eastus", 
          "eastus2"
        ]
      }
    }'

# Assign policy for allowed SKUs
az policy assignment create --name "Set permitted VM SKUs" \
  --resource-group myResourceGroup \
  --policy $skuDefinition \
  --params '{ 
      "listOfAllowedSKUs": {
        "value": [
          "Standard_DS1_v2", 
          "Standard_E2s_v2"
        ]
      }
    }'

# Assign policy for auditing unmanaged disks
az policy assignment create --name "Audit unmanaged disks" \
  --resource-group myResourceGroup \
  --policy $auditDefinition
```

Az előző példában azt feltételezi, hogy már ismeri a szabályzat paramétereit. Ha szeretné megtekinteni a paraméterek, használja:

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>A virtuális gép telepítése

Szerepkörök és a házirendeket, készen áll a megoldás üzembe helyezéséhez rendelt hozzá. Az alapértelmezett méret Standard_DS1_v2, amely egyike a megengedett SKU. A parancsot SSH-kulcsok hoz létre, ha azok nem léteznek az alapértelmezett helye.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

A telepítés befejezése után további felügyeleti beállításokat alkalmazhat a megoldás.

## <a name="lock-resources"></a>Erőforrások zárolása

[Erőforrás zárolások](../../azure-resource-manager/resource-group-lock-resources.md) akadályozza meg a szervezet véletlen törlés vagy módosítása a fontos erőforrásokhoz. Szerepköralapú hozzáférés-vezérlés, eltérően erőforrás zárolások alkalmazhatók a korlátozás összes felhasználók és szerepkörök. Beállíthatja a zárolási szint beállítása azokhoz a *CanNotDelete* vagy *ReadOnly*.

Hozzon létre, vagy törölje a felügyeleti zárolás, hozzáféréssel kell rendelkeznie `Microsoft.Authorization/locks/*` műveletek. A beépített szerepkörök, csak **tulajdonos** és **felhasználói hozzáférés adminisztrátora** kapnak a csatolási műveleteket.

A virtuális gép és a hálózati biztonsági csoport zárolása, használja a [az zárolási létrehozása](/cli/azure/lock#az_lock_create) parancs:

```azurecli-interactive
# Add CanNotDelete lock to the VM
az lock create --name LockVM \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVM \
  --resource-type Microsoft.Compute/virtualMachines

# Add CanNotDelete lock to the network security group
az lock create --name LockNSG \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVMNSG \
  --resource-type Microsoft.Network/networkSecurityGroups
```

A zárolásokat teszteléséhez futtassa a következő parancsot:

```azurecli-interactive 
az group delete --name myResourceGroup
```

Arról, hogy a törlési művelet nem hajtható végre egy zárolás miatt hibaüzenet jelenik meg. Az erőforráscsoport csak akkor lehet törölni, ha kifejezetten eltávolítja a zárolása. A lépés megjelenik-e a [erőforrások törlése](#clean-up-resources).

## <a name="tag-resources"></a>Címke erőforrások

Alkalmazott [címkék](../../azure-resource-manager/resource-group-using-tags.md) számára az Azure-erőforrások logikailag kategóriák szerint rendezheti. Minden címke egy névből és egy értékből áll. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

[!include[Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

Címkék azokra a virtuális gép, használja a [az erőforrás címke](/cli/azure/resource#az_resource_tag) parancsot. Az erőforrás-meglévő címkék nem lesznek megőrizve.

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>Erőforrások keresése címke szerint

A címke neve és értéke erőforrások megkereséséhez használja a [az erőforráslistát](/cli/azure/resource#az_resource_list) parancs:

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

A felügyeleti feladatokat, például egy címke az összes virtuális gép leállítása a visszaadott értékeket is használhat.

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>Nézet költségek címkeértékeket.

[!include[Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A zárolt hálózati biztonsági csoport nem törölhető, amíg a rendszer eltávolítja a zárolást. Távolítsa el a zárolást, beolvasása az azonosítók a zárolást, és adja meg azokat a [az zárolási törlése](/cli/azure/lock#az_lock_delete) parancs:

```azurecli-interactive
vmlock=$(az lock show --name LockVM \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Compute/virtualMachines \
  --resource-name myVM --output tsv --query id)
nsglock=$(az lock show --name LockNSG \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Network/networkSecurityGroups \
  --resource-name myVMNSG --output tsv --query id)
az lock delete --ids $vmlock $nsglock
```

Ha már nincs rá szükség, a [az group delete](/cli/azure/group#az_group_delete) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás. Lépjen ki az SSH-munkamenetből a virtuális gépre, majd törölje az erőforrásokat a következő módon:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy egyéni virtuálisgép-rendszerképet. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Felhasználók hozzárendelése egy szerepkörhöz
> * Házirendek, szabványok érvényesítő
> * Zárral kritikus erőforrások védelme
> * A számlázási és a felügyeleti címke erőforrások

Előzetes megtudhatja, hogyan magas rendelkezésre állású virtuális gépek kapcsolatos következő oktatóanyagot.

> [!div class="nextstepaction"]
> [Virtuális gépek figyelése](tutorial-monitoring.md)

