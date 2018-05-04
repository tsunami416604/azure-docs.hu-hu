---
title: Azure rövid útmutató – Key Vault parancssori felület létrehozása | Microsoft Docs
description: Rövid útmutató, amely bemutatja az Azure Key Vault létrehozását a parancssori felület használatával
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: ''
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/16/2018
ms.author: barclayn
ms.openlocfilehash: aaf8b93a41399b7754fb458d7d1d278a64f82139
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="quickstart-create-an-azure-key-vault-using-the-cli"></a>Rövid útmutató: Azure Key Vault létrehozása a parancssori felülettel

Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. A Key Vaulttal kapcsolatos további információt az [Áttekintés](key-vault-overview.md) szakaszban talál. Az Azure CLI az Azure-erőforrások létrehozására és kezelésére szolgál, parancsok és szkriptek alkalmazásával. Ebben cikkben egy Key Vaultot hoz létre. Ebben a rövid útmutatóban egy kulcstartót hoz létre. Miután végzett ezzel, titkos kulcsot fog tárolni.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

Ha szeretne bejelentkezni az Azure-ba a parancssori felület használatával, gépelje be a következőt:

```azurecli
az login
```

A parancssori felület bejelentkezési lehetőségeivel kapcsolatos további információ: [Bejelentkezés az Azure CLI 2.0 használatával](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehoz egy *ContosoResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli
az group create --name 'ContosoResourceGroup' --location eastus
```

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Azután létrehoz egy Key Vaultot abban az erőforráscsoportban, amelyet az előző lépésben hozott létre. A következő információkat kell megadnia:

- Ehhez a rövid útmutatóhoz a **Contoso-vault2** nevet használjuk. A tesztelésnél egyedi nevet adjon meg.
- Erőforráscsoport neve: **ContosoResourceGroup**.
- Hely: **USA keleti régiója**.

```azurecli
az keyvault create --name 'Contoso-Vault2' --resource-group 'ContosoResourceGroup' --location eastus
```

Ezzel a parancsmaggal megjelenítheti az újonnan létrehozott Key Vault tulajdonságait. Jegyezze fel az alábbi két tulajdonságot:

- **Tároló neve**: A példában ez a **Contoso-Vault2**. Ezt a nevet fogja más Key Vault-parancsokban is megadni.
- **Tároló URI-ja**: A példában ez a https://contoso-vault2.vault.azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Az Azure-fiókja jelenleg az egyetlen, amelyik jogosult arra, hogy műveleteket végezzen ezen az új tárolón.

## <a name="add-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz

Titkos kód a tárolóhoz való hozzáadásához csak néhány további lépést kell végrehajtania. Ezt a jelszót egy alkalmazás használhatja. A jelszó neve **ExamplePassword** lesz, és a **Pa$$w0rd** értékét fogja tárolni.

Írja be az alábbi parancsokat, ha a Key Vaultban titkos kódot szeretne létrehozni **ExamplePassword** névvel, amely tárolni fogja a **Pa$$w0rd** értékét:

```azurecli
az keyvault secret set --vault-name 'Contoso-Vault2' --name 'ExamplePassword' --value 'Pa$$w0rd'
```

Az Azure Key Vaulthoz hozzáadott jelszóra ezután az URI használatával hivatkozhat. A jelenlegi verzió beszerzéséhez használja a következőt: **https://ContosoVault.vault.azure.net/secrets/ExamplePassword**. 

A titkos kódban tárolt érték megtekintése egyszerű szövegként:

```azurecli
az keyvault secret show --name 'ExamplePassword' --vault-name 'Contoso-Vault2'
```

Most létrehozott egy Key Vaultot, tárolt egy titkos kulcsot, és lekérte azt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, az [az group delete](/cli/azure/group#delete) paranccsal eltávolíthatja az erőforráscsoportot és az összes kapcsolódó erőforrást. Az erőforrásokat a következőképpen törölheti:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Key Vaultot és tárolt benne egy titkos kulcsot. Ha bővebb információra van szüksége a Key Vaultról és arról, hogyan használhatja az alkalmazásaival, lépjen tovább a Key Vaulttal használható webalkalmazásokat bemutató oktatóanyagra.

> [!div class="nextstepaction"]
> [Az Azure Key Vault használata webalkalmazásból](key-vault-use-from-web-application.md) Ha meg szeretné tudni, hogyan olvashatja be a Key Vault titkos kulcsait egy [felügyeltszolgáltatás-identitást](/active-directory/managed-service-identity/overview.md) használó webalkalmazással, lépjen tovább a következő oktatóanyagra: [Azure-webalkalmazások konfigurálása a Key Vault titkos kulcsainak olvasásához](tutorial-web-application-keyvault.md)
