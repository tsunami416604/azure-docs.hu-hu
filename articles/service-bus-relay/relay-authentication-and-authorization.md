---
title: Az Azure Relay-hitelesítés és engedélyezés |} A Microsoft Docs
description: Közös hozzáférésű Jogosultságkód (SAS) hitelesítés az Azure Relay áttekintése
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: fff484f2ee6722083a34383b8960931cb37b8316
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700446"
---
# <a name="azure-relay-authentication-and-authorization"></a>Az Azure Relay-hitelesítés és engedélyezés

Alkalmazások az Azure Relay-hitelesítés közös hozzáférésű Jogosultságkód (SAS) használatával hitelesítheti. SAS hitelesítési lehetővé teszi az alkalmazások az Azure Relay szolgáltatás konfigurált a Relay-névtér hozzáférési kulcs használatával történő hitelesítéséhez. Ezután használhatja ezt a kulcsot, hozzon létre egy közös hozzáférésű Jogosultságkód tokent, amellyel az ügyfelek a relay szolgáltatással való hitelesítésre.

## <a name="shared-access-signature-authentication"></a>Közös hozzáférésű Jogosultságkód-hitelesítés

[SAS hitelesítési](../service-bus-messaging/service-bus-sas.md) lehetővé teszi, hogy egy felhasználó hozzáférést biztosítani Azure Relay-erőforrás meghatározott jogosultságokkal. SAS hitelesítési egy titkosítási kulcs az erőforrás társított jogosultsággal rendelkező konfigurációja magában foglalja. Az ügyfelek így az ezekből nyerhető erőforráshoz való hozzáférést egy SAS-token, amely tartalmazza az erőforrás-URI elért szabályzatkérelem, és a egy lejárati aláírva a konfigurált kulcs.

Kulcsok a Relay-névtér SAS konfigurálható. A Service Bus-üzenetkezelés, eltérően [Relay hibrid kapcsolatai](relay-hybrid-connections-protocol.md) támogatja a nem hitelesített vagy névtelen feladók. Engedélyezheti a névtelen hozzáférés az entitás létrehozásakor, a portálon az alábbi képernyőfelvételen látható módon:

![][0]

SAS használatával konfigurálhat egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektum a Relay-névtér, amely a következőket tartalmazza:

* *Kulcsnév* , amely azonosítja a szabályt.
* *PrimaryKey* egy titkosítási kulcs érvényesítéséhez használt bejelentkezési/SAS-tokeneket.
* *Másodlagos kulcs* egy titkosítási kulcs érvényesítéséhez használt bejelentkezési/SAS-tokeneket.
* *Jogok* jelölő Listen gyűjteményét, vagy a megadott jogokat kezelése.

A névterek szintjén konfigurált engedélyezési szabályokat adhat hozzáférést az ügyfelek egy névtér összes relay kapcsolat a megfelelő kulcsával aláírt jogkivonatokat. Legfeljebb 12 ilyen engedélyezési szabályok konfigurálhatók a Relay-névteret. Alapértelmezés szerint egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) jogosultságok mindegyikével rendelkező van konfigurálva névtereinek első helyezésekor.

Egy entitás eléréséhez az ügyfélnek szüksége van egy adott használatával létrehozott SAS-tokent [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). A SAS-jogkivonat jön létre a HMAC-SHA256-kivonata egy erőforrás-karakterlánc, amely az erőforrás URI azonosítója, amelyhez hozzáférést igényelnek áll, és a egy lejárati használatával az engedélyezési szabály társított titkosítási kulccsal.

SAS-hitelesítési támogatás a Azure Relay részeként elérhető az Azure .NET SDK-verziók 2.0-s és újabb verziók. SAS-támogatást tartalmaz egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Minden API-k, amelyek egy kapcsolati karakterlánc paraméterként fogadják közé tartozik a SAS-kapcsolati karakterláncok támogatása.

## <a name="next-steps"></a>További lépések

- Olvassa tovább [Service Bus-hitelesítés közös hozzáférésű jogosultságkódokkal](../service-bus-messaging/service-bus-sas.md) SAS kapcsolatos további részletekért.
- Tekintse meg a [Azure Relay hibrid kapcsolatok protokoll – útmutató](relay-hybrid-connections-protocol.md) a hibrid kapcsolatok képesség kapcsolatos részletes információkat.
- Service Bus-üzenetkezelés hitelesítési és engedélyezési információ vonatkozó információkért lásd: [Service Bus-hitelesítés és engedélyezés](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png