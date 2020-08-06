---
title: Azure Event Grid partnerekkel kapcsolatos témakörök
description: A harmadik féltől származó Event Grid SaaS-és Pásti-partnerektől származó eseményeket közvetlenül az Azure-szolgáltatásoknak küldheti el Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: e56055c55a3b30c5d13736b9838257f3c0bbec10
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87831906"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Partneri témakörök Azure Event Grid (előzetes verzió)
A partneri témakörök segítségével közvetlenül a Azure Event Grid csatlakozhat a harmadik féltől származó eseményekhez. Ez az integráció lehetővé teszi, hogy az Azure-szolgáltatásokból származó eseményekre való előfizetéssel azonos módon fizessen elő a partnerek eseményeire. 

## <a name="available-partners"></a>Elérhető partnerek
Event Grid partneri témakörökben elérhető első partner a Auth0. A Auth0 és az Azure-fiókok összekapcsolásához használhatja a [Auth0-partner témakört](auth0-overview.md) . Az integráció lehetővé teszi, hogy valós időben reagáljon a Auth0 eseményeire, naplózza és figyelje.

[A kipróbáláshoz](auth0-how-to.md)jelentkezzen be a Auth0-fiókjába, és hozzon létre egy Event Grid-integrációt. Miután kiválasztotta a **Létrehozás** a Auth0-ben lehetőséget, megjelenik egy függőben lévő Auth0 témakör az Azure-fiókjában. Válassza az **aktiválás**lehetőséget, és hozzon létre Event Grid előfizetéseket az események irányítására, szűrésére és továbbítására ugyanúgy, mint bármely más eseményforrás.

## <a name="pricing"></a>Díjszabás
A partneri témakörök a rendszertémakörökkel megegyező műveleti sebességgel lesznek felszámítva.

## <a name="limits"></a>Korlátok
A partneri témakörök nyilvános előzetes verzióban érhetők el. A nyilvános előzetes verzióban a partneri témakörök [ugyanazok a korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits) vonatkoznak, mint a rendszertémakörök és az egyéni témakörök.

## <a name="how-do-i-become-an-event-grid-partner"></a>Hogyan váljon Event Grid partner?
Az indítás támogatásához létrehozott infrastruktúra megkönnyíti az új partnerek számára, hogy Event Grid használatával integrálják az eseményekkel kapcsolatos képességeiket. További információkért tekintse meg a partner bevezetési [dokumentációját](partner-onboarding-overview.md).

## <a name="next-steps"></a>További lépések

- [Auth0-partneri témakör](auth0-overview.md)
- [Az Auth0-partneri témakör használata](auth0-how-to.md)
- [Legyen Event Grid partner](partner-onboarding-overview.md)