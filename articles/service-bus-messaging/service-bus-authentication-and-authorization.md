---
title: Hitelesítés és engedélyezés Azure Service Bus | Microsoft Docs
description: Az alkalmazások hitelesítése megosztott hozzáférés-aláírási (SAS-) hitelesítéssel Service Bus.
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
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013245"
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus-hitelesítés és -engedélyezés

Az alkalmazások közös hozzáférésű aláírás (SAS) jogkivonat-hitelesítés használatával férnek hozzá Azure Service Bus erőforrásokhoz. Az SAS-vel az alkalmazások olyan Service Bus jogkivonatot kapnak, amely egy, a jogkivonat-kiállító és a Service Bus (azaz "Shared") által ismert szimmetrikus kulccsal van aláírva, és a kulcs közvetlenül társítva van egy olyan szabállyal, amely konkrét hozzáférési jogokat biztosít, például a következőhöz: üzenetek fogadása/figyelése vagy küldése. A SAS-szabályok a névtérben vannak konfigurálva, vagy közvetlenül olyan entitásokon, mint például a várólista vagy a témakör, ami lehetővé teszi a részletes hozzáférés-vezérlést.

Az SAS-jogkivonatokat közvetlenül egy Service Bus-ügyfél hozhatja létre, vagy egy olyan közbenső jogkivonat-kiállítói végpont generálhatja, amellyel az ügyfél kommunikál. Előfordulhat például, hogy egy rendszer megköveteli, hogy az ügyfél meghívjon egy Active Directory hitelesítéssel védett webszolgáltatás-végpontot, hogy igazolja az identitását és a rendszer hozzáférési jogosultságát, és a webszolgáltatás ezt követően visszaadja a megfelelő Service Bus tokent. Ez az SAS-token egyszerűen létrehozható az Azure SDK-ban foglalt Service Bus jogkivonat-szolgáltató használatával. 

> [!IMPORTANT]
> Ha Azure Active Directory Access Controlt (más néven Access Control Service vagy ACS) használ Service Bus, vegye figyelembe, hogy a metódus támogatása már korlátozott, és az alkalmazást az SAS használatára kell áttelepítenie. További információt [ebben](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) a blogbejegyzésben és [a cikkben](service-bus-migrate-acs-sas.md)talál.

## <a name="azure-active-directory"></a>Azure Active Directory
A Azure Active Directory (Azure AD) integrációja Service Bus erőforrásokhoz szerepköralapú hozzáférés-vezérlést (RBAC) biztosít az ügyfelek erőforrásaihoz való hozzáférés részletes szabályozásához. A szerepköralapú hozzáférés-vezérlés (RBAC) segítségével engedélyeket biztosíthat a rendszerbiztonsági tag számára, amely lehet egy felhasználó, egy csoport vagy egy egyszerű alkalmazás. A rendszerbiztonsági tag hitelesítése az Azure AD által OAuth 2,0 token visszaküldésével történik. A token használatával engedélyezhető egy Service Bus erőforrás elérésére irányuló kérés (Üzenetsor, témakör stb.).

Az Azure AD-vel történő hitelesítéssel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Hitelesítés felügyelt identitásokkal](service-bus-managed-service-identity.md)
- [Hitelesítés alkalmazásból](authenticate-application.md)

> [!IMPORTANT]
> Az Azure AD által visszaadott OAuth 2,0 tokent használó felhasználók vagy alkalmazások engedélyezése kiváló biztonságot és egyszerű használatot biztosít a közös hozzáférésű aláírások (SAS) számára. Az Azure AD-ben nincs szükség a jogkivonatok tárolására a kódban, és kockázatos biztonsági réseket. Azt javasoljuk, hogy ha lehetséges, az Azure AD-t használja a Azure Service Bus alkalmazásaihoz. 


## <a name="shared-access-signature"></a>Közös hozzáférésű jogosultságkód
Az [sas-hitelesítés](service-bus-sas.md) lehetővé teszi a felhasználók számára, hogy hozzáférést biztosítson Service Bus erőforrásokhoz adott jogokkal. A Service Bus SAS-hitelesítése egy Service Bus erőforráson társított jogokkal rendelkező titkosítási kulcs konfigurációját foglalja magában. Az ügyfelek ezután egy SAS-token bemutatásával férhetnek hozzá ehhez az erőforráshoz, amely az elérni kívánt erőforrás-URI-t és a beállított kulccsal lejáró lejárati értéket tartalmazza.

Az SAS kulcsait Service Bus névtérben is konfigurálhatja. A kulcs az adott névtéren belüli összes üzenetküldési entitásra vonatkozik. A kulcsokat Service Bus várólistákon és témakörökben is konfigurálhatja. Az SAS [Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md)is támogatott.

Ha SAS-t szeretne használni, konfigurálhat egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -objektumot egy névtérben, egy várólistán vagy egy témakörben. Ez a szabály a következő elemekből áll:

* *Kulcsnév*: a szabályt azonosítja.
* *PrimaryKey*: a sas-jogkivonatok aláírására/érvényesítésére használt titkosítási kulcs.
* *Értesítésiközpont*: a sas-jogkivonatok aláírására/érvényesítésére használt titkosítási kulcs.
* *Rights*: a megadható **figyelési**, **küldési**vagy **kezelési** jogosultságok gyűjteményét jelöli.

A névtér szintjén konfigurált engedélyezési szabályok hozzáférést biztosíthatnak a névtérben lévő összes entitáshoz a megfelelő kulccsal aláírt tokenekkel rendelkező ügyfelek számára. Egy Service Bus névtérben, várólistán vagy témakörön legfeljebb 12 ilyen engedélyezési szabályt állíthat be. Alapértelmezés szerint minden egyes jogosultsággal rendelkező [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) konfigurálva van minden névtérhez, amikor először kiépítik őket.

Egy entitás eléréséhez az ügyfélnek egy adott [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)létrehozott sas-tokent kell használnia. Az SAS-tokent egy olyan erőforrás-karakterlánc HMAC-SHA256 hozza létre, amely a hozzáférést igényelő erőforrás-URI-t, valamint az engedélyezési szabályhoz társított titkosítási kulccsal lejáró lejáratot tartalmaz.

A Service Bus SAS-hitelesítésének támogatása az Azure .NET SDK 2,0-as és újabb verzióiban érhető el. Az SAS a [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)támogatását is támogatja. Minden olyan API-t, amely a paramétert fogadja, az SAS-kapcsolatok karakterláncok támogatását tartalmazza.

## <a name="next-steps"></a>További lépések

- A SAS-vel kapcsolatos további részletekért olvassa [el Service Bus hitelesítés közös hozzáférési aláírásokkal](service-bus-sas.md) című cikkét.
- Migrálás [Azure Active Directory Access Controlról (ACS) a közös hozzáférésű aláírás engedélyezésére](service-bus-migrate-acs-sas.md).
- [Az ACS-kompatibilis névterek változásai](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- A Azure Relay hitelesítéssel és engedélyezéssel kapcsolatos megfelelő információkért lásd: [Azure Relay hitelesítés és engedélyezés](../service-bus-relay/relay-authentication-and-authorization.md). 

