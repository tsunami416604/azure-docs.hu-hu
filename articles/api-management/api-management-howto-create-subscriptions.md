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
ms.openlocfilehash: 1393e548c46c23f6b50c1b18a274febb74914ae8
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54054502"
---
# <a name="create-subscriptions-in-azure-api-management"></a>Az Azure API Management-előfizetések létrehozása

API-k Azure API Management szolgáltatáson keresztül tesz közzé, amelyet esetén könnyen és a közös API-kból való biztonságos hozzáférés előfizetési kulcsok használatával. A fogyasztói a közzétett API-kat használó ügyfélalkalmazások tartalmaznia kell egy érvényes előfizetési kulcsot a HTTP-kérések ezen API-kat fejlécekben. Az első API-k eléréséhez egy előfizetési kulcsot, egy előfizetésére szükség. Az előfizetésekkel kapcsolatos további információkért lásd: [előfizetések az Azure API Management](api-management-subscriptions.md).

Ez a cikk lépésről lépésre bemutatja az előfizetések létrehozása az Azure Portalon.

## <a name="prerequisites"></a>Előfeltételek

Ebben a cikkben hajtsa végre a lépéseket, hogy az Előfeltételek a következők:

+ [API Management szolgáltatáspéldány létrehozása](get-started-create-service-instance.md).
+ Megismerheti [az API Management előfizetések](api-management-subscriptions.md).

## <a name="create-a-new-subscription"></a>Hozzon létre egy új előfizetést

1. Válassza ki **előfizetések** a bal oldali menüben.
2. Válassza ki **előfizetés hozzáadása**.
3. Adja meg az előfizetés nevét, és válassza ki a hatókört.
4. Kattintson a **Mentés** gombra.

![Rugalmas előfizetések](./media/api-management-subscriptions/flexible-subscription.png)

Miután létrehozta az előfizetést, a két API-kulcsot az API-k elérésére szolgálnak. Egy kulcs az elsődleges, és egy másodlagos. 

## <a name="next-steps"></a>További lépések
További információ az API Management:

+ Ismerje meg, hogy más [fogalmak](api-management-terminology.md) az API Management szolgáltatásban.
+ Kövesse a [oktatóanyagok](import-and-publish.md) további információ az API Management.
+ Ellenőrizze a [gyakori kérdéseket tartalmazó oldal](api-management-faq.md) gyakori kérdéseket.