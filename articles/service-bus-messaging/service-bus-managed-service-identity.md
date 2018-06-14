---
title: Az Azure Service Bus preview Szolgáltatásidentitás felügyelt |} Microsoft Docs
description: Az Azure Service Bus felügyelt szolgáltatás-identitások használata
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 7b9901ee3478cb193c808b65d2dbbcf8b596a3c1
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
ms.locfileid: "29874652"
---
# <a name="managed-service-identity-preview"></a>Felügyelt Szolgáltatásidentitás (előzetes verzió)

Felügyelt szolgáltatás identitásának (MSI) lehetővé teszi az Azure-határokon, amely lehetővé teszi a központi telepítést, amely alatt az alkalmazás kódjában fut. társított egy biztonságos azonosító létrehozása. Identitás majd társíthat egyéni engedélyeket az eléréséhez szükséges konkrét Azure-erőforrások az alkalmazást, a hozzáférés-vezérlési szerepkörtől.

Az MSI-fájl az Azure platform kezeli a futásidejű identitást. Nem kell tárolni, és az alkalmazás kódja vagy konfiguráció, vagy maga identitását, vagy az erőforrások eléréséhez szükséges tárelérési kulcsok védelme. A Service Bus-ügyfélalkalmazás belül az Azure App Service alkalmazás vagy virtuális gépen futó engedélyezett MSI-támogatással rendelkező nem kell SAS szabályok és a kulcsok vagy más hozzáférési jogkivonatok kell kezelni. Az ügyfélalkalmazás csak kell a Service Bus üzenetkezelés névtér a végpont címét. Ha összekapcsolja az alkalmazást, a Service Bus a MSI környezetet az ügyfél egy műveletben, például a cikk későbbi részében látható van kötve. 

Amikor felügyelt szolgáltatásidentitás társítva, egy Service Bus-ügyfélalkalmazást minden engedélyezett műveleteket hajthat végre. Az engedélyt a Service Bus szerepek egy olyan MSI Csomaghoz társításával. 

## <a name="service-bus-roles-and-permissions"></a>A Service Bus-szerepköröket és engedélyeket

Kezdeti nyilvános előzetes csak felügyelt szolgáltatásidentitás adhat hozzá a Service Bus-névtér, amely az identitás teljes hozzáférést a névtér összes entitásának a "Tulajdonos" vagy "Közreműködői" szerepét. Azonban a névtér topológia módosítása olyan kezdetben felügyeleti támogatott azonban csak Azure Resource Manager és a Service Bus REST natív kezelőfelület keresztül nem. Ez a támogatás azt is jelenti, hogy nem használható a .NET-keretrendszer ügyfél [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) felügyelt szolgáltatásidentitás objektummal.

## <a name="use-service-bus-with-a-managed-service-identity"></a>A Service Bus használata egy felügyelt identitás

A következő szakasz azt ismerteti, hogyan hozhat létre és telepíthet a mintaalkalmazás, amely felügyelt szolgáltatásidentitás, hogyan egy Service Bus üzenetkezelés névtér identitás hozzáférést, és hogyan kommunikál a Service busszal az alkalmazás fut. Ezzel az identitással entitások.

Ez a bevezető ismerteti egy webalkalmazást [Azure App Service](https://azure.microsoft.com/services/app-service/). A virtuális gép által üzemeltetett alkalmazások szükséges lépések hasonlóak.

### <a name="create-an-app-service-web-application"></a>Az App Service-webalkalmazás létrehozása

Az első lépés, ha az App Service ASP.NET-alkalmazások. Ha még nem ismeri a ezzel az Azure-ban, hajtsa végre a [Ez az útmutató útmutató](../app-service/app-service-web-get-started-dotnet-framework.md). Ahogy az az oktatóprogram MVC alkalmazás létrehozása helyett azonban Web Forms-alkalmazás létrehozása.

### <a name="set-up-the-managed-service-identity"></a>Állítsa be a felügyelt azonosítója

Miután létrehozta az alkalmazást, nyissa meg az újonnan létrehozott webalkalmazás az Azure portálon (az útmutató is látható), majd keresse meg a **Szolgáltatásidentitás felügyelt** lapon, és engedélyezze a szolgáltatást: 

![](./media/service-bus-managed-service-identity/msi1.png)

A szolgáltatás engedélyezése után egy új szolgáltatásidentitás létrehozása az Azure Active Directoryban, és az App Service-állomás konfigurált.

### <a name="create-a-new-service-bus-messaging-namespace"></a>Új Service Bus üzenetkezelés névtér létrehozása

Ezt követően [hozzon létre egy Service Bus üzenetkezelés névtér](service-bus-create-namespace-portal.md) egy Azure-régiókban, amelyek preview támogatják a Szerepalapú: **amerikai keleti**, **amerikai keleti régiója 2**, vagy **Nyugat-Európa** . 

Keresse meg a névtér **hozzáférés-vezérlés (IAM)** a portál lapot, és kattintson a **Hozzáadás** hozzáadása a felügyelt identitását a **tulajdonos** szerepkör. Ehhez keresse meg a webalkalmazás nevét a **engedélyek hozzáadása** panel **válasszon** mezőben, majd kattintson a bejegyzést. Ezután kattintson a **Save** (Mentés) gombra.

![](./media/service-bus-managed-service-identity/msi2.png)
 
A webalkalmazás felügyelt szolgáltatásidentitás most már hozzáférhet a Service Bus-névtér, és a várakozási sorba korábban létrehozott. 

### <a name="run-the-app"></a>Az alkalmazás futtatása

Most már módosíthatja az ASP.NET-alkalmazás létrehozott alapértelmezett oldalán. Használhatja a webes alkalmazás kód [a GitHub-tárházban](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/ManagedServiceIdentity).

A Default.aspx lap a kezdőlapja. A kód a Default.aspx.cs fájlban találhatók. Néhány számbeviteli mezők, valamint a minimális webalkalmazás eredménye **küldése** és **kap** gombok, amelyhez csatlakozni a Service Bus számára, vagy üzenetek küldése / fogadása.

Megjegyzés: az [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) objektum inicializálása. A megosztott hozzáférési jogkivonat (SAS) jogkivonat-szolgáltató helyett a kód hoz létre a felügyelt szolgáltatás identitásának a jogkivonat-szolgáltatót a `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` hívható meg. Nincsenek, nincs megőrizheti és felhasználhatja a titkos kulcsok. A felügyelt szolgáltatás identitásának környezet Service Bus és a hitelesítési kézfogás folyamata automatikusan kezeli a jogkivonat-szolgáltató, ami egy egyszerűbb modell, mint a SAS használatával.

Miután végrehajtotta ezeket a módosításokat, közzététele, és futtassa az alkalmazást. Egyszerű módot nyújt a helyes közzétételi adatokat, hogy töltse le és majd a Visual Studio-közzétételi profil importálása:

![](./media/service-bus-managed-service-identity/msi3.png)
 
Üzenetek küldése / fogadása, adja meg a névtér nevét és a létrehozott entitás nevét, majd jelölje be az **küldése** vagy **kap**.
 
Figyelje meg, hogy a felügyelt szolgáltatásidentitás csak akkor működik, az Azure-környezeten belül, és csak a az App Service telepítésében, amelyben konfigurált ilyet. Ne feledje, hogy felügyelt szolgáltatás-identitások nem működik együtt az App Service üzembe helyezési most.

## <a name="next-steps"></a>További lépések

A Service Bus üzenetkezelésről az alábbi témakörökben találhat további információkat.

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)