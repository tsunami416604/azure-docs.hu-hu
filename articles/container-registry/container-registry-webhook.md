---
title: Az Azure Container Registry webhookok
description: Ismerje meg, a beállításjegyzék-tárházak esetében bizonyos műveleteket bekövetkezésekor kiváltó események webhookok használatával.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/14/2019
ms.author: danlep
ms.openlocfilehash: 0a3d2d0e858dc052095c0a58287970d10c06f0ba
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099848"
---
# <a name="using-azure-container-registry-webhooks"></a>Azure Container Registry webhookok használata

Egy Azure container registry tárolja és kezeli a privát Docker-tárolók lemezképeit, Docker Hub nyilvános Docker-rendszerképeket tárol hasonló. Azt is üzemeltethetnek tárolóhelyekkel [Helm-diagramok](container-registry-helm-repos.md) (előzetes verzió) üzembe helyezéséhez a Kubernetes-alkalmazások csomagolási formázása. Amikor az egyes műveletekre kerül sor egy, a beállításjegyzék-tárházak kiváltó események webhookok is használhatja. Webhookok reagálhat az eseményekre a beállításjegyzék szintjén, vagy azok leszűkítheti egy adott adattárra címkét.

További információ a webhook-kérelem: [Azure Container Registry webhookok sémaleírás](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Előfeltételek

* Az Azure container registry - tároló-beállításjegyzék létrehozása az Azure-előfizetésében. Például a [az Azure portal](container-registry-get-started-portal.md) vagy a [Azure CLI-vel](container-registry-get-started-azure-cli.md). A [Azure Container Registry Termékváltozatai](container-registry-skus.md) különböző webhookok kvóták.
* Docker CLI - helyi számítógépét Docker-gazdagépként beállítani és elérni a Docker CLI-parancsok telepítése [Docker-motor](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Webhook – Azure portal létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Lépjen a tárolóregisztrációs adatbázisba, amelyben meg szeretné hozzon létre egy webhookot.
1. A **szolgáltatások**válassza **Webhookok**.
1. Válassza ki **Hozzáadás** a webhook eszköztáron.
1. Végezze el a *webhook létrehozása* űrlapján az alábbi adatokat:

| Érték | Leírás |
|---|---|
| Name (Név) | Kíván adni a webhook nevét. Csak betűket és számokat tartalmazhat, és 5 – 50 karakter hosszúságúnak kell lennie. |
| Szolgáltatás URI-ja | Az URI-t, a webhook POST értesítéseket küldjön-e. |
| Egyéni fejlécek | A POST-kérés továbbítása a kívánt fejléceket. Lehetnek a "kulcs: érték" formátumban. |
| Műveletek indítása | A webhook kiváltó műveletek. Műveletek közé tartozik kép leküldéses, lemezkép törlése, Helm-diagram leküldéses, Helm-diagram törlése, és képet karanténba. Választhat egy vagy több műveletet a webhook aktiválásához. |
| status | A webhook létrehozása után az állapotát. Ez alapértelmezés szerint engedélyezve van. |
| Hatókör | A hatókör, amelyen a webhook működik. Nincs megadva, a hatókör-e az összes esemény a beállításjegyzékben. Azt adható meg a tárház vagy egy címkét a következő formátumban: "tárházat: címke" vagy "tárház: *" az összes olyan címkével. |

Példa webhook-űrlapon:

![Az ACR webhook létrehozása felhasználói felület az Azure Portalon](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Hozzon létre a webhook – Azure CLI-vel

Hozzon létre egy webhookot, az Azure CLI-vel, használja a [az acr webhook létrehozása](/cli/azure/acr/webhook#az-acr-webhook-create) parancsot. A következő parancs létrehoz egy webhookot, az összes rendszerkép események törlése a beállításjegyzékben *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Webhook tesztelése

### <a name="azure-portal"></a>Azure Portal

A webhook használata előtt tesztelheti azt a **Ping** gombra. Ping egy általános POST kérést küld a megadott végponton, és a válasz naplózza. A ping szolgáltatás használatával segítségével ellenőrizze, hogy megfelelően állította be a webhook.

1. Válassza ki a vizsgálni kívánt webhook.
2. A felső eszköztáron válassza **Ping**.
3. A végpont válasz látogasson el a **HTTP-állapot** oszlop.

![Az ACR webhook létrehozása felhasználói felület az Azure Portalon](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI-vel egy ACR webhook teszteléséhez használja a [az acr webhook pingelése](/cli/azure/acr/webhook#az-acr-webhook-ping) parancsot.

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Az eredmények megtekintéséhez használja a [az acr webhook-lista-eseményei](/cli/azure/acr/webhook) parancsot.

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Webhook törlése

### <a name="azure-portal"></a>Azure Portal

Egyes webhookok kiválasztásával a webhook törölhetők, majd a **törlése** gomb az Azure Portalon.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>További lépések

### <a name="webhook-schema-reference"></a>Webhook-adatbázisséma hivatkozása

További információ a formátum és a JSON esemény is észleltünk adattartalmakat. az Azure Container Registry által kibocsátott tulajdonságait tekintse meg a webhook-adatbázisséma hivatkozása:

[Az Azure Container Registry webhookok adatbázisséma hivatkozása](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Event Grid-események

A natív beállításjegyzék webhookesemények ebben a cikkben tárgyalt, mellett az Azure Container Registry kibocsátható az eseményeket az Event Grid:

[Rövid útmutató: Container registry események küldése az Event Grid](container-registry-event-grid-quickstart.md)
