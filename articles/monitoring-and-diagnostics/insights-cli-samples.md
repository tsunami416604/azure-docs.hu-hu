---
title: Az Azure Monitor parancssori felület gyors üzembe helyezési minták
description: Minta CLI 2.0-parancsok az Azure Monitor-funkciók. Az Azure Monitor egy Microsoft Azure-szolgáltatás, amely lehetővé teszi, hogy a riasztási értesítéseket küldeni, a hívások webes URL-címek, a konfigurált telemetriai adatokat, és az automatikus méretezés a Cloud Services, a virtuális gépek és a Web Apps értékek alapján.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: robb
ms.component: ''
ms.openlocfilehash: dfc9a65325d25889c9d8d3c10aa0dcda2b5a9980
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213210"
---
# <a name="azure-monitor-cli-20-quick-start-samples"></a>Az Azure Monitor CLI 2.0 gyors üzembe helyezési minták
Ez a cikk bemutatja, mintaadatokat parancssori felület (CLI) parancsai segítséget nyújtanak az Azure Monitor funkcióhoz férhet hozzá. Az Azure Monitor lehetővé teszi az automatikus méretezés a Cloud Services, a virtuális gépek és a Web Apps, és a riasztási értesítéseket küldeni, vagy hívja a webes URL-címek, a konfigurált telemetriai adat értékek alapján.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem telepítette az Azure CLI, kövesse az utasításokat [az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). Is [Azure Cloud Shell](/azure/cloud-shell) futtassa a parancssori Felületet, mint egy interaktív élmény a böngészőben. Tekintse meg az összes elérhető parancsok a teljes hivatkozást a [Azure Monitor CLI-referenciáját](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Az első lépés az, hogy jelentkezzen be az Azure-fiókjába.

```azurecli
az login
```

Ez a parancs futtatása után be kell jelentkezniük a keresztül a képernyőn megjelenő utasításokat. Minden parancs működik, az alapértelmezett előfizetést kontextusában.

Az aktuális előfizetés részleteinek megtekintéséhez a következő paranccsal.

```azurecli
az account show
```

Egy másik előfizetésbe működő környezetben módosításához használja a következő parancsot.

```azurecli
az account set -s <Subscription ID or name>
```

Az összes támogatott Azure Monitor-parancsok listájának megtekintéséhez hajtsa végre a következő.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Egy előfizetéshez tartozó tevékenység napló megtekintése

Tevékenységnapló-események listájának megtekintéséhez hajtsa végre a következő.

```azurecli
az monitor activity-log list
```

Próbálja ki az összes rendelkezésre álló beállítások megtekintéséhez a következő.

```azurecli
az monitor activity-log list -h
```

Íme egy példa a lista naplók egy erőforráscsoport szerint

```azurecli
az monitor activity-log list --resource-group <group name>
```

Példa a hívó által megadott lista naplók

```azurecli
az monitor activity-log list --caller myname@company.com
```

Példa egy erőforrástípusra, adott dátumtartományban a hívó által megadott lista naplókkal

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>A riasztások kezelése 
> [!NOTE]
> Csak riasztások (klasszikus) jelenleg támogatott parancssori felületen. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Egy erőforráscsoportba tartozó riasztás (klasszikus) szabályok beolvasása

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Metrikaalapú riasztás (klasszikus) szabály létrehozása

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Egy riasztás (klasszikus) szabály törlése

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Naplóprofilok

Ebben a szakaszban található információk segítségével naplóprofilok dolgozhat.

### <a name="get-a-log-profile"></a>A napló profil beolvasása

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Az adatmegőrzési naplót profil hozzáadása

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Napló megőrzése és az EventHub profil hozzáadása

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Napló profil eltávolítása

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnosztika

Ebben a szakaszban található információk segítségével a diagnosztikai beállítások kezelése.

### <a name="get-a-diagnostic-setting"></a>Diagnosztikai beállítás beolvasása

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-log-setting"></a>Diagnosztikai napló beállítás létrehozása 

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <storage account ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

### <a name="delete-a-diagnostic-setting"></a>Diagnosztikai beállítás törlése

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Automatikus méretezés

Ebben a szakaszban található információk segítségével automatikus méretezési beállításokkal való munka. Ezekben a példákban módosítania.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Automatikus skálázási beállítások egy erőforráscsoport beolvasása

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Lépjen be az automatikus méretezési beállítások név szerint egy erőforráscsoportot

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Automatikus skálázási beállítások megadása

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```
