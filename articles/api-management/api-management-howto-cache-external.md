---
title: Külső gyorsítótár használata az Azure API Managementban | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat és használhat külső gyorsítótárat az Azure API Managementban.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: apimpm
ms.openlocfilehash: 2e8863eed774884a99de8643c9e497378368d166
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "70072503"
---
# <a name="use-an-external-azure-cache-for-redis-in-azure-api-management"></a>Külső Azure Cache for Redis használata az Azure API Managementben

A beépített gyorsítótár kihasználása mellett az Azure API Management is lehetővé teszi, hogy a Redis egy külső Azure-gyorsítótárban válaszokat engedélyezzen.

A külső gyorsítótár használata lehetővé teszi a beépített gyorsítótár néhány korlátozásának megszüntetését. Ez különösen hasznos, ha a következőket szeretné:

* Kerülje a gyorsítótár rendszeres törlését API Management frissítések során
* Nagyobb mértékben szabályozhatja a gyorsítótár konfigurációját
* A API Management-csomaggal több adattal is gyorsítótárazhat, így
* A gyorsítótárazás használata a API Management fogyasztási szintjével

A gyorsítótárazással kapcsolatos részletes információk: [Az API Management gyorsítótárazási házirendjei](api-management-caching-policies.md) és [Egyedi gyorsítótárazás az Azure API Management szolgáltatásban](api-management-sample-cache-by-key.md).

![Saját gyorsítótár használata a APIM](media/api-management-howto-cache-external/overview.png)

Ismertetett témák:

> [!div class="checklist"]
> * Külső gyorsítótár hozzáadása API Management

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

+ [Azure API Management-példány létrehozása](get-started-create-service-instance.md)
+ [Az Azure-API Management gyorsítótárazásának](api-management-howto-cache.md) megismerése

## <a name="create-azure-cache-for-redis"></a><a name="create-cache"> </a> Azure cache létrehozása a Redis

Ez a szakasz azt ismerteti, hogyan hozható létre Azure cache a Redis az Azure-ban. Ha már rendelkezik Azure-gyorsítótárral az Azure-on belüli vagy kívüli Redis, <a href="#add-external-cache">ugorjon</a> a következő szakaszra.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="add-an-external-cache"></a><a name="add-external-cache"> </a>Külső gyorsítótár hozzáadása

Kövesse az alábbi lépéseket egy külső Azure-gyorsítótár hozzáadásához az Azure API Management Redis.

![Saját gyorsítótár használata a APIM](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> A **használat forrása** beállítással megadható, hogy API Management regionális központi telepítés hogyan kommunikáljon a konfigurált gyorsítótárral, ha a API Management több régiós konfigurációja is van. Az **alapértelmezettként** megadott gyorsítótárak felülbírálása a regionális értékkel rendelkező gyorsítótárak esetében történik.
>
> Ha például API Management az USA keleti régiójában, a Délkelet-Ázsiában és a Nyugat-európai régióban található, és két gyorsítótár van konfigurálva, az egyik az **alapértelmezett** , a másik pedig a **Délkelet-ázsiai**, a **Délkelet-ázsiai** API Management a saját gyorsítótárát fogja használni, míg a másik két régió az **alapértelmezett** gyorsítótár-bejegyzést fogja használni.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Azure cache hozzáadása ugyanahhoz az előfizetéshez tartozó Redis

1. Tallózással keresse meg API Management-példányát a Azure Portal.
2. Válassza a **külső gyorsítótár** fület a bal oldali menüből.
3. Kattintson a **+ Hozzáadás** gombra.
4. Válassza ki a gyorsítótárat a **gyorsítótár-példány** legördülő mezőjében.
5. Válassza az **alapértelmezett** lehetőséget, vagy adja meg a kívánt régiót a **használat** a legördülő listából mezőben.
6. Kattintson a **Save** (Mentés) gombra.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Azure-gyorsítótár hozzáadása az aktuális Azure-előfizetésen vagy az Azure-on kívül üzemeltetett Redis

1. Tallózással keresse meg API Management-példányát a Azure Portal.
2. Válassza a **külső gyorsítótár** fület a bal oldali menüből.
3. Kattintson a **+ Hozzáadás** gombra.
4. Válassza az **Egyéni** lehetőséget a **gyorsítótár-példány** legördülő mezőjében.
5. Válassza az **alapértelmezett** lehetőséget, vagy adja meg a kívánt régiót a **használat** a legördülő listából mezőben.
6. Adja meg az Azure cache-t a Redis-kapcsolatok karakterláncához a **kapcsolatok karakterlánca** mezőben.
7. Kattintson a **Save** (Mentés) gombra.

## <a name="use-the-external-cache"></a>Külső gyorsítótár használata

Ha a külső gyorsítótár konfigurálva van az Azure API Managementban, a gyorsítótárazási házirendek használatával is használható. A részletes lépésekért lásd: [gyorsítótár hozzáadása az Azure-API Management teljesítményének növeléséhez](api-management-howto-cache.md) .

## <a name="next-steps"></a><a name="next-steps"> </a>További lépések

* További információt a gyorsítótárazási házirendekről az [API Management házirend-referencia][API Management policy reference] oktatóanyag [Gyorsítótárazási házirendek][Caching policies] szakaszában talál.
* További információ az elemeknek a házirend-kifejezések kulcsával történő gyorsítótárazásáról: [Egyéni gyorsítótárazás az Azure API Management szolgáltatásban](api-management-sample-cache-by-key.md).

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
