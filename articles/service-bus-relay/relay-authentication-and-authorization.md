---
title: Azure Relay hitelesítése és engedélyezése | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt a megosztott hozzáférésű aláírás (SAS) hitelesítésaz Azure Relay szolgáltatással.
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
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: aac5c973a99b13d5918a0162feb7f1ede443463b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514578"
---
# <a name="azure-relay-authentication-and-authorization"></a>Azure Relay hitelesítése és engedélyezése

Az alkalmazások a megosztott hozzáférésű aláírás (SAS) hitelesítésével hitelesíthetik magukat az Azure Relay számára. A SAS-hitelesítés lehetővé teszi, hogy az alkalmazások hitelesítsék magukat az Azure Relay szolgáltatás ban a továbbító névtérben konfigurált hozzáférési kulcs használatával. Ezt a kulcsot használhatja egy megosztott hozzáférésű aláírási jogkivonat létrehozásához, amelyet az ügyfelek a továbbító szolgáltatás hitelesítéséhez használhatnak.

## <a name="shared-access-signature-authentication"></a>Megosztott hozzáférésű aláírás hitelesítése

[A SAS-hitelesítés](../service-bus-messaging/service-bus-sas.md) lehetővé teszi, hogy a felhasználó számára hozzáférést biztosítson az Azure Relay adott jogosultságokkal rendelkező erőforrásaihoz. A SAS-hitelesítés egy erőforráshoz kapcsolódó jogosultságokkal rendelkező kriptográfiai kulcs konfigurációját foglalja magában. Az ügyfelek ezután hozzáférhetnek az erőforráshoz egy SAS-jogkivonat bemutatásával, amely az elérendő erőforrás URI-t és a beállított kulccsal aláírt lejárati szintet jelenti.

A SAS-kulcsokat továbbító névtérben konfigurálhatja. A Service Bus-üzenetküldéssel ellentétben [a hibrid kapcsolatok továbbítása](relay-hybrid-connections-protocol.md) támogatja a jogosulatlan vagy névtelen feladókat. Az entitás létrehozásakor engedélyezheti a névtelen hozzáférést, ahogy az a portálról a következő képernyőképen látható:

![][0]

A SAS használatához konfigurálhat egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektumot egy továbbító névtérben, amely a következőkből áll:

* A szabályt azonosító *kulcsnév.*
* *A PrimaryKey* egy sas-tokenek aláírására/érvényesítésére használt kriptográfiai kulcs.
* *SecondaryKey* egy kriptográfiai kulcs aláírására/érvényesítésére használt SAS-jogkivonatok.
* A megadott figyelési, küldési vagy kezelési jogok gyűjteményét képviselő *jogok.*

A névtér szintjén konfigurált engedélyezési szabályok hozzáférést biztosíthatnak a névtérben lévő összes továbbítókapcsolathoz a megfelelő kulccsal aláírt jogkivonatokkal rendelkező ügyfelek számára. Legfeljebb 12 ilyen engedélyezési szabály konfigurálható egy továbbító névtérben. Alapértelmezés szerint az összes jogosultsággal rendelkező [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) minden névtérhez konfigurálva van, amikor először kivan építve.

Egy entitás eléréséhez az ügyfélnek egy adott [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)használatával létrehozott SAS-jogkivonatra van szüksége. A SAS-jogkivonat egy erőforrás-karakterlánc HMAC-SHA256 használatával jön létre, amely az erőforrás URI-címéből áll, amelyhez a hozzáférést igénylik, és egy lejárati lejárati lejárati lejárata az engedélyezési szabályhoz társított kriptográfiai kulccsal.

Az Azure Relay SAS-hitelesítéstámogatása megtalálható az Azure .NET SDK 2.0-s és újabb verzióiban. A SAS támogatja a [SharedAccessAuthorizationRule programot.](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) A kapcsolati karakterláncot paraméterként fogadó összes API tartalmazza a SAS-kapcsolati karakterláncok támogatását.

## <a name="next-steps"></a>További lépések

- Folytassa a [Service Bus-hitelesítés olvasását megosztott hozzáférésű aláírásokkal](../service-bus-messaging/service-bus-sas.md) a SAS-szal kapcsolatos további részletekért.
- A hibrid kapcsolatok ról az [Azure Relay Hybrid Connections protokoll útmutatójában](relay-hybrid-connections-protocol.md) részletes információkat talál.
- A Service Bus Messaging hitelesítésével és engedélyezésével kapcsolatos megfelelő információkért lásd: [Service Bus hitelesítés és engedélyezés.](../service-bus-messaging/service-bus-authentication-and-authorization.md) 

[0]: ./media/relay-authentication-and-authorization/hcanon.png