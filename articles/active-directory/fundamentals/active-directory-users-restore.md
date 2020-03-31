---
title: A legutóbb törölt felhasználó visszaállítása vagy végleges eltávolítása – Azure AD
description: Visszaállítható felhasználók megtekintése, törölt felhasználó visszaállítása vagy véglegesen törölheti a felhasználót az Azure Active Directory használatával.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 573269da1ca6b9ee09b493f4e758e78121d6c2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422866"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Nemrég törölt felhasználó visszaállítása vagy eltávolítása az Azure Active Directory használatával
A felhasználó törlése után a fiók 30 napig felfüggesztett állapotban marad. A 30 napos időszak alatt a felhasználói fiók az összes tulajdonságával együtt visszaállítható. A 30 napos időszak elteltével a felhasználó automatikusan és véglegesen törlődik.

Megtekintheti a helyreállítható felhasználókat, visszaállíthat egy törölt felhasználót, vagy véglegesen törölhet egy felhasználót az Azure Active Directory (Azure AD) használatával az Azure Portalon.

>[!Important]
>Sem Ön, sem a Microsoft ügyfélszolgálata nem állíthatja vissza a véglegesen törölt felhasználót.

## <a name="required-permissions"></a>Szükséges engedélyek
A felhasználók visszaállításához és végleges törléséhez az alábbi szerepkörök valamelyikével kell rendelkeznie.

- Globális rendszergazda

- Partner tier1 támogatás

- Partner tier2 támogatás

- Rendszergazda

## <a name="view-your-restorable-users"></a>A helyreállítható felhasználók megtekintése
Láthatja az összes olyan felhasználót, amelyet kevesebb mint 30 nappal ezelőtt töröltek. Ezek a felhasználók visszaállíthatók.

### <a name="to-view-your-restorable-users"></a>A helyreállítható felhasználók megtekintése
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) a szervezet globális rendszergazdai fiókjával.

2. Válassza az **Azure Active Directory**( **Felhasználók**) lehetőséget, majd a **Törölt felhasználók**lehetőséget.

    Tekintse át a visszaállítható felhasználók listáját.

    ![Felhasználók – Törölt felhasználók lap, visszaállítható felhasználókkal](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Nemrég törölt felhasználó visszaállítása

Ha egy felhasználói fiókot törölnek a szervezetből, a fiók felfüggesztett állapotban van, és az összes kapcsolódó szervezeti információ megmarad. Amikor visszaállít egy felhasználót, a szervezet adatai is visszaállnak.

> [!Note]
> A felhasználó visszaállítása után a törlés időpontjában a felhasználóhoz rendelt licencek is visszaállnak, még akkor is, ha a licencekhez nem áll rendelkezésre hely. Ha ezután több licencet fogyaszt, mint amennyit megvásárolt, előfordulhat, hogy a szervezet átmenetileg nem felel meg a licenchasználatnak.

### <a name="to-restore-a-user"></a>Felhasználó visszaállítása
1. A **Felhasználók – Törölt felhasználók** lapon keresse meg és válassza ki az elérhető felhasználók egyikét. Például _Mary Parker_.

2. Válassza **a Felhasználó visszaállítása**lehetőséget.

    ![Felhasználók – Törölt felhasználók lap, kiemelt Felhasználói beállítások visszaállítása](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Felhasználó végleges törlése
A felhasználó véglegesen törölhető a szervezetből anélkül, hogy megvárna 30 napot az automatikus törlésre. A véglegesen törölt felhasználókat ön, egy másik rendszergazda és a Microsoft ügyfélszolgálata sem állíthatja vissza.

>[!Note]
>Ha véletlenül véglegesen töröl egy felhasználót, új felhasználót kell létrehoznia, és manuálisan kell megadnia az összes korábbi információt. Az új felhasználók létrehozásáról a [Felhasználók hozzáadása és törlése](add-users-azure-active-directory.md)című témakörben talál további információt.

### <a name="to-permanently-delete-a-user"></a>Felhasználó végleges törlése

1. A **Felhasználók – Törölt felhasználók** lapon keresse meg és válassza ki az elérhető felhasználók egyikét. _Például, Rae Huff_.

2. Válassza **a Törlés véglegesen**lehetőséget.

    ![Felhasználók – Törölt felhasználók lap, kiemelt Felhasználói beállítások visszaállítása](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>További lépések
Miután visszaállította vagy törölte a felhasználókat, a következő alapvető folyamatokat hajthatja végre:

- [Felhasználók hozzáadása vagy törlése](add-users-azure-active-directory.md)

- [Szerepkörök hozzárendelése felhasználókhoz](active-directory-users-assign-role-azure-portal.md)

- [Profiladatok hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)

- [Vendégfelhasználók hozzáadása másik szervezetből](../b2b/what-is-b2b.md)

Az egyéb elérhető felhasználókezelési feladatokról az [Azure AD felhasználói felügyeleti dokumentációja](../users-groups-roles/index.yml)tartalmaz további információt.
