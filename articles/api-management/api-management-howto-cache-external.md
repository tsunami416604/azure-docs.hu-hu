---
title: Külső gyorsítótár használata az Azure API Management ben | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhatja és használhatja a külső gyorsítótárat az Azure API Managementben.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70072503"
---
# <a name="use-an-external-azure-cache-for-redis-in-azure-api-management"></a>Külső Azure Cache for Redis használata az Azure API Managementben

A beépített gyorsítótár használata mellett az Azure API Management lehetővé teszi a válaszok gyorsítótárazását egy külső Azure-gyorsítótárban a Redis számára.

A külső gyorsítótár használata lehetővé teszi a beépített gyorsítótár néhány korlátozásának leküzdését. Ez különösen előnyös, ha szeretné:

* Az API Management-frissítések során rendszeresen törölje a gyorsítótárat
* Jobban szabályozhatja a gyorsítótár konfigurációját
* Több adatot tárolhat, mint amennyit az API Felügyeleti réteg
* Gyorsítótárazás használata az API Management felhasználási szintjével

A gyorsítótárazással kapcsolatos részletes információk: [Az API Management gyorsítótárazási házirendjei](api-management-caching-policies.md) és [Egyedi gyorsítótárazás az Azure API Management szolgáltatásban](api-management-sample-cache-by-key.md).

![Saját gyorsítótár elhozása az APIM-be](media/api-management-howto-cache-external/overview.png)

Ismertetett témák:

> [!div class="checklist"]
> * Külső gyorsítótár hozzáadása az API Management ben

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

+ [Azure API Management-példány létrehozása](get-started-create-service-instance.md)
+ A [gyorsítótárazás ismertetése az Azure API Managementben](api-management-howto-cache.md)

## <a name="create-azure-cache-for-redis"></a><a name="create-cache"> </a> Azure-gyorsítótár létrehozása a Redis számára

Ez a szakasz bemutatja, hogyan hozhat létre egy Azure-gyorsítótárat a Redis az Azure-ban. Ha már rendelkezik egy Azure-gyorsítótár a Redis, az Azure-on belül vagy kívül, <a href="#add-external-cache">ugorhat</a> a következő szakaszra.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="add-an-external-cache"></a><a name="add-external-cache"> </a>Külső gyorsítótár hozzáadása

Az alábbi lépéseket követve hozzáadhat egy külső Azure-gyorsítótárat a Redishez az Azure API Managementben.

![Saját gyorsítótár elhozása az APIM-be](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> A **Használat a beállításból** beállítás határozza meg, hogy az API Management területi központi telepítése melyik kapcsolatot kommunikáljon a konfigurált gyorsítótárral az API Management többrégiós konfigurációja esetén. Az **alapértelmezettként** megadott gyorsítótárakat a regionális értékkel rendelkező gyorsítótárak felülbírálják.
>
> Ha például az API Management az USA keleti régiójában, Délkelet-Ázsiában és Nyugat-Európában található, és két gyorsítótár van konfigurálva, az egyik **az Alapértelmezett,** a másik **délkelet-ázsiai**, az API Management **Délkelet-Ázsiában** a saját gyorsítótárát fogja használni, míg a másik két régió az **Alapértelmezett** gyorsítótár-bejegyzést fogja használni.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Azure-gyorsítótár hozzáadása a Redis-hez ugyanabból az előfizetésből

1. Tallózzon az API Management-példány ban az Azure Portalon.
2. A bal oldali menüben válassza a **Külső gyorsítótár** lapot.
3. Kattintson a **+ Hozzáadás** gombra.
4. Válassza ki a gyorsítótárat a **Gyorsítótár példány** legördülő mezőjében.
5. Válassza az **Alapértelmezett** lehetőséget, vagy adja meg a kívánt területet a **Használat** a legördülő mezőben.
6. Kattintson a **Mentés** gombra.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Azure-gyorsítótár hozzáadása a Redis-hez, amelyet az aktuális Azure-előfizetésen vagy általában az Azure-on kívül üzemeltetnek

1. Tallózzon az API Management-példány ban az Azure Portalon.
2. A bal oldali menüben válassza a **Külső gyorsítótár** lapot.
3. Kattintson a **+ Hozzáadás** gombra.
4. A Gyorsítótár **példány** legördülő mezőjében válassza az **Egyéni** lehetőséget.
5. Válassza az **Alapértelmezett** lehetőséget, vagy adja meg a kívánt területet a **Használat** a legördülő mezőben.
6. Adja meg az Azure Cache for Redis kapcsolati karakterlánc a **Kapcsolat i.**
7. Kattintson a **Mentés** gombra.

## <a name="use-the-external-cache"></a>A külső gyorsítótár használata

Miután a külső gyorsítótár konfigurálva van az Azure API Managementben, a házirendek gyorsítótárazási házirendek segítségével használható. A részletes lépésekért olvassa el [a Gyorsítótárazás hozzáadása című témakört az Azure API Management teljesítményének javítása érdekében.](api-management-howto-cache.md)

## <a name="next-steps"></a><a name="next-steps"> </a>További lépések

* További információt a gyorsítótárazási házirendekről az [API Management házirend-referencia][API Management policy reference] oktatóanyag [Gyorsítótárazási házirendek][Caching policies] szakaszában talál.
* További információ az elemeknek a házirend-kifejezések kulcsával történő gyorsítótárazásáról: [Egyéni gyorsítótárazás az Azure API Management szolgáltatásban](api-management-sample-cache-by-key.md).

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
