---
title: Előfizetések létrehozása az Azure API Management ben | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre előfizetéseket az Azure API Managementben.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073540"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Előfizetések létrehozása az Azure API Managementben

Ha API-kat tesz közzé az Azure API Managementen keresztül, az előfizetési kulcsok használatával egyszerűen és általánosan biztosíthatja az API-khoz való hozzáférést. A közzétett API-kat használó ügyfélalkalmazásoknak érvényes előfizetési kulcsot kell tartalmazniuk a HTTP-kérelmekben, amikor az API-khoz hívásokat kezdeményeznek. Az API-k eléréséhez előfizetési kulcs lekérni, előfizetés szükséges. Az előfizetésekről az [Előfizetések az Azure API Management ben](api-management-subscriptions.md)című témakörben talál további információt.

Ez a cikk bemutatja az Azure Portalon létrehozott előfizetések lépéseit.

## <a name="prerequisites"></a>Előfeltételek

A cikkben ismertetett lépések végrehajtásához az előfeltételek a következők:

+ [Hozzon létre egy API Management példányt.](get-started-create-service-instance.md)
+ Ismerje meg [az API Management előfizetéseit.](api-management-subscriptions.md)

## <a name="create-a-new-subscription"></a>Új előfizetés létrehozása

1. A bal oldali menüben válassza az **Előfizetések** lehetőséget.
2. Válassza **az Előfizetés hozzáadása**lehetőséget.
3. Adja meg az előfizetés nevét, és válassza ki a hatókört.
4. Tetszés szerint válassza ki, hogy az előfizetést egy felhasználóhoz kell-e társválasztani.
5. Kattintson a **Mentés** gombra.

![Rugalmas előfizetések](./media/api-management-subscriptions/flexible-subscription.png)

Az előfizetés létrehozása után két API-kulcs áll rendelkezésre az API-k eléréséhez. Az egyik kulcs az elsődleges, a másik másodlagos. 

## <a name="next-steps"></a>További lépések
További információ az API Managementről:

+ Ismerje meg az API Management egyéb [fogalmait.](api-management-terminology.md)
+ Kövesse [oktatóanyagainkat,](import-and-publish.md) hogy többet tudjon meg az API Managementről.
+ A gyakori kérdésekért tekintse meg [gyik oldalunkat.](api-management-faq.md)