---
title: 'Rövid útmutató: Kulcs beállítása és beolvasása az Azure Key Vaultból'
description: Rövid útmutató, amely bemutatja, hogyan állíthat be és kérhet be egy kulcsot az Azure Key Vaultból az Azure CLI használatával
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.openlocfilehash: defc5317c127d771786989748e404285ca0c0584
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424208"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-cli"></a>Rövid útmutató: Kulcs beállítása és lekérése az Azure Key Vaultból az Azure CLI használatával

Ebben a rövid útmutatóban hozzon létre egy key vault az Azure Key Vault az Azure CLI. Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. A Key Vaultról további információt az [Áttekintés című témakörben talál.](../general/overview.md) Az Azure CLI az Azure-erőforrások létrehozására és kezelésére szolgál, parancsok és szkriptek alkalmazásával. Amint ezt befejezte, tárolni fog egy kulcsot.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

Az Azure-ba való bejelentkezéshez a CLI segítségével, amelyet beírhat:

```azurecli
az login
```

A CLI-n keresztüli bejelentkezési lehetőségekről az [Azure CLI-vel való bejelentkezésről](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) további információt talál.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehoz egy *ContosoResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Azután létrehoz egy Key Vaultot abban az erőforráscsoportban, amelyet az előző lépésben hozott létre. A következő információkat kell megadnia:

- Ehhez a rövid útmutatóhoz a **Contoso-vault2** nevet használjuk. A tesztelésnél egyedi nevet adjon meg.
- Erőforráscsoport neve **ContosoResourceGroup.**
- Hely: **USA keleti régiója**.

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

Ezzel a parancsmaggal megjelenítheti az újonnan létrehozott Key Vault tulajdonságait. Jegyezze fel az alábbi két tulajdonságot:

- **Tároló neve**: A példában ez a **Contoso-Vault2**. Ezt a nevet fogja más Key Vault-parancsokban is megadni.
- **Tár URI-ja**: A példában ez a https://contoso-vault2.vault.azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Az Azure-fiókja jelenleg az egyetlen, amelyik jogosult arra, hogy műveleteket végezzen ezen az új tárolón.

## <a name="add-a-key-to-key-vault"></a>Kulcs hozzáadása a Key Vaulthoz

Kulcs hozzáadása a tárolóhoz, csak néhány további lépést kell tennie. Ezt a kulcsot egy alkalmazás is használhat. 

Az alábbi parancsok beírásával hozzon létre egy **ExampleKey nevű:**

```azurecli
az keyvault key create --vault-name "Contoso-Vault2" -n ExampleKey --protection software
```

Most már hivatkozhat erre a kulcsra, amelyet az URI használatával adott hozzá az Azure Key Vaulthoz. Az **https://Contoso-Vault2.vault.azure.net/keys/ExampleKey** aktuális verzió bekésezéséhez használható. 

A korábban tárolt kulcs megtekintése:

```azurecli

az keyvault key show --name "ExampleKey" --vault-name "Contoso-Vault2"
```

Most létrehozott egy Key Vaultot, tárolt egy kulcsot, és lekérte azt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, az [az group delete](/cli/azure/group) paranccsal eltávolíthatja az erőforráscsoportot és az összes kapcsolódó erőforrást. Az erőforrásokat a következőképpen törölheti:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault ot, és egy kulcsot tárolt benne. Ha többet szeretne megtudni a Key Vaultról és arról, hogyan integrálhatja azt az alkalmazásokkal, folytassa az alábbi cikkekkel.

- Az [Azure Key Vault áttekintésének elolvasása](../general/overview.md)
- Tekintse meg az [Azure CLI az keyvault parancsok](/cli/azure/keyvault?view=azure-cli-latest) hivatkozását
- Tekintse át az [Azure Key Vault gyakorlati tanácsait](../general/best-practices.md)
