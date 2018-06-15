---
title: Az Azure Service Bus-hitelesítés és engedélyezés |} Microsoft Docs
description: Service Bus-alkalmazások a közös hozzáférésű Jogosultságkód (SAS) hitelesítési hitelesítéséhez.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: sethm
ms.openlocfilehash: b8b5887f2003dd793ae7a50f066b893f685002a0
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
ms.locfileid: "24813929"
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus-hitelesítés és -engedélyezés

Alkalmazások közös hozzáférésű Jogosultságkód (SAS) tokent használó hitelesítés Azure Service Bus-erőforrások eléréséhez. Az SAS-alkalmazások bevezetését a jogkivonatot az aláírt Service Bus egy ismert egyaránt a jogkivonatot kibocsátó szimmetrikus kulcs és a Service Bus (a ezért "megosztás"), és kulcs közvetlenül egy adott hozzáférési jogok, például a fájlokra vonatkozó engedély szabályhoz fogadására/figyelés vagy üzeneteket küldeni. SAS-szabályok a következők vagy konfigurálva, a névtér, vagy közvetlenül entitások például üzenetsor vagy témakör, így részletes hozzáférés-vezérléshez.

SAS-tokenje kell által generált, vagy egy Service Bus-ügyfélalkalmazást közvetlenül, vagy azok válthat ki néhány köztes jogkivonatot kibocsátó végpontot, amelyhez az ügyfél kommunikál. Például a rendszer az ügyfél az Active Directory védett engedélyezési webszolgáltatási végpontot identitása és a rendszer hozzáférési jogait, és a webszolgáltatás majd értéket ad vissza a megfelelő Service Bus token igazolásához hívása lehet szükség. A SAS-jogkivonat könnyen létrehozhatja a Service Bus token szolgáltató az Azure SDK tartalmazza. 

> [!IMPORTANT]
> Ha az Azure Active Directory hozzáférés-vezérlés (más néven a hozzáférés-vezérlési szolgáltatásban vagy az ACS) használ a Service busszal, vegye figyelembe, hogy ez a módszer támogatása most korlátozott, és át kell telepíteni az alkalmazást, hogy az SAS. További információkért lásd: [ebben a blogbejegyzésben](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) és [Ez a cikk](service-bus-migrate-acs-sas.md).

## <a name="shared-access-signature-authentication"></a>Közös hozzáférésű Jogosultságkód hitelesítés

[SAS hitelesítési](service-bus-sas.md) lehetővé teszi egy felhasználó hozzáférést biztosít a Service Bus erőforrásainak adott jogosultságokkal. SAS-hitelesítés a Service Bus során konfigurálnia kell a Service Bus erőforrás társított jogosultságok titkosítási kulcsot. Ügyfelek majd férhet hozzá az adott erőforrás segítségével egy SAS-jogkivonatot, amely tartalmazza az erőforrás URI azonosítója férnek hozzá, és egy lejárati konfigurált kulccsal aláírva.

A biztonsági Társítások kulcsok állíthatja be a Service Bus-névtér. A kulcs érvényes minden üzenetküldési entitások, az adott névtérben. A Service Bus-üzenetsorok és témakörök beállíthatók a kulcsokat. SAS is támogatott [Azure továbbítási](../service-bus-relay/relay-authentication-and-authorization.md).

SAS használatával konfigurálhatja egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektum a névtér, üzenetsor vagy témakör. Ez a szabály a következő elemekből áll:

* *Kulcsnév*: a szabály azonosítja.
* *PrimaryKey*: egy SAS-tokenje bejelentkezési/ellenőrzésére használt titkosítási kulcs.
* *Másodlagos kulcs*: egy SAS-tokenje bejelentkezési/ellenőrzésére használt titkosítási kulcs.
* *Jogok*: a gyűjteményét képviseli **figyelésére**, **küldése**, vagy **kezelése** engedélyek.

A névterek szintjén konfigurált engedélyezési szabályok hozzáférési jogosultságot tud biztosítani a névtér összes entitásának ügyfelek jogkivonatokkal a megfelelő kulccsal aláírva. Legfeljebb 12 ilyen engedélyezési szabályok egy Service Bus-névtér, üzenetsor vagy témakör konfigurálhatók. Alapértelmezés szerint egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) jogosultságok mindegyikével rendelkező rendszer konfigurálja minden névtér esetében először ki van építve.

Entitást szeretne használni, az ügyfél használatához egy SAS-jogkivonat segítségével egy adott generált [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). A SAS-jogkivonat generálta HMAC-SHA256-kivonata egy erőforrás-karakterlánc, amely az erőforrás URI azonosítója, amelyhez hozzáférést igényelnek áll, és egy lejárati az engedélyezési szabály társított titkosítási kulccsal.

SAS-hitelesítési támogatás a Service Bus megtalálható az Azure .NET SDK-verziók 2.0-s és újabb verziók. SAS támogatását is magában foglalja a [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Fogadja el a kapcsolati karakterlánc paraméterként API-k közé tartozik a SAS-kapcsolati karakterláncok támogatása.

## <a name="next-steps"></a>Következő lépések

- Továbbra is olvasási [Service Bus hitelesítési megosztott hozzáférési aláírásokkal](service-bus-sas.md) SAS kapcsolatos további részletekért.
- Hogyan [telepítse át az Azure Active Directory Access Control (ACS) közös hozzáférésű Jogosultságkód engedélyezési](service-bus-migrate-acs-sas.md).
- [Módosítások ACS engedélyezett névterek](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Azure-továbbítási hitelesítési és engedélyezési megfelelő információ: [Azure továbbítási hitelesítési és engedélyezési](../service-bus-relay/relay-authentication-and-authorization.md). 

