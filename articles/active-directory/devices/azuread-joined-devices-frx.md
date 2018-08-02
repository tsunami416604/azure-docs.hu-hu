---
title: Az Azure ad-vel az első futás közben új Windows 10-es eszköz csatlakoztatása |} A Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan felhasználók is az Azure AD Join beállítása során az első futtatási élmény.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 1376f011d056aac33333f6ac31ee2eaadaf3ef4a
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39415000"
---
# <a name="join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Az Azure ad-vel az első futás közben új Windows 10-es eszköz csatlakoztatása

Az Eszközfelügyelet az Azure Active Directory (Azure AD) biztosíthatja, hogy a felhasználók az erőforrásokhoz hozzáférő eszközei megfeleljenek a biztonsági és megfelelőségi szabványoknak. További információkért lásd: a [bemutatása az Eszközfelügyelet az Azure Active Directory](overview.md).

A Windows 10-ben egy új eszköz csatlakozhat az Azure AD az első futtatási élmény (FRX) alatt.  
Ez lehetővé teszi, hogy az alkalmazottak vagy a diákok shrink-wrapped eszközök terjesztése.

Ha Windows 10 Professional vagy Windows 10 Enterprise telepítve az eszközön, a tapasztalatok alapértelmezés szerint a telepítési folyamat a vállalat által birtokolt eszközök.

A Windows a *out-of-box élmény*, egy a helyszíni Active Directory (AD) tartományhoz való csatlakozás nem támogatott. Ha azt tervezi, a számítógép csatlakoztatása egy AD-tartományát, a telepítés során, választhat a hivatkozás **Windows beállítása helyi fiókkal**. Ezután csatlakozhat a beállításokat a tartomány a számítógépen.
 


## <a name="before-you-begin"></a>Előkészületek

A Windows 10-es eszköz csatlakoztatása, hogy lehetővé tegye az eszközök regisztrálása az eszközregisztrációs szolgáltatás kell konfigurálni. Eszközök csatlakoztatása az Azure AD-bérlője számára, mellett rendelkeznie kell kevesebb, mint a beállított maximális regisztrált eszközök. További információkért lásd: [eszközbeállítások konfigurálása](device-management-azure-portal.md#configure-device-settings).

Ezenkívül ha a bérlő összevonást használ, az identitásszolgáltató támogatnia kell WS-Fed és a WS-Trust felhasználónév/jelszó végpont. 1.3-as vagy 2005 verzióra is lehet. Ez a protokoll-támogatás mind az eszköz csatlakoztatása az Azure ad-ben, és jelentkezzen be egy jelszót az eszköz szükség.

## <a name="joining-a-device"></a>Eszköz csatlakoztatása

**A Windows 10-es eszköz csatlakoztatása az Azure ad-ben FRX során:**


1. Kapcsolja be az új eszközt, és indítsa el a telepítési folyamatot, amikor megjelenik a **kész első** üzenet. Kövesse az utasításokat követve konfigurálja az eszközt.

2. Indítsa el a területi és nyelvi testreszabás. Majd fogadja el a Microsoft szoftverlicenc-feltételeket.
 
    ![Az Ön régiójában testreszabása](./media/azuread-joined-devices-frx/01.png)

3. Válassza ki a hálózatot, az internethez való kapcsolódáshoz használandó.

4. Kattintson a **szervezethez tartozik az eszköz**. 

    ![Kié a PC-s képernyő](./media/azuread-joined-devices-frx/02.png)

5. Adja meg a hitelesítő adatait, amely a szervezete által Önhöz van megadva, és kattintson **jelentkezzen be a**.

    ![Bejelentkezési képernyő](./media/azuread-joined-devices-frx/03.png)

6. Ön eszköz egyező bérlő keresi meg az Azure ad-ben. Ha egy összevont tartományban vannak, a rendszer átirányítja a helyszíni Secure Token Service (STS) kiszolgálóval, például az Active Directory összevonási szolgáltatások (AD FS).

7. Ha egy felhasználó egy nem összevont tartományban, közvetlenül az Azure AD-ban üzemeltetett lapon adja meg a hitelesítő adatait. 

8. A multi-factor authentication kihívást megadását kéri. 
 
9. Az Azure AD ellenőrzi, hogy szükség-e a mobileszköz-kezelés regisztrációs.

10. Windows regisztrálja az eszközt a szervezet címtárában, az Azure ad-ben, és regisztrálja azt a mobileszköz-kezelés, ha van ilyen.

11. Ha Ön:
    - A felügyelt felhasználó Windows megnyílik az automatikus bejelentkezési folyamat során az asztalon.

    - Egy összevont felhasználó, a rendszer átirányítja a Windows bejelentkezési képernyőn megadhatja a hitelesítő adatait.

## <a name="verification"></a>Ellenőrzés

Annak ellenőrzéséhez, hogy egy eszköz csatlakozott az Azure AD, tekintse át a **hozzáférés munkahelyi vagy iskolai** párbeszédpanel a Windows-eszközön. A párbeszédpanel jeleznie kell, hogy csatlakozott az Azure AD-címtárhoz.

![Hozzáférés munkahelyi vagy iskolai rendszerhez](./media/azuread-joined-devices-frx/13.png)


## <a name="next-steps"></a>További lépések

- További információkért lásd: a [bemutatása az Eszközfelügyelet az Azure Active Directory](overview.md).

- Az Azure AD portálon eszközök kezelésével kapcsolatos további információkért lásd: [az Eszközfelügyelet az Azure portal használatával](device-management-azure-portal.md).
