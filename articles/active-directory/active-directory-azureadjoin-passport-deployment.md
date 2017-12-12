---
title: "Engedélyezze a Microsoft vállalati Windows Hello a szervezet |} Microsoft Docs"
description: "A Microsoft Passport engedélyezése a szervezetében üzembe helyezési utasításokat tartalmaz."
services: active-directory
documentationcenter: 
keywords: "a Microsoft Passport, Microsoft Windows Hello-üzleti központi telepítés konfigurálása"
author: MarkusVi
manager: mtillman
tags: azure-classic-portal
ms.assetid: 7dbbe3c6-1cd7-429c-a9b2-115fcbc02416
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.openlocfilehash: b5b48edda76bedc14d5528fa82f1d9b7bfffe53a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="enable-microsoft-windows-hello-for-business-in-your-organization"></a>Engedélyezze a Microsoft vállalati Windows Hello a szervezetben
Után [Windows 10-tartományhoz csatlakoztatott eszközök csatlakoztatása az Azure Active Directoryval](active-directory-azureadjoin-devices-group-policy.md), engedélyezze a Microsoft vállalati Windows Hello a szervezet érdekében tegye a következőket:

1. A System Center Configuration Manager központi telepítése  
2. Házirend-beállítások konfigurálása
3. A tanúsítványprofilok konfigurálása  

## <a name="deploy-system-center-configuration-manager"></a>A System Center Configuration Manager központi telepítése
A Windows Hello üzleti kulcsok alapján felhasználói tanúsítványok telepítése, a következőkre lesz szüksége:

* **A System Center Configuration Manager aktuális ágának** -1606 vagy jobb verzióját telepíteni kell. További információkért lásd: a [dokumentáció a System Center Configuration Manager](https://technet.microsoft.com/library/mt346023.aspx) és [System Center Configuration Manager Team Blog](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx).
* **Nyilvános kulcsokra épülő infrastruktúra (PKI)** - engedélyezése a Microsoft vállalati Windows Hello segítségével a felhasználói tanúsítványok, rendelkeznie kell egy nyilvános kulcsokra épülő infrastruktúra helyen. Ha még nincs fiókja, vagy nem kívánja használni a felhasználói tanúsítványok, telepíthet egy új tartományvezérlő, amely a Windows Server 2016-os build 10551 (vagy nagyobb frekvenciával) telepítve van. Kövesse a lépéseket a [replika tartományvezérlő telepítése meglévő tartományban](https://technet.microsoft.com/library/jj574134.aspx) vagy [egy új Active Directory-erdő telepítéséhez, ha hoz létre egy új környezetben](https://technet.microsoft.com/library/jj574166). (Az ISO letölthetők a [Signiant Media Exchange](https://datatransfer.microsoft.com/signiant_media_exchange/spring/main?sdkAccessible=true).)

## <a name="configure-policy-settings"></a>Házirend-beállítások konfigurálása
Az a Microsoft Windows Hello-üzleti házirend-beállítások megadásához két lehetőség közül választhat:

* Az Active Directory csoportházirend 
* A System Center Configuration Managerben 

Az Active Directoryban a csoportházirendekkel a konfigurálásának javasolt módja a Microsoft Windows Hello az üzleti házirend-beállításokat. 

A System Center Configuration Manager az előnyben részesített módszere is használata esetén azt szolgáltatást tanúsítványok központi telepítésére. Ebben a forgatókönyvben:

* Biztosítja a kompatibilitást a újabb történő telepítések esetén
* Az ügyféloldali Windows 10-es verzió 1607 vagy nagyobb frekvenciával igényel.

### <a name="configure-microsoft-windows-hello-for-business-via-group-policy-in-active-directory"></a>Konfigurálja a Microsoft vállalati Windows Hello az Active Directory csoportházirend segítségével
**Lépéseket**:

1. Nyissa meg a Kiszolgálókezelőt, és navigáljon a **eszközök** > **csoportházirend-kezelő**.
2. A Csoportházirend kezelése eszközt navigáljon a kívánja engedélyezése az Azure AD-csatlakozás tartományhoz tartozó tartományi csomópontot.
3. Kattintson a jobb gombbal **csoportházirend-objektumok**, és válassza ki **új**. Nevezze el a csoportházirend-objektum, például engedélyezése vállalati Windows Hello. Kattintson az **OK** gombra.
4. Kattintson a jobb gombbal az új csoportházirend-objektumot, majd válassza ki **szerkesztése**.
5. Navigáljon a **számítógép konfigurációja** > **házirendek** > **felügyeleti sablonok** > **Windows Összetevők** > **a vállalati Windows Hello**.
6. Kattintson a jobb gombbal **engedélyezése vállalati Windows Hello**, majd válassza ki **szerkesztése**.
7. Válassza ki a **engedélyezve** választógomb, és kattintson a **alkalmaz**. Kattintson az **OK** gombra.
8. A csoportházirend-objektum most hozzákapcsolhatja egy tetszés szerinti helyre. Ahhoz, hogy ez a házirend az összes a tartományhoz csatlakoztatott Windows 10-eszközök a szervezetben, kapcsolja a csoportházirendet a tartományhoz. Példa:
   * Egy adott szervezeti egység (OU) az Active Directoryban, ha Windows 10-es tartományhoz csatlakoztatott számítógépek lesznek elhelyezve
   * Egy adott biztonsági csoport, amely tartalmazza a Windows 10-tartományhoz csatlakoztatott számítógépeket, amelyek automatikusan regisztrálva lesz az Azure ad szolgáltatással

### <a name="configure-windows-hello-for-business-using-system-center-configuration-manager"></a>A System Center Configuration Manager vállalati Windows Hello konfigurálása
**Lépéseket**:

1. Nyissa meg a **System Center Configuration Manager**, és navigáljon arra **eszközök és megfelelőség > megfelelőségi beállítások > Vállalati erőforrások elérése > üzleti profilok a Windows Hello-**.
   
    ![A vállalati Windows Hello konfigurálása](./media/active-directory-azureadjoin-passport-deployment/01.png)
2. A felső eszköztáron kattintson **létrehozása a Windows Hello-profil üzleti**.
   
    ![A vállalati Windows Hello konfigurálása](./media/active-directory-azureadjoin-passport-deployment/02.png)
3. Az a **általános** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![A vállalati Windows Hello konfigurálása](./media/active-directory-azureadjoin-passport-deployment/03.png)
   
    a. Az a **neve** szövegmező, írja be például a profil nevét **WHfB profil**.
   
    b. Kattintson a **Tovább** gombra.
4. Az a **által támogatott platformok** párbeszédpanelen jelölje ki a platformokat, amelyek megkapják a Windows Hello-profil üzleti, és kattintson a **következő**.
   
    ![A vállalati Windows Hello konfigurálása](./media/active-directory-azureadjoin-passport-deployment/04.png)
5. Az a **beállítások** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![A vállalati Windows Hello konfigurálása](./media/active-directory-azureadjoin-passport-deployment/05.png)
   
    a. Mint **konfigurálása vállalati Windows Hello**, jelölje be **engedélyezve**.
   
    b. Mint **egy platformmegbízhatósági modul (TPM) használata**, jelölje be **szükséges**. 
   
    c. Mint **hitelesítési módszer**, jelölje be **tanúsítványalapú**.
   
    d. Kattintson a **Tovább** gombra.
6. Az a **összegzés** párbeszédpanel, kattintson a **következő**.
7. Az a **befejezési** párbeszédpanel, kattintson a **Bezárás**.
8. A felső eszköztáron kattintson **telepítés**.
   
    ![A vállalati Windows Hello konfigurálása](./media/active-directory-azureadjoin-passport-deployment/06.png)

## <a name="configure-the-certificate-profile"></a>A tanúsítványprofilok konfigurálása
Ha a helyszíni hitelesítéshez tanúsítvány alapú hitelesítést használ, meg kell konfigurálnia és tanúsítványprofil központi telepítése. Ez a feladat meg kell adnia egy NDES-kiszolgáló és a Tanúsítványregisztrációs pont helyrendszer-szerepkör a System Center Configuration Manager számára. További részletekért lásd: a [előfeltételei a Configuration Manager Tanúsítványprofiljaihoz](https://technet.microsoft.com/library/dn261205.aspx).

1. Nyissa meg a **System Center Configuration Manager**, és navigáljon arra **eszközök és megfelelőség > megfelelőségi beállítások > Vállalati erőforrások elérése > Tanúsítványprofilok**.
2. Válasszon olyan sablont, amely rendelkezik az intelligens kártyás bejelentkezési kibővített kulcshasználat (EKU).

Az a **SCEP-igénylés** lap a tanúsítványprofilok, meg kell adnia **telepítse a Passport for Work szolgáltatásba, másként meghibásodás történik** , a **kulcstároló-szolgáltató**.

## <a name="next-steps"></a>Következő lépések
* [Vállalati használatú Windows 10: Az eszközök munkahelyi célú használata](active-directory-azureadjoin-windows10-devices-overview.md)
* [A felhőalapú képességek kiterjesztése a Windows 10-eszközökre az Azure Active Directory Joinon keresztül](active-directory-azureadjoin-user-upgrade.md)
* [Jelszóhasználat nélküli hitelesítés](active-directory-azureadjoin-passport.md)
* [További információk az Azure AD Join használati forgatókönyveiről](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Tartományhoz csatlakoztatott eszközök csatlakoztatása az Azure AD-hez Windows 10-es környezetben](active-directory-azureadjoin-devices-group-policy.md)
* [Az Azure AD Join beállítása](active-directory-azureadjoin-setup.md)

