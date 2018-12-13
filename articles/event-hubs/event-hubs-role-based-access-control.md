---
title: Szerepköralapú hozzáférés-vezérlés előzetes – Azure Event Hubs |} A Microsoft Docs
description: Ez a cikk a szerepköralapú hozzáférés-vezérlés információkat biztosít az Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: c7d38538b3876ae91c0ae3794e14ab11f08993c6
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53162885"
---
# <a name="active-directory-role-based-access-control-preview"></a>Aktív Directory Role-Based hozzáférés-vezérlés (előzetes verzió)

A Microsoft Azure-erőforrások és az Azure Active Directory (Azure AD-) alapú alkalmazások integrált hozzáférés-vezérlési felügyeletet biztosít. Az Azure ad-vel vagy kezelheti felhasználói fiókok és alkalmazások kifejezetten az Azure-alapú alkalmazások a, vagy meg is összevonható az Azure AD-céges szintű single-sign-on, amely Azure-erőforrások is kiterjedő a meglévő Active Directory-infrastruktúra és az Azure-ban üzemeltetett alkalmazások. Majd hozzárendelheti ezeket az Azure AD-felhasználói és identitások globális és a szolgáltatás-specifikus szerepkörökhöz annak érdekében, hogy az Azure-erőforrásokhoz való hozzáférést.

Azure Event hubs-névterek és az összes kapcsolódó erőforrást az Azure Portalon a felügyeleti és az Azure resource management API már védett használatával a *szerepköralapú hozzáférés-vezérlés* (RBAC) modellt. Futásidejű műveletek RBAC funkciója mostantól nyilvános előzetes verzióban érhető el. 

Azure AD RBAC használó alkalmazások nem kell kezelni a SAS-szabályok és a kulcsok vagy bármilyen más konkrét és az Event Hubs hozzáférési jogkivonatok. Az ügyfélalkalmazás kommunikál az Azure AD-hitelesítési környezetet létrehozni, és a egy hozzáférési jogkivonatot szerez az Event Hubs. A tartományi felhasználói fiókokat, amelyek interaktív bejelentkezést igényelnek, az alkalmazás soha nem kezeli a hitelesítő adatokat közvetlenül.

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs szerepkörök és engedélyek

A kezdeti nyilvános előzetes verzió csak hozzáadhat az Azure AD-fiókok és az egyszerű szolgáltatások a "Tulajdonos" vagy "Közreműködő" Event Hubs-névtér-szerepkörökhöz. Ez a művelet az identitás a névtérben lévő összes entitáshoz teljes hozzáférést biztosít. Felügyeleti műveleteket, amelyek a névtér topológia módosítása rendszer kezdetben csak támogatott, ha az Azure erőforrás-kezelést, és nem a natív Event hubs szolgáltatás REST-felügyeleti felületén keresztül. Ez a támogatás is azt jelenti, hogy a .NET-keretrendszer ügyfél [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) objektum nem használható az Azure AD-fiókot.  

## <a name="use-event-hubs-with-an-azure-ad-domain-user-account"></a>Event Hubs használata az Azure AD tartományi felhasználói fiók

A következő szakasz azt ismerteti, hogyan hozhat létre és futtathat egy mintaalkalmazást, amely kérni fogja az interaktív Azure AD-felhasználó bejelentkezésének, hogyan lehet hozzáférést biztosítani az Event Hubs, a felhasználói fiók és az identitásukat el az Event Hubs használatával. 

Ez a bevezető ismerteti egy egyszerű konzolalkalmazást a [, amelynek kódja a Githubon](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Rbac/EventHubsSenderReceiverRbac/)

### <a name="create-an-active-directory-user-account"></a>Az Active Directory felhasználói fiók létrehozása

Az első lépés nem kötelező. Minden Azure-előfizetés automatikusan megfeleltetni az Azure Active Directory-bérlő, és ha rendelkezik hozzáféréssel az Azure-előfizetéssel, a felhasználói fiók már regisztrálva van. Ez azt jelenti, hogy a fiókot használhatja. 

Ha továbbra is szeretne létrehozni ebben a forgatókönyvben egy külön fiókot [kövesse az alábbi lépéseket](../automation/automation-create-aduser-account.md). Fiókok létrehozása az Azure Active Directory-bérlőjéhez, amely nem lehet nagyobb a vállalati forgatókönyvek esetében a engedéllyel kell rendelkeznie.

### <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Ezután [Event Hubs-névtér létrehozása](event-hubs-create.md) RBAC Event hubs szolgáltatás előzetes támogató Azure-régiók egyikében: **USA keleti régiója**, **USA keleti régiója 2**, vagy **Nyugat-Európa**. 

Miután létrejött a névteret, lépjen a **hozzáférés-vezérlés (IAM)** lapon a portálon, és kattintson a **szerepkör-hozzárendelés hozzáadása** az Azure AD felhasználói fiók hozzáadása a tulajdonosi szerepkör. Saját felhasználói fiókját használja, és létrehozta a névteret, ha Ön már a tulajdonosi szerepkör. Egy másik fiókot ad hozzá a szerepkört, keresse meg a webalkalmazás nevére a **engedélyek hozzáadása** panel **kiválasztása** mezőben, majd kattintson a bejegyzésre. Ezután kattintson a **Save** (Mentés) gombra. A felhasználói fiókot most már hozzáférhet az Event Hubs-névtér és az event hubs korábban hozott létre.
 
### <a name="register-the-application"></a>Az alkalmazás regisztrálása

A mintaalkalmazás futtatása előtt az Azure ad-ben regisztrálja, és hagyja jóvá a beleegyezést kérő üzenetet, amely lehetővé teszi az alkalmazás eléréséhez az Event Hubs nyújtsanak a nevében. 

Mivel a mintaalkalmazás egy konzolalkalmazást, kell regisztrálni egy natív alkalmazást és API-hozzáférés biztosítása a **hátralékának** a "szükséges engedélyek" csoporthoz. Natív alkalmazások is kell egy **átirányítási URI** az Azure ad-ben, amely; azonosítót az URI-t kell lennie egy hálózati cél. Használat `http://eventhubs.microsoft.com` ebben a példában, mivel már kód a minta az URI-ra.

A részletes regisztrációs lépéseket mutatjuk be [ebben az oktatóanyagban](../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Kövesse a lépéseket a regisztráció egy **natív** alkalmazást, majd kövesse a frissítés utasításokat hozzáadása a **hátralékának** API-t a szükséges engedélyekkel. A lépéseket, jegyezze fel a **TenantId** és a **ApplicationId**, mert szüksége lesz ezekre az értékekre, az alkalmazás futtatásához.

### <a name="run-the-app"></a>Az alkalmazás futtatása

A minta futtatása előtt szerkessze az App.config fájlt, és a forgatókönyvtől függően adja meg a következő értékeket:

- `tenantId`: Állítsa be **TenantId** értéket.
- `clientId`: Állítsa be **ApplicationId** értéket. 
- `clientSecret`: Ha azt szeretné, jelentkezzen be az ügyfél titkos kulcsát, hozza létre az Azure ad-ben. Egy webalkalmazás vagy API-t is, használja a natív alkalmazás helyett. Adja hozzá az alkalmazás alatt **hozzáférés-vezérlés (IAM)** a korábban létrehozott névtér.
- `eventHubNamespaceFQDN`: Az újonnan létrehozott Event Hubs-névtér; teljesen minősített DNS-nevének beállítása Ha például `example.servicebus.windows.net`.
- `eventHubName`: Állítsa be a létrehozott eseményközpont nevére.
- Az átirányítási URI-t az alkalmazásba az előző lépésben megadott.
 
Futtassa a konzolalkalmazást, ha kéri választása; Kattintson a **interaktív felhasználói bejelentkezési** írja be a számát, és lenyomja az ENTER BILLENTYŰT. Az alkalmazás jeleníti meg a bejelentkezési ablakban, Ön hozzájárul az Event Hubs eléréséhez kéri, és a szolgáltatás segítségével a bejelentkezési azonosító használatával küldése/fogadása forgatókönyv futtatása.

## <a name="next-steps"></a>További lépések

Ha további információkat szeretne az Event Hubsról, tekintse meg az alábbi hivatkozásokat:

* Bevezetés az [Event Hubs használatába oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
* [Az Event Hubs szolgáltatás díjszabását.](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Az Event Hubsot használó mintaalkalmazások](https://github.com/Azure/azure-event-hubs/tree/master/samples)
