---
title: Hozzáférés új csomag létrehozása az Azure ad-ben tagjogosultság-kezelés (előzetes verzió) – az Azure Active Directory
description: Megtudhatja, hogyan hozhat létre az Azure Active Directory tagjogosultság-kezelés (előzetes verzió) a megosztani kívánt erőforrások új hozzáférés-csomagot.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/24/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ad6570a3f30e40e4074502a8ce85bf739f58d3f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866442"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>Hozzáférés új csomag létrehozása az Azure ad-ben tagjogosultság-kezelés (előzetes verzió)

> [!IMPORTANT]
> Az Azure Active Directory (Azure AD) tagjogosultság-kezelés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Egy hozzáférési csomag lehetővé teszi, hogy az erőforrások és a szabályzatok egy egyszeri beállítás, amely automatikusan felügyeli a hozzáférést a hozzáférés csomag élettartama. Ez a cikk bemutatja, hogyan hozhat létre egy új hozzáférési csomagot.

## <a name="overview"></a>Áttekintés

Minden hozzáférési csomagok egy katalógus nevű tárolóban kell elhelyezni. A katalógus milyen erőforrásokat is hozzáadhat a hozzáférés-csomag határozza meg. Ha nem ad meg egy katalógus, a hozzáférés-csomagot az általános katalógusba kerülnek az. Egy másik katalógusban jelenleg nem helyezhetők át egy meglévő hozzáférési csomagot.

Minden hozzáférési csomagok rendelkeznie kell legalább egy szabályzat. Házirendek határozzák meg, akik kérheti a hozzáférés csomag és a is jóváhagyása és a lejárati beállítások. Ha egy új hozzáférési csomagot hoz létre, a címtárban, a felhasználók számára a címtárban, a rendszergazda csak a közvetlen hozzárendelések nem hozhat létre egy kezdeti szabályzatot a felhasználók számára, vagy választhatja azt is, a szabályzat létrehozása később.

Az alábbi ábrán látható, a magas szintű folyamata hozzon létre egy új hozzáférési csomagot.

![Hozzon létre egy hozzáférési csomag folyamatot](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>Indítsa el az új hozzáférés-csomag

**Előfeltétel szerepkör:** Felhasználói rendszergazda vagy a katalógus tulajdonos

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **csomagok eléréséhez**.

    ![Tagjogosultság-kezelés az Azure Portalon](./media/entitlement-management-shared/elm-access-packages.png)

1. Kattintson a **új hozzáférési csomag**.

## <a name="basics"></a>Alapvető beállítások

Az a **alapjai** lap, adja meg a hozzáférés-csomag nevét és adja meg, mely a katalógusban, az access-csomag létrehozása.

1. Adjon meg egy megjelenített nevet és leírást a hozzáférés-csomag. Felhasználók a hozzáférés-csomag kérelem küldése, ha ez az információ jelenik meg.

1. Az a **katalógus** legördülő listában válassza a katalógus szeretne létrehozni a hozzáférést a csomag. Például előfordulhat, hogy lehet, amely kezeli a marketing erőforrásokat, amelyek igényelhetnek katalógus tulajdonosa. Ebben az esetben a marketing katalógus bejelölheti.

    Katalógusok csak akkor jelenik meg a hozzáférési csomagok létrehozásához szükséges engedéllyel rendelkezik. Hozzáférés csomag létrehozásához a meglévő katalógus, kell lennie legalább egy felhasználói rendszergazda, a katalógus tulajdonos vagy a hozzáférés Csomagkezelő.

    ![Csomag – alapvető tudnivalók eléréséhez](./media/entitlement-management-access-package-create/basics.png)

    Ha szeretné a hozzáférés-csomag létrehozása az új katalógus, kattintson a **új létrehozása**. Adja meg a katalógus nevét és leírását, és kattintson a **létrehozás**.

    A hozzáférés csomagot hoz létre, és a benne foglalt erőforrásokat megjelenik az új katalógus. Emellett automatikusan Ön lesz a katalógus első tulajdonosa. Hozzáadhat további katalógus tulajdonosai.

    Hozzon létre egy új katalógust, hogy legalább egy felhasználó rendszergazda vagy a katalógus létrehozójának kell lennie.

1. Kattintson a **tovább**.

## <a name="resource-roles"></a>Erőforrás-szerepkörök

Az a **erőforrás-szerepkörökkel** lapon, válassza ki az erőforrásokat a hozzáférés csomaghoz.

1. Kattintson a hozzáadni kívánt erőforrás típusa (**csoportok**, **alkalmazások**, vagy **SharePoint-webhelyek**).

1. A megjelenő válassza ablaktáblán válassza ki a listából egy vagy több erőforrást.

    ![Access-package - erőforrásszerepkörök](./media/entitlement-management-access-package-create/resource-roles.png)

    A hozzáférés-csomagot az általános és egy új katalógus a létrehozásakor, lesz a címtárból, saját bármely erőforrás kiválasztása. Legalább egy felhasználó rendszergazdai vagy a katalógus létrehozó kell.

    A meglévő katalógus létrehozásakor a hozzáférés-csomag, kiválaszthatja valamelyik erőforrás, amely már szerepel a katalógusban, a tulajdonos nélkül.

    Ha egy felhasználó rendszergazda vagy a katalógus tulajdonos, lehetősége van további erőforrások a saját, amelyek nem, de a katalógus kiválasztása. Ha erőforrás jelenleg nem a kiválasztott katalógus, ezeket az erőforrásokat is felveszi a hozzáférési csomagok más katalógus-rendszergazdák számára a katalógushoz. Ha azt szeretné, hogy csak válassza ki az erőforrásokat, amelyek jelenleg a kiválasztott katalógus, ellenőrizze a **csak** felső részén válassza ki a pan jelölőnégyzetet.

1. Miután kiválasztotta, az erőforrásokat a **szerepkör** listájához, válassza ki a felhasználóknak hozzá kell rendelni az erőforrás kívánt szerepkört.

    ![Access-package - erőforrás szerepkör kiválasztása](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Kattintson a **tovább**.

## <a name="policy"></a>Szabályzat

Az a **házirend** lapon hoz létre az első házirend, akik kérheti a hozzáférés csomag és a is jóváhagyása és a lejárati beállítások megadásához. Később további házirendeket, hogy a felhasználók a hozzáférés csomag kérést a saját jóváhagyás és a lejárati beállítások további csoportokat is létrehozhat. A szabályzat létrehozása később is beállíthatja.

1. Állítsa be a **első szabályzat létrehozása** kapcsolót **most** vagy **később**.

    ![Access-package - házirend](./media/entitlement-management-access-package-create/policy.png)

1. Ha **később**, le a kihagyása a [felülvizsgálati + létrehozása](#review--create) szakaszban a hozzáférés-csomag létrehozása.

1. Ha **most**, hajtsa végre a lépéseket a következő házirend-szakaszok egyikét.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>Áttekintés + létrehozás

Az a **tekintse át + létrehozása** lapon áttekintheti a beállításokat, és ellenőrizze az esetleges érvényesítési hibákat.

1. Tekintse át a beállításokat a hozzáférés-csomag

    ![Access-package - házirend engedélyezése a házirend-beállítással](./media/entitlement-management-access-package-create/review-create.png)

1. Kattintson a **létrehozás** a hozzáférés-csomag létrehozásához.

    Az új hozzáférés-csomagot a hozzáférési csomagok listájában jelenik meg.

## <a name="next-steps"></a>További lépések

- [Szerkesztése és kezelése a meglévő hozzáférési csomag](entitlement-management-access-package-edit.md)
- [Hozzon létre és -katalógus kezelése](entitlement-management-catalog-create.md)
