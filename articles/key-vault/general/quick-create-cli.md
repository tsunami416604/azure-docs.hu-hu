---
title: Rövid útmutató – Azure Key Vault létrehozása az Azure CLI-vel
description: Gyors útmutató, amely bemutatja, hogyan hozhat létre Azure Key Vault az Azure CLI használatával
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: f3b9758d52c4be8e4d85c398f5ef1d0b3fae7e86
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87541804"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>Gyors útmutató: kulcstartó létrehozása az Azure CLI használatával

A Azure Key Vault egy felhőalapú szolgáltatás, amely biztonságos tárolót biztosít a kulcsok, [titkos](../secrets/index.yml) [kódok](../keys/index.yml)és [tanúsítványok](../certificates/index.yml)számára. További információ a Key Vaultről: [About Azure Key Vault](overview.md); a Key vaultban tárolt adatokkal kapcsolatos további információkért lásd: [a kulcsok, a titkok és a tanúsítványok ismertetése](about-keys-secrets-certificates.md).

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ebben a rövid útmutatóban egy kulcstartót hoz létre az [Azure CLI](/cli/azure/)-vel. Az Azure CLI-vel parancsok vagy parancsfájlok használatával hozhat létre és kezelhet Azure-erőforrásokat.  Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

Ha a parancssori felülettel szeretne bejelentkezni az Azure-ba, írja be a következőt:

```azurecli
az login
```

Ha további információt szeretne a bejelentkezési lehetőségekről a CLI-n keresztül, tekintse meg az [Azure CLI-vel való bejelentkezést](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) ismertető részt.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehoz egy *ContosoResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Hozzon létre egy Key Vault az erőforráscsoporthoz az előző lépésben. A következő információkat kell megadnia:

- Key Vault neve: 3 – 24 karakterből álló karakterlánc, amely csak számokat (0-9), betűket (a-z, A-z) és kötőjeleket (-) tartalmazhat.

  > [!Important]
  > Minden kulcstartónak egyedi névvel kell rendelkeznie. A következő példákban cserélje le a <az egyedi-kulcstartó-Name> a Key Vault nevét.

- Erőforráscsoport neve: **myResourceGroup**.
- A hely: **EastUS**.

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

Ezzel a parancsmaggal megjelenítheti az újonnan létrehozott kulcstartó tulajdonságait. Jegyezze fel az alábbi két tulajdonságot:

- Tár **neve**: a fenti--Name paraméterhez megadott név.
- Tároló **URI-ja**: a példában ez a https:// &lt; az egyedi-kulcstartó-neve &gt; . Vault.Azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Az Azure-fiókja jelenleg az egyetlen, amelyik jogosult arra, hogy műveleteket végezzen ezen az új tárolón.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.

Ha már nincs rá szükség, az Azure CLI az [Group delete](/cli/azure/group) paranccsal eltávolíthatja az erőforráscsoportot és az összes kapcsolódó erőforrást:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, és törölte. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](overview.md)
- Tekintse meg az Azure CLI-re vonatkozó hivatkozást az kulcstartó [parancsainál](/cli/azure/keyvault?view=azure-cli-latest)
- [Azure Key Vault ajánlott eljárások](best-practices.md) áttekintése
