---
title: "Az Azure továbbítási hitelesítési és engedélyezési |} Microsoft Docs"
description: "Az Azure-továbbítási közös hozzáférésű Jogosultságkód (SAS) hitelesítési áttekintése"
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 4ef8cbf22f2fcd7017af16083240608e5ca0fb5e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-relay-authentication-and-authorization"></a>Az Azure továbbítási hitelesítés és engedélyezés

Alkalmazások Azure irányítja közös hozzáférésű Jogosultságkód (SAS) hitelesítés használatával képes hitelesíteni. SAS hitelesítési lehetővé teszi, hogy a konfigurált a továbbítási névtér hozzáférési kulcs használata Azure Relay szolgáltatással való hitelesítésre szolgáló alkalmazások. Ez a kulcs segítségével majd, amellyel az ügyfelek a relay szolgáltatással való hitelesítésre szolgáló közös hozzáférésű Jogosultságkód-token létrehozásához.

## <a name="shared-access-signature-authentication"></a>Közös hozzáférésű Jogosultságkód hitelesítés
[SAS hitelesítési](../service-bus-messaging/service-bus-sas.md) lehetővé teszi egy felhasználó hozzáférést biztosíthat a meghatározott jogosultsággal rendelkező Azure továbbítási erőforrásokhoz. SAS hitelesítési során konfigurálnia kell a titkosítási kulcsot egy erőforrás társított jogosultságok. Ügyfelek majd férhet hozzá az adott erőforrás segítségével egy SAS-jogkivonatot, amely tartalmazza az erőforrás URI azonosítója férnek hozzá, és egy lejárati konfigurált kulccsal aláírva.

A biztonsági Társítások kulcsok továbbítási névtérben állíthatja be. Service Bus üzenetkezelés, eltérően [hibrid kapcsolatok](relay-hybrid-connections-protocol.md) nem hitelesített vagy névtelen feladók támogatja. Engedélyezheti a névtelen hozzáférést az entitás létrehozásakor, ahogy az az alábbi képernyőképen megtekinthet a portálról:

![][0]

SAS használatával konfigurálhatja egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektum továbbítási névtérben, amely a következőket tartalmazza:

* *Kulcsnév* , amely azonosítja a szabályt.
* *PrimaryKey* egy SAS-tokenje bejelentkezési/ellenőrzésére használt titkosítási kulcs.
* *Másodlagos kulcs* egy SAS-tokenje bejelentkezési/ellenőrzésére használt titkosítási kulcs.
* *Jogok* képviselő figyelési gyűjteménye, küldjön, vagy engedélyek kezeléséhez.

Az engedélyezési szabályok a névterek szintjén konfigurált ügyfelek összes továbbító-kapcsolatok a névtér hozzáférést biztosíthat a megfelelő kulcsával aláírt jogkivonatokat. Legfeljebb 12 ilyen engedélyezési szabályok is lehet konfigurálni a továbbítási névtér. Alapértelmezés szerint egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) jogosultságok mindegyikével rendelkező rendszer konfigurálja minden névtér esetében először ki van építve.

Entitást szeretne használni, az ügyfél használatához egy SAS-jogkivonat segítségével egy adott generált [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). A SAS-jogkivonat generálta HMAC-SHA256-kivonata egy erőforrás-karakterlánc, amely az erőforrás URI azonosítója, amelyhez hozzáférést igényelnek áll, és egy lejárati az engedélyezési szabály társított titkosítási kulccsal.

SAS-hitelesítési támogatás a Azure továbbítási megtalálható az Azure .NET SDK-verziók 2.0-s és újabb verziók. SAS támogatását is magában foglalja a [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Fogadja el a kapcsolati karakterlánc paraméterként API-k közé tartozik a SAS-kapcsolati karakterláncok támogatása.

## <a name="next-steps"></a>Következő lépések
- Továbbra is olvasási [Service Bus hitelesítési megosztott hozzáférési aláírásokkal](../service-bus-messaging/service-bus-sas.md) SAS kapcsolatos további részletekért.
- Tekintse meg a [Azure hibrid kapcsolatok protokoll útmutató](relay-hybrid-connections-protocol.md) a hibrid kapcsolatok képességgel kapcsolatos részletes információkat.
- Service Bus üzenetkezelés hitelesítési és engedélyezési megfelelő információ: [Service Bus hitelesítési és engedélyezési](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png