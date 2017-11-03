---
title: "Az Azure Service Bus-hitelesítés és engedélyezés |} Microsoft Docs"
description: "Service Bus-alkalmazások a közös hozzáférésű Jogosultságkód (SAS) hitelesítési hitelesítéséhez."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2017
ms.author: sethm
ms.openlocfilehash: b4b9d5d272bdb172f1d40db379a519a4f617550a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus-hitelesítés és -engedélyezés

Alkalmazások közös hozzáférésű Jogosultságkód (SAS) token hitelesítést használó Azure Service Bus-funkciók eléréséhez. Az SAS-alkalmazások bevezetését a jogkivonatot az aláírt Service Bus egy ismert egyaránt a jogkivonatot kibocsátó szimmetrikus kulcs és a Service Bus ("megosztás"), és kulcs közvetlenül egy adott hozzáférési jogok, például a fogadási engedéllyel szabályhoz / Figyelje, illetve üzeneteket küldeni. SAS-szabályok a következők vagy konfigurálva, a névtér, vagy közvetlenül entitások, például üzenetsor vagy témakör, így részletes hozzáférés-vezérléshez.

SAS-tokenje kell által generált, vagy egy Service Bus-ügyfélalkalmazást közvetlenül, vagy azok válthat ki néhány köztes jogkivonatot kibocsátó végpont, amely az ügyfél kommunikál. Például egy rendszernek az ügyfél az Active Directory védett engedélyezési webszolgáltatási végpontot az azonosítási és rendszerinformációk hozzáférési jogok igazolásához hívni, és a webszolgáltatás fog majd térjen vissza a megfelelő Service Bus-jogkivonat. A SAS-jogkivonat könnyen létrehozhatja a Service Bus token szolgáltató, az SDK tartalmazza. 

> [!IMPORTANT]
> Azure Active Directory hozzáférés-vezérlés (más néven a hozzáférés-vezérlési szolgáltatásban vagy az ACS) használatakor a Service Bus együtt vegye figyelembe, hogy ez a módszer támogatása most korlátozott, és át kell telepíteni az alkalmazást a SAS használatával. További információkért lásd: [ebben a blogbejegyzésben](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).

## <a name="shared-access-signature-authentication"></a>Közös hozzáférésű Jogosultságkód hitelesítés

[SAS hitelesítési](service-bus-sas.md) lehetővé teszi egy felhasználó hozzáférést biztosít a Service Bus erőforrásainak adott jogosultságokkal. SAS-hitelesítés a Service Bus során konfigurálnia kell a Service Bus erőforrás társított jogosultságok titkosítási kulcsot. Ügyfelek majd férhet hozzá az adott erőforrás segítségével egy SAS-jogkivonatot, amely tartalmazza az erőforrás URI azonosítója férnek hozzá, és egy lejárati konfigurált kulccsal aláírva.

A biztonsági Társítások kulcsok állíthatja be a Service Bus-névtér. A kulcsot minden üzenetküldési entitások, hogy a névtér vonatkozik. A Service Bus-üzenetsorok és témakörök beállíthatók a kulcsokat. SAS is támogatott [Azure továbbítási](../service-bus-relay/relay-authentication-and-authorization.md).

SAS használatával konfigurálhatja egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektum a névtér, üzenetsor vagy témakör. Ez a szabály a következő elemekből áll:

* *Kulcsnév* , amely azonosítja a szabályt.
* *PrimaryKey* egy SAS-tokenje bejelentkezési/ellenőrzésére használt titkosítási kulcs.
* *Másodlagos kulcs* egy SAS-tokenje bejelentkezési/ellenőrzésére használt titkosítási kulcs.
* *Jogok* képviselő figyelési gyűjteménye, küldjön, vagy engedélyek kezeléséhez.

A névterek szintjén konfigurált engedélyezési szabályok hozzáférési jogosultságot tud biztosítani a névtér összes entitásának ügyfelek jogkivonatokkal a megfelelő kulccsal aláírva. Legfeljebb 12 ilyen engedélyezési szabályok is lehet konfigurálni egy Service Bus-névtér, üzenetsor vagy témakör. Alapértelmezés szerint egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) jogosultságok mindegyikével rendelkező rendszer konfigurálja minden névtér esetében először ki van építve.

Entitást szeretne használni, az ügyfél használatához egy SAS-jogkivonat segítségével egy adott generált [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). A SAS-jogkivonat generálta HMAC-SHA256-kivonata egy erőforrás-karakterlánc, amely az erőforrás URI azonosítója, amelyhez hozzáférést igényelnek áll, és egy lejárati az engedélyezési szabály társított titkosítási kulccsal.

SAS-hitelesítési támogatás a Service Bus megtalálható az Azure .NET SDK-verziók 2.0-s és újabb verziók. SAS támogatását is magában foglalja a [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Fogadja el a kapcsolati karakterlánc paraméterként API-k közé tartozik a SAS-kapcsolati karakterláncok támogatása.

## <a name="next-steps"></a>Következő lépések

- Továbbra is olvasási [Service Bus hitelesítési megosztott hozzáférési aláírásokkal](service-bus-sas.md) SAS kapcsolatos további részletekért.
- [Módosítások ACS engedélyezett névterek.](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/)
- Azure-továbbítási hitelesítési és engedélyezési megfelelő információ: [Azure továbbítási hitelesítési és engedélyezési](../service-bus-relay/relay-authentication-and-authorization.md). 

