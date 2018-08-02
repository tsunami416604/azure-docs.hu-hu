---
title: Állítsa be az Azure Active Directory-hez csatlakoztatott eszközök |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be az Azure Active Directoryhoz csatlakoztatott eszközökön.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: lizross
ms.reviewer: jairoc
ms.openlocfilehash: 83eea565cc775a0569015c04d79d627d6ba120e6
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39411709"
---
# <a name="set-up-azure-active-directory-joined-devices"></a>Állítsa be az Azure Active Directory-hez csatlakoztatott eszközök

Az Eszközfelügyelet az Azure Active Directory (Azure AD) biztosíthatja, hogy a felhasználók az erőforrásokhoz hozzáférő eszközei megfeleljenek a biztonsági és megfelelőségi szabványoknak. További információkért lásd: [bemutatása az Eszközfelügyelet az Azure Active Directory](../device-management-introduction.md).

Ha azt szeretné, ahhoz, hogy a munka a Windows 10-es eszközök Azure AD felügyelete alá tartozik, ez elvégezhető az Azure AD-csatlakoztatott eszközök konfigurálásával. Ez a témakör a kapcsolódó lépések biztosít. 


## <a name="prerequisites"></a>Előfeltételek

A Windows 10-es eszköz csatlakoztatása, hogy lehetővé tegye az eszközök regisztrálása az eszközregisztrációs szolgáltatás kell konfigurálni. Eszközök csatlakoztatása az Azure AD-bérlője számára, mellett rendelkeznie kell kevesebb, mint a beállított maximális regisztrált eszközök. További információkért lásd: [eszközbeállítások konfigurálása](../devices/device-management-azure-portal.md#configure-device-settings).



## <a name="what-you-should-know"></a>Tudnivalók


- Windows csatlakoztatja az eszközt az Azure ad-ben a szervezet címtárában.

- Akkor haladhat végig a multi-factor authentication kihívás lehet szükség. Ez a kérdés nem konfigurálható, a rendszergazda által.

- Az Azure AD ellenőrzi, hogy az eszköz mobileszköz-felügyeleti beléptetéséhez szükséges-e, és regisztrálja azt, ha van ilyen.

- A felügyelt felhasználó esetén Windows végigvezeti az asztalhoz az automatikus bejelentkezést.

- Ha Ön egy összevont felhasználó, akkor jelentkezzen be a hitelesítő adatok használatával.

- Összevont, ha az identitásszolgáltató támogatnia kell a WS-Fed és a WS-Trust felhasználónév/jelszó végpont. 1.3-as vagy 2005 verzióra is lehet. Ez a protokoll-támogatás mind az eszköz csatlakoztatása az Azure ad-ben, és jelentkezzen be egy jelszót az eszköz szükség. 




## <a name="joining-a-device"></a>Eszköz csatlakoztatása

A szakasz ismerteti a lépéseket a Windows 10 rendszerű eszköz csatlakoztatása az Azure ad-ben. Sikeresen csatlakoztatva eszköz megjelenik-e **csatlakozik \<az Azure AD-\>**.

![Csatlakozva](./media/device-management-azuread-joined-devices-setup/13.png)


**A Windows 10 rendszerű eszköz csatlakozni:**

1. Az a **Start** menüben kattintson a **beállítások**.

    ![Beállítások](./media/device-management-azuread-joined-devices-setup/01.png)

2. Kattintson a **fiókok**.

    ![Fiókok](./media/device-management-azuread-joined-devices-setup/02.png)


3. Kattintson a **hozzáférés munkahelyi vagy iskolai**.

    ![Hozzáférés munkahelyi vagy iskolai rendszerhez](./media/device-management-azuread-joined-devices-setup/03.png)

4. Az a **hozzáférés munkahelyi vagy iskolai** párbeszédpanelen kattintson a **Connect**.

    ![Kapcsolódás](./media/device-management-azuread-joined-devices-setup/04.png)


5. Az a **munkahelyi vagy iskolai fiók beállítása** párbeszédpanelen kattintson a **eszköz csatlakoztatása az Azure Active Directory**.

    ![Kapcsolódás](./media/device-management-azuread-joined-devices-setup/08.png)


6. Az a **jelentkezzen** párbeszédpanelen adja meg a fiók nevét (például someone@example.com), és kattintson a **tovább**.

    ![Nézzük az első lépések](./media/device-management-azuread-joined-devices-setup/10.png)


6. Az a **jelszó** párbeszédpanelen adja meg a jelszót, és kattintson **jelentkezzen be a**.

    ![Írja be a jelszót](./media/device-management-azuread-joined-devices-setup/05.png)


7. Az a **ellenőrizze, hogy ez a szervezet** párbeszédpanelen kattintson a **csatlakozzon**.

    ![Ellenőrizze, hogy ez a szervezet](./media/device-management-azuread-joined-devices-setup/11.png)


8. Az a **készen vagyunk** párbeszédpanelen kattintson a **kész**.

    ![Minden beállítás kész](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>Ellenőrzés

Győződjön meg arról, hogy egy eszköz csatlakozott egy Azure ad-ben, tekintse át a **hozzáférés munkahelyi vagy iskolai** párbeszédpanel az eszközön.

![Csatlakozva](./media/device-management-azuread-joined-devices-setup/13.png)

Ehelyett futtathatja a következő parancsot: `dsregcmd /status`  
A sikeresen csatlakoztatva eszközön **AzureAdJoined** van **Igen**.

![Csatlakozva](./media/device-management-azuread-joined-devices-setup/14.png)

Emellett áttekintheti az Azure AD Portal Eszközbeállítások.

![Csatlakozva](./media/device-management-azuread-joined-devices-setup/15.png)

További információkért lásd: [keresse meg az eszközöket](../devices/device-management-azure-portal.md#locate-devices).


## <a name="next-steps"></a>További lépések

További információkért lásd: 

- A [az Eszközfelügyelet az Azure Active Directory bemutatása](../devices/overview.md)
- [Az Eszközfelügyelet az Azure portal használatával](../devices/device-management-azure-portal.md)


