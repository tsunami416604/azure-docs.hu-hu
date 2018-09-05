---
title: Az Azure Service Bus-hitelesítés és engedélyezés |} A Microsoft Docs
description: Hitelesítés közös hozzáférésű Jogosultságkód (SAS) hitelesítés a Service Bus az alkalmazások.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: spelluru
ms.openlocfilehash: e98d980747edfb9987430a635ff3118cdd455828
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702321"
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus-hitelesítés és -engedélyezés

Alkalmazások közös hozzáférésű Jogosultságkód (SAS) tokent használó hitelesítés biztosítását az Azure Service Bus-erőforrások eléréséhez. Az SAS-alkalmazások jelenthet egy jogkivonatot a Service Bus az aláírt egy ismert mindkettőt a jogkivonat kibocsátója szimmetrikus kulcsot és a Service Bus (a ezért "megosztott"), és a kulcs közvetlenül egy adott hozzáférési jogok, például a szükséges engedély szabályhoz társított fogadása és figyelés, vagy üzeneteket küldhet. SAS-szabályok a következők vagy konfigurálva, a névteret, vagy közvetlenül entitások, például egy üzenetsorba vagy témakörbe, így részletes hozzáférés-vezérlést.

SAS-tokeneket generált, vagy lehet egy Service Bus-ügyfél által közvetlenül, vagy azok hozhat létre néhány köztes jogkivonat kiállítása végpont, amelyhez az ügyfél kommunikál. Például a rendszernek az ügyfél számára egy Active Directory védett engedélyezési webszolgáltatási végpontot az identitása és a rendszer hozzáférési jogosultságokat, és a webszolgáltatás majd értéket ad vissza a megfelelő Service Bus token igazolásához hívja. A SAS-jogkivonat egyszerűen létrehozható használatával a Service Bus jogkivonat-szolgáltatót az Azure SDK-ban található. 

> [!IMPORTANT]
> Ha az Azure Active Directory hozzáférés-vezérlés (más néven az Access Control Service szolgáltatást vagy az ACS) használata a Service Bus, vegye figyelembe, hogy ez a módszer támogatása jelenleg korlátozott, és át kell telepítenie az alkalmazást, hogy az SAS. További információkért lásd: [ebben a blogbejegyzésben](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) és [Ez a cikk](service-bus-migrate-acs-sas.md).

## <a name="shared-access-signature-authentication"></a>Közös hozzáférésű Jogosultságkód-hitelesítés

[SAS hitelesítési](service-bus-sas.md) lehetővé teszi, hogy egy felhasználó hozzáférést biztosít a Service Bus-erőforrások, a megadott jogokat. SAS-hitelesítés a Service Bus magában foglalja egy titkosítási kulcs társított jogosultságokkal a Service Bus erőforrás konfigurációját. Az ügyfelek így az ezekből nyerhető erőforráshoz való hozzáférést egy SAS-token, amely tartalmazza az erőforrás-URI elért szabályzatkérelem, és a egy lejárati aláírva a konfigurált kulcs.

Kulcsok a Service Bus-névtér SAS konfigurálható. A kulcs a névtéren belül minden üzenetküldési entitások vonatkozik. A Service Bus-üzenetsorok és témakörök beállíthatók a kulcsokat. Szintén támogatott az SAS [Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md).

SAS használatával konfigurálhat egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektum névtér, üzenetsor vagy témakör. Ez a szabály a következő elemekből áll:

* *Kulcsnév*: azonosítja a szabályt.
* *PrimaryKey*: egy titkosítási kulcs érvényesítéséhez használt bejelentkezési/SAS-tokeneket.
* *Másodlagos kulcs*: egy titkosítási kulcs érvényesítéséhez használt bejelentkezési/SAS-tokeneket.
* *Jogok*: gyűjteményét jelölik **figyelésére**, **küldése**, vagy **kezelés** megadott jogokat.

A névterek szintjén konfigurált engedélyezési szabályokat adhat hozzáférést a névtérben lévő összes entitáshoz ügyfelek számára a megfelelő kulcsával aláírt jogkivonatokat. A Service Bus-névtér, üzenetsor vagy témakör legfeljebb 12 ilyen engedélyezési szabályok is beállíthatja. Alapértelmezés szerint egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) jogosultságok mindegyikével rendelkező van konfigurálva névtereinek első helyezésekor.

Egy entitás eléréséhez az ügyfélnek szüksége van egy adott használatával létrehozott SAS-tokent [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). A SAS-jogkivonat jön létre a HMAC-SHA256-kivonata egy erőforrás-karakterlánc, amely az erőforrás URI azonosítója, amelyhez hozzáférést igényelnek áll, és a egy lejárati használatával az engedélyezési szabály társított titkosítási kulccsal.

SAS-hitelesítési támogatás a Service Bus részeként elérhető az Azure .NET SDK-verziók 2.0-s és újabb verziók. SAS-támogatást tartalmaz egy [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Minden API-k, amelyek egy kapcsolati karakterlánc paraméterként fogadják közé tartozik a SAS-kapcsolati karakterláncok támogatása.

## <a name="next-steps"></a>További lépések

- Olvassa tovább [Service Bus-hitelesítés közös hozzáférésű jogosultságkódokkal](service-bus-sas.md) SAS kapcsolatos további részletekért.
- Hogyan [áttelepítése az Azure Active Directory Access Control (ACS) közös hozzáférésű Jogosultságkód engedélyezési](service-bus-migrate-acs-sas.md).
- [Módosítások ACS engedélyezni névterek](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Kapcsolódó információ az Azure Relay-hitelesítés és engedélyezés: [Azure Relay hitelesítési és engedélyezési](../service-bus-relay/relay-authentication-and-authorization.md). 

