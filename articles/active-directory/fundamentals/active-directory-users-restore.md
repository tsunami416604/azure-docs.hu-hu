---
title: Állítsa vissza vagy véglegesen eltávolítja egy nemrég törölt felhasználó – az Azure Active Directory |} A Microsoft Docs
description: Hogyan visszaállítható a felhasználók megtekintéséhez, törölt felhasználói visszaállítása vagy az Azure Active Directory felhasználók végleges törlésére.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: 066c9189e0c3cea409a99217e0ab4b46002df1f9
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556781"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Állítsa vissza vagy az Azure Active Directoryval nemrégiben törölt felhasználó eltávolítása
A felhasználó törlését követően a fiók maradt felfüggesztett állapotba 30 napig. A 30 napos időszak alatt a felhasználói fiók is lehet visszaállítani, együtt minden hozzá tartozó tulajdonságok. A 30 napos időszak letelte után a felhasználó automatikusan, és véglegesen, törlődik.

A visszaállítható felhasználók megtekintése, törölt felhasználói visszaállítása vagy véglegesen törli a felhasználó Azure Active Directory (Azure AD) használatával az Azure Portalon.

>[!Important]
>Sem az Ön, sem a Microsoft ügyfélszolgálata állíthatja vissza egy felhasználó véglegesen törölve.

## <a name="required-permissions"></a>Szükséges engedélyek
Felhasználók végleges törlése és visszaállítása a következő szerepkörök egyikét kell rendelkeznie.

- Céges rendszergazda

- 1. szintű partnertámogatás

- 2. szintű partnertámogatás

- Felhasználóifiók-adminisztrátor

## <a name="view-your-restorable-users"></a>A visszaállítható felhasználók megtekintése
Legalább 30 nappal ezelőtt törölt összes felhasználója láthatja. Ezek a felhasználók vissza tudja állítani.

### <a name="to-view-your-restorable-users"></a>A visszaállítható felhasználók megtekintése
1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Válassza ki **Azure Active Directory**válassza **felhasználók**, majd válassza ki **törölt felhasználók**.

    Tekintse át a kiválasztott felhasználók visszaállítása számára elérhető.

    ![Felhasználók – törölt felhasználók lapon, a felhasználók továbbra is visszaállíthatók](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Közelmúltban törölt felhasználó visszaállítása
Egy felhasználói fiók fel van függesztve, amíg a rendszer megőrzi a kapcsolódó összes címtáradatokhoz. Felhasználó visszaállítása, ha a címtár is vissza.

### <a name="to-restore-a-user"></a>A felhasználó visszaállítása
1. Az a **felhasználók – törölt felhasználók** lapon keresse meg és válassza ki az elérhető felhasználók egyikét. Ha például _Mary Parker_.

2. Válassza ki **visszaállítási felhasználói**.

    ![Felhasználók – törölt felhasználók lapon a visszaállítási felhasználói opció kiemelésével](media/active-directory-users-restore/users-deleted-users-restore-user.png)

>[!NOTE]
>Korábban, amikor egy felhasználó lett eltávolítva a helyszíni szinkronizálási hatókör, és törli a felhőben, a fiók a DirSyncEnabled állapota hibás állított be "false"értékre. Ha, hogy a felhasználó ezt követően manuálisan lett visszaállítva a az Azure AD Recycle Bin, szerint a "Csak felhőalapú" fiók egy nem megfelelő állapotát. Ez most már megoldották a problémát, és a DirSyncEnabled állapot értéke mindig megőrzi "True" egy felhasználó eltávolításakor az adatszinkronizálás hatóköre, majd helyreállíthatóan törölt, és manuálisan helyreállított az Azure AD Recycle Bin.

## <a name="permanently-delete-a-user"></a>Felhasználó végleges törlése
Az automatikus törlésre 30 nap várakozás nélkül a címtárban lévő véglegesen törli a felhasználó. Nem állítható vissza egy felhasználó véglegesen törölve, és Ön egy másik rendszergazda nem támogatja a Microsoft ügyfélszolgálat.

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

- [Vendégfelhasználók hozzáadása másik címtárból](../b2b/what-is-b2b.md) 

További információ az elérhető felhasználói ő dolgozik [Azure Active Directory felhasználói felügyeleti dokumentáció](../users-groups-roles/index.yml).
