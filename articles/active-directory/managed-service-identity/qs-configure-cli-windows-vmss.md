---
title: MSI konfigurálása az Azure virtuálisgép-méretezési beállítása az Azure parancssori felület használatával
description: Lépés által felügyelt szolgáltatás identitásának (MSI) konfigurálásához egy Azure virtuálisgép-méretezési, Azure parancssori felület használatának részletes utasításokat.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: 9cdf5225f2d87fffa2290e3edd09d4ae829aee21
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Konfigurálja a virtuálisgép-méretezési készlet által felügyelt szolgáltatás Identity (MSI) Azure parancssori felület használatával

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt Szolgáltatásidentitást az Azure Active Directoryban automatikusan felügyelt identitással Azure szolgáltatásokat biztosít. Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatokat a kódban a szolgáltatással való hitelesítésre szolgáló használhatja. 

Ebből a cikkből megtudhatja engedélyezésével és az Azure parancssori felület használatával be egy Azure virtuálisgép-méretezési MSI eltávolítása.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

A parancssori felület parancsfájl példák futtatásához három lehetőség közül választhat:

- Használjon [Azure Cloud rendszerhéj](../../cloud-shell/overview.md) Azure-portálról (lásd a következő szakaszt).
- A beágyazott Azure Cloud rendszerhéj a "próbálja" gombra, minden egyes kódblokk jobb felső sarkában található keresztül használja.
- [Telepítse a legújabb verziót a CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 vagy újabb verzió) Ha a helyi CLI-konzollal szeretné. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set"></a>Egy Azure virtuálisgép-méretezési csoport létrehozásakor MSI engedélyezése

Az MSI-kompatibilis virtuálisgép-méretezési készlet létrehozása:

1. Az Azure parancssori felület a helyi konzol használata, először jelentkezzen be az Azure használatával [az bejelentkezési](/cli/azure/reference-index#az_login). Használjon olyan fiókot, amelybe szeretne telepíteni a virtuálisgép-méretezési csoport Azure-előfizetéssel társított:

   ```azurecli-interactive
   az login
   ```

2. Hozzon létre egy [erőforráscsoport](../../azure-resource-manager/resource-group-overview.md#terminology) elszigetelési és telepítéséhez, a virtuálisgép-méretezési csoport és azok kapcsolódó erőforrásait, használatával [az csoport létrehozása](/cli/azure/group/#az_group_create). Ezt a lépést kihagyhatja, ha már van erőforráscsoport szeretné használni:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Hozzon létre egy virtuálisgép-méretezési segítségével [az vmss létrehozása](/cli/azure/vmss/#az_vmss_create) . Az alábbi példa létrehoz egy virtuálisgép-méretezési beállítása a nevesített *myVMSS* rendelkező egy olyan MSI Csomaghoz, amint azt a `--assign-identity` paraméter. A `--admin-username` és `--admin-password` paraméterek fiókot ad meg a rendszergazda felhasználó nevét és jelszavát virtuálisgép-bejelentkezés. Ezeket az értékeket a környezetének megfelelő frissítése: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

## <a name="enable-msi-on-an-existing-azure-virtual-machine-scale-set"></a>Egy meglévő Azure virtuálisgép-méretezési csoport MSI engedélyezése

Ha egy meglévő Azure virtuálisgép-méretezési csoport MSI engedélyezni kell:

1. Az Azure parancssori felület a helyi konzol használata, először jelentkezzen be az Azure használatával [az bejelentkezési](/cli/azure/reference-index#az_login). Az Azure-előfizetés, amely tartalmazza a virtuálisgép-méretezési csoport társított olyan fiókot használjon.

   ```azurecli-interactive
   az login
   ```

2. Használjon [az vmss identitás hozzárendelése](/cli/azure/vmss/identity/#az_vmss_identity_assign) parancs futtatásával adja hozzá egy meglévő virtuális gép egy olyan MSI Csomaghoz:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Egy Azure virtuálisgép-méretezési csoport MSI eltávolítása

Ha egy virtuálisgép-méretezési csoport, amely már nem kell egy olyan MSI Csomaghoz:

1. Az Azure parancssori felület a helyi konzol használata, először jelentkezzen be az Azure használatával [az bejelentkezési](/cli/azure/reference-index#az_login). Az Azure-előfizetés, amely tartalmazza a virtuálisgép-méretezési csoport társított olyan fiókot használjon.

   ```azurecli-interactive
   az login
   ```

2. Használjon [az vmss identitás eltávolítása](/cli/azure/vmss/identity/#az_vmss_remove_identity) parancs beírásával távolítsa el a MSI:

   ```azurecli-interactive
   az vmss identity remove -g myResourceGroup -n myVMSS --identities readerID writerID
   ```

## <a name="next-steps"></a>További lépések

- [Felügyelt Szolgáltatásidentitás áttekintése](overview.md)
- A teljes Azure-beli virtuálisgép-méretezési létrehozása gyors üzembe helyezés, olvassa el: 

  - [Hozzon létre egy virtuálisgép-méretezési csoport parancssori felület](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.
















