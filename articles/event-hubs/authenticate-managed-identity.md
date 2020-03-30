---
title: Felügyelt identitás hitelesítése az Azure Active Directoryval
description: Ez a cikk az Azure Active Directoryval felügyelt identitások azure-beli Eseményközpont-erőforrások eléréséhez való hitelesítéséről nyújt tájékoztatást
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: 672b663a9cab72d465ea00e0a5ade364eadbf64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251535"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Felügyelt identitás hitelesítése az Azure Active Directoryval az Event Hubs-erőforrások eléréséhez
Az Azure Event Hubs támogatja az Azure Active Directory (Azure AD) hitelesítését [az Azure-erőforrások felügyelt identitásaival.](../active-directory/managed-identities-azure-resources/overview.md) Az Azure-erőforrások felügyelt identitásai engedélyezhetik az Event Hubs-erőforrásokhoz való hozzáférést az Azure Virtuális gépeken (VM-ekben), a Függvényalkalmazásokban, a virtuálisgép-méretezési csoportokban és más szolgáltatásokban futó alkalmazások Azure AD-hitelesítő adatainak használatával. Felügyelt identitások használatával az Azure-erőforrások és az Azure AD-hitelesítés, elkerülheti a hitelesítő adatok tárolása a felhőben futó alkalmazások használatával.

Ez a cikk bemutatja, hogyan engedélyezheti a hozzáférést egy eseményközponthoz egy Azure virtuális gép felügyelt identitás használatával.

## <a name="enable-managed-identities-on-a-vm"></a>Felügyelt identitások engedélyezése virtuális számítógépen
Mielőtt az Azure Resources felügyelt identitások használatával engedélyezheti az Event Hubs-erőforrásokat a virtuális gépről, először engedélyeznie kell az Azure Resources felügyelt identitásait a virtuális gépen. Ha tudni szeretné, hogyan engedélyezheti az Azure Resources felügyelt identitásait, tekintse meg az alábbi cikkek egyikét:

- [Azure-portál](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablon](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager ügyféltárak](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Engedélyek megadása felügyelt identitáshoz az Azure AD-ben
Ha engedélyezni szeretne egy kérelmet az Event Hubs szolgáltatásnak egy felügyelt identitásból az alkalmazásban, először konfigurálja a felügyelt identitás Szerepköralapú hozzáférés-vezérlési (RBAC) beállításait. Az Azure Event Hubs olyan RBAC-szerepköröket határoz meg, amelyek az Event Hubs-ból történő küldéshez és olvasáshoz szükséges engedélyeket foglalják magukban. Ha az RBAC szerepkör egy felügyelt identitáshoz van rendelve, a felügyelt identitás hozzáférést kap az Event Hubs-adatokhoz a megfelelő hatókörön.

Az RBAC-szerepkörök hozzárendeléséről a Hitelesítés az Azure Active Directoryval című témakörben talál további információt [az Event Hubs-erőforrásokhoz való hozzáférésért.](authorize-access-azure-active-directory.md)

## <a name="use-event-hubs-with-managed-identities"></a>Az Event Hubs használata felügyelt identitásokkal
Az Event Hubs felügyelt identitásokkal való használatához hozzá kell rendelnie a szerepkört és a megfelelő hatókört. Az ebben a szakaszban található eljárás egy egyszerű alkalmazást használ, amely felügyelt identitás alatt fut, és hozzáfér az Event Hubs erőforrásokhoz.

Itt az [Azure App Service-ben](https://azure.microsoft.com/services/app-service/)üzemeltetett mintawebalkalmazást használjuk. A webalkalmazások létrehozásának lépésenkénti útmutatója: [ASP.NET Core webalkalmazás létrehozása az Azure-ban](../app-service/app-service-web-get-started-dotnet.md)

Az alkalmazás létrehozása után kövesse az alábbi lépéseket: 

1. Nyissa meg a **Beállítások lapot,** és válassza **az Identitás**lehetőséget. 
1. Válassza ki a bekapcsolt **állapotot.** **Status** 
1. A beállítás mentéséhez kattintson a **Mentés** gombra. 

    ![Felügyelt identitás egy webalkalmazáshoz](./media/authenticate-managed-identity/identity-web-app.png)

Miután engedélyezte ezt a beállítást, egy új szolgáltatásidentitás jön létre az Azure Active Directoryban (Azure AD), és az App Service-gazdagépkonfigurálása.

Most rendelje hozzá ezt a szolgáltatásidentitást az Event Hubs-erőforrások szükséges hatókörében lévő szerepkörhöz.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>RBAC-szerepkörök hozzárendelése az Azure Portalhasználatával
Ha szerepkört szeretne hozzárendelni az Event Hubs-erőforrásokhoz, keresse meg az adott erőforrást az Azure Portalon. Jelenítse meg az erőforrás hozzáférés-vezérlési (IAM) beállításait, és kövesse az alábbi utasításokat a szerepkör-hozzárendelések kezeléséhez:

> [!NOTE]
> A következő lépések egy szolgáltatásidentitás-szerepkört rendelnek az Event Hubs névteréhez. Ugyanazokat a lépéseket követve rendeljen hozzá egy szerepkör hatókörét bármely Event Hubs erőforráshoz. 

1. Az Azure Portalon keresse meg az Event Hubs névterét, és jelenítse meg a névtér **áttekintése.** 
1. Válassza a **hozzáférés-vezérlés (IAM)** lehetőséget a bal oldali menüben az eseményközpont hozzáférés-vezérlési beállításainak megjelenítéséhez.
1.  A **szerepkör-hozzárendelések** listájának megtekintéséhez válassza a Szerepkör-hozzárendelések lapot.
3.  Új szerepkör hozzáadásához válassza a **Hozzáadás** lehetőséget.
4.  A **Szerepkör-hozzárendelés hozzáadása** lapon jelölje ki a hozzárendelni kívánt Eseményközpontok szerepköröket. Ezután keresse meg a szerepkör hozzárendeléséhez regisztrált szolgáltatásidentitást.
    
    ![Szerepkör-hozzárendelés hozzáadása lap](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  Kattintson a **Mentés** gombra. A szerepkörhöz rendelt identitás megjelenik a szerepkör alatt. Az alábbi képen például látható, hogy a szolgáltatásidentitás rendelkezik az Event Hubs Data tulajdonosa.
    
    ![Szerepkörhöz rendelt identitás](./media/authenticate-managed-identity/role-assigned.png)

Miután hozzárendelte a szerepkört, a webalkalmazás hozzáférhet az Event Hubs erőforrásokhoz a megadott hatókör alatt. 

### <a name="test-the-web-application"></a>A webalkalmazás tesztelése
1. Hozzon létre egy Eseményközpontok névtere és egy eseményközpont. 
2. Telepítse a webalkalmazást az Azure-ba. Tekintse meg a következő többlapos szakaszban a githubon a webalkalmazásra mutató hivatkozásokat. 
3. Győződjön meg arról, hogy a SendReceive.aspx van beállítva a webalkalmazás alapértelmezett dokumentumaként. 
3. Engedélyezze az **identitást** a webalkalmazáshoz. 
4. Rendelje hozzá ezt az identitást az **Event Hubs-adatközpontok** adatközpont-szerepkörhöz a névtér vagy az eseményközpont szintjén. 
5. Futtassa a webalkalmazást, írja be a névtér nevét és az eseményközpont nevét, egy üzenetet, és válassza a **Küldés gombot.** Az esemény fogadásához válassza a **Fogadás**lehetőséget. 

#### <a name="azuremessagingeventhubs-latest"></a>[Azure.Messaging.EventHubs (legújabb)](#tab/latest)
Most már elindíthatja a webalkalmazást, és a böngészőt a minta aspx oldalra irányíthatja. A [GitHub-tárházban](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)megtalálhatja az Event Hubs-erőforrásokból adatokat küldő és fogadó mintawebalkalmazást.

Telepítse a legújabb csomagot a [NuGet-ből,](https://www.nuget.org/packages/Azure.Messaging.EventHubs/)és kezdje el elküldeni az eseményeket az Event **HubProducerClient** használatával, és fogadja az eseményeket **az EventHubConsumerClient használatával.** 

> [!NOTE]
> Egy Java-minta, amely egy felügyelt identitás takaregy eseményközpont események közzétételére, [lásd: Események közzététele az Azure-identitásminta a GitHubon.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)

```csharp
protected async void btnSend_Click(object sender, EventArgs e)
{
    await using (EventHubProducerClient producerClient = new EventHubProducerClient(txtNamespace.Text, txtEventHub.Text, new DefaultAzureCredential()))
    {
        // create a batch
        using (EventDataBatch eventBatch = await producerClient.CreateBatchAsync())
        {

            // add events to the batch. only one in this case. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes(txtData.Text)));

            // send the batch to the event hub
            await producerClient.SendAsync(eventBatch);
        }

        txtOutput.Text = $"{DateTime.Now} - SENT{Environment.NewLine}{txtOutput.Text}";
    }
}
protected async void btnReceive_Click(object sender, EventArgs e)
{
    await using (var consumerClient = new EventHubConsumerClient(EventHubConsumerClient.DefaultConsumerGroupName, $"{txtNamespace.Text}.servicebus.windows.net", txtEventHub.Text, new DefaultAzureCredential()))
    {
        int eventsRead = 0;
        try
        {
            using CancellationTokenSource cancellationSource = new CancellationTokenSource();
            cancellationSource.CancelAfter(TimeSpan.FromSeconds(5));

            await foreach (PartitionEvent partitionEvent in consumerClient.ReadEventsAsync(cancellationSource.Token))
            {
                txtOutput.Text = $"Event Read: { Encoding.UTF8.GetString(partitionEvent.Data.Body.ToArray()) }{ Environment.NewLine}" + txtOutput.Text;
                eventsRead++;
            }
        }
        catch (TaskCanceledException ex)
        {
            txtOutput.Text = $"Number of events read: {eventsRead}{ Environment.NewLine}" + txtOutput.Text;
        }
    }
}
```

#### <a name="microsoftazureeventhubs-legacy"></a>[Microsoft.Azure.EventHubs (örökölt)](#tab/old)
Most már elindíthatja a webalkalmazást, és a böngészőt a minta aspx oldalra irányíthatja. A [GitHub-tárházban](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp)megtalálhatja az Event Hubs-erőforrásokból adatokat küldő és fogadó mintawebalkalmazást.

Telepítse a legújabb csomagot a [NuGet-ből,](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/)és kezdjen el adatokat küldeni és fogadni az EventhubClient programból az EventHubClient használatával, ahogy az a következő kódban látható: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```
---

## <a name="event-hubs-for-kafka"></a>A Kafkához készült Event Hubs
Az Apache Kafka-alkalmazásokkal üzeneteket küldhet és fogadhat az Azure Event Hubs-ból felügyelt oauth identitás használatával. Tekintse meg a következő mintát a GitHubon: [A Kafka eseményközpontjai – üzenetek küldése és fogadása felügyelt identitással OAuth.](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)

## <a name="samples"></a>Példák
- **Az Azure.Messaging.EventHubs-minták**
    - [.NET](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Microsoft.Azure.EventHubs mintákat](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Ezek a minták a régi **Microsoft.Azure.EventHubs-könyvtárat** használják, de könnyedén frissítheti azt a legújabb **Azure.Messaging.EventHubs-könyvtár** használatával. Ha át szeretné helyezni a mintát a régi könyvtár használatából az újkönyvtárba, olvassa el a [Microsoft.Azure.EventHubs-ról az Azure.Messaging.EventHubs szolgáltatásba való áttelepítés útmutatóját.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/migration-guide-from-v4.md)
    Ez a minta frissítve lett a legújabb **Azure.Messaging.EventHubs-könyvtár** használatával.
- [A Kafka eseményközpontjai – üzenetek küldése és fogadása felügyelt identitással OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth/java/managedidentity)


## <a name="next-steps"></a>További lépések
- Tekintse meg a következő cikket az Azure-erőforrások felügyelt identitásairól: [Mi az Azure-erőforrások felügyelt identitásai?](../active-directory/managed-identities-azure-resources/overview.md)
- Lásd a következő kapcsolódó cikkeket:
    - [Az Azure-eseményközpontoknak az Azure Active Directory használatával történő hitelesítése](authenticate-application.md)
    - [Az Azure Event Hubs-ra irányuló kérelmek hitelesítése megosztott hozzáférésű aláírásokkal](authenticate-shared-access-signature.md)
    - [Hozzáférés engedélyezése az Event Hubs-erőforrásokhoz az Azure Active Directory használatával](authorize-access-azure-active-directory.md)
    - [Hozzáférés engedélyezése az Event Hubs-erőforrásokhoz megosztott hozzáférési aláírásokkal](authorize-access-shared-access-signature.md)