---
title: "Állítsa be az Azure Active Directoryhoz csatlakoztatott eszközök |} Microsoft Docs"
description: "Ismerje meg, hogyan állíthat be Azure Active Directoryhoz csatlakoztatott eszközök."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: ac6c9224925e5bfd3cb056c6c8d9cf2a96b0eb2b
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="set-up-azure-active-directory-joined-devices"></a>Állítsa be az Azure Active Directoryhoz csatlakoztatott eszközök

Az eszköz kezelése az Azure Active Directory (Azure AD) biztosíthatja, hogy a felhasználók a biztonsági és megfelelőségi szabványoknak megfelelő eszközökről érnek el az erőforrásokat. További információkért lásd: [bemutatása az Azure Active Directoryban kezelés](device-management-introduction.md).

Ha azt szeretné, az Azure AD felügyelete alá vonandó munkahelyi Windows 10-es eszközök, akkor ez úgy konfigurálja az Azure AD csatlakoztatott eszközök érhető el. Ez a témakör a kapcsolódó lépések. 


## <a name="prerequisites"></a>Előfeltételek

Egy Windows 10 rendszerű eszköz csatlakozni az eszközregisztrációs szolgáltatást kell állítani ahhoz, hogy regisztrálják eszközeiket. Mellett eszközök csatlakoztatása az Azure AD-bérlőben engedéllyel, a beállított maximális regisztrált kevesebb eszközök kell rendelkeznie. További információkért lásd: [eszközbeállítások konfigurálása](device-management-azure-portal.md#configure-device-settings).



## <a name="what-you-should-know"></a>Tudnivalók


- Windows Azure AD-ben az eszköz csatlakozik a munkahely címtárában.

- Szükséges lehet a multi-factor authentication ellenőrző végrehajtania. Ez a probléma a rendszergazda által konfigurálható.

- Az Azure AD ellenőrzi, hogy az eszköz mobileszköz-kezelési beléptetéséhez szükséges-e, és regisztrálja azt, ha van ilyen.

- Ha egy felügyelt felhasználók Windows végigvezeti Önt az asztalhoz az automatikus bejelentkezés.

- Ha egy összevont felhasználó, akkor jelentkezzen be a hitelesítő adatok használatával.


## <a name="joining-a-device"></a>Eszköz csatlakoztatása

A szakasz ismerteti a lépéseket a Windows 10 rendszerű eszköz csatlakoztatása az Azure AD. Ha sikeresen csatlakozott az eszközt az Azure AD a **hozzáférés munkahelyi vagy iskolai** párbeszédpanel jelzi ezt egy **csatlakoztatott \<az Azure AD\>**  bejegyzés.

![Csatlakoztatva](./media/device-management-azuread-joined-devices-setup/13.png)


**A Windows 10-eszköz csatlakozni:**

1. Az a **Start** menüben kattintson a **beállítások**.

    ![Beállítások](./media/device-management-azuread-joined-devices-setup/01.png)

2. Kattintson a **fiókok**.

    ![Fiókok](./media/device-management-azuread-joined-devices-setup/02.png)


3. Kattintson a **hozzáférés munkahelyi vagy iskolai**.

    ![Hozzáférés a munkahelyi vagy iskolai](./media/device-management-azuread-joined-devices-setup/03.png)

4. Az a **hozzáférés munkahelyi vagy iskolai** párbeszédpanel, kattintson a **Connect**.

    ![Kapcsolódás](./media/device-management-azuread-joined-devices-setup/04.png)


5. Az a **munkahelyi vagy iskolai fiók beállítása** párbeszédpanel, kattintson a **az eszköz csatlakoztatása az Azure Active Directory**.

    ![Kapcsolódás](./media/device-management-azuread-joined-devices-setup/08.png)


6. Az a **jelentkezzen be** párbeszédpanelen adja meg a fiók nevét (például someone@example.com), és kattintson a **következő**.

    ![Jelentkezzen be](./media/device-management-azuread-joined-devices-setup/10.png)


6. Az a **jelszó megadni** párbeszédpanelen írja be a jelszót, és kattintson **bejelentkezés**.

    ![Jelszó megadása](./media/device-management-azuread-joined-devices-setup/05.png)


7. Az a **győződjön meg arról, hogy ez a szervezet** párbeszédpanel, kattintson a **csatlakozás**.

    ![Győződjön meg arról, hogy ez a szervezet](./media/device-management-azuread-joined-devices-setup/11.png)


8. Az a **minden beállítás kész** párbeszédpanel, kattintson a **végzett**.

    ![Minden beállítás kész](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>Ellenőrzés

Győződjön meg arról, hogy egy eszköz csatlakozik-e az Azure AD, tekintse át a **hozzáférés munkahelyi vagy iskolai** párbeszédpanel az eszközön.

![Csatlakoztatva](./media/device-management-azuread-joined-devices-setup/13.png)

Alternatív megoldásként futtathatja a következő parancsot:`dsregcmd /status`  
Sikeresen csatlakoztatva eszközön **AzureAdJoined** van **Igen**.

![Csatlakoztatva](./media/device-management-azuread-joined-devices-setup/14.png)

Emellett áttekintheti az Azure AD portálon Eszközbeállítások.

![Csatlakoztatva](./media/device-management-azuread-joined-devices-setup/15.png)

További információkért lásd: [keresse meg az eszközök](device-management-azure-portal.md#locate-devices).


## <a name="next-steps"></a>További lépések

További információkért lásd: 

- A [eszköz kezelése az Azure Active Directoryban bemutatása](device-management-introduction.md)
- [Az Azure portál használatával eszközök kezelése](device-management-azure-portal.md)
- 



