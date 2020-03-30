---
title: Javaslatkivonat az Azure Advisorszámára
description: Rendszeres összegzés az aktív javaslatokról
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 98f1bfd42a486e005cd1e777a83f5b615a7c8304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502463"
---
# <a name="configure-periodic-summary-for-recommendations"></a>Javaslatok időszakos összegzésének konfigurálása

A tanácsadói **ajánlási kivonatokkal** egyszerűen és proaktív módon őrizheti meg aktív javaslatait, különböző kategóriákban. A szolgáltatás lehetővé teszi, hogy rendszeres értesítéseket konfiguráljon az összes aktív javaslat összegzéséhez, különböző kategóriákban. Az akciócsoportok segítségével kiválaszthatja a kívánt csatornát az olyan értesítésekhez, mint az e-mail, az SMS vagy mások. Ez a cikk bemutatja, hogyan állíthat be egy **javaslat kivonata** a tanácsadó ijavaslatok.


## <a name="setting-up-your-recommendation-digest"></a>Az ajánláskivonat beállítása 

A **javaslat kivonatolási** létrehozási felület segít az összegzés konfigurálása. A konfigurációk alábbi paramétereit választhatja ki:
1. Kategória: Olyan ajánlási kategóriákkal rendelkezünk, mint a költség, a magas rendelkezésre állás, a teljesítmény és a működési kiválóság. A képesség még nem érhető el a biztonsági javaslatokhoz.
2. Az emésztés gyakorisága: Az összefoglaló értesítések gyakorisága lehet heti, kéthetente és havonta.
3. Műveletcsoport: Kijelölhet egy meglévő műveletcsoportot, vagy létrehozhat egy új műveletcsoportot. A műveletcsoportokról a [Műveletcsoportok létrehozása és kezelése](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)című témakörben olvashat bővebben.
4. Az emésztés nyelve
5. Javaslat kivonatoló neve: Felhasználóbarát karakterlánc használatával jobban nyomon követheti és figyelheti az emésztéseket.

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Javaslatok kivonatlétrehozásának lépései az Azure Portalon

A **javaslatkivonat** létrehozásának lépései:
* **1. lépés:** Az Azure Portalon nyissa meg az **Advisort,** és **a Figyelés** szakaszban válassza **a Javaslat kivonatlehetőséget.** 

   ![Ajánlás kivonatoló belépési pont](./media/digest-0.png)

* **2. lépés:** Válassza az **Új javaslat kivonatolás a** felső sávból az alábbi módon lehetőséget:

   ![Javaslatkivonat létrehozása](./media/digest-5.png)

* **3. lépés:** A **hatókör** szakaszban válassza ki a kivonatoló **előfizetését.**

   ![Ajánláskivonat-bemenetek biztosítása](./media/digest-1.png)

* **4. lépés:** A **feltétel** szakaszban válassza ki a **kategóriákat,** **gyakoriságot** és **nyelvet**

   ![Ajánláskivonat-beviteli feltételek biztosítása](./media/digest-2.png)

* **5. lépés:** A **műveletcsoport** szakaszban jelölje ki a **kivonatoló műveletcsoportját.** Itt további információ – [Műveletcsoportok létrehozása és kezelése](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

   ![Javaslat kivonatoló beviteli műveletcsoportjának biztosítása](./media/digest-3.png)

* **6. lépés:** Ebben az utolsó szakaszban kivonat **részletek**, nevet és állapotot rendelhet a javaslat kivonat. Nyomja **meg a create recommendation digest billentyűt** a beállítás befejezéséhez.
   ![Teljes ajánláskivonat létrehozása](./media/digest-4.png)

## <a name="next-steps"></a>További lépések

További információ az Advisor-ajánlásokról:
* [Bevezetés az Azure Advisorba](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Tanácsadói költségajánlások](advisor-cost-recommendations.md)
* [Tanácsadó teljesítményre vonatkozó ajánlásai](advisor-performance-recommendations.md)
* [Tanácsadó biztonsági ajánlásai](advisor-security-recommendations.md)
* [Az Advisor Operational Excellence ajánlásai](advisor-operational-excellence-recommendations.md)
* [Tanácsadó PIHENÉS API](https://docs.microsoft.com/rest/api/advisor/)
