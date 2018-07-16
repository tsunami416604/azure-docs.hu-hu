---
title: Állítsa be az Azure Active Directoryban regisztrált eszközökön |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be az Azure Active Directoryban regisztrált eszközök.
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
ms.openlocfilehash: 7ce632c76a86fb00101db6664e9e79615484f9a1
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059794"
---
# <a name="set-up-azure-active-directory-registered-windows-10-devices"></a>Állítsa be az Azure Active Directoryban regisztrált Windows 10-eszközök

Az Eszközfelügyelet az Azure Active Directory (Azure AD) biztosíthatja, hogy a felhasználók az erőforrásokhoz hozzáférő eszközei megfeleljenek a biztonsági és megfelelőségi szabványoknak. További információkért lásd: [bemutatása az Eszközfelügyelet az Azure Active Directory](../device-management-introduction.md).

Ha engedélyezni szeretné a **Bring Your Own Device (BYOD)** forgatókönyvben Ez elvégezhető konfigurálása az Azure ad-ben regisztrált eszközök által. Az Azure AD-ben konfigurálhatja úgy az Azure ad-ben regisztrált eszközök Windows 10, iOS, Android és MacOS rendszeren. Ez a cikk a kapcsolódó lépések Windows 10 rendszerű eszközökhöz. 


## <a name="before-you-begin"></a>Előkészületek

A Windows 10 rendszerű eszköz regisztrálása, hogy lehetővé tegye az eszközök regisztrálása az eszközregisztrációs szolgáltatás kell konfigurálni. Emellett rendelkeznie kell kevesebb, mint a beállított maximális regisztrált eszközök. További információkért lásd: [eszközbeállítások konfigurálása](../device-management-azure-portal.md#configure-device-settings).

## <a name="what-you-should-know"></a>Tudnivalók

Egy eszköz regisztrálásakor a szem előtt kell tartania a következő:

- Windows regisztrálja az eszközt a szervezet címtárában az Azure ad-ben

- Akkor haladhat végig a multi-factor authentication kihívás lehet szükség. Ez a kérdés nem konfigurálható, a rendszergazda által.

- Az Azure AD ellenőrzi, hogy az eszköz mobileszköz-felügyeleti beléptetéséhez szükséges-e, és regisztrálja azt, ha van ilyen.

- A felügyelt felhasználó esetén Windows végigvezeti az asztalhoz az automatikus bejelentkezést.

- Ha Ön egy összevont felhasználó, megnyílik egy adott képernyőre Windows bejelentkezési hitelesítő adatait.


## <a name="registering-a-device"></a>Eszköz regisztrálása

A szakasz ismerteti a lépéseket az Azure ad a Windows 10 rendszerű eszköz regisztrálása. Sikeresen regisztrált eszköz megjelenik-e az egy **munkahelyi vagy iskolai fiók** bejegyzés.

![Regisztráljon](./media/device-management-azuread-registered-devices-windows10-setup/08.png)


**A Windows 10 rendszerű eszköz regisztrálása:**

1. Az a **Start** menüben kattintson a **beállítások**.

    ![Beállítások](./media/device-management-azuread-registered-devices-windows10-setup/01.png)

2. Kattintson a **fiókok**.

    ![Fiókok](./media/device-management-azuread-registered-devices-windows10-setup/02.png)


3. Kattintson a **hozzáférés munkahelyi vagy iskolai**.

    ![Hozzáférés munkahelyi vagy iskolai rendszerhez](./media/device-management-azuread-registered-devices-windows10-setup/03.png)

4. Az a **hozzáférés munkahelyi vagy iskolai** párbeszédpanelen kattintson a **Connect**.

    ![Kapcsolódás](./media/device-management-azuread-registered-devices-windows10-setup/04.png)


5. Az a **munkahelyi vagy iskolai fiók beállítása** párbeszédpanelen adja meg a fiók nevét (például someone@example.com), és kattintson a **tovább**.

    ![Kapcsolódás](./media/device-management-azuread-registered-devices-windows10-setup/06.png)


6. Az a **jelszó** párbeszédpanelen adja meg a jelszót, és kattintson **tovább**.

    ![Kapcsolódás](./media/device-management-azuread-registered-devices-windows10-setup/05.png)


7. Az a **készen vagyunk** párbeszédpanelen kattintson a **kész**.

    ![Kapcsolódás](./media/device-management-azuread-registered-devices-windows10-setup/07.png)

## <a name="verification"></a>Ellenőrzés

Győződjön meg arról, hogy egy eszköz csatlakozott egy Azure ad-ben, tekintse át a **hozzáférés munkahelyi vagy iskolai** párbeszédpanel az eszközön.

![Regisztráljon](./media/device-management-azuread-registered-devices-windows10-setup/08.png)

Azt is megteheti áttekintheti az Azure AD Portal Eszközbeállítások.

![Regisztráljon](./media/device-management-azuread-registered-devices-windows10-setup/09.png)





## <a name="next-steps"></a>További lépések

- További információkért lásd: a [az Eszközfelügyelet az Azure Active Directory bemutatása](../device-management-introduction.md)

- Az Azure AD portálon eszközök kezelésével kapcsolatos további információkért lásd: a [az Eszközfelügyelet az Azure portal használatával ](../device-management-azure-portal.md).




