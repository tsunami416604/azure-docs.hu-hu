---
title: "Az Azure CLI 1.0 figyelő gyors üzembe helyezési minták. | Microsoft Docs"
description: "Parancssori felület 1.0 Példaparancsok Azure figyelő szolgáltatások. Az Azure figyelőjének értéke a Microsoft Azure szolgáltatást, amely lehetővé teszi a riasztási értesítéseket küldeni, hívja webes URL-címek, a konfigurált telemetriai adatokat, és az automatikus skálázás Cloud Services, a virtuális gépek és a Web Apps értékek alapján."
author: kamathashwin
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 1653aa81-0ee6-4622-9c65-d4801ed9006f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: ashwink
ms.openlocfilehash: ec4512500dc3c77a40d2ebd1e6b460d5bb005811
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-monitor--cross-platform-cli-10-quick-start-samples"></a>Azure-figyelő platformfüggetlen parancssori felület 1.0-ás gyors üzembe helyezési minták
Ez a cikk bemutatja, minta segítséget nyújtanak a figyelő az Azure-szolgáltatások elérésének parancsok parancssori felület (CLI). Az Azure figyelő lehetővé teszi az automatikus skálázás Felhőszolgáltatásokat, a virtuális gépek és a Web Apps és riasztási értesítések küldését, vagy hívja a webes URL-címek, a konfigurált telemetriai adatok értékek alapján.

> [!NOTE]
> Az Azure figyelő csak 2016. Szeptembertől 25. az "Azure Insights" nevezett új neve. Azonban a névterek, ezért az alábbi parancsok továbbra is tartalmazza az "insights".
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ha még nem telepítette az Azure parancssori felület, lásd: [az Azure parancssori felület telepítése](../cli-install-nodejs.md). Ha ismeri az Azure parancssori felület, további információ a [használja az Azure parancssori felület Mac, Linux és a Windows Azure Resource Manager](../xplat-cli-azure-resource-manager.md).

A Windows telepítéséhez az npm a [Node.js webhely](https://nodejs.org/). Futtatás rendszergazdaként jogosultságokkal rendelkező CMD.exe használatával a telepítés befejezése után a mappából, amelyen telepítve van-e az npm hajtsa végre az alábbiakat:

```console
npm install azure-cli --global
```

A következő nyissa meg azt a mappát/szeretne, és írja be a parancssorban:

```console
azure help
```

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba
Az első lépés jelentkezzen be az Azure-fiókjával.

```console
azure login
```

Ez a parancs futtatása után jelentkezzen be a képernyőn megjelenő utasításokat keresztül kell. A későbbiekben láthatja a fiókhoz, a TenantId és az alapértelmezett előfizetés-azonosító. Minden parancs működik az alapértelmezett előfizetés keretében.

A jelenlegi előfizetés részleteinek megtekintéséhez használja a következő parancsot.

```console
azure account show
```

Egy másik előfizetésbe környezetben működő módosításához használja a következő parancsot.

```console
azure account set "subscription ID or subscription name"
```

Azure Resource Manager és az Azure-figyelő parancsok használata, kell lennie az Azure Resource Manager módra.

```console
azure config mode arm
```

Az összes támogatott Azure-figyelő parancsok listájának megtekintéséhez tegye az alábbiakat.

```console
azure insights
```

## <a name="view-activity-log-for-a-subscription"></a>Az előfizetéshez tevékenység napló megtekintése
Tevékenység alkalmazásnapló-események listájának megtekintéséhez tegye az alábbiakat.

```console
azure insights logs list [options]
```

Próbálja meg a következőt az összes elérhető beállítások megtekintéséhez.

```console
azure insights logs list -help
```

Íme egy példa az egy erőforráscsoport lista naplói

```console
azure insights logs list --resourceGroup "myrg1"
```

A hívó által lista naplókhoz – példa

```console
azure insights logs list --caller "myname@company.com"
```

Példa listához belül a kezdő és záró dátumát egy erőforrástípust a hívó által naplózza.

```console
azure insights logs list --resourceProvider "Microsoft.Web" --caller "myname@company.com" --startTime 2016-03-08T00:00:00Z --endTime 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>A riasztások kezelése
A szakasz az információk segítségével a riasztások kezelése.

### <a name="get-alert-rules-in-a-resource-group"></a>A riasztási szabályok lekérése egy erőforráscsoportban található
```console
azure insights alerts rule list abhingrgtest123
azure insights alerts rule list abhingrgtest123 --ruleName andy0323
```

### <a name="create-a-metric-alert-rule"></a>Hozzon létre egy metrika riasztási szabályt
```console
azure insights alerts actions email create --customEmails foo@microsoft.com
azure insights alerts actions webhook create https://someuri.com
azure insights alerts rule metric set andy0323 eastus abhingrgtest123 PT5M GreaterThan 2 /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Web-EastUS/providers/Microsoft.Web/serverfarms/Default1 BytesReceived Total
```

### <a name="create-webtest-alert-rule"></a>Hozzon létre riasztási szabályt webtesztben.
```console
azure insights alerts rule webtest set leowebtestr1-webtestr1 eastus Default-Web-WestUS PT5M 1 GSMT_AvRaw /subscriptions/b67f7fec-69fc-4974-9099-a26bd6ffeda3/resourcegroups/Default-Web-WestUS/providers/microsoft.insights/webtests/leowebtestr1-webtestr1
```

### <a name="delete-an-alert-rule"></a>Riasztási szabály törlése
```console
azure insights alerts rule delete abhingrgtest123 andy0323
```

## <a name="log-profiles"></a>Napló-profilok
Olvassa el ebben a szakaszban napló profilok dolgozni.

### <a name="get-a-log-profile"></a>A napló profil beolvasása
```console
azure insights logprofile list
azure insights logprofile get -n default
```


### <a name="add-a-log-profile-without-retention"></a>Megőrzési nélkül napló profil hozzáadása
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Napló-profil eltávolítása
```console
azure insights logprofile delete --name default
```

### <a name="add-a-log-profile-with-retention"></a>Az adatmegőrzési naplót profil hozzáadása
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>A megőrzési és EventHub napló profil hozzáadása
```console
azure insights logprofile add --name default --storageId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/insightsintegration7777 --serviceBusRuleId /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/testshoeboxeastus/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia --retentionInDays 90
```


## <a name="diagnostics"></a>Diagnosztika
Olvassa el ebben a szakaszban a diagnosztikai beállítások kezelése.

### <a name="get-a-diagnostic-setting"></a>A diagnosztikai beállításának beolvasása
```console
azure insights diagnostic get --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp
```

### <a name="disable-a-diagnostic-setting"></a>A diagnosztikai beállítás letiltása
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled false
```

### <a name="enable-a-diagnostic-setting-without-retention"></a>Egy diagnosztikai beállítás nélkül megőrzési engedélyezése
```console
azure insights diagnostic set --resourceId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/andyrg/providers/Microsoft.Logic/workflows/andy0315logicapp --storageId /subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/shibanitesting --enabled true
```


## <a name="autoscale"></a>Automatikus méretezés
Olvassa el ebben a szakaszban automatikus skálázás beállításokkal való együttműködés érdekében. Módosítania kell a példákat.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Erőforráscsoport automatikus skálázás beállításainak beolvasása
```console
azure insights autoscale setting list montest2
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Automatikus skálázás beállításainak beolvasása nevű erőforráscsoportban
```console
azure insights autoscale setting list montest2 -n setting2
```


### <a name="set-auotoscale-settings"></a>Auotoscale beállításainak megadása
```console
azure insights autoscale setting set montest2 -n setting2 --settingSpec
```
