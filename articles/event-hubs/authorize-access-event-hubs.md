---
title: Hozzáférés engedélyezése az Azure Event Hubs
description: Ez a cikk az Azure Event Hubs-erőforrásokhoz való hozzáférés engedélyezésének különböző lehetőségeiről nyújt információt.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 9847f2f51da57526f84a7fcb3929e95ae267546c
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992548"
---
# <a name="authorize-access-to-azure-event-hubs"></a>Hozzáférés engedélyezése az Azure Event Hubs
Minden alkalommal, amikor egy Event hub-ből tesz közzé vagy használ eseményeket/információkat, az ügyfél megpróbál hozzáférni Event Hubs erőforrásokhoz. A biztonságos erőforrásokra vonatkozó minden kérést engedélyezni kell, hogy a szolgáltatás biztosítsa, hogy az ügyfél rendelkezik a szükséges engedélyekkel az adatközzétételhez/felhasználáshoz. 

Az Azure Event Hubs a következő lehetőségeket kínálja a biztonságos erőforrásokhoz való hozzáférés engedélyezéséhez:

## <a name="azure-active-directory"></a>Azure Active Directory
A Azure Active Directory (Azure AD) integrációja Event Hubs erőforrásokhoz szerepköralapú hozzáférés-vezérlést (RBAC) biztosít az ügyfelek erőforrásaihoz való hozzáférés részletes szabályozásához. A szerepköralapú hozzáférés-vezérlés (RBAC) segítségével engedélyeket biztosíthat a rendszerbiztonsági tag számára, amely lehet egy felhasználó, egy csoport vagy egy egyszerű alkalmazás. A rendszerbiztonsági tag hitelesítése az Azure AD által OAuth 2,0 token visszaküldésével történik. A token használatával engedélyezhető egy Event Hubs erőforrás elérésére irányuló kérés.

Az Azure AD-val végzett hitelesítéssel kapcsolatos további információkért lásd: [kérelmek hitelesítése az azure Event Hubs Azure Active Directory használatával](authenticate-application.md). Az Azure AD-vel való engedélyezéssel kapcsolatos további információkért lásd: [hozzáférés engedélyezése Event Hubs erőforrásokhoz Azure Active Directory használatával](authorize-access-azure-active-directory.md).

## <a name="share-access-signatures"></a>Hozzáférési aláírások megosztása 
A Event Hubs erőforrásokhoz tartozó közös hozzáférésű aláírások (SAS) korlátozott delegált hozzáférést biztosítanak Event Hubs erőforrásaihoz. Megkötések hozzáadása olyan időintervallumhoz, amely esetében az aláírás érvényes, vagy az általa nyújtott engedélyekkel az erőforrások kezelése rugalmasságot biztosít. További információ: a [hitelesítés közös hozzáférésű aláírásokkal (SAS)](authenticate-shared-access-signature.md). 

Az Azure AD által visszaadott OAuth 2,0 tokent használó felhasználók vagy alkalmazások engedélyezése kiváló biztonságot és egyszerű használatot biztosít a közös hozzáférésű aláírások (SAS) számára. Az Azure AD-ben nem kell a hozzáférési jogkivonatokat a kóddal és a potenciális biztonsági rések használatával tárolni. Habár továbbra is használhatja a közös hozzáférésű aláírásokat (SAS) a Event Hubs erőforrások részletes elérésének biztosításához, az Azure AD hasonló képességeket kínál anélkül, hogy az SAS-tokeneket kellene kezelnie, vagy nem kell aggódnia a sérült SAS visszavonásával kapcsolatban. 

Alapértelmezés szerint az összes Event Hubs erőforrás biztonságos, és csak a fiók tulajdonosa számára érhető el. Bár a fentiekben ismertetett hitelesítési stratégiák bármelyikét felhasználhatja az ügyfelek számára az Event hub erőforrásaihoz való hozzáférés biztosításához. Csak a Azure Resource Manager üzembe helyezési modellel létrehozott Event Hubs erőforrások támogatják az Azure AD-hitelesítést. A Microsoft az Azure AD használatát javasolja, ha lehetséges, a maximális biztonság és a könnyű használat érdekében.

Az SAS használatával történő engedélyezéssel kapcsolatos további információkért lásd: [hozzáférés engedélyezése Event Hubs erőforrásokhoz közös hozzáférési aláírások használatával](authorize-access-shared-access-signature.md).

## <a name="next-steps"></a>További lépések
- Tekintse át a GitHub-tárházban közzétett [RBAC-mintákat](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) . 
- Lásd az alábbi cikkeket:
    - [Kérelmek hitelesítése az Azure Event Hubs alkalmazásból Azure Active Directory használatával](authenticate-application.md)
    - [Felügyelt identitás hitelesítése Azure Active Directory használatával Event Hubs erőforrások eléréséhez](authenticate-managed-identity.md)
    - [Kérelmek hitelesítése az Azure Event Hubs megosztott hozzáférési aláírások használatával](authenticate-shared-access-signature.md)
    - [Hozzáférés engedélyezése Event Hubs erőforrásokhoz a Azure Active Directory használatával](authorize-access-azure-active-directory.md)
    - [Hozzáférés engedélyezése Event Hubs erőforrásokhoz közös hozzáférési aláírások használatával](authorize-access-shared-access-signature.md)

