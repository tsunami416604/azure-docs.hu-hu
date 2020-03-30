---
title: Az Azure Event Hubshoz való hozzáférés engedélyezése
description: Ez a cikk az Azure Event Hubs-erőforrásokhoz való hozzáférés engedélyezésének különböző lehetőségeiről tartalmaz tájékoztatást.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: spelluru
ms.openlocfilehash: d4304abf0ca089fbbea86f12cd03dea836db612e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368354"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Az Azure Event Hubshoz való hozzáférés engedélyezése
Minden alkalommal, amikor eseményeket/adatokat tesz közzé vagy használ fel egy eseményközpontból, az ügyfél megpróbál hozzáférni az Event Hubs erőforrásaihoz. A biztonságos erőforrásokminden egyes kérelmét engedélyezni kell, hogy a szolgáltatás biztosíthassa, hogy az ügyfél rendelkezzen az adatok közzétételéhez/felhasználásához szükséges engedélyekkel. 

Az Azure Event Hubs a következő lehetőségeket kínálja a biztonságos erőforrásokhoz való hozzáférés engedélyezéséhez:

- Azure Active Directory
- Közös hozzáférésű jogosultságkód

> [!NOTE]
> Ez a cikk az Event Hubs és az [Apache Kafka-forgatókönyvek](event-hubs-for-kafka-ecosystem-overview.md) reis vonatkozik. 

## <a name="azure-active-directory"></a>Azure Active Directory
Az Azure Active Directory (Azure AD) integráció az Event Hubs-erőforrások hoz szerepköralapú hozzáférés-vezérlés (RBAC) az ügyfél erőforrásokhoz való hozzáférésének részletes szabályozásához. A szerepköralapú hozzáférés-vezérlés (RBAC) használatával engedélyeket adhat a rendszerbiztonsági tagnak, amely lehet felhasználó, csoport vagy egyszerű alkalmazásszolgáltatás. A rendszerbiztonsági tag az Azure AD által hitelesített egy OAuth 2.0-s jogkivonat ot ad vissza. A jogkivonat segítségével engedélyezheti az Event Hubs-erőforrás elérésére irányuló kérelmet.

Az Azure AD-vel való hitelesítésről az alábbi cikkekben talál további információt:

- [Az Azure-eseményközpontoknak az Azure Active Directory használatával történő kérelmek hitelesítése](authenticate-application.md)
- [Engedélyezze a hozzáférést az Event Hubs-erőforrásokhoz az Azure Active Directory használatával.](authorize-access-azure-active-directory.md)

## <a name="shared-access-signatures"></a>Megosztott hozzáférésű aláírások 
Az Event Hubs-erőforrások megosztott hozzáférési aláírásai (SAS) korlátozott delegált hozzáférést biztosítanak az Event Hubs-erőforrásokhoz. Az aláírás érvényességi ideje vagy az általa biztosított engedélyek korlátozásának hozzáadása rugalmasságot biztosít az erőforrások kezelésében. További információ: [Authenticate using shared access signatures (SAS)](authenticate-shared-access-signature.md). 

Az Azure AD által visszaadott OAuth 2.0-s jogkivonatot használó felhasználók vagy alkalmazások engedélyezése kiváló biztonságot és egyszerű használatot biztosít a megosztott hozzáférésű aláírások (SAS) használatával. Az Azure AD-vel nem kell tárolnia a hozzáférési jogkivonatokat a kóddal, és kockáztatni a potenciális biztonsági réseket. Bár továbbra is használhatja a megosztott hozzáférésű aláírások (SAS) az Event Hubs-erőforrások részletes elérésének engedélyezéséhez, az Azure AD hasonló képességeket kínál anélkül, hogy kezelnie kellene a SAS-jogkivonatokat, vagy aggódnia kellene a sérült SAS visszavonása miatt. 

Alapértelmezés szerint az Összes Event Hubs erőforrás biztonságos, és csak a fiók tulajdonosa számára érhető el. Bár a fent ismertetett engedélyezési stratégiák bármelyikével hozzáférést biztosíthat az ügyfeleknek az Event Hub-erőforrásokhoz. Csak az Azure Resource Manager telepítési modellel létrehozott Event Hubs-erőforrások támogatják az Azure AD-hitelesítést. A Microsoft azt javasolja, hogy az Azure AD, ha lehetséges, a maximális biztonság és a könnyű használat érdekében.

A SAS használatával történő engedélyezésről az [Event Hubs-erőforrásokhoz való hozzáférés engedélyezése megosztott hozzáférési aláírásokkal című témakörben](authorize-access-shared-access-signature.md)talál további információt.

## <a name="next-steps"></a>További lépések
- Tekintse át a GitHub-tárházban közzétett [RBAC-mintákat.](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) 
- Lásd az alábbi cikkeket:
    - [Az Azure-eseményközpontoknak az Azure Active Directory használatával történő hitelesítése](authenticate-application.md)
    - [Felügyelt identitás hitelesítése az Azure Active Directoryval az Event Hubs-erőforrások eléréséhez](authenticate-managed-identity.md)
    - [Az Azure Event Hubs-ra irányuló kérelmek hitelesítése megosztott hozzáférésű aláírásokkal](authenticate-shared-access-signature.md)
    - [Hozzáférés engedélyezése az Event Hubs-erőforrásokhoz az Azure Active Directory használatával](authorize-access-azure-active-directory.md)
    - [Hozzáférés engedélyezése az Event Hubs-erőforrásokhoz megosztott hozzáférési aláírásokkal](authorize-access-shared-access-signature.md)

