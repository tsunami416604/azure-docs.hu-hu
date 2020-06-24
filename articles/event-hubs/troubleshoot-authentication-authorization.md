---
title: Hitelesítési és engedélyezési problémák elhárítása – Azure Event Hubs
description: Ez a cikk tájékoztatást nyújt az Azure Event Hubs-vel kapcsolatos hitelesítési és engedélyezési hibák elhárításáról.
services: event-hubs
documentationcenter: na
author: spelluru
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 05/18/2020
ms.author: spelluru
ms.openlocfilehash: d093be47ce38efa9ba9f2009fa09e1021d48336b
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84713243"
---
# <a name="troubleshoot-authentication-and-authorization-issues---azure-event-hubs"></a>Hitelesítési és engedélyezési problémák elhárítása – Azure Event Hubs
A [kapcsolódási problémák elhárítása](troubleshooting-guide.md) című cikk tippekkel szolgál az Azure Event Hubs kapcsolódási problémáinak elhárításához. Ez a cikk tippeket és javaslatokat tartalmaz az Azure Event Hubs-vel kapcsolatos hitelesítési és engedélyezési problémák elhárításához. 

## <a name="if-you-are-using-azure-active-directory"></a>Ha Azure Active Directory használ
Ha Azure Active Directoryt (Azure AD) használ az Azure Event Hubs hitelesítésére és engedélyezésére, győződjön meg arról, hogy az Event hub-hoz hozzáférő identitás tagja a megfelelő **szerepköralapú hozzáférés-vezérlési (RBAC) szerepkörnek** a megfelelő **erőforrás-hatókörben** (fogyasztói csoport, Event hub, névtér, erőforráscsoport vagy előfizetés).

### <a name="rbac-roles"></a>RBAC szerepkörei
- Az [Azure Event Hubs adattulajdonos](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) Event Hubs erőforrásokhoz való teljes hozzáféréshez.
- Az [Azure Event Hubs adatfeladót](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) a küldési hozzáféréshez.
- [Azure Event Hubs adatfogadó](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) a fogadási hozzáféréshez.

### <a name="resource-scopes"></a>Erőforrás-hatókörök
- **Fogyasztói csoport**: ezen a hatókörön a szerepkör-hozzárendelés csak erre az entitásra vonatkozik. A Azure Portal jelenleg nem támogatja az RBAC-szerepkör hozzárendelését egy rendszerbiztonsági tag számára ezen a szinten. 
- **Event hub**: a szerepkör-hozzárendelés az Event hub-entitásra és az alatta lévő fogyasztói csoportra vonatkozik.
- **Névtér**: a szerepkör-hozzárendelés a névtér és a hozzá társított fogyasztói csoport teljes topológiáját Event Hubs.
- **Erőforráscsoport**: a szerepkör-hozzárendelés az erőforráscsoport összes Event Hubs erőforrására vonatkozik.
- **Előfizetés**: a szerepkör-hozzárendelés az előfizetés összes erőforrás-csoportjának összes Event Hubs erőforrására vonatkozik.

További információért tekintse át a következő cikkeket:

- [Alkalmazás hitelesítése Azure Active Directory használatával Event Hubs erőforrások eléréséhez](authenticate-application.md)
- [Hozzáférés engedélyezése Event Hubs erőforrásokhoz a Azure Active Directory használatával](authorize-access-azure-active-directory.md)

## <a name="if-you-are-using-shared-access-signatures-sas"></a>Ha megosztott hozzáférési aláírásokat (SAS) használ
Ha [sas](authenticate-shared-access-signature.md)-t használ, kövesse az alábbi lépéseket: 

- Győződjön meg arról, hogy az Ön által használt SAS-kulcs helyes. Ha nem, használja a megfelelő SAS-kulcsot.
- Győződjön meg arról, hogy a kulcs rendelkezik a megfelelő engedélyekkel (küldés, fogadás vagy kezelés). Ha nem, olyan kulcsot használjon, amely rendelkezik a szükséges engedélyekkel. 
- Ellenőrizze, hogy a kulcs lejárt-e. Javasoljuk, hogy a lejárata előtt is újítsa meg a SAS-t. Ha az ügyfél és a Event Hubs szolgáltatási csomópontok között óra van elferdítve, előfordulhat, hogy a hitelesítési jogkivonat lejár, mielőtt az ügyfél felismeri. Az aktuális implementációs fiókok órája akár 5 percet is igénybe vehet, azaz a token 5 perccel a lejárata előtt megújítja az ügyfelet. Ezért ha az óra ferde értéke 5 percnél nagyobb, akkor az ügyfél megfigyelheti az időszakos hitelesítési hibákat.
- Ha a **sas kezdési ideje** **most**értékre van állítva, akkor az első néhány perc időszakos meghibásodása miatt időnként eldöntheti, hogy az aktuális idő a különböző gépeken milyen eltéréseket mutat. Állítsa be a kezdési időpontot legalább 15 percre a múltban, vagy ne állítsa be egyáltalán. Ugyanez általában a lejárati időre is vonatkozik. 

További információért tekintse át a következő cikkeket: 

- [Hitelesítés közös hozzáférésű aláírások (SAS) használatával](authenticate-shared-access-signature.md). 
- [A Event Hubs-erőforrásokhoz való hozzáférés engedélyezése közös hozzáférési aláírások használatával](authorize-access-shared-access-signature.md)

## <a name="next-steps"></a>További lépések

Lásd az alábbi cikkeket:

* [Kapcsolati problémák hibaelhárítása](troubleshooting-guide.md)
