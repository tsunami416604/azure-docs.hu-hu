---
title: "Azure Event Hubs Role-Based hozzáférés-vezérlés (RBAC) előzetes verzió |} Microsoft Docs"
description: "Az Azure Event Hubs szerepköralapú hozzáférés-vezérlés"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 0d3a779eb2cccf242bcd42d82c1a90048b3512ab
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2017
---
# <a name="active-directory-role-based-access-control-preview"></a>Aktív Directory Role-Based hozzáférés-vezérlés (előzetes verzió)

Microsoft Azure a erőforrások és alkalmazások az Azure Active Directory (Azure AD) alapján integrált hozzáférés-vezérlési felügyeletet biztosít. Az Azure ad-vel, kezelése vagy az felhasználói fiókok és alkalmazások kifejezetten az Azure-alapú alkalmazások, vagy lehet összevonást végezni a meglévő Active Directory-infrastruktúra az Azure ad-val vállalati szintű single-sign-on is kiterjedő az Azure-erőforrások és az Azure üzemeltetett alkalmazások. Majd rendelhet az Azure AD felhasználó- és alkalmazás megszakítása globális és a szolgáltatásspecifikus szerepkörök ahhoz, hogy az Azure-erőforrások hozzáférést.

Az Azure Event Hubs, a névterek és az Azure portálon keresztül az összes kapcsolódó erőforrások kezelése és az Azure erőforrás-kezelés API már védje a *szerepköralapú hozzáférés-vezérlés* (RBAC) modell. Futásidejű műveletekhez RBAC egy olyan szolgáltatás mostantól nyilvános előzetes verziójában. 

Azure AD RBAC használó alkalmazások nem kell SAS szabályok és a kulcsok vagy bármely más adott Event hubs hozzáférési jogkivonatok kell kezelni. Az ügyfélalkalmazás együttműködik az Azure AD hitelesítési környezetet létrehozásához, és szerez be a hozzáférési tokent az Event Hubs számára. A tartományi felhasználói fiókok, interaktív bejelentkezéshez szükséges az alkalmazás soha nem kezeli a hitelesítő adatok közvetlenül.

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs szerepköröket és engedélyeket

A kezdeti nyilvános előzetes verzióhoz csak adhat az Azure AD-fiókok és a szolgáltatás rendszerbiztonsági tagok a "Tulajdonos" vagy "Közreműködői" szerepköröket, az Event Hubs névtér. Ez a művelet az identitás teljes hozzáférést biztosítanak a névtér összes entitásának. Módosítsa a névtér topológia felügyeleti műveleteket kezdetben csak támogatott, ha az Azure erőforrás-kezelés és nem a natív Event Hubs REST kiszolgálókezelési felületen segítségével. Ez a támogatás azt is jelenti, hogy a .NET-keretrendszer ügyfél [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objektum nem használható az Azure AD-fiókot.  

## <a name="use-event-hubs-with-an-azure-ad-domain-user-account"></a>Az Event Hubs egy Azure AD tartományi felhasználói fiók használata

A következő szakasz azt ismerteti, hogyan hozhat létre és futtathat a mintaalkalmazás, amely felszólítja az interaktív Azure AD-felhasználó bejelentkezni, az Event Hubs hozzáférést, a felhasználói fiók, és az Event Hubs eléréséhez használja az identitásukat. 

Ez a bevezető egyszerű konzolalkalmazásként, ismerteti a [kódot, amelynek van a Githubon](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Rbac/EventHubsSenderReceiverRbac/)

### <a name="create-an-active-directory-user-account"></a>Az Active Directory felhasználói fiók létrehozása

Az első lépés nem kötelező. Minden Azure-előfizetés automatikusan megfeleltetni az Azure Active Directory-bérlő, és rendelkezik Azure-előfizetéssel, ha a felhasználói fiók már regisztrálva van. Ez azt jelenti, hogy csak a fiókot használhat. 

Ha továbbra is szeretné létrehoz egy külön fiókot ehhez a forgatókönyvhöz [kövesse az alábbi lépéseket](../automation/automation-create-aduser-account.md). Fiók létrehozása az Azure Active Directory-bérlő, amely nem lehet nagyobb vállalati forgatókönyvek esetében a engedéllyel kell rendelkeznie.

### <a name="create-an-event-hubs-namespace"></a>Az Event Hubs-névtér létrehozása

Ezt követően [az Event Hubs-névtér létrehozása](event-hubs-create.md) egy Azure-régiókban, amelyek az Event Hubs preview támogatják a Szerepalapú: **amerikai keleti**, **amerikai keleti régiója 2**, vagy **Nyugat-Európa** . 

A névtér létrehozása után nyissa meg a **hozzáférés-vezérlés (IAM)** a portál lapot, és kattintson a **Hozzáadás** az Azure AD-felhasználói fiók hozzáadása a tulajdonosi szerepkört. Ha a saját felhasználói fiókot használ, és a névtér létrehozott, akkor már szerepelnek a tulajdonosi szerepkört. Egy másik fiókot a szerepkör hozzáadásához keresse meg a webalkalmazás nevét a **engedélyek hozzáadása** panel **kiválasztása** mezőben, majd kattintson a bejegyzést. Ezután kattintson a **Save** (Mentés) gombra.
 
![](./media/event-hubs-role-based-access-control/rbac1.PNG)

A felhasználói fiók hozzáférhet az Event Hubs névtérhez, és az event hubs korábban hozott létre.
 
### <a name="register-the-application"></a>Az alkalmazás regisztrálása

A mintaalkalmazás futtatása előtt az Azure ad-ben regisztrálja, és hagyja jóvá a beleegyezést kérő üzenete, amely lehetővé teszi az alkalmazás nevében az Event Hubs eléréséhez. 

Mivel a mintaalkalmazás egy konzolalkalmazást, kell egy natív alkalmazás regisztrálása és API-hozzáférés biztosítása a **Microsoft.EventHub** a "szükséges engedélyek" készletéhez. Natív alkalmazásokat is kell egy **átirányítási URI-t** szolgál a azonosítóként; Azure AD-ben az URI nem kell egy hálózati cél. Használjon `http://eventhubs.microsoft.com` ehhez a példához, mivel már kód a minta az adott URI.

A részletes regisztrációs lépéseket magyarázata [ebben az oktatóanyagban](../active-directory/develop/active-directory-integrating-applications.md). Regisztrálásához kövesse a **natív** alkalmazást, majd kövesse az utasításokat a frissítés a **Microsoft.EventHub** API-t a szükséges engedélyekkel. A lépések végrehajtásával, jegyezze fel a **TenantId** és a **ApplicationId**, mivel ezeket az értékeket az alkalmazás futtatásához kell.

### <a name="run-the-app"></a>Az alkalmazás futtatása

A minta futtatásához, szerkessze az App.config fájlt, és a forgatókönyvtől függően adja meg a következő értékeket:

- `tenantId`: Beállítása **TenantId** érték.
- `clientId`: Beállítása **ApplicationId** érték. 
- `clientSecret`: Ha azt szeretné, a titkos ügyfélkulcs bejelentkezni, hozza létre az Azure ad-ben. Is használjon webalkalmazás vagy API a natív alkalmazások helyett. Továbbá adja hozzá az alkalmazás a **hozzáférés-vezérlés (IAM)** a korábban létrehozott névtérben.
- `eventHubNamespaceFQDN`: Állítsa be a teljes DNS-nevével, az újonnan létrehozott Event Hubs névtér; például `example.servicebus.windows.net`.
- `eventHubName`: A létrehozott eseményközpont nevére állítja.
- Az átirányítási URI-t adott meg az előző lépésben az alkalmazásban.
 
A konzol alkalmazás futtatásakor kéri a választása; Kattintson a **interaktív felhasználói bejelentkezés** ehhez írja be annak a számát, és nyomja le az ENTER BILLENTYŰT. Az alkalmazás egy bejelentkezési ablak megjeleníti, bekéri az Ön hozzájárul az Event Hubs eléréséhez és a szolgáltatás segítségével lépéseinek a Küldés/fogadás a forgatókönyvet a bejelentkezési azonosítót.

## <a name="next-steps"></a>További lépések

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

* Bevezetés az [Event Hubs használatába oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Az Event Hubs díjszabása](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)