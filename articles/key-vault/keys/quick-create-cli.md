---
title: Kulcsok attribútumainak létrehozása és lekérése Azure Key Vaultban – Azure CLI
description: Gyors útmutató, amely bemutatja, hogyan lehet kulcsokat beállítani és lekérni Azure Key Vault az Azure CLI használatával
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.openlocfilehash: e9d56f0ad2da696eb683ea393ad75254ba3ce954
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87061314"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-cli"></a>Rövid útmutató: kulcs beállítása és lekérése Azure Key Vault az Azure CLI használatával

Ebben a rövid útmutatóban egy kulcstartót hoz létre Azure Key Vault az Azure CLI-vel. Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. További információ a Key Vaultről: [Áttekintés](../general/overview.md). Az Azure CLI az Azure-erőforrások létrehozására és kezelésére szolgál, parancsok és szkriptek alkalmazásával. Ha ezt elvégezte, egy kulcsot fog tárolni.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

Ha a parancssori felülettel szeretne bejelentkezni az Azure-ba, írja be a következőt:

```azurecli
az login
```

Ha további információt szeretne a bejelentkezési lehetőségekről a CLI-n keresztül, tekintse meg az [Azure CLI-vel való bejelentkezést](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) ismertető részt.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehoz egy *ContosoResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Azután létrehoz egy Key Vaultot abban az erőforráscsoportban, amelyet az előző lépésben hozott létre. A következő információkat kell megadnia:

- Ehhez a rövid útmutatóhoz a **Contoso-vault2** nevet használjuk. A tesztelésnél egyedi nevet adjon meg.
- Erőforráscsoport neve **ContosoResourceGroup**.
- Hely: **USA keleti régiója**.

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

Ezzel a parancsmaggal megjelenítheti az újonnan létrehozott Key Vault tulajdonságait. Jegyezze fel az alábbi két tulajdonságot:

- **Tároló neve**: A példában ez a **Contoso-Vault2**. Ezt a nevet fogja más Key Vault-parancsokban is megadni.
- **Tár URI-ja**: A példában ez a https://contoso-vault2.vault.azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Az Azure-fiókja jelenleg az egyetlen, amelyik jogosult arra, hogy műveleteket végezzen ezen az új tárolón.

## <a name="add-a-key-to-key-vault"></a>Kulcs hozzáadása a Key Vaulthoz

A kulcs a tárolóhoz való hozzáadásához mindössze néhány további lépést kell elvégeznie. Ezt a kulcsot egy alkalmazás használhatja. 

A nevezett **ExampleKey** létrehozásához írja be az alábbi parancsokat:

```azurecli
az keyvault key create --vault-name "Contoso-Vault2" -n ExampleKey --protection software
```

Most már hivatkozhat erre a kulcsra, amelyet a Azure Key Vaulthoz adott hozzá az URI használatával. **https://Contoso-Vault2.vault.azure.net/keys/ExampleKey**Az aktuális verzió beszerzéséhez használja a következőt:. 

A korábban tárolt kulcs megtekintése:

```azurecli

az keyvault key show --name "ExampleKey" --vault-name "Contoso-Vault2"
```

Most létrehozott egy Key Vault, tárolt egy kulcsot, és beolvasta azt.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, az [az group delete](/cli/azure/group) paranccsal eltávolíthatja az erőforráscsoportot és az összes kapcsolódó erőforrást. Az erőforrásokat a következőképpen törölheti:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, és tárolt benne egy kulcsot. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](../general/overview.md)
- Tekintse meg az Azure CLI-re vonatkozó hivatkozást az kulcstartó [parancsainál](/cli/azure/keyvault?view=azure-cli-latest)
- [Azure Key Vault ajánlott eljárások](../general/best-practices.md) áttekintése
