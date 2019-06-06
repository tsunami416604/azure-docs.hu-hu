---
title: Konfigurálja, és hagyja jóvá a igény szerinti elérése az Azure által felügyelt alkalmazások
description: Ismerteti, hogyan fogyasztók számára az Azure Managed Applications jóváhagyások just-in-time-hozzáférés a felügyelt alkalmazásokhoz.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 55fbf7292ab894cad3de3de9e96ddc96fe0b79b3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66481714"
---
# <a name="configure-and-approve-just-in-time-access-for-azure-managed-applications"></a>Konfigurálja, és hagyja jóvá a igény szerinti elérése az Azure által felügyelt alkalmazások

Egy felügyelt alkalmazás vásárlói, nem feltétlenül okoz gondot a közzétevő jogosultságot ad a felügyelt erőforráscsoporthoz való állandó hozzáférést. Nagyobb mértékben vezérelheti a felügyelt erőforrásokhoz való hozzáférést biztosíthat, Azure által felügyelt alkalmazások a szolgáltatás jelenleg előzetes verzióban – igény (szerinti JIT) hozzáférési biztosít. Ez lehetővé teszi, hogy mikor jóváhagyása, és mennyi ideig a közzétevő férhet hozzá az erőforráscsoportot. A közzétevő teheti a szükséges frissítések ebben az időszakban, de az időpont fölé esetén a kiadó hozzáférés lejár.

A munkafolyamat hozzáférés biztosításához a következő:

1. A közzétevő ad hozzá egy felügyelt alkalmazást, a piactéren, és megadja, hogy az igény szerinti hozzáférés érhető el.

1. A telepítés során engedélyezi igény szerinti hozzáférés a felügyelt alkalmazás példánya esetében.

1. Az üzembe helyezést követően módosíthatja a JIT hozzáférési beállításait.

1. A közzétevő vonatkozó hozzáférési kérést küld.

1. Jóváhagyhatja a kérést.

Ez a cikk a fogyasztók igény szerinti elérésének lehetővé tétele és a kérések jóváhagyása műveletek összpontosít. Igény szerinti hozzáféréssel rendelkező felügyelt alkalmazás közzététele kapcsolatos további információkért lásd: [just-in-time-hozzáférés az Azure Managed Applications kérése](request-just-in-time-access.md).

> [!NOTE]
> Igény szerinti hozzáférés használatához rendelkeznie kell egy [Azure Active Directory P2-licenc](../active-directory/privileged-identity-management/subscription-requirements.md).

## <a name="enable-during-deployment"></a>Üzembe helyezés során engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A Marketplace-en bejegyzés megkeresi egy felügyelt alkalmazás igény szerinti engedélyezve van. Kattintson a **Létrehozás** gombra.

1. Értékek az új felügyelt alkalmazás, miközben a **igény szerinti konfigurációs** a lépés lehetővé teszi, hogy engedélyezi vagy letiltja a felügyelt alkalmazás igény szerinti hozzáférést. Válassza ki **Igen** a **igény szerinti elérésének lehetővé tétele**. A beállítás engedélyezve van a Marketplace-en JIT meghatározott felügyelt alkalmazások számára alapértelmezés szerint.

   ![Hozzáférés konfigurálása](./media/approve-just-in-time-access/configure-jit-access.png)

   Üzembe helyezés során csak engedélyezheti az igény szerinti hozzáférést. Ha **nem**, a közzétevő állandó hozzáférést kap a felügyelt erőforráscsoporthoz. JIT hozzáférési később nem engedélyezhető.

1. Az alapértelmezett jóváhagyási beállítások módosításához válasszon **testreszabása igény szerinti konfigurációs**.

   ![Hozzáférés testreszabása](./media/approve-just-in-time-access/customize-jit-access.png)

   Alapértelmezés szerint egy felügyelt alkalmazást, az igény szerinti engedélyezve van a következő beállításokkal rendelkezik:

   * Engedélyezéses mód – automatikus
   * Hozzáférés maximális időtartama – 8 óránként
   * A jóváhagyók – none

   Ha a jóváhagyási mód beállítása **automatikus**, a jóváhagyók az egyes kérések értesítést kapni, de a kérelem jóváhagyása automatikus. Ha a beállítása **manuális**, a jóváhagyók értesítés küldése az egyes kérések és, az egyiket kell jóváhagynia.

   Az aktiválás maximális időtartama meghatározza a maximális időt közzétevő kérheti a felügyelt erőforráscsoporthoz való hozzáféréshez.

   Az Azure Active Directory-felhasználók, amelyek a JIT hozzáférési kérelem jóváhagyhatja a jóváhagyók Tranzakciólista. Jóváhagyó hozzáadása, válassza ki a **jóváhagyó hozzáadása** , és keresse meg a felhasználót.

   Miután frissítette a beállítást, jelölje be a **mentése**.

## <a name="update-after-deployment"></a>Üzembe helyezés után frissítése

Hogyan kérések engedélyezett értékeit módosíthatja. Azonban ha nem engedélyezte az igény szerinti hozzáférés üzembe helyezése során, nem engedélyezhető, később.

A központilag telepített felügyelt alkalmazás beállításainak módosítása:

1. A portálon válassza a manage alkalmazás.

1. Válassza ki **igény szerinti konfigurációs** és szükség szerint módosítsa a beállításokat.

   ![Hozzáférési beállítások módosítása](./media/approve-just-in-time-access/change-settings.png)

1. Ha elkészült, kattintson a **Mentés** elemre.

## <a name="approve-requests"></a>Kérések jóváhagyása

Amikor a közzétevő hozzáféréssel, értesítést kap a kérelem. JIT hozzáférési kérelmeket, vagy közvetlenül a felügyelt alkalmazás, vagy az Azure AD Privileged Identity Management szolgáltatáson keresztül felügyelt alkalmazások közötti jóváhagyhatja. Igény szerinti hozzáférés használatához rendelkeznie kell egy [Azure Active Directory P2-licenc](../active-directory/privileged-identity-management/subscription-requirements.md).

Jóváhagyásra váró kérelmek a felügyelt alkalmazáson keresztül:

1. Válassza ki **JIT hozzáférési** a felügyelt alkalmazást, és válassza a **kérések jóváhagyása**.

   ![Kérések jóváhagyása](./media/approve-just-in-time-access/approve-requests.png)
 
1. Válassza ki a jóváhagyandó kérést.

   ![Válassza ki a kérés](./media/approve-just-in-time-access/select-request.png)

1. A képernyőn, adja meg a jóváhagyási, és válassza az az oka **jóváhagyás**.

Jóváhagyásra váró kérelmek keresztül az Azure AD Privileged Identity Management:

1. Válassza ki **minden szolgáltatás** és kezdeni a keresést a **Azure AD Privileged Identity Management**. Válassza ki az elérhető lehetőségek közül.

   ![Keresési szolgáltatás](./media/approve-just-in-time-access/search.png)

1. Válassza ki **kérések jóváhagyása**.

   ![Válassza ki a kérések jóváhagyása](./media/approve-just-in-time-access/select-approve-requests.png)

1. Válassza ki **Azure által felügyelt alkalmazások**, és válassza ki a jóváhagyandó kérést.

   ![Válassza ki a kérelmek](./media/approve-just-in-time-access/view-requests.png)

## <a name="next-steps"></a>További lépések

Igény szerinti hozzáféréssel rendelkező felügyelt alkalmazás közzététele kapcsolatos további információkért lásd: [just-in-time-hozzáférés az Azure Managed Applications kérése](request-just-in-time-access.md).
