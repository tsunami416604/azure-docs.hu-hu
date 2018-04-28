---
title: Az Azure tároló beállításjegyzék webhookok
description: Útmutató webhookok eseményindító eseményekre, ha bizonyos műveleteket a beállításjegyzék adattárak lép fel.
services: container-registry
author: neilpeterson
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 12/02/2017
ms.author: nepeters
ms.openlocfilehash: cf71deda9ec9053a91596c17e18fc797995754e2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="using-azure-container-registry-webhooks"></a>Azure-tároló beállításjegyzék webhookok használatával

Egy Azure-tárolót beállításjegyzék tárolja, és kezeli a saját Docker tároló képek, hasonló ahhoz, ahogy a Docker Hub nyilvános Docker-lemezképet tárolja. Bizonyos műveleteket kerül sor egy, a beállításjegyzék-adattárak eseményindító események webhookok is használhatja. Webhook válaszolhassanak a beállításjegyzék szinten események, vagy azok le egy adott tárház címke hatóköre beállítható úgy.

A webhook kérelmek részletekért lásd: [Azure tároló beállításjegyzék webhook sémareferenciája](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-tárolót beállításjegyzék - tároló beállításjegyzékbeli létrehozása az Azure-előfizetése. Tegyük fel például, a [Azure-portálon](container-registry-get-started-portal.md) vagy a [Azure CLI](container-registry-get-started-azure-cli.md).
* A docker parancssori felület – Docker-gazdagépként a helyi számítógépet, és a Docker parancssori felület parancsait eléréséhez telepítse [Docker-motorhoz](https://docs.docker.com/engine/installation/).

## <a name="create-webhook-azure-portal"></a>Azure-portálon webhook létrehozása

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com)
1. Nyissa meg a tároló beállításjegyzék kívánja a webhook létrehozása.
1. A **szolgáltatások**, jelölje be **Webhookok**.
1. Válassza ki **Hozzáadás** webhook eszköztárán.
1. Fejezze be a *webhook létrehozása* képernyőn a következő információkat:

| Érték | Leírás |
|---|---|
| Name (Név) | Kíván beállítani a webhook nevét. Csak kisbetűket és számokat tartalmazhat, és 5-50 karakter hosszúságúnak kell lennie. |
| Szolgáltatás URI-ja | Az URI, ahol a webhook POST értesítést küldjön-e. |
| Egyéni fejlécek | A POST-kérelmet együtt átadni kívánt fejléceket. Össze kell a "kulcs: érték" formátumban. |
| Eseményindító műveletek | A webhook kiváltó műveletek. Webhook jelenleg forrása a kép leküldéses és/vagy törlési műveletek. |
| status | A webhook létrehozása után állapota. Alapértelmezés szerint engedélyezve van. |
| Hatókör | A hatókör, ahol a webhook működik. Alapértelmezés szerint a hatóköre az összes esemény a beállításjegyzékben. Azt is adható meg a tárház vagy egy címkét a "tárház: címke" formátumban. |

Példa webhook űrlap:

![ACR webhook létrehozása felhasználói felület az Azure-portálon](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook-azure-cli"></a>Az Azure parancssori felület webhook létrehozása

A webhook az Azure parancssori felület használatával hozhat létre a [az acr webhook létrehozása](/cli/azure/acr/webhook#az_acr_webhook_create) parancsot.

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Teszt webhook

### <a name="azure-portal"></a>Azure Portal

Való használata előtt a webhook tárolóra kép leküldéses és törlési műveletek, tesztelheti, és a **Ping** gombra. Ping egy általános POST kérést küld a megadott végpontot, és a válasz naplózza. A ping szolgáltatás használatával segítségével ellenőrizze, hogy megfelelően konfigurálta a webhook.

1. Válassza ki a vizsgálni kívánt webhook.
2. A felső eszköztárban látható, válassza ki a **Ping**.
3. Ellenőrizze, hogy a végpont válasz szerepel a **HTTP-állapot** oszlop.

![ACR webhook létrehozása felhasználói felület az Azure-portálon](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Az Azure parancssori felülettel ACR webhook teszteléséhez használja a [az acr webhook ping](/cli/azure/acr/webhook#az_acr_webhook_ping) parancsot.

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Az eredmények megtekintéséhez használja a [az acr webhook lista-események](/cli/azure/acr/webhook#list-events) parancsot.

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Webhook törlése

### <a name="azure-portal"></a>Azure Portal

Minden egyes webhook törölheti a webhook kiválasztásával, majd a **törlése** gomb az Azure portálon.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>További lépések

[Az Azure tároló beállításjegyzék webhook adatbázisséma hivatkozása](container-registry-webhook-reference.md)