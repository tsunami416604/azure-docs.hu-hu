---
title: Előfizetések létrehozása az Azure API Managementban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre előfizetéseket az Azure API Managementban.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: f8b2238eb0fab9aeeb42d11b4176c0d681b5f8e5
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073540"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Előfizetések létrehozása az Azure-ban API Management

Amikor API-kat tesz közzé az Azure API Managementon keresztül, az előfizetés-kulcsok használatával egyszerűen és közösen biztosíthatja az API-k elérését. A közzétett API-kat használó ügyfélalkalmazások számára érvényes előfizetési kulcsot kell tartalmazniuk a HTTP-kérelmekben, amikor hívásokat kezdeményeznek az API-khoz. Az API-k eléréséhez szükséges előfizetési kulcs beszerzéséhez előfizetésre van szükség. Az előfizetésekkel kapcsolatos további információkért lásd: előfizetések [Az Azure API Managementban](api-management-subscriptions.md).

Ez a cikk végigvezeti az előfizetések Azure Portalban történő létrehozásának lépésein.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához az előfeltételek a következők:

+ [Hozzon létre egy API Management példányt](get-started-create-service-instance.md).
+ [Az](api-management-subscriptions.md)előfizetések értelmezése API Managementban.

## <a name="create-a-new-subscription"></a>Új előfizetés létrehozása

1. Válassza az előfizetések lehetőséget a bal oldali menüben.
2. Válassza az **előfizetés hozzáadása**lehetőséget.
3. Adja meg az előfizetés nevét, és válassza ki a hatókört.
4. Ha szeretné, válassza ki, hogy az előfizetést társítani kell-e egy felhasználóhoz.
5. Kattintson a **Mentés** gombra.

![Rugalmas előfizetések](./media/api-management-subscriptions/flexible-subscription.png)

Az előfizetés létrehozása után a rendszer két API-kulcsot biztosít az API-k eléréséhez. Az egyik kulcs elsődleges, és az egyik a másodlagos. 

## <a name="next-steps"></a>További lépések
További információ a API Managementról:

+ Ismerje meg [](api-management-terminology.md) a API Management egyéb fogalmait.
+ A API Management [](import-and-publish.md) megismeréséhez kövesse az oktatóanyagokat.
+ Gyakori kérdésekért tekintse meg a [gyakran ismételt](api-management-faq.md) kérdések oldalát.