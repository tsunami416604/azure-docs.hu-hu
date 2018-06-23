---
title: 'Az Azure Active Directory tartományi szolgáltatások: Felügyelni a DNS a felügyelt tartományok |} Microsoft Docs'
description: A felügyelt tartományok Azure Active Directory tartományi szolgáltatások DNS felügyelete
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
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: d8559df366bdd9c1439f2ff8c7b7ebc1a7c66960
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "36334190"
---
# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Felügyelni a DNS a az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz
Az Azure Active Directory tartományi szolgáltatások magában foglalja a DNS-feloldás biztosít a felügyelt tartományra (tartománynév feloldásával) DNS-kiszolgálóval. Alkalmanként esetleg DNS konfigurálása a felügyelt tartományra. Szükség lehet, a tartományhoz nem csatlakozó gépek DNS-rekordok létrehozása, konfigurálása a terheléselosztók virtuális IP-címet, vagy a telepítés külső DNS-továbbítók. Ezért a "AAD DC rendszergazdák" csoportba tartozó felhasználók kapnak a felügyelt tartományra DNS felügyeleti jogosultságokkal.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Előkészületek
A cikkben szereplő feladatok elvégzéséhez szüksége:

1. Egy érvényes **Azure-előfizetés**.
2. Egy **Azure AD-címtár** -vagy egy helyszíni címtár vagy egy csak felhőalapú directory szinkronizálva.
3. **Azure AD tartományi szolgáltatások** az Azure AD-címtár engedélyezni kell. Ha még nem tette meg, az összes ismertetett feladatok végrehajtásával a [első lépések útmutató](active-directory-ds-getting-started.md).
4. A **tartományhoz csatlakoztatott virtuális gép** , amelyből az Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete. Ha egy virtuális gép nem rendelkezik, a című cikkben ismertetett összes feladatok végrehajtásával [egy Windows rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz](active-directory-ds-admin-guide-join-windows-vm.md).
5. A hitelesítő adatait kell egy **az "AAD DC rendszergazdák" csoportba tartozó felhasználói fiók** a könyvtárban, a felügyelt tartományok DNS felügyeletéhez.

<br>

## <a name="task-1---create-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>1. feladat – távoli felügyelete a felügyelt tartomány DNS-tartományhoz virtuális gép létrehozása
Az Azure AD tartományi szolgáltatások felügyelt tartományok kezelheti távolról már ismerős eszközökkel Active Directory felügyeleti például az Active Directory felügyeleti központ (ADAC) vagy AD PowerShell segítségével. Hasonlóképpen a felügyelt tartományra DNS használatával lehet felügyelni távolról a DNS-kiszolgáló felügyeleti eszközei.

Az Azure AD-címtár rendszergazdái nem jogosult csatlakozni a távoli asztalon keresztül a felügyelt tartományra tartományvezérlők. A "AAD DC rendszergazdák" csoportba DNS felügyelheti a felügyelt tartomány tagja egy Windows Server-ügyfél számítógépről távolról DNS-kiszolgáló eszközeivel felügyelt tartományok. DNS-kiszolgáló eszközök a Távoli kiszolgálófelügyelet eszközei (RSAT) választható szolgáltatás részét képezik.

Az első tevékenységet, ha a Windows Server virtuális gép, amely a felügyelt tartományhoz csatlakozik. Útmutatásért tekintse meg a című cikk [Windows Server virtuális gép csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-windows-vm.md).

## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>2. feladat – telepítés DNS-kiszolgáló eszközök a virtuális gépen
Kövesse az alábbi lépéseket a DNS-felügyeleti eszközök telepítése a tartományhoz csatlakoztatott virtuális gépen. További információ a [telepítéséről és használatáról a Távoli kiszolgálófelügyelet eszközei](https://technet.microsoft.com/library/hh831501.aspx), tekintse meg a TechNet webhelyén.

1. Keresse meg az Azure-portálon. Kattintson a **összes erőforrás** a bal oldali panelen. Keresse meg, és kattintson az 1. feladatban létrehozott virtuális gépre.
2. Kattintson a **Connect** – Áttekintés lap gombjára. A távoli asztal protokoll (RDP) fájl létrehozása, és le.

    ![Windows virtuális géphez](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz. Ha a rendszer kéri, akkor kattintson a **Csatlakozás** gombra. Az "AAD DC rendszergazdák" csoportba tartozó felhasználói hitelesítő adatokat használja. Például "bob@domainservicespreview.onmicrosoft.com". A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Kattintson az Igen gombra vagy a Folytatás való csatlakozáshoz.

4. A kezdőképernyőről nyissa meg a **Kiszolgálókezelő**. Kattintson a **szerepkörök és szolgáltatások hozzáadása** a Kiszolgálókezelő ablakban központi panelén.

    ![Indítsa el a Kiszolgálókezelőt a virtuális gépen](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. Az a **előkészületek** oldalán a **hozzáadása szerepkörök és szolgáltatások varázsló**, kattintson a **következő**.

    ![Mielőtt elkezdené lap](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. Az a **telepítési típus** lapon, hagyja a **szerepköralapú vagy szolgáltatásalapú telepítés** beállítás be van jelölve, és kattintson **következő**.

    ![Telepítés típusa lap](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. Az a **kiszolgáló kiválasztása** lapon válassza ki az aktuális virtuális gépet a kiszolgálókészletből, és kattintson a **következő**.

    ![Kiszolgáló kiválasztása lap](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. Az a **kiszolgálói szerepkörök** kattintson **következő**.
9. Az a **szolgáltatások** lap gombra, bontsa ki a **távoli kiszolgálófelügyelet eszközei** csomópontra majd bontsa ki a **szerepkör-felügyeleti eszközök** csomópont. Válassza ki **DNS-kiszolgálói eszközök** szolgáltatás szerepkör-felügyeleti eszközök a listából.

    ![Szolgáltatások lapon](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)
10. Az a **megerősítő** kattintson **telepítése** a DNS-kiszolgáló eszközök szolgáltatás telepítése a virtuális gépen. Ha a szolgáltatás telepítése sikeresen befejeződött, kattintson **Bezárás** való kilépéshez a **szerepkörök és szolgáltatások hozzáadása** varázsló.

    ![Jóváhagyás lap](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)

## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>3. feladat – indítsa el a DNS-kezelő konzol DNS felügyeletéhez
Most Windows Server DNS-eszközök segítségével felügyelheti a felügyelt tartomány DNS.

> [!NOTE]
> Ahhoz, hogy adminisztrálja a felügyelt tartomány DNS "AAD DC rendszergazdák" csoport tagjának lennie kell.
>
>

1. A kezdőképernyőről kattintson **felügyeleti eszközök**. Megjelenik a **DNS** konzol telepítve legyen a virtuális gépen.

    ![Felügyeleti eszközök – DNS-konzol](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)
2. Kattintson a **DNS** elindíthatja a DNS-kezelőben.
3. A a **kapcsolódás a DNS-kiszolgáló** párbeszédpanel, kattintson a **a következő számítógép**, és írja be a DNS-tartománynév, a felügyelt tartomány (például "contoso100.com").

    ![DNS-konzol - csatlakozás tartományhoz](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)
4. A DNS-konzol kapcsolódik a felügyelt tartományra.

    ![DNS-konzol - tartomány felügyelete](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)
5. A DNS-konzol segítségével most adja hozzá a virtuális hálózat, amelyen engedélyezve van a AAD tartományi szolgáltatásokra belüli számítógépek DNS-bejegyzéseket.

> [!WARNING]
> Ügyeljen arra, hogy a DNS-felügyeleti eszközök segítségével felügyelt tartomány DNS felügyeletekor. Győződjön meg arról, hogy **törölni vagy módosítani a beépített DNS-rekordokat, a tartomány tartományi szolgáltatások által használt**. Beépített DNS-rekordok közé tartozik a tartomány DNS-rekordokat, a névkiszolgáló rekordjait és a DC helyen használt egyéb rögzíti. Ha módosítja ezeket a rekordokat, a tartományi szolgáltatások a virtuális hálózaton megszakadnak.
>
>

DNS kezelésével kapcsolatos további információkért tekintse meg a [DNS-eszközök a következő cikket a TechNet webhelyén](https://technet.microsoft.com/library/cc753579.aspx).

## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [Windows Server virtuális gép csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz](active-directory-ds-admin-guide-join-windows-vm.md)
* [Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete](active-directory-ds-admin-guide-administer-domain.md)
* [A DNS-felügyeleti eszközök](https://technet.microsoft.com/library/cc753579.aspx)
