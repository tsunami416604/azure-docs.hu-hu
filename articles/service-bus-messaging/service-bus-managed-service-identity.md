---
title: Felügyelt identitások az Azure-erőforrások Azure Service Bus-előzetes verzió |} A Microsoft Docs
description: Az Azure-erőforrások Azure Service Bus felügyelt identitásokat használ
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/01/2018
ms.author: spelluru
ms.openlocfilehash: 25d2db5dcf3979341fc104643f7178047c29483b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842832"
---
# <a name="managed-identities-for-azure-resources-with-service-bus"></a>Felügyelt identitások az Azure Service Bus-erőforrások 

[Felügyelt identitások az Azure-erőforrások](../active-directory/managed-identities-azure-resources/overview.md) több Azure-funkció, amely lehetővé teszi, hogy a központi telepítést, amely alatt az alkalmazás kódja fut társított biztonságos identitás létrehozása. Ezután társíthatja az identitásukat hozzáférés-vezérlési szerepkörökkel, amelyek egyéni engedélyeket adott, az alkalmazásnak szüksége van az Azure erőforrások eléréséhez.

A felügyelt identitásokból az Azure platform kezeli a futtatókörnyezet identitást. Nem kell tárolja és védi a tárelérési kulcsok az alkalmazáskód vagy a konfiguráció, vagy maga identitását, vagy az erőforrások eléréséhez szükséges. A Service Bus belüli Azure App Service-alkalmazáshoz vagy virtuális gép engedélyezett futó ügyfélalkalmazás felügyelt entitások Azure-támogatási SAS-szabályok és a kulcsokat, vagy bármely más hozzáférési jogkivonatok kezeléséhez nem szükséges erőforrások. Az ügyfélalkalmazásnak csupán a végpont címe a Service Bus Messaging-névteret. Amikor az alkalmazás csatlakozik, a Service Bus a felügyelt entitás környezet egy műveletben, amely a cikk későbbi részében egy példa látható az ügyfél van kötve. Miután társított egy felügyelt identitás, a Service Bus-ügyfélalkalmazást minden engedélyezett műveletek teheti meg. Engedélyt adott felügyelt entitással, a Service Bus-szerepkörökhöz való társításával. 

## <a name="service-bus-roles-and-permissions"></a>A Service Bus-szerepkörök és engedélyek

Hozzáadhat egy felügyelt identitás csak a "Tulajdonos" vagy "Közreműködő" Service Bus-névtér-szerepkörökhöz. Az identitás a névtérben lévő összes entitáshoz teljes hozzáférést ad. Azonban a névtér topológia módosító műveletekre kezdetben felügyeleti támogatott azonban csak az Azure Resource Manager. Nem áll a natív Service Bus REST-felügyeleti felületén keresztül. Ez a támogatás azt is jelenti, hogy nem használható a .NET-keretrendszer ügyfél [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) egy felügyelt identitás-objektummal.

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>A Service Bus felügyelt identitások az Azure-erőforrások

A következő szakasz azt ismerteti, hogyan létrehozása és üzembe helyezünk egy mintaalkalmazást, amely egy felügyelt identitás, hogyan identitás hozzáférést egy Service Bus Messaging-névteret, és hogyan kommunikál a Service Bus-entitások használatával az alkalmazás fut. az identitásukat.

Ez a bevezető ismerteti a lévő üzemeltetett webalkalmazásban [Azure App Service](https://azure.microsoft.com/services/app-service/). Egy virtuális gép által üzemeltetett alkalmazás számára szükséges lépések hasonlóak.

### <a name="create-an-app-service-web-application"></a>Hozzon létre egy App Service-webalkalmazás

Az első lépés az App Service ASP.NET-alkalmazás létrehozása. Ha még nem ismeri az ehhez az Azure-ban, hajtsa végre a [Ez az útmutató](../app-service/app-service-web-get-started-dotnet-framework.md). Azonban helyett, ahogyan az oktatóprogram MVC alkalmazás létrehozása, hozzon létre egy Web Forms-alkalmazást.

### <a name="set-up-the-managed-identity"></a>A felügyelt identitás beállítása

Miután létrehozta az alkalmazást, nyissa meg az újonnan létrehozott webalkalmazás az Azure Portalon (az útmutató is látható), majd nyissa meg a **Felügyeltszolgáltatás-identitás** lapon, és engedélyezze a szolgáltatást: 

![](./media/service-bus-managed-service-identity/msi1.png)

Miután engedélyezte a funkciót, egy új felügyeltszolgáltatás-identitás létrehozása az Azure Active Directoryban, és az App Service-ben gazdagépen konfigurált.

### <a name="create-a-new-service-bus-messaging-namespace"></a>Hozzon létre egy új Service Bus Messaging-névteret

Ezután [hozzon létre egy Service Bus üzenetkezelési névteret](service-bus-create-namespace-portal.md) RBAC előzetes támogató Azure-régiók egyikében: **USA keleti régiójában**, **USA keleti régiója 2**, vagy **Nyugat-Európa** . 

Keresse meg a névtér **hozzáférés-vezérlés (IAM)** lapon a portálon, és kattintson a **szerepkör-hozzárendelés hozzáadása** felügyelt identitásnak hozzáadása a **tulajdonosa** szerepkör. Ehhez keresse meg a webalkalmazás nevére a **engedélyek hozzáadása** panel **kiválasztása** mezőben, majd kattintson a bejegyzésre. Ezután kattintson a **Save** (Mentés) gombra.

A webalkalmazás felügyelt identitás ezentúl hozzáférhet a Service Bus-névteret, és az üzenetsorba korábban hozott létre. 

### <a name="run-the-app"></a>Az alkalmazás futtatása

Most módosítsa az alapértelmezett oldalt, az ASP.NET alkalmazás hozott létre. Használhatja a webes alkalmazás kódot az [GitHub-adattárban](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

A Default.aspx oldal a kezdőlapja. A kód a Default.aspx.cs fájlban található. Az eredmény néhány számbeviteli mezők, valamint a minimális webalkalmazás **küldése** és **kap** gombot, amely csatlakozik a Service Bus vagy üzeneteket küldeni vagy fogadni a.

Megjegyzés: a [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objektum inicializálása. Helyett a szolgáltatóval közös hozzáférési jogkivonat (SAS-) token, a kódot hoz létre a felügyelt identitás a jogkivonat-szolgáltatót a `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` hívja. Ezért nincsenek nincs megőrizheti és felhasználhatja a titkos kulcsok. A jogkivonat-szolgáltatót automatikusan kezeli a folyamatot a Service Bus és a hitelesítési kézfogás felügyelt identitás-környezet. Egy egyszerűbb modell, mint a SAS használatával.

Miután végrehajtotta ezeket a módosításokat, közzététel, és futtathatja az alkalmazást. Szerezheti be a helyes közzétételi adatok könnyen letöltésével és a egy közzétételi profilt a Visual Studióban, majd importálásával:

![](./media/service-bus-managed-service-identity/msi3.png)
 
Üzeneteket küldeni vagy fogadni, adja meg a névtér nevét és a létrehozott entitás nevével. Kattintson bármelyik **küldése** vagy **kap**.


> [!NOTE]
> - A felügyelt identitás működik csak belül az Azure-környezethez, az App services, Azure virtuális gépekhez és méretezési csoportok. A .NET-alkalmazásokban a Microsoft.Azure.Services.AppAuthentication könyvtárat, amelyet a Service Bus NuGet-csomagot használ, absztrakciós biztosít a protokoll, és támogatja a helyi fejlesztési környezetet biztosít. Ez a kódtár lehetővé teszi tesztelheti a kódját a helyi fejlesztői gépen, a felhasználói fiókot a Visual Studio, az Azure CLI 2.0 vagy az Active Directory integrált hitelesítést használ. További információ a helyi fejlesztési lehetőségek az ebben a könyvtárban,: [szolgáltatások közötti hitelesítés a .NET használatával az Azure Key Vault](../key-vault/service-to-service-authentication.md).  
> 
> - Felügyelt identitások jelenleg nem működik az App Service üzembe helyezési pontok.

## <a name="next-steps"></a>További lépések

További információ a Service Bus-üzenetkezelés, tekintse meg a következő témaköröket:

* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)