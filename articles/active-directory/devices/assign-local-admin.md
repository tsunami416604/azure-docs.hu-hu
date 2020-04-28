---
title: Helyi rendszergazdák kezelése az Azure AD-hez csatlakoztatott eszközökön
description: Ismerje meg, hogyan rendelhet Azure-szerepköröket egy Windows-eszköz helyi rendszergazdák csoportjához.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc1812d955590ec0c7372e1311c9d69f93b9957c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80128883"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>A helyi Rendszergazdák csoport kezelése az Azure AD-hez csatlakoztatott eszközökön

Windows-eszközök kezeléséhez a helyi Rendszergazdák csoport tagjának kell lennie. A Azure Active Directory (Azure AD) csatlakoztatási folyamatának részeként az Azure AD frissíti a csoport tagságát az eszközön. A tagsági frissítést testreszabhatja az üzleti igények kielégítése érdekében. A tagság frissítése például hasznos, ha engedélyezni szeretné, hogy az ügyfélszolgálat munkatársai olyan feladatokat végezzenek el, amelyeken rendszergazdai jogosultságok szükségesek az eszközön.

Ez a cikk azt ismerteti, hogyan működik a tagsági frissítés, és hogyan szabható testre az Azure AD JOIN szolgáltatásban. A cikk tartalma nem vonatkozik a **hibrid** Azure ad-csatlakozásra.

## <a name="how-it-works"></a>Működés

Ha Azure ad-csatlakozással csatlakoztat egy Windows-eszközt az Azure AD-hez, az Azure AD a következő biztonsági alapelveket adja hozzá a helyi Rendszergazdák csoporthoz az eszközön:

- Az Azure AD globális rendszergazdai szerepköre
- Az Azure AD-eszköz rendszergazdai szerepköre 
- Az Azure AD Joint végző felhasználó   

Az Azure AD-szerepkörök a helyi Rendszergazdák csoporthoz való hozzáadásával frissítheti azokat a felhasználókat, akik az Azure AD-ben bármikor kezelhetik az eszközöket anélkül, hogy bármit módosítani kellene az eszközön. Jelenleg nem rendelhet hozzá csoportokat rendszergazdai szerepkörhöz.
Az Azure AD az Azure AD-eszköz rendszergazdai szerepkörét is hozzáadja a helyi rendszergazdák csoportjához a legalacsonyabb jogosultsági szint (PoLP) elvének támogatásához. A globális rendszergazdák mellett engedélyezheti azon felhasználók számára is, akik *csak* az eszköz rendszergazdai szerepkörét rendelték hozzá az eszközök kezeléséhez. 

## <a name="manage-the-global-administrators-role"></a>A globális rendszergazdák szerepkör kezelése

A globális rendszergazdai szerepkör tagságának megtekintéséhez és frissítéséhez tekintse meg a következőt:

- [Rendszergazdai szerepkör összes tagjának megtekintése Azure Active Directory](../users-groups-roles/directory-manage-roles-portal.md)
- [Felhasználó hozzárendelése rendszergazdai szerepkörökhöz az Azure Active Directory-ban](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Az eszköz rendszergazdai szerepkörének kezelése 

A Azure Portal az **eszközök** lapon kezelheti az eszköz rendszergazdai szerepkörét. Az **eszközök** lap megnyitása:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként.
1. Keresse meg és válassza ki az *Azure Active Directoryt*.
1. A **kezelés** szakaszban kattintson az **eszközök**elemre.
1. Az **eszközök** lapon kattintson az **eszközbeállítások**elemre.

Az eszköz rendszergazdai szerepkörének módosításához **további helyi rendszergazdákat kell konfigurálnia az Azure ad-hez csatlakoztatott eszközökön**.  

![További helyi rendszergazdák](./media/assign-local-admin/10.png)

>[!NOTE]
> Ehhez a beállításhoz prémium szintű Azure AD bérlő szükséges. 

Az eszközök rendszergazdái az összes Azure AD-hez csatlakoztatott eszközhöz vannak rendelve. Az eszközök rendszergazdái nem állíthatók be egy adott eszközre. Az eszköz rendszergazdai szerepkörének frissítése nem feltétlenül jelent azonnali hatást az érintett felhasználókra. Azokon az eszközökön, amelyeken a felhasználó már be van jelentkezve, a jogosultsági frissítés akkor kerül sor, ha az alábbi műveletek *is* megtörténnek:

- 4 óra telt el az Azure AD számára, hogy új elsődleges frissítési jogkivonatot adjon ki a megfelelő jogosultságokkal. 
- A felhasználó kijelentkezik, és visszajelentkezik, nem zárolja vagy oldja fel a profil frissítését.

## <a name="manage-regular-users"></a>Normál felhasználók kezelése

Alapértelmezés szerint az Azure AD hozzáadja az Azure AD-csatlakozást végző felhasználót az eszközön található rendszergazdai csoporthoz. Ha meg szeretné akadályozni, hogy a normál felhasználók helyi rendszergazdák legyenek, a következő lehetőségek közül választhat:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) – a Windows Autopilot lehetővé teszi, hogy megakadályozza, hogy az összekapcsolást végző elsődleges felhasználó helyi rendszergazda legyen. Ezt az [Autopilot-profil létrehozásával](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile)végezheti el.
- [Tömeges beléptetés](/intune/windows-bulk-enroll) – egy olyan Azure ad-csatlakozás, amely egy tömeges beléptetés kontextusában történik, egy automatikusan létrehozott felhasználó kontextusában történik. Az eszköz csatlakoztatása után bejelentkezett felhasználók nem lesznek hozzáadva a rendszergazdák csoporthoz.   

## <a name="manually-elevate-a-user-on-a-device"></a>Felhasználó manuális megemelése egy eszközön 

Az Azure AD JOIN folyamatán kívül manuálisan is beállíthatja, hogy egy normál felhasználó helyi rendszergazda legyen egy adott eszközön. Ehhez a lépéshez már a helyi Rendszergazdák csoport tagjának kell lennie. 

A **Windows 10 1709** kiadástól kezdve ezt a feladatot elvégezheti a **Beállítások – > fiókok – > más felhasználók**. Válassza a **munkahelyi vagy iskolai felhasználó hozzáadása**lehetőséget, írja be a felhasználó egyszerű felhasználónevét a **felhasználói fiók** területen, és válassza a *rendszergazda* elemet a **Fiók típusa** területen.  
 
Emellett a parancssor használatával is hozzáadhat felhasználókat:

- Ha a bérlő felhasználóit szinkronizálják a helyszíni Active Directory, használja `net localgroup administrators /add "Contoso\username"`a következőt:.
- Ha a bérlői felhasználók az Azure AD-ban jönnek létre, használja a`net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Megfontolandó szempontok 

Nem rendelhet hozzá csoportokat az eszköz rendszergazdai szerepköréhez, csak az egyes felhasználók engedélyezettek.

Az eszközök rendszergazdái az összes Azure AD-hez csatlakoztatott eszközhöz vannak rendelve. Nem lehetnek hatókörük egy adott eszközre.

Ha eltávolítja a felhasználókat az eszköz rendszergazdai szerepkörből, akkor továbbra is a helyi rendszergazdai jogosultsággal rendelkezik az eszközön, amíg be van jelentkezve. A rendszer visszavonja a jogosultságot a következő bejelentkezéskor, vagy 4 óra elteltével, amikor új elsődleges frissítési jogkivonatot állít ki.

## <a name="next-steps"></a>További lépések

- További információk az eszközök Azure Portalon végzett felügyeletéről: [Eszközfelügyelet az Azure Portalon](device-management-azure-portal.md).
- Ha többet szeretne megtudni az eszközökön alapuló feltételes hozzáférésről, tekintse meg [Azure Active Directory eszközön alapuló feltételes hozzáférési szabályzatok konfigurálása](../conditional-access/require-managed-devices.md)című témakört.
