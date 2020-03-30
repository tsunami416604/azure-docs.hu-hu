---
title: Webhookok a beállításjegyzék-műveletekre való válaszadáshoz
description: Ismerje meg, hogyan webhookok események indítására, amikor leküldéses vagy lekéréses műveletek a rendszerleíró adatbázis-tárolókban történik.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5e6fd2d9f4c7727365a8e2fe3893aafebfeb7bd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454376"
---
# <a name="using-azure-container-registry-webhooks"></a>Az Azure Container Registry webhookjainak használata

Az Azure-beli tároló-beállításjegyzékek privát Docker-tárolórendszerképeket tárol és felügyel, hasonlóan ahhoz, ahogyan a Docker Hub nyilvános Docker-rendszerképeket tárol. Azt is üzemeltetheti a [Helm-diagramok](container-registry-helm-repos.md) (előzetes verzió), a csomagolási formátum alkalmazások kubernetes üzembe helyezéséhez. Webhookok segítségével eseményeket indíthat el, amikor bizonyos műveletek et hajtanak végre az egyik rendszerleíró adattárban. A webhookok válaszolhatnak a rendszerleíró adatbázis szintjén lévő eseményekre, vagy egy adott tárházcímkét is levihetnek. A [georeplikált](container-registry-geo-replication.md) beállításjegyzék konfigurálja az egyes webhookesemények egy adott regionális replika.

A webhook-kérelmekről az [Azure Container Registry webhook-sémahivatkozása.](container-registry-webhook-reference.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure Container Registry – Létrehozhat egy tároló-beállításjegyzéket Azure-előfizetésében. Használja például az [Azure Portalt](container-registry-get-started-portal.md) vagy az [Azure CLI-t.](container-registry-get-started-azure-cli.md) Az [Azure Container Registry SK-ok](container-registry-skus.md) különböző webhookok kvóták.
* A Docker parancssori felülete – Ha szeretné helyi számítógépét Docker-gazdagépként beállítani és elérni a Docker parancssori felületének parancsait, telepítse a [Docker Engine-t](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Webhook létrehozása – Azure portal

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. Keresse meg azt a tárolóbeállításjegyzéket, amelyben webhookot szeretne létrehozni.
1. A **Szolgáltatások csoportban**válassza a **Webhooks lehetőséget.**
1. Válassza **a Hozzáadás lehetőséget** a webhook eszköztáron.
1. Töltse ki a *Create webhook* űrlapot a következő információkkal:

| Érték | Leírás |
|---|---|
| Webhook neve | A webhooknak adni kívánt név. Csak betűket és számokat tartalmazhat, és 5-50 karakter hosszúnak kell lennie. |
| Hely | Georeplikált beállításjegyzék esetén adja meg a rendszerleíró adatbázis replikájának [Azure-régióját.](container-registry-geo-replication.md) 
| Szolgáltatás URI-ja | Az URI, ahol a webhook postértesítéseket kell küldenie. |
| Egyéni fejlécek | A POST kéréssel együtt átadni kívánt fejlécek. "kulcs: érték" formátumban kell lenniük. |
| Műveletek aktiválása | A webhookot kiváltó műveletek. A műveletek közé tartozik a képleküldéses, a képtörlés, a Helm-diagram leküldése, a Helm-diagram törlése és a képkarantén. A webhook aktiválásához egy vagy több műveletet is kiválaszthat. |
| status | A webhook állapota a létrehozás után. Alapértelmezés szerint engedélyezve van. |
| Hatókör | A webhook működésének hatóköre. Ha nincs megadva, a hatókör a beállításjegyzék ben lévő összes eseményre van kitéve. Megadható egy tárházhoz vagy címkéhez a "repository:tag" vagy a "repository:*" formátum használatával a tárház összes címkéjéhez. |

Példa webhook űrlapra:

![ACR webhook létrehozása felhasználói felület az Azure Portalon](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Webhook létrehozása - Azure CLI

Webhook az Azure CLI használatával hozzon létre egy webhook, használja az [azacr webhook create](/cli/azure/acr/webhook#az-acr-webhook-create) parancsot. A következő parancs létrehoz egy webhook az összes kép törlési események a rendszerleíró adatbázisban *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Webhook tesztelése

### <a name="azure-portal"></a>Azure portál

A webhook használata előtt tesztelheti azt a **Ping** gombbal. Ping egy általános POST-kérelmet küld a megadott végpontnak, és naplózza a választ. A ping funkció használatával ellenőrizheti, hogy megfelelően konfigurálta-e a webhookot.

1. Válassza ki a tesztelni kívánt webhookot.
2. A felső eszköztáron válassza a **Ping**gombot.
3. Ellenőrizze a végpont válaszát a **HTTP STATUS** oszlopban.

![ACR webhook létrehozása felhasználói felület az Azure Portalon](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Az ACR webhook az Azure CLI teszteléséhez használja az [azacr webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping) parancsot.

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Az eredmények megtekintéséhez használja az [azacr webhook list-events](/cli/azure/acr/webhook) parancsot.

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Webhook törlése

### <a name="azure-portal"></a>Azure portál

Minden webhook törölhető a webhook kiválasztásával, majd a **Törlés gombra** az Azure Portalon.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>További lépések

### <a name="webhook-schema-reference"></a>Webhook-séma hivatkozása

A JSON-esemény rakományainak az Azure Container Registry által kibocsátott formátumáról és tulajdonságairól a webhook-séma hivatkozási száma:

[Az Azure Container Registry webhook sémahivatkozása](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Eseményrács eseményei

A cikkben tárgyalt natív beállításjegyzék-webhook-események mellett az Azure Container Registry eseményeket bocsáthat ki az Event Gridbe:

[Rövid útmutató: Tárolóbeállítási események küldése az Event Gridre](container-registry-event-grid-quickstart.md)
