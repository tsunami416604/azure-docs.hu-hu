---
title: Esemény kézbesítése felügyelt szolgáltatás identitásával
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a felügyelt szolgáltatás identitása egy Azure Event Grid-témakörben. Használatával továbbíthatja az eseményeket a támogatott célhelyekre.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 04/24/2020
ms.author: spelluru
ms.openlocfilehash: 4d96f28b98cccada2ac5c77589acc6df1430bb02
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700655"
---
# <a name="event-delivery-with-managed-identity"></a>Esemény kézbesítése felügyelt identitással
Ez a cikk azt ismerteti, hogyan engedélyezhető a [felügyelt szolgáltatás identitása](../active-directory/managed-identities-azure-resources/overview.md) egy Event Grid-témakörben vagy-tartományban. Használatával továbbíthatja az eseményeket olyan támogatott célhelyekre, mint a Service Bus várólisták és témakörök, az Event hubok és a Storage-fiókok.

A cikk részletesen ismerteti a következő lépéseket:
1. Hozzon létre egy témakört vagy tartományt egy rendszer által hozzárendelt identitással (vagy) frissítsen egy meglévő témakört vagy tartományt az identitás engedélyezéséhez. 
2. Adja hozzá az identitást egy megfelelő szerepkörhöz (példa: Service Bus adatfeladó) a célhelyen (példa: a Service Bus üzenetsor)
3. Esemény-előfizetések létrehozásakor engedélyezze az identitás használatát, hogy az eseményeket a célhelyre kézbesítse. 

## <a name="create-a-topic-or-domain-with-an-identity"></a>Témakör vagy tartomány létrehozása identitással
Először nézzük meg, hogyan hozható létre egy témakör vagy egy, a rendszer által felügyelt identitással rendelkező tartomány.

### <a name="using-azure-portal"></a>Az Azure Portal használata
Egy témakörhöz/tartományhoz tartozó rendszer által hozzárendelt identitást engedélyezheti a Azure Portalban való létrehozásakor. Az alábbi képen bemutatjuk, hogyan engedélyezhető a rendszer által felügyelt identitás egy témakörben. Alapvetően a létrehozási varázsló **speciális** lapján jelölje be a **rendszerhez rendelt identitás engedélyezése** jelölőnégyzetet. Ezt a lehetőséget a tartomány létrehozása varázsló **speciális** lapján is megtekintheti. 

![Identitás engedélyezése témakör létrehozásakor](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Az Azure CLI-vel olyan témakört vagy tartományt is létrehozhat, amely rendszerhez rendelt identitással rendelkezik. Használja a parancsot a következőhöz `az eventgrid topic create` `--identity` beállított paraméterrel: `systemassigned` . Ha nem ad meg értéket ehhez a paraméterhez, a rendszer az alapértelmezett értéket `noidentity` használja. 

```azurecli-interactive
# create a topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Hasonlóképpen a `az eventgrid domain create` paranccsal is létrehozható egy rendszer által felügyelt identitással rendelkező tartomány.

## <a name="enable-identity-for-an-existing-topic-or-domain"></a>Identitás engedélyezése meglévő témakörhöz vagy tartományhoz
Az utolsó szakaszban megtanulta, hogyan engedélyezheti a rendszerfelügyelt identitást egy témakör vagy tartomány létrehozásakor. Ebből a szakaszból megtudhatja, hogyan engedélyezheti a rendszer által felügyelt identitást egy meglévő témakörhöz vagy tartományhoz. 

### <a name="using-azure-portal"></a>Az Azure Portal használata
1. Lépjen az [Azure Portalra](https://portal.azure.com)
2. Keresse meg az **Event Grid-témaköröket** a keresősáv alatt.
3. Válassza ki azt a **témakört** , amelyhez engedélyezni kívánja a felügyelt identitást. 
4. Váltson az **Identity (identitás** ) lapra. 
5. Kapcsolja be a kapcsolót az identitás engedélyezéséhez. 

    A hasonló lépéseket követve engedélyezheti az identitást egy Event Grid-tartományhoz.

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Használja a parancsot a következőre `az eventgrid topic update` `--identity` : beállítással `systemassigned` engedélyezheti a rendszerhez rendelt identitást egy meglévő témakörhöz. Ha le szeretné tiltani az identitást, állítsa be `noidentity` értékként. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

Egy meglévő tartomány frissítésére szolgáló parancs hasonló ( `az eventgrid domain update` ).

## <a name="supported-destinations-and-role-based-access-check-rbac-roles"></a>Támogatott célhelyek és szerepköralapú hozzáférés-ellenőrzési (RBAC) szerepkörök
Miután engedélyezte az identitást az Event Grid-témakörhöz vagy-tartományhoz, az Azure automatikusan létrehoz egy identitást a Azure Active Directoryban (Azure AD). Adja hozzá ezt az identitást a megfelelő RBAC-szerepkörökhöz, hogy a témakör vagy tartomány továbbítsa az eseményeket a támogatott célhelyekre. Adja hozzá például az identitást az **Azure Event Hubs Adatfeladói** szerepkörhöz egy Event Hubs névtérhez, hogy az Event Grid-témakör továbbítsa az eseményeket az adott névtérben található Event hubokba.  

Jelenleg Azure Event Grid támogatja a rendszerhez rendelt felügyelt identitással konfigurált témaköröket vagy tartományokat az események továbbításához a következő célhelyekre. Ez a tábla olyan szerepköröket is biztosít, amelyeknek az identitásnak kell lennie, hogy a témakör továbbítsa az eseményeket.

| Cél | RBAC-szerepkör | 
| ----------- | --------- | 
| Várólisták és témakörök Service Bus | [Adatfeladó Azure Service Bus](../service-bus-messaging/authenticate-application.md#built-in-rbac-roles-for-azure-service-bus) |
| Eseményközpont | [Azure Event Hubs adatfeladó](../event-hubs/authorize-access-azure-active-directory.md#built-in-rbac-roles-for-azure-event-hubs) | 
| Blob Storage | [Storage blob adatközreműködői](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) |
| Queue Storage |[Tárolási várólista adatüzenetének küldője](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) | 

## <a name="add-identity-to-rbac-roles-on-destinations"></a>Identitás hozzáadása a RBAC szerepköreihez a célhelyeken
Ez a szakasz azt ismerteti, hogyan adható hozzá a témakörhöz vagy tartományhoz tartozó identitás egy RBAC-szerepkörhöz. 

### <a name="using-azure-portal"></a>Az Azure Portal használata
A **Azure Portal** használatával hozzárendelheti a témakör/tartomány identitását egy megfelelő szerepkörhöz, hogy a témakör/tartomány továbbítsa az eseményeket a célhelyre. 

Az alábbi példa egy **msitesttopic** nevű Event Grid-témakörhöz tartozó felügyelt identitást hoz létre egy olyan Service Bus **névtérhez** , amely üzenetsor-vagy témakör-erőforrást tartalmaz **Azure Service Bus adatfeladói** szerepkörhöz. Ha a szerepkört a névtér szintjén adja hozzá, akkor a témakör a névtérben lévő összes entitáshoz továbbíthatja az eseményeket. 

1. Navigáljon a **Service Bus névtérhez** a [Azure Portal](https://portal.azure.com). 
2. A bal oldali ablaktáblán válassza a **Access Control** lehetőséget. 
3. A **szerepkör-hozzárendelés hozzáadása** szakaszban válassza a **Hozzáadás** lehetőséget. 
4. A **szerepkör-hozzárendelés hozzáadása** oldalon hajtsa végre a következő lépéseket:
    1. Válassza ki a szerepkört. Ebben az esetben **Azure Service Bus az adatfeladó**. 
    2. Válassza ki a témakör vagy tartomány **identitását** . 
    3. a konfiguráció mentéséhez kattintson a **Mentés** gombra.

A lépések hasonlóak az identitásnak a táblázatban említett más szerepkörökhöz való hozzáadásához. 

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Az ebben a szakaszban szereplő példa bemutatja, hogyan adhat identitást egy RBAC-szerepkörhöz az **Azure CLI** használatával. A mintául szolgáló parancsok az Event Grid-témakörökre vonatkoznak. Az Event Grid-tartományok parancsai hasonlóak. 

#### <a name="get-principal-id-for-the-topics-system-identity"></a>Résztvevő AZONOSÍTÓjának lekérése a témakör rendszeridentitásához 
Először szerezze be a témakör rendszerfelügyelt identitásának elsődleges AZONOSÍTÓját, és rendelje hozzá az identitást a megfelelő szerepkörökhöz.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Szerepkör-hozzárendelés létrehozása az Event hubokhoz különböző hatókörökben 
Az alábbi CLI-példa bemutatja, hogyan adhat hozzá egy témakör identitását az **Azure Event Hubs Adatfeladói** szerepkörhöz a névtér vagy az Event hub szintjén. Ha a szerepkör-hozzárendelést a névtérben hozza létre, a témakör továbbíthatja az eseményeket az adott névtérben található összes Event hubokba. Ha az Event hub szintjén hoz létre, akkor a témakör csak az adott Event hub-ra továbbíthatja az eseményeket. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-service-bus-topic-at-various-scopes"></a>Szerepkör-hozzárendelés létrehozása Service Bus témakörhöz különböző hatókörökben 
Az alábbi CLI-példa azt szemlélteti, hogyan adhat hozzá egy témakör identitását a **Azure Service Bus Adatfeladói** szerepkörhöz a névtér szintjén vagy a Service Bus témakör szintjén. Ha a névtérben létrehozza a szerepkör-hozzárendelést, az Event Grid-témakör továbbíthatja az összes entitást (Service Bus várólistákat vagy témaköröket) az adott névtéren belül. Ha a Service Bus üzenetsor vagy a témakör szintjén hozza létre, az Event Grid-témakör csak az adott Service Bus-várólistára vagy-témakörre továbbítja az eseményeket. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-identity"></a>Identitást használó esemény-előfizetések létrehozása
Miután rendelkezik egy, a rendszer által felügyelt identitással rendelkező témakörrel vagy tartománnyal, és hozzáadta az identitást a célhely megfelelő szerepköréhez, készen áll az identitást használó előfizetések létrehozására. 

### <a name="using-azure-portal"></a>Az Azure Portal használata
Esemény-előfizetés létrehozásakor megjelenik egy lehetőség, amely lehetővé teszi a rendszer által hozzárendelt identitás használatának engedélyezését egy végponthoz a **VÉGPONT részletei** szakaszban. 

![Identitás engedélyezése az Service Bus üzenetsor esemény-előfizetésének létrehozásakor](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

Engedélyezheti a rendszerhez rendelt identitás használatát is a **további funkciók** lapon a kézbesítetlen levelekhez. 

![Rendszer által hozzárendelt identitás engedélyezése a kézbesítetlen levelekhez](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="using-azure-cli---service-bus-queue"></a>Az Azure CLI – Service Bus üzenetsor használata 
Ebből a szakaszból megtudhatja, hogyan használhatja az **Azure CLI** -t a rendszer által hozzárendelt identitások használatának engedélyezésére Service Bus üzenetsor számára. Az identitásnak a **Azure Service Bus Adatfeladói** szerepkör tagjának kell lennie. Emellett a **Storage-blob adatközreműködői** szerepkör tagjának kell lennie a kézbesítetlen levelekhez használt Storage-fiókban. 

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

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery"></a>esemény-előfizetés létrehozása felügyelt identitás használatával kézbesítéshez 
Ez a minta egy esemény-előfizetést hoz létre egy Event Grid-témakörhöz, amelyben a végpont típusa **Service Bus várólistára**van állítva. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery-and-dead-lettering"></a>esemény-előfizetés létrehozása felügyelt identitás használatával kézbesítéshez és kézbesítetlen levelekhez
Ez a minta egy esemény-előfizetést hoz létre egy Event Grid-témakörhöz, amelyben a végpont típusa **Service Bus várólistára**van állítva. Azt is meghatározza, hogy a rendszer által felügyelt identitás a kézbesítetlen leveleknél használható legyen. 

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

### <a name="azure-cli---event-hubs"></a>Azure CLI – Event Hubs 
Ebből a szakaszból megtudhatja, hogyan használhatja az **Azure CLI** -t a rendszer által hozzárendelt identitások használatának engedélyezéséhez, hogy eseményeket nyújtson az Event hub számára. Az identitásnak az **Azure Event Hubs Adatfeladói** szerepkör tagjának kell lennie. Emellett a **Storage-blob adatközreműködői** szerepkör tagjának kell lennie a kézbesítetlen levelekhez használt Storage-fiókban. 

#### <a name="define-variables"></a>Változók meghatározása
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>esemény-előfizetés létrehozása felügyelt identitás használatával kézbesítéshez 
Ez a minta egy esemény-előfizetést hoz létre egy Event Grid-témakörhöz, amelyben a végpont típusa **Event Hubs**értékre van állítva. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>Esemény-előfizetés létrehozása felügyelt identitás használatával a kézbesítéshez és a kézbesítetlen levelek 
Ez a minta egy esemény-előfizetést hoz létre egy Event Grid-témakörhöz, amelyben a végpont típusa **Event Hubs**értékre van állítva. Azt is meghatározza, hogy a rendszer által felügyelt identitás a kézbesítetlen leveleknél használható legyen. 

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

### <a name="azure-cli---azure-storage-queue"></a>Azure CLI – Azure Storage-üzenetsor 
Ebből a szakaszból megtudhatja, hogyan használhatja az **Azure CLI** -t az események Azure Storage-üzenetsorbe való továbbítására a rendszer által hozzárendelt identitások használatának lehetővé tételéhez. Az identitásnak a Storage- **blob adatközreműködői** szerepkör tagjának kell lennie a Storage-fiókban.

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

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>Esemény-előfizetés létrehozása felügyelt identitás használatával kézbesítéshez 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>Esemény-előfizetés létrehozása felügyelt identitás használatával a kézbesítéshez és a kézbesítetlen levelek 

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