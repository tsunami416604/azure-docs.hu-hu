---
title: 'Gyors útmutató: Azure Key Vault tanúsítványok & nézetének beállítása – Azure CLI'
description: Gyors útmutató, amely bemutatja, hogyan lehet tanúsítványokat beállítani és lekérni Azure Key Vault az Azure CLI használatával
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 55a25f4f40668172aa8a9c80dd08d9fafcef78af
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516476"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-cli"></a>Gyors útmutató: tanúsítvány beállítása és lekérése Azure Key Vault az Azure CLI használatával

Ebben a rövid útmutatóban egy kulcstartót hoz létre Azure Key Vault az Azure CLI-vel. Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. További információ a Key Vaultről: [Áttekintés](../general/overview.md). Az Azure CLI az Azure-erőforrások létrehozására és kezelésére szolgál, parancsok és szkriptek alkalmazásával. Ha ezt elvégezte, egy tanúsítványt fog tárolni.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ehhez a rövid útmutatóhoz az Azure CLI 2.0.4 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

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

- **Tároló neve** : A példában ez a **Contoso-Vault2**. Ezt a nevet fogja más Key Vault-parancsokban is megadni.
- **Tár URI-ja** : A példában ez a https://contoso-vault2.vault.azure.net/. A tárolót a REST API-ján keresztül használó alkalmazásoknak ezt az URI-t kell használniuk.

Az Azure-fiókja jelenleg az egyetlen, amelyik jogosult arra, hogy műveleteket végezzen ezen az új tárolón.

## <a name="add-a-certificate-to-key-vault"></a>Tanúsítvány hozzáadása a Key Vaulthoz

Ha tanúsítványt szeretne hozzáadni a tárolóhoz, mindössze néhány további lépést kell elvégeznie. Ezt a tanúsítványt egy alkalmazás használhatja. 

Írja be az alábbi parancsokat, és hozzon létre egy önaláírt tanúsítványt a **ExampleCertificate** nevű alapértelmezett házirenddel:

```azurecli
az keyvault certificate create --vault-name "Contoso-Vault2" -n ExampleCertificate -p "$(az keyvault certificate get-default-policy)"
```

Most már hivatkozhat erre a tanúsítványra, amelyet a Azure Key Vaulthoz adott hozzá az URI használatával. A **jelenlegi https://Contoso-Vault2.vault.azure.net/certificates/ExampleCertificate** verziójának lekéréséhez használja a következőt:. 

A korábban tárolt tanúsítvány megtekintése:

```azurecli

az keyvault certificate show --name "ExampleCertificate" --vault-name "Contoso-Vault2"
```

Most létrehozott egy Key Vault, tárolt egy tanúsítványt, és beolvasta azt.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, az [az group delete](/cli/azure/group) paranccsal eltávolíthatja az erőforráscsoportot és az összes kapcsolódó erőforrást. Az erőforrásokat a következőképpen törölheti:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, és tárolt benne egy tanúsítványt. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](../general/overview.md)
- Tekintse meg az Azure CLI-re vonatkozó hivatkozást az kulcstartó [parancsainál](/cli/azure/keyvault?view=azure-cli-latest)
- [Azure Key Vault ajánlott eljárások](../general/best-practices.md) áttekintése
