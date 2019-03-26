---
title: Egy külső gyorsítótár az Azure API Management használata |} A Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja és használhatja az Azure API Management egy külső gyorsítótár.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.assetid: 740f6a27-8323-474d-ade2-828ae0c75e7a
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 8e3f34210627fbb455a1eca0b415cdd6de9b3681
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407623"
---
# <a name="use-an-external-azure-cache-for-redis-in-azure-api-management"></a>A Redis az Azure API Management egy külső Azure Cache használatához

Mellett a beépített gyorsítótárat használó, Azure API Management lehetővé teszi a válaszok egy külső Azure Cache-ben a Redis-gyorsítótárazás.

Külső gyorsítótár használata lehetővé teszi, hogy a beépített gyorsítótár vonatkozik néhány korlátozás. Ez akkor különösen hasznos, ha szeretné:

* Ne kelljen a gyorsítótárban, az API Management-frissítések során rendszeresen törölve
* További szabályozhatják a gyorsítótár konfigurálása
* Az API Management szint lehetővé teszi, hogy több adatot gyorsítótárazása
* Használjon gyorsítótárazást a Használatalapú csomag az API Management

A gyorsítótárazással kapcsolatos részletes információk: [Az API Management gyorsítótárazási házirendjei](api-management-caching-policies.md) és [Egyedi gyorsítótárazás az Azure API Management szolgáltatásban](api-management-sample-cache-by-key.md).

![A saját gyorsítótár életre APIM](media/api-management-howto-cache-external/overview.png)

Ismertetett témák:

> [!div class="checklist"]
> * Adjon hozzá egy külső gyorsítótár az API Management szolgáltatásban

## <a name="availability"></a>Rendelkezésre állás

> [!NOTE]
> Ez a funkció jelenleg csak érhető el a **fogyasztás** az Azure API Management szintjéhez.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

+ [Azure API Management-példány létrehozása](get-started-create-service-instance.md)
+ Megismerheti [gyorsítótárazás az Azure API Management](api-management-howto-cache.md)

## <a name="create-cache"> </a> A Redis az Azure Cache létrehozása

Ez a szakasz ismerteti, hogyan hozhat létre egy Azure Cache redis az Azure-ban. Ha már rendelkezik egy Azure Cache redis, belül vagy az Azure-on kívül is <a href="#add-external-cache">kihagyása</a> a következő szakaszra.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="add-external-cache"> </a>Adjon hozzá egy külső gyorsítótár

Adjon hozzá egy külső Azure Cache redis az Azure API Management az alábbi lépésekkel.

![A saját gyorsítótár életre APIM](media/api-management-howto-cache-external/add-external-cache.png)

> [!NOTE]
> A **használata** beállítással melyik regionális telepítési kommunikálni fog az API Management a gyorsítótár konfigurált több regionális az API Management-konfiguráció esetén. A megadott gyorsítótárak **alapértelmezett** felülbírálják gyorsítótárak regionális értékkel.
>
> Például ha az API Management az USA keleti RÉGIÓJA, Délkelet-Ázsiában és Nyugat-európai régióban üzemel, és nincsenek két gyorsítótárak konfigurálni, egyet-egyet **alapértelmezett** és a egy **Délkelet-Ázsia**, azAPIManagement **Délkelet-Ázsia** fogja használni a saját gyorsítótár, míg a másik két régió fogja használni a **alapértelmezett** gyorsítótár-bejegyzés.

### <a name="add-an-azure-cache-for-redis-from-the-same-subscription"></a>Adja hozzá az Azure Cache redis ugyanabba az előfizetésbe tartozó

1. Keresse meg az API Management-példány az Azure Portalon.
2. Válassza ki a **külső gyorsítótár** fülre a bal oldali menüben.
3. Kattintson a **+ Hozzáadás** gombra.
4. Válassza ki a saját gyorsítótárához az a **gyorsítótárpéldány** legördülő mező.
5. Válassza ki **alapértelmezett** , vagy adja meg a kívánt régiót, a a **használata** legördülő mező.
6. Kattintson a **Save** (Mentés) gombra.

### <a name="add-an-azure-cache-for-redis-hosted-outside-of-the-current-azure-subscription-or-azure-in-general"></a>Adja hozzá az Azure Cache általában az aktuális Azure-előfizetés vagy az Azure-on kívül üzemeltetett redis

1. Keresse meg az API Management-példány az Azure Portalon.
2. Válassza ki a **külső gyorsítótár** fülre a bal oldali menüben.
3. Kattintson a **+ Hozzáadás** gombra.
4. Válassza ki **egyéni** a a **gyorsítótárpéldány** legördülő mező.
5. Válassza ki **alapértelmezett** , vagy adja meg a kívánt régiót, a a **használata** legördülő mező.
6. Adja meg az Azure Cache Redis kapcsolati karakterlánc az **kapcsolati karakterlánc** mező.
7. Kattintson a **Save** (Mentés) gombra.

## <a name="use-the-external-cache"></a>A külső gyorsítótár használata

Ha a külső gyorsítótár már konfigurálva van az Azure API Management szolgáltatásban, használat gyorsítótárazási házirendek keresztül. Lásd: [hozzáadása az Azure API Management teljesítményének javításához gyorsítótárazás](api-management-howto-cache.md) a részletes lépéseket.

## <a name="next-steps"></a>Következő lépések
* További információt a gyorsítótárazási házirendekről az [API Management házirend-referencia][API Management policy reference] oktatóanyag [Gyorsítótárazási házirendek][Caching policies] szakaszában talál.
* További információ az elemeknek a házirend-kifejezések kulcsával történő gyorsítótárazásáról: [Egyéni gyorsítótárazás az Azure API Management szolgáltatásban](api-management-sample-cache-by-key.md).

[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
