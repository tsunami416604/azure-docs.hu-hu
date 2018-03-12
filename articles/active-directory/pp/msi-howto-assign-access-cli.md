---
title: "A felhasználó által hozzárendelt MSI hozzáférés hozzárendelése egy Azure-erőforrás, Azure parancssori felület használatával"
description: "Lépésről lépésre utasítások hozzárendelése a felhasználó által hozzárendelt MSI egy erőforráson elérni egy másik erőforrás, Azure parancssori felület használatával."
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
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 8cdfd80f436a9ef39426da9d3f2f76ae59677847
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="assign-a-user-assigned-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>A felhasználó által hozzárendelt felügyelt szolgáltatás identitásának (MSI) hozzáférés hozzárendelése egy erőforrást, az Azure parancssori felület használatával

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Ha egy felhasználó által hozzárendelt MSI létrehozta, a MSI hozzáférést biztosíthat más erőforráshoz, hasonlóan egy rendszerbiztonsági tagot. Ez a példa bemutatja, hogyan való hozzáférést egy felhasználó által hozzárendelt MSI-fájl az Azure storage-fiókhoz, az Azure parancssori felület használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Ebben az oktatóanyagban a CLI-parancsfájlt példák futtatásához két lehetőség közül választhat:

- Használjon [Azure Cloud rendszerhéj](~/articles/cloud-shell/overview.md) vagy Azure-portálról, vagy a "próbálja" gombra, keresztül minden kódblokk jobb felső sarkában található.
- [Telepítse a legújabb verziót a CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 vagy újabb verzió) Ha a helyi CLI-konzollal szeretné. Majd jelentkezzen be az Azure használatával [az bejelentkezési](/cli/azure/reference-index#az_login). Használjon olyan fiókot, amelybe szeretne telepíteni, a felhasználó által hozzárendelt MSI és a virtuális gép Azure-előfizetéssel társított:

   ```azurecli
   az login
   ```

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Az MSI-hozzáférés hozzárendelése egy másik erőforrás RBAC használata

Ahhoz, hogy egy olyan MSI Csomaghoz egy gazdagép-erőforrás (például egy virtuális gép), a bejelentkezési és erőforrás-hozzáférési, az MSI először hozzáférést kell rendelni a erőforrás szerepkör-hozzárendelés keresztül. Ehhez a gazdagépen, és azután a az MSI való társítás előtt. Létrehozása és egy virtuális Gépet társít a lépéseket, tekintse meg a [egy felhasználó által hozzárendelt MSI konfigurálja a virtuális gépek Azure parancssori felület használatával](msi-qs-configure-cli-windows-vm.md).

A következő példában egy felhasználó által hozzárendelt MSI való hozzáférése a storage-fiók.  

1. Az MSI hozzáférést, az ügyfél-azonosítója (más néven Alkalmazásazonosító) az MSI-szolgáltatás egyszerű van szüksége. Az ügyfél-azonosító által biztosított [az identitás létrehozása](/cli/azure/identity#az_identity_create), a MSI-t és az egyszerű szolgáltatásnév (Itt referenciaként látható) telepítése után:

   ```azurecli-interactive
   az identity create -g rgPrivate -n msiServiceApp
   ```

   A sikeres válasz tartalmazza a felhasználó által hozzárendelt MSI egyszerű szolgáltatásnév, az ügyfél-azonosítója a `clientId` tulajdonság:

   ```json
   {
        "clientId": "9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp/credentials?tid=733a8f0p-ec41-4e69-8adz-971fc4b533bl&oid=z4baa4fc-fce4-4202-8250-5fb359abblll&aid=9391e5b1-dada-4a8z-834a-43ad44f296bl",
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.ManagedIdentity/userAssignedIdentities/msiServiceApp",
        "location": "westcentralus",
        "name": "msiServiceApp",
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "resourceGroup": "rgPrivate",
        "tags": {},
        "tenantId": "733a8f0p-ec41-4e69-8adz-971fc4b533bl",
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
   }
   ```

2. Ha az ügyfél-Azonosítót is ismert, a [az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az_role_assignment_create) az MSI-hozzáférés hozzárendelése egy másik erőforrás. Ügyeljen arra, hogy az az ügyfél-azonosító használata a `--assignee` paraméter, és a megfelelő előfizetési azonosító és az erőforrás csoport nevét, ahogy futtatásakor visszaadott `az identity create`. Itt az MSI "Olvasó" hozzáférési van rendelve egy "myStorageAcct" nevű tárfiókot:

   ```azurecli-interactive
   az role assignment create --assignee 9391e5b1-dada-4a8z-834a-43ad44f296bl --role Reader --scope /subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

   A sikeres válasz a következő kimeneti hasonlít:

   ```json
   {
        "id": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct/providers/Microsoft.Authorization/roleAssignments/f4766864-9493-43c6-91d7-abd131c3c017",
        "name": "f4766864-9493-43c6-91d7-abd131c3c017",
        "properties": {
            "additionalProperties": {
            "createdBy": null,
            "createdOn": "2017-12-21T20:49:37.5590544Z",
            "updatedBy": "pd78b21f-17a4-41az-b7db-9aadec3376bl",
            "updatedOn": "2017-12-21T20:49:37.5590544Z"
            },
        "principalId": "z4baa4fc-fce4-4202-8250-5fb359abblll",
        "roleDefinitionId": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "scope": "/subscriptions/90z696ff-5efa-4909-a64d-f1b616f423ll/resourcegroups/rgPrivate/providers/Microsoft.Storage/storageAccounts/myStorageAcct"
        },
        "resourceGroup": "rgPrivate",
        "type": "Microsoft.Authorization/roleAssignments"
   }
   ```

## <a name="next-steps"></a>További lépések

- MSI áttekintését lásd: [Szolgáltatásidentitás felügyelete – áttekintés](msi-overview.md).
- Ahhoz, hogy egy felhasználó által hozzárendelt MSI egy Azure virtuális gépen, lásd: [egy felhasználó által hozzárendelt felügyelt szolgáltatás identitásának (MSI) konfigurálja a virtuális gépek Azure parancssori felület használatával](msi-qs-configure-cli-windows-vm.md).

Az alábbi Megjegyzések szakasz segítségével visszajelzést, és segítsen pontosítsa és a tartalom.

