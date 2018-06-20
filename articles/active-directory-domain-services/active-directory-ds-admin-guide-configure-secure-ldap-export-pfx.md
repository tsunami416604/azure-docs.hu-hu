---
title: Biztonságos LDAP (LDAPS) konfigurálása az Azure AD tartományi szolgáltatásokban |} Microsoft Docs
description: Biztonságos LDAP (LDAPS) konfigurálása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: maheshu
ms.openlocfilehash: d2c7bd8b335ce49bed8e39812cccbe7ab474bf8f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36211526"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Biztonságos LDAP (LDAPS) használatos az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz tartozó konfigurálása

## <a name="before-you-begin"></a>Előkészületek
Győződjön meg arról, hogy befejezte [1. feladat – tanúsítvány beszerzése biztonságos LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md).


## <a name="task-2---export-the-secure-ldap-certificate-to-a-pfx-file"></a>2. feladat – a biztonságos LDAP tanúsítvány exportálása a. PFX-fájlból
Ez a feladat megkezdése előtt győződjön meg arról, szerzett be a biztonságos LDAP tanúsítványt nyilvános hitelesítésszolgáltatótól származó, vagy önaláírt tanúsítványt hozott létre.

A következő lépésekkel, a LDAPS tanúsítvány exportálása a. PFX-fájlt.

1. Nyomja meg a **Start** gombra, és írja be **R**. Az a **futtatása** párbeszédpanel, írja be **mmc** kattintson **OK**.

    ![Nyissa meg az MMC konzolt](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)
2. Az a **felhasználói fiókok felügyelete** kérdés, kattintson a **Igen** rendszergazdaként (Microsoft Management Console) MMC indítása.
3. Az a **fájl** menüben kattintson a **beépülő modul hozzáadása/eltávolítása...** .

    ![Az MMC-konzolt a beépülő modul hozzáadása](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)
4. Az a **hozzáadása vagy eltávolítása a beépülő modulok** párbeszédpanelen válassza a **tanúsítványok** beépülő modult, majd kattintson a **Hozzáadás >** gombra.

    ![Az MMC konzol Tanúsítványok beépülő modul hozzáadása](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)
5. Az a **tanúsítványok beépülő modul** varázslóban válassza **számítógépfiók** kattintson **következő**.

    ![Tanúsítványok beépülő modul számítógép fiók hozzáadása](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)
6. Az a **számítógép kijelölése** lapon, hogy melyik **helyi számítógép: (a számítógép a konzol fut)** kattintson **Befejezés**.

    ![Tanúsítványok beépülő modul – jelölje be a számítógép hozzáadása](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)
7. Az a **hozzáadása vagy eltávolítása a beépülő modulok** párbeszédpanel, kattintson a **OK** hozzáadása a tanúsítványok beépülő MMC-hez.

    ![Tanúsítványok beépülő MMC - végzett hozzáadása](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)
8. Az MMC ablak Ide kattintva kibonthatja a **konzolgyökér**. Meg kell jelennie a tanúsítványok beépülő modul betöltése. Kattintson a **tanúsítványok (helyi számítógép)** kibontásához. Ide kattintva bontsa ki a **személyes** csomópontot, majd a **tanúsítványok** csomópont.

    ![Nyissa meg személyes tanúsítványok tárolójában](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)
9. Meg kell jelenniük a létrehozott önaláírt tanúsítványt. A tanúsítvány meg arról, hogy az ujjlenyomat, amely a PowerShell windows jelentett létrehozása után a tanúsítvány tulajdonságainak ellenőrizheti.
10. Válassza ki az önaláírt tanúsítványt, és **kattintson a jobb gombbal**. A helyi menüben válassza ki a **feladataival** válassza **exportálása...** .

    ![Tanúsítvány exportálása](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)
11. Az a **Tanúsítványexportáló varázsló**, kattintson a **következő**.

    ![Exportálja a tanúsítványt varázsló](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)
12. Az a **titkos kulcs exportálása** lapon jelölje be **Igen, a titkos kulcs exportálását választom**, és kattintson a **következő**.

    ![Tanúsítvány titkos kulcs exportálása](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [!WARNING]
    > Exportálnia kell a titkos kulcsot, valamint a tanúsítvány. Ha megad egy PFX a tanúsítványhoz tartozó titkos kulcs nem tartalmazó, a felügyelt tartományok biztonságos LDAP engedélyezése sikertelen.
    >
    >
13. Az a **Exportfájlformátum** lapon jelölje be **személyes információcsere - PKCS #12 (. PFX)** , a fájlformátum az exportált tanúsítványhoz.

    ![A tanúsítvány fájlformátuma exportálása](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [!NOTE]
    > Csak a. PFX-fájl formátuma támogatott. Nem exportálja a tanúsítványt a. CER-fájlformátum.
    >
    >
14. Az a **biztonsági** lapon jelölje be a **jelszó** lehetőséget és a jelszót írja be védelméhez a. PFX-fájlt. Ne felejtse el ezt a jelszót, mert a következő feladat lesz szükség. Kattintson a **tovább** a folytatáshoz.

    ![A Tanúsítványexportálás jelszó ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [!NOTE]
    > Jegyezze fel ezt a jelszót. A felügyelt tartomány biztonságos LDAP engedélyezése során szüksége [3. feladat – a felügyelt tartomány számára biztonságos LDAP engedélyezése](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
    >
    >
15. Az a **exportálandó fájl** csoportjában adja meg a fájl nevét és helyét, hol szeretné, exportálja a tanúsítványt.

    ![A Tanúsítványexportálás elérési útja](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. Kattintson a következő lap **Befejezés** exportálja a tanúsítványt egy PFX-fájl. Amikor exportálta a tanúsítványt meg kell jelennie a megerősítő párbeszédpanelen.

    ![Kész tanúsítvány exportálása](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="next-step"></a>Következő lépés
[3. feladat – a felügyelt tartomány számára biztonságos LDAP engedélyezése](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
