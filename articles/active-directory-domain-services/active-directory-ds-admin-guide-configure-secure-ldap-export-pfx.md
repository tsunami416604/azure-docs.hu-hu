---
title: Hozzon létre egy secure LDAP-tanúsítványt a tartomány egy Azure AD tartományi szolgáltatások kezelése |} A Microsoft Docs
description: Hozzon létre egy secure LDAP-tanúsítványt a tartomány egy Azure AD tartományi szolgáltatások kezelése
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
ms.topic: conceptual
ms.date: 08/01/2017
ms.author: maheshu
ms.openlocfilehash: d34da3c7a32a9fd425e30880ba9bc808d9d2bab1
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505984"
---
# <a name="create-a-pfx-file-with-the-secure-ldap-ldaps-certificate-for-a-managed-domain"></a>Hozzon létre egy. PFX-fájlt a biztonságos LDAP (LDAPS) tanúsítványt a felügyelt tartományhoz

## <a name="before-you-begin"></a>Előkészületek
Teljes [az 1. feladat: tanúsítvány beszerzése biztonságos LDAP](active-directory-ds-admin-guide-configure-secure-ldap.md).


## <a name="task-2-export-the-secure-ldap-certificate-to-a-pfx-file"></a>2. feladat: A secure LDAP-tanúsítványt exportálni egy. PFX-fájl
Ez a feladat a Kezdés előtt lekérése a secure LDAP-tanúsítványt nyilvános hitelesítésszolgáltatótól származó, vagy hozzon létre egy önaláírt tanúsítványt.

Az LDAPS-t tanúsítványának exportálásához egy. PFX-fájlt:

1. Nyomja le az **Start** gombra, és írja be **R**. Az a **futtatása** párbeszédpanel, írja be **mmc** kattintson **OK**.

    ![Nyissa meg az MMC konzolt](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)
2. Az a **felhasználói fiókok felügyelete** kérdés, kattintson a **Igen** rendszergazdaként (a Microsoft Management Console) MMC indítása.
3. Az a **fájl** menüben kattintson a **beépülő modul hozzáadása/eltávolítása...** .

    ![Az MMC-konzolt a beépülő modul hozzáadása](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)
4. Az a **hozzáadása vagy eltávolítása a beépülő modulok** párbeszédpanelen válassza ki a **tanúsítványok** beépülő modult, majd kattintson a **Hozzáadás >** gombra.

    ![Az MMC konzol Tanúsítványok beépülő modul hozzáadása](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)
5. Az a **tanúsítványok beépülő modul** varázslóban válassza **számítógépfiók** kattintson **tovább**.

    ![Tanúsítványkezelő beépülő modul számítógép fiók hozzáadása](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)
6. Az a **számítógép kijelölése** lapra, jelölje be **helyi számítógép: (a számítógépet a konzol fut)** kattintson **Befejezés**.

    ![Tanúsítványok beépülő modul – válassza ki a számítógép hozzáadása](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)
7. Az a **hozzáadása vagy eltávolítása a beépülő modulok** párbeszédpanelen kattintson a **OK** hozzáadása a tanúsítványok beépülő MMC.

    ![Tanúsítványok beépülő MMC - kész hozzáadása](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)
8. Bontsa ki az MMC ablakában kattintson **konzolgyökér**. A tanúsítványok beépülő modul betöltése kell megjelennie. Kattintson a **tanúsítványok (helyi számítógép)** kibontásához. Ide kattintva bontsa ki a **személyes** csomópontot, majd a **tanúsítványok** csomópont.

    ![Nyissa meg személyes tanúsítványok tárolójában](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)
9. A létrehozott önaláírt tanúsítványt kell megjelennie. A tanúsítvány ujjlenyomata megegyezik a tanúsítvány létrehozásakor a PowerShell windows jelentett ellenőrzése tulajdonságainak ellenőrizheti.
10. Válassza ki az önaláírt tanúsítványt, és **kattintson a jobb gombbal**. A helyi menüben válassza ki a **feladatok** válassza **exportálása...** .

    ![Tanúsítvány exportálása](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)
11. Az a **Tanúsítványexportáló varázsló**, kattintson a **tovább**.

    ![Exportálja a tanúsítványt varázsló](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)
12. A a **titkos kulcs exportálása** lapon jelölje be **Igen, a titkos kulcs exportálásának**, és kattintson a **tovább**.

    ![Tanúsítvány titkos kulcs exportálása](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [!WARNING]
    > Exportálnia kell a titkos kulcsot, valamint a tanúsítvány. Ha megad egy PFX, amely nem tartalmazza a tanúsítvány titkos kulcsát, a felügyelt tartomány secure LDAP engedélyezése sikertelen.
    >
    >

13. Az a **Exportfájlformátum** lapon jelölje be **személyes információcsere – PKCS #12 (. PFX)** , a fájlformátum az exportált tanúsítványhoz.

    ![Exportálás tanúsítvány fájlformátuma](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [!NOTE]
    > Csak a. PFX-fájl formátuma támogatott. Nem akarom exportálni a tanúsítványt a. CER-fájlformátum.
    >
    >

14. Az a **biztonsági** lapon válassza ki a **jelszó** védelméhez lehetőséget és adja meg egy jelszót a. PFX-fájlt. Ne felejtse el ezt a jelszót, mivel az a következő feladat lesz szükség. Kattintson a **Tovább** gombra.

    ![A Tanúsítványexportálás jelszó ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [!NOTE]
    > Jegyezze fel ezt a jelszót. Szüksége lesz rá az a felügyelt tartomány secure LDAP engedélyezése során [3. feladat – a felügyelt tartomány secure LDAP engedélyezése](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
    >
    >

15. Az a **exportálandó fájl** adja meg azokat a fájl nevét és helyét, ahol szeretné exportálni a tanúsítványt.

    ![A Tanúsítványexportálás elérési útja](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. A következő lapon kattintson a **Befejezés** exportálja a tanúsítványt egy PFX-fájlba. Ha a tanúsítvány exportálása párbeszédpanel a megerősítő kell megjelennie.

    ![Kész tanúsítvány exportálása](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="next-step"></a>Következő lépés
[3. feladat: a felügyelt tartomány secure LDAP engedélyezése](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
