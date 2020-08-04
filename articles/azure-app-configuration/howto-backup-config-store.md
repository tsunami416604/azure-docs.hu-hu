---
title: A kulcsok értékének automatikus biztonsági mentése az Azure app Configuration Store-ból
description: Ismerje meg, hogyan állíthat be automatikus biztonsági mentést az alkalmazás konfigurációs tárolói közötti kulcs-értékekről
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: ef777f235c85c423ea126fb7249f8e6a11492d3a
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87528548"
---
# <a name="backup-app-configuration-stores-automatically"></a>A biztonsági mentési alkalmazás konfigurációs tárolói automatikusan

Ebből a cikkből megtudhatja, hogyan állíthatja be a kulcs értékeinek automatikus biztonsági mentését egy elsődleges alkalmazás konfigurációs tárolójából egy másodlagos tárolóba. Kihasználja Azure Event Grid integrálását az alkalmazás konfigurációjával. A beállítás után az alkalmazás konfigurációja közzéteszi az eseményeket, hogy Event Grid a konfigurációs tárolóban lévő kulcs-értékekben végrehajtott módosításokat. Event Grid számos olyan Azure-szolgáltatást támogat, amelyekről a felhasználók előfizethetnek a kulcs-érték létrehozásakor, frissítésekor vagy törlésekor.

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban egy Azure Storage-üzenetsor használatával fogadja az eseményeket a Event Gridtól, és időzítő-triggert használ Azure Functions a kötegek tárolási várólistájában lévő események feldolgozásához. Ha az események alapján aktiválódik, a függvény beolvassa az elsődleges alkalmazás konfigurációs tárolójából módosított kulcsok legújabb értékeit, és ennek megfelelően frissíti a másodlagos tárolót. Ezzel a beállítással több módosítást is összekapcsolhat egy biztonsági mentési művelet rövid időszakában, és elkerülheti az alkalmazás konfigurációs tárolóinak túlzott kéréseit.

![Alkalmazás-konfigurációs tár biztonsági mentési architektúrája](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Erőforrás kiépítése

Az alkalmazások konfigurációs tárolóinak biztonsági mentésének motivációja az, hogy több konfigurációs áruházat használjon különböző Azure-régiókban az alkalmazás földrajzi rugalmasságának növeléséhez. Ennek eléréséhez az elsődleges és a másodlagos tárolónak különböző Azure-régiókban kell lennie. Az oktatóanyagban létrehozott összes más erőforrás kiépíthető bármely tetszőleges régióban. Ennek az az oka, hogy ha az elsődleges régió nem érhető el, a biztonsági mentés nem lesz új, amíg az elsődleges régió újra elérhetővé nem válik.

Ebben az oktatóanyagban másodlagos tárolót fog létrehozni a `centralus` régióban, és minden más erőforrást a `westus` régióban.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/). Igény szerint a Azure Cloud Shell is használhatja.
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) az Azure-fejlesztési számítási feladattal.
- Töltse le és telepítse a [.net Core SDK](https://dotnet.microsoft.com/download).
- Az Azure CLI legújabb verziója (2.3.1 vagy újabb). A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha az Azure CLI-t használja, először be kell jelentkeznie a használatával `az login` . Igény szerint a Azure Cloud Shell is használhatja.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az erőforráscsoport egy olyan logikai gyűjtemény, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal.

A következő példában létrehozunk egy `westus` nevű erőforráscsoportot a `<resource_group_name>` helyen.  A `<resource_group_name>` elemet az erőforráscsoport egyedi nevére cserélje le.

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>Alkalmazás-konfigurációs tárolók létrehozása

Hozza létre az elsődleges és a másodlagos alkalmazás konfigurációs tárolóit különböző régiókban.
Cserélje le a  `<primary_appconfig_name>` és `<secondary_appconfig_name>` a nevet a konfigurációs tárolók egyedi neveire. Az áruház nevének egyedinek kell lennie, mert DNS-névként van használatban.

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-azure-storage-queue"></a>Azure Storage-üzenetsor létrehozása

Hozzon létre egy Storage-fiókot és egy tárolási várólistát a Event Grid által közzétett események fogadásához.

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>Előfizetés az alkalmazás konfigurációs adattár eseményeire

Előfizet az alábbi két eseményre az elsődleges alkalmazás konfigurációs tárolójából:

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

A következő parancs egy Event Grid-előfizetést hoz létre a tárolási üzenetsor felé irányuló két eseményhez, ahol a végpont típusa, `storagequeue` a végpont pedig a várólista-azonosítóra van állítva. Cserélje le az `<event_subscription_name>` nevet az esemény-előfizetés számára választott névre.

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-azure-functions-for-handling-events-from-storage-queue"></a>Azure Functions létrehozása az események tárolási sorból való kezelésére

### <a name="setup-with-ready-to-use-azure-functions"></a>A telepítő használatra kész Azure Functions

Ebben az oktatóanyagban a C# Azure Functions fog működni a következő tulajdonságokkal:
- Futásidejű verem .NET Core 3,1
- Azure Functions futtatókörnyezet 3. x verziója
- Az időzítő által aktivált függvény 10 percenként

Annak érdekében, hogy megkönnyítse az adatai biztonsági mentését, teszteltük és közzétettük azokat a [Azure Functionsokat](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) , amelyeket a kód módosítása nélkül használhat. Töltse le a Project-fájlokat, és [tegye közzé a saját Azure-függvényalkalmazás a Visual studióból.](/azure/azure-functions/functions-develop-vs#publish-to-azure)

> [!IMPORTANT]
> A letöltött kódban ne módosítsa a környezeti változókat. A szükséges alkalmazás-beállításokat a következő szakaszban fogja létrehozni.
>

### <a name="build-your-own-azure-functions"></a>Saját Azure Functions létrehozása

Ha a fent megadott mintakód nem felel meg a követelményeknek, létrehozhat saját Azure Functions is. A biztonsági mentés befejezéséhez a függvénynek képesnek kell lennie a következő feladatok végrehajtására:
- Rendszeresen olvassa el a tárolási várólista tartalmát, és ellenőrizze, hogy tartalmaz-e értesítéseket a Event Gridról. A megvalósítás részleteiért tekintse meg a [Storage ÜZENETSOR SDK](/azure/storage/queues/storage-quickstart-queues-dotnet) -t.
- Ha a tárolási várólista [Event Gridból származó esemény-értesítéseket](/azure/azure-app-configuration/concept-app-configuration-event?branch=pr-en-us-112982#event-schema)tartalmaz, bontsa ki az összes egyedi <kulcsot, a címke> az esemény üzenetei közül. Az elsődleges tárolóban a kulcs-érték változások egyedi azonosítója a kulcs és a felirat kombinációja.
- Az elsődleges tároló összes beállításának beolvasása. Csak azokat a beállításokat frissítse a másodlagos tárolóban, amelyek megfelelő eseménnyel rendelkeznek a tárolási várólistában. Törölje az összes olyan beállítást a másodlagos tárolóból, amely megtalálható a Storage-várólistában, de az elsődleges tárolóban nem. Kihasználhatja az [app CONFIGURATION SDK](https://github.com/Azure/AppConfiguration#sdks) -t, hogy programozott módon hozzáférhessen a konfigurációs áruházakhoz.
- Üzenetek törlése a tárolási sorból, ha a feldolgozás során nem történt kivétel.
- Győződjön meg arról, hogy az igényeinek megfelelően valósítja meg a hibakezelés megvalósítását. A fenti mintakód használatával megtekintheti az esetlegesen kezelni kívánt gyakori kivételeket.

Ha többet szeretne megtudni a Azure Functions létrehozásáról, tekintse meg az [időzítő által aktivált függvény létrehozása az Azure-ban](/azure/azure-functions/functions-create-scheduled-function) című témakört, és [fejlesszen Azure functions a Visual Studióval](/azure/azure-functions/functions-develop-vs).


> [!IMPORTANT]
> A legjobb döntés szerint válassza ki az időzítő ütemtervet az elsődleges konfigurációs tároló módosításainak gyakorisága alapján. Ne feledje, hogy a függvény futtatása túl gyakran a tárolóra irányuló kérelmek lekérését eredményezheti.
>


## <a name="create-azure-function-app-settings"></a>Azure függvényalkalmazás-beállítások létrehozása

Ha a megadott Azure Functions használja, akkor az Azure-függvényalkalmazás következő Alkalmazásbeállítások szükségesek:
- `PrimaryStoreEndpoint`: Az elsődleges alkalmazás konfigurációs tárolójához tartozó végpont. Például: `https://{primary_appconfig_name}.azconfig.io`
- `SecondaryStoreEndpoint`: Végpont a másodlagos alkalmazás konfigurációs tárolójához. Például: `https://{secondary_appconfig_name}.azconfig.io`
- `StorageQueueUri`: Tárolási várólista URI-ja. Például: `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`

A következő parancs létrehozza a szükséges Alkalmazásbeállítások az Azure-függvényalkalmazás. Cserélje le az helyére `<function_app_name>` Az Azure-függvényalkalmazás nevét.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-azure-function-app"></a>Hozzáférés biztosítása az Azure függvényalkalmazás felügyelt identitásához

Az alábbi parancs vagy [Azure Portal](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity) használatával hozzáadhat egy rendszerhez rendelt felügyelt identitást az Azure-függvényalkalmazáshoz.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> A szükséges erőforrás-létrehozási és-szerepkör-kezelés végrehajtásához a fióknak `'Owner'` a megfelelő hatókörben (az előfizetésben vagy az erőforráscsoportban) engedélyekkel kell rendelkeznie. Ha segítségre van szüksége a szerepkör-hozzárendeléshez, Ismerje meg, [hogyan adhat hozzá vagy távolíthat el Azure-beli szerepkör-hozzárendeléseket a Azure Portal használatával](/azure/role-based-access-control/role-assignments-portal).

Az alábbi parancsok vagy [Azure Portal](/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity#grant-access-to-app-configuration) használatával biztosíthatja az Azure függvényalkalmazás hozzáférésének felügyelt identitását az alkalmazás konfigurációs tárolói számára.
- Rendeljen `App Configuration Data Reader` szerepkört az elsődleges alkalmazás-konfigurációs tárolóban.
- Rendeljen `App Configuration Data Owner` szerepkört a másodlagos alkalmazás-konfigurációs tárolóban.

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

Az alábbi parancs vagy [Azure Portal](/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal) használatával biztosíthatja az Azure függvényalkalmazás hozzáférését a tárolási sorhoz. 
- Rendeljen hozzá `Storage Queue Data Contributor` szerepkört a tárolási várólistában.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Alkalmazás-konfigurációs esemény elindítása

Annak ellenőrzéséhez, hogy minden működik-e, létrehozhat/frissíthet vagy törölhet egy Key értéket az elsődleges tárolóból. Ezt a változást a másodlagos tárolóban automatikusan kell látni néhány másodperccel azután, hogy Azure Functions az időzítő aktiválódik.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

A rendszer elindította az eseményt, és néhány pillanat múlva Event Grid küldi az eseményt az Azure Storage-várólistába. ***A Azure functions következő ütemezett futtatása után***tekintse meg a másodlagos tároló konfigurációs beállításait, és ellenőrizze, hogy tartalmazza-e a frissített kulcs értékét az elsődleges tárolóból.

> [!NOTE]
> A tesztelés és a hibaelhárítás során a [Azure functions manuálisan](/azure/azure-functions/functions-manually-run-non-http) is aktiválhatja az ütemezett időzítő-triggerre való várakozás nélkül.

Miután ellenőrizte, hogy a biztonsági mentési függvény sikeresen lefutott, láthatja, hogy a kulcs már szerepel a másodlagos tárolóban.

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>Hibaelhárítás

Ha nem látja az új beállítást a másodlagos tárolóban:

- Győződjön meg arról, hogy a biztonsági mentési függvény aktiválva lett, ***miután*** létrehozta a beállítást az elsődleges tárolóban.
- Lehetséges, hogy Event Grid nem tudta időben elküldeni az esemény-értesítést a Storage-várólistába. Ellenőrizze, hogy a tárolási üzenetsor továbbra is tartalmazza-e az eseményről szóló értesítést az elsődleges tárolóból, és ha igen, aktiválja újra a biztonsági mentési függvényt.
- Az esetleges hibákkal és figyelmeztetésekkel kapcsolatban keresse meg [Azure functions naplókat](/azure/azure-functions/functions-create-scheduled-function#test-the-function) .
- A [Azure Portal](/azure/azure-functions/functions-how-to-use-azure-function-app-settings#get-started-in-the-azure-portal) használatával győződjön meg arról, hogy az Azure függvényalkalmazás helyes értékeket tartalmaz azon Alkalmazásbeállítások számára, amelyeket az Azure functions olvasni próbál.
- Az [Azure Application Insights](/azure/azure-functions/functions-monitoring?tabs=cmd) használatával beállíthatja a Azure functions figyelését és riasztását is. 


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha azt tervezi, hogy folytatja az alkalmazás konfigurálását és az esemény-előfizetést, ne törölje az ebben a cikkben létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő paranccsal törölheti a cikkben létrehozott erőforrásokat.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>További lépések

Most, hogy már tudja, hogyan állíthatja be a kulcs értékeinek automatikus biztonsági mentését, további információkat tudhat meg arról, hogyan növelheti az alkalmazás földrajzi rugalmasságát:

- [Rugalmasság és vészhelyreállítás](concept-disaster-recovery.md)
