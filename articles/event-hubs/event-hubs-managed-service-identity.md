---
title: Az Azure Event Hubs előzetes Szolgáltatásidentitás felügyelt |} Microsoft Docs
description: Felügyelt szolgáltatás-identitások használja az Azure Event Hubs
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2017
ms.author: sethm
ms.openlocfilehash: dd50e4f6ebc5fdf5496a5127fde20bd052087b59
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
ms.locfileid: "26783444"
---
# <a name="managed-service-identity-preview"></a>Felügyelt Szolgáltatásidentitás (előzetes verzió)

Felügyelt szolgáltatás identitásának (MSI) lehetővé teszi az Azure-határokon, amely lehetővé teszi a központi telepítést, amely alatt az alkalmazás kódjában fut. társított egy biztonságos azonosító létrehozása. Identitás majd társíthat egyéni engedélyeket az eléréséhez szükséges konkrét Azure-erőforrások az alkalmazást, a hozzáférés-vezérlési szerepkörtől. 

Az MSI-fájl az Azure platform kezeli a futásidejű identitást. Nem kell tárolni, és az alkalmazás kódja vagy konfiguráció, vagy maga identitását, vagy az erőforrások eléréséhez szükséges tárelérési kulcsok védelme. Az Event Hubs ügyfélalkalmazás Azure App Service alkalmazás belül, vagy a virtuális gépen futó engedélyezett MSI-támogatással rendelkező nem kell SAS szabályok és a kulcsok vagy más hozzáférési jogkivonatok kell kezelni. A végpont címét, az Event Hubs névtér csak kell az ügyfél alkalmazást. Ha összekapcsolja az alkalmazást, az Event Hubs az MSI környezetet az ügyfél egy műveletben, például a cikk későbbi részében látható van kötve.

Ha egy felügyelt partneridentitásával társított, Event Hubs-ügyfél az összes jogosult műveleteket hajthat végre. Az engedélyt az Event Hubs szerepkörök egy olyan MSI Csomaghoz rendeli. 

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs szerepköröket és engedélyeket

Kezdeti nyilvános előzetes csak a "Tulajdonos" vagy "Közreműködői" szerepkörök egy Event Hubs névtér, amely az identitás teljes hozzáférést a névtér összes entitásának a adhat hozzá egy felügyelt szolgáltatásidentitás. Azonban a névtér topológia módosítása olyan kezdetben felügyeleti támogatott azonban csak Azure Resource Manager és a nem a natív Event Hubs REST kiszolgálókezelési felületen segítségével. Ez a támogatás azt is jelenti, hogy nem használható a .NET-keretrendszer ügyfél [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) felügyelt szolgáltatásidentitás objektummal. 
 
## <a name="use-event-hubs-with-a-managed-service-identity"></a>Az Event Hubs egy felügyelt Szolgáltatásidentitás használni

A következő szakasz azt ismerteti, hogyan hozhat létre és telepíthet a mintaalkalmazás, amely felügyelt szolgáltatásidentitás, hogyan egy Event Hubs névtér identitás hozzáférést, és hogyan kommunikál az event hubs használatával, amely az alkalmazás fut. identitás.

Ez a bevezető ismerteti egy webalkalmazást [Azure App Service](https://azure.microsoft.com/services/app-service/). A virtuális gép által üzemeltetett alkalmazások szükséges lépések hasonlóak.

### <a name="create-an-app-service-web-application"></a>Az App Service-webalkalmazás létrehozása

Az első lépés, ha az App Service ASP.NET-alkalmazások. Ha még nem ismeri a ezzel az Azure-ban, hajtsa végre a [Ez az útmutató útmutató](../app-service/app-service-web-get-started-dotnet-framework.md). Ahogy az az oktatóprogram MVC alkalmazás létrehozása helyett azonban Web Forms-alkalmazás létrehozása.

### <a name="set-up-the-managed-service-identity"></a>Állítsa be a felügyelt azonosítója

Miután létrehozta az alkalmazást, nyissa meg az újonnan létrehozott webalkalmazás az Azure portálon (az útmutató is látható), majd keresse meg a **Szolgáltatásidentitás felügyelt** lapon, és engedélyezze a szolgáltatást: 

![](./media/event-hubs-managed-service-identity/msi1.png)
 
A szolgáltatás engedélyezése után egy új szolgáltatásidentitás létrehozása az Azure Active Directoryban, és az App Service-állomás konfigurált.

### <a name="create-a-new-event-hubs-namespace"></a>Az Event Hubs új névtér létrehozása

Tovább, [az Event Hubs-névtér létrehozása](event-hubs-create.md) egy Azure-régiókban, amelyek MSI preview támogatása: **amerikai keleti**, **amerikai keleti régiója 2**, vagy **Nyugat-Európában**. 

Keresse meg a névtér **hozzáférés-vezérlés (IAM)** a portál lapot, és kattintson a **Hozzáadás** hozzáadása a felügyelt identitását a **tulajdonos** szerepkör. Ehhez keresse meg a webalkalmazás nevét a **engedélyek hozzáadása** panel **válasszon** mezőben, majd kattintson a bejegyzést. Ezután kattintson a **Save** (Mentés) gombra.

![](./media/event-hubs-managed-service-identity/msi2.png)
 
A webalkalmazás felügyelt szolgáltatásidentitás most már hozzáférhet az Event Hubs névtérhez, és az event hubs korábban létrehozott. 

### <a name="run-the-app"></a>Az alkalmazás futtatása

Most már módosíthatja az ASP.NET-alkalmazás létrehozott alapértelmezett oldalán. Használhatja a webes alkalmazás kód [a GitHub-tárházban](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp). 

Ha az alkalmazás indításakor, irányítsa a böngészőt az EventHubsMSIDemo.aspx. Másik lehetőségként állítsa be a kezdőlapot. A kód a EventHubsMSIDemo.aspx.cs fájlban találhatók. Néhány számbeviteli mezők, valamint a minimális webalkalmazás eredménye **küldése** és **kap** gombok vagy üzenetek küldése / fogadása az Event Hubs-hez. 

Megjegyzés: az [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objektum inicializálása. A megosztott hozzáférési jogkivonat (SAS) jogkivonat-szolgáltató helyett a kód hoz létre a felügyelt szolgáltatás identitásának a jogkivonat-szolgáltatót a `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` hívható meg. Nincsenek, nincs megőrizheti és felhasználhatja a titkos kulcsok. A felügyelt szolgáltatás identitásának környezet az Eseményközpontokhoz és a hitelesítési kézfogás folyamata automatikusan kezeli a jogkivonat-szolgáltató, ami egy egyszerűbb modell, mint a SAS használatával.

Miután végrehajtotta ezeket a módosításokat, közzététele, és futtassa az alkalmazást. Egyszerű módot nyújt a helyes közzétételi adatokat, hogy töltse le és majd a Visual Studio-közzétételi profil importálása:

![](./media/event-hubs-managed-service-identity/msi3.png)
 
Üzenetek küldése / fogadása, adja meg a névtér nevét és a létrehozott entitás nevét, majd jelölje be az **küldése** vagy **kap**. 
 
Figyelje meg, hogy a felügyelt szolgáltatásidentitás csak akkor működik, az Azure-környezeten belül, és csak a az App Service telepítésében, amelyben konfigurált ilyet. Ne feledje, hogy felügyelt szolgáltatás-identitások nem működik együtt az App Service üzembe helyezési most.

## <a name="next-steps"></a>További lépések

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

* Bevezetés az [Event Hubs használatába oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Az Event Hubs díjszabása](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)