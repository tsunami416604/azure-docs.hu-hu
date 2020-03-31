---
title: Bejelentkezési hibákról szóló jelentések elhárítása | Microsoft dokumentumok
description: Megtudhatja, hogyan háríthatja el a bejelentkezési hibákat az Azure Active Directory-jelentések használatával az Azure Portalon
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec5fe7f62e8537a7f687202d365eb37d43b48b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008060"
---
# <a name="how-to-troubleshoot-sign-in-errors-using-azure-active-directory-reports"></a>Útmutató: Bejelentkezési hibák elhárítása az Azure Active Directory-jelentések használatával

Az Azure Active Directoryban (Azure AD) a [bejelentkezések jelentés](concept-sign-ins.md) lehetővé teszi, hogy válaszokat találjon a szervezetben lévő alkalmazásokhoz való hozzáférés kezelésével kapcsolatos kérdésekre, beleértve a következőket:

- Milyen egy adott felhasználó bejelentkezési mintázata?
- Hány felhasználó jelentkezett be egy adott héten?
- Milyen állapotúak ezek a bejelentkezések?


Emellett a bejelentkezések jelentés is segíthet a szervezet felhasználóinak bejelentkezési hibáinak elhárításában. Ebben az útmutatóban megtudhatja, hogyan különítheti el a bejelentkezési hibát a bejelentkezési jelentésben, és használhatja a hiba kiváltó okának megértéséhez.

## <a name="prerequisites"></a>Előfeltételek

A következők szükségesek:

* Egy Azure AD-bérlő prémium (P1/P2) licenccel. Az [Azure Active Directory-kiadás](../fundamentals/active-directory-get-started-premium.md) frissítéséhez olvassa el az Azure Active Directory Premium szolgáltatás első lépéseit.
* A **felhasználó,** aki a globális rendszergazda , **biztonsági rendszergazda**, **biztonsági olvasó**, vagy **jelentésolvasó** szerepkör a bérlő. Emellett bármely felhasználó hozzáférhet a saját bejelentkezések. 

## <a name="troubleshoot-sign-in-errors-using-the-sign-ins-report"></a>Bejelentkezési hibák elhárítása a bejelentkezési jelentéssel

1. Keresse meg az [Azure Portalt,](https://portal.azure.com) és válassza ki a címtárat.
2. Válassza ki **az Azure Active Directoryt,** és válassza a **Bejelentkezések** a **figyelési** szakaszban. 
3. A megadott szűrők segítségével szűkítheti a hibát a felhasználónév vagy az objektumazonosító, az alkalmazás neve vagy a dátum alapján. Ezenkívül válassza **a Hiba** lehetőséget az **Állapot** legördülő menüből, ha csak a sikertelen bejelentkezéseket szeretné megjeleníteni. 

    ![Szűrés eredményei](./media/howto-troubleshoot-sign-in-errors/filters.png)
        
4. Azonosítsa a vizsgálni kívánt sikertelen bejelentkezést. Jelölje ki, ha meg szeretné nyitni a további részleteket tartalmazó ablakot a sikertelen bejelentkezéssel kapcsolatos további információkkal. Jegyezze fel a **bejelentkezési hibakódot** és a **hiba okát.** 

    ![Rekord kijelölése](./media/howto-troubleshoot-sign-in-errors/sign-in-failures.png)
        
5. Ezeket az információkat a **Hibaelhárítás és támogatás** lapon, a részletek ablakban találja.

    ![Hibaelhárítás és támogatás](./media/howto-troubleshoot-sign-in-errors/troubleshooting-and-support.png)

6. A hiba oka a hibát írja le. A fenti forgatókönyv ben például a hiba oka **érvénytelen felhasználónév vagy jelszó, vagy érvénytelen helyszíni felhasználónév vagy jelszó.** A javítás az, hogy egyszerűen jelentkezzen be újra a megfelelő felhasználónevet és jelszót.

7. Ebben a példában az **50126-os** hibakód ban a [bejelentkezési hibakódok hivatkozásában](reference-sign-ins-error-codes.md)további információkat kaphat, beleértve a szervizelésre vonatkozó ötleteket is. 

8. Ha minden más sikertelen, vagy a probléma az ajánlott eljárás ellenére is fennáll, [nyisson meg egy támogatási jegyet](../fundamentals/active-directory-troubleshooting-support-howto.md) a **Hibaelhárítás és támogatás** lap lépéseit követve. 

## <a name="next-steps"></a>További lépések

* [Bejelentkezési hibakódok hivatkozása](reference-sign-ins-error-codes.md)
* [Bejelentkezési jelentés – áttekintés](concept-sign-ins.md)
