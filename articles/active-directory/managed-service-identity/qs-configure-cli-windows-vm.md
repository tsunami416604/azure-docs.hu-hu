---
title: "MSI konfigurálása az Azure virtuális gép Azure parancssori felület használatával"
description: "Lépés által felügyelt szolgáltatás identitásának (MSI) konfigurálásához egy Azure virtuális gépen, Azure parancssori felület használatának részletes utasításokat."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 0863d5bec71a79f28017582eaa111f6c4a97c1ec
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="configure-a-vm-managed-service-identity-msi-using-azure-cli"></a>Konfigurálja a virtuális gép felügyelt szolgáltatás identitásának (MSI) Azure parancssori felület használatával

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt Szolgáltatásidentitást az Azure Active Directoryban automatikusan felügyelt identitással Azure szolgáltatásokat biztosít. Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatokat a kódban a szolgáltatással való hitelesítésre szolgáló használhatja. 

Ebből a cikkből megtudhatja engedélyezése, és távolítsa el az MSI-fájl egy Azure virtuális gép, Azure parancssori felület használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

A parancssori felület parancsfájl példák futtatásához három lehetőség közül választhat:

- Használjon [Azure Cloud rendszerhéj](../../cloud-shell/overview.md) Azure-portálról (lásd a következő szakaszt).
- A beágyazott Azure Cloud rendszerhéj a "próbálja" gombra, minden egyes kódblokk jobb felső sarkában található keresztül használja.
- [Telepítse a legújabb verziót a CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 vagy újabb verzió) Ha a helyi CLI-konzollal szeretné. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>MSI engedélyezése az Azure virtuális gép létrehozása

Az MSI-kompatibilis virtuális gép létrehozása:

1. Az Azure parancssori felület a helyi konzol használata, először jelentkezzen be az Azure használatával [az bejelentkezési](/cli/azure/reference-index#az_login). Használjon olyan fiókot, amelybe szeretne telepíteni a virtuális Gépet az Azure-előfizetéshez társított:

   ```azurecli-interactive
   az login
   ```

2. Hozzon létre egy [erőforráscsoport](../../azure-resource-manager/resource-group-overview.md#terminology) elszigetelési és telepítéséhez, a virtuális Gépet, és azok kapcsolódó erőforrásait, használatával [az csoport létrehozása](/cli/azure/group/#az_group_create). Ezt a lépést kihagyhatja, ha már van erőforráscsoport szeretné használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Hozzon létre egy virtuális gép az [az virtuális gép létrehozása](/cli/azure/vm/#az_vm_create). Az alábbi példakód létrehozza a virtuális gépek nevű *myVM* rendelkező egy olyan MSI Csomaghoz, amint azt a `--assign-identity` paraméter. A `--admin-username` és `--admin-password` paraméterek fiókot ad meg a rendszergazda felhasználó nevét és jelszavát virtuálisgép-bejelentkezés. Ezeket az értékeket a környezetének megfelelő frissítése: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Egy meglévő Azure virtuális gépen az MSI engedélyezése

Ha egy meglévő virtuális gépen az MSI engedélyezni kell:

1. Az Azure parancssori felület a helyi konzol használata, először jelentkezzen be az Azure használatával [az bejelentkezési](/cli/azure/reference-index#az_login). Az Azure-előfizetés, amely tartalmazza a virtuális Géphez társított olyan fiókot használjon. Emellett győződjön meg arról, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi a virtuális Gépen, például a "Virtuális gép közreműködő" írási engedélyekkel:

   ```azurecli-interactive
   az login
   ```

2. Használja [az vm hozzárendelése-identitás](/cli/azure/vm/#az_vm_assign_identity) rendelkező a `--assign-identity` paraméter egy olyan MSI Csomaghoz hozzáadása egy meglévő virtuális Gépen:

   ```azurecli-interactive
   az vm assign-identity -g myResourceGroup -n myVm
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Az Azure virtuális gép MSI eltávolítása

Ha egy virtuális gép, amely már nem kell egy olyan MSI Csomaghoz:

1. Az Azure parancssori felület a helyi konzol használata, először jelentkezzen be az Azure használatával [az bejelentkezési](/cli/azure/reference-index#az_login). Az Azure-előfizetés, amely tartalmazza a virtuális Géphez társított olyan fiókot használjon. Emellett győződjön meg arról, hogy a fiókja tagja egy szerepkör, amely lehetővé teszi a virtuális Gépen, például a "Virtuális gép közreműködő" írási engedélyekkel:

   ```azurecli-interactive
   az login
   ```

2. Használja a `-n ManagedIdentityExtensionForWindows` vagy `-n ManagedIdentityExtensionForLinux` kapcsoló (virtuális gép függően) [az virtuálisgép-bővítmény törlése](https://docs.microsoft.com/cli/azure/vm/#assign-identity) az MSI eltávolítása:

   ```azurecli-interactive
   az vm extension delete --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
   ```

## <a name="related-content"></a>Kapcsolódó tartalom

- [Felügyelt Szolgáltatásidentitás áttekintése](overview.md)
- A teljes Azure virtuális gépek létrehozására – Gyorsútmutatók lásd: 

  - [Windows virtuális gép létrehozása a parancssori felület](../../virtual-machines/windows/quick-create-cli.md)  
  - [Linux virtuális gép létrehozása a parancssori felület](../../virtual-machines/linux/quick-create-cli.md) 

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.
















