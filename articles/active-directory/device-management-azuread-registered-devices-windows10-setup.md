---
title: Azure Active Directory regisztrált eszközöket beállíthatja |} Microsoft Docs
description: Ismerje meg, hogyan állíthat be Azure Active Directory regisztrált eszközök.
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
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 541f37bb11c604567b356716db282d0e4e2f629b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713524"
---
# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Azure Active Directory beállítása regisztrált Windows 10-eszközök

Az eszköz kezelése az Azure Active Directory (Azure AD) biztosíthatja, hogy a felhasználók a biztonsági és megfelelőségi szabványoknak megfelelő eszközökről érnek el az erőforrásokat. További információkért lásd: [bemutatása az Azure Active Directoryban kezelés](device-management-introduction.md).

Ha engedélyezni szeretné a **kapcsolja a saját eszközök használata (BYOD)** esetben ez elvégezhető az Azure AD regisztrált eszközök konfigurálásával. Az Azure ad-ben a regisztrált az Azure AD-eszközök a Windows 10, iOS, Android és macOS állíthatja be. Ez a cikk biztosít a kapcsolódó lépések Windows 10 rendszerű eszközökhöz. 


## <a name="before-you-begin"></a>Előkészületek

Windows 10-eszköz regisztrálása, az eszközregisztrációs szolgáltatást kell állítani ahhoz, hogy regisztrálják eszközeiket. Emellett rendelkeznie kell a beállított maximális regisztrált kevesebb eszközök. További információkért lásd: [eszközbeállítások konfigurálása](device-management-azure-portal.md#configure-device-settings).

## <a name="what-you-should-know"></a>Tudnivalók

Eszközök regisztrálásakor figyelembe kell venni a következő:

- Windows regisztrálja az eszközt a munkahely címtárában az Azure AD

- Szükséges lehet a multi-factor authentication ellenőrző végrehajtania. Ez a probléma a rendszergazda által konfigurálható.

- Az Azure AD ellenőrzi, hogy az eszköz mobileszköz-kezelési beléptetéséhez szükséges-e, és regisztrálja azt, ha van ilyen.

- Ha egy felügyelt felhasználók Windows végigvezeti Önt az asztalhoz az automatikus bejelentkezés.

- Ha egy összevont felhasználó, akkor megnyílik a Windows bejelentkezési képernyő hitelesítő adatait.


## <a name="registering-a-device"></a>Eszközök regisztrálása

A szakasz ismerteti a lépéseket az Azure ad a Windows 10-eszköz regisztrálása. Sikeresen regisztrált egy eszközt a megjelenik egy **munkahelyi vagy iskolai fiók** bejegyzés.

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


5. Az a **munkahelyi vagy iskolai fiók beállítása** párbeszédpanelen adja meg a fiók nevét (például someone@example.com), és kattintson a **következő**.

    ![Kapcsolódás](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. Az a **jelszó megadni** párbeszédpanelen írja be a jelszót, és kattintson **következő**.

    ![Kapcsolódás](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. Az a **minden beállítás kész** párbeszédpanel, kattintson a **végzett**.

    ![Kapcsolódás](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Ellenőrzés

Győződjön meg arról, hogy egy eszköz csatlakozik-e az Azure AD, tekintse át a **hozzáférés munkahelyi vagy iskolai** párbeszédpanel az eszközön.

![Regisztráljon](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

Azt is megteheti áttekintheti az Azure AD portálon Eszközbeállítások.

![Regisztráljon](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>További lépések

- További információkért lásd: a [eszköz kezelése az Azure Active Directoryban bemutatása](device-management-introduction.md)

- Az Azure AD portálon eszközök felügyeletével kapcsolatos további információkért lásd: a [kezelése az Azure portál használatával eszközök ](device-management-azure-portal.md).




