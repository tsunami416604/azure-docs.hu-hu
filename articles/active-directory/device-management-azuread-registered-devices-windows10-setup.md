---
title: "Azure Active Directory regisztrált eszközöket beállíthatja |} Microsoft Docs"
description: "Ismerje meg, hogyan állíthat be Azure Active Directory regisztrált eszközök."
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
ms.openlocfilehash: 2560e51d61506389e84288bf983b0ebcb5776ff2
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Azure Active Directory beállítása regisztrált Windows 10-eszközök

Az eszköz kezelése az Azure Active Directory (Azure AD) biztosíthatja, hogy a felhasználók a biztonsági és megfelelőségi szabványoknak megfelelő eszközökről érnek el az erőforrásokat. További részletekért lásd: [bemutatása az Azure Active Directoryban kezelés](device-management-introduction.md).

Ha engedélyezni szeretné a **kapcsolja a saját eszközök használata (BYOD)** esetben ez elvégezhető az Azure AD regisztrált eszközök konfigurálásával. Az Azure AD-eszközök regisztrálása az Azure AD konfigurálása a Windows 10, iOS, Android és macOS. Ez a témakör a kapcsolódó lépések Windows 10 rendszerű eszközökhöz. 


## <a name="before-you-begin"></a>Előkészületek

Windows 10-eszköz regisztrálása, az eszközregisztrációs szolgáltatást kell állítani ahhoz, hogy regisztrálják eszközeiket. Eszközök regisztrálása az Azure AD-bérlő jogosultsága mellett, kevesebb a beállított maximális regisztrált eszközök kell rendelkeznie. További részletekért lásd: [eszközbeállítások konfigurálása](device-management-azure-portal.md#configure-device-settings).

## <a name="what-you-should-know"></a>Tudnivalók

Eszközök regisztrálásakor figyelembe kell venni a következő:

- Windows regisztrálja az eszközt a munkahely címtárában az Azure AD

- Szükséges lehet a multi-factor authentication ellenőrző végrehajtania. Ez a probléma a rendszergazda által konfigurálható.

- Az Azure AD ellenőrzi, hogy az eszköz mobileszköz-kezelési beléptetéséhez szükséges-e, és regisztrálja azt, ha van ilyen.

- Ha egy felügyelt felhasználók Windows végigvezeti Önt az asztalhoz az automatikus bejelentkezés.

- Ha egy összevont felhasználó, akkor megnyílik a Windows bejelentkezési képernyő hitelesítő adatait.


## <a name="registering-a-device"></a>Eszközök regisztrálása

A szakasz ismerteti a lépéseket az Azure ad a Windows 10-eszköz regisztrálása. Sikeresen regisztrálta az eszközt az Azure AD, ha a **hozzáférés munkahelyi vagy iskolai** párbeszédpanel jelzi ezt egy **munkahelyi vagy iskolai fiókkal** bejegyzés.

![Regisztráljon](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


**A Windows 10-eszköz regisztrálása:**

1. Az a **Start** menüben kattintson a **beállítások**.

    ![Beállítások](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. Kattintson a **fiókok**.

    ![Fiókok](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. Kattintson a **hozzáférés munkahelyi vagy iskolai**.

    ![Hozzáférés a munkahelyi vagy iskolai](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. Az a **hozzáférés munkahelyi vagy iskolai** párbeszédpanel, kattintson a **Connect**.

    ![Kapcsolódás](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. Az a **munkahelyi vagy iskolai fiók beállítása** párbeszédpanelen adja meg a fiók nevét (pl.: someone@example.com), és kattintson a **következő**.

    ![Kapcsolódás](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. Az a **jelszó megadni** párbeszédpanelen írja be a jelszót, és kattintson **következő**.

    ![Kapcsolódás](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. Az a **minden beállítás kész** párbeszédpanel, kattintson a **végzett**.

    ![Kapcsolódás](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Ellenőrzés

Győződjön meg arról, hogy egy eszköz csatlakozik-e az Azure AD, tekintse át a **hozzáférés munkahelyi vagy iskolai** párbeszédpanel az eszközön.

![Regisztráljon](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

Másik lehetőségként is felhasználhatja a eszközbeállítások az Azure AD portálon.

![Regisztráljon](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>További lépések

- További részletekért lásd: a [eszköz kezelése az Azure Active Directoryban bemutatása](device-management-introduction.md)

- Az Azure AD portálon eszközök felügyeletével kapcsolatos további részletekért lásd: a [kezelése az Azure portál használatával eszközök ](device-management-azure-portal.md).




