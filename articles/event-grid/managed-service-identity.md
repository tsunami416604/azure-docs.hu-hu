---
title: Esemény kézbesítése felügyelt szolgáltatás identitásával
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a felügyelt szolgáltatás identitása egy Azure Event Grid-témakörben. Használatával továbbíthatja az eseményeket a támogatott célhelyekre.
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: b437d519a076104b64fb2df5cba1cd61a865b1fc
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87499823"
---
# <a name="event-delivery-with-a-managed-identity"></a>Esemény kézbesítése felügyelt identitással
Ez a cikk azt ismerteti, hogyan engedélyezhető a [felügyelt szolgáltatás identitása](../active-directory/managed-identities-azure-resources/overview.md) az Azure Event Grid-témakörökhöz vagy-tartományokhoz. Használatával továbbíthatja az eseményeket olyan támogatott célhelyekre, mint a Service Bus várólisták és témakörök, az Event hubok és a Storage-fiókok.

A cikk részletesen ismerteti a következő lépéseket:
1. Hozzon létre egy témakört vagy tartományt egy rendszer által hozzárendelt identitással, vagy frissítsen egy meglévő témakört vagy tartományt az identitás engedélyezéséhez. 
1. Adja hozzá az identitást egy megfelelő szerepkörhöz (például Service Bus adatfeladóhoz) a célhelyen (például egy Service Bus üzenetsor).
1. Esemény-előfizetések létrehozásakor engedélyezze az identitás használatát, hogy az eseményeket a célhelyre kézbesítse. 

## <a name="create-a-topic-or-domain-with-an-identity"></a>Témakör vagy tartomány létrehozása identitással
Először nézzük meg, hogyan hozható létre egy témakör vagy egy, a rendszer által felügyelt identitással rendelkező tartomány.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata
Egy témakörhöz vagy tartományhoz is engedélyezheti a rendszerhez rendelt identitást, miközben a Azure Portalban hozza létre. Az alábbi képen bemutatjuk, hogyan engedélyezhető a rendszer által felügyelt identitás egy témakörhöz. Alapvetően a létrehozási varázsló **speciális** lapján jelölje be a **rendszerhez rendelt identitás engedélyezése** jelölőnégyzetet. Ezt a lehetőséget a tartomány-létrehozási varázsló **speciális** oldalán fogja látni. 

![Identitás engedélyezése témakör létrehozásakor](./media/managed-service-identity/create-topic-identity.png)

### <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata
Az Azure CLI-vel olyan témakört vagy tartományt is létrehozhat, amely rendszerhez rendelt identitással rendelkezik. Használja a parancsot a következőhöz `az eventgrid topic create` `--identity` beállított paraméterrel: `systemassigned` . Ha nem ad meg értéket ehhez a paraméterhez, a rendszer az alapértelmezett értéket `noidentity` használja. 

```azurecli-interactive
# create a topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Hasonlóképpen a `az eventgrid domain create` paranccsal is létrehozható egy rendszer által felügyelt identitással rendelkező tartomány.

## <a name="enable-an-identity-for-an-existing-topic-or-domain"></a>Identitás engedélyezése meglévő témakörhöz vagy tartományhoz
Az előző szakaszban megtanulta, hogyan engedélyezheti a rendszer által felügyelt identitásokat egy témakör vagy tartomány létrehozásakor. Ebből a szakaszból megtudhatja, hogyan engedélyezheti a rendszer által felügyelt identitásokat egy meglévő témakörhöz vagy tartományhoz. 

### <a name="use-the-azure-portal"></a>Az Azure Portal használata
A következő eljárás bemutatja, hogyan engedélyezheti a rendszer által felügyelt identitást a témakörben. A tartomány identitásának engedélyezésének lépései hasonlóak. 

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. Keresse meg az **Event Grid-témaköröket** a felül található keresési sávon.
3. Válassza ki azt a **témakört** , amelyhez engedélyezni kívánja a felügyelt identitást. 
4. Váltson az **Identity (identitás** ) lapra. 
5. Kapcsolja **be a** kapcsolót az identitás engedélyezéséhez. 
1. A beállítás mentéséhez válassza az eszköztár **Mentés** elemét. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="A témakör Identity lapja"::: 

Az Event Grid-tartomány identitásának engedélyezéséhez hasonló lépések használhatók.

### <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata
Használja a parancsot a következőre `az eventgrid topic update` `--identity` : beállítással `systemassigned` engedélyezheti a rendszerhez rendelt identitást egy meglévő témakörhöz. Ha le szeretné tiltani az identitást, állítsa be `noidentity` értékként. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

Egy meglévő tartomány frissítésére szolgáló parancs hasonló ( `az eventgrid domain update` ).

## <a name="supported-destinations-and-rbac-roles"></a>Támogatott célhelyek és RBAC szerepkörök
Miután engedélyezte az identitást az Event Grid-témakörhöz vagy-tartományhoz, az Azure automatikusan létrehoz egy identitást Azure Active Directoryban. Adja hozzá ezt az identitást a megfelelő Azure-szerepkörökhöz, hogy a témakör vagy tartomány továbbítsa az eseményeket a támogatott célhelyekre. Például adja hozzá az identitást az **azure Event Hubs Adatfeladói** szerepkörhöz egy Azure Event Hubs-névtérhez, hogy az Event Grid-témakör továbbítsa az eseményeket az adott névtérben található Event hubokba. 

Az Azure Event Grid jelenleg a rendszerhez rendelt felügyelt identitással konfigurált témaköröket és tartományokat támogatja az események továbbításához a következő célhelyekre. Ez a tábla olyan szerepköröket is biztosít, amelyeknek az identitásnak kell lennie, hogy a témakör továbbítsa az eseményeket.

| Cél | RBAC-szerepkör | 
| ----------- | --------- | 
| Várólisták és témakörök Service Bus | [Adatfeladó Azure Service Bus](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Azure Event Hubs | [Azure Event Hubs adatfeladó](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Azure Blob Storage | [Storage blob adatközreműködői](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) |
| Azure Queue Storage |[Tárolási várólista adatüzenetének küldője](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) | 

## <a name="add-an-identity-to-rbac-roles-on-destinations"></a>Identitás hozzáadása RBAC-szerepkörökhöz célhelyeken
Ez a szakasz azt ismerteti, hogyan adható hozzá a témakörhöz vagy tartományhoz tartozó identitás egy RBAC-szerepkörhöz. 

### <a name="use-the-azure-portal"></a>Az Azure Portal használata
A Azure Portal használatával hozzárendelheti a témakört vagy a tartományi identitást egy megfelelő szerepkörhöz, hogy a témakör vagy tartomány továbbítsa az eseményeket a célhelyre. 

Az alábbi példa egy **msitesttopic** nevű Event Grid-témakörhöz tartozó felügyelt identitást hoz létre egy olyan Service Bus névtérhez, amely üzenetsor-vagy témakör-erőforrást tartalmaz **Azure Service Bus adatfeladói** szerepkörhöz. Ha a szerepkört a névtér szintjén adja hozzá, a témakör a névtérben lévő összes entitáshoz továbbíthatja az eseményeket. 

1. Nyissa meg a **Service Bus névteret** a [Azure Portalban](https://portal.azure.com). 
1. A bal oldali ablaktáblán válassza a **Access Control** lehetőséget. 
1. A **szerepkör-hozzárendelés hozzáadása** szakaszban válassza a **Hozzáadás** lehetőséget. 
1. A **szerepkör-hozzárendelés hozzáadása** oldalon hajtsa végre a következő lépéseket:
    1. Válassza ki a szerepkört. Ebben az esetben **Azure Service Bus az adatfeladó**. 
    1. Válassza ki a témakör vagy tartomány **identitását** . 
    1. A konfiguráció mentéséhez kattintson a **Mentés** gombra.

A lépések hasonlóak az identitásnak a táblázatban említett más szerepkörökhöz való hozzáadásához. 

### <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata
Az ebben a szakaszban szereplő példa bemutatja, hogyan adhat identitást egy RBAC-szerepkörhöz az Azure CLI használatával. A mintául szolgáló parancsok az Event Grid-témakörökre vonatkoznak. Az Event Grid-tartományok parancsai hasonlóak. 

#### <a name="get-the-principal-id-for-the-topics-system-identity"></a>A témakör rendszeridentitásának elsődleges AZONOSÍTÓjának beolvasása 
Először szerezze be a témakör rendszerfelügyelt identitásának elsődleges AZONOSÍTÓját, és rendelje hozzá az identitást a megfelelő szerepkörökhöz.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Szerepkör-hozzárendelés létrehozása az Event hubokhoz különböző hatókörökben 
Az alábbi CLI-példa bemutatja, hogyan adhat hozzá egy témakör identitását az **Azure Event Hubs Adatfeladói** szerepkörhöz a névtér vagy az Event hub szintjén. Ha a szerepkör-hozzárendelést a névtér szintjén hozza létre, a témakör továbbíthatja az eseményeket az adott névtérben található összes Event hubokba. Ha az Event hub szintjén hoz létre szerepkör-hozzárendelést, a témakör csak az adott Event hubhoz továbbítja az eseményeket. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Szerepkör-hozzárendelés létrehozása Service Bus témakörhöz különböző hatókörökben 
Az alábbi CLI-példa azt szemlélteti, hogyan adhat hozzá egy témakör identitását a **Azure Service Bus Adatfeladói** szerepkörhöz a névtér szintjén vagy a Service Bus témakör szintjén. Ha a szerepkör-hozzárendelést a névtér szintjén hozza létre, az Event Grid-témakör a névtérben lévő összes entitásra (Service Bus várólistákra vagy témakörökre) továbbíthatja az eseményeket. Ha a Service Bus üzenetsor vagy a témakör szintjén hoz létre szerepkör-hozzárendelést, akkor az Event Grid-témakör csak az adott Service Bus-várólistára vagy-témakörre továbbíthatja az eseményeket. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-an-identity"></a>Identitást használó esemény-előfizetések létrehozása
Ha már rendelkezik egy, a rendszer által felügyelt identitással rendelkező témakörrel vagy tartománnyal, és az identitást hozzáadta a megfelelő szerepkörhöz a célhelyen, készen áll az identitást használó előfizetések létrehozására. 

### <a name="use-the-azure-portal"></a>Az Azure Portal használata
Ha létrehoz egy esemény-előfizetést, a végpont **részletei** szakaszban megtekintheti a rendszer által hozzárendelt identitások használatának engedélyezését. 

![Identitás engedélyezése Service Bus üzenetsor esemény-előfizetésének létrehozásakor](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

Azt is engedélyezheti, hogy a rendszer által hozzárendelt identitást használja a **további funkciók** lapon a kézbesítetlen levelekhez. 

![Rendszer által hozzárendelt identitás engedélyezése a kézbesítetlen levelekhez](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="use-the-azure-cli---service-bus-queue"></a>Az Azure CLI-Service Bus üzenetsor használata 
Ebből a szakaszból megtudhatja, hogyan használhatja az Azure CLI-t, hogy egy rendszer által hozzárendelt identitást engedélyezzen az események Service Bus üzenetsor számára való továbbításához. Az identitásnak a **Azure Service Bus Adatfeladói** szerepkör tagjának kell lennie. Emellett a **Storage-blob adatközreműködői** szerepkör tagjának kell lennie a kézbesítetlen levelekhez használt Storage-fiókban. 

#### <a name="define-variables"></a>Változók meghatározása
Először a CLI-parancsban használandó következő változók értékeit kell megadnia. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Esemény-előfizetés létrehozása felügyelt identitás használatával kézbesítéshez 
Ez a minta egy Event Grid-témakörhöz hoz létre egy esemény-előfizetést, amelyhez **Service Bus üzenetsor**van beállítva. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>Esemény-előfizetés létrehozása felügyelt identitás használatával kézbesítéshez és kézbesítetlen levelekhez
Ez a minta egy Event Grid-témakörhöz hoz létre egy esemény-előfizetést, amelyhez **Service Bus üzenetsor**van beállítva. Azt is megadja, hogy a rendszer által felügyelt identitást kell használni a kézbesítetlen levelekhez. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="use-the-azure-cli---event-hubs"></a>Az Azure CLI használata – Event Hubs 
Ebből a szakaszból megtudhatja, hogyan használhatja az Azure CLI-t, hogy egy rendszer által hozzárendelt identitást engedélyezzen az események az Event hubhoz való továbbításához. Az identitásnak az **Azure Event Hubs Adatfeladói** szerepkör tagjának kell lennie. Emellett a **Storage-blob adatközreműködői** szerepkör tagjának kell lennie a kézbesítetlen levelekhez használt Storage-fiókban. 

#### <a name="define-variables"></a>Változók meghatározása
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Esemény-előfizetés létrehozása felügyelt identitás használatával kézbesítéshez 
Ez a minta parancs egy Event Grid-témakörhöz hoz létre egy esemény-előfizetést, amely **Event Hubs**értékre van beállítva. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Esemény-előfizetés létrehozása felügyelt identitás használatával a kézbesítéshez és a kézbesítetlen levelek 
Ez a minta parancs egy Event Grid-témakörhöz hoz létre egy esemény-előfizetést, amely **Event Hubs**értékre van beállítva. Azt is megadja, hogy a rendszer által felügyelt identitást kell használni a kézbesítetlen levelekhez. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="use-the-azure-cli---azure-storage-queue"></a>Az Azure CLI – Azure Storage-üzenetsor használata 
Ebből a szakaszból megtudhatja, hogyan használhatja az Azure CLI-t, hogy egy rendszer által hozzárendelt identitás használatával kézbesítse az eseményeket egy Azure Storage-üzenetsor számára. Az identitásnak a Storage- **blob adatközreműködői** szerepkör tagjának kell lennie a Storage-fiókban.

#### <a name="define-variables"></a>Változók meghatározása  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Esemény-előfizetés létrehozása felügyelt identitás használatával kézbesítéshez 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Esemény-előfizetés létrehozása felügyelt identitás használatával a kézbesítéshez és a kézbesítetlen levelek 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```



## <a name="next-steps"></a>További lépések
A felügyelt szolgáltatás identitásával kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitása](../active-directory/managed-identities-azure-resources/overview.md). 
