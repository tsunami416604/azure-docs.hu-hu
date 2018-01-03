---
title: "Azure Service Bus Role-Based hozzáférés-vezérlés (RBAC) előzetes verzió |} Microsoft Docs"
description: "Az Azure Service Bus szerepköralapú hozzáférés-vezérlés"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 729d6db6b2fc6495ffb0f4fbe4d545d7ad953cef
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="active-directory-role-based-access-control-preview"></a>Aktív Directory Role-Based hozzáférés-vezérlés (előzetes verzió)

Microsoft Azure a erőforrások és alkalmazások az Azure Active Directory (Azure AD) alapján integrált hozzáférés-vezérlési felügyeletet biztosít. Az Azure ad-vel, kezelése vagy az felhasználói fiókok és alkalmazások kifejezetten az Azure-alapú alkalmazások, vagy lehet összevonást végezni a meglévő Active Directory-infrastruktúra az Azure ad-val vállalati szintű single-sign-on is kiterjedő az Azure-erőforrások és az Azure üzemeltetett alkalmazások. Majd rendelhet az Azure AD felhasználó- és alkalmazás megszakítása globális és a szolgáltatásspecifikus szerepkörök ahhoz, hogy az Azure-erőforrások hozzáférést.

Azure Service Bus, a névterek és az Azure portálon keresztül az összes kapcsolódó erőforrások kezelése és az Azure erőforrás-kezelés API már védje a *szerepköralapú hozzáférés-vezérlés* (RBAC) modell. Futásidejű műveletekhez RBAC egy olyan szolgáltatás mostantól nyilvános előzetes verziójában. 

Azure AD RBAC használó alkalmazások nem kell SAS szabályok és a kulcsok vagy bármely más Service Bus vonatkozó hozzáférési jogkivonatok kell kezelni. Az ügyfélalkalmazás együttműködik az Azure AD hitelesítési környezetet létrehozásához, és a Service Bus szerez be a hozzáférési tokent. A tartományi felhasználói fiókok, interaktív bejelentkezéshez szükséges az alkalmazás soha nem kezeli a hitelesítő adatok közvetlenül.

## <a name="service-bus-roles-and-permissions"></a>A Service Bus-szerepköröket és engedélyeket

A kezdeti nyilvános előzetes verzióhoz csak adhat az Azure AD-fiókok és a szolgáltatás rendszerbiztonsági tagok a "Tulajdonos" vagy "Közreműködői" szerepkörök a Service Bus üzenetkezelés névtér. Ez a művelet az identitás teljes hozzáférést biztosítanak a névtér összes entitásának. Módosítsa a névtér topológia felügyeleti műveleteket kezdetben csak támogatott, ha az Azure erőforrás-kezelés és a Service Bus REST natív kezelőfelület keresztül nem. Ez a támogatás azt is jelenti, hogy a .NET-keretrendszer ügyfél [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objektum nem használható az Azure AD-fiókot.  

## <a name="use-service-bus-with-an-azure-ad-domain-user-account"></a>A Service Bus használata egy Azure AD tartományi felhasználói fiókot

A következő szakasz azt ismerteti, hogyan hozhat létre és futtathat a mintaalkalmazás, amely felszólítja az interaktív Azure AD-felhasználó bejelentkezni, a Service Bus engedélyt, a felhasználói fiók, és az Event Hubs eléréséhez használja az identitásukat. 

Ez a bevezető egyszerű konzolalkalmazásként, ismerteti a [, amelynek kódja a Githubon van](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).

### <a name="create-an-active-directory-user-account"></a>Az Active Directory felhasználói fiók létrehozása

Az első lépés nem kötelező. Minden Azure-előfizetés automatikusan megfeleltetni az Azure Active Directory-bérlő, és rendelkezik Azure-előfizetéssel, ha a felhasználói fiók már regisztrálva van. Ez azt jelenti, hogy csak a fiókot használhat. 

Ha továbbra is szeretné létrehoz egy külön fiókot ehhez a forgatókönyvhöz [kövesse az alábbi lépéseket](../automation/automation-create-aduser-account.md). Fiók létrehozása az Azure Active Directory-bérlő, amely nem lehet nagyobb vállalati forgatókönyvek esetében a engedéllyel kell rendelkeznie.

### <a name="create-a-service-bus-namespace"></a>Service Bus-névtér létrehozása

Ezt követően [hozzon létre egy Service Bus üzenetkezelés névtér](service-bus-create-namespace-portal.md) egy Azure-régiókban, amelyek preview támogatják a Szerepalapú: **amerikai keleti**, **amerikai keleti régiója 2**, vagy **Nyugat-Európa** . 

A névtér létrehozása után nyissa meg a **hozzáférés-vezérlés (IAM)** a portál lapot, és kattintson a **Hozzáadás** az Azure AD-felhasználói fiók hozzáadása a tulajdonosi szerepkört. Ha a saját felhasználói fiókot használ, és a névtér létrehozott, akkor már szerepelnek a tulajdonosi szerepkört. Egy másik fiókot a szerepkör hozzáadásához keresse meg a webalkalmazás nevét a **engedélyek hozzáadása** panel **kiválasztása** mezőben, majd kattintson a bejegyzést. Ezután kattintson a **Save** (Mentés) gombra.

![](./media/service-bus-role-based-access-control/rbac1.PNG)

A felhasználói fiók hozzáférhet a Service Bus-névtér, és a várakozási sorba korábban hozott létre.
 
### <a name="register-the-application"></a>Az alkalmazás regisztrálása

A mintaalkalmazás futtatása előtt az Azure ad-ben regisztrálja, és hagyja jóvá a beleegyezést kérő üzenete, amely lehetővé teszi az alkalmazás Azure Service Bus eléréséhez a nevében. 

Mivel a mintaalkalmazás egy konzolalkalmazást, kell egy natív alkalmazás regisztrálása és API-hozzáférés biztosítása a **Microsoft.ServiceBus** a "szükséges engedélyek" készletéhez. Natív alkalmazásokat is kell egy **átirányítási URI-t** szolgál a azonosítóként; Azure AD-ben az URI nem kell egy hálózati cél. Használjon `http://servicebus.microsoft.com` ehhez a példához, mivel már kód a minta az adott URI.

A részletes regisztrációs lépéseket magyarázata [ebben az oktatóanyagban](../active-directory/develop/active-directory-integrating-applications.md). Regisztrálásához kövesse a **natív** alkalmazást, majd kövesse az utasításokat a frissítés a **Microsoft.ServiceBus** API-t a szükséges engedélyekkel. A lépések végrehajtásával, jegyezze fel a **TenantId** és a **ApplicationId**, mivel ezeket az értékeket az alkalmazás futtatásához kell.

### <a name="run-the-app"></a>Az alkalmazás futtatása

A minta futtatásához, szerkessze az App.config fájlt, és a forgatókönyvtől függően adja meg a következő értékeket:

- `tenantId`: Beállítása **TenantId** érték.
- `clientId`: Beállítása **ApplicationId** érték. 
- `clientSecret`: Ha azt szeretné, a titkos ügyfélkulcs bejelentkezni, hozza létre az Azure ad-ben. Is használjon webalkalmazás vagy API a natív alkalmazások helyett. Továbbá adja hozzá az alkalmazás a **hozzáférés-vezérlés (IAM)** a korábban létrehozott névtérben.
- `serviceBusNamespaceFQDN`: Állítsa be a teljes DNS-nevével, az újonnan létrehozott Service Bus-névtér; például `example.servicebus.windows.net`.
- `queueName`: A létrehozott üzenetsorba nevére állítja.
- Az átirányítási URI-t adott meg az előző lépésben az alkalmazásban.
 
A konzol alkalmazás futtatásakor kéri a választása; Kattintson a **interaktív felhasználói bejelentkezés** ehhez írja be annak a számát, és nyomja le az ENTER BILLENTYŰT. Az alkalmazás bejelentkezési ablak megjeleníti, a Service Bus eléréséhez beleegyezést kér, és a szolgáltatás segítségével a Küldés/fogadás a forgatókönyvet a bejelentkezési azonosítót végigfuttatása.

## <a name="next-steps"></a>További lépések

A Service Bus üzenetkezelésről az alábbi témakörökben találhat további információkat.

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)