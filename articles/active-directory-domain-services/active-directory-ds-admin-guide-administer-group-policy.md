---
title: 'Az Azure Active Directory Domain Services: A felügyelt tartományok Csoportházirend-kezelése |} A Microsoft Docs'
description: Csoportházirend felügyelete az Azure Active Directory Domain Services által felügyelt tartományokhoz
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: 31805f7588a58eb1682ce50fd55dd14b967d6099
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50158340"
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Az Azure AD tartományi szolgáltatásokkal felügyelt tartományban a Csoportházirend-kezelése
Az Azure Active Directory Domain Services beépített csoportházirend-objektumok (GPO) tartalmazza a "AADDC felhasználók" és "AADDC számítógépek" tárolók. Testre szabhatja a beépített csoportházirend-objektumokat a csoportházirend a felügyelt tartományon. Az "AAD DC rendszergazdák" csoport tagjai ezenkívül a felügyelt tartomány hozhat létre saját egyéni szervezeti egységekhez. Azok is egyéni csoportházirend-objektumok létrehozásához, és hozzárendelheti őket az alábbi egyéni szervezeti egységek. Az "AAD DC rendszergazdák" csoportba tartozó felhasználók kapnak a felügyelt tartomány csoportházirend felügyeleti jogosultságokkal.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok elvégzéséhez szüksége:

1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy az egy helyszíni címtár vagy egy csak felhőalapú címtárral szinkronizálja.
3. **Az Azure AD Domain Services** engedélyezve kell lennie az Azure AD-címtárban. Ha még nem tette, minden ismertetett feladatok végrehajtásával a [a kezdeti lépések útmutatóban](active-directory-ds-getting-started.md).
4. A **tartományhoz csatlakoztatott virtuális gép** , amelyről felügyelheti az Azure AD tartományi szolgáltatásokkal felügyelt tartományban. Ha egy virtuális gép nem rendelkezik, az összes című cikkben ismertetett feladatok végrehajtásával [Windows virtuális gépek csatlakoztatása felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md).
5. A hitelesítő adatait kell egy **felhasználói fiók, az "AAD DC rendszergazdák" csoportba tartozó** a címtárban, a felügyelt tartomány felügyeletéhez a csoportházirend.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>1. feladat – távoli felügyeletéhez a felügyelt tartományhoz tartozó csoportházirend tartományhoz csatlakoztatott virtuális gép kiépítése
Az Azure AD Domain Services felügyelt tartomány távolról a jól ismert az Active Directory felügyeleti eszközök például az Active Directory felügyeleti központ (ADAC) vagy AD PowerShell segítségével is felügyelhetők. Hasonlóképpen a felügyelt tartományhoz tartozó csoportházirend felügyelhetők távolról a csoportházirend felügyeleti eszközök használatával.

Az Azure AD-címtár rendszergazdái nem rendelkezik jogosultságokkal a tartományvezérlők a távoli asztalon keresztül felügyelt tartományon való kapcsolódáshoz. Az "AAD DC rendszergazdák" csoport tagjai felügyelhetik a csoportházirend a felügyelt tartományok távolról. A felügyelt tartományhoz csatlakoztatott Windows Server-ügyfél számítógépen használhatják a csoportházirend-eszközök. A csoportházirend eszközei a Csoportházirend kezelése a Windows Server és a felügyelt tartományhoz csatlakoztatott ügyfélgépek választható szolgáltatás részeként is telepíthető.

Az első feladatra, hogy a felügyelt tartományhoz csatlakozó Windows Server virtuális gép kiépítése. Útmutatásért tekintse meg a című cikkben [Windows Server virtuális gép csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>2. feladat – a virtuális gép telepítése csoportházirend-eszközök
A következő lépésekkel telepítse a csoport házirend felügyeleti eszközeit a tartományhoz csatlakoztatott virtuális gépen.

1. Keresse meg az Azure Portalon. Kattintson a **összes erőforrás** a bal oldali panelen. Keresse meg és kattintson a virtuális gép az 1. feladatban létrehozott.
2. Kattintson a **Connect** gomb az Áttekintés lapon. Egy Remote Desktop Protocol (.rdp) fájlt a rendszer létrehoz és letölt.

    ![Windows virtuális gép eléréséhez](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz. Ha a rendszer kéri, akkor kattintson a **Csatlakozás** gombra. Bejelentkezés a parancssorba az "AAD DC rendszergazdák" csoportba tartozó felhasználói hitelesítő adatokat használja. Például, hogy használja "bob@domainservicespreview.onmicrosoft.com" Ebben az esetben a. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Kattintson az Igen gombra, vagy továbbra is a csatlakozás folytatásához.
4. A kezdőképernyőről nyissa meg a **Kiszolgálókezelő**. Kattintson a **szerepkörök és szolgáltatások hozzáadása** a Kiszolgálókezelő ablakban középső ablaktábláján.

    ![Indítsa el a Kiszolgálókezelőt a virtuális gépen](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Az a **alapismeretek** lapján a **adja hozzá szerepkörök és szolgáltatások varázsló**, kattintson a **tovább**.

    ![Lap megkezdése előtt](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Az a **telepítési típus** lapon, hagyja a **szerepköralapú vagy szolgáltatásalapú telepítés** lehetőség be van jelölve, majd kattintson **tovább**.

    ![Telepítés típusa lap](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Az a **kiszolgáló kiválasztása** lapon válassza ki az aktuális virtuális gépet a kiszolgálókészletből, és kattintson a **tovább**.

    ![Kiszolgáló kiválasztása lap](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Az a **kiszolgálói szerepkörök** kattintson **tovább**. Ezen a lapon azt kihagyása, mert azt nem telepíti minden szerepkört a kiszolgálón.
9. Az a **funkciók** lapon válassza ki a **Csoportházirend kezelése** funkció.

    ![Szolgáltatások lapon](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. Az a **megerősítő** kattintson **telepítése** telepítheti a Csoportházirend kezelése szolgáltatást a virtuális gépen. Ha a szolgáltatás telepítése sikeresen befejeződött, kattintson az **Bezárás** való kilépéshez a **szerepkörök és szolgáltatások hozzáadása** varázsló.

    ![Megerősítő oldal](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>3. feladat – indítsa el a Csoportházirend kezelése konzol Csoportházirend-kezelése
A tartományhoz csatlakoztatott virtuális gépen a Csoportházirend kezelése konzol segítségével felügyelheti a csoportházirend a felügyelt tartományon.

> [!NOTE]
> A felügyelt tartományon a Csoportházirend-kezelése a "AAD DC rendszergazdák" csoport tagjának lennie kell.
>
>

1. A kezdőképernyőről kattintson **felügyeleti eszközök**. Megtekintheti a **Csoportházirend kezelése** konzol telepítése a virtuális gépen.

    ![Indítsa el a Csoportházirend kezelése](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. Kattintson a **Csoportházirend kezelése** , indítsa el a Csoportházirend kezelése konzolt.

    ![Csoportházirend-konzol](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>4. feladat – beépített csoportházirend-objektumok testreszabása
Nincsenek két beépített csoportházirend-objektumok (GPO) – egyet-egyet a a felügyelt tartomány "AADDC számítógépek" és "AADDC felhasználók" tárolókat. Testre szabhatja a csoportházirend-objektumokat a csoportházirend a felügyelt tartományon.

1. Az a **Csoportházirend kezelése** konzol, ide kattintva bontsa ki a **erdő: contoso100.com** és **tartományok** lásd: a csoportházirendek a felügyelt tartományhoz tartozó csomópontokat.

    ![Beépített csoportházirend-objektumok](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. Ezek beépített csoportházirend-objektumok csoportházirend konfigurálása a felügyelt tartomány testre szabhatja. Kattintson a jobb gombbal a csoportházirend-Objektumot, és kattintson a **szerkesztése...**  szabhatja testre a beépített csoportházirend-Objektumot. A Helyicsoportházirend-szerkesztő konfigurációs eszköz lehetővé teszi, hogy testre szabhatja a csoportházirend-Objektumot.

    ![Szerkessze a beépített GPO](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. Most már használhatja a **Csoportházirendkezelés-szerkesztő** konzol a beépített csoportházirend-objektum szerkesztéséhez. Az alábbi képernyőképen például bemutatja, hogyan szabhatja testre a beépített "AADDC számítógépek" csoportházirend-Objektumot.

    ![Csoportházirend-objektum testreszabása](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>5. feladat – egy egyéni szabályzat objektum (GPO) létrehozása
Hozzon létre, vagy saját egyéni csoportházirend-objektumok importálása. A felügyelt tartományra a létrehozott egyéni szervezeti egyéni csoportházirend-objektumok is csatolható. Egyéni szervezeti egységek létrehozásával kapcsolatos további információkért lásd: [egy egyéni szervezeti egység létrehozása egy felügyelt tartományon](active-directory-ds-admin-guide-create-ou.md).

> [!NOTE]
> A felügyelt tartományon a Csoportházirend-kezelése a "AAD DC rendszergazdák" csoport tagjának lennie kell.
>
>

1. Az a **Csoportházirend kezelése** konzol, kattintson az egyéni szervezeti egység (OU) kiválasztásához. Kattintson a jobb gombbal a szervezeti Egységet, és kattintson a **csoportházirend-objektum létrehozása ebben a tartományban, és hivatkozás létrehozása itt...** .

    ![Hozzon létre egy egyéni csoportházirend-objektum](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Adjon meg egy nevet az új csoportházirend-objektum, és kattintson **OK**.

    ![Adja meg a csoportházirend-objektum nevét](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Egy új csoportházirend-objektum létrejött, és az egyéni szervezeti Egységhez kapcsolódik. Kattintson a jobb gombbal a csoportházirend-Objektumot, és kattintson a **szerkesztése...**  a menüből.

    ![Az újonnan létrehozott csoportházirend-objektum](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. Az újonnan létrehozott csoportházirend-objektum segítségével testre szabhatja a **Csoportházirendkezelés-szerkesztő**.

    ![Új csoportházirend-objektum testreszabása](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


További információk [Csoportházirend kezelése konzol](https://technet.microsoft.com/library/cc753298.aspx) érhető el a TechNet webhelyén.

## <a name="related-content"></a>Kapcsolódó tartalom
* [Az Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [A Windows Server virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-windows-vm.md)
* [Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete](active-directory-ds-admin-guide-administer-domain.md)
* [Csoportházirend kezelése konzol](https://technet.microsoft.com/library/cc753298.aspx)
