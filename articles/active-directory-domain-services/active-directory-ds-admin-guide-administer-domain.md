---
title: 'Az Azure Active Directory tartományi szolgáltatások: Felügyelt tartomány kezelése |} A Microsoft Docs'
description: Az Azure Active Directory Domain Services felügyelt tartomány felügyeletéhez
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: d4fdbc75-3e6b-4e20-8494-5dcc3bf2220a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: f1e3272bd8f0d353a7bf817d7f79d2d596277caa
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173817"
---
# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Az Azure Active Directory tartományi szolgáltatások által felügyelt tartományok adminisztrációja
Ez a cikk bemutatja, hogyan felügyelheti az Azure Active Directory (AD) tartományi szolgáltatásokkal felügyelt tartományban.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok végrehajtásához szükséges:

1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy az egy helyszíni címtár vagy egy csak felhőalapú címtárral szinkronizálja.
3. **Az Azure AD Domain Services** engedélyezve kell lennie az Azure AD-címtárban. Ha még nem tette, minden ismertetett feladatok végrehajtásával a [a kezdeti lépések útmutatóban](active-directory-ds-getting-started.md).
4. A **tartományhoz csatlakoztatott virtuális gép** , amelyről felügyelheti az Azure AD tartományi szolgáltatásokkal felügyelt tartományban. Ha egy virtuális gép nem rendelkezik, az összes című cikkben ismertetett feladatok végrehajtásával [Windows virtuális gépek csatlakoztatása felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md).
5. A hitelesítő adatait kell egy **felhasználói fiók, az "AAD DC rendszergazdák" csoportba tartozó** a címtárban, a felügyelt tartomány felügyeletéhez.

<br>

## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Felügyeleti feladatokat hajthat végre egy felügyelt tartományon
Az "AAD DC rendszergazdák" csoport tagjai kapnak, amely lehetővé teszi számukra a feladatokat hajthat végre, mint például a felügyelt tartomány jogosultságokkal:

* Gépek csatlakoztatása a felügyelt tartományhoz.
* A beépített GPO konfigurálása a felügyelt tartomány „AADDC számítógépek” és „AADDC felhasználók” tárolóinak esetében.
* DNS-kezelés a felügyelt tartományon.
* Hozzon létre, és egyéni szervezeti egységhez (OU) a felügyelt tartomány felügyeletéhez.
* Felügyeleti hozzáférés szerzése a felügyelt tartományhoz csatlakoztatott számítógépekhez.

## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>Egy felügyelt tartományon nem rendelkezik rendszergazdai jogosultságokkal
A tartomány Microsoft által kezelt, ideértve a tevékenységeket, mint például a javítás, felügyeleti és biztonsági másolatok készítése. A tartomány zárolva van, és nem rendelkezik jogosultságokkal a tartományban lévő bizonyos felügyeleti feladatokat hajthat végre. Az alábbiakban néhány példa a feladatok nem hajtható végre.

* Nem kell a felügyelt tartományhoz tartozó tartományi rendszergazdaként vagy vállalati rendszergazdai jogosultságokkal.
* A felügyelt tartomány sémája nem terjeszthetők ki.
* Nem lehet csatlakozni a távoli asztal használatával felügyelt tartományhoz tartozó tartományvezérlőket.
* A felügyelt tartomány tartományvezérlőket nem lehet hozzáadni.

## <a name="task-1---create-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>1. feladat – tartományhoz csatlakoztatott Windows Server virtuális gép létrehozása távoli felügyeletéhez a felügyelt tartományhoz
Az Azure AD Domain Services felügyelt tartomány a jól ismert az Active Directory felügyeleti eszközök például az Active Directory felügyeleti központ (ADAC) vagy AD PowerShell használatával kezelhetők. A bérlői rendszergazdák nem rendelkezik jogosultságokkal a tartományvezérlők a távoli asztalon keresztül felügyelt tartományon való kapcsolódáshoz. Az "AAD DC rendszergazdák" csoport tagjai felügyelhetik távolról az AD felügyeleti eszközök a felügyelt tartományhoz csatlakozó számítógépről a Windows Server vagy Windows-ügyfél használatával felügyelt tartományok. Felügyeleti eszközök AD a Távoli kiszolgálófelügyelet eszközei (RSAT) a Windows Server és a felügyelt tartományhoz csatlakoztatott ügyfélgépek választható szolgáltatás részeként is telepíthető.

Az első lépés, hogy a Windows Server virtuális gép, amely a felügyelt tartományhoz csatlakozik. Útmutatásért tekintse meg a című cikkben [Windows Server virtuális gép csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Távoli felügyeletéhez a felügyelt tartomány számítógépről (például a Windows 10)
A felügyelt tartomány felügyeletéhez az AAD-DS-ben Ez a cikk egy Windows Servert futtató virtuális gép használati utasításait. Azonban azt is beállíthatja ehhez a Windows ügyfél (például Windows 10-es) virtuális gép használatával.

Is [távoli kiszolgálófelügyelet eszközei (RSAT) telepítése](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) utasításait követve, TechNeten Windows ügyfél virtuális gépen.

## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>2. feladat – telepítés Active Directory felügyeleti eszközök a virtuális gépen
A következő lépéseket az Active Directory-felügyeleti eszközök telepítése a tartományhoz csatlakoztatott virtuális gépen. Tekintse meg a Technet további [telepítéséről és a Távoli kiszolgálófelügyelet eszközei használatával](https://technet.microsoft.com/library/hh831501.aspx).

1. Keresse meg az Azure Portalon. Kattintson a **összes erőforrás** a bal oldali panelen. Keresse meg és kattintson a virtuális gép az 1. feladatban létrehozott.
2. Kattintson a **Connect** gomb az Áttekintés lapon. Egy Remote Desktop Protocol (.rdp) fájlt a rendszer létrehoz és letölt.

    ![Windows virtuális gép eléréséhez](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz. Ha a rendszer kéri, akkor kattintson a **Csatlakozás** gombra. A "AAD DC rendszergazdák" csoportba tartozó felhasználói hitelesítő adatokat használja. Például "bob@domainservicespreview.onmicrosoft.com". A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Kattintson az Igen gombra, vagy továbbra is a csatlakozás folytatásához.
4. A kezdőképernyőről nyissa meg a **Kiszolgálókezelő**. Kattintson a **szerepkörök és szolgáltatások hozzáadása** a Kiszolgálókezelő ablakban középső ablaktábláján.

    ![Indítsa el a Kiszolgálókezelőt a virtuális gépen](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Az a **alapismeretek** lapján a **adja hozzá szerepkörök és szolgáltatások varázsló**, kattintson a **tovább**.

    ![Lap megkezdése előtt](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Az a **telepítési típus** lapon, hagyja a **szerepköralapú vagy szolgáltatásalapú telepítés** lehetőség be van jelölve, majd kattintson **tovább**.

    ![Telepítés típusa lap](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Az a **kiszolgáló kiválasztása** lapon válassza ki az aktuális virtuális gépet a kiszolgálókészletből, és kattintson a **tovább**.

    ![Kiszolgáló kiválasztása lap](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Az a **kiszolgálói szerepkörök** kattintson **tovább**.
9. Az a **funkciók** oldal, ide kattintva bontsa ki a **távoli kiszolgálófelügyelet eszközei** csomópontot, és bontsa ki, majd kattintson a **szerepkör-felügyeleti eszközök** csomópont. Válassza ki **AD DS és AD LDS-eszközök** szolgáltatása szerepkör-felügyeleti eszközök a listából.

    ![Szolgáltatások lapon](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)
10. Az a **megerősítő** kattintson **telepítése** telepítése az AD és az AD LDS-eszközök szolgáltatás a virtuális gépen. Ha a szolgáltatás telepítése sikeresen befejeződött, kattintson az **Bezárás** való kilépéshez a **szerepkörök és szolgáltatások hozzáadása** varázsló.

    ![Megerősítő oldal](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)

## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>3. feladat csatlakozik, és Fedezze fel a felügyelt tartományhoz
Most használhatja a Windows Server AD-felügyeleti eszközök megismerését és a felügyelt tartomány felügyeletéhez.

> [!NOTE]
> A felügyelt tartomány felügyeletéhez a "AAD DC rendszergazdák" csoport tagjának lennie kell.
>
>

1. A kezdőképernyőről kattintson **felügyeleti eszközök**. A felügyeleti eszközök AD, a virtuális gépen telepítve kell megjelennie.

    ![A kiszolgálón telepített felügyeleti eszközök](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Kattintson a **Active Directory felügyeleti központ**.

    ![Active Directory felügyeleti központ](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. Ismerje meg a tartomány, kattintson a bal oldali ablaktáblán (például "contoso100.com") a tartomány nevét. Figyelje meg, hogy a "AADDC számítógépek" és "AADDC felhasználók" nevű, illetve két tárolót.

    ![Az ADAC - nézet tartomány](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)
4. Kattintson a tároló nevű **AADDC felhasználók** minden felhasználó és a felügyelt tartományhoz tartozó csoport megtekintéséhez. Megjelenik a felhasználói fiókok és csoportok az Azure AD-ből a bérlői show fel ebben a tárolóban. Figyelje meg ebben a példában, egy felhasználói fiókot a "Belinszky" nevű felhasználó és a egy "AAD DC rendszergazdák" nevű csoportot érhetők el ebben a tárolóban.

    ![Az ADAC - tartományi felhasználók](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)
5. Kattintson a tároló nevű **AADDC számítógépek** megtekintheti a felügyelt tartományhoz kapcsolódó számítógépekre. Megjelenik egy bejegyzés az aktuális virtuális gép, amely csatolva van a tartományhoz. Minden számítógép, amely az Azure AD tartományi szolgáltatásokat a felügyelt tartományhoz csatlakozó számítógépek fiókjainak a "AADDC számítógépek" tároló tárolja.

    ![Az ADAC - tartományhoz csatlakozó számítógépek](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Kapcsolódó tartalom
* [Az Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [A Windows Server virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-windows-vm.md)
* [Távoli kiszolgálófelügyelet eszközeinek telepítése](https://technet.microsoft.com/library/hh831501.aspx)
