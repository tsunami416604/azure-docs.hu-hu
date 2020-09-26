---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-oktatóanyagokhoz – példány beállításának előfeltétele
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 302a478dd32c93a555bb17e9d7d0f60bd7859a50
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91292681"
---
### <a name="prepare-an-azure-digital-twins-instance"></a>Azure digitális Twins-példány előkészítése

Az oktatóanyag elvégzéséhez szüksége lesz egy **Azure Digital Twins-példányra** a programhoz. Ha már rendelkezik egy Azure Digital Twins-példánnyal, amely az előző munkafolyamatból lett beállítva, akkor ezt a példányt használhatja.

* Ha nem, **állítson be egy példányt és hitelesítést** a útmutatóban található útmutató [*: példány és hitelesítés beállítása*](../articles/digital-twins/how-to-set-up-instance-portal.md)című témakörben leírtak alapján. Az utasítások azt is tartalmazzák, hogy az egyes lépéseket sikeresen elvégezte-e, és készen állnak az új példány használatára való áttérésre.

Ebben az oktatóanyagban az alábbi értékekre lesz szüksége a példány beállításakor. Ha újra össze kell gyűjtenie ezeket az értékeket, az alábbi hivatkozásokra kattintva megkeresheti azokat a [Azure Portalban](https://portal.azure.com).
* Azure digitális Twins-példány **_neve_** ([Keresés a portálon](../articles/digital-twins/how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Azure digitális Twins-példány **_állomásneve_** ([Keresés a portálon](../articles/digital-twins/how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* Azure AD App Registration **_Application (ügyfél-) azonosító_** ([Keresés a portálon](../articles/digital-twins/how-to-set-up-instance-portal.md#collect-important-values))
* Azure AD-alkalmazás regisztrációs **_könyvtárának (bérlői) azonosítója_** ([Keresés a portálon](../articles/digital-twins/how-to-set-up-instance-portal.md#collect-important-values))