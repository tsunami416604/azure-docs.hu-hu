---
title: Állítsa vissza vagy véglegesen eltávolítja egy nemrég törölt felhasználó – az Azure Active Directory |} A Microsoft Docs
description: Hogyan visszaállítható a felhasználók megtekintéséhez, törölt felhasználói visszaállítása vagy az Azure Active Directory felhasználók végleges törlésére.
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
ms.openlocfilehash: 9864634020ad115a48bdb70a6736733919d0f0d3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561673"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Állítsa vissza vagy az Azure Active Directoryval nemrégiben törölt felhasználó eltávolítása
A felhasználó törlését követően a fiók maradt felfüggesztett állapotba 30 napig. A 30 napos időszak alatt a felhasználói fiók is lehet visszaállítani, együtt minden hozzá tartozó tulajdonságok. A 30 napos időszak letelte után a felhasználó automatikusan, és véglegesen, törlődik.

A visszaállítható felhasználók megtekintése, törölt felhasználói visszaállítása vagy véglegesen törli a felhasználó Azure Active Directory (Azure AD) használatával az Azure Portalon.

>[!Important]
>Sem az Ön, sem a Microsoft ügyfélszolgálata állíthatja vissza egy felhasználó véglegesen törölve.

## <a name="required-permissions"></a>Szükséges engedélyek
Felhasználók végleges törlése és visszaállítása a következő szerepkörök egyikét kell rendelkeznie.

- Globális rendszergazda

- 1\. szintű partnertámogatás

- 2\. szintű partnertámogatás

- Felhasználói adminisztrátor

## <a name="view-your-restorable-users"></a>A visszaállítható felhasználók megtekintése
Legalább 30 nappal ezelőtt törölt összes felhasználója láthatja. Ezek a felhasználók vissza tudja állítani.

### <a name="to-view-your-restorable-users"></a>A visszaállítható felhasználók megtekintése
1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) globális rendszergazdai fiókkal a szervezet számára.

2. Válassza ki **Azure Active Directory**válassza **felhasználók**, majd válassza ki **törölt felhasználók**.

    Tekintse át a kiválasztott felhasználók visszaállítása számára elérhető.

    ![Felhasználók – törölt felhasználók lapon, a felhasználók továbbra is visszaállíthatók](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Közelmúltban törölt felhasználó visszaállítása

Ha töröl egy felhasználói fiókot a szervezetből, a fiók felfüggesztett állapotba kerül, és az összes kapcsolódó szervezeti információ megőrződik. Egy felhasználó visszaállításakor a rendszer a szervezeti adatokat is visszaállítja.

> [!Note]
> A felhasználó visszaállítása után a rendszer a törléskor a felhasználóhoz rendelt licenceket is visszaállítja, még akkor is, ha nincsenek elérhető helyek a licencekhez. Ha ezt követően több licencet használ, mint amennyit megvásárolt, a szervezet átmenetileg nem felel meg a licencek használatának.

### <a name="to-restore-a-user"></a>A felhasználó visszaállítása
1. Az a **felhasználók – törölt felhasználók** lapon keresse meg és válassza ki az elérhető felhasználók egyikét. Ha például _Mary Parker_.

2. Válassza ki **visszaállítási felhasználói**.

    ![Felhasználók – törölt felhasználók lapon a visszaállítási felhasználói opció kiemelésével](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Felhasználó végleges törlése
Véglegesen törölheti a felhasználót a szervezetből anélkül, hogy az automatikus törlés 30 napját kellene várnia. Nem állítható vissza egy felhasználó véglegesen törölve, és Ön egy másik rendszergazda nem támogatja a Microsoft ügyfélszolgálat.

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

- [Vendég felhasználók hozzáadása egy másik szervezethez](../b2b/what-is-b2b.md)

További információ az [Azure ad](../users-groups-roles/index.yml)-beli felhasználói felügyeleti feladatokról.
