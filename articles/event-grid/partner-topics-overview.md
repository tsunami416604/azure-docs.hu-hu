---
title: Azure Event Grid partnerekkel kapcsolatos témakörök
description: A harmadik féltől származó Event Grid SaaS-és Pásti-partnerektől származó eseményeket közvetlenül az Azure-szolgáltatásoknak küldheti el Azure Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 4546874b8a6cd8a7e45c3e6957a5181d66c7433f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83691123"
---
# <a name="partner-topics-in-azure-event-grid-preview"></a>Partneri témakörök Azure Event Grid (előzetes verzió)
A partneri témakörök lehetővé teszik harmadik féltől származó eseményforrás közvetlen összekapcsolását Event Grid. Ez az integráció lehetővé teszi, hogy az Azure-szolgáltatásokból származó eseményekre való előfizetéssel azonos módon fizessen elő a partnerek eseményeire. 

## <a name="available-partners"></a>Elérhető partnerek
Event Grid partneri témakörökben elérhető első partner a Auth0. A [Auth0 partneri témakör](auth0-overview.md) lehetővé teszi a Auth0 és az Azure-fiókok összekapcsolását. A Auth0 események valós idejű reagálásának, naplózásának és figyelésének integrálása.

[Próbálja ki](auth0-how-to.md) még ma, és jelentkezzen be a Auth0-fiókjába, és hozzon létre egy Event Grid-integrációt. Miután rákattintott a Létrehozás gombra a Auth0-ben, egy függőben lévő Auth0-témakör jelenik meg az Azure-fiókjában. Kattintson az aktiválás lehetőségre, és létrehozhat esemény-előfizetéseket, átirányíthatja, szűrheti és kézbesítheti az eseményeket ugyanúgy, mint bármely más eseményforrás.

## <a name="pricing"></a>Díjszabás
A partneri témakörök a rendszertémakörökkel megegyező műveleti sebességgel lesznek felszámítva.

## <a name="limits"></a>Korlátok
A partneri témakörök nyilvános előzetes verzióban érhetők el. A nyilvános előzetes verzióban a partneri témakörök [ugyanazok a korlátozások](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#event-grid-limits) vonatkoznak, mint a rendszertémakörök és az egyéni témakörök.

## <a name="how-do-i-become-an-event-grid-partner"></a>Hogyan váljon Event Grid partner?
Az indítás támogatásához létrehozott infrastruktúra megkönnyíti az új partnerek számára, hogy Event Grid használatával integrálják az eseményekkel kapcsolatos képességeiket. További információkért olvassa el a partner bevezetési [dokumentációját](partner-onboarding-overview.md) .

## <a name="next-steps"></a>További lépések

- [Auth0-partneri témakör](auth0-overview.md)
- [Az Auth0-partneri témakör használata](auth0-how-to.md)
- [Legyen Event Grid partner](partner-onboarding-overview.md)