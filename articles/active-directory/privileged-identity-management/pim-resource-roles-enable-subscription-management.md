---
title: A bérlő – Azure előfizetés-kezelés engedélyezése |} A Microsoft Docs
description: Ismerje meg, hogy a bérlő előfizetés-kezelés engedélyezése az Azure AD Privileged Identity Management (PIM) használatakor.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: bcaea64484010672a53050cb806c4256a17b19a8
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901175"
---
# <a name="enable-subscription-management-in-your-tenant"></a>A bérlő előfizetés-kezelés engedélyezése

A címtár globális rendszergazdaként hogy előfordulhat, hogy nem fér alapértelmezett összes-előfizetések erőforrásaihoz való a bérlőben. Ez a cikk a saját maga hozzáférést biztosíthat a bérlőn belüli összes előfizetés lépéseit ismerteti. Azt is javasolt módszert biztosít a fennmaradó felelnek meg minden olyan biztonsági ellenőrzéseket, a szervezet megköveteli, miután megkapta a hozzáférést.

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>Ki engedélyezheti a címtáramban előfizetések kezelése?

Minden felhasználó, a globális rendszergazda szerepkörrel kell az alábbi lépésekkel előfizetés-kezelés engedélyezéséhez. Miután engedélyezte a saját előfizetés-kezelési, adhat hozzá más globális rendszergazdákat, akiknek előfordulhat, hogy szükség van az erőforrás hozzáféréssel is. Nem tartozik Directoryban beállítás, amely lehetővé teszi a hozzáférést a globális rendszergazdai szerepkör összes tagja számára.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az Azure Portalra egy olyan fiókkal, amely a globális rendszergazdai szerepkör jogosult vagy aktív tagja. Ha a fiók jogosult globális rendszergazda, először aktiválnia kell a szerepkör a következő lépés váltás előtt.

## <a name="access-the-azure-active-directory-admin-center"></a>Az Azure Active Directory felügyeleti központ elérése

Most, hogy az Azure Portalra globális rendszergazdaként jelentkezett be, szerkesztheti a beállításokat, amelyek az Azure-előfizetések hozzáférést biztosítanak. Keresse meg az Azure Active Directory (Azure AD) felügyeleti központban, és válassza ki **tulajdonságok**.

![Képernyőkép az Azure AD felügyeleti központban, kiemelve tulajdonságokkal](media/azure-pim-resource-rbac/aad_properties.png)

A tulajdonságok listájában alatt **globális rendszergazdák által kezelhető az Azure-előfizetések**válassza **Igen**.

![Képernyőfelvétel: a Tulajdonságok oldala, amelyen Igen be-vagy kikapcsolása](media/azure-pim-resource-rbac/aad_properties_save.png)

Fiókja most már automatikusan hozzáadódik a felhasználói hozzáférés rendszergazdájának szerepköre a bérlő minden előfizetés erőforráshoz.

## <a name="browse-to-azure-ad-pim"></a>Tallózással keresse meg az Azure AD PIM-ben

 Itt lépjen az Azure AD Privileged Identity Management (PIM). A **kezelés**válassza **Azure-erőforrások**.

![Képernyőkép a PIM, az Azure-erőforrások vannak kiemelve](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>Kezelheti és erőforrások észlelése

Ha a szervezet védelme érdekében a rendszergazdák Azure AD-ben már az Azure AD PIM-ben, az előfizetések listáját láthatja a panel betöltődése.

![Képernyőkép a PIM, a panelen látható az előfizetések listája](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> Ha nem látja, akkor olyan erőforrások, ellenőrizze, hogy:
>- A globális rendszergazdai szerepköre nem járt le. 
>- A szervezet Azure-előfizetéssel rendelkezik.

![Képernyőkép a PIM, az üres erőforrások listája](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>Hozzárendelések konfigurálása

Válasszon ki egy előfizetést a listából, és rendelje hozzá a saját magát (vagy egy csoportot, akik tagjai), egy jogosult az erőforrás tulajdonosa. 
[További információk a szerepkörök hozzárendelése](pim-resource-roles-assign-roles.md).

Ismételje meg ezt a folyamatot az egyes erőforrások, mielőtt folytatná a következő lépéssel.

## <a name="clean-up-standing-access"></a>Állandó hozzáférés törlése

Most, hogy a szervezet rendelkezik az a fontos előfizetések jogosult hozzárendelésekkel, távolíthatja el állandó hozzáféréssel letiltja a beállítást a címtár tulajdonságait.

![Képernyőkép a Tulajdonságok oldala, amelyen nem be-vagy kikapcsolása](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>További lépések

- [Fedezze fel a PIM kezelése Azure-erőforrások](pim-resource-roles-discover-resources.md)
- [A PIM Azure szerepkör-beállítások konfigurálása](pim-resource-roles-configure-role-settings.md)
