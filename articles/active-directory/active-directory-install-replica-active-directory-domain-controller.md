---
title: "Az Active Directory replika tartományvezérlő telepítése az Azure-ban |} Microsoft Docs"
description: "Ez az oktatóanyag ismerteti a tartományvezérlő telepítése egy Azure virtuális gépen a helyszíni Active Directory erdőből származik."
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 8c9ebf1b-289a-4dd6-9567-a946450005c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: fb0bacac346445e6bde9df22f3355419e3162a3c
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Az Active Directory replika tartományvezérlő telepítése egy Azure virtuális hálózatban
Ez a témakör bemutatja a további tartományvezérlők (más néven a tartományvezérlők replika) telepítése az Azure virtuális hálózat az Azure virtuális gépek (VM) a helyszíni Active Directory-tartományhoz.

> [!IMPORTANT]
> A Microsoft javasolja, hogy az Azure Portalon található [Azure AD felügyeleti központból](https://aad.portal.azure.com) kezelje az Azure AD-t az ebben a cikkben javasolt klasszikus Azure portál helyett.

Is érdekelheti az alábbi kapcsolódó témakörök:

* Egy új Active Directory-erdő opcionálisan egy Azure virtuális hálózatra is telepíthető. Ezeket a lépéseket lásd: [egy új Active Directory-erdő telepítése Azure virtuális hálózatra](active-directory-new-forest-virtual-machine.md).
* Active Directory tartományi szolgáltatások (AD DS) telepítése egy Azure virtuális hálózatra vonatkozó fogalmi útmutatóért lásd: [telepítése Windows Server Active Directory telepítése Azure virtuális gépekre vonatkozó irányelvek](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>A forgatókönyv diagramja
Ebben a forgatókönyvben a külső felhasználók tartományhoz csatlakozó kiszolgálón futó alkalmazások eléréséhez szükséges. A virtuális gépeket futtató az alkalmazás-kiszolgálókat, és a replika tartományvezérlők egy Azure virtuális hálózatra van telepítve. A virtuális hálózat csatlakozhat a helyszíni hálózathoz egy [telephelyek közötti VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) kapcsolat, ahogy az az alábbi ábrán, vagy használhat [ExpressRoute](../expressroute/expressroute-locations-providers.md) a gyorsabb kapcsolatok.

Az alkalmazás-kiszolgálókat és a tartományvezérlők számítási feldolgozási terjeszteni különálló felhőszolgáltatások és belül vannak telepítve [rendelkezésre állási készletek](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) továbbfejlesztett a hibatűrés.
A tartományvezérlők Active Directory-replikáció használatával replikálja az egymással, és a helyszíni tartományvezérlők. Egyetlen szinkronizálási eszközöket van szükség.

![Diagram pf replika Active Directory-tartományvezérlő egy Azure virtuális hálózatot][1]

## <a name="create-an-active-directory-site-for-the-azure-virtual-network"></a>Active Directory-helyet az Azure virtuális hálózat létrehozása
Jó ötlet a webhely létrehozása az Active Directoryban, amely a megfelelő hálózati régiót jelöli a virtuális hálózathoz. Amely segít a hitelesítés, a replikáció és az egyéb DC hely műveletek optimalizálása. A következő lépések bemutatják, hogyan hozhat létre egy helyet és további háttér, lásd: [hozzáadása egy új helyet](https://technet.microsoft.com/library/cc781496.aspx).

1. Nyissa meg az Active Directory – helyek és szolgáltatások: **Kiszolgálókezelő** > **eszközök** > **Active Directory – helyek és szolgáltatások**.
2. Hozzon létre egy helyet, a régiót, amelyben létrehozta az Azure virtuális hálózat képviselő: kattintson a **helyek** > **művelet** > **új hely** > típus a az új webhely, például Azure Velünk nyugati neve > Válassza ki a helykapcsolatot > **OK**.
3. Hozzon létre egy alhálózatot, és rendelje hozzá az új hely: kattintson duplán a **helyek** > kattintson a jobb gombbal **alhálózatok** > **új alhálózat** > írja be az IP-címtartományt, a virtuális hálózat (például 10.1.0.0/16 a forgatókönyv diagramon) > Válassza ki az új Azure hely > **OK**.

## <a name="create-an-azure-virtual-network"></a>Az Azure virtuális hálózat létrehozása
1. Az a [a klasszikus Azure portálon](https://manage.windowsazure.com), kattintson a **új** > **hálózati szolgáltatások** > **virtuális hálózati**  >  **Egyéni létrehozás** és a következő értékeket használja a varázsló befejezéséhez.

   | A varázsló e lapján... | Adja meg ezeket az értékeket |
   | --- | --- |
   |  **Virtuális hálózat adatai** |<p>Name: Írja be a virtuális hálózatokon, például WestUSVNet nevét.</p><p>Régió: Válassza ki a legközelebbi régiót.</p> |
   |  **DNS- és VPN-kapcsolat** |<p>DNS-kiszolgálók: Adja meg, és a helyi DNS-kiszolgálók egy vagy több IP-címét.</p><p>Kapcsolat: Válassza ki **a telephelyek közötti VPN konfigurálása**.</p><p>Helyi hálózati: Adjon meg egy új helyi hálózatot.</p><p>Ha ExpressRoute használ VPN helyett, lásd: [Exchange szolgáltatón keresztül ExpressRoute kapcsolat konfigurálása](../expressroute/expressroute-locations-providers.md).</p> |
   |  **Hely-hely kapcsolatot** |<p>Name: Írja be a helyszíni hálózat nevét.</p><p>VPN-eszköz IP-cím: Adja meg a nyilvános IP-címét az eszközt, hogy a virtuális hálózathoz csatlakozzon. A VPN-eszköz nem lehet NAT mögött.</p><p>Cím: Adja meg (például a forgatókönyv diagramon 192.168.0.0/16) a helyi hálózat címtartományát.</p> |
   |  **Virtuális hálózat** |<p>Címterület: Az Azure virtuális hálózat (például 10.1.0.0/16 a forgatókönyv diagramon) futtatni kívánt virtuális gépekhez adja meg az IP-címtartományt. Ez a címtartomány a helyszíni hálózat címtartományainak nem lehet átfedésben.</p><p>Alhálózatok: Adja meg, és az alkalmazáskiszolgálók az alhálózat címét (például az előtér 10.1.1.0/24) és a tartományvezérlők (például a háttér 10.1.2.0/24).</p><p>Kattintson a **átjáró alhálózatának hozzáadása**.</p> |
2. Ezután konfigurálhatja a virtuális hálózati átjáró pont-pont biztonságos VPN-kapcsolat létrehozásához. Lásd: [konfigurálja a virtuális hálózati átjáró](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) vonatkozó utasításokat.
3. A pont-pont VPN kapcsolatot az új virtuális hálózat és egy helyszíni VPN-eszköz létrehozása. Lásd: [konfigurálja a virtuális hálózati átjáró](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) vonatkozó utasításokat.

## <a name="create-azure-vms-for-the-dc-roles"></a>A tartományvezérlő a szerepkörök Azure virtuális gépek létrehozása
Ismételje meg a következő lépésekkel hozza létre a tartományvezérlő szerepkört igény szerint a virtuális gépek. A hibatűrés és a redundancia legalább két virtuális tartományvezérlők kell telepíteni. Ha az Azure virtuális hálózat tartalmazza-e legalább két tartományvezérlő hasonló módon konfigurált (Ez azt jelenti, hogy mindkét juthatnak ebbe a generációba, futtassa a DNS-kiszolgáló, és nem rendelkezik a műveleti Főkiszolgálói szerepkört, és így tovább) helyezze a futtató virtuális gépeken a tartományvezérlők a rendelkezésre állási készlet továbbfejlesztett hibatűrést.
A virtuális gépek létrehozását a felhasználói felület helyett a Windows PowerShell használatával: [a Azure PowerShell szolgáltatás használatával hozzon létre, és előre konfigurálása a Windows-alapú virtuális gépek](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

1. Az a [a klasszikus Azure portálon](https://manage.windowsazure.com), kattintson a **új** > **számítási** > **virtuális gép**  >  **Gyűjteményből**. A következő értékeket használja a varázsló befejezéséhez. Fogadja el az alapértelmezett érték a következőhöz: setting, kivéve, ha egy másik érték javasolt vagy szükséges.

   | A varázsló e lapján... | Adja meg ezeket az értékeket |
   | --- | --- |
   |  **Kép kiválasztása** |Windows Server 2012 R2 Datacenter |
   |  **Virtuálisgép-konfiguráció** |<p>Virtuális gép neve: (Például AzureDC1) egycímkés nevet írja be.</p><p>Új felhasználónevet: Írja be annak a felhasználónak a nevére. Ez a felhasználó a virtuális Gépen a helyi Rendszergazdák csoport tagja lesz. Szüksége lesz erre a névre kattintva először jelentkezzen be a virtuális Gépet. A beépített Rendszergazda fiók nem fog működni.</p><p>Új jelszó megerősítése: Adjon meg egy jelszót</p> |
   |  **Virtuálisgép-konfiguráció** |<p>Felhőalapú szolgáltatás: Válassza ki <b>hozzon létre egy új felhőalapú szolgáltatás</b> az első virtuális gép, és válassza ki az adott felhőalapú szolgáltatás néven további virtuális gépek létrehozásakor a tartományvezérlő szerepkör fogja futtatni.</p><p>Felhőalapú szolgáltatás DNS-neve: Globálisan egyedi nevet adjon meg.</p><p>Régió/Affinitáscsoport/virtuális hálózat: Adja meg a virtuális hálózat nevét (például WestUSVNet).</p><p>Tárfiók: Válassza ki <b>automatikusan létrehozott tárfiók használata</b> az első virtuális gép, és válassza ki azt, hogy ugyanazon tárfiók neve további virtuális gépek létrehozásakor a tartományvezérlő szerepkör fogja futtatni.</p><p>Rendelkezésre állási csoportot: Válassza ki <b>egy rendelkezésre állási csoport létrehozása</b>.</p><p>Rendelkezésre állási csoport neve: a rendelkezésre állási csoporthoz az első virtuális gép létrehozásakor, és válassza ki, hogy ugyanaz az további virtuális gépek létrehozásakor nevet írja be nevét.</p> |
   |  **Virtuálisgép-konfiguráció** |<p>Válassza ki <b>a Virtuálisgép-ügynök telepítése</b> és bármely más bővítmények van szüksége.</p> |
2. Lemez csatolása a DC-kiszolgálói szerepkör által futtatott virtuális gépek. A bővített lemezterület AD adatbázis, naplófájlok és SYSVOL tároló van szükség. Adja meg a méretet, a lemez (például 10 GB-os), és hagyja a **állomás gyorsítótár preferencia** beállítása **nincs**. Az útmutató: [hogyan lehet adatlemezt csatolni egy Windows rendszerű virtuális gép](../virtual-machines/windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
3. Miután először bejelentkezik a virtuális géphez, nyissa meg **Kiszolgálókezelő** > **fájl- és tárolási szolgáltatások** kötet létrehozása a lemezen NTFS fájlrendszerrel.
4. Egy statikus IP-címet lefoglalni a tartományvezérlő szerepkör által futtatott virtuális gépek. Egy statikus IP-címet lefoglalni, töltse le a Microsoft Webplatform-telepítő és [Azure PowerShell telepítése](/powershell/azure/overview) és futtassa a Set-AzureStaticVNetIP parancsmagot. Példa:

    "Get-AzureVM - ServiceName AzureDC1-Name AzureDC1 |} Set-AzureStaticVNetIP - IP-cím 10.0.0.4 |} Frissítés-AzureVM

Egy statikus IP-cím beállításával kapcsolatos további információkért lásd: [egy statikus belső IP-cím konfigurálása a virtuális gépek](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>Active Directory tartományi szolgáltatások telepítése Azure virtuális gépeken
Jelentkezzen be egy virtuális Gépet, és ellenőrizze, hogy van kapcsolat a webhelyek közötti VPN- vagy ExpressRoute kapcsolattal erőforrások között a helyi hálózaton. Majd telepítse az Active Directory tartományi szolgáltatások az Azure virtuális gépeken. Használhatja ugyanazt a folyamatot, amellyel további tartományvezérlő telepítése a helyi hálózaton (felhasználói felület, a Windows PowerShell vagy válaszfájl). Active Directory tartományi szolgáltatások telepítéséhez, ellenőrizze, hogy megadja az új kötet AD adatbázisát, naplófájlok és SYSVOL helyét. Ha az AD DS-telepítés frissítő van szüksége, tekintse meg [Active Directory tartományi szolgáltatások telepítése (100-as szint)](https://technet.microsoft.com/library/hh472162.aspx) vagy [egy replika Windows Server 2012 tartományvezérlő telepítése meglévő tartományban (200-as szint)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>A virtuális hálózat DNS-kiszolgáló
1. A a [Azure-portálon](https://portal.azure.com), a a **keresési erőforrások** adja meg a *virtuális hálózatok*, majd kattintson a **virtuális hálózatok (klasszikus)** a a a keresési eredményekben. Kattintson a virtuális hálózat nevére, majd [a virtuális hálózat DNS-kiszolgáló IP-címének újrakonfigurálása](../virtual-network/virtual-network-manage-network.md#dns-servers) a statikus IP-címek hozzárendelve a replika tartományvezérlők helyett egy helyi DNS-kiszolgálók IP-címét.
2. Győződjön meg arról, hogy a virtuális hálózat összes replika tartományvezérlő virtuális gépek vannak konfigurálva a virtuális hálózati DNS-kiszolgálók használatát, kattintson a **virtuális gépek**, kattintson az Állapot oszlopban az egyes virtuális gépek, majd **indítsa újra a**. Várjon, amíg a virtuális gép látható **futtató** előtt jelentkezzen be az állapot.

## <a name="create-vms-for-application-servers"></a>Virtuális gépek létrehozása az alkalmazáskiszolgálók
1. Ismételje meg a következő lépésekkel hozza létre az alkalmazás-kiszolgálókat futtató virtuális gépek. Fogadja el az alapértelmezett érték a következőhöz: setting, kivéve, ha egy másik érték javasolt vagy szükséges.

   | A varázsló e lapján... | Adja meg ezeket az értékeket |
   | --- | --- |
   |  **Kép kiválasztása** |Windows Server 2012 R2 Datacenter |
   |  **Virtuálisgép-konfiguráció** |<p>Virtuális gép neve: (Például AppServer1) egycímkés nevet írja be.</p><p>Új felhasználónevet: Írja be annak a felhasználónak a nevére. Ez a felhasználó a virtuális Gépen a helyi Rendszergazdák csoport tagja lesz. Szüksége lesz erre a névre kattintva először jelentkezzen be a virtuális Gépet. A beépített Rendszergazda fiók nem fog működni.</p><p>Új jelszó megerősítése: Adjon meg egy jelszót</p> |
   |  **Virtuálisgép-konfiguráció** |<p>Felhőalapú szolgáltatás: Válassza ki **hozzon létre egy új felhőalapú szolgáltatás** az első virtuális gép, és válassza ki az adott felhőalapú szolgáltatás néven további virtuális gépek létrehozásakor az alkalmazás fogja futtatni.</p><p>Felhőalapú szolgáltatás DNS-neve: Globálisan egyedi nevet adjon meg.</p><p>Régió/Affinitáscsoport/virtuális hálózat: Adja meg a virtuális hálózat nevét (például WestUSVNet).</p><p>Tárfiók: Válassza ki **automatikusan létrehozott tárfiók használata** az első virtuális gép, és válassza ki azt, hogy ugyanazon tárfiók neve további virtuális gépek létrehozásakor az alkalmazás fogja futtatni.</p><p>Rendelkezésre állási csoportot: Válassza ki **egy rendelkezésre állási csoport létrehozása**.</p><p>Rendelkezésre állási csoport neve: a rendelkezésre állási csoporthoz az első virtuális gép létrehozásakor, és válassza ki, hogy ugyanaz az további virtuális gépek létrehozásakor nevet írja be nevét.</p> |
   |  **Virtuálisgép-konfiguráció** |<p>Válassza ki <b>a Virtuálisgép-ügynök telepítése</b> és bármely más bővítmények van szüksége.</p> |
2. Minden virtuális gép üzembe helyezése után jelentkezzen be, és csatlakoztatása a tartományhoz. A **Kiszolgálókezelő**, kattintson a **helyi kiszolgáló** > **munkacsoport** > **módosítása...** Válassza ki és **tartomány** és a helyszíni tartománynak a nevét. Adjon meg egy tartományi felhasználó hitelesítő adataival, majd indítsa újra a virtuális Gépen végezze el a tartományhoz való csatlakozást.

A virtuális gépek létrehozását a felhasználói felület helyett a Windows PowerShell használatával: [a Azure PowerShell szolgáltatás használatával hozzon létre, és előre konfigurálása a Windows-alapú virtuális gépek](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

A Windows PowerShell használatával kapcsolatos további információkért lásd: [Ismerkedés az Azure-parancsmagok](/powershell/azure/overview) és [Azure parancsmag-referencia](/powershell/azure/get-started-azureps).

## <a name="additional-resources"></a>További források
* [Windows Server Active Directory az Azure virtuális gépeken telepítési útmutatója](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Hogyan lehet feltölteni a meglévő helyszíni Hyper-V rendszerű tartományvezérlők Azure az Azure PowerShell használatával](http://support.microsoft.com/kb/2904015)
* [Egy új Active Directory-erdő telepítése Azure virtuális hálózaton](active-directory-new-forest-virtual-machine.md)
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* [A Microsoft Azure informatikai szakemberek IaaS: (01) virtuális gép – alapok](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [A Microsoft Azure informatikai szakemberek IaaS: (05) létrehozása virtuális hálózatok és a létesítmények közötti kapcsolat](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Azure PowerShell](/powershell/azure/overview)
* [Az Azure felügyeleti parancsmagok](/powershell/module/azurerm.compute/#virtual_machines)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png
