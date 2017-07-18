---
title: "Privát Docker-tárolójegyzék létrehozása – Azure CLI | Microsoft Docs"
description: "Bevezetés a privát Docker-tárolójegyzékek létrehozásába és kezelésébe az Azure CLI 2.0 segítségével"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: c7cdb1b13bf32388d18c2a25af28337a81861c1e
ms.contentlocale: hu-hu
ms.lasthandoff: 07/12/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-cli"></a>Felügyelt tárolóregisztrációs adatbázis létrehozása az Azure CLI-vel

Az Azure Container Registry egy felügyelt Docker-tárolóregisztrációs adatbázis-szolgáltatás, amely a privát Docker-tárolók rendszerképeinek tárolására szolgál. Ez az útmutató a felügyelt Azure Container Registry-példányok Azure CLI-vel való létrehozását ismerteti.

A felügyelt Azure-tárolóregisztrációs adatbázisok előzetes verziós kiadásban, és nem az összes régióban érhetők el.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a gyorsútmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *westcentralus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Hozzon létre egy ACR-példányt az [az acr create](/cli/azure/acr#create) paranccsal.

> [!NOTE]
> A beállításjegyzék létrehozásakor adjon meg egy globálisan egyedi legfelső szintű tartománynevet, amely csak betűket és számokat tartalmaz.

 A beállításjegyzék neve a példában *myContainerRegistry1*, de behelyettesítheti egy saját, egyedi névvel.

```azurecli
az acr create --name myContainerRegistry1 --resource-group myResourceGroup --sku Managed_Standard
```

A tárolóregisztrációs adatbázis létrehozásakor a kimenet a következő példához hasonló:

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-29T04:50:28.607134+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry1",
  "location": "westcentralus",
  "loginServer": "mycontainerregistry1.azurecr.io",
  "name": "myContainerRegistry1",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Managed_Standard",
    "tier": "Managed"
  },
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="log-in-to-acr-instance"></a>Bejelentkezés az ACR-példányba

A tárolórendszerképek mozgatásához először be kell jelentkeznie az ACR-példányba. Ehhez használja az [az acr login](/cli/azure/acr#login) parancsot.

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

A parancs a „Bejelentkezés sikeres” üzenetet adja vissza, ha befejeződött.

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

Ebben a rövid útmutatóban egy felügyelt Azure Container Registry-példányt hozott létre az Azure CLI-vel.

> [!div class="nextstepaction"]
> [Az első rendszerkép leküldése a Docker parancssori felületével](container-registry-get-started-docker-cli.md)

