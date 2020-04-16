---
title: Key Vault figyelése az Azure Event Griddel
description: Kulcstároló-eseményekre való feliratkozás az Azure Event Grid használatával
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: cc12cc9a4828404e960aee239bd388af5b1ea3b7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431903"
---
# <a name="monitoring-key-vault-with-azure-event-grid-preview"></a>Key Vault figyelése az Azure Event Griddel (előzetes verzió)

A Key Vault és az Event Grid integrációja jelenleg előzetes verzióban érhető el. Lehetővé teszi a felhasználók számára, hogy értesítést kapjon, ha a key vaultban tárolt titkos kulcs állapota megváltozott. Az állapotváltozás olyan titokként van definiálva, amely hamarosan lejár (a lejárattól számított 30 napon belül), egy lejárt titok vagy egy titkos, amelyegy új verzióval rendelkezik. A három titkos típus (kulcs, tanúsítvány és titkos) értesítései támogatottak.

Az alkalmazások modern kiszolgáló nélküli architektúrák használatával reagálhatnak ezekre az eseményekre, anélkül, hogy bonyolult kódra vagy költséges és nem hatékony lekérdezési szolgáltatásokra lenne szükség. Az események az [Azure Event Griden](https://azure.microsoft.com/services/event-grid/) keresztül az eseménykezelőkbe, például [az Azure Functionsbe,](https://azure.microsoft.com/services/functions/)az Azure [Logic Apps-be](https://azure.microsoft.com/services/logic-apps/)vagy akár a saját webhookba kerülnek, és csak azért kell fizetnie, amit használ. Az árképzésről az [Event Grid díjszabása](https://azure.microsoft.com/pricing/details/event-grid/)című témakörben talál további információt.

## <a name="key-vault-events-and-schemas"></a>Key Vault-események és sémák

Az eseményrács [esemény-előfizetéseket](../../event-grid/concepts.md#event-subscriptions) használ az eseményüzenetek előfizetőkhöz való irányításához. A Key Vault-események tartalmazzák az összes olyan információt, amely az adatok változásaira való válaszadáshoz szükséges. A Key Vault-eseményeket azonosítani tudja, mert az eventType tulajdonság "Microsoft.KeyVault" programmal kezdődik.

További információt a [Key Vault eseménysémája című témakörben talál.](../../event-grid/event-schema-key-vault.md)

> [!WARNING]
> Értesítési események csak a titkos kulcsok, kulcsok és tanúsítványok új verziói, és először elő kell fizetnie az eseményre a key vault fogadásához ezeket az értesítéseket.
> 
> A tanúsítványokon csak akkor kap értesítési eseményeket, ha a tanúsítvány automatikusan megújul a tanúsítványhoz megadott házirendnek megfelelően.

## <a name="practices-for-consuming-events"></a>Az események fogyasztásának gyakorlata

A Key Vault-eseményeket kezelő alkalmazásoknak néhány ajánlott gyakorlatot kell követniük:

* Több előfizetés konfigurálható úgy, hogy az eseményeket ugyanarra az eseménykezelőre irányítsa. Fontos, hogy ne feltételezze, hogy az események egy adott forrásból származnak, hanem ellenőrizze az üzenet témáját, hogy megbizonyosodjon arról, hogy a várt kulcstartóból származik.
* Hasonlóképpen ellenőrizze, hogy az eventType olyan, amelyet fel kell dolgoznia, és ne feltételezze, hogy az összes kapott esemény a várt típusú lesz.
* Figyelmen kívül hagyja azolyan mezőket, amelyeket nem ért.  Ez a gyakorlat segít megőrizni a rugalmas új funkciók, amelyek a jövőben hozzáadott.
* A "tárgy" előtag és utótagegyezések használatával az eseményeket egy adott eseményre korlátozhatja.

## <a name="next-steps"></a>További lépések

- [Az Azure Key Vault áttekintése](overview.md))
- [Azure Event Grid – áttekintés](../../event-grid/overview.md)
- Útmutató: [A Kulcstároló eseményeinek irányítása az Automation Runbook (előzetes verzió) alkalmazásba.](event-grid-tutorial.md)
- Útmutató: [E-mailek fogadása, ha egy kulcstartó titkos titkára megváltozik](event-grid-logicapps.md)
- [Azure Event Grid eseménysémája az Azure Key Vaulthoz (előzetes verzió)](../../event-grid/event-schema-key-vault.md)
- [Az Azure Automation áttekintése](../../automation/index.yml)
