---
title: Új Windows 10-eszköz az első futtatás során az Azure AD JOIN |} Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan felhasználók állíthat be az Azure AD Join a first run Experience összetevő során.
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
ms.openlocfilehash: 0341c5b65816cf1c54fe3f2f7781dde46bf084ad
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757392"
---
# <a name="join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Új Windows 10-eszköz az első futtatás során az Azure AD JOIN

Az eszköz kezelése az Azure Active Directory (Azure AD) biztosíthatja, hogy a felhasználók a biztonsági és megfelelőségi szabványoknak megfelelő eszközökről érnek el az erőforrásokat. További részletekért lásd: a [bemutatása az Azure Active Directoryban kezelés](device-management-introduction.md).

A Windows 10 új eszköz csatlakozhat az Azure AD a kezdőélmény (FRX) során.  
Ez lehetővé teszi, hogy az alkalmazottak vagy az diákok légmentes fóliacsomagolásúak eszközök terjesztése.

Ha Windows 10 Professional vagy Windows 10 Enterprise telepítve az eszközön, a felhasználói élmény alapértelmezés szerint a telepítési folyamat a vállalat által birtokolt eszközök.

A Windows *out-of-box élmény*, egy a helyszíni Active Directory (AD) tartományhoz való csatlakozás nem támogatott. Ha azt tervezi, a számítógép csatlakoztatása egy Active Directory-tartománynak, a telepítés során, ki kell jelölni a hivatkozás **állítsa be a Windowst helyi fiókkal**. Majd csatlakozhat a tartományhoz, a beállítások a számítógépen.
 


## <a name="before-you-begin"></a>Előkészületek

Egy Windows 10 rendszerű eszköz csatlakozni az eszközregisztrációs szolgáltatást kell állítani ahhoz, hogy regisztrálják eszközeiket. Mellett eszközök csatlakoztatása az Azure AD-bérlőben engedéllyel, a beállított maximális regisztrált kevesebb eszközök kell rendelkeznie. További részletekért lásd: [eszközbeállítások konfigurálása](device-management-azure-portal.md#configure-device-settings).

Ha a bérlő össze van vonva, továbbá az identitásszolgáltató támogatnia kell WS-Fed és a WS-Trust felhasználónév/jelszó végpont. Ez lehet 1.3 vagy 2005 verzióra. A protokoll támogatására szükség az eszköz csatlakoztatása az Azure AD és az eszköz jelszóval bejelentkezni.

## <a name="joining-a-device"></a>Eszköz csatlakoztatása

**Egy Windows 10 rendszerű eszköz csatlakoztatása az Azure AD FRX során:**


1. Kapcsolja be az új eszközt, és a telepítés megkezdéséhez, láthatja a **készen első** üzenet. Kövesse az utasításokat, az eszköz beállításához.

2. Indítsa el a területi és nyelvi beállítások testreszabása. Majd fogadja el a Microsoft szoftverlicenc-szerződést.
 
    ![A régió testreszabása](./media/device-management-azuread-joined-devices-frx/01.png)

3. Válassza ki a hálózatot, az internethez való kapcsolódáshoz használandó.

4. Kattintson a **saját szervezethez tartozik az eszköz**. 

    ![Ez a számítógép képernyő tulajdonosára](./media/device-management-azuread-joined-devices-frx/02.png)

5. Írja be a hitelesítő adatokkal, amelyeket Ön számára a szervezet által, és kattintson **bejelentkezés**.

    ![Bejelentkezési képernyő](./media/device-management-azuread-joined-devices-frx/03.png)

6. Ön eszköz egyező bérlő megkeresi az Azure ad-ben. Ha egy összevont tartományban vannak, akkor a helyszíni Secure Token Service (STS) kiszolgálón, például az Active Directory összevonási szolgáltatások (AD FS) van átirányítva.

7. Ha egy felhasználó egy nem összevont tartományban, adja meg a hitelesítő adatok közvetlenül az Azure AD által szolgáltatott lapon. 

8. A multi-factor authentication kihívást kéri. 
 
9. Az Azure AD ellenőrzi, hogy szükség-e a mobileszköz-kezelés igénylésére.

10. A Windows a munkahely címtárában, az Azure ad-ben regisztrálja az eszközt, és regisztrálja a mobileszköz-kezelés, ha van ilyen.

11. Ha:
    - A felügyelt felhasználói Windows viszi az automatikus bejelentkezési folyamat során az asztalon.

    - Egy összevont felhasználó, akkor a rendszer átirányítja a Windows bejelentkezési képernyő hitelesítő adatait.

## <a name="verification"></a>Ellenőrzés

Győződjön meg arról, hogy egy eszköz csatlakozik-e az Azure AD, tekintse át a **hozzáférés munkahelyi vagy iskolai** párbeszédpanel a Windows-eszközön. A párbeszédpanel kell jeleznie, hogy az Azure AD-címtár csatlakozik.

![Hozzáférés a munkahelyi vagy iskolai](./media/device-management-azuread-joined-devices-frx/13.png)


## <a name="next-steps"></a>További lépések

- További részletekért lásd: a [bemutatása az Azure Active Directoryban kezelés](device-management-introduction.md).

- Az Azure AD portálon eszközök felügyeletével kapcsolatos további részletekért lásd: [kezelése az Azure portál használatával eszközök](device-management-azure-portal.md).
