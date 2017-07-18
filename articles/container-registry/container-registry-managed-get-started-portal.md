---
title: "Privát Docker-beállításjegyzék létrehozása – Azure Portal | Microsoft Docs"
description: "Bevezetés a privát Docker-beállításjegyzékek létrehozásába és kezelésébe az Azure Portalon"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: 560aee42b0c5a61c37c594d7937f833ab7183d49
ms.contentlocale: hu-hu
ms.lasthandoff: 07/12/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-portal"></a>Felügyelt tárolóregisztrációs adatbázis létrehozása az Azure Portallal

Az Azure Container Registry egy felügyelt Docker-tárolóregisztrációs adatbázis-szolgáltatás, amely a privát Docker-tárolók rendszerképeinek tárolására szolgál. Ez az útmutató a felügyelt Azure Container Registry-példányok Azure Portallal való létrehozását ismerteti.

A felügyelt Azure-tárolóregisztrációs adatbázisok előzetes verziós kiadásban, és nem az összes régióban érhetők el.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a http://portal.azure.com webhelyen.

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.

2. Keresse meg a piactéren az **Azure Container Registry** elemet, és válassza ki azt.

3. Kattintson a **Létrehozás** parancsra, hogy megnyissa az ACR létrehozása panelt.

    ![A tároló-beállításjegyzékek beállításai](./media/container-registry-get-started-portal/managed-container-registry-settings.png)

4. Az **Azure Container Registry** panelen adja meg a következő információkat. Amikor elkészült, kattintson a **Létrehozás** gombra.

    a. **Beállításjegyzék neve**: egy globálisan egyedi legfelső szintű tartománynév az adott beállításjegyzékhez. Ebben a példában a beállításjegyzék neve *myAzureContainerRegistry1*, de behelyettesítheti egy saját, egyedi névvel. A név csak betűket és számokat tartalmazhat.

    b. **Erőforráscsoport**: Válasszon egy meglévő [erőforráscsoportot](../azure-resource-manager/resource-group-overview.md#resource-groups), vagy adja meg egy új csoport nevét.

    c. **Hely**: Válasszon ki egy Azure-adatközpontot, ahol a szolgáltatás [elérhető](https://azure.microsoft.com/regions/services/), például az **USA déli középső régióját**.

    d. **Rendszergazdai felhasználó**: Ha szeretné, engedélyezze egy rendszergazdai felhasználó számára a beállításjegyzék elérését. Ezt a beállítást a beállításjegyzék létrehozását követően módosíthatja.

    e. **Felügyelt beállításjegyzék használata**: Válassza az igen lehetőséget, hogy az ACR automatikusan felügyelje a beállításjegyzék tárterületét, webhookokat használjon és AAD-hitelesítést használjon.

    f. **Tarifacsomag**: Válasszon egy tarifacsomagot. További információért tekintse meg itt az ACR-díjszabást.

## <a name="log-in-to-acr-instance"></a>Bejelentkezés az ACR-példányba

A tárolórendszerképek mozgatásához először be kell jelentkeznie az ACR-példányba. 

Ehhez használja az Azure CLI 2.0-s verzióját. Először szükség esetén jelentkezzen be az Azure-ba az [az login](/cli/azure/#login) paranccsal. 

```azurecli
az login
```

Ezután az [az acr login](/cli/azure/acr#login) paranccsal jelentkezzen be az Azure Container Registrybe.

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

## <a name="use-azure-container-registry"></a>Az Azure Container Registry használata

### <a name="list-container-images"></a>Tárolórendszerképek listázása

Az `az acr` parancssori felületi parancsokkal lekérdezheti az adattárakban tárolt képeket és címkéket.

> [!NOTE]
> A Container Registry jelenleg nem támogatja a `docker search` parancsot a képek és címkék lekérdezéséhez.

### <a name="list-repositories"></a>Adattárak listázása

A következő példa egy beállításjegyzék adattárait listázza JSON (JavaScript Object Notation) formátumban:

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### <a name="list-tags"></a>Címkék listázása

A következő példa a **samples/nginx** adattárban lévő címkéket listázza JSON formátumban:

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy felügyelt Azure Container Registry-példányt hozott létre az Azure Portallal.

> [!div class="nextstepaction"]
> [Az első rendszerkép leküldése a Docker parancssori felületével](container-registry-get-started-docker-cli.md)
