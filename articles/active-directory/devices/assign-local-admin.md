---
title: Helyi rendszergazdák kezelése az Azure AD-hez csatlakozó eszközökön
description: Megtudhatja, hogyan rendelhet Azure-szerepköröket a Windows-eszközök helyi rendszergazdák csoportjához.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128883"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>A helyi rendszergazdák csoport kezelése az Azure AD-hez csatlakozó eszközökön

A Windows-eszközök kezeléséhez a helyi rendszergazdák csoportjának tagjának kell lennie. Az Azure Active Directory (Azure AD) csatlakozási folyamat részeként az Azure AD frissíti a csoport tagságát egy eszközön. A tagsági frissítést az üzleti követelményeknek megfelelően testre szabhatja. A tagság frissítése például akkor hasznos, ha engedélyezni szeretné az ügyfélszolgálati munkatársaknak, hogy rendszergazdai jogokat igénylő feladatokat végezzenek az eszközön.

Ez a cikk bemutatja, hogyan működik a tagsági frissítés, és hogyan szabhatja testre az Azure AD-csatlakozás során. A cikk tartalma nem vonatkozik a **hibrid** Azure AD-csatlakozás.

## <a name="how-it-works"></a>Működés

Ha egy Windows-eszközt azure AD-s csatlakozással csatlakoztat az Azure AD-hez, az Azure AD a következő biztonsági alapelveket adja hozzá az eszköz helyi rendszergazdák csoportjához:

- Az Azure AD globális rendszergazdai szerepköre
- Az Azure AD eszközrendszergazdai szerepkör 
- Az Azure AD-csatlakozást végző felhasználó   

Az Azure AD-szerepkörök hozzáadásával a helyi rendszergazdák csoport, frissítheti a felhasználókat, amelyek kezelhetik az eszközt bármikor az Azure AD módosítása nélkül semmit az eszközön. Jelenleg nem rendelhet csoportokat rendszergazdai szerepkörhöz.
Az Azure AD is hozzáadja az Azure AD-eszköz rendszergazdai szerepkört a helyi rendszergazdák csoport a minimális jogosultság (PoLP) elvének támogatása érdekében. A globális rendszergazdákon kívül engedélyezheti az eszközrendszergazdai szerepkörrel *csak* hozzárendelt felhasználók számára is az eszköz kezelését. 

## <a name="manage-the-global-administrators-role"></a>A globális rendszergazdák szerepkör kezelése

A globális rendszergazdai szerepkör tagságának megtekintéséhez és frissítéséhez lásd:

- [Rendszergazdai szerepkör összes tagjának megtekintése az Azure Active Directoryban](../users-groups-roles/directory-manage-roles-portal.md)
- [Felhasználó hozzárendelése rendszergazdai szerepkörökhöz az Azure Active Directory-ban](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Az eszközrendszergazdai szerepkör kezelése 

Az Azure Portalon kezelheti az eszközrendszergazdai szerepkört az **Eszközök** lapon. Az **Eszközök** lap megnyitása:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) globális rendszergazdaként.
1. Keresse meg és válassza ki az *Azure Active Directoryt*.
1. A **Kezelés** csoportban kattintson az **Eszközök gombra.**
1. Az **Eszközök** lapon kattintson az **Eszközbeállítások gombra.**

Az eszközrendszergazdai szerepkör módosításához konfigurálja **a További helyi rendszergazdákkonfigurálását az Azure AD-hez csatlakozó eszközökön.**  

![További helyi rendszergazdák](./media/assign-local-admin/10.png)

>[!NOTE]
> Ehhez a beállításhoz egy Azure AD Premium-bérlő szükséges. 

Az eszközrendszergazdák az összes Azure AD-hez csatlakozott eszközhöz vannak hozzárendelve. Az eszközrendszergazdák nem hatóköre adott eszközcsoportra. Az eszközrendszergazdai szerepkör frissítése nem feltétlenül gyakorol azonnali hatást az érintett felhasználókra. Azokon az eszközökön, amelyeken a felhasználó már be van jelentkezve, a jogosultságfrissítése az alábbi műveletek *mindkét* művelete esetén történik:

- 4 óra telt el az Azure AD számára, hogy kiadja a megfelelő jogosultságokkal rendelkező új elsődleges frissítési jogkivonatot. 
- A felhasználó kijelentkezik, és bejelentkezik, nem zár / kinyit, hogy frissítse a profilját.

## <a name="manage-regular-users"></a>Rendszeres felhasználók kezelése

Alapértelmezés szerint az Azure AD hozzáadja az Azure AD-csatlakozást végző felhasználót az eszközön lévő rendszergazdai csoporthoz. Ha meg szeretné akadályozni, hogy a normál felhasználók helyi rendszergazdákká váljanak, a következő lehetőségek közül választhat:

- [A Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) – Windows Autopilot lehetőséget biztosít arra, hogy megakadályozza, hogy az elsődleges felhasználó helyi rendszergazdává váljon. Ezt [autopilot profil létrehozásával](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile)érheti el.
- [Tömeges regisztráció](/intune/windows-bulk-enroll) – Egy Azure AD-csatlakozás, amely egy tömeges regisztráció környezetében történik egy automatikusan létrehozott felhasználó környezetben történik. Az eszköz csatlakoztatása után bejelentkező felhasználók nem kerülnek be a Rendszergazdák csoportba.   

## <a name="manually-elevate-a-user-on-a-device"></a>Felhasználó manuális feleslege az eszközön 

Az Azure AD-illesztési folyamat használata mellett manuálisan is emelheti a rendszeres felhasználó lesz a helyi rendszergazda egy adott eszközön. Ehhez a lépéshez már a helyi rendszergazdák csoportjának tagjának kell lennie. 

A **Windows 10 1709** kiadástól kezdve ezt a feladatot a **Settings -> Accounts -> Other felhasználók**segítségével hajthatja végre. Válassza **a Munkahelyi vagy iskolai felhasználó hozzáadása**lehetőséget, írja be a felhasználó felhasználói felhasználónévcsoportját a Felhasználói **fiók** csoportban, és válassza *a Rendszergazda* lehetőséget a **Fióktípus** csoportban.  
 
Ezenkívül a parancssorhasználatával is hozzáadhat felhasználókat:

- Ha a bérlői felhasználók szinkronizálása a helyszíni `net localgroup administrators /add "Contoso\username"`Active Directoryból történik, használja a használatát.
- Ha a bérlői felhasználók az Azure AD-ben jönnek létre, használja a`net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Megfontolandó szempontok 

Nem rendelhet csoportokat az eszközrendszergazdai szerepkörhöz, csak az egyes felhasználók engedélyezettek.

Az eszközrendszergazdák az összes Azure AD-hez csatlakozott eszközhöz vannak hozzárendelve. Nem lehet hatókört egy adott eszközcsoportra.

Ha eltávolítja a felhasználókat az eszközrendszergazdai szerepkörből, akkor is rendelkeznek a helyi rendszergazdai jogosultsággal az eszközön, amíg be vannak jelentkezve. A jogosultság a következő bejelentkezés során, vagy 4 óra elteltével egy új elsődleges frissítési jogkivonat kiadása után visszavonják.

## <a name="next-steps"></a>További lépések

- További információk az eszközök Azure Portalon végzett felügyeletéről: [Eszközfelügyelet az Azure Portalon](device-management-azure-portal.md).
- Az eszközalapú feltételes hozzáférésről az [Azure Active Directory eszközalapú feltételes hozzáférési szabályzatok konfigurálása című témakörben](../conditional-access/require-managed-devices.md)olvashat bővebben.
