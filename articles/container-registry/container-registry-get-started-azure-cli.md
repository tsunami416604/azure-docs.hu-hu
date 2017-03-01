---
title: "Azure-tároló beállításjegyzékének létrehozása – CLI | Microsoft Docs"
description: "Bevezetés az Azure Container Registryk létrehozásába és kezelésébe az Azure CLI 2.0 segítségével"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: stevelas
translationtype: Human Translation
ms.sourcegitcommit: 2a381431acb6436ddd8e13c69b05423a33cd4fa6
ms.openlocfilehash: 1d5e16952cbc56a381ead23843515cf6ed1d74a9

---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Tároló-beállításjegyzék létrehozása az Azure CLI-vel
Az [Azure CLI 2.0](https://github.com/Azure/azure-cli) parancsaival létrehozhat egy tároló-beállításjegyzéket, és kezelheti annak beállításait Linux, Mac vagy Windows rendszerű számítógépéről. A tároló-beállításjegyzékeket létrehozhatja és kezelheti az [Azure Portalon](container-registry-get-started-portal.md) vagy programozott módon a tároló-beállításjegyzék [REST API-jával](https://go.microsoft.com/fwlink/p/?linkid=834376) is.


* Háttérinformációkért és a fogalmakkal kapcsolatban lásd: [Mi az az Azure Container Registry?](container-registry-intro.md)
* A Container Registry parancssori felületének parancsaival (`az acr` parancsok) kapcsolatos segítségért adja át a `-h` paramétert egy parancsnak.

> [!NOTE]
> A Container Registry jelenleg előzetes verzióban érhető el.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
* **Azure CLI 2.0** – A CLI 2.0 telepítéshez és megismeréséhez tekintse meg a [telepítési utasításokat](https://github.com/Azure/azure-cli/blob/master/README.rst). Jelentkezzen be Azure-előfizetésébe az `az login` futtatásával.
* **Erőforráscsoport** – A tároló-beállításjegyzék létrehozása előtt hozzon létre egy [erőforráscsoportot](../azure-resource-manager/resource-group-overview.md#resource-groups), vagy használjon egy meglévő erőforráscsoportot. Győződjön meg arról, hogy az erőforráscsoport olyan helyen található, ahol a Container Registry szolgáltatás [elérhető](https://azure.microsoft.com/regions/services/). Ha a CLI 2.0-val szeretne erőforráscsoportot létrehozni, tekintse meg [a CLI 2.0 mintáit](https://github.com/Azure/azure-cli-samples/tree/master/arm). 
* **Storage-fiók** (nem kötelező) – Hozzon létre egy standard Azure [Storage-fiókot](../storage/storage-introduction.md) a tároló-beállításjegyzékhez azzal megegyező helyen. Ha nem ad meg Storage-fiókot, amikor létrehozza a beállításjegyzéket az `az acr create` paranccsal, a parancs létrehoz egyet. Ha a CLI 2.0-val szeretne tárfiókot létrehozni, tekintse meg [a CLI 2.0 mintáit](https://github.com/Azure/azure-cli-samples/tree/master/storage).
* **Egyszerű szolgáltatás** (nem kötelező) – Ha a parancssori felülettel hoz létre beállításjegyzéket, az alapértelmezés szerint nem lesz elérhető. Igény szerint a beállításjegyzékhez hozzárendelhet egy meglévő Azure Active Directory egyszerű szolgáltatást (vagy létrehozhat és hozzárendelhet egy újat), vagy engedélyezheti a beállításjegyzék rendszergazdai felhasználói fiókját. Ezzel kapcsolatban lásd a cikk későbbi részeit. A beállításjegyzék elérésével kapcsolatos további információkat a [tároló-beállításjegyzékkel való hitelesítéssel kapcsolatos cikkben](container-registry-authentication.md) találhat. 

## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása
Futtassa az `az acr create` parancsot egy tároló-beállításjegyzék létrehozásához. 

> [!TIP]
> A beállításjegyzék létrehozásakor adjon meg egy globálisan egyedi legfelső szintű tartománynevet, amely csak betűket és számokat tartalmaz. A beállításjegyzék neve a példákban `myRegistry`, de helyettesítsen be egy saját, egyedi nevet. 
> 
> 

Az alábbi parancs a minimális paramétereket használja a `myRegistry` tároló beállításjegyzék a `myResourceGroup` erőforráscsoportban való létrehozásához az USA déli középső régiójában:

```azurecli
az acr create -n myRegistry -g myResourceGroup -l southcentralus
```

* A `--storage-account-name` és az `-s` nem kötelező. Ha nincs megadva, a Storage-fiók véletlenszerű névvel jön létre a megadott erőforráscsoportban.

A kimenet a következő példához hasonló:

![az acr create kimenete](./media/container-registry-get-started-azure-cli/acr_create.png)


Különösen ügyeljen a következőre:

* `id` – Az előfizetésben lévő beállításjegyzék azonosítója, amelyre akkor van szükség, ha egyszerű szolgáltatást szeretne hozzárendelni. 
* `loginServer` – A teljes név, amelyet a [beállításjegyzékbe való bejelentkezéshez](container-registry-authentication.md) ad meg. Ebben a példában a név `myregistry-contoso.exp.azurecr.io` (csak kisbetűkkel).

## <a name="assign-a-service-principal"></a>Egyszerű szolgáltatás hozzárendelése
Azure Active Directory egyszerű szolgáltatások beállításjegyzékhez való hozzárendeléséhez a CLI 2.0 parancsait használhatja. A példákban szereplő egyszerű szolgáltatáshoz a tulajdonosi szerepkör van hozzárendelve, de [más szerepköröket](../active-directory/role-based-access-control-configure.md) is hozzárendelhet, ha szeretne.

### <a name="create-a-service-principal-and-assign-access-to-the-registry"></a>Egyszerű szolgáltatás létrehozása és hozzáférés biztosítása a beállításjegyzékhez
Az alábbi parancs tulajdonosi szerepkör szintű hozzáférést biztosít az új egyszerű szolgáltatás számára a `--scopes` paraméterrel átadott beállításjegyzék-azonosítóhoz. Adjon meg egy erős jelszót a `--password` paraméterrel.

```azurecli
az ad sp create-for-rbac --scopes /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry --role Owner --password myPassword
```



### <a name="assign-an-existing-service-principal"></a>Meglévő egyszerű szolgáltatás hozzárendelése
Ha már rendelkezik egyszerű szolgáltatással, és tulajdonosi szerepkör szintű hozzáférést szeretne számára biztosítani a beállításjegyzékhez, futtasson egy, a következő példához hasonló parancsot. Az egyszerű szolgáltatás alkalmazásazonosítóját az `--assignee` paraméterrel lehet átadni:

```azurecli
az role assignment create --scope /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry --role Owner --assignee myAppId
```



## <a name="manage-admin-credentials"></a>Rendszergazdai hitelesítő adatok kezelése
Minden tároló-beállításjegyzékhez automatikusan létrejön egy rendszergazdai fiók, amely alapértelmezés szerint le van tiltva. Az alábbi példák `az acr` parancssori felületi parancsokat mutatnak be a tároló-beállításjegyzék rendszergazdai hitelesítő adatainak kezeléséhez.

### <a name="obtain-admin-user-credentials"></a>Rendszergazdai hitelesítő adatok beszerzése
```azurecli
az acr credential show -n myRegistry
```

### <a name="enable-admin-user-for-an-existing-registry"></a>Rendszergazdai felhasználó engedélyezése meglévő beállításjegyzékhez
```azurecli
az acr update -n myRegistry --admin-enabled true
```

### <a name="disable-admin-user-for-an-existing-registry"></a>Rendszergazdai felhasználó letiltása meglévő beállításjegyzékhez
```azurecli
az acr update -n myRegistry --admin-enabled false
```

## <a name="list-images-and-tags"></a>Képek és címkék listázása
Az `az acr` parancssori felületi parancsokkal lekérdezheti az adattárakban tárolt képeket és címkéket. 

> [!NOTE]
> A Container Registry jelenleg nem támogatja a `docker search` parancsot a képek és címkék lekérdezéséhez.


### <a name="list-repositories"></a>Adattárak listázása
A következő példa egy beállításjegyzék adattárait listázza JSON (JavaScript Object Notation) formátumban:

```azurecli
az acr repository list -n myRegistry -o json
```

### <a name="list-tags"></a>Címkék listázása
A következő példa a **samples/nginx** adattárban lévő címkéket listázza JSON formátumban:

```azurecli
az acr repository show-tags -n myRegistry --repository samples/nginx -o json
```

## <a name="next-steps"></a>Következő lépések
* [Az első rendszerkép leküldése a Docker parancssori felületével](container-registry-get-started-docker-cli.md)




<!--HONumber=Feb17_HO4-->


