---
title: Azure Service Bus hitelesítése és engedélyezése | Microsoft dokumentumok
description: Hitelesítse az alkalmazásokat a Service Bus-ba megosztott hozzáférésű aláírással (SAS) hitelesítéssel.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 7234e33c04e742c77630f8d87481c7831fb00bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70013245"
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus-hitelesítés és -engedélyezés

Az alkalmazások megosztott hozzáférésű aláírás (SAS) tokenhitelesítéssel férhetnek hozzá az Azure Service Bus erőforrásaihoz. A SAS-szal az alkalmazások egy olyan jogkivonatot mutatnak be a Service Busnak, amely a jogkivonat-kibocsátó és a Service Bus (így a "megosztott") által ismert szimmetrikus kulccsal van aláírva, és ez a kulcs közvetlenül társítva van egy adott hozzáférési jogokat megadó szabályhoz, például az üzenetek fogadására/figyelésére vagy küldésére vonatkozó engedéllyel. A SAS-szabályok vagy a névtéren vannak konfigurálva, vagy közvetlenül az entitásokon, például egy várólistán vagy témakörön, lehetővé téve a részletes hozzáférés-vezérlést.

A SAS-jogkivonatok közvetlenül is létrehozható egy Service Bus-ügyfél által, vagy létrehozható valamilyen köztes jogkivonat-kibocsátó végpont, amellyel az ügyfél kommunikál. Például egy rendszer megkövetelheti az ügyféltől, hogy hívja meg az Active Directory engedélyezési védett webszolgáltatás-végpontot az identitás és a rendszer hozzáférési jogainak igazolására, és a webszolgáltatás ezután visszaadja a megfelelő Service Bus-jogkivonatot. Ez a SAS-jogkivonat könnyen létrehozható az Azure SDK-ban található Service Bus jogkivonat-szolgáltató használatával. 

> [!IMPORTANT]
> Ha az Azure Active Directory hozzáférés-vezérlési (más néven hozzáférés-vezérlési szolgáltatás vagy ACS) a Service Bus, vegye figyelembe, hogy a támogatás a módszer már korlátozott, és át kell telepítenie az alkalmazást a SAS használatához. További információt [ebben a blogbejegyzésben](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) és [ebben a cikkben](service-bus-migrate-acs-sas.md)talál.

## <a name="azure-active-directory"></a>Azure Active Directory
Az Azure Active Directory (Azure AD) integráció a Service Bus-erőforrások szerepköralapú hozzáférés-vezérlés (RBAC) az ügyfél erőforrásokhoz való hozzáférésének részletes szabályozásához. A szerepköralapú hozzáférés-vezérlés (RBAC) használatával engedélyeket adhat a rendszerbiztonsági tagnak, amely lehet felhasználó, csoport vagy egyszerű alkalmazásszolgáltatás. A rendszerbiztonsági tag az Azure AD által hitelesített egy OAuth 2.0-s jogkivonat ot ad vissza. A jogkivonat egy Service Bus-erőforrás (várólista, témakör stb.) elérésére irányuló kérelem engedélyezésére használható.

Az Azure AD-vel való hitelesítésről az alábbi cikkekben talál további információt:

- [Hitelesítés felügyelt identitásokkal](service-bus-managed-service-identity.md)
- [Hitelesítés egy alkalmazásból](authenticate-application.md)

> [!IMPORTANT]
> Az Azure AD által visszaadott OAuth 2.0-s jogkivonatot használó felhasználók vagy alkalmazások engedélyezése kiváló biztonságot és egyszerű használatot biztosít a megosztott hozzáférésű aláírások (SAS) használatával. Az Azure AD-vel nincs szükség a jogkivonatok tárolására a kódban, és potenciális biztonsági rések et kockáztat. Azt javasoljuk, hogy az Azure AD használatával az Azure Service Bus-alkalmazások, ha lehetséges. 


## <a name="shared-access-signature"></a>Közös hozzáférésű jogosultságkód
[A SAS-hitelesítés](service-bus-sas.md) lehetővé teszi, hogy a felhasználó számára hozzáférést biztosítson a Service Bus erőforrásaihoz, adott jogosultságokkal. A Service Bus SAS-hitelesítése magában foglalja a Service Bus-erőforrástársított jogosultságokkal rendelkező kriptográfiai kulcs ának konfigurálását. Az ügyfelek ezután hozzáférhetnek az erőforráshoz egy SAS-jogkivonat bemutatásával, amely az elérendő erőforrás URI-t és a beállított kulccsal aláírt lejárati szintet jelenti.

A SAS kulcsait a Service Bus névtérben konfigurálhatja. A kulcs a névtérben lévő összes üzenetkezelő entitásra vonatkozik. Kulcsokat is konfigurálhat a Service Bus várólistákés témakörök. A SAS az [Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md)is támogatott.

A SAS használatához konfigurálhat egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektumot névtérben, várólistán vagy témakörben. Ez a szabály a következő elemekből áll:

* *KeyName*: azonosítja a szabályt.
* *PrimaryKey*: a SAS-jogkivonatok aláírására/érvényesítésére használt kriptográfiai kulcs.
* *SecondaryKey*: SAS-jogkivonatok aláírására/érvényesítésére használt kriptográfiai kulcs.
* *Jogok*: a **megítélt Figyelés,** **Küldés**vagy **Kezelés** jogok gyűjteményét jelöli.

A névtér szintjén konfigurált engedélyezési szabályok hozzáférést biztosíthatnak a névtérben lévő összes entitásszámára a megfelelő kulccsal aláírt jogkivonatokkal rendelkező ügyfelek számára. Legfeljebb 12 ilyen engedélyezési szabályt konfigurálhat egy Service Bus-névtérben, várólistán vagy témakörben. Alapértelmezés szerint az összes jogosultsággal rendelkező [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) minden névtérhez konfigurálva van, amikor először kivan építve.

Egy entitás eléréséhez az ügyfélnek egy adott [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)használatával létrehozott SAS-jogkivonatra van szüksége. A SAS-jogkivonat egy erőforrás-karakterlánc HMAC-SHA256 használatával jön létre, amely az erőforrás URI-címéből áll, amelyhez a hozzáférést igénylik, és egy lejárati lejárati lejárati lejárata az engedélyezési szabályhoz társított kriptográfiai kulccsal.

A Service Bus SAS-hitelesítéstámogatása megtalálható az Azure .NET SDK 2.0-s és újabb verzióiban. A SAS támogatja a [SharedAccessAuthorizationRule programot.](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) A kapcsolati karakterláncot paraméterként fogadó összes API tartalmazza a SAS-kapcsolati karakterláncok támogatását.

## <a name="next-steps"></a>További lépések

- Folytassa a [Service Bus-hitelesítés olvasását megosztott hozzáférésű aláírásokkal](service-bus-sas.md) a SAS-szal kapcsolatos további részletekért.
- Az [Azure Active Directory hozzáférés-vezérlési szolgáltatásból a megosztott hozzáférés-aláírás engedélyezésére való áttelepítés.](service-bus-migrate-acs-sas.md)
- [Változások az ACS-alapú névtereken.](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/)
- Az Azure Relay hitelesítésével és engedélyezésével kapcsolatos megfelelő információkért lásd: [Azure Relay hitelesítés és engedélyezés.](../service-bus-relay/relay-authentication-and-authorization.md) 

