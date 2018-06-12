---
title: Figyelő CLI Azure gyors üzembe helyezési-minták
description: Parancssori felület 2.0 Példaparancsok Azure figyelő szolgáltatások. Az Azure figyelőjének értéke a Microsoft Azure szolgáltatást, amely lehetővé teszi a riasztási értesítéseket küldeni, hívja webes URL-címek, a konfigurált telemetriai adatokat, és az automatikus skálázás Cloud Services, a virtuális gépek és a Web Apps értékek alapján.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: robb
ms.component: ''
ms.openlocfilehash: 0b98cc29325310cfc0c7a62de693c309b6731447
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262402"
---
# <a name="azure-monitor-cli-20-quick-start-samples"></a>Az Azure CLI 2.0 figyelő gyors üzembe helyezési minták
Ez a cikk bemutatja, minta segítséget nyújtanak a figyelő az Azure-szolgáltatások elérésének parancsok parancssori felület (CLI). Az Azure figyelő lehetővé teszi az automatikus skálázás Felhőszolgáltatásokat, a virtuális gépek és a Web Apps és riasztási értesítések küldését, vagy hívja a webes URL-címek, a konfigurált telemetriai adatok értékek alapján.

## <a name="prerequisites"></a>Előfeltételek

Ha még nem telepítette az Azure parancssori felület, kövesse az utasításokat [az Azure CLI 2.0-s verzióját](/cli/azure/install-azure-cli). Is [Azure Cloud rendszerhéj](/azure/cloud-shell) való futtatásra a parancssori felület egy interaktív felhasználói élmény a böngészőben. Az összes rendelkezésre álló elérhető parancs a teljes körű referenciáért lásd: a [Azure CLI-figyelő referencia](https://docs.microsoft.com/en-us/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Az első lépés jelentkezzen be az Azure-fiókjával.

```azurecli
az login
```

Ez a parancs futtatása után jelentkezzen be a képernyőn megjelenő utasításokat keresztül kell. Minden parancs működik az alapértelmezett előfizetés keretében.

A jelenlegi előfizetés részleteinek megtekintéséhez használja a következő parancsot.

```azurecli
az account show
```

Egy másik előfizetésbe környezetben működő módosításához használja a következő parancsot.

```azurecli
az account set -s <Subscription ID or name>
```

Az összes támogatott Azure-figyelő parancsok listájának megtekintéséhez tegye az alábbiakat.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Az előfizetéshez tevékenység napló megtekintése

Tevékenység alkalmazásnapló-események listájának megtekintéséhez tegye az alábbiakat.

```azurecli
az monitor activity-log list
```

Próbálja meg a következőt az összes elérhető beállítások megtekintéséhez.

```azurecli
az monitor activity-log list -h
```

Íme egy példa az egy erőforráscsoport lista naplói

```azurecli
az monitor activity-log list --resource-group <group name>
```

A hívó által lista naplókhoz – példa

```azurecli
az monitor activity-log list --caller myname@company.com
```

Az erőforrástípushoz, adott dátumtartományban hívó lista naplókba – példa

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>A riasztások kezelése 
> [!NOTE]
> (Klasszikus) csak riasztások jelenleg CLI használata támogatott. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Riasztási (klasszikus) szabályok lekérése egy erőforráscsoportban található

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>A mérték (klasszikus) riasztási szabály létrehozása

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Riasztás (klasszikus) szabály törlése

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Napló-profilok

Olvassa el ebben a szakaszban napló profilok dolgozni.

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

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>A megőrzési és EventHub napló profil hozzáadása

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Napló-profil eltávolítása

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnosztika

Olvassa el ebben a szakaszban a diagnosztikai beállítások kezelése.

### <a name="get-a-diagnostic-setting"></a>A diagnosztikai beállításának beolvasása

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-log-setting"></a>Hozzon létre egy diagnosztikai naplófájl beállítást 

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

### <a name="delete-a-diagnostic-setting"></a>A diagnosztikai beállítás törlése

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Automatikus méretezés

Olvassa el ebben a szakaszban automatikus skálázás beállításokkal való együttműködés érdekében. Módosítania kell a példákat.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Erőforráscsoport automatikus skálázás beállításainak beolvasása

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Automatikus skálázás beállításainak beolvasása nevű erőforráscsoportban

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Automatikus skálázás beállítása

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```
