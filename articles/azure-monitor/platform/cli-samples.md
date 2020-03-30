---
title: Az Azure Monitor CLI gyorsindítási mintái
description: Minta CLI-parancsok az Azure Monitor szolgáltatásaihoz. Az Azure Monitor egy Microsoft Azure-szolgáltatás, amely lehetővé teszi, hogy riasztási értesítéseket küldjön, webes URL-címeket hívjon meg a konfigurált telemetriai adatok értékei alapján, valamint az autoScale-felhőszolgáltatásokat, a virtuális gépeket és a webalkalmazásokat.
ms.subservice: ''
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: 69687cee8b8a907b82f2c848242ac64d54dedb87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248930"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>Az Azure Monitor CLI gyorsindítási mintái
Ez a cikk bemutatja a minta parancssori felület (CLI) parancsokat, amelyek segítenek az Azure Monitor szolgáltatásainak elérésében. Az Azure Monitor lehetővé teszi a felhőszolgáltatások, a virtuális gépek és a webalkalmazások automatikus skálázását, valamint riasztási értesítések küldését vagy webes URL-címek hívását a konfigurált telemetriai adatok értékei alapján.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem telepítette az Azure CLI-t, kövesse [az Azure CLI telepítése](/cli/azure/install-azure-cli)című témakörutasításait. Az Azure [Cloud Shell](/azure/cloud-shell) használatával is futtathatja a CLI-t interaktív élményként a böngészőjében. Tekintse meg az Azure Monitor [CLI-hivatkozásában](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest)elérhető összes parancs teljes körű hivatkozását. 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Az első lépés az Azure-fiókjába való bejelentkezés.

```azurecli
az login
```

A parancs futtatása után a képernyőn megjelenő utasításokon keresztül kell bejelentkeznie. Minden parancs az alapértelmezett előfizetés környezetében működik.

Az aktuális előfizetés részleteinek listázásához használja a következő parancsot.

```azurecli
az account show
```

Ha a munkakörnyezetet másik előfizetésre szeretné módosítani, használja a következő parancsot.

```azurecli
az account set -s <Subscription ID or name>
```

Az összes támogatott Azure Monitor-parancs listájának megtekintéséhez hajtsa végre az alábbiakat.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Előfizetés tevékenységnaplójának megtekintése

A tevékenységnapló-események listájának megtekintéséhez hajtsa végre az alábbiakat.

```azurecli
az monitor activity-log list
```

Próbálja ki az alábbiakat az összes rendelkezésre álló lehetőség megtekintéséhez.

```azurecli
az monitor activity-log list -h
```

Íme egy példa a resourceGroup naplóinak listázására

```azurecli
az monitor activity-log list --resource-group <group name>
```

Példa a naplók hívó szerinti listára

```azurecli
az monitor activity-log list --caller myname@company.com
```

Példa a naplók hívó szerint történő listázására egy erőforrástípuson belül, egy dátumtartományon belül

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Riasztások kal való kapcsolat 
> [!NOTE]
> A CLI jelenleg csak a riasztásokat (klasszikus) támogatja. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Riasztási (klasszikus) szabályok beszereznie egy erőforráscsoportban

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Metrikariasztási (klasszikus) szabály létrehozása

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Riasztási (klasszikus) szabály törlése

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Naplóprofilok

Az ebben a szakaszban található információk segítségével naplóprofilokkal dolgozhat.

### <a name="get-a-log-profile"></a>Naplóprofil beszereznie

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Adatmegőrzéssel rendelkező naplóprofil hozzáadása

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Naplóprofil hozzáadása megőrzési és EventHub-profillal

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Naplóprofil eltávolítása

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnosztika

Az ebben a szakaszban található információk segítségével dolgozhat a diagnosztikai beállításokkal.

### <a name="get-a-diagnostic-setting"></a>Diagnosztikai beállítás beszereznie

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-setting"></a>Diagnosztikai beállítás létrehozása 

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

Az ebben a szakaszban található információk segítségével automatikus skálázási beállításokat használhat. Ezeket a példákat módosítania kell.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Erőforráscsoport automatikus skálázási beállításainak beszereznie

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Automatikus méretezési beállítások beszereznie név szerint egy erőforráscsoportban

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Automatikus méretezési beállítások megadása

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```

