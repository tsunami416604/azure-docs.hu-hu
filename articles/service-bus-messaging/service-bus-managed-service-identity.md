---
title: Felügyelt Felügyeltszolgáltatás-identitás az Azure Service Bus előzetes verziójában |} A Microsoft Docs
description: Az Azure Service Bus felügyelt Szolgáltatásidentitások használata
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
ms.date: 08/01/2018
ms.author: spelluru
ms.openlocfilehash: e886089bbceff5fc1963044c4c1f2f7a012ea2a2
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697730"
---
# <a name="managed-service-identity-preview"></a>Felügyeltszolgáltatás-identitás (előzetes verzió)

A Felügyeltszolgáltatás-identitás (MSI) közötti Azure-beli funkciója lehetővé teszi, hogy a központi telepítést, amely alatt az alkalmazás kódja fut társított biztonságos identitás létrehozása. Ezután társíthatja az identitásukat hozzáférés-vezérlési szerepkörökkel, amelyek egyéni engedélyeket adott, az alkalmazásnak szüksége van az Azure erőforrások eléréséhez.

Az MSI-vel az Azure platform kezeli a futtatókörnyezet identitást. Nem kell tárolja és védi a tárelérési kulcsok az alkalmazáskód vagy a konfiguráció, vagy maga identitását, vagy az erőforrások eléréséhez szükséges. Egy engedélyezett MSI-támogatással rendelkező Azure App Service-alkalmazáshoz vagy virtuális gépen futó Service Bus-ügyfélalkalmazás nem kell kezelni a SAS-szabályok és a kulcsokat, vagy bármely más hozzáférési jogkivonatok. Az ügyfélalkalmazásnak csupán a végpont címe a Service Bus Messaging-névteret. Amikor az alkalmazás csatlakozik, a Service Bus az MSI-környezet van kötve az ügyfél egy műveletben, például a cikk későbbi részében látható. 

Miután társítva a felügyeltszolgáltatás-identitás, a Service Bus-ügyfélalkalmazást összes jogosult műveletek hajthatók végre. Az engedély megadása társít egy olyan MSI Csomaghoz, a Service Bus-szerepkörökhöz. 

## <a name="service-bus-roles-and-permissions"></a>A Service Bus-szerepkörök és engedélyek

A kezdeti előzetes kiadás csak a felügyeltszolgáltatás-identitás adhat hozzá a Service Bus-névtér, amely az identitás a névtérben lévő összes entitáshoz teljes hozzáférést biztosít a "Tulajdonos" vagy "Közreműködő" szerepköröket. Azonban a névtér topológia módosító műveletekre kezdetben felügyeleti támogatott azonban csak az Azure Resource Manager és a nem a natív Service Bus REST-felügyeleti felületén keresztül. Ez a támogatás azt is jelenti, hogy nem használható a .NET-keretrendszer ügyfél [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) felügyeltszolgáltatás-identitás-objektummal.

## <a name="use-service-bus-with-a-managed-service-identity"></a>A Service Bus használata a Felügyeltszolgáltatás-identitás

A következő szakasz azt ismerteti, hogyan létrehozása és üzembe helyezünk egy mintaalkalmazást, amely alatt a felügyeltszolgáltatás-identitás, hogyan identitás hozzáférést egy Service Bus Messaging-névteret, és hogyan kommunikál a Service Bus az alkalmazás fut. Ezzel az identitással entitások.

Ez a bevezető ismerteti a lévő üzemeltetett webalkalmazásban [Azure App Service](https://azure.microsoft.com/services/app-service/). Egy virtuális gép által üzemeltetett alkalmazás számára szükséges lépések hasonlóak.

### <a name="create-an-app-service-web-application"></a>Hozzon létre egy App Service-webalkalmazás

Az első lépés az App Service ASP.NET-alkalmazás létrehozása. Ha még nem ismeri az ehhez az Azure-ban, hajtsa végre a [Ez az útmutató](../app-service/app-service-web-get-started-dotnet-framework.md). Azonban helyett, ahogyan az oktatóprogram MVC alkalmazás létrehozása, hozzon létre egy Web Forms-alkalmazást.

### <a name="set-up-the-managed-service-identity"></a>A felügyeltszolgáltatás-identitás beállítása

Miután létrehozta az alkalmazást, nyissa meg az újonnan létrehozott webalkalmazás az Azure Portalon (az útmutató is látható), majd nyissa meg a **Felügyeltszolgáltatás-identitás** lapon, és engedélyezze a szolgáltatást: 

![](./media/service-bus-managed-service-identity/msi1.png)

Miután engedélyezte a funkciót, egy új felügyeltszolgáltatás-identitás létrehozása az Azure Active Directoryban, és az App Service-ben gazdagépen konfigurált.

### <a name="create-a-new-service-bus-messaging-namespace"></a>Hozzon létre egy új Service Bus Messaging-névteret

Ezután [hozzon létre egy Service Bus üzenetkezelési névteret](service-bus-create-namespace-portal.md) RBAC előzetes támogató Azure-régiók egyikében: **USA keleti régiójában**, **USA keleti régiója 2**, vagy **Nyugat-Európa** . 

Keresse meg a névtér **hozzáférés-vezérlés (IAM)** lapon a portálon, és kattintson a **Hozzáadás** a felügyeltszolgáltatás-identitás hozzáadása a **tulajdonosa** szerepkör. Ehhez keresse meg a webalkalmazás nevére a **engedélyek hozzáadása** panel **kiválasztása** mezőben, majd kattintson a bejegyzésre. Ezután kattintson a **Save** (Mentés) gombra.

![](./media/service-bus-managed-service-identity/msi2.png)
 
A webalkalmazás felügyeltszolgáltatás-identitás ezentúl hozzáférhet a Service Bus-névteret, és az üzenetsorba korábban hozott létre. 

### <a name="run-the-app"></a>Az alkalmazás futtatása

Most módosítsa az alapértelmezett oldalt, az ASP.NET alkalmazás hozott létre. Használhatja a webes alkalmazás kódot az [GitHub-adattárban](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

A Default.aspx oldal a kezdőlapja. A kód a Default.aspx.cs fájlban található. Az eredmény néhány számbeviteli mezők, valamint a minimális webalkalmazás **küldése** és **kap** gombot, amely csatlakozik a Service Bus vagy üzeneteket küldeni vagy fogadni a.

Megjegyzés: a [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objektum inicializálása. Helyett a szolgáltatóval közös hozzáférési jogkivonat (SAS-) token, a kódot hoz létre a felügyeltszolgáltatás-identitását a jogkivonat-szolgáltatót a `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` hívja. Ezért nincsenek nincs megőrizheti és felhasználhatja a titkos kulcsok. A folyamat a felügyeltszolgáltatás-identitás környezet Service Bus és a hitelesítési kézfogás a jogkivonat-szolgáltatót, amely egyszerűbb, mint a SAS használatával modell automatikusan kezeli.

Miután elvégezte ezeket a módosításokat, közzététel, és futtathatja az alkalmazást. Egy egyszerű módja annak, hogy a helyes közzétételi adatokat, hogy töltse le és importálja a Visual Studióban egy közzétételi profilt:

![](./media/service-bus-managed-service-identity/msi3.png)
 
Üzeneteket küldeni vagy fogadni, adja meg a névtér nevét és a létrehozott entitás nevét, majd kattintson **küldése** vagy **kap**.


> [!NOTE]
> - A felügyeltszolgáltatás-identitás működik csak belül az Azure-környezethez, az App services, Azure virtuális gépekhez és méretezési csoportok. A .NET-alkalmazásokban a Microsoft.Azure.Services.AppAuthentication könyvtárat, amelyet a Service Bus NuGet-csomagot használ, absztrakciós biztosít a protokoll, és támogatja a helyi fejlesztési környezetet biztosít. Ez a kódtár lehetővé teszi tesztelheti a kódját a helyi fejlesztői gépen, a felhasználói fiókot a Visual Studio, az Azure CLI 2.0 vagy az Active Directory integrált hitelesítést használ. További információ a helyi fejlesztési lehetőségek az ebben a könyvtárban,: [szolgáltatások közötti hitelesítés a .NET használatával az Azure Key Vault](../key-vault/service-to-service-authentication.md).  
> 
> - Felügyelt szolgáltatásidentitások jelenleg nem működik az App Service üzembe helyezési pontok.

## <a name="next-steps"></a>További lépések

A Service Bus üzenetkezelésről az alábbi témakörökben találhat további információkat.

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)