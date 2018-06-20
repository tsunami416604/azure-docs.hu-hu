---
title: 'Az Azure Active Directory tartományi szolgáltatások: A csoportházirend a felügyelt tartományok felügyelete |} Microsoft Docs'
description: Csoportházirend felügyelete az Azure Active Directory tartományi szolgáltatások által felügyelt tartományok
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: 3f49e4ac0073c81a6e55e6653acc7c6531989379
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36212240"
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Csoportházirend a egy Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete
Az Azure Active Directory tartományi szolgáltatások beépített csoportházirend-objektumok (GPO-k) a "AADDC felhasználók" és "AADDC számítógépek" tárolók magában foglalja. Testre szabhatja a beépített csoportházirend-objektumokat a csoportházirend a kezelt tartományban. A "AAD DC rendszergazdák" csoportba, a felügyelt tartományra hozhat létre a saját egyéni szervezeti egységekhez. Akkor is egyéni csoportházirend-objektumok létrehozása, és kapcsolja őket a egyéni szervezeti egységekhez. A "AAD DC rendszergazdák" csoportba tartozó felhasználók kapnak a felügyelt tartományra csoportházirend felügyeleti jogosultságokkal.

## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok elvégzéséhez szüksége:

1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy egy helyszíni címtár vagy egy csak felhőalapú directory szinkronizálva.
3. **Azure AD tartományi szolgáltatások** az Azure AD-címtár engedélyezni kell. Ha még nem tette meg, az összes ismertetett feladatok végrehajtásával a [első lépések útmutató](active-directory-ds-getting-started.md).
4. A **tartományhoz csatlakoztatott virtuális gép** , amelyből az Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete. Ha egy virtuális gép nem rendelkezik, a című cikkben ismertetett összes feladatok végrehajtásával [egy Windows rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md).
5. A hitelesítő adatait kell egy **az "AAD DC rendszergazdák" csoportba tartozó felhasználói fiók** a könyvtárban, a felügyelt tartományok csoportházirend felügyeletéhez.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>1. feladat – rendszerű távoli felügyelete a felügyelt tartomány csoportházirend tartományhoz csatlakoztatott virtuális gép
Az Azure AD tartományi szolgáltatások felügyelt tartományok kezelheti távolról már ismerős eszközökkel Active Directory felügyeleti például az Active Directory felügyeleti központ (ADAC) vagy AD PowerShell segítségével. Hasonlóképpen a felügyelt tartomány számára a csoportházirend használatával lehet felügyelni távolról a csoportházirend-felügyeleti eszközök.

Az Azure AD-címtár rendszergazdái nem jogosult csatlakozni a távoli asztalon keresztül a felügyelt tartományra tartományvezérlők. A "AAD DC rendszergazdák" csoportba felügyelheti a csoportházirend felügyelt tartományok távolról. Csoportházirend eszközök használhatnak ügyfélszámítógépen Windows Server vagy a felügyelt tartományhoz csatlakozik. A csoportházirend eszközei is telepíthető a Csoportházirend kezelése a Windows Server és a felügyelt tartományhoz csatlakozó ügyfélgépek választható szolgáltatás részeként.

Az első feladata a Windows Server rendszerű virtuális gép, amely a felügyelt tartományhoz csatlakozik. Útmutatásért tekintse meg a című cikk [Windows Server virtuális gép csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>2. feladat – a virtuális gépen a telepítés csoportházirend eszközök
A következő lépésekkel telepítse a csoport házirend felügyeleti eszközeit a tartományhoz csatlakoztatott virtuális gépen.

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
9. Az a **szolgáltatások** lapon jelölje be a **csoportházirend-kezelő** szolgáltatás.

    ![Szolgáltatások lapon](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. Az a **megerősítő** kattintson **telepítése** telepítheti a Csoportházirend kezelése szolgáltatást a virtuális gépen. Ha a szolgáltatás telepítése sikeresen befejeződött, kattintson **Bezárás** való kilépéshez a **szerepkörök és szolgáltatások hozzáadása** varázsló.

    ![Jóváhagyás lap](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>3. feladat – indítsa el a Csoportházirend kezelése konzol Csoportházirend felügyelete
A tartományhoz csatlakoztatott virtuális gépen a Csoportházirend kezelése konzol segítségével felügyelheti a csoportházirend a kezelt tartományban.

> [!NOTE]
> Csoportházirend a kezelt tartomány felügyelete a "AAD DC rendszergazdák" csoport tagjának lennie kell.
>
>

1. A kezdőképernyőről kattintson **felügyeleti eszközök**. Megjelenik a **csoportházirend-kezelő** konzol telepítve legyen a virtuális gépen.

    ![Indítsa el a Csoportházirend kezelése](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. Kattintson a **csoportházirend-kezelő** a Csoportházirend kezelése konzol elindítása.

    ![Csoportházirend-konzol](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>4. feladat – beépített csoportházirend-objektumok testreszabása
Két beépített csoportházirend-objektumok (GPO) – egyet-egyet a "AADDC számítógépek" és "AADDC felhasználók" tárolók a kezelt tartományban van. Testre szabhatja a csoportházirend-objektumokat a csoportházirend a kezelt tartományban.

1. Az a **csoportházirend-kezelő** konzol gombra, bontsa ki a **erdő: contoso100.com** és **tartományok** lásd: a csoportházirendek a felügyelt tartományok csomópontok.

    ![Beépített csoportházirend-objektumok](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. Testre szabhatja a beépített csoportházirend-objektumokat a felügyelt tartományok csoport házirendek konfigurálásához. Kattintson a jobb gombbal a csoportházirend-Objektumot, és kattintson a **szerkesztése...**  testreszabása a beépített csoportházirend-objektum. A Helyicsoportházirend-szerkesztő konfigurációs eszköz lehetővé teszi testre szabhatja a csoportházirend-objektum.

    ![Beépített csoportházirend-objektum szerkesztéséhez](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. Ezután már használhatja a **Csoportházirendkezelés-szerkesztő** konzol a beépített csoportházirend-objektum szerkesztéséhez. Például az alábbi képernyőfelvételen látható a beépített "AADDC számítógépek" csoportházirend-objektum testreszabása.

    ![Csoportházirend-objektum testreszabása](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>5. feladat – hozzon létre egy egyéni házirend objektum (GPO)
Hozzon létre, vagy a saját egyéni csoportházirend-objektumok importálása. A felügyelt tartományok létrehozott egyéni szervezeti is kapcsolja egyéni csoportházirend-objektumokat. Egyéni szervezeti egységek létrehozásáról további információk: [hozzon létre egy egyéni szervezeti felügyelt tartomány](active-directory-ds-admin-guide-create-ou.md).

> [!NOTE]
> Csoportházirend a kezelt tartomány felügyelete a "AAD DC rendszergazdák" csoport tagjának lennie kell.
>
>

1. Az a **csoportházirend-kezelő** konzol, jelölje be az egyéni szervezeti egység (OU). Kattintson a jobb gombbal a szervezeti Egységet, és kattintson a **egy csoportházirend-objektum létrehozása ebben a tartományban, és hivatkozás létrehozása itt...** .

    ![Egy egyéni csoportházirend-objektum létrehozása](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Adjon meg egy nevet az új csoportházirend-Objektumot, és kattintson **OK**.

    ![Adja meg a csoportházirend-objektum nevét](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Egy új csoportházirend-objektum létrehozása és az egyéni szervezeti Egységhez kapcsolódik. Kattintson a jobb gombbal a csoportházirend-Objektumot, és kattintson a **szerkesztése...**  a menüből.

    ![Az újonnan létrehozott GPO](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. Az újonnan létrehozott csoportházirend-objektum használatával testre szabhatja a **Csoportházirendkezelés-szerkesztő**.

    ![Új csoportházirend-objektum testreszabása](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


További információk [Csoportházirend kezelése konzol](https://technet.microsoft.com/library/cc753298.aspx) a TechNet webhelyen érhető el.

## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [Windows Server virtuális gép csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-windows-vm.md)
* [Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete](active-directory-ds-admin-guide-administer-domain.md)
* [Csoportházirend kezelése konzol](https://technet.microsoft.com/library/cc753298.aspx)
