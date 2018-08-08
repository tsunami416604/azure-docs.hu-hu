---
title: A helyi Rendszergazdák csoport kezelése az Azure AD-hez csatlakoztatott eszközök |} A Microsoft Docs
description: Ismerje meg az Azure-szerepkörök hozzárendelése a helyi Rendszergazdák csoport egy Windows-eszköz.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: markvi
ms.reviewer: ravenn
ms.openlocfilehash: 2dfef8d0dbaf4d70559c966f1587764210d8b49c
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39598330"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>A helyi Rendszergazdák csoport kezelése az Azure AD-hez csatlakoztatott eszközök

Windows-eszköz felügyelete, a helyi Rendszergazdák csoport tagjának lennie kell. Az Azure Active Directory (Azure AD) való csatlakozás folyamat részeként az Azure AD frissíti egy eszközön a csoport tagságát. Testre szabhatja a csoporttagság-frissítést, az üzleti követelményeinek kielégítéséhez. A csoporttagság-frissítést akkor lehet hasznos, például a segélyszolgálat munkatársait a feladatok végrehajtására kellene rendszergazdai jogosultságokkal az eszközön engedélyezni kívánja.

Ez a cikk azt ismerteti, hogyan működik az a csoporttagság-frissítést, és hogyan lehet testre szabni.


## <a name="how-it-works"></a>Működés

Ha egy Windows-eszköz csatlakozik az Azure AD szolgáltatással az Azure AD-csatlakozás, Azure ad-ben az eszközön a helyi Rendszergazdák csoport ad hozzá a következő biztonsági alapelvek:

- Az Azure AD globális rendszergazdai szerepkör
- Az eszköz rendszergazdai szerepkörű Azure AD 
- A felhasználó elvégezhesse a az Azure AD-csatlakozás   

Az Azure AD-szerepkörök hozzáadása a helyi rendszergazdák csoportjához, frissítheti a felhasználók által kezelhető semmit az eszközön módosítása nélkül bármikor az Azure ad-ben egy eszközt. Csoportok jelenleg nem rendelhet egy rendszergazdai szerepkörhöz.
Az Azure AD rendszergazdai szerepkörű Azure AD eszköz is hozzáadja a helyi rendszergazdák csoportjának a elvét (PoLP) támogatásához. A globális rendszergazdák mellett is engedélyezhető, amelyeket felhasználók *csak* eszköz felügyelete, az eszköz rendszergazdai szerepkör. 


## <a name="manage-the-global-administrators-role"></a>A globális Rendszergazdák szerepkör kezelése

Megtekintheti, és frissíti a globális rendszergazdai szerepkör tagsága, lásd:

- [Az Azure Active Directory-Rendszergazdák szerepkör tagjai az összes megtekintése](../users-groups-roles/directory-manage-roles-portal.md)

- [Felhasználó hozzárendelése az Azure Active Directory rendszergazdai szerepkörök](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Az eszköz rendszergazdai szerepkör kezelése 

Az Azure Portalon kezelheti az eszközadminisztrátori szerepkörre a **eszközök** lapot. Megnyitásához a **eszközök** oldalon:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) egy globális rendszergazdai vagy eszköz rendszergazdaként.
2. A bal oldali navigációs sávon kattintson **Azure Active Directory**. 
3. Az a **kezelés** területén kattintson **eszközök**.
4. Az a **eszközök** kattintson **eszközbeállítások**.

Az eszköz rendszergazdai szerepkör módosítása, állítson be **további helyi rendszergazdák az Azure AD-hez csatlakoztatott eszközök**.  

![További helyi rendszergazdák](./media/assign-local-admin/10.png)

 
Eszközadminisztrátorok minden Azure AD-csatlakoztatott eszközök vannak hozzárendelve. Az eszközök meghatározott eszközadminisztrátorok nem körét. Az eszköz rendszergazdai szerepkör frissítése nem feltétlenül közvetlen hatással, az érintett felhasználók. Az eszközök esetén a felhasználó már bejelentkezett, a jogosultság frissítése történik:
     

- Amikor egy felhasználó bejelentkezik,.
- 4 óra elteltével új elsődleges jogkivonat frissítése jelenik meg. 




## <a name="manage-regular-users"></a>Normál felhasználók kezelése

Alapértelmezés szerint az Azure AD hozzáadja a felhasználó elvégezhesse a rendszergazda csoporthoz az Azure AD-csatlakozás az eszközön. Ha azt szeretné, hogy rendszeres felhasználók ne váljon a helyi rendszergazdák, a következő lehetőségek állnak rendelkezésére:

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot) – Windows Autopilot-beállítás megakadályozza, hogy az illesztés végrehajtása a helyi rendszergazdák váljon elsődleges felhasználója biztosít. Ez által elvégezhető [Autopilot-profil létrehozása](https://docs.microsoft.com/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
 
- [Csoportos regisztrálás](https://docs.microsoft.com/intune/windows-bulk-enroll) – egy Azure AD-csatlakozás a tömeges Regisztrálás a környezetben végrehajtott műveletek egy automatikusan létrehozott felhasználó környezetében történik. Bejelentkezés után az eszköz csatlakoztatva lett a felhasználók ne adják hozzá a Rendszergazdák csoportnak.   



## <a name="manually-elevate-a-user-on-a-device"></a>Manuálisan egy felhasználó jogosultságszintjének emeléséhez az eszközön 

Mellett az Azure AD join folyamat használja, manuálisan is szintre emelhet, az átlagos felhasználók egy adott eszköz helyi rendszergazdája lesz. Ez a lépés megköveteli, hogy a helyi Rendszergazdák csoport tagjának lennie. 

Kezdve a **Windows 10 1709-es** kiadásban hajthat végre ezt a feladatot **beállítások -> fiókok -> más felhasználók** kiválasztásával **adja hozzá a munkahelyi vagy iskolai felhasználó**.
 
Ezenkívül is hozzáadhat felhasználókat a parancssor használatával:

- Ha a bérlői felhasználó számára a helyszíni Active Directoryból szinkronizált, `net localgroup administrators /add “Contoso\username”`.

- Ha az Azure AD-bérlő felhasználóit jönnek létre, `net localgroup administrators /add “AzureAD\UserUpn”`


## <a name="considerations"></a>Megfontolandó szempontok 

Az eszköz rendszergazdai szerepkör nem rendelhet hozzá csoportokat, csak az egyes felhasználók engedélyezettek.

Eszközadminisztrátorok minden Azure AD-csatlakoztatás eszközök vannak hozzárendelve. Azok az eszközök meghatározott nem illeszthetők.

Felhasználók eltávolítása az eszközadminisztrátori szerepkörre, ha továbbra sem rendelkezik az eszközön a helyi rendszergazdai jogosultság mindaddig, amíg rá bejelentkezés. A jogosultság visszavonva során a következő bejelentkezést a, vagy amikor egy új elsődleges frissítési jogkivonat kiadott 4 óra elteltével.



## <a name="next-steps"></a>További lépések

- Áttekintheti az Azure Portalon eszközök felügyelete, lásd: [az Eszközfelügyelet az Azure portal használatával](device-management-azure-portal.md)

- Eszközalapú feltételes hozzáférési kapcsolatos további információkért lásd: [Azure Active Directory eszközalapú feltételes hozzáférési szabályzatok konfigurálhatók](../conditional-access/require-managed-devices.md).


