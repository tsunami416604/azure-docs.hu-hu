---
title: Előfizetések létrehozása az Azure API Management |} A Microsoft Docs
description: Megtudhatja, hogyan hozhatnak létre előfizetéseket az Azure API Management szolgáltatásban.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 3f4e113125ec9644aac974e47996afe290e57cee
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621804"
---
# <a name="how-to-create-subscriptions-in-azure-api-management"></a>Előfizetések létrehozása az Azure API Management szolgáltatásban

Közzététel az API-k Azure API Management (APIM) keresztül, a legegyszerűbb és leggyakrabban használt módszer a biztonságos hozzáférés az API-k esetén előfizetési kulcsok használatával. Más szóval a fogyasztói a közzétett API-kat használó ügyfélalkalmazások tartalmaznia kell egy érvényes előfizetési kulcsot a HTTP-kérések API-k hívása esetén. Szerezze be API-k eléréséhez egy előfizetési kulcsot, az előfizetés szükség. Az előfizetésekkel kapcsolatos további információkért lásd: [előfizetések az Azure API Management szolgáltatásban](api-management-subscriptions.md)

Ez a cikk lépésről lépésre bemutatja az előfizetések létrehozása az Azure Portalon.

## <a name="prerequisites"></a>Előfeltételek

A jelen cikkben ismertetett lépések végrehajtásához kell tennie:

+ [APIM-példány létrehozása](get-started-create-service-instance.md)
+ Megismerheti [APIM-előfizetések](api-management-subscriptions.md)

## <a name="create-a-new-subscription"></a>Hozzon létre egy új előfizetést

1. Kattintson a **előfizetések** a bal oldali menüben
2. Kattintson a **előfizetés hozzáadása**
3. Adja meg az előfizetés nevét és a hatókör kiválasztása
4. Kattintson a **Mentés** gombra.

![Rugalmas előfizetések](./media/api-management-subscriptions/flexible-subscription.png)

Az előfizetés létrehozása után két API-kulcsok (elsődleges és másodlagos) az API-k eléréséhez vannak kiépítve.

## <a name="next-steps"></a>További lépések
További információ az API Management:

+ Ismerje meg, hogy más [fogalmak](api-management-terminology.md) az API Management szolgáltatásban
+ Kövesse a [oktatóanyagok](import-and-publish.md) további információ az API Management
+ Ellenőrizze a [gyakori kérdéseket tartalmazó oldal](api-management-faq.md) vonatkozó gyakori kérdések