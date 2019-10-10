---
title: Hozzáférési csomaghoz való hozzáférés kérése az Azure AD-jogosultságok kezelésében (előzetes verzió) – Azure Active Directory
description: Megtudhatja, hogyan kérhet hozzáférést egy hozzáférési csomaghoz a Azure Active Directory jogosultságok kezelése (előzetes verzió) szolgáltatásban a saját hozzáférési portál használatával.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/19/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aaa58d3027fecdbfdf594e5712cbdbd758380a7
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169818"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Hozzáférési csomaghoz való hozzáférés kérése az Azure AD-jogosultságok kezelésében (előzetes verzió)

> [!IMPORTANT]
> A Azure Active Directory (Azure AD) jogosultság-kezelési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure AD-jogosultságok kezelésével a hozzáférési csomagok lehetővé teszik az erőforrások és házirendek egyszeri beállítását, amely automatikusan felügyeli a hozzáférési csomag élettartamának felügyeletét. 

Az Access Package Manager olyan házirendeket konfigurálhat, amelyek jóváhagyást igényelnek a felhasználók számára a hozzáférési csomagokhoz való hozzáféréshez. Egy hozzáférési csomag elérését igénylő felhasználónak kérést küldhet a hozzáféréshez. Ez a cikk egy hozzáférési kérelem küldését ismerteti.

## <a name="sign-in-to-the-my-access-portal"></a>Bejelentkezés a saját hozzáférési portálra

Első lépésként jelentkezzen be a saját hozzáférési portálra, ahol hozzáférést igényelhet egy hozzáférési csomaghoz.

**Előfeltételként szükséges szerepkör:** Kérelmező

1. Keressen egy e-mailt vagy egy üzenetet a projektből vagy a Business managerből, amelyen dolgozik. Az e-mailnek tartalmaznia kell a hozzáférési csomagra mutató hivatkozást, amelyre szüksége lesz. A hivatkozás a "myaccess" kifejezéssel kezdődik, tartalmaz egy könyvtárat, és a következőhöz hasonló hozzáférési csomag-AZONOSÍTÓval végződik:
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Nyissa meg a hivatkozást.

1. Jelentkezzen be a saját hozzáférési portálra.

    Ügyeljen arra, hogy a szervezeti (munkahelyi vagy iskolai) fiókot használja. Ha nem biztos a dolgában, forduljon a projekthez vagy a Business Managerhez.

## <a name="request-an-access-package"></a>Hozzáférési csomag igénylése

Miután megtalálta a hozzáférési csomagot a saját hozzáférési portálon, elküldheti a kérelmet.

**Előfeltételként szükséges szerepkör:** Kérelmező

1. Keresse meg a hozzáférési csomagot a listában.  Ha szükséges, a kereséshez írja be a keresési karakterláncot, majd válassza ki a **nevet**, a **katalógust**vagy az **erőforrások** szűrőt.

    ![Saját hozzáférési portál – erőforrás-keresés](./media/entitlement-management-request-access/elm-myaccess-resource-search.png)
1. Kattintson a pipa jelre a hozzáférési csomag kiválasztásához.

    ![Hozzáférési portál – hozzáférési csomagok](./media/entitlement-management-shared/my-access-access-packages.png)

1. Kattintson a **hozzáférés kérése** elemre a hozzáférés kérése panel megnyitásához.

1. Ha megjelenik az **üzleti indoklás** mező, írja be a hozzáféréshez szükséges indoklást.

1. Ha az **adott időszakra vonatkozó kérelem** van engedélyezve, válassza az **Igen** vagy a **nem**lehetőséget.

1. Szükség esetén a kezdési és a befejezési dátumot is meg kell adni.

    ![Hozzáférési portál – hozzáférés kérése](./media/entitlement-management-shared/my-access-request-access.png)

1. Ha elkészült, kattintson a **Submit (Küldés** ) gombra a kérelem elküldéséhez.

1. Kattintson a kérelmek **előzményei** gombra a kérések és az állapot listájának megtekintéséhez.

    Ha a hozzáférési csomag jóváhagyást igényel, a kérelem már függőben lévő jóváhagyási állapotban van.

## <a name="cancel-a-request"></a>Kérelem megszakítása

Ha hozzáférési kérelmet küld be, és a kérés még mindig a **függőben lévő jóváhagyási** állapotban van, megszakíthatja a kérelmet.

**Előfeltételként szükséges szerepkör:** Kérelmező

1. A saját hozzáférési portál bal oldalán kattintson a kérelmek **előzményei** elemre a kérések és az állapot megjelenítéséhez.

1. Kattintson a megszakítani kívánt kérelem **megtekintési** hivatkozására.

1. Ha a kérelem továbbra is a **függőben lévő jóváhagyási** állapotban van, a kérelem megszakításához kattintson a **kérelem megszakítása** lehetőségre.

    ![Saját hozzáférési portál – kérelem megszakítása](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Kattintson a **kérelmek előzményei** elemre a kérelem törlésének megerősítéséhez.

## <a name="select-a-policy"></a>Szabályzat kiválasztása

Ha olyan hozzáférési csomaghoz kér hozzáférést, amelyhez több szabályzat is tartozik, akkor előfordulhat, hogy ki kell választania egy szabályzatot. A hozzáférési csomag kezelője például két, belső alkalmazottak két csoportjára vonatkozó házirendet konfigurálhat. Az első szabályzat 60 napig is engedélyezheti a hozzáférést, és jóváhagyást igényelhet. A második szabályzat 2 napig is engedélyezheti a hozzáférést, és nem igényel jóváhagyást. Ha ebben a forgatókönyvben találkozik, ki kell választania a használni kívánt szabályzatot.

**Előfeltételként szükséges szerepkör:** Kérelmező

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési kérelmek jóváhagyása vagy megtagadása](entitlement-management-request-approve.md)
- [Kérelmek feldolgozása és e-mail-értesítések](entitlement-management-process.md)
