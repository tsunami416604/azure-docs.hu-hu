---
title: A Azure Active Directory B2C támogatása | Microsoft Docs
description: A Azure Active Directory B2C támogatási kéréseinek beszerzése.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a4bb82665ecd9d04c097a127f46814bb7198f393
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227177"
---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C: Fájl-támogatási kérelmek
A Azure Portal Azure Active Directory (Azure AD) B2C-hez tartozó támogatási kérelmeket a következő lépések végrehajtásával teheti meg:

1. Váltson a B2C-bérlőről egy másik bérlőre, amelyhez hozzá van rendelve egy Azure-előfizetés. Az utóbbi általában az Ön alkalmazott bérlője vagy az alapértelmezett bérlő, amely az Azure-előfizetésre való regisztráláskor lett létrehozva. További információ: [how an Azure-előfizetés az Azure ad-hez kapcsolódóan](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

    ![Azure Portal a bérlő kiválasztása kiemelve](./media/active-directory-b2c-support/support-switch-dir.png)

1. A bérlők váltását követően kattintson a **Súgó és támogatás**elemre.

    ![A Súgó + támogatás csempe kiemelve Azure Portal](./media/active-directory-b2c-support/support-support.png)

1. Kattintson az **új támogatási kérelem**elemre.

    ![Új támogatási kérés csempe kiemelve a Azure Portal](./media/active-directory-b2c-support/support-new.png)

1. Az **alapvető beállítások** panelen használja ezeket az adatokat, és kattintson a **tovább**gombra.

    * A **probléma típusa** **technikai**jellegű.
    * Válassza ki amegfelelő előfizetést.
    * A szolgáltatás **Active Directory**.
    * Válassza ki a megfelelő **támogatási csomagot**. Ha még nem rendelkezik ilyennel, [itt](https://azure.microsoft.com/support/plans/)regisztrálhat egyet.

     ![Alapismeretek lap a következő gomb kiemelve Azure Portal](./media/active-directory-b2c-support/support-basics.png)

1. A **probléma** panelen használja ezeket az adatokat, és kattintson a **tovább**gombra.

    * Válassza ki a megfelelő súlyossági szintet.
    * A **probléma típusa** **B2C**.
    * Válassza ki amegfelelő kategóriát.
    * Írja le a problémát a **részletek** mezőben. Adja meg az adatokat, például a B2C-bérlő nevét, a probléma leírását, a hibaüzeneteket, a korrelációs azonosítókat (ha vannak ilyenek) stb.
    * Az **időkeret** mezőben adja meg a probléma előfordulásának dátumát és időpontját (az időzónát is beleértve).
    * A **fájl feltöltése**területen töltse fel az összes olyan képernyőképet és fájlt, amelyet úgy gondol, hogy a probléma megoldásához segítséget nyújt.

     ![Probléma oldal a következő gomb kiemelésével Azure Portal](./media/active-directory-b2c-support/support-problem.png)

1. A **kapcsolattartási adatok** panelen adja meg a kapcsolattartási adatait. Kattintson a **Create** (Létrehozás) gombra.

    ![Kapcsolattartási adatok lap a portálon Kiemelt létrehozás gombbal](./media/active-directory-b2c-support/support-contact.png)

1. A támogatási kérelem elküldése után a Kezdőpulton, majd a **támogatási kérések kezelése**lehetőségre **kattintva** figyelheti azt.

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>Ismert probléma: Támogatási kérelem bejelentése B2C-bérlő kontextusában

Ha kihagyta a fenti 2. lépést, és megpróbál létrehozni egy támogatási kérést a B2C-bérlő kontextusában, a következő hibaüzenet jelenik meg.

> [!IMPORTANT]
> Ne próbáljon regisztrálni egy új Azure-előfizetésre a B2C-bérlőben.

![Nem rendelkezik előfizetési hibával Azure Portal](./media/active-directory-b2c-support/support-no-sub.png)
