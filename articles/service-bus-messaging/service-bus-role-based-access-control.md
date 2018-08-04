---
title: Azure Service Bus Role-Based hozzáférés-vezérlés (RBAC) – előzetes verzió |} A Microsoft Docs
description: Az Azure Service Bus szerepköralapú hozzáférés-vezérlés
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
ms.openlocfilehash: 80c226b4b4295a232a6cefb4da12e1db23adae66
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505233"
---
# <a name="active-directory-role-based-access-control-preview"></a>Aktív Directory Role-Based hozzáférés-vezérlés (előzetes verzió)

A Microsoft Azure-erőforrások és az Azure Active Directory (Azure AD-) alapú alkalmazások integrált hozzáférés-vezérlési felügyeletet biztosít. Az Azure ad-vel, vagy kezelheti a felhasználói fiókok és alkalmazások kifejezetten az Azure-alapú alkalmazások, vagy Ön is összevonható az Azure AD-céges szintű single-sign-on is kiterjedő az Azure-erőforrások a meglévő Active Directory-infrastruktúra és az Azure-ban üzemeltetett alkalmazások. Majd hozzárendelheti ezeket az Azure AD-felhasználói és identitások globális és a szolgáltatás-specifikus szerepkörökhöz annak érdekében, hogy az Azure-erőforrásokhoz való hozzáférést.

Azure Service Bus-névterek és az összes kapcsolódó erőforrást az Azure Portalon a felügyeleti és az Azure resource management API már védett használatával a *szerepköralapú hozzáférés-vezérlés* (RBAC) modellt. Futásidejű műveletek RBAC funkciója mostantól nyilvános előzetes verzióban érhető el. 

Azure AD RBAC használó alkalmazások nem kell kezelni a SAS-szabályok és a kulcsok vagy bármilyen más konkrét, a Service Bus hozzáférési jogkivonatok. Az ügyfélalkalmazás kommunikál az Azure AD-hitelesítési környezetet létrehozni, és a egy hozzáférési jogkivonatot szerez be a Service Bus számára. A tartományi felhasználói fiókokat, amelyek interaktív bejelentkezést igényelnek, az alkalmazás soha nem kezeli a hitelesítő adatokat közvetlenül.

## <a name="service-bus-roles-and-permissions"></a>A Service Bus-szerepkörök és engedélyek

A kezdeti nyilvános előzetes verzió csak adhat az Azure AD-fiókok és az egyszerű szolgáltatások a Service Bus Messaging-névteret a "Tulajdonos" vagy "Közreműködő" szerepköröket. Ez a művelet az identitás a névtérben lévő összes entitáshoz teljes hozzáférést biztosít. Felügyeleti műveleteket, amelyek a névtér topológia módosítása rendszer kezdetben csak támogatott, ha az Azure erőforrás-kezelést, és nem a natív Service Bus REST-felügyeleti felületén keresztül. Ez a támogatás is azt jelenti, hogy a .NET-keretrendszer ügyfél [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objektum nem használható az Azure AD-fiókot.  

## <a name="use-service-bus-with-an-azure-ad-domain-user-account"></a>A Service Bus használata egy Azure AD tartományi felhasználói fiók

A következő szakasz azt ismerteti, hogyan hozhat létre és futtathat egy mintaalkalmazást, amely kérni fogja az interaktív Azure AD-felhasználó bejelentkezni, hogyan lehet hozzáférést biztosítani a Service Bus, a felhasználói fiók és az identitásukat el az Event Hubs használatával. 

Ez a bevezető ismerteti egy egyszerű konzolalkalmazást a [, amelynek kódja a Githubon](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/RoleBasedAccessControl).

### <a name="create-an-active-directory-user-account"></a>Az Active Directory felhasználói fiók létrehozása

Az első lépés nem kötelező. Minden Azure-előfizetés automatikusan megfeleltetni az Azure Active Directory-bérlő, és ha rendelkezik hozzáféréssel az Azure-előfizetéssel, a felhasználói fiók már regisztrálva van. Ez azt jelenti, hogy a fiókot használhatja. 

Ha továbbra is szeretne létrehozni ebben a forgatókönyvben egy külön fiókot [kövesse az alábbi lépéseket](../automation/automation-create-aduser-account.md). Fiókok létrehozása az Azure Active Directory-bérlőjéhez, amely nem lehet nagyobb a vállalati forgatókönyvek esetében a engedéllyel kell rendelkeznie.

### <a name="create-a-service-bus-namespace"></a>Service Bus-névtér létrehozása

Ezután [hozzon létre egy Service Bus üzenetkezelési névteret](service-bus-create-namespace-portal.md) RBAC előzetes támogató Azure-régiók egyikében: **USA keleti régiójában**, **USA keleti régiója 2**, vagy **Nyugat-Európa** . 

A névtér létrehozása után lépjen a **hozzáférés-vezérlés (IAM)** lapon a portálon, és kattintson a **Hozzáadás** az Azure AD felhasználói fiók hozzáadása a tulajdonosi szerepkör. Saját felhasználói fiókját használja, és létrehozta a névteret, ha Ön már a tulajdonosi szerepkör. Egy másik fiókot ad hozzá a szerepkört, keresse meg a webalkalmazás nevére a **engedélyek hozzáadása** panel **kiválasztása** mezőben, majd kattintson a bejegyzésre. Ezután kattintson a **Save** (Mentés) gombra.

![](./media/service-bus-role-based-access-control/rbac1.PNG)

A felhasználói fiókot most már hozzáférhet a Service Bus-névteret, és az üzenetsorba korábban hozott létre.
 
### <a name="register-the-application"></a>Az alkalmazás regisztrálása

A mintaalkalmazás futtatása előtt az Azure ad-ben regisztrálja, és hagyja jóvá a beleegyezést kérő üzenetet, amely lehetővé teszi az alkalmazás Azure Service Bus eléréséhez nyújtsanak a nevében. 

Mivel a mintaalkalmazás egy konzolalkalmazást, kell regisztrálni egy natív alkalmazást és API-hozzáférés biztosítása a **Microsoft.ServiceBus** a "szükséges engedélyek" csoporthoz. Natív alkalmazások is kell egy **átirányítási URI** szolgál a azonosítóként; Azure AD-ben az URI-t kell lennie egy hálózati cél. Használat `http://servicebus.microsoft.com` ebben a példában, mivel már kód a minta az URI-ra.

A részletes regisztrációs lépéseket mutatjuk be [ebben az oktatóanyagban](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Kövesse a lépéseket a regisztráció egy **natív** alkalmazást, majd kövesse a frissítés utasításokat hozzáadása a **Microsoft.ServiceBus** API-t a szükséges engedélyekkel. A lépéseket, jegyezze fel a **TenantId** és a **ApplicationId**, mert szüksége lesz ezekre az értékekre, az alkalmazás futtatásához.

### <a name="run-the-app"></a>Az alkalmazás futtatása

A minta futtatása előtt szerkessze az App.config fájlt, és a forgatókönyvtől függően adja meg a következő értékeket:

- `tenantId`: Állítsa **TenantId** értéket.
- `clientId`: Állítsa **ApplicationId** értéket. 
- `clientSecret`: Ha azt szeretné, a titkos ügyfélkulcsot használatával kíván bejelentkezni, hozza létre az Azure ad-ben. Egy webalkalmazás vagy API-t is, használja a natív alkalmazás helyett. Adja hozzá az alkalmazás alatt **hozzáférés-vezérlés (IAM)** a korábban létrehozott névtér.
- `serviceBusNamespaceFQDN`: Állítsa az újonnan létrehozott Service Bus-névtér; teljes DNS-neve Ha például `example.servicebus.windows.net`.
- `queueName`: Állítsa a létrehozott üzenetsor neve.
- Az átirányítási URI-t az alkalmazásba az előző lépésben megadott.
 
Futtassa a konzolalkalmazást, ha kéri választása; Kattintson a **interaktív felhasználói bejelentkezési** írja be a számát, és lenyomja az ENTER BILLENTYŰT. Az alkalmazás jeleníti meg a bejelentkezési ablakban, a Service Bus eléréséhez beleegyezését kéri, és a szolgáltatás segítségével a bejelentkezési azonosító használatával küldése/fogadása forgatókönyv futtatása.

## <a name="next-steps"></a>További lépések

A Service Bus üzenetkezelésről az alábbi témakörökben találhat további információkat.

* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus-üzenetsorok, -témakörök és -előfizetések](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)