---
title: "Privát Docker-tárolójegyzék létrehozása – Azure CLI | Microsoft Docs"
description: "Bevezetés a privát Docker-tárolójegyzékek létrehozásába és kezelésébe az Azure CLI 2.0 segítségével"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: cristyg
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 2875f4089231ed12a0312b2c2e077938440365c6
ms.contentlocale: hu-hu
ms.lasthandoff: 08/21/2017

---
# <a name="create-a-private-docker-container-registry-using-the-azure-cli-20"></a>Privát Docker-tárolójegyzék létrehozása az Azure CLI 2.0 használatával
Az [Azure CLI 2.0](https://github.com/Azure/azure-cli) parancsaival létrehozhat egy tároló-beállításjegyzéket, és kezelheti annak beállításait Linux, Mac vagy Windows rendszerű számítógépéről. A tároló-beállításjegyzékeket létrehozhatja és kezelheti az [Azure Portalon](container-registry-get-started-portal.md) vagy programozott módon a tároló-beállításjegyzék [REST API-jával](https://go.microsoft.com/fwlink/p/?linkid=834376) is.


* Háttér-információkért és a fogalmakkal kapcsolatban lásd [az áttekintést](container-registry-intro.md).
* A Container Registry parancssori felületének parancsaival (`az acr` parancsok) kapcsolatos segítségért adja át a `-h` paramétert egy parancsnak.


## <a name="prerequisites"></a>Előfeltételek
* **Azure CLI 2.0**: A CLI 2.0 telepítéshez és megismeréséhez tekintse meg a [telepítési utasításokat](/cli/azure/install-azure-cli). Jelentkezzen be Azure-előfizetésébe az `az login` futtatásával. További információkért lásd a [CLI 2.0 használatának első lépéseit](/cli/azure/get-started-with-azure-cli) ismertető témakört.
* **Erőforráscsoport**: A tárolójegyzék létrehozása előtt hozzon létre egy [erőforráscsoportot](../azure-resource-manager/resource-group-overview.md#resource-groups), vagy használjon egy meglévő erőforráscsoportot. Győződjön meg arról, hogy az erőforráscsoport olyan helyen található, ahol a Container Registry szolgáltatás [elérhető](https://azure.microsoft.com/regions/services/). Ha a CLI 2.0-val szeretne erőforráscsoportot létrehozni, tekintse meg [a CLI 2.0-referenciát](/cli/azure/group).
* **Storage-fiók** (nem kötelező): Hozzon létre egy standard Azure [Storage-fiókot](../storage/common/storage-introduction.md) a tárolójegyzékhez a tárolójegyzékkel megegyező helyen. Ha nem ad meg Storage-fiókot, amikor létrehozza a beállításjegyzéket az `az acr create` paranccsal, a parancs létrehoz egyet. Ha a CLI 2.0-val szeretne tárfiókot létrehozni, tekintse meg [a CLI 2.0-referenciát](/cli/azure/storage/account). A Premium Storage jelenleg nem támogatott.
* **Egyszerű szolgáltatás** (nem kötelező): Ha a parancssori felülettel hoz létre beállításjegyzéket, az alapértelmezés szerint nem lesz elérhető. Igény szerint a beállításjegyzékhez hozzárendelhet egy meglévő Azure Active Directory egyszerű szolgáltatást (vagy létrehozhat és hozzárendelhet egy újat), vagy engedélyezheti a beállításjegyzék rendszergazdai felhasználói fiókját. Ezzel kapcsolatban lásd a cikk későbbi részeit. A beállításjegyzék elérésével kapcsolatos további információkat a [tároló-beállításjegyzékkel való hitelesítéssel kapcsolatos cikkben](container-registry-authentication.md) találhat.

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása
Futtassa az `az acr create` parancsot egy tároló-beállításjegyzék létrehozásához.

> [!TIP]
> A beállításjegyzék létrehozásakor adjon meg egy globálisan egyedi legfelső szintű tartománynevet, amely csak betűket és számokat tartalmaz. A beállításjegyzék neve a példákban `myRegistry1`, de helyettesítsen be egy saját, egyedi nevet.
>
>

Az alábbi parancs az *alapszintű* termékváltozattal a minimális paramétereket használja a tárolóregisztrációs adatbázis (`myRegistry1`) `myResourceGroup` erőforráscsoportban való létrehozásához:

```azurecli
az acr create --name myRegistry1 --resource-group myResourceGroup --sku Basic
```

* A(z) `--storage-account-name` nem kötelező. Ha nincs megadva, a Storage-fiók a beállításjegyzék nevéből és egy időbélyegzőből álló névvel jön létre a megadott erőforráscsoportban.

A tárolóregisztrációs adatbázis létrehozásakor a kimenet a következő példához hasonló:

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T18:36:29.124842+00:00",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry
/registries/myRegistry1",
  "location": "southcentralus",
  "loginServer": "myregistry1.azurecr.io",
  "name": "myRegistry1",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "myregistry123456789"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}

```


Különösen ügyeljen a következőre:

* `id` – Az előfizetésben lévő beállításjegyzék azonosítója, amelyre akkor van szükség, ha egyszerű szolgáltatást szeretne hozzárendelni.
* `loginServer` – A teljes név, amelyet a [beállításjegyzékbe való bejelentkezéshez](container-registry-authentication.md) ad meg. Ebben a példában a név `myregistry1.exp.azurecr.io` (csak kisbetűkkel).

## <a name="assign-a-service-principal"></a>Egyszerű szolgáltatás hozzárendelése
Azure Active Directory egyszerű szolgáltatások beállításjegyzékhez való hozzárendeléséhez a CLI 2.0 parancsait használhatja. A példákban szereplő egyszerű szolgáltatáshoz a tulajdonosi szerepkör van hozzárendelve, de [más szerepköröket](../active-directory/role-based-access-control-configure.md) is hozzárendelhet, ha szeretne.

### <a name="create-a-service-principal-and-assign-access-to-the-registry"></a>Egyszerű szolgáltatás létrehozása és hozzáférés biztosítása a beállításjegyzékhez
Az alábbi parancs tulajdonosi szerepkör szintű hozzáférést biztosít az új egyszerű szolgáltatás számára a `--scopes` paraméterrel átadott beállításjegyzék-azonosítóhoz. Adjon meg egy erős jelszót a `--password` paraméterrel.

```azurecli
az ad sp create-for-rbac --scopes /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --password myPassword
```



### <a name="assign-an-existing-service-principal"></a>Meglévő egyszerű szolgáltatás hozzárendelése
Ha már rendelkezik egyszerű szolgáltatással, és tulajdonosi szerepkör szintű hozzáférést szeretne számára biztosítani a beállításjegyzékhez, futtasson egy, a következő példához hasonló parancsot. Az egyszerű szolgáltatás alkalmazásazonosítóját az `--assignee` paraméterrel lehet átadni:

```azurecli
az role assignment create --scope /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry1 --role Owner --assignee myAppId
```



## <a name="manage-admin-credentials"></a>Rendszergazdai hitelesítő adatok kezelése
Minden tároló-beállításjegyzékhez automatikusan létrejön egy rendszergazdai fiók, amely alapértelmezés szerint le van tiltva. Az alábbi példák `az acr` parancssori felületi parancsokat mutatnak be a tároló-beállításjegyzék rendszergazdai hitelesítő adatainak kezeléséhez.

### <a name="obtain-admin-user-credentials"></a>Rendszergazdai hitelesítő adatok beszerzése
```azurecli
az acr credential show -n myRegistry1
```

### <a name="enable-admin-user-for-an-existing-registry"></a>Rendszergazdai felhasználó engedélyezése meglévő beállításjegyzékhez
```azurecli
az acr update -n myRegistry1 --admin-enabled true
```

### <a name="disable-admin-user-for-an-existing-registry"></a>Rendszergazdai felhasználó letiltása meglévő beállításjegyzékhez
```azurecli
az acr update -n myRegistry1 --admin-enabled false
```

## <a name="list-images-and-tags"></a>Képek és címkék listázása
Az `az acr` parancssori felületi parancsokkal lekérdezheti az adattárakban tárolt képeket és címkéket.

> [!NOTE]
> A Container Registry jelenleg nem támogatja a `docker search` parancsot a képek és címkék lekérdezéséhez.


### <a name="list-repositories"></a>Adattárak listázása
A következő példa egy beállításjegyzék adattárait listázza JSON (JavaScript Object Notation) formátumban:

```azurecli
az acr repository list -n myRegistry1 -o json
```

### <a name="list-tags"></a>Címkék listázása
A következő példa a **samples/nginx** adattárban lévő címkéket listázza JSON formátumban:

```azurecli
az acr repository show-tags -n myRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>Következő lépések
* [Az első rendszerkép leküldése a Docker parancssori felületével](container-registry-get-started-docker-cli.md)

