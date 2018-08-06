---
title: 'Az Azure Active Directory Domain Services: A felügyelt tartomány DNS-kezelés |} A Microsoft Docs'
description: Az Azure Active Directory Domain Services felügyelt tartomány DNS-kezelés
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
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: f20b2859f72087e208e8963fb18b297c7c670f4f
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504284"
---
# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Az Azure AD tartományi szolgáltatások által kezelt tartomány DNS-kezelés
Az Azure Active Directory Domain Services által biztosított DNS-feloldás a felügyelt tartományhoz tartozó (tartománynevek feloldását) DNS-kiszolgáló is tartalmaz. Egyes esetekben szükség lehet DNS konfigurálása a felügyelt tartományon. Szükség lehet, a tartományhoz nem csatlakozó gépek DNS-rekordok létrehozása, konfigurálása a terheléselosztók virtuális IP-címek vagy külső DNS-továbbítók beállítása. Ebből kifolyólag az "AAD DC rendszergazdák" csoportba tartozó felhasználók kapnak a felügyelt tartomány DNS felügyeleti jogosultságokkal.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok végrehajtásához szükséges:

1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy az egy helyszíni címtár vagy egy csak felhőalapú címtárral szinkronizálja.
3. **Az Azure AD Domain Services** engedélyezve kell lennie az Azure AD-címtárban. Ha még nem tette, minden ismertetett feladatok végrehajtásával a [a kezdeti lépések útmutatóban](active-directory-ds-getting-started.md).
4. A **tartományhoz csatlakoztatott virtuális gép** , amelyről felügyelheti az Azure AD tartományi szolgáltatásokkal felügyelt tartományban. Ha egy virtuális gép nem rendelkezik, az összes című cikkben ismertetett feladatok végrehajtásával [Windows virtuális gépek csatlakoztatása felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md).
5. A hitelesítő adatait kell egy **felhasználói fiók, az "AAD DC rendszergazdák" csoportba tartozó** a címtárban, a DNS-kezelés a felügyelt tartományok.

<br>

## <a name="task-1---create-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>1. feladat – távoli felügyeletéhez a felügyelt tartományhoz tartozó DNS tartományhoz csatlakoztatott virtuális gép létrehozása
Az Azure AD Domain Services felügyelt tartomány távolról a jól ismert az Active Directory felügyeleti eszközök például az Active Directory felügyeleti központ (ADAC) vagy AD PowerShell segítségével is felügyelhetők. Hasonlóképpen a felügyelt tartományhoz tartozó DNS felügyelhetők távolról segítségével a DNS-kiszolgáló felügyeleti eszközei.

Az Azure AD-címtár rendszergazdái nem rendelkezik jogosultságokkal a tartományvezérlők a távoli asztalon keresztül felügyelt tartományon való kapcsolódáshoz. Az "AAD DC rendszergazdák" csoport tagjai felügyelhetik DNS távolról a DNS-kiszolgáló eszközök a felügyelt tartományhoz csatlakozó számítógépről a Windows Server vagy Windows-ügyfél használatával felügyelt tartományokban. DNS-kiszolgáló eszközök a Távoli kiszolgálófelügyelet eszközei (RSAT) választható szolgáltatás részét képezik.

Az első feladatra, hogy a felügyelt tartományhoz csatlakozó Windows Server virtuális gép létrehozása. Útmutatásért tekintse meg a című cikkben [Windows Server virtuális gép csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>2. feladat – a virtuális gép DNS-kiszolgáló telepítési eszközök
A következő lépéseket a DNS-felügyeleti eszközök telepítése a tartományhoz csatlakoztatott virtuális gépen. További információ a [telepítéséről és használatáról a Távoli kiszolgálófelügyelet eszközei](https://technet.microsoft.com/library/hh831501.aspx), tekintse meg a TechNet webhelyen.

1. Keresse meg az Azure Portalon. Kattintson a **összes erőforrás** a bal oldali panelen. Keresse meg és kattintson a virtuális gép az 1. feladatban létrehozott.
2. Kattintson a **Connect** gomb az Áttekintés lapon. Egy Remote Desktop Protocol (.rdp) fájlt a rendszer létrehoz és letölt.

    ![Windows virtuális gép eléréséhez](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz. Ha a rendszer kéri, akkor kattintson a **Csatlakozás** gombra. A "AAD DC rendszergazdák" csoportba tartozó felhasználói hitelesítő adatokat használja. Például "bob@domainservicespreview.onmicrosoft.com". A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Kattintson az Igen gombra vagy való csatlakozáshoz.

4. A kezdőképernyőről nyissa meg a **Kiszolgálókezelő**. Kattintson a **szerepkörök és szolgáltatások hozzáadása** a Kiszolgálókezelő ablakban középső ablaktábláján.

    ![Indítsa el a Kiszolgálókezelőt a virtuális gépen](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Az a **alapismeretek** lapján a **adja hozzá szerepkörök és szolgáltatások varázsló**, kattintson a **tovább**.

    ![Lap megkezdése előtt](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Az a **telepítési típus** lapon, hagyja a **szerepköralapú vagy szolgáltatásalapú telepítés** lehetőség be van jelölve, majd kattintson **tovább**.

    ![Telepítés típusa lap](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Az a **kiszolgáló kiválasztása** lapon válassza ki az aktuális virtuális gépet a kiszolgálókészletből, és kattintson a **tovább**.

    ![Kiszolgáló kiválasztása lap](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Az a **kiszolgálói szerepkörök** kattintson **tovább**.
9. Az a **funkciók** oldal, ide kattintva bontsa ki a **távoli kiszolgálófelügyelet eszközei** csomópontot, és bontsa ki, majd kattintson a **szerepkör-felügyeleti eszközök** csomópont. Válassza ki **DNS-kiszolgálói eszközök** szolgáltatása szerepkör-felügyeleti eszközök a listából.

    ![Szolgáltatások lapon](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)
10. Az a **megerősítő** kattintson **telepítése** a DNS-kiszolgáló eszközei szolgáltatás telepítése a virtuális gépen. Ha a szolgáltatás telepítése sikeresen befejeződött, kattintson az **Bezárás** való kilépéshez a **szerepkörök és szolgáltatások hozzáadása** varázsló.

    ![Megerősítő oldal](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)

## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>3. feladat – indítsa el a DNS-kezelő konzolt, a DNS-kezelés
A Windows Server DNS-eszközök segítségével, a felügyelt tartomány DNS-kezelés.

> [!NOTE]
> A felügyelt tartomány DNS-kezelés az "AAD DC rendszergazdák" csoport tagjának lennie kell.
>
>

1. A kezdőképernyőről kattintson **felügyeleti eszközök**. Megtekintheti a **DNS** konzol telepítése a virtuális gépen.

    ![Felügyeleti eszközök – DNS-konzol](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)
2. Kattintson a **DNS** , indítsa el a DNS-felügyeleti konzolt.
3. A a **kapcsolódás a DNS-kiszolgáló** párbeszédpanelen kattintson **a következő számítógép**, és adja meg a DNS-tartománynév számára a felügyelt tartomány (például "contoso100.com").

    ![DNS-konzol - csatlakozás tartományhoz](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)
4. A DNS-konzolt a felügyelt tartományhoz csatlakozik.

    ![DNS-konzol - tartomány kezelése](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)
5. Mostantól használhatja a DNS-konzolt, amelyben engedélyezte az AAD tartományi szolgáltatásokra a virtuális hálózaton lévő összes számítógép DNS-bejegyzéseinek hozzáadásához.

> [!WARNING]
> Ügyeljen arra, hogy amikor DNS felügyelete a felügyelt tartomány DNS-felügyeleti eszközök használatával. Győződjön meg arról, hogy Ön **törölje vagy módosítsa a beépített DNS-rekordok a tartomány a tartományi szolgáltatások által használt**. Beépített DNS-rekordok közé tartozik a tartomány DNS-rekordok névkiszolgálói rekordjainak és más rekordok használt Adatközpont helye. Ha ezeket a rekordokat módosítja, a tartományi szolgáltatások a virtuális hálózati megszakadnak.
>
>

DNS kezelésével kapcsolatos további információkért lásd: a [DNS-eszközök cikket TechNet](https://technet.microsoft.com/library/cc753579.aspx).

## <a name="related-content"></a>Kapcsolódó tartalom
* [Az Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [A Windows Server virtuális gépek csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-windows-vm.md)
* [Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete](active-directory-ds-admin-guide-administer-domain.md)
* [DNS-felügyeleti eszközök](https://technet.microsoft.com/library/cc753579.aspx)
