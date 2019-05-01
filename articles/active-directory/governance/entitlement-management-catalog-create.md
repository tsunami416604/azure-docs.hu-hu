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
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6d9220cd2162b4c8cb77c1e7abd0372052f5454
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541615"
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

Erőforrások foglalandó egy hozzáférés-csomagot, az erőforrásokat a katalógus léteznie kell. A típusú erőforrásokat is hozzáadhat olyan csoportok, alkalmazások és a SharePoint Online-webhelyhez.

**Előfeltétel szerepkör:** Felhasználói rendszergazda vagy a katalógus tulajdonos

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **katalógusok** , majd nyissa meg a katalógus szeretne hozzáadni az erőforrásokat.

1. A bal oldali menüben kattintson a **erőforrások**.

1. Kattintson a **erőforrások hozzáadása**.

1. Kattintson egy erőforrás típusa: **Csoportok**, **alkalmazások**, vagy **SharePoint-webhelyek**.

    Ha Ön a katalógus létrehozó, minden Office 365-csoport vagy a katalógus a saját Azure AD biztonsági csoportot is hozzáadhat. Ha egy csoportot, amely a felhasználók számára hozzárendelni kívánt van, de Ön nem tulajdonosa a csoportnak, szüksége lesz egy adott csoportot adja hozzá a katalógus felhasználói rendszergazda rendelkezik.

    Ha Ön a katalógus létrehozó, minden olyan Azure AD vállalati alkalmazás tulajdonosa, beleértve a SaaS-alkalmazásokhoz és a saját alkalmazások összevonva az Azure ad-ben, a katalógus is hozzáadhat. Ha olyan alkalmazás, amely hozzá szeretné rendelni a felhasználók számára, de nem Ön a tulajdonosa, szüksége lesz egy felhasználói rendszergazdája, vegye fel a katalógust, hogy alkalmazást rendelkezik. Ha az alkalmazás a katalógusba, az alkalmazás-szerepkörök közül választhat egy hozzáférés-csomagot.

1. Válassza ki egy vagy több erőforrást, amelyet szeretne hozzáadni a katalógus-típus.

1. Ha befejezte, kattintson a **Hozzáadás**.

    Ezek az erőforrások már felvehetők a hozzáférési csomagok átveheti a katalógusban.

## <a name="remove-resources-from-a-catalog"></a>Távolítsa el az erőforrásokat a katalógusból

A katalógusból is eltávolíthatja az erőforrásokat. Egy erőforrás csak eltávolítható a katalógusból, ha nem van használatban, a katalógus hozzáférési csomagok egyikében.

**Előfeltétel szerepkör:** Felhasználói rendszergazda vagy a katalógus tulajdonos

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **katalógusok** , majd nyissa meg a katalógust el kívánja távolítani a erőforrásait.

1. A bal oldali menüben kattintson a **erőforrások**.

1. Válassza ki az eltávolítani kívánt erőforrásokat.

1. Kattintson a **eltávolítása** (vagy kattintson a három pont (**...** ) majd **erőforrás eltávolítása**).

## <a name="add-catalog-owners-or-access-package-managers"></a>Katalógus tulajdonosok hozzáadása vagy csomagkezelők eléréséhez

Ha szeretné a katalógus vagy a hozzáférési csomagok a katalógusban felügyelet delegálása, katalógus tulajdonosok hozzáadása vagy csomagkezelők eléréséhez. Amikor valaki létrehoz egy katalógust lesz a katalógus első tulajdonosa.

**Előfeltétel szerepkör:** Felhasználói rendszergazda vagy a katalógus tulajdonos

1. Az Azure Portalon kattintson a **Azure Active Directory** majd **Identitáskezelést**.

1. A bal oldali menüben kattintson a **katalógusok** , majd nyissa meg a katalógus-rendszergazdák számára, hogy a hozzáadni kívánt.

1. A bal oldali menüben kattintson a **szerepkörök és a rendszergazdák**.

1. Kattintson a **tulajdonosok hozzáadása** vagy **hozzáférés csomagkezelők hozzáadása** válassza ki a kívánt ezek a szerepkörök tagjait.

1. Kattintson a **kiválasztása** ezekről a tagokról hozzáadásához.

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

- [Létrehozása és kezelése egy hozzáférés-csomag](entitlement-management-access-package-create.md)
