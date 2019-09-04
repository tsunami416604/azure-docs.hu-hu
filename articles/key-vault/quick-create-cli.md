---
title: Rövid Azure-útmutató – Titkos kulcs beállítása és lekérése a Key Vaultból az Azure CLI használatával | Microsoft Docs
description: Rövid útmutató, amely bemutatja a titkos kulcsok beállítását és lekérését az Azure Key Vaultból az Azure CLI használatával
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 37fc4003ba19e9e8f3d156a7fcb9bb84001d2e90
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259317"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Gyors útmutató: Azure Key Vault titkos kód beállítása és beolvasása az Azure CLI használatával

Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. A Key Vaulttal kapcsolatos további információt az [Áttekintés](key-vault-overview.md) szakaszban talál. Az Azure CLI az Azure-erőforrások létrehozására és kezelésére szolgál, parancsok és szkriptek alkalmazásával. Ebben a rövid útmutatóban egy kulcstartót hoz létre. Miután végzett ezzel, titkos kulcsot fog tárolni.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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
- Erőforráscsoport neve: **ContosoResourceGroup**.
- Hely: **USA keleti régiója**.

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

Ezzel a parancsmaggal megjelenítheti az újonnan létrehozott Key Vault tulajdonságait. Jegyezze fel az alábbi két tulajdonságot:

- Tár **neve**: A példában ez a **contoso-Vault2**. Ezt a nevet fogja más Key Vault-parancsokban is megadni.
- Tároló **URI-ja**: A példában ez a következő https://contoso-vault2.vault.azure.net/:. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Az Azure-fiókja jelenleg az egyetlen, amelyik jogosult arra, hogy műveleteket végezzen ezen az új tárolón.

## <a name="add-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz

Titkos kód a tárolóhoz való hozzáadásához csak néhány további lépést kell végrehajtania. Ezt a jelszót egy alkalmazás használhatja. A jelszó neve **ExamplePassword** lesz, és a **hVFkk965BuUv** értékét fogja tárolni.

Írja be az alábbi parancsokat egy titkos kód létrehozásához Key Vault nevű **ExamplePassword** , amely a **hVFkk965BuUv** értéket fogja tárolni:

```azurecli
az keyvault secret set --vault-name "Contoso-Vault2" --name "ExamplePassword" --value "hVFkk965BuUv"
```

Az Azure Key Vaulthoz hozzáadott jelszóra ezután az URI használatával hivatkozhat. A jelenlegi verzió beszerzéséhez használja a következőt: **https://ContosoVault.vault.azure.net/secrets/ExamplePassword** . 

A titkos kódban tárolt érték megtekintése egyszerű szövegként:

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "Contoso-Vault2"
```

Most létrehozott egy Key Vaultot, tárolt egy titkos kulcsot, és lekérte azt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, az [az group delete](/cli/azure/group) paranccsal eltávolíthatja az erőforráscsoportot és az összes kapcsolódó erőforrást. Az erőforrásokat a következőképpen törölheti:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, és egy titkos kulcsot tárolt benne. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](key-vault-overview.md)
- Tekintse meg az Azure CLI-re vonatkozó hivatkozást az kulcstartó [parancsainál](/cli/azure/keyvault?view=azure-cli-latest)
- Tudnivalók a [kulcsokról, a titkokról és a tanúsítványokról](about-keys-secrets-and-certificates.md)
- [Azure Key Vault ajánlott eljárások](key-vault-best-practices.md) áttekintése
