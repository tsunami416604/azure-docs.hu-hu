---
title: 'Az Azure Active Directory tartományi szolgáltatások: Egy felügyelt tartomány felügyelete |} Microsoft Docs'
description: Azure Active Directory tartományi szolgáltatások felügyelt tartományok felügyelete
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: d4fdbc75-3e6b-4e20-8494-5dcc3bf2220a
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2017
ms.author: maheshu
ms.openlocfilehash: 6a4e25dd3f819ad4f0fee7846e87df2202f5227f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210618"
---
# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Az Azure Active Directory tartományi szolgáltatások által felügyelt tartományok adminisztrációja
Ez a cikk bemutatja, hogyan felügyelheti az Azure Active Directory (AD) tartományi szolgáltatások által felügyelt tartományokhoz.

## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok elvégzéséhez szüksége:

1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy egy helyszíni címtár vagy egy csak felhőalapú directory szinkronizálva.
3. **Azure AD tartományi szolgáltatások** az Azure AD-címtár engedélyezni kell. Ha még nem tette meg, az összes ismertetett feladatok végrehajtásával a [első lépések útmutató](active-directory-ds-getting-started.md).
4. A **tartományhoz csatlakoztatott virtuális gép** , amelyből az Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete. Ha egy virtuális gép nem rendelkezik, a című cikkben ismertetett összes feladatok végrehajtásával [egy Windows rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md).
5. A hitelesítő adatait kell egy **az "AAD DC rendszergazdák" csoportba tartozó felhasználói fiók** a könyvtárban, a felügyelt tartományok felügyeletéhez.

<br>

## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>Felügyeleti feladatokat hajthat végre egy felügyelt tartomány
A "AAD DC rendszergazdák" csoportba kapnak a felügyelt tartományra, amely lehetővé teszi a feladatok elvégzéséhez jogosultságokkal:

* Számítógépek csatlakoztatása a felügyelt tartományra.
* A beépített GPO konfigurálása a felügyelt tartomány „AADDC számítógépek” és „AADDC felhasználók” tárolóinak esetében.
* DNS-kezelés a felügyelt tartományon.
* Hozzon létre, és egyéni szervezeti egységben (OU) a felügyelt tartományra felügyeletéhez.
* Felügyeleti hozzáférés szerzése a felügyelt tartományhoz csatlakoztatott számítógépekhez.

## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>Ön nem rendelkezik egy felügyelt tartományi rendszergazdai jogosultságokkal
A tartomány a Microsoft, ideértve a tevékenységeket, például a javítás, figyelés és a biztonsági mentés kezeli. Ezért a tartomány zárolva van, és nincs bizonyos felügyeleti feladatok elvégzésére a tartományon. Néhány példa a feladatok nem hajtható végre a rendszer alatt.

* Nem kapnak a felügyelt tartomány számára a tartományi rendszergazda vagy a vállalati rendszergazdai jogosultságokkal.
* A felügyelt tartományra sémája nem bővíthető.
* Nem lehet csatlakozni a távoli asztal használata felügyelt tartomány számára tartományvezérlők.
* Tartományvezérlők nem adható hozzá a felügyelt tartományra.

## <a name="task-1---provision-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>1. feladat – egy tartományhoz csatlakoztatott Windows Server rendszerű virtuális gép távoli felügyelete a felügyelt tartományra
Az Azure AD tartományi szolgáltatások felügyelt tartományok ismeri az Active Directory felügyeleti eszközök például az Active Directory felügyeleti központ (ADAC) vagy AD PowerShell segítségével kezelhetők. A bérlői rendszergazdák nem jogosult csatlakozni a távoli asztalon keresztül a felügyelt tartományra tartományvezérlők. Ezért a "AAD DC rendszergazdák" csoport tagjai felügyelhetik felügyelt tartományok távolról az AD felügyeleti eszközök a Windows Server-ügyfél számítógépről, amely a felügyelt tartomány tagja. AD felügyeleti eszközök a Távoli kiszolgálófelügyelet eszközei (RSAT) választható szolgáltatás Windows Server és a felügyelt tartományhoz csatlakozó ügyfélgépek részeként telepíthető.

Az első lépés, hogy állítson be egy Windows Server virtuális gépet, amely a felügyelt tartományhoz csatlakozik. Útmutatásért tekintse meg a című cikk [Windows Server virtuális gép csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-windows-vm.md).

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>Távoli felügyeletéhez a felügyelt tartományra ügyfélszámítógépről (például a Windows 10)
Ez a cikk a Windows Server virtuális gép használata utasításait az AAD-DS felügyeletéhez felügyelt tartomány. Akkor is kiválaszthatja, ehhez a Windows ügyfél (például a Windows 10) virtuális gép használata.

Is [telepíti a Távoli kiszolgálófelügyelet eszközei (RSAT)](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) cikk utasításait követve a TechNet Windows ügyfél virtuális gépen.

## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>2. feladat – telepítés Active Directory felügyeleti eszközök a virtuális gépen
A következő lépésekkel telepítse az Active Directory-felügyeleti eszközök a tartományhoz csatlakoztatott virtuális gépre. Tekintse meg a Technet további [telepítéséről és a Távoli kiszolgálófelügyelet eszközeivel](https://technet.microsoft.com/library/hh831501.aspx).

1. Keresse meg az Azure-portálon. Kattintson a **összes erőforrás** a bal oldali panelen. Keresse meg, és kattintson az 1. feladatban létrehozott virtuális gépre.
2. Kattintson a **Connect** – Áttekintés lap gombjára. A távoli asztal protokoll (RDP) fájl létrehozása, és le.

    ![Windows virtuális géphez](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz. Ha a rendszer kéri, akkor kattintson a **Csatlakozás** gombra. Bejelentkezés a parancssorba az "AAD DC rendszergazdák" csoportba tartozó felhasználói hitelesítő adatokat használja. Például használjuk "bob@domainservicespreview.onmicrosoft.com" esetünkben. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Kattintson az Igen gombra, vagy folytassa a kapcsolat.
4. A kezdőképernyőről nyissa meg a **Kiszolgálókezelő**. Kattintson a **szerepkörök és szolgáltatások hozzáadása** a Kiszolgálókezelő ablakban központi panelén.

    ![Indítsa el a Kiszolgálókezelőt a virtuális gépen](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Az a **előkészületek** oldalán a **hozzáadása szerepkörök és szolgáltatások varázsló**, kattintson a **következő**.

    ![Mielőtt elkezdené lap](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Az a **telepítési típus** lapon, hagyja a **szerepköralapú vagy szolgáltatásalapú telepítés** beállítás be van jelölve, és kattintson **következő**.

    ![Telepítés típusa lap](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Az a **kiszolgáló kiválasztása** lapon válassza ki az aktuális virtuális gépet a kiszolgálókészletből, és kattintson a **következő**.

    ![Kiszolgáló kiválasztása lap](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Az a **kiszolgálói szerepkörök** kattintson **következő**. Azt e lap kihagyása, mert jelenleg nem telepít szerepköröket a kiszolgálón.
9. Az a **szolgáltatások** lap gombra, bontsa ki a **távoli kiszolgálófelügyelet eszközei** csomópontra majd bontsa ki a **szerepkör-felügyeleti eszközök** csomópont. Válassza ki **AD DS és AD LDS-eszközök** szolgáltatás szerepkör-felügyeleti eszközök a listából.

    ![Szolgáltatások lapon](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)
10. Az a **megerősítő** kattintson **telepítése** telepítése az AD és az AD LDS-eszközök szolgáltatás a virtuális gépen. Ha a szolgáltatás telepítése sikeresen befejeződött, kattintson **Bezárás** való kilépéshez a **szerepkörök és szolgáltatások hozzáadása** varázsló.

    ![Jóváhagyás lap](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)

## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>A feladat 3 - csatlakozni, és vizsgálja meg a felügyelt tartományra
Most, hogy az AD-felügyeleti eszközök telepítve vannak a tartományhoz csatlakoztatott virtuális gép, ezekkel az eszközökkel vizsgálatát, és felügyelheti a felügyelt tartományra is használjuk.

> [!NOTE]
> Felügyelheti a felügyelt tartományra "AAD DC rendszergazdák" csoport tagjának lennie kell.
>
>

1. A kezdőképernyőről kattintson **felügyeleti eszközök**. Meg kell jelennie a virtuális gépen telepített AD felügyeleti eszközök.

    ![A kiszolgálón telepített felügyeleti eszközök](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. Kattintson a **Active Directory felügyeleti központ**.

    ![Active Directory felügyeleti központ](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. A tartomány megismeréséhez kattintson a tartomány nevét, a bal oldali ablaktáblán (például "contoso100.com"). Figyelje meg, "AADDC számítógépek" és "AADDC felhasználók" nevezett, illetve két tárolók.

    ![Az ADAC - nézet tartomány](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)
4. Kattintson a tároló neve **AADDC felhasználók** összes felhasználók és csoportok felügyelt tartományhoz tartozó megjelenítéséhez. Felhasználói fiókokat kell megjelennie, és az Azure ad-csoportok a bérlői megjelenítése fel ebben a tárolóban. Figyelje meg ebben a példában, a "Belinszky" nevű felhasználó és a "AAD DC rendszergazdák" nevű csoport felhasználói fiók érhetők el ebben a tárolóban.

    ![Az ADAC - tartományi felhasználók](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)
5. Kattintson a tároló neve **AADDC számítógépek** felügyelt tartományhoz csatlakoztatott számítógépek megtekintéséhez. Az aktuális virtuális gép, amely csatlakozik a tartományhoz tartozó bejegyzés kell megjelennie. Minden olyan számítógép, az Azure AD tartományi szolgáltatások által kezelt tartomány tartományhoz csatlakoztatott számítógépek fiókjainak a AADDC számítógépfiókokra vannak tárolva.

    ![Az ADAC - tartományhoz csatlakoztatott számítógépek](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [Windows Server virtuális gép csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-windows-vm.md)
* [Távoli kiszolgálófelügyelet eszközeinek telepítése](https://technet.microsoft.com/library/hh831501.aspx)
