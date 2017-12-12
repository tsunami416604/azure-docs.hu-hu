---
title: "A helyszíni Active Directory-tartomány replika tartományvezérlők telepítése Azure virtuális gépeken futó |} Microsoft Docs"
description: "Hogyan lehet telepíteni az Azure virtuális gépek (VM) a helyszíni Active Directory-tartomány replika tartományvezérlők egy Azure virtuális hálózatra."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 8c9ebf1b-289a-4dd6-9567-a946450005c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 3f7624d588e958985a73c5b40e8010e18e8879cb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Az Active Directory replika tartományvezérlő telepítése egy Azure virtuális hálózatban
A cikk ismerteti a további tartományvezérlők (DC) használandó tartományvezérlők replikaként Azure virtuális gépek (VM) a helyszíni Active Directory-tartomány telepítése egy Azure virtuális hálózatban. Emellett [Windows Server Active Directory-erdő telepítése Azure virtuális hálózatra](active-directory-new-forest-virtual-machine.md). Az Active Directory tartományi szolgáltatások (AD DS) telepítése egy Azure virtuális hálózat tudnivalókat [telepítése Windows Server Active Directory telepítése Azure virtuális gépekre vonatkozó irányelvek](https://msdn.microsoft.com/library/azure/jj156090.aspx).

## <a name="scenario-diagram"></a>A forgatókönyv diagramja
Ebben a forgatókönyvben a külső felhasználók tartományhoz csatlakozó kiszolgálón futó alkalmazások eléréséhez szükséges. A virtuális gépeket futtató az alkalmazás-kiszolgálókat, és a replika tartományvezérlők egy Azure virtuális hálózatra van telepítve. A helyszíni hálózathoz csatlakozhat a virtuális hálózati [ExpressRoute](../expressroute/expressroute-locations-providers.md), vagy használhat egy [telephelyek közötti VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) kapcsolat látható módon: 

![Diagram pf replika Active Directory-tartományvezérlő egy Azure virtuális hálózatot][1]

Az alkalmazás-kiszolgálókat és a tartományvezérlők számítási feldolgozási terjeszteni különálló felhőszolgáltatások és rendelkezésre állási csoportok jobb hibatűrés belül vannak telepítve.
A tartományvezérlők Active Directory-replikáció használatával replikálja az egymással, és a helyszíni tartományvezérlők. Egyetlen szinkronizálási eszközöket van szükség.

## <a name="create-an-on-premises-active-directory-site-for-the-azure-virtual-network"></a>A helyszíni Active Directory-helyet az Azure virtuális hálózat létrehozása
Létrehozhat egy helyet az Active Directoryban, amely a virtuális hálózathoz a megfelelő hálózati régiót jelöli. Ez a hely segítségével optimalizálhatja a hitelesítés, a replikáció és az egyéb DC hely műveletek. A következő lépések bemutatják, hogyan hozhat létre egy helyet és további háttér, lásd: [hozzáadása egy új helyet](https://technet.microsoft.com/library/cc781496.aspx).

1. Nyissa meg az Active Directory – helyek és szolgáltatások: **Kiszolgálókezelő** > **eszközök** > **Active Directory – helyek és szolgáltatások**.
2. Hozzon létre egy helyet, a régiót, amelyben létrehozta az Azure virtuális hálózat képviselő: kattintson a **helyek** > **művelet** > **új hely** > típus a az új webhely, például Azure Velünk nyugati neve > Válassza ki a helykapcsolatot > **OK**.
3. Hozzon létre egy alhálózatot, és rendelje hozzá az új hely: kattintson duplán a **helyek** > kattintson a jobb gombbal **alhálózatok** > **új alhálózat** > írja be az IP-címtartományt, a virtuális hálózat (például 10.1.0.0/16 a forgatókönyv diagramon) > Válassza ki az új Azure hely > **OK**.

## <a name="create-an-azure-virtual-network"></a>Az Azure virtuális hálózat létrehozása
Hozzon létre egy Azure virtuális hálózatra, és a pont-pont virtuális magánhálózat beállításához, kövesse a cikkben szereplő [pont-pont kapcsolatot](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md). 

A virtuális hálózati átjárót, pont-pont biztonságos VPN-kapcsolat létrehozásához is konfigurálhatja. A pont-pont VPN kapcsolatot az új virtuális hálózat és egy helyszíni VPN-eszköz létrehozása. Útmutatásért lásd: [konfigurálja a virtuális hálózati átjáró](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md).

## <a name="create-azure-vms-for-the-dc-roles"></a>A tartományvezérlő a szerepkörök Azure virtuális gépek létrehozása
A tartományvezérlő futtatja a virtuális gépek létrehozásához ismételje meg a [Windows virtuális gép létrehozása az Azure portálon](../virtual-machines/windows/quick-create-portal.md) igény szerint. A hibatűrés és a redundancia legalább két virtuális tartományvezérlők telepítése. Ha az Azure virtuális hálózat legalább két tartományvezérlő hasonló módon konfigurált, a virtuális gépek rendelkezésre állási készlet továbbfejlesztett hibatűrést ezek azok a tartományvezérlők futtató elhelyezhet.

A virtuális gépek létrehozása az Azure-portál helyett a Windows PowerShell használatával: [a Azure PowerShell szolgáltatás használatával hozzon létre, és előre konfigurálása a Windows-alapú virtuális gépek](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Egy statikus IP-címet lefoglalni a tartományvezérlő szerepkör által futtatott virtuális gépek. Egy statikus IP-címet lefoglalni, töltse le a Microsoft Webplatform-telepítő és [Azure PowerShell telepítése](/powershell/azure/overview) és futtassa a Set-AzureStaticVNetIP parancsmagot. Példa:

````
Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM
````
Egy statikus IP-cím beállításával kapcsolatos további információkért lásd: [egy statikus belső IP-cím konfigurálása a virtuális gépek](../virtual-network/virtual-networks-reserved-private-ip.md).

## <a name="install-ad-ds-on-azure-vms"></a>Active Directory tartományi szolgáltatások telepítése Azure virtuális gépeken
Jelentkezzen be egy virtuális Gépet, és ellenőrizze, hogy van kapcsolat a webhelyek közötti VPN- vagy ExpressRoute kapcsolattal erőforrások között a helyi hálózaton. Majd telepítse az Active Directory tartományi szolgáltatások az Azure virtuális gépeken. Használhatja ugyanazt a folyamatot, amellyel további tartományvezérlő telepítése a helyi hálózaton (felhasználói felület, a Windows PowerShell vagy válaszfájl). Active Directory tartományi szolgáltatások telepítéséhez, ellenőrizze, hogy megadja az új kötet AD adatbázisát, naplófájlok és SYSVOL helyét. Ha az AD DS-telepítés frissítő van szüksége, tekintse meg [Active Directory tartományi szolgáltatások telepítése (100-as szint)](https://technet.microsoft.com/library/hh472162.aspx) vagy [egy replika Windows Server 2012 tartományvezérlő telepítése meglévő tartományban (200-as szint)](https://technet.microsoft.com/library/jj574134.aspx).

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>A virtuális hálózat DNS-kiszolgáló
1. Beolvasni a virtuális hálózati nevek listája a [Azure-portálon](https://portal.azure.com), keresse meg *virtuális hálózatok*, majd jelölje be **virtuális hálózatok** megjelenítéséhez. 
2. Nyissa meg a virtuális hálózatot szeretne kezelni, majd [a virtuális hálózat DNS-kiszolgáló IP-címének újrakonfigurálása](../virtual-network/virtual-network-manage-network.md#dns-servers) a statikus IP-címek, a replika tartományvezérlők helyett a helyi DNS-kiszolgálók az IP-címet hozzárendelni.
3. Annak érdekében, hogy az összes replika tartományvezérlő virtuális gépeket a virtuális hálózat használatára van konfigurálva a DNS-kiszolgálók a virtuális hálózaton:
  1. Válassza ki **virtuális gépek**.
  2. A virtuális gépeket, majd válassza ki és **indítsa újra a**. 
  3. Várjon, amíg a virtuális gép **futtató** újra, és jelentkezzen be.

## <a name="create-vms-for-application-servers"></a>Virtuális gépek létrehozása az alkalmazáskiszolgálók

Hozzon létre virtuális gépek üzemeltetéséhez az Alkalmazáskiszolgáló szerepkört, ismételje [Windows virtuális gép létrehozása az Azure portálon](../virtual-machines/windows/quick-create-portal.md) igény szerint. A virtuális gépek létrehozása helyett az Azure-portál a Microsoft PowerShell használatával: [használata Azure PowerShell létrehozása és konfigurálása a Windows-alapú virtuális gépek](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). A következő táblázat a javasolt beállításait tartalmazza.

| Beállítás | Értékek |
| --- | --- |
|  **Kép kiválasztása** | Windows Server 2012 R2 Datacenter |
|  **Virtuálisgép-konfiguráció** |<p>Virtuális gép neve: (Például AppServer1) egycímkés nevet írja be.</p><p>Új felhasználónevet: Írja be annak a felhasználónak a nevére. Ez a felhasználó a virtuális Gépen a helyi Rendszergazdák csoport tagja lesz. Szüksége lesz erre a névre kattintva először jelentkezzen be a virtuális Gépet. A beépített Rendszergazda fiók nem fog működni.</p><p>Új jelszó megerősítése: Adjon meg egy jelszót</p> |
|  **Virtuálisgép-konfiguráció** |<p>Felhőalapú szolgáltatás: Válassza ki **hozzon létre egy új felhőalapú szolgáltatás** az első virtuális gép, és válassza ki az adott felhőalapú szolgáltatás néven további virtuális gépek létrehozásakor az alkalmazás fogja futtatni.</p><p>Felhőalapú szolgáltatás DNS-neve: Globálisan egyedi nevet adjon meg.</p><p>Régió/Affinitáscsoport/virtuális hálózat: Adja meg a virtuális hálózat nevét (például WestUSVNet).</p><p>Tárfiók: Válassza ki **automatikusan létrehozott tárfiók használata** az első virtuális gép, és válassza ki azt, hogy ugyanazon tárfiók neve további virtuális gépek létrehozásakor az alkalmazás fogja futtatni.</p><p>Rendelkezésre állási csoportot: Válassza ki **egy rendelkezésre állási csoport létrehozása**.</p><p>Rendelkezésre állási csoport neve: a rendelkezésre állási csoporthoz az első virtuális gép létrehozásakor, és válassza ki, hogy ugyanaz az további virtuális gépek létrehozásakor nevet írja be nevét.</p> |
|  **Virtuálisgép-konfiguráció** |<p>Válassza ki <b>a Virtuálisgép-ügynök telepítése</b> és bármely más bővítmények van szüksége.</p> |
  
Minden virtuális gép üzembe helyezése után jelentkezzen be, és csatlakoztatása a tartományhoz. 
1. A **Kiszolgálókezelő** &gt; **helyi kiszolgáló** &gt; **munkacsoport** &gt; **módosítása...** , jelölje be **tartomány**.
2. Adja meg a helyi tartomány nevét. 
3. Adjon meg egy tartományi felhasználó hitelesítő adataival.
4. Indítsa újra a virtuális Gépet.

## <a name="additional-resources"></a>További források

* A Windows PowerShell használatával kapcsolatos további információkért lásd: [Ismerkedés az Azure-parancsmagok](/powershell/azure/overview) és [Azure parancsmag-referencia](/powershell/azure/get-started-azureps).
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
