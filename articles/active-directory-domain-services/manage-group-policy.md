---
title: 'Azure Active Directory Domain Services: Csoportházirend felügyelete | Microsoft Docs'
description: Csoportházirend felügyelete Azure Active Directory Domain Services felügyelt tartományokban
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: c7b32885fdb3cf4f3e584c916d6b234fff54bfc4
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234031"
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Csoportházirend felügyelete Azure AD Domain Services felügyelt tartományon
A Azure Active Directory Domain Services beépített Csoportházirend objektumokat (GPO-kat) tartalmaz a "AADDC Users" és a "AADDC Computers" tárolók számára. Ezeket a beépített csoportházirend-objektumokat testreszabhatja a felügyelt tartomány Csoportházirend konfigurálásához. Emellett az "HRE DC-rendszergazdák" csoport tagjai létrehozhatják saját egyéni szervezeti egységeket a felügyelt tartományban is. Emellett egyéni csoportházirend-objektumokat is létrehozhatnak, és összekapcsolhatják ezeket az egyéni szervezeti egységeket. Az "HRE DC rendszergazdák" csoportba tartozó felhasználók Csoportházirend felügyeleti jogosultságokat kapnak a felügyelt tartományon.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben felsorolt feladatok elvégzéséhez a következőkre lesz szüksége:

1. Érvényes **Azure-előfizetés**.
2. Egy **Azure ad-címtár** – szinkronizálva van egy helyszíni címtárral vagy egy csak felhőalapú címtárral.
3. **Azure ad Domain Services** engedélyezni kell az Azure ad-címtárat. Ha még nem tette meg, kövesse az [első lépések útmutatóban](create-instance.md)ismertetett összes feladatot.
4. Egy **tartományhoz csatlakoztatott virtuális gép** , amelyről felügyeli a Azure ad Domain Services felügyelt tartományt. Ha nem rendelkezik ilyen virtuális géppel, hajtsa végre a [Windows rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md)című cikkben ismertetett lépéseket.
5. Szüksége lesz egy olyan felhasználói fiók hitelesítő adataira, amely a címtárban található **"HRE DC Administrators" csoportjába tart** , a felügyelt tartomány csoportházirend felügyeletéhez.

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>1\. feladat – tartományhoz csatlakoztatott virtuális gép kiépítése a felügyelt tartomány Csoportházirend távoli felügyeletéhez
Azure AD Domain Services felügyelt tartományok távolról kezelhetők a jól ismert Active Directory felügyeleti eszközök, például a Active Directory felügyeleti központ (ADAC) vagy az AD PowerShell használatával. Hasonlóképpen, a felügyelt tartomány Csoportházirend a Csoportházirend felügyeleti eszközök használatával távolról is felügyelhetők.

Az Azure AD-címtár rendszergazdái nem rendelkeznek jogosultságokkal a felügyelt tartományhoz tartozó tartományvezérlőkön való kapcsolódáshoz Távoli asztalon keresztül. Az "HRE DC rendszergazdák" csoport tagjai távolról felügyelhetik Csoportházirend felügyelt tartományokhoz. Használhatják Csoportházirend eszközöket a felügyelt tartományhoz csatlakoztatott Windows Server-vagy ügyfélszámítógép-számítógépen. Csoportházirend eszközök a felügyelt tartományhoz csatlakoztatott Windows Server-és ügyfélszámítógépeken a Csoportházirend felügyelet választható funkciójának részeként telepíthetők.

Az első feladat a felügyelt tartományhoz csatlakoztatott Windows Server rendszerű virtuális gép kiépítése. Útmutatásért tekintse meg a [Windows Server rendszerű virtuális gép csatlakoztatása Azure ad Domain Services felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md)című cikket.

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>2\. feladat – Csoportházirend eszközök telepítése a virtuális gépre
A következő lépések végrehajtásával telepítse a Csoportházirend felügyeleti eszközöket a tartományhoz csatlakoztatott virtuális gépre.

1. Navigáljon a Azure Portal. Kattintson az **összes erőforrás** elemre a bal oldali panelen. Keresse meg és kattintson arra a virtuális gépre, amelyet az 1. feladatban hozott létre.
2. Kattintson a **kapcsolat** gombra az Áttekintés lapon. A rendszer létrehoz és letölt egy RDP protokoll (. rdp) fájlt.

    ![Kapcsolódás a Windows rendszerű virtuális géphez](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz. Ha a rendszer kéri, akkor kattintson a **Csatlakozás** gombra. A bejelentkezési kérésben használja az "HRE DC rendszergazdák" csoportba tartozó felhasználó hitelesítő adatait. Például a "bob@domainservicespreview.onmicrosoft.com"-t használjuk a esetünkben. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A kapcsolódás folytatásához kattintson az igen vagy a Folytatás gombra.
4. A kezdőképernyőn nyissa meg a **Kiszolgálókezelő alkalmazást**. Kattintson a **szerepkörök és szolgáltatások hozzáadása** elemre a Kiszolgálókezelő ablakának központi ablaktábláján.

    ![A Kiszolgálókezelő elindítása a virtuális gépen](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. A **szerepkörök és szolgáltatások hozzáadása varázsló**alapismeretek lapján kattintson a **tovább**gombra.

    ![Mielőtt elkezdené a lapot](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. A **telepítés típusa** lapon hagyja bejelölve a **szerepköralapú vagy a szolgáltatáson alapuló telepítési** beállítást, majd kattintson a **tovább**gombra.

    ![Telepítés típusa lap](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. A **kiszolgáló kiválasztása** lapon válassza ki az aktuális virtuális gépet a kiszolgáló készletből, és kattintson a **tovább**gombra.

    ![Kiszolgáló kiválasztása lap](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. A **kiszolgálói szerepkörök** lapon kattintson a **tovább**gombra. Ezt az oldalt kihagyjuk, mivel nem telepítünk szerepköröket a kiszolgálón.
9. A **szolgáltatások** lapon válassza ki a **csoportházirend felügyeleti** funkciót.

    ![Szolgáltatások lap](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. A **jóváhagyás** lapon kattintson a **telepítés** gombra a csoportházirend felügyeleti szolgáltatás virtuális gépen való telepítéséhez. Ha a szolgáltatás telepítése sikeresen befejeződik, kattintson a **Bezárás** gombra a **szerepkörök és szolgáltatások hozzáadása** varázslóból való kilépéshez.

    ![Megerősítést kérő oldal](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>3\. feladat – a Csoportházirend felügyeleti konzol elindítása Csoportházirend felügyeletéhez
A tartományhoz csatlakoztatott virtuális gépen a Csoportházirend felügyeleti konzol használatával felügyelheti Csoportházirend a felügyelt tartományon.

> [!NOTE]
> Az "HRE DC rendszergazdák" csoport tagjának kell lennie a felügyelt tartomány Csoportházirend felügyeletéhez.
>
>

1. A kezdőképernyőn kattintson a **felügyeleti eszközök**elemre. A virtuális gépre telepített **csoportházirend felügyeleti** konzolnak kell megjelennie.

    ![Csoportházirend-kezelés elindítása](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. A Csoportházirend felügyeleti konzol elindításához kattintson a **csoportházirend felügyelet** elemre.

    ![Csoportházirend konzol](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>4\. feladat – beépített Csoportházirend objektumok testreszabása
Két beépített Csoportházirend objektum (GPO) létezik – egyet a felügyelt tartomány "AADDC számítógépek" és "AADDC Users" tárolói számára. A csoportházirend-objektumok testreszabásával konfigurálhatja a csoportházirendet a felügyelt tartományon.

1. A **csoportházirend felügyeleti** konzolon kattintson ide az **erdő: contoso100.com** és **tartományok** csomópontok kibontásához a felügyelt tartományhoz tartozó csoportházirendek megtekintéséhez.

    ![Beépített csoportházirend-objektumok](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. Ezeket a beépített csoportházirend-objektumokat testreszabhatja a felügyelt tartományhoz tartozó csoportházirendek konfigurálásához. Kattintson a jobb gombbal a csoportházirend-objektumra, majd kattintson a **Szerkesztés...** elemre a beépített csoportházirend-objektum testreszabásához. A Csoportházirend Configuration Editor eszköz lehetővé teszi a csoportházirend-objektum testreszabását.

    ![Beépített csoportházirend-objektum szerkesztése](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. Mostantól a **csoportházirend-felügyeleti szerkesztő** -konzollal szerkesztheti a beépített csoportházirend-objektumot. A következő képernyőkép például bemutatja, hogyan szabhatja testre a beépített "AADDC számítógépek" csoportházirend-objektumot.

    ![Csoportházirend-objektum testreszabása](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>5\. feladat – egyéni Csoportházirend objektum (GPO) létrehozása
Létrehozhat vagy importálhat saját egyéni csoportházirend-objektumokat. Egyéni csoportházirend-objektumokat is csatolhat a felügyelt tartományban létrehozott egyéni szervezeti egységekhez. Az egyéni szervezeti egységek létrehozásával kapcsolatos további információkért lásd: [Egyéni szervezeti egység létrehozása felügyelt tartományon](create-ou.md).

> [!NOTE]
> Az "HRE DC rendszergazdák" csoport tagjának kell lennie a felügyelt tartomány Csoportházirend felügyeletéhez.
>
>

1. A **csoportházirend felügyeleti** konzolon kattintson az egyéni szervezeti egység (OU) kiválasztásához. Kattintson a jobb gombbal a szervezeti egységre, majd kattintson **a csoportházirend-objektum létrehozása ebben a tartományban, és hivatkozás itt..** . elemre.

    ![Egyéni csoportházirend-objektum létrehozása](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. Adja meg az új csoportházirend-objektum nevét, majd kattintson **az OK**gombra.

    ![Adja meg a GPO nevét](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. Létrejön egy új csoportházirend-objektum, amely az egyéni szervezeti egységhez van csatolva. Kattintson a jobb gombbal a csoportházirend-objektumra, majd kattintson a menü **Szerkesztés...** parancsára.

    ![Újonnan létrehozott GPO](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. Az újonnan létrehozott csoportházirend-objektumot a **csoportházirend-felügyeleti szerkesztő**használatával szabhatja testre.

    ![Új csoportházirend-objektum testreszabása](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


További információ a [csoportházirend-kezelő konzol](https://technet.microsoft.com/library/cc753298.aspx) használatáról a TechNet webhelyen érhető el.

## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD Domain Services – Első lépések útmutató](create-instance.md)
* [Windows Server rendszerű virtuális gép csatlakoztatása Azure AD Domain Services felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md)
* [Azure AD Domain Services tartomány kezelése](manage-domain.md)
* [Csoportházirend-kezelő konzol](https://technet.microsoft.com/library/cc753298.aspx)
