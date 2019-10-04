---
title: Webhookok Azure Container Registry
description: Megtudhatja, hogyan indíthat el eseményeket a webhookok használatával, amikor bizonyos műveletek történnek a beállításjegyzékbeli adattárakban.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 05/24/2019
ms.author: danlep
ms.openlocfilehash: 59e8d4979e7be02d6097e1c3eccc44e64da87e95
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311586"
---
# <a name="using-azure-container-registry-webhooks"></a>Azure Container Registry webhookok használata

Az Azure Container Registry tárolja és felügyeli a privát Docker-tárolók lemezképeit, hasonlóan ahhoz, ahogy a Docker hub nyilvános Docker-rendszerképeket tárol. Emellett a [Helm-diagramok](container-registry-helm-repos.md) (előzetes verzió) adattárházait is üzemeltetheti, amelyekkel alkalmazásokat telepíthet a Kubernetes. A webhookok használatával aktiválhatja az eseményeket, amikor bizonyos műveletek az egyik beállításjegyzékbeli tárházban lépnek életbe. A webhookok a beállításjegyzék szintjén válaszolnak az eseményekre, vagy hatókörük egy adott adattár címkéjére is felhasználhatók. Földrajzilag [replikált](container-registry-geo-replication.md) beállításjegyzék esetén az egyes webhookokat úgy konfigurálja, hogy válaszoljon az adott regionális replika eseményeire.

A webhook-kérelmekkel kapcsolatos részletekért lásd: [Azure Container Registry webhook-séma referenciája](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure Container Registry – hozzon létre egy tároló-beállításjegyzéket az Azure-előfizetésében. Használja például a [Azure Portal](container-registry-get-started-portal.md) vagy az [Azure CLI](container-registry-get-started-azure-cli.md)-t. Az [Azure Container Registry SKU](container-registry-skus.md) -ban különböző webhookok kvótái vannak.
* Docker CLI – a helyi számítógép Docker-gazdagépként való beállításához és a Docker CLI-parancsok eléréséhez, a Docker- [motor](https://docs.docker.com/engine/installation/)telepítéséhez.

## <a name="create-webhook---azure-portal"></a>Webhook létrehozása – Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Navigáljon ahhoz a tároló-beállításjegyzékhez, amelyben létre kíván hozni egy webhookot.
1. A **szolgáltatások**területen válassza a webhookok lehetőséget.
1. A webhook eszköztárban válassza a **Hozzáadás** lehetőséget.
1. Fejezze be a *webhook létrehozása* űrlapot a következő információkkal:

| Value | Leírás |
|---|---|
| Webhook neve | A webhookhoz adni kívánt név. Csak betűket és számokat tartalmazhat, és 5-50 karakter hosszúnak kell lennie. |
| Location | Földrajzilag [replikált](container-registry-geo-replication.md) beállításjegyzék esetén a beállításjegyzék-replika Azure-régióját kell megadnia. 
| Szolgáltatás URI-ja | Az az URI, amelyben a webhooknak POST-értesítéseket kell küldenie. |
| Egyéni fejlécek | A POST kérelemmel együtt átadni kívánt fejlécek. A "Key: Value" formátumúnak kell lenniük. |
| Trigger műveletek | A webhookot kiváltó műveletek. A műveletek közé tartozik a képek leküldése, a rendszerkép törlése, a Helm chart push, a Helm diagram törlése és a képek karanténba helyezése. Kiválaszthat egy vagy több műveletet a webhook elindításához. |
| Állapot | A webhook állapota a létrehozásuk után. Alapértelmezés szerint engedélyezve van. |
| Scope | A webhook működésének hatóköre. Ha nincs megadva, a hatókör a beállításjegyzékben található összes eseményre kiterjed. Megadható egy adattárhoz vagy egy címkéhez "adattár: tag" vagy "adattár: *" formátumban a tárházban található összes címkéhez. |

Példa webhook-űrlapra:

![ACR webhook-létrehozási felhasználói felület a Azure Portal](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Webhook létrehozása – Azure CLI

Webhook az Azure CLI használatával történő létrehozásához használja az az [ACR webhook Create](/cli/azure/acr/webhook#az-acr-webhook-create) parancsot. A következő parancs létrehoz egy webhookot az összes rendszerkép-törlési eseményhez a beállításjegyzék *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Webhook tesztelése

### <a name="azure-portal"></a>Azure Portal

A webhook használatának megkezdése előtt tesztelheti a **ping** gomb segítségével. A ping általános POST-kérést küld a megadott végpontnak, és naplózza a választ. A ping funkció használatával ellenőrizheti, hogy helyesen konfigurálta-e a webhookot.

1. Válassza ki a tesztelni kívánt webhookot.
2. A felső eszköztáron válassza a **pingelés**lehetőséget.
3. A **http-állapot** oszlopban keresse meg a végpont válaszát.

![ACR webhook-létrehozási felhasználói felület a Azure Portal](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Az ACR webhook Azure CLI-vel való teszteléséhez használja az az [ACR webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping) parancsot.

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Az eredmények megtekintéséhez használja az az [ACR webhook List-Events](/cli/azure/acr/webhook) parancsot.

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Webhook törlése

### <a name="azure-portal"></a>Azure Portal

Minden webhook törölhető úgy, hogy kiválasztja a webhookot, majd a Azure Portal **Törlés** gombját.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>További lépések

### <a name="webhook-schema-reference"></a>Webhook-séma – dokumentáció

A Azure Container Registry által kibocsátott JSON-események tartalmának formátumával és tulajdonságaival kapcsolatos részletekért tekintse meg a webhook sémájának dokumentációját:

[Azure Container Registry webhook-séma referenciája](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Események Event Grid

A cikkben tárgyalt natív beállításjegyzékbeli webhook-eseményeken kívül a Azure Container Registry eseményeket is kibocsáthat a Event Gridba:

[Rövid útmutató: Tároló-beállításjegyzékbeli események küldése Event Grid](container-registry-event-grid-quickstart.md)
