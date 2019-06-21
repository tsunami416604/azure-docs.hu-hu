---
title: Hozzon létre, és a egy az Azure ad-ben tagjogosultság-kezelés (előzetes verzió) – az Azure Active Directory-katalógus kezelése
description: Ismerje meg, hogyan hozhat létre egy új tárolót, az erőforrások és a hozzáférési csomagok az Azure Active Directory tagjogosultság-kezelés (előzetes verzió).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/29/2019
ms.author: rolyon
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5988f4723f1ef73cf0767ef8ac1b9adf3c1435d
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190245"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>Létrehozása és kezelése a katalógus az Azure ad-ben tagjogosultság-kezelés (előzetes verzió)

> [!IMPORTANT]
> Az Azure Active Directory (Azure AD) tagjogosultság-kezelés jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-catalog"></a>Katalógus létrehozása

A katalógus egy olyan tároló, erőforrások és a hozzáférési csomagok. A katalógus hoz létre, ha a kapcsolódó erőforrások csoportosítása és csomagjainak eléréséhez. A katalógus hoz létre, amikor valaki első katalógus tulajdonosává válik. A katalógus tulajdonosa adhat hozzá további katalógus tulajdonosai.

**Előfeltétel szerepkör:** Felhasználói rendszergazda vagy a katalógus létrehozója

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **katalógusok**.

    ![A jogosultság felügyeleti katalógusok az Azure Portalon](./media/entitlement-management-catalog-create/catalogs.png)

1. Kattintson a **új katalógus**.

1. Adjon meg egy egyedi nevet a katalógusban, és adjon meg egy leírást.

    Felhasználók látni fogják ezt az információt egy hozzáférési csomag részletei.

1. Ha azt szeretné, hogy a hozzáférési csomagok kérése a lehető legrövidebb időn belül jönnek létre, hogy a felhasználók számára elérhető legyen a katalógusban, **engedélyezve** való **Igen**.

1. Ha azt szeretné, hogy a felhasználók tudják a kérelem hozzáférési csomagok a katalógusban, állítsa be a kijelölt külső címtárakban **engedélyezett a külső felhasználók** való **Igen**.

    ![Új katalógus panel](./media/entitlement-management-catalog-create/new-catalog.png)

1. Kattintson a **létrehozás** a katalógus létrehozása.

## <a name="add-resources-to-a-catalog"></a>Erőforrások hozzáadása egy katalógushoz

Erőforrások foglalandó egy hozzáférés-csomagot, az erőforrásokat a katalógus léteznie kell. A típusú erőforrásokat is hozzáadhat olyan csoportok, alkalmazások és a SharePoint Online-webhelyhez. A csoportok lehetnek a felhő által létrehozott Office 365-csoportok vagy a felhő által létrehozott Azure AD biztonsági csoportokat. Az alkalmazások az Azure AD vállalati alkalmazásokat, beleértve a SaaS-alkalmazásokhoz és a saját Azure ad összevont alkalmazások is lehet. A helyek lehetnek, a SharePoint Online-webhelyek vagy a SharePoint Online webhelycsoportokhoz.

**Előfeltétel szerepkör:** Lásd: [szükséges erőforrások hozzáadása a katalógus szerepkörök](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **katalógusok** , majd nyissa meg a katalógus szeretne hozzáadni az erőforrásokat.

1. A bal oldali menüben kattintson a **erőforrások**.

1. Kattintson a **erőforrások hozzáadása**.

1. Kattintson egy erőforrás típusa: **Csoportok**, **alkalmazások**, vagy **SharePoint-webhelyek**.

    Ha nem jelenik meg, hogy a hozzáadni kívánt erőforrás vagy -erőforrás hozzáadása nem, ellenőrizze, hogy a szükséges Azure AD-címtárbeli szerepkör és a jogosultság felügyeleti szerepkör. Szüksége lehet az erőforrás hozzáadása a katalógus a szükséges szerepkörök rendelkező felhasználóra. További információkért lásd: [szükséges erőforrások hozzáadása a katalógus szerepkörök](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Válassza ki egy vagy több erőforrást, amelyet szeretne hozzáadni a katalógus-típus.

1. Ha befejezte, kattintson a **Hozzáadás**.

    Ezek az erőforrások már felvehetők a hozzáférési csomagok átveheti a katalógusban.

## <a name="remove-resources-from-a-catalog"></a>Távolítsa el az erőforrásokat a katalógusból

A katalógusból is eltávolíthatja az erőforrásokat. Egy erőforrás csak eltávolítható a katalógusból, ha nem van használatban, a katalógus hozzáférési csomagok egyikében.

**Előfeltétel szerepkör:** Lásd: [szükséges erőforrások hozzáadása a katalógus szerepkörök](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **katalógusok** , majd nyissa meg a katalógust el kívánja távolítani a erőforrásait.

1. A bal oldali menüben kattintson a **erőforrások**.

1. Válassza ki az eltávolítani kívánt erőforrásokat.

1. Kattintson a **eltávolítása** (vagy kattintson a három pont ( **...** ) majd **erőforrás eltávolítása**).

## <a name="edit-a-catalog"></a>Katalógus szerkesztése

Módosíthatja a nevet és leírást a katalógus. Felhasználók látják ezt az információt egy hozzáférési csomag részletei.

**Előfeltétel szerepkör:** Felhasználói rendszergazda vagy a katalógus tulajdonos

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **katalógusok** , majd nyissa meg a szerkeszteni kívánt katalógus.

1. A katalógus **áttekintése** kattintson **szerkesztése**.

1. Módosítsa a katalógus neve vagy leírása.

1. Kattintson a **Save** (Mentés) gombra.

## <a name="delete-a-catalog"></a>Katalógus törlése

Egy katalógus, de csak ha nincs semmilyen hozzáférési csomagok törölhetők.

**Előfeltétel szerepkör:** Felhasználói rendszergazda vagy a katalógus tulajdonos

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **katalógusok** , majd nyissa meg a törölni kívánt katalógus.

1. A katalógus **áttekintése**, kattintson a **törlése**.

1. A megjelenő üzenetpanelen kattintson **Igen**.

## <a name="next-steps"></a>További lépések

- [Adja hozzá a katalógus-készítő](entitlement-management-delegate.md#add-a-catalog-creator)
- [Létrehozása és kezelése egy hozzáférés-csomag](entitlement-management-access-package-create.md)
