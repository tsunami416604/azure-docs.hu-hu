---
title: Felügyelt identitások konfigurálása az Azure App konfigurációjával
description: Megtudhatja, hogyan működnek a felügyelt identitások az Azure App konfigurációjában, és hogyan konfigurálhatók felügyelt identitások
author: jpconnock
ms.topic: article
ms.date: 02/25/2020
ms.author: jeconnoc
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: fe66466395a100221e6a3cdebdef870bdf195afc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623026"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Felügyelt identitások használata az Azure App-konfigurációhoz

Ez a témakör bemutatja, hogyan hozhat létre felügyelt identitást az Azure App Configuration. Az Azure Active Directory (AAD) felügyelt identitása lehetővé teszi, hogy az Azure App Configuration egyszerűen hozzáférhessen más AAD-védelemmel ellátott erőforrásokhoz, például az Azure Key Vaulthoz. Az identitást az Azure platform kezeli. Nem követeli meg, hogy a rendelkezésre, vagy forgassa a titkokat. A felügyelt identitások Az AAD-ban további információkért lásd: [Felügyelt identitások az Azure-erőforrásokhoz.](../active-directory/managed-identities-azure-resources/overview.md)

A kérelem kétféle identitást kaphat:

- A **rendszer által hozzárendelt identitás** a konfigurációs tárolóhoz van kötve. A rendszer törli, ha a konfigurációs tároló törlődik. Egy konfigurációs tároló csak egy rendszeráltal hozzárendelt identitással rendelkezhet.
- A **felhasználó által hozzárendelt identitás** egy önálló Azure-erőforrás, amely hozzárendelhető a konfigurációs tárolóhoz. Egy konfigurációs tároló több felhasználó által hozzárendelt identitással is rendelkezhet.

## <a name="adding-a-system-assigned-identity"></a>Rendszerhez rendelt identitás hozzáadása

Egy alkalmazáskonfigurációs tároló létrehozása rendszeráltal hozzárendelt identitással egy további tulajdonságot kell beállítani az áruházban.

### <a name="using-the-azure-cli"></a>Az Azure parancssori felületének használata

Felügyelt identitás beállítása az Azure CLI használatával, használja az [az appconfig identity assign] parancsot egy meglévő konfigurációs tároló ellen. Ebben a szakaszban három lehetőség közül választhat a példák futtatásához:

- Használja az [Azure Cloud Shell](../cloud-shell/overview.md) az Azure Portalon.
- Használja a beágyazott Azure Cloud Shell segítségével a "Try It" gomb, található a jobb felső sarokban minden kódblokk alatt.
- [Telepítse az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.1-es vagy újabb) legújabb verzióját, ha helyi CLI-konzolt szeretne használni.

A következő lépések végigvezetik egy alkalmazáskonfigurációs áruház létrehozásán, és identitás hozzárendelésén a CLI használatával:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login] paranccsal jelentkezzen be az Azure-ba. Az Azure-előfizetéséhez társított fiók használata:

    ```azurecli-interactive
    az login
    ```

1. Hozzon létre egy alkalmazáskonfigurációs áruházat a CLI használatával. A CLI azure-beli alkalmazáskonfigurációval való használatának további példáit az [Alkalmazáskonfigurációs CLI-minták című témakörben talál:](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Futtassa az [az appconfig identity assign] parancsot a rendszeráltal hozzárendelt identitás létrehozásához ehhez a konfigurációs tárolóhoz:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Felhasználó által hozzárendelt identitás hozzáadása

Az alkalmazáskonfigurációs tároló felhasználó által hozzárendelt identitással való létrehozása megköveteli az identitás létrehozását, majd az erőforrás-azonosító hozzárendelését az üzlethez.

### <a name="using-the-azure-cli"></a>Az Azure parancssori felületének használata

Felügyelt identitás beállítása az Azure CLI használatával, használja az [az appconfig identity assign] parancsot egy meglévő konfigurációs tároló ellen. Ebben a szakaszban három lehetőség közül választhat a példák futtatásához:

- Használja az [Azure Cloud Shell](../cloud-shell/overview.md) az Azure Portalon.
- Használja a beágyazott Azure Cloud Shell segítségével a "Try It" gomb, található a jobb felső sarokban minden kódblokk alatt.
- [Telepítse az Azure CLI legújabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 vagy újabb), ha helyi CLI-konzolt szeretne használni.

A következő lépések végigvezetik egy felhasználó által hozzárendelt identitás és egy alkalmazáskonfigurációs tároló létrehozásán, majd az identitás hozzárendelésén az üzlethez a CLI használatával:

1. Ha az Azure CLI-t helyi konzolban használja, akkor először az [az login] paranccsal jelentkezzen be az Azure-ba. Az Azure-előfizetéséhez társított fiók használata:

    ```azurecli-interactive
    az login
    ```

1. Hozzon létre egy alkalmazáskonfigurációs áruházat a CLI használatával. A CLI azure-beli alkalmazáskonfigurációval való használatának további példáit az [Alkalmazáskonfigurációs CLI-minták című témakörben talál:](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Hozzon létre egy felhasználó `myUserAssignedIdentity` által hozzárendelt identitást, amelyet a CLI használatával hívnak meg.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    A parancs kimenetében vegye figyelembe a `id` tulajdonság értékét.

1. Futtassa az [az appconfig identity assign] parancsot az új, felhasználó által hozzárendelt identitás hozzárendeléséhez ehhez a konfigurációs tárolóhoz. Használja az előző `id` lépésben feljegyzett tulajdonság értékét.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Identitás eltávolítása

A rendszer által hozzárendelt identitás eltávolítható a szolgáltatás letiltásával az [az appconfig identitás eltávolítási](/cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-remove) parancs használatával az Azure CLI használatával. A felhasználó által hozzárendelt identitások egyenként is eltávolíthatók. Ha ily módon eltávolítja a rendszerhez rendelt identitást, az az AAD-ből is törlődik. A rendszer által hozzárendelt identitások is automatikusan törlődnek az AAD-ből az alkalmazáserőforrás törlésekor.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hozzon létre egy ASP.NET-alkalmazást az Azure App Konfigurációjával](quickstart-aspnet-core-app.md)

[az appconfig identitás hozzárendelése]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[az login]: /cli/azure/reference-index#az-login
