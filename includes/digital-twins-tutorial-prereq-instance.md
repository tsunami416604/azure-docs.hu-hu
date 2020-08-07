---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-oktatóanyagokhoz – példány beállításának előfeltétele
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 5c41f7516cecdb6bbc42a66d118a90986dd7de56
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87827327"
---
### <a name="prepare-an-azure-digital-twins-instance"></a>Azure digitális Twins-példány előkészítése

Az oktatóanyag elvégzéséhez szüksége lesz egy **Azure Digital Twins-példányra** a programhoz. Ha már rendelkezik egy Azure Digital Twins-példánnyal, amely az előző munkafolyamatból lett beállítva, akkor ezt a példányt használhatja.

* Ha nem, **állítson be egy példányt és hitelesítést** a útmutatóban található útmutató [*: példány és hitelesítés beállítása*](../articles/digital-twins/how-to-set-up-instance-scripted.md)című témakörben leírtak alapján. Az utasítások azt is tartalmazzák, hogy az egyes lépéseket sikeresen elvégezte-e, és készen állnak az új példány használatára való áttérésre.

Ebben az oktatóanyagban az alábbi értékekre lesz szüksége a példány beállításakor. Ha újra össze kell gyűjtenie ezeket az értékeket, az alábbi hivatkozásokra kattintva megkeresheti azokat a [Azure Portalban](https://portal.azure.com).
* Azure digitális Twins-példány **_neve_** ([Keresés a portálon](../articles/digital-twins/how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Azure digitális Twins-példány **_állomásneve_** ([Keresés a portálon](../articles/digital-twins/how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Azure AD App Registration **_Application (ügyfél-) azonosító_** ([Keresés a portálon](../articles/digital-twins/how-to-set-up-instance-portal.md#collect-important-values))
* Azure AD-alkalmazás regisztrációs **_könyvtárának (bérlői) azonosítója_** ([Keresés a portálon](../articles/digital-twins/how-to-set-up-instance-portal.md#collect-important-values))