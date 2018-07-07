---
title: Felügyelt Felügyeltszolgáltatás-identitás az Azure Event Hubs előzetes verziójában |} A Microsoft Docs
description: Felügyelt Szolgáltatásidentitások használata az Azure Event hubs szolgáltatással
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: sethm
ms.openlocfilehash: abff3f715a1fccba172147f02b83f7209f87cf9e
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902516"
---
# <a name="managed-service-identity-preview"></a>Felügyeltszolgáltatás-identitás (előzetes verzió)

A Felügyeltszolgáltatás-identitás (MSI) közötti Azure-beli funkciója lehetővé teszi, hogy a központi telepítést, amely alatt az alkalmazás kódja fut társított biztonságos identitás létrehozása. Ezután társíthatja az identitásukat hozzáférés-vezérlési szerepkörökkel, amelyek egyéni engedélyeket adott, az alkalmazásnak szüksége van az Azure erőforrások eléréséhez. 

Az MSI-vel az Azure platform kezeli a futtatókörnyezet identitást. Nem kell tárolja és védi a tárelérési kulcsok az alkalmazáskód vagy a konfiguráció, vagy maga identitását, vagy az erőforrások eléréséhez szükséges. Engedélyezett MSI-támogatással rendelkező Azure App Service-alkalmazáshoz vagy virtuális gépen futó alkalmazás az Event Hubs ügyfél nem kell kezelni a SAS-szabályok és a kulcsokat, vagy bármely más hozzáférési jogkivonatok. Az ügyfélalkalmazásnak csupán az Event Hubs-névtér végpont címe. Amikor az alkalmazás csatlakozik, az Event Hubs az MSI-környezet van kötve az ügyfél egy műveletben, például a cikk későbbi részében látható.

Miután társítva a felügyeltszolgáltatás-identitás, Event Hubs-ügyfél az összes jogosult műveletek hajthatók végre. Az engedély megadása társít egy olyan MSI Csomaghoz, az Event Hubs-szerepkörökhöz. 

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs szerepkörök és engedélyek

A kezdeti előzetes kiadás csak a felügyeltszolgáltatás-identitás adhat hozzá a Event Hubs-névtér, amely az identitás a névtérben lévő összes entitáshoz teljes hozzáférést biztosít a "Tulajdonos" vagy "Közreműködő" szerepköröket. Azonban a névtér topológia módosító műveletekre kezdetben felügyeleti támogatott azonban csak az Azure Resource Manager és a nem a natív Event hubs szolgáltatás REST-felügyeleti felületén keresztül. Ez a támogatás azt is jelenti, hogy nem használható a .NET-keretrendszer ügyfél [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) felügyeltszolgáltatás-identitás-objektummal. 
 
## <a name="use-event-hubs-with-a-managed-service-identity"></a>Az Event Hubs használata a Felügyeltszolgáltatás-identitás

Az alábbi szakasz létrehozása és üzembe helyezünk egy mintaalkalmazást, amely fut a felügyeltszolgáltatás-identitás, hogyan identitás hozzáférést Event Hubs-névtér és az alkalmazás hogyan kapcsolódik az event hubs használatával, amely a szükséges lépéseket ismerteti. identitás.

Ez a bevezető ismerteti a lévő üzemeltetett webalkalmazásban [Azure App Service](https://azure.microsoft.com/services/app-service/). Egy virtuális gép által üzemeltetett alkalmazás számára szükséges lépések hasonlóak.

### <a name="create-an-app-service-web-application"></a>Hozzon létre egy App Service-webalkalmazás

Az első lépés az App Service ASP.NET-alkalmazás létrehozása. Ha még nem ismeri az ehhez az Azure-ban, hajtsa végre a [Ez az útmutató](../app-service/app-service-web-get-started-dotnet-framework.md). Azonban helyett, ahogyan az oktatóprogram MVC alkalmazás létrehozása, hozzon létre egy Web Forms-alkalmazást.

### <a name="set-up-the-managed-service-identity"></a>A felügyeltszolgáltatás-identitás beállítása

Miután létrehozta az alkalmazást, nyissa meg az újonnan létrehozott webalkalmazás az Azure Portalon (az útmutató is látható), majd nyissa meg a **Felügyeltszolgáltatás-identitás** lapon, és engedélyezze a szolgáltatást: 

![](./media/event-hubs-managed-service-identity/msi1.png)
 
Miután engedélyezte a funkciót, egy új felügyeltszolgáltatás-identitás létrehozása az Azure Active Directoryban, és az App Service-ben gazdagépen konfigurált.

### <a name="create-a-new-event-hubs-namespace"></a>Hozzon létre egy új Event Hubs-névtér

Ezután [Event Hubs-névtér létrehozása](event-hubs-create.md) MSI előzetes támogatással rendelkezik az Azure-régiók egyikében: **USA keleti régiójában**, **USA keleti régiója 2**, vagy **Nyugat-Európa**. 

Keresse meg a névtér **hozzáférés-vezérlés (IAM)** lapon a portálon, és kattintson a **Hozzáadás** a felügyeltszolgáltatás-identitás hozzáadása a **tulajdonosa** szerepkör. Ehhez keresse meg a webalkalmazás nevére a **engedélyek hozzáadása** panel **kiválasztása** mezőben, majd kattintson a bejegyzésre. Ezután kattintson a **Save** (Mentés) gombra.

![](./media/event-hubs-managed-service-identity/msi2.png)
 
A felügyeltszolgáltatás-identitás, a webes alkalmazás most már hozzáfér az Event Hubs-névtér és az event hubs korábban hozott létre. 

### <a name="run-the-app"></a>Az alkalmazás futtatása

Most úgy módosítja az alapértelmezett oldalt, az ASP.NET alkalmazás hozott létre. Használhatja a webes alkalmazás kódot az [GitHub-adattárban](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

>[!NOTE] 
> Bár az MSI-funkció előzetes verzióban érhető el, ügyeljen arra, hogy a [előzetes verzióját, a Service Bus library](https://www.nuget.org/packages/WindowsAzure.ServiceBus/4.2.2-preview) az új API-k eléréséhez. 

Miután az alkalmazás elindításához, a böngésző EventHubsMSIDemo.aspx mutat. Másik lehetőségként állítsa be a kezdőlap. A kód a EventHubsMSIDemo.aspx.cs fájlban található. Az eredmény néhány számbeviteli mezők, valamint a minimális webalkalmazás **küldése** és **kap** küldése vagy események fogadása az Event Hubs csatlakozó gombok. 

Megjegyzés: a [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objektum inicializálása. Helyett a szolgáltatóval közös hozzáférési jogkivonat (SAS-) token, a kódot hoz létre a felügyeltszolgáltatás-identitását a jogkivonat-szolgáltatót a `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` hívja. Ezért nincsenek nincs megőrizheti és felhasználhatja a titkos kulcsok. A jogkivonat-szolgáltatót, amely egyszerűbb, mint a SAS használatával modell automatikusan kezeli, a folyamat az Event Hubs és a hitelesítési kézfogás felügyeltszolgáltatás-identitás környezet.

Miután elvégezte ezeket a módosításokat, közzététel, és futtathatja az alkalmazást. Egy egyszerű módja annak, hogy a helyes közzétételi adatokat, hogy töltse le és importálja a Visual Studióban egy közzétételi profilt:

![](./media/event-hubs-managed-service-identity/msi3.png)
 
Üzeneteket küldeni vagy fogadni, adja meg a névtér nevét és a létrehozott entitás nevét, majd kattintson **küldése** vagy **kap**. 
 
Vegye figyelembe, hogy a felügyeltszolgáltatás-identitás csak működik az Azure-környezeten belül, és csak az App Service-környezet, amelyben be vannak állítva ezek. Vegye figyelembe, hogy felügyelt szolgáltatásidentitások sem működik együtt az App Service üzembe helyezési pontok jelenleg is.

## <a name="next-steps"></a>További lépések

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

* Bevezetés az [Event Hubs használatába oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Az Event Hubs szolgáltatás díjszabását.](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)