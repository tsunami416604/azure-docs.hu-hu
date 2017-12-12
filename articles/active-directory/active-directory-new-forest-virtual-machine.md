---
title: "Active Directory-erdő telepítése Azure virtuális hálózatra |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan hozzon létre egy új Active Directory-erdőt egy virtuális gépen (VM) egy Azure virtuális hálózaton."
services: active-directory, virtual-network
keywords: "az Active directory virtuális gép, telepítés active directory-erdőben, az azure active directory-videót "
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
tags: 
ms.assetid: eb7170d0-266a-4caa-adce-1855589d65d1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2017
ms.author: joflore
ms.openlocfilehash: 23bea4b6e3351bdce77e6d265ba258ce60a22a36
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="install-a-new-active-directory-forest-on-an-azure-virtual-network"></a>Egy új Active Directory-erdő telepítése Azure virtuális hálózaton
Ez a cikk bemutatja, hogyan hozható létre egy új Windows Server Active Directory-környezetben a virtuális gépen (VM) egy [Azure-beli virtuális hálózat](../virtual-network/virtual-networks-overview.md). Ebben az esetben az Azure virtuális hálózat nem egy helyi hálózathoz csatlakozik.

Is érdekelheti a kapcsolódó cikkekben:

* Ezeket a lépéseket bemutató videót: [egy új Active Directory-erdő telepítése Azure virtuális hálózat](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
* Igény szerint is [a telephelyek közötti VPN konfigurálása](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) és majd egy új erdő telepítéséhez vagy egy Azure virtuális hálózat a helyi erdő kiterjesztése. Ezeket a lépéseket lásd: [egy replika az Active Directory-tartományvezérlő telepítése egy Azure virtuális hálózat](active-directory-install-replica-active-directory-domain-controller.md).
* Active Directory tartományi szolgáltatások (AD DS) telepítése egy Azure virtuális hálózatra vonatkozó fogalmi útmutatóért lásd: [telepítése Windows Server Active Directory telepítése Azure virtuális gépekre vonatkozó irányelvek](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>A forgatókönyv diagramja
Ebben a forgatókönyvben a külső felhasználók tartományhoz csatlakozó kiszolgálón futó alkalmazások eléréséhez szükséges. Az alkalmazás-kiszolgálókat futtató virtuális gépeken és a tartományvezérlők futtató virtuális gépeken telepített Azure virtuális hálózat a saját felhő szolgáltatásának telepítve. Ezek megtalálhatók is a rendelkezésre állási készlet továbbfejlesztett hibatűrést.

![Active Directory-erdő Azure virtuális hálózatban a virtuális gépek ][1] 7

## <a name="how-does-this-differ-from-on-premises"></a>Ez eltérések a helyszíni?
Nincs sok tartományvezérlő telepítése az Azure-on és a helyszíni különbsége. A fő különbség a következő táblázatban láthatók.

| Konfigurálása... | Helyszíni követelmények | Azure virtuális hálózat |
| --- | --- | --- |
| **A tartományvezérlő IP-cím** |Rendelje hozzá a statikus IP-címet a hálózati adapter tulajdonságai |Futtassa a Set-AzureStaticVNetIP parancsmagot egy statikus IP-cím hozzárendelése |
| **DNS-ügyfél feloldó** |Előnyben részesített és másodlagos DNS-kiszolgáló címét a hálózaton állítsa be a tartományi tagok adapter tulajdonságai |A DNS-kiszolgáló címének beállítása a a virtuális hálózati tulajdonságok |
| **Active Directory adatbázistár** |Igény szerint módosítsa az alapértelmezett tárolási helyét C:\ |Módosítania kell alapértelmezett tárolási helyét a C:\ |

## <a name="create-an-azure-virtual-network"></a>Az Azure virtuális hálózat létrehozása
1. Jelentkezzen be az Azure portálra.
2. Hozzon létre egy virtuális hálózatot. Kattintson a **hálózatok** > **hozzon létre egy virtuális hálózatot**. Az alábbi táblázatban szereplő értékek használatához a varázsló befejezéséhez.

   | A varázsló e lapján... | Adja meg ezeket az értékeket |
   | --- | --- |
   |  **Virtuális hálózat adatai** |<p>Name: Adjon meg egy nevet a virtuális hálózat</p><p>Régió: Válassza ki a legközelebbi régiót</p> |
   |  **DNS- és VPN** |<p>Hagyja üresen a DNS-kiszolgáló</p><p>Ne válassza ki bármelyik VPN-beállítás</p> |
   |  **Virtuális hálózat** |<p>Alhálózati név: Adja meg az alhálózat nevét</p><p>Kezdő IP: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p> |

## <a name="create-vms-to-run-the-domain-controller-and-dns-server-roles"></a>A tartományvezérlő és DNS-kiszolgálói szerepkörökre futtatandó virtuális gépek létrehozása
Ismételje meg a következő lépésekkel hozza létre a tartományvezérlő szerepkört igény szerint a virtuális gépek. A hibatűrés és a redundancia legalább két virtuális tartományvezérlők kell telepíteni. Ha az Azure virtuális hálózat tartalmazza-e legalább két tartományvezérlő hasonló módon konfigurált (Ez azt jelenti, hogy mindkét juthatnak ebbe a generációba, futtassa a DNS-kiszolgáló, és nem rendelkezik a műveleti Főkiszolgálói szerepkört, és így tovább) helyezze a futtató virtuális gépeken a tartományvezérlők a rendelkezésre állási készlet továbbfejlesztett hibatűrést.

A virtuális gépek létrehozását a felhasználói felület helyett a Windows PowerShell használatával: [a Azure PowerShell szolgáltatás használatával hozzon létre, és előre konfigurálása a Windows-alapú virtuális gépek](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

1. Válassza ki az Azure-portálon **új** > **számítási**, majd válassza ki a virtuális gép. A következő értékeket használja a varázsló befejezéséhez. Fogadja el az alapértelmezett érték a következőhöz: setting, kivéve, ha egy másik érték javasolt vagy szükséges.

   | A varázsló e lapján... | Adja meg ezeket az értékeket |
   | --- | --- |
   |  **Kép kiválasztása** |Windows Server 2012 R2 Datacenter |
   |  **Virtuálisgép-konfiguráció** |<p>Virtuális gép neve: (Például AzureDC1) egycímkés nevet írja be.</p><p>Új felhasználónevet: Írja be annak a felhasználónak a nevére. Ez a felhasználó a virtuális Gépen a helyi Rendszergazdák csoport tagja lesz. Szüksége lesz erre a névre kattintva először jelentkezzen be a virtuális Gépet. A beépített Rendszergazda fiók nem fog működni.</p><p>Új jelszó megerősítése: Adjon meg egy jelszót</p> |
   |  **Virtuálisgép-konfiguráció** |<p>Felhőalapú szolgáltatás: Válassza ki <b>hozzon létre egy új felhőalapú szolgáltatás</b> az első virtuális gép, és válassza ki az adott felhőalapú szolgáltatás néven további virtuális gépek létrehozásakor a tartományvezérlő szerepkör fogja futtatni.</p><p>Felhőalapú szolgáltatás DNS-neve: Globálisan egyedi nevet adjon meg.</p><p>Régió/Affinitáscsoport/virtuális hálózat: Adja meg a virtuális hálózat nevét (például WestUSVNet).</p><p>Tárfiók: Válassza ki <b>automatikusan létrehozott tárfiók használata</b> az első virtuális gép, és válassza ki azt, hogy ugyanazon tárfiók neve további virtuális gépek létrehozásakor a tartományvezérlő szerepkör fogja futtatni.</p><p>Rendelkezésre állási csoportot: Válassza ki <b>egy rendelkezésre állási csoport létrehozása</b>.</p><p>Rendelkezésre állási csoport neve: a rendelkezésre állási csoporthoz az első virtuális gép létrehozásakor, és válassza ki, hogy ugyanaz az további virtuális gépek létrehozásakor nevet írja be nevét.</p> |
   |  **Virtuálisgép-konfiguráció** |<p>Válassza ki <b>a Virtuálisgép-ügynök telepítése</b> és bármely más bővítmények van szüksége.</p> |
2. Lemez csatolása a DC-kiszolgálói szerepkör által futtatott virtuális gépek. A bővített lemezterület AD adatbázis, naplófájlok és SYSVOL tároló van szükség. Adja meg a méretet, a lemez (például 10 GB-os), és hagyja a **állomás gyorsítótár preferencia** beállítása **nincs**. Az útmutató: [hogyan lehet adatlemezt csatolni egy Windows rendszerű virtuális gép](../virtual-machines/windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
3. Miután először bejelentkezik a virtuális géphez, nyissa meg **Kiszolgálókezelő** > **fájl- és tárolási szolgáltatások** kötet létrehozása a lemezen NTFS fájlrendszerrel.
4. Egy statikus IP-címet lefoglalni a tartományvezérlő szerepkör által futtatott virtuális gépek. Egy statikus IP-címet lefoglalni, töltse le a Microsoft Webplatform-telepítő és [Azure PowerShell telepítése](/powershell/azure/overview) és futtassa a Set-AzureStaticVNetIP parancsmagot. Példa:

    `Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM`

Egy statikus IP-cím beállításával kapcsolatos további információkért lásd: [egy statikus belső IP-cím konfigurálása a virtuális gépek](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-windows-server-active-directory"></a>Windows Server Active Directory telepítése
Használja az ugyanazon rutin [Active Directory tartományi szolgáltatások telepítése](https://technet.microsoft.com/library/jj574166.aspx) , hogy használja-e a helyi (vagyis, használhatja a felhasználói felület, a válaszfájl vagy a Windows PowerShell). Meg kell adnia egy új erdő telepítéséhez rendszergazdai hitelesítő adatokkal. Adja meg az Active Directory-adatbázis, a naplókat, és a SYSVOL mappa helyét, módosítsa az alapértelmezett tárolási helyét a az operációs rendszer meghajtóján további adatok lemezre, amely a virtuális géphez csatolt.

A tartományvezérlő telepítésének befejezése után csatlakoztassa újra a virtuális Gépet, és jelentkezzen be a tartományvezérlő. Ne felejtse el a tartományi hitelesítő adatok megadásához.

## <a name="reset-the-dns-server-for-the-azure-virtual-network"></a>A DNS-kiszolgáló, az Azure virtuális hálózat alaphelyzetbe állítása
1. Alaphelyzetbe állítja a DNS-továbbító beállítása az új tartományvezérlő/DNS-kiszolgálón.
   1. A Kiszolgálókezelőben kattintson **eszközök** > **DNS**.
   2. A **DNS-kezelő**, kattintson a jobb gombbal a DNS-kiszolgáló nevét, és kattintson a **tulajdonságok**.
   3. Az a **továbbítók** lapra, kattintson a továbbító IP-címét, majd **szerkesztése**.  Válassza ki az IP-címet, és kattintson a **törlése**.
   4. Kattintson a **OK** a szerkesztő bezárása és **Ok** a DNS-kiszolgáló tulajdonságainak bezárásához.
2. A DNS-kiszolgáló beállítása a virtuális hálózat frissítése.
   1. Kattintson a **virtuális hálózatok** > kattintson duplán a virtuális hálózat létrehozott > **konfigurálása** > **DNS-kiszolgálók**, írja be a nevet és az IP-címe a virtuális gépek közül amelyen fut a tartományvezérlő/DNS-kiszolgálói szerepkört, majd kattintson **mentése**.
   2. Válassza ki a virtuális Gépet, és kattintson a **indítsa újra a** elindítani a virtuális gépek a DNS-feloldási beállítások konfigurálása az új DNS-kiszolgáló IP-címmel.

## <a name="create-vms-for-domain-members"></a>Tartományhoz csatlakoztatott virtuális gépek létrehozása
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

## <a name="see-also"></a>Lásd még:
* [Egy új Active Directory-erdő telepítése Azure virtuális hálózat](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
* [Windows Server Active Directory az Azure virtuális gépeken telepítési útmutatója](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [A telephelyek közötti VPN konfigurálása](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [A replika Active Directory-tartományvezérlő telepítése egy Azure virtuális hálózatban](active-directory-install-replica-active-directory-domain-controller.md)
* [A Microsoft Azure informatikai szakemberek IaaS: (01) virtuális gép – alapok](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [A Microsoft Azure informatikai szakemberek IaaS: (05) létrehozása virtuális hálózatok és a létesítmények közötti kapcsolat](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md)
* [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure parancsmag-referencia](/powershell/azure/get-started-azureps)
* [Azure virtuális gép statikus IP-cím beállítása](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
* [Azure virtuális gép statikus IP-cím hozzárendelése](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
* [Egy új Active Directory-erdő telepítése](https://technet.microsoft.com/library/jj574166.aspx)
* [Bevezetés az Active Directory tartományi szolgáltatások (AD DS) Virtualizálásába (100-as szint)](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png
