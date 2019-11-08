---
title: Felhasználók hozzáadása vagy törlése – Azure Active Directory | Microsoft Docs
description: Útmutatás új felhasználók hozzáadásához vagy meglévő felhasználók törléséhez Azure Active Directory használatával.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e98b09d8b07c625613e3be149e64ac8f06adc089
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73805549"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Felhasználók hozzáadása vagy törlése Azure Active Directory használatával
Vegyen fel új felhasználókat, vagy törölje a meglévő felhasználókat a Azure Active Directory (Azure AD) szervezetből. Felhasználók hozzáadásához vagy törléséhez felhasználói rendszergazdának vagy globális rendszergazdának kell lennie. 

## <a name="add-a-new-user"></a>Új felhasználó hozzáadása
Létrehozhat egy új felhasználót a Azure Active Directory portál használatával.

### <a name="to-add-a-new-user"></a>Új felhasználó hozzáadása
1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) felhasználói rendszergazdaként a szervezet számára.

2. Válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd az **új felhasználó**lehetőséget.

    ![Felhasználók – minden felhasználó lap új felhasználóval kiemelve](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. A **Felhasználó** lapon adja meg a kötelező adatokat.

    ![Új felhasználó, felhasználói oldal hozzáadása a felhasználói adatokhoz](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **Név (kötelező).** Az új felhasználó vezetékneve és vezetékneve. Például: Mary Parker.

   - **Felhasználónév (kötelező).** Az új felhasználó felhasználóneve. Például: mary@contoso.com.
    
       A Felhasználónév tartomány részének a kezdeti alapértelmezett tartománynevet kell használnia, <_yourdomainname_>. onmicrosoft. com vagy egy egyéni tartománynevet, például contoso.com. Az Egyéni tartománynév létrehozásával kapcsolatos további információkért lásd: [Egyéni tartománynév hozzáadása a Azure Active Directoryhoz](add-custom-domain.md).

   - **Profil.** Igény szerint további információkat is hozzáadhat a felhasználóval kapcsolatban. Később is hozzáadhat felhasználói adatokat. A felhasználói adatok hozzáadásával kapcsolatos további információkért lásd: [felhasználói profil adatainak hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md).

   - **Csoportok.** Igény szerint hozzáadhatja a felhasználót egy vagy több meglévő csoporthoz. A felhasználót később is hozzáadhatja a csoportokhoz. A felhasználók csoportokba való felvételével kapcsolatos további információkért lásd: [alapszintű csoport létrehozása és Tagok hozzáadása](active-directory-groups-create-azure-portal.md).

   - **Címtárbeli szerepkör.** Igény szerint hozzáadhatja a felhasználót egy Azure AD-rendszergazdai szerepkörhöz. A felhasználót hozzárendelheti globális rendszergazdaként, vagy egy vagy több korlátozott rendszergazdai szerepkörrel az Azure AD-ben. A szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [szerepkörök társítása a felhasználókhoz](active-directory-users-assign-role-azure-portal.md).

4. Másolja a **jelszó** mezőben megadott automatikusan generált jelszót. Ezt a jelszót kell megadnia a felhasználónak a kezdeti bejelentkezési folyamathoz.

5. Kattintson a **Létrehozás** gombra.

    A rendszer létrehozza és hozzáadja a felhasználót az Azure AD-bérlőhöz.

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Új felhasználó hozzáadása hibrid környezetben
Ha Azure Active Directory (felhő) és a Windows Server Active Directory (helyszíni) környezettel rendelkezik, új felhasználókat is hozzáadhat a meglévő felhasználói fiókadatok szinkronizálásával. További információ a hibrid környezetekről és a felhasználókról: [a helyszíni címtárak integrálása Azure Active Directorysal](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Felhasználó törlése
Meglévő felhasználót a Azure Active Directory portál használatával törölhet.

### <a name="to-delete-a-user"></a>Felhasználó törlése
1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) a szervezet felhasználói rendszergazdai fiókjának használatával.

2. Válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**lehetőséget, majd keresse meg és válassza ki azt a felhasználót, akit törölni szeretne az Azure ad-bérlőből. Például: _Mary Parker_.

3. Válassza a **felhasználó törlése**lehetőséget.

    ![Felhasználók – minden felhasználó lap Kiemelt felhasználóval](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    A felhasználó törölve lett, és már nem jelenik meg a **felhasználók – minden felhasználó** lapon. A felhasználó a **törölt felhasználók** lapon a következő 30 napra megtekinthető, és ebben az időszakban visszaállítható. A felhasználók visszaállításával kapcsolatos további információkért lásd: [a nemrég törölt felhasználók visszaállítása vagy végleges eltávolítása](active-directory-users-restore.md). Ha töröl egy felhasználót, a felhasználó által felhasznált összes licenc elérhetővé válik más felhasználók számára.

    >[!Note]
    >A Windows Server Active Directory segítségével frissítse az identitást, a kapcsolattartási adatokat vagy a feladatokat azon felhasználók számára, akiknek a forrása a Windows Server Active Directory. A frissítés befejezése után meg kell várnia, hogy a következő szinkronizálási ciklus befejeződjön, mielőtt látni fogja a módosításokat.

## <a name="next-steps"></a>További lépések

A felhasználók hozzáadása után a következő alapvető folyamatokat végezheti el:

- [Profil adatainak hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)

- [Szerepkörök hozzárendelése felhasználókhoz](active-directory-users-assign-role-azure-portal.md)

- [Alapszintű csoport létrehozása és Tagok hozzáadása](active-directory-groups-create-azure-portal.md)

- [Dinamikus csoportok és felhasználók használata](../users-groups-roles/groups-create-rule.md)

Más felhasználói felügyeleti feladatokat is elvégezhet, például a [vendég felhasználóinak egy másik címtárból való hozzáadásával](../b2b/what-is-b2b.md) vagy [egy törölt felhasználó visszaállításával](active-directory-users-restore.md). További információ az egyéb elérhető műveletekről: [Azure Active Directory felhasználói kezelés dokumentációja](../users-groups-roles/index.yml).
