---
title: Igény szerinti hozzáférés igénylése
description: Ez a témakör azt ismerteti, hogy az Azure felügyelt alkalmazások fogyasztói hogyan hagyják jóvá a felügyelt alkalmazásokhoz való just-in-time hozzáférésre vonatkozó kérelmeket.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 4a3604a3256fb2d3f4253891bbc28c7685748cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651019"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Az Azure felügyelt alkalmazásokhoz való just-in-time hozzáférés konfigurálása és jóváhagyása

Egy felügyelt alkalmazás fogyasztójaként előfordulhat, hogy nem szívesen ad állandó hozzáférést a közzétevőnek a felügyelt erőforráscsoporthoz. Annak érdekében, hogy nagyobb ellenőrzést biztosítson a felügyelt erőforrásokhoz való hozzáférés biztosítása felett, az Azure Managed Applications egy just-in-time (JIT) access nevű szolgáltatást biztosít, amely jelenleg előzetes verzióban érhető el. Lehetővé teszi, hogy jóváhagyja, hogy a közzétevő mikor és mennyi ideig férhet hozzá az erőforráscsoporthoz. A közzétevő ez idő alatt eltudja végezni a szükséges frissítéseket, de ha ez az idő lejárt, a közzétevő hozzáférése lejár.

A hozzáférés biztosításához szükséges munkafolyamat a következő:

1. A közzétevő hozzáad egy felügyelt alkalmazást a piactérhez, és meghatározza, hogy jit-hozzáférés érhető el.

1. A telepítés során lehetővé teszi a JIT-hozzáférést a felügyelt alkalmazás példányához.

1. A telepítés után módosíthatja a jit-hozzáférés beállításait.

1. A közzétevő hozzáférési kérelmet küld.

1. Jóváhagyja a kérelmet.

Ez a cikk a fogyasztók által a közös nyomozói hozzáférés engedélyezéséhez és a kérelmek jóváhagyásához tett műveletekre összpontosít. Ha többet szeretne tudni a felügyelt alkalmazások jit-hozzáféréssel való közzétételéről, olvassa [el a Just-in-time access kérése](request-just-in-time-access.md)az Azure felügyelt alkalmazásokban .

> [!NOTE]
> A just-in-time hozzáférés használatához Rendelkeznie kell egy [Azure Active Directory P2 licenccel.](../../active-directory/privileged-identity-management/subscription-requirements.md)

## <a name="enable-during-deployment"></a>Engedélyezés telepítés közben

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Marketplace-bejegyzés keresése felügyelt alkalmazáshoz, ha a JIT engedélyezve van. Kattintson a **Létrehozás** gombra.

1. Az új felügyelt alkalmazás értékeinek megadása közben a **JIT-konfiguráció** lépés lehetővé teszi a jit-hozzáférés engedélyezését vagy letiltását a felügyelt alkalmazás számára. Válassza az **Igen** lehetőséget a **JIT-hozzáférés engedélyezése mezőben.** Ez a beállítás alapértelmezés szerint be van jelölve olyan felügyelt alkalmazásokesetében, amelyek a piactéren engedélyezve vannak a jit-kapcsolaton.

   ![Hozzáférés konfigurálása](./media/approve-just-in-time-access/configure-jit-access.png)

   Csak a telepítés során engedélyezheti a JIT-hozzáférést. Ha a **Nem**lehetőséget választja, a közzétevő állandó hozzáférést kap a felügyelt erőforráscsoporthoz. Később nem engedélyezheti a jit-hozzáférést.

1. Az alapértelmezett jóváhagyási beállítások módosításához válassza a **JIT-konfiguráció testreszabása lehetőséget.**

   ![Hozzáférés testreszabása](./media/approve-just-in-time-access/customize-jit-access.png)

   Alapértelmezés szerint a JIT-kompatibilis felügyelt alkalmazások beállításai a következők:

   * Jóváhagyási mód – automatikus
   * Maximális hozzáférés időtartama – 8 óra
   * Jóváhagyók – nincs

   Ha a jóváhagyási mód **automatikusra**van állítva, a jóváhagyók értesítést kapnak minden kérésről, de a kérés automatikusan jóváhagyásra kerül. Ha **kézi**beállításra van állítva, a jóváhagyók értesítést kapnak minden kérésről, és egyiküknek jóvá kell hagynia azt.

   Az aktiválás maximális időtartama azt a maximális időtartamot határozza meg, hogy a közzétevő legfeljebb mennyi ideig kérhet hozzáférést a felügyelt erőforráscsoporthoz.

   A jóváhagyók listája az Azure Active Directory-felhasználók, amelyek jóváhagyhatják a jit hozzáférési kérelmek. Jóváhagyó hozzáadásához válassza **a Jóváhagyó hozzáadása** lehetőséget, és keresse meg a felhasználót.

   A beállítás frissítése után válassza a **Mentés gombot.**

## <a name="update-after-deployment"></a>Frissítés telepítés után

Módosíthatja a kérelmek jóváhagyásának értékét. Ha azonban nem engedélyezte a jit-hozzáférést a telepítés során, később nem engedélyezheti.

Telepített felügyelt alkalmazás beállításainak módosítása:

1. A portálon válassza ki az alkalmazás kezelését.

1. Válassza a **JIT-konfiguráció lehetőséget,** és szükség szerint módosítsa a beállításokat.

   ![Hozzáférési beállítások módosítása](./media/approve-just-in-time-access/change-settings.png)

1. Ha elkészült, kattintson a **Mentés** elemre.

## <a name="approve-requests"></a>Kérések jóváhagyása

Amikor a közzétevő hozzáférést kér, értesítést kap a kérésről. A jit-hozzáférési kérelmeket közvetlenül a felügyelt alkalmazáson keresztül, vagy az Azure AD privilegizált identitáskezelési szolgáltatáson keresztül hagyhatja jóvá. A just-in-time hozzáférés használatához Rendelkeznie kell egy [Azure Active Directory P2 licenccel.](../../active-directory/privileged-identity-management/subscription-requirements.md)

Kérelmek jóváhagyása a felügyelt alkalmazáson keresztül:

1. Válassza a **JIT Access lehetőséget** a felügyelt alkalmazáshoz, és válassza **a Kérelmek jóváhagyása**lehetőséget.

   ![Kérések jóváhagyása](./media/approve-just-in-time-access/approve-requests.png)
 
1. Válassza ki a jóváhagyandó kérelmet.

   ![Kérelem kiválasztása](./media/approve-just-in-time-access/select-request.png)

1. A képernyőn adja meg a jóváhagyás okát, és válassza a **Jóváhagyás**lehetőséget.

Kérelmek jóváhagyása az Azure AD kiemelt identitáskezelésen keresztül:

1. Válassza a **Minden szolgáltatás** lehetőséget, és kezdje el keresni az **Azure AD kiemelt identitáskezelés szolgáltatását.** Válassza ki a rendelkezésre álló lehetőségek közül.

   ![Szolgáltatás keresése](./media/approve-just-in-time-access/search.png)

1. Válassza **a Kérelmek jóváhagyása**lehetőséget.

   ![Kérelmek jóváhagyása](./media/approve-just-in-time-access/select-approve-requests.png)

1. Válassza ki **az Azure által felügyelt alkalmazásokat**, és válassza ki a jóváhagyandó kérelmet.

   ![Kérések kiválasztása](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni a felügyelt alkalmazások jit-hozzáféréssel való közzétételéről, olvassa [el a Just-in-time access kérése](request-just-in-time-access.md)az Azure felügyelt alkalmazásokban .
