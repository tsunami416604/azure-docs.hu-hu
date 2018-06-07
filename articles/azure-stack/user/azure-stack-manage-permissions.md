---
title: Kezeli az engedélyeket a verem Azure-ban felhasználónként erőforrásokhoz |} Microsoft Docs
description: Szolgáltatás-rendszergazdaként vagy bérlői megtudhatja, hogyan kezeli az RBAC-engedélyeket.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 026c686b1d5654aa50dd63b9addd619dd5322da0
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808145"
---
# <a name="manage-access-to-resources-with-azure-stack-role-based-access-control"></a>Hozzáférés-vezérléssel Azure Stack Role-Based erőforrásokhoz való hozzáférés kezelése

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Azure verem támogatja a szerepköralapú hozzáférés-vezérlés (RBAC) azonos [kezelési biztonsági modell](https://docs.microsoft.com/azure/role-based-access-control/overview) , amely a Microsoft Azure használja. Az RBAC segítségével kezelheti a felhasználó, csoport vagy előfizetések, erőforrások és szolgáltatások alkalmazás eléréséhez.

## <a name="basics-of-access-management"></a>Kezelési alapjai

Szerepköralapú hozzáférés-vezérlés lehetővé részletes hozzáférés-vezérlést használó megvédeni környezetét. Felhasználók megadhatja a pontos által egy adott hatókörben RBAC szerepkör hozzárendelése szükséges engedélyeket. A szerepkör-hozzárendelés hatóköre lehet előfizetés, egy erőforráscsoport vagy egy erőforrást. Olvassa el a [szerepköralapú hozzáférés-vezérlés az Azure portálon](https://docs.microsoft.com/azure/role-based-access-control/overview) cikkel, hogy hozzáférés-kezeléssel kapcsolatos további részletes információk.

### <a name="built-in-roles"></a>Beépített szerepkörök

Azure verem rendelkezik, amely az összes erőforrástípus alkalmazható három alapvető szerepkörök:

* **Tulajdonos** mindent felügyelhetnek, beleértve az erőforrásokhoz való hozzáférést.
* **A közreműködői** erőforrásokhoz való hozzáférés kivételével mindent felügyelhetnek.
* **Olvasó** mindent megtekinthetnek, de nem módosíthatja.

### <a name="resource-hierarchy-and-inheritance"></a>Erőforrás-hierarchiát és öröklés

Azure verem rendelkezik a következő erőforrás-hierarchiában:

* Minden előfizetés tartozik egy címtárban.
* Egy előfizetés tartozik minden erőforráscsoportban.
* Az egyes erőforrások egy erőforráscsoporthoz tartozik.

A gyermekhatókör örökölt, hogy a szülő hatókörben számára biztosítson hozzáférést. Példa:

* Az olvasó szerepkört rendelni az előfizetési hatókört, az Azure Active Directory-csoportnak. A csoport tagjai megtekintheti minden erőforrás csoport- és az előfizetés.
* A közreműködői szerepkör hozzárendelése az erőforrás-csoport hatóköre az alkalmazást. Az alkalmazás kezelhetik az erőforrásokat bármilyen típusú erőforráscsoport, de nem egyéb erőforráscsoportok az előfizetést.

### <a name="assigning-roles"></a>szerepkörök hozzárendelése

Több szerepkör hozzárendelése egy felhasználóhoz, és minden egyes szerepkör társítható egy másik hatókört. Példa:

* Előfizetés-1 TestUser-A az olvasó szerepkört rendelni.
* A tulajdonosi TestUser-A szerepkör hozzárendelése TestVM-1.

Az Azure [szerepkör-hozzárendelések](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) cikk megtekintése, hozzárendelése és szerepkörök törlése kapcsolatos részletes információkat tartalmazza.

### <a name="resource-hierarchy-and-inheritance"></a>Erőforrás-hierarchiát és öröklés

Azure verem rendelkezik a következő erőforrás-hierarchiában:

* Minden előfizetés tartozik egy címtárban.
* Egy előfizetés tartozik minden erőforráscsoportban.
* Az egyes erőforrások egy erőforráscsoporthoz tartozik.

A gyermekhatókör örökölt, hogy a szülő hatókörben számára biztosítson hozzáférést. Példa:

* Rendel a **olvasó** szerepkört az előfizetés hatókörben az Azure Active Directory-csoportnak. A csoport tagjai megtekintheti minden erőforrás csoport- és az előfizetés.
* Rendel a **közreműködő** szerepkör erőforrás csoport hatókörben egy alkalmazáshoz. Az alkalmazás kezelhetik az erőforrásokat bármilyen típusú erőforráscsoport, de nem egyéb erőforráscsoportok az előfizetést.

### <a name="assigning-roles"></a>szerepkörök hozzárendelése

Több szerepkör hozzárendelése egy felhasználóhoz, és minden egyes szerepkör társítható egy másik hatókört. Példa:

* Előfizetés-1 TestUser-A az olvasó szerepkört rendelni.
* A tulajdonosi TestUser-A szerepkör hozzárendelése TestVM-1.

Az Azure [szerepkör-hozzárendelések](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) cikk megtekintése, hozzárendelése és szerepkörök törlése kapcsolatos részletes információkat tartalmazza.

## <a name="set-access-permissions-for-a-user"></a>A felhasználó hozzáférési engedélyeinek beállítása

Az alábbi lépéseket egy felhasználó engedélyeinek konfigurálását ismertetik.

1. Olyan fiókkal jelentkezzen be, amely a kezelni kívánt erőforrás tulajdonos jogosult.
2. A bal oldali navigációs panelen válassza az **Erőforráscsoportok** lehetőséget.
3. Válassza ki az engedélyek beállítása a kívánt erőforráscsoport nevét.
4. Az erőforrás csoport navigációs ablaktábláján válassza **hozzáférés-vezérlés (IAM)**. A **hozzáférés-vezérlés** nézet az erőforráscsoport hozzáféréssel rendelkező elemeket sorolja fel. Az eredmények szűréséhez, és használja a menüsávon hozzáadása vagy eltávolítása.
5. Az a **hozzáférés-vezérlés** menü megnyitásához, válassza a **+ Hozzáadás**.
6. A **engedélyek hozzáadása**:

   * Válassza ki a hozzárendelni kívánt szerepkört a **szerepkör** legördülő listából.
   * Válassza ki a hozzárendelni kívánt erőforrást a **való hozzáférés hozzárendelése** legördülő listából.
   * Válassza ki azt a felhasználót, csoportot vagy alkalmazást a címtárában, amely számára hozzáférést kíván biztosítani. A címtárban rákereshet megjelenítendő nevekre, e-mail-címekre és objektumazonosítókra.

7. Kattintson a **Mentés** gombra.

## <a name="next-steps"></a>További lépések

[Egyszerű szolgáltatások létrehozása](azure-stack-create-service-principals.md)