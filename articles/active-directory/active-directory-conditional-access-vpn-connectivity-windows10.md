---
title: "Az Azure Active Directory feltételes hozzáférés a VPN-kapcsolat (előzetes verzió) |} Microsoft Docs"
description: "Ismerje meg, hogyan működik a feltételes hozzáférés Azure Active Directory VPN-kapcsolatot. "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 511df58befe9aed4aa65fc6944cae3a8e2c74c2d
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="azure-active-directory-conditional-access-for-vpn-connectivity-preview"></a>Az Azure Active Directory feltételes hozzáférés a VPN-kapcsolat (előzetes verzió)

A [Azure Active Directory (Azure AD) feltételes hozzáférés](active-directory-conditional-access-azure-portal.md), úgy finomhangolhatja, hogyan engedéllyel rendelkező felhasználók férhetnek hozzá az erőforrásokhoz. Az Azure AD feltételes hozzáférést a virtuális magánhálózati (VPN) kapcsolatot a VPN-kapcsolatok védelmét biztosíthatja.


Feltételes hozzáférés a VPN-kapcsolat konfigurálásához kövesse az alábbi lépéseket kell: 

1.  A VPN-kiszolgáló konfigurálása.
2.  A VPN-ügyfél konfigurálása.
3.  A feltételes hozzáférési házirend konfigurálása.


## <a name="before-you-begin"></a>Előkészületek

Ez a témakör feltételezi, hogy ismeri a következő témaköröket:

- [Feltételes hozzáférés az Azure Active Directoryban](active-directory-conditional-access-azure-portal.md)
- [VPN- és a feltételes hozzáférés](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)

Hogyan valósítja meg a Microsoft a szolgáltatás a dcu, lásd: [az automatikus VPN-profilhoz Windows 10 távoli hozzáférés](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile).   


## <a name="prerequisites"></a>Előfeltételek

Az Azure Active Directory feltételes hozzáférés a VPN-kapcsolat megadásához kell egy VPN-kiszolgáló konfigurálva van. 



## <a name="step-1-configure-your-vpn-server"></a>1. lépés: A VPN-kiszolgáló konfigurálása 

Ez a lépés az Azure AD a VPN-hitelesítésre szolgáló konfigurál. Feltételes hozzáférés a VPN-kapcsolat konfigurálásához kell:

1. A VPN-tanúsítványának létrehozása az Azure portálon.
2. A VPN-tanúsítványának letöltése.
2. A tanúsítvány a VPN-kiszolgáló központi telepítése.

Az Azure AD a VPN-tanúsítványának használ az Azure AD a VPN-kapcsolat hitelesítéséhez a Windows 10-ügyfelek számára kiállított tanúsítványok aláírásához. A jogkivonatot, amelyet a Windows 10-ügyfeleknek igényel olyan tanúsítvány, amely ezután megadja az alkalmazáshoz, amely ebben az esetben a VPN-kiszolgáló.

![A feltételes hozzáférés tanúsítvány letöltése](./media/active-directory-conditional-access-vpn-connectivity-windows10/06.png)

Az Azure-portálon is létrehozhat két tanúsítványt az átállás kezelését, ha egy tanúsítvány érvényessége hamarosan lejár. Amikor létrehoz egy tanúsítványt, kiválaszthatja, hogy-e az elsődleges tanúsítványt, amely a kapcsolat a tanúsítvány aláírására használatos a hitelesítés során.

A VPN-tanúsítványának létrehozása:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) globális rendszergazdaként.

2. Kattintson a bal oldali menü **Azure Active Directory**. 

    ![Válassza az Azure Active Directory elemet.](./media/active-directory-conditional-access-vpn-connectivity-windows10/01.png)

3. Az a **Azure Active Directory** lap a **kezelése** kattintson **feltételes hozzáférés**.

    ![Jelölje be a feltételes hozzáférés](./media/active-directory-conditional-access-azure-portal-get-started/02.png)

4. Az a **feltételes hozzáférés** lap a **kezelése** kattintson **VPN-kapcsolat (előzetes verzió)**.

    ![Válassza ki a VPN-kapcsolat](./media/active-directory-conditional-access-vpn-connectivity-windows10/03.png)

5. Az a **VPN-kapcsolat** kattintson **új tanúsítvány**.

    ![Új tanúsítvány kiválasztása](./media/active-directory-conditional-access-vpn-connectivity-windows10/04.png)

6. Az a **új** lapon, a következő lépésekkel:

    ![Válassza ki a telepítések időtartamát és elsődleges](./media/active-directory-conditional-access-vpn-connectivity-windows10/05.png)

    a. A **válassza ki a duration**, jelölje be **1 év**.

    b. A **elsődleges**, jelölje be **Igen**.

    c. Kattintson a **Create** (Létrehozás) gombra.

7. A VPN-kapcsolat lapján kattintson a **tanúsítvánnyal letöltés**.


Most már készen áll az újonnan létrehozott tanúsítványt a VPN-kiszolgáló telepítéséhez. A VPN-kiszolgáló, vegye fel a letöltött tanúsítvány egy *megbízható legfelső szintű Hitelesítésszolgáltatóhoz tartozó VPN-hitelesítési*.

Az Windows RRAS-alapú telepítések esetén a hálózati házirend-kiszolgálón adja hozzá azokat a legfelső szintű tanúsítvány a *vállalati NTauth* tárolja a következő parancsok futtatásával:

1. `certutil -dspublish <CACERT> RootCA`
2. `certutil -dspublish <CACERT> NtAuthCA`



## <a name="step-2-configure-your-vpn-client"></a>2. lépés: A VPN-ügyfél konfigurálása 

Ebben a lépésben konfigurálja a VPN-ügyfél-csatlakozási profil a [VPN és a feltételes hozzáférés](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access).


## <a name="step-3-configure-your-conditional-access-policy"></a>3. lépés: A feltételes hozzáférési szabályzat konfigurálása

Ez a szakasz a VPN-kapcsolatot a feltételes hozzáférési házirend konfigurálására vonatkozó útmutatásokat nyújt.


1. Az a **feltételes hozzáférés** lapon, a felső eszköztáron kattintson **Hozzáadás**.

    ![Válassza ki a feltételes hozzáférés lapon hozzáadása](./media/active-directory-conditional-access-vpn-connectivity-windows10/07.png)

2. Az a **új** lap a **neve** mezőbe írja be a házirend nevét. Írja be például **VPN-szabályzat**.

    ![A feltételes hozzáférés lapon vegye fel a házirend nevét](./media/active-directory-conditional-access-vpn-connectivity-windows10/08.png)

5. Az a **hozzárendelés** kattintson **felhasználók és csoportok**.

    ![Felhasználók és csoportok kijelölése](./media/active-directory-conditional-access-vpn-connectivity-windows10/09.png)

6. Az a **felhasználók és csoportok** lapon, a következő lépésekkel:

    ![Jelölje be a tesztfelhasználó számára](./media/active-directory-conditional-access-vpn-connectivity-windows10/10.png)

    a. Kattintson a **felhasználók és csoportok kiválasztása**.

    b. Kattintson a **Kiválasztás** gombra.

    c. A a **válasszon** lapon jelölje be a tesztfelhasználó számára, és kattintson a **válasszon**.

    d. Az a **felhasználók és csoportok** kattintson **végzett**.

7. Az a **új** lapon, a következő lépésekkel:

    ![Válassza ki a felhőalapú alkalmazások](./media/active-directory-conditional-access-vpn-connectivity-windows10/11.png)

    a. Az a **hozzárendelések** kattintson **felhőalapú alkalmazásokba**.

    b. A a **felhőalapú alkalmazásokba** kattintson **alkalmazások kiválasztása**, és kattintson a **kiválasztása**.

    c. Az a **válasszon** lap a **alkalmazások** mezőbe írja be **vpn**.

    d. Válassza ki **VPN-kiszolgáló**.

    e. Kattintson a **Kiválasztás** gombra.


13. A a **új** lapon nyissa meg a **Grant** lap a **vezérlők** területén kattintson **Grant**.

    ![SELECT engedély](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. Az a **Grant** lapon, a következő lépésekkel:

    ![Adja meg a multi-factor authentication szükséges](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Válassza ki **többtényezős hitelesítést**.

    b. Kattintson a **Kiválasztás** gombra.

15. Az a **új** lap **házirend engedélyezése**, kattintson a **a**.

    ![Házirend engedélyezése](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. Az a **új** kattintson **létrehozása**.



## <a name="next-steps"></a>További lépések

Hogyan valósítja meg a Microsoft a szolgáltatás megismerésében, lásd: [az automatikus VPN-profilhoz Windows 10 távoli hozzáférés](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile).    

