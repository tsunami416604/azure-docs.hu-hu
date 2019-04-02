---
title: Állítsa vissza vagy véglegesen eltávolítja egy nemrég törölt felhasználó – az Azure Active Directory |} A Microsoft Docs
description: Hogyan visszaállítható a felhasználók megtekintéséhez, törölt felhasználói visszaállítása vagy az Azure Active Directory felhasználók végleges törlésére.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d69bd931f2f8c72fd1e6fc79c16662ea367617d6
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802014"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Állítsa vissza vagy az Azure Active Directoryval nemrégiben törölt felhasználó eltávolítása
A felhasználó törlését követően a fiók maradt felfüggesztett állapotba 30 napig. A 30 napos időszak alatt a felhasználói fiók is lehet visszaállítani, együtt minden hozzá tartozó tulajdonságok. A 30 napos időszak letelte után a felhasználó automatikusan, és véglegesen, törlődik.

A visszaállítható felhasználók megtekintése, törölt felhasználói visszaállítása vagy véglegesen törli a felhasználó Azure Active Directory (Azure AD) használatával az Azure Portalon.

>[!Important]
>Sem az Ön, sem a Microsoft ügyfélszolgálata állíthatja vissza egy felhasználó véglegesen törölve.

## <a name="required-permissions"></a>Szükséges engedélyek
Felhasználók végleges törlése és visszaállítása a következő szerepkörök egyikét kell rendelkeznie.

- Globális rendszergazda

- 1. szintű partnertámogatás

- 2. szintű partnertámogatás

- Felhasználói adminisztrátor

## <a name="view-your-restorable-users"></a>A visszaállítható felhasználók megtekintése
Legalább 30 nappal ezelőtt törölt összes felhasználója láthatja. Ezek a felhasználók vissza tudja állítani.

### <a name="to-view-your-restorable-users"></a>A visszaállítható felhasználók megtekintése
1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) egy globális rendszergazdai fiók használatával a szervezet számára.

2. Válassza ki **Azure Active Directory**válassza **felhasználók**, majd válassza ki **törölt felhasználók**.

    Tekintse át a kiválasztott felhasználók visszaállítása számára elérhető.

    ![Felhasználók – törölt felhasználók lapon, a felhasználók továbbra is visszaállíthatók](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Közelmúltban törölt felhasználó visszaállítása

Egy felhasználói fiókot a szervezet a törölt felfüggesztett állapotban van a fiók, és minden kapcsolódó szervezet adatai megmaradnak. Felhasználó visszaállítása, ha a szervezet is vissza.

> [!Note]
> A felhasználó visszaállítása után a felhasználó törlésekor a hozzárendelt licenceket is vissza akkor is, ha nincsenek elérhető ezek nincsenek munkaállomások. Ha használ majd fel további licenceket vásárolt egynél több, a szervezet lehet ideiglenesen nem megfelelő a licenchasználatot.

### <a name="to-restore-a-user"></a>A felhasználó visszaállítása
1. Az a **felhasználók – törölt felhasználók** lapon keresse meg és válassza ki az elérhető felhasználók egyikét. Ha például _Mary Parker_.

2. Válassza ki **visszaállítási felhasználói**.

    ![Felhasználók – törölt felhasználók lapon a visszaállítási felhasználói opció kiemelésével](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Felhasználó végleges törlése
A felhasználó a szervezet a 30 nap automatikus törlésre jóváhagyású véglegesen törli. Nem állítható vissza egy felhasználó véglegesen törölve, és Ön egy másik rendszergazda nem támogatja a Microsoft ügyfélszolgálat.

>[!Note]
>Ha véglegesen törli egy felhasználó véletlenül, meg kell hozzon létre egy új felhasználót, és manuálisan adja meg az előző adatokat. Új felhasználó létrehozásával kapcsolatos további információkért lásd: [hozzáadása vagy törlése felhasználók](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>A felhasználó végleges törlése

1. Az a **felhasználók – törölt felhasználók** lapon keresse meg és válassza ki az elérhető felhasználók egyikét. Ha például _Rae Huff_.

2. Válassza ki **véglegesen törli a**.

    ![Felhasználók – törölt felhasználók lapon a visszaállítási felhasználói opció kiemelésével](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>További lépések
Miután visszaállította, vagy törli a felhasználók, az alábbi alapszintű folyamatok hajthatja végre:

- [Adja hozzá, vagy a felhasználók törlése](add-users-azure-active-directory.md)

- [Szerepkörök hozzárendelése felhasználókhoz](active-directory-users-assign-role-azure-portal.md)

- [Profil adatok hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)

- [Ha egy másik szervezet vendégfelhasználók hozzáadása](../b2b/what-is-b2b.md)

További információ az elérhető felhasználói ő dolgozik [az Azure AD felügyeleti dokumentáció](../users-groups-roles/index.yml).
