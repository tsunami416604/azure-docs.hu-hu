---
title: Katalógus létrehozása és kezelése az Azure AD-jogosultságok kezelésében (előzetes verzió) – Azure Active Directory
description: Ismerje meg, hogyan hozhat létre új erőforrás-tárolót és hozzáférési csomagokat a Azure Active Directory jogosultságok kezelése (előzetes verzió) szolgáltatásban.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e563d86abe3817e4c77cc0d5c8df928e41563f8
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489088"
---
# <a name="create-and-manage-a-catalog-in-azure-ad-entitlement-management-preview"></a>Katalógus létrehozása és kezelése az Azure AD-jogosultságok kezelésében (előzetes verzió)

> [!IMPORTANT]
> A Azure Active Directory (Azure AD) jogosultság-kezelési szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-catalog"></a>Katalógus létrehozása

A katalógus erőforrások és hozzáférési csomagok tárolója. Létre kell hoznia egy katalógust, ha a kapcsolódó erőforrásokat és a hozzáférési csomagokat szeretné csoportosítani. Aki létrehozza a katalógust, az első katalógus tulajdonosa lesz. A katalógus tulajdonosa további katalógus-tulajdonosokat adhat hozzá.

**Előfeltételként szükséges szerepkör:** Felhasználói rendszergazda vagy katalógus létrehozója

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Kattintson a **Azure Active Directory** , majd az **identitás-szabályozás**elemre.

1. A bal oldali menüben kattintson a **katalógusok**elemre.

    ![Jogosultsági felügyeleti katalógusok a Azure Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Kattintson az **új katalógus**lehetőségre.

1. Adjon meg egy egyedi nevet a katalógus számára, és adjon meg egy leírást.

    A felhasználók ezeket az információkat egy hozzáférési csomag részleteiben látják.

1. Ha azt szeretné, hogy a katalógushoz tartozó hozzáférési csomagok elérhetők legyenek a felhasználók számára a létrehozásuk után, állítsa az **engedélyezve** beállítást **Igen**értékre.

1. Ha engedélyezni szeretné a kiválasztott külső címtárakban lévő felhasználók számára a hozzáférési csomagok kérését ebben a katalógusban, akkor a **külső felhasználók számára engedélyezze a** beállítást **Igen**értékre.

    ![Új katalógus panel](./media/entitlement-management-catalog-create/new-catalog.png)

1. A katalógus létrehozásához kattintson a **Létrehozás** gombra.

## <a name="add-resources-to-a-catalog"></a>Erőforrások hozzáadása egy katalógushoz

Ahhoz, hogy egy hozzáférési csomagban erőforrásokat tartalmazzon, az erőforrásoknak léteznie kell egy katalógusban. A felvehető erőforrások típusai: csoportok, alkalmazások és SharePoint Online-webhelyek. A csoportok lehetnek felhőben létrehozott Office 365-csoportok vagy felhőalapú Azure AD-alapú biztonsági csoportok. Az alkalmazások lehetnek az Azure ad vállalati alkalmazások, például az SaaS-alkalmazások és az Azure AD-ben összevont saját alkalmazások is. A webhelyek lehetnek SharePoint Online-webhelyek vagy SharePoint Online-webhelycsoportok.

**Előfeltételként szükséges szerepkör:** [Erőforrások katalógusba való felvételéhez szükséges szerepkörök](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **katalógusok** lehetőségre, majd nyissa meg azt a katalógust, amelyhez erőforrásokat szeretne hozzáadni.

1. A bal oldali menüben kattintson az **erőforrások**elemre.

1. Kattintson az **erőforrások hozzáadása**lehetőségre.

1. Válassza ki az erőforrás típusát: **Csoportok**, **alkalmazások**vagy **SharePoint-webhelyek**.

    Ha nem lát olyan erőforrást, amelyet hozzá szeretne adni, vagy nem tud hozzáadni egy erőforrást, győződjön meg arról, hogy rendelkezik a szükséges Azure AD-címtárbeli szerepkörrel és jogosultság-kezelési szerepkörrel. Előfordulhat, hogy a szükséges szerepkörökkel rendelkező személynek hozzá kell adnia az erőforrást a katalógushoz. További információ: a [szükséges szerepkörök az erőforrások katalógushoz való hozzáadásához](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Válasszon ki egy vagy több olyan típusú erőforrást, amelyet hozzá szeretne adni a katalógushoz.

1. Ha elkészült, kattintson a **Hozzáadás**gombra.

    Ezek az erőforrások mostantól a katalógusban lévő hozzáférési csomagokba is belefoglalhatók.

## <a name="remove-resources-from-a-catalog"></a>Erőforrások eltávolítása egy katalógusból

A katalógusból eltávolíthatja az erőforrásokat. Egy erőforrás csak akkor távolítható el a katalógusból, ha a katalógus egyik hozzáférési csomagja sem használja.

**Előfeltételként szükséges szerepkör:** [Erőforrások katalógusba való felvételéhez szükséges szerepkörök](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **katalógusok** lehetőségre, majd nyissa meg azt a katalógust, amelyből el szeretné távolítani az erőforrásokat.

1. A bal oldali menüben kattintson az **erőforrások**elemre.

1. Válassza ki az eltávolítani kívánt erőforrásokat.

1. Kattintson az **Eltávolítás** gombra (vagy kattintson a három pontra ( **...** ), majd az **erőforrás eltávolítása**gombra.

## <a name="edit-a-catalog"></a>Katalógus szerkesztése

Szerkesztheti a katalógus nevét és leírását. A felhasználók ezt az információt egy hozzáférési csomag részleteiben látják.

**Előfeltételként szükséges szerepkör:** Felhasználói rendszergazda vagy katalógus tulajdonosa

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **katalógusok** lehetőségre, majd nyissa meg a szerkeszteni kívánt katalógust.

1. A katalógus **Áttekintés** lapján kattintson a **Szerkesztés**elemre.

1. Szerkessze a katalógus nevét vagy leírását.

1. Kattintson a **Save** (Mentés) gombra.

## <a name="delete-a-catalog"></a>Katalógus törlése

A katalógust törölheti, de csak akkor, ha nincs hozzáférési csomagja.

**Előfeltételként szükséges szerepkör:** Felhasználói rendszergazda vagy katalógus tulajdonosa

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás**elemre.

1. A bal oldali menüben kattintson a **katalógusok** lehetőségre, majd nyissa meg a törölni kívánt katalógust.

1. A katalógus áttekintésében kattintson a **Törlés**elemre.

1. A megjelenő üzenetablakban kattintson az **Igen**gombra.

## <a name="next-steps"></a>További lépések

- [Katalógus létrehozójának hozzáadása](entitlement-management-delegate.md#add-a-catalog-creator)
- [Hozzáférési csomag létrehozása és kezelése](entitlement-management-access-package-create.md)
