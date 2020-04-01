---
title: Erőforrások katalógusának létrehozása & kezelése a jogosultságkezelésben – Azure AD
description: Ismerje meg, hogyan hozhat létre új tárolót az erőforrásokból és a hozzáférési csomagokhoz az Azure Active Directory jogosultságkezelésében.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffa6f2fbdcde41d81e268779c3a0586bd8310792
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437558"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Erőforrások katalógusának létrehozása és kezelése az Azure AD jogosultságkezelésében

## <a name="create-a-catalog"></a>Katalógus létrehozása

A katalógus erőforrások és hozzáférési csomagok tárolója. Katalógust akkor hoz létre, ha a kapcsolódó erőforrásokat és hozzáférési csomagokat szeretné csoportosítani. Aki létrehozza a katalógust, az lesz az első katalógustulajdonos. A katalógus tulajdonosa további katalógustulajdonosokat is hozzáadhat.

**Előfeltételi szerepkör:** Globális rendszergazda, felhasználó vagy katalóguskészítő

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson a **Katalógusok parancsra.**

    ![Jogosultságkezelési katalógusok az Azure Portalon](./media/entitlement-management-catalog-create/catalogs.png)

1. Kattintson **az Új katalógus gombra.**

1. Adja meg a katalógus egyedi nevét, és adjon meg egy leírást.

    A felhasználók ezeket az információkat a hozzáférési csomag részleteiben fogják látni.

1. Ha azt szeretné, hogy a katalógusban szereplő hozzáférési csomagok a létrehozásuk után azonnal kérjenek, állítsa **az Engedélyezve értéket** **Igen**értékre.

1. Ha engedélyezni szeretné, hogy a kiválasztott külső könyvtárak felhasználói hozzáférési csomagokat kérjenek ebben a katalógusban, állítsa **az Engedélyezve a külső felhasználók számára értékre** **igen**értéket.

    ![Új katalógus ablaktábla](./media/entitlement-management-shared/new-catalog.png)

1. A katalógus létrehozásához kattintson a **Létrehozás** gombra.

### <a name="creating-a-catalog-programmatically"></a>Katalógus létrehozása programozott módon

Katalógust a Microsoft Graph segítségével is létrehozhat.  A delegált `EntitlementManagement.ReadWrite.All` engedéllyel rendelkező alkalmazással rendelkező alkalmazással rendelkező felhasználó meghívhatja az API-t [accessPackageCatalog létrehozásához.](https://docs.microsoft.com/graph/api/accesspackagecatalog-post?view=graph-rest-beta)

## <a name="add-resources-to-a-catalog"></a>Erőforrások hozzáadása katalógushoz

Az erőforrások hozzáférési csomagba való felvételéhez az erőforrásoknak szerepelniük kell egy katalógusban. A hozzáadható erőforrások típusai csoportok, alkalmazások és SharePoint Online-webhelyek. A csoportok lehetnek felhőalapú Office 365 Csoportok vagy felhőalapú Azure AD biztonsági csoportok. Az alkalmazások lehetnek Azure AD nagyvállalati alkalmazások, beleértve a SaaS-alkalmazások at és az Azure AD-hez összevont saját alkalmazásokat is. A webhelyek lehetnek SharePoint Online-webhelyek vagy SharePoint Online-webhelycsoportok.

**Előfeltételi szerepkör:** Lásd: [Szükséges szerepkörök erőforrások hozzáadása katalógushoz](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson a **Katalógusok parancsra,** majd nyissa meg azt a katalógust, amelyhez erőforrásokat szeretne hozzáadni.

1. A bal oldali menüben kattintson az **Erőforrások parancsra.**

1. Kattintson **az Erőforrások hozzáadása gombra.**

1. Kattintson egy erőforrástípusra: **Csoportok és csapatok,** **alkalmazások**vagy **SharePoint-webhelyek**.

    Ha nem látja a hozzáadni kívánt erőforrást, vagy nem tud hozzáadni egy erőforrást, győződjön meg arról, hogy rendelkezik a szükséges Azure AD címtárszerepkörrel és jogosultságkezelési szerepkörrel. Előfordulhat, hogy a szükséges szerepkörökkel rendelkező személynek hozzá kell adnia az erőforrást a katalógushoz. További információ: [Szükséges szerepkörök erőforrások hozzáadása katalógushoz](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Jelöljön ki egy vagy több olyan típusú erőforrást, amelyet hozzá szeretne adni a katalógushoz.

    ![Erőforrások hozzáadása katalógushoz](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Ha végzett, kattintson a **Hozzáadás gombra.**

    Ezek az erőforrások most már szerepelhetnek a katalógushozzáférési csomagokban.

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Erőforrás hozzáadása a katalógushoz programozott módon

A Microsoft Graph segítségével erőforrásokat is hozzáadhat egy katalógushoz.  A megfelelő szerepkörben lévő vagy katalógus- és erőforrástulajdonos, delegált `EntitlementManagement.ReadWrite.All` engedéllyel rendelkező alkalmazással rendelkező felhasználó meghívhatja az API-t [accessPackageResourceRequest létrehozásához.](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta)

## <a name="remove-resources-from-a-catalog"></a>Erőforrások eltávolítása katalógusból

Az erőforrásokat eltávolíthatja a katalógusból. Egy erőforrás csak akkor távolítható el a katalógusból, ha azokat nem használják a katalógus egyik hozzáférési csomagjában sem.

**Előfeltételi szerepkör:** Lásd: [Szükséges szerepkörök erőforrások hozzáadása katalógushoz](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson a **Katalógusok parancsra,** majd nyissa meg azt a katalógust, amelyből el szeretné távolítani az erőforrásokat.

1. A bal oldali menüben kattintson az **Erőforrások parancsra.**

1. Jelölje ki az eltávolítani kívánt erőforrásokat.

1. Kattintson **az Eltávolítás** gombra (vagy kattintson a három pontra (**...**), majd az **Erőforrás eltávolítása**parancsra).

## <a name="add-additional-catalog-owners"></a>További katalógustulajdonosok hozzáadása

A katalógust létrehozó felhasználó lesz az első katalógustulajdonos. Katalógus kezelésének delegálásához felhasználókat kell hozzáadnia a katalógus tulajdonosi szerepköréhez. Ez segít megosztani a katalóguskezelési feladatokat. 

A felhasználó hozzárendelése a katalógus tulajdonosi szerepköréhez az alábbi lépésekkel:

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználó vagy katalógus tulajdonosa

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson a **Katalógusok parancsra,** majd nyissa meg azt a katalógust, amelyhez rendszergazdákat szeretne hozzáadni.

1. A bal oldali menüben kattintson a **Szerepkörök és rendszergazdák parancsra.**

    ![Katalógusok szerepkörök és rendszergazdák](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Kattintson **a Tulajdonosok hozzáadása** gombra a szerepkörök tagjainak kiválasztásához.

1. A tagok hozzáadásához kattintson a **Kijelölés** gombra.

## <a name="edit-a-catalog"></a>Katalógus szerkesztése

A katalógus nevét és leírását módosíthatja. A felhasználók ezeket az információkat egy hozzáférési csomag részleteiben látják.

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználó vagy katalógus tulajdonosa

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson a **Katalógusok parancsra,** majd nyissa meg a szerkesztni kívánt katalógust.

1. A katalógus **Áttekintés lapján** kattintson a **Szerkesztés gombra.**

1. A katalógus nevének, leírásának vagy engedélyezett beállításainak szerkesztése.

    ![Katalógusbeállítások szerkesztése](./media/entitlement-management-shared/catalog-edit.png)

1. Kattintson a **Mentés** gombra.

## <a name="delete-a-catalog"></a>Katalógus törlése

Törölheti a katalógust, de csak akkor, ha nem rendelkezik hozzáférési csomagokkal.

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználó vagy katalógus tulajdonosa

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson a **Katalógusok parancsra,** majd nyissa meg a törölni kívánt katalógust.

1. A katalógus **áttekintése**oldalon kattintson a **Törlés gombra.**

1. A megjelenő üzenetpanelen kattintson az **Igen**gombra.

### <a name="deleting-a-catalog-programmatically"></a>Katalógus törlése programozott módon

Katalógust a Microsoft Graph segítségével is törölhet.  A delegált `EntitlementManagement.ReadWrite.All` engedéllyel rendelkező alkalmazással rendelkező alkalmazással rendelkező felhasználó meghívhatja az API-t [az accessPackageCatalog törléséhez.](https://docs.microsoft.com/graph/api/accesspackagecatalog-delete?view=graph-rest-beta)

## <a name="next-steps"></a>További lépések

- [Hozzáférés-szabályozás delegálása a csomagkezelőkszámára](entitlement-management-delegate-managers.md)
