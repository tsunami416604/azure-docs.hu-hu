---
title: Beágyazott virtualizálás engedélyezése az Azure virtuális gépeken
description: Beágyazott virtualizálás engedélyezése az Azure virtuális gépeken
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: 16f5bed5a2342bb1d120d0d3dc853e0bc44376dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033119"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Beágyazott virtualizálás engedélyezése Azure-beli virtuális gépben

A beágyazott virtualizációt számos Azure virtuálisgép-család támogatja. Ez a funkció nagy rugalmasságot biztosít az olyan forgatókönyvek támogatásában, mint a fejlesztési, tesztelési, képzési és demonstrációs környezetek.   

Ez a cikk a Hyper-V engedélyezése egy Azure virtuális gépen, és az internetkapcsolat konfigurálása, hogy a vendég virtuális gép.

## <a name="create-a-nesting-capable-azure-vm"></a>Beágyazási alkalmas Azure-gép létrehozása

Hozzon létre egy új Windows Server 2016 Azure virtuális gép.  A gyors referencia érdekében minden v3-as virtuális gép támogatja a beágyazott virtualizációt. A beágyazást támogató virtuális gépméretek teljes listáját az [Azure Compute Unit cikkben](acu.md)olvashatja el.

Ne felejtse el olyan virtuális gépméretet választani, amely elég nagy ahhoz, hogy egy vendég virtuális gép igényeit támogassa. Ebben a példában egy D3_v3 méretű Azure virtuális gép. 

A Dv3 vagy Ev3 sorozatú virtuális gépek regionális elérhetőségét [itt](https://azure.microsoft.com/regions/services/)tekintheti meg.

>[!NOTE]
>
>Az új virtuális gépek létrehozásáról a [Windows virtuális gépek létrehozása és kezelése az Azure PowerShell-modullal című](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm) témakörben talál részletes útmutatást.
    
## <a name="connect-to-your-azure-vm"></a>Csatlakozás az Azure-beli virtuális géphez

Hozzon létre egy távoli asztali kapcsolatot a virtuális géppel.

1. Kattintson a **Csatlakozás** gombra a virtuális gép tulajdonságai között. A rendszer létrehoz és letölt egy Remote Desktop Protocol-fájlt (.rdp-fájlt).

2. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz. Ha a rendszer kéri, akkor kattintson a **Csatlakozás** gombra. Mac rendszerben szüksége van egy RDP-kliensre, mint például a Mac App Store áruházban elérhető [távoli asztali ügyfélre](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12).

3. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót, majd kattintson az **OK** gombra.

4. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A csatlakozás folytatásához kattintson az **Igen** vagy **Folytatás** gombra.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>A Hyper-V funkció engedélyezése az Azure virtuális gépen
Ezeket a beállításokat manuálisan is konfigurálhatja, vagy egy PowerShell-parancsfájlt biztosítottunk a konfiguráció automatizálásához.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>1. lehetőség: Beágyazott virtualizáció konfigurálása PowerShell-parancsfájl használatával
A Windows Server 2016-állomásbeágyazott virtualizálását engedélyező PowerShell-parancsfájl a [GitHubon](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested)érhető el. A parancsfájl ellenőrzi az előfeltételeket, majd konfigurálja a beágyazott virtualizációt az Azure virtuális gépen. Az Azure virtuális gép újraindítása szükséges a konfiguráció befejezéséhez. Ez a parancsfájl más környezetekben is működhet, de nem garantált. Tekintse meg az Azure blogbejegyzést egy élő videóbemutatóval az Azure-ban futó beágyazott virtualizációról! https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>2. lehetőség: Beágyazott virtualizáció manuális konfigurálása

1. Az Azure virtuális gép, nyissa meg a PowerShell rendszergazdaként. 

2. Engedélyezze a Hyper-V funkciót és a felügyeleti eszközöket.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Ez a parancs újraindítja az Azure virtuális gép. Az újraindítási folyamat során megszakad az RDP-kapcsolat.
    
3. Az Azure virtuális gép újraindítása után újra csatlakozhat a virtuális géphez rdp használatával.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>Internetkapcsolat beállítása a vendég virtuális géphez
Hozzon létre egy új virtuális hálózati adaptert a vendég virtuális gépszámára, és konfiguráljon egy NAT átjárót az internetkapcsolat engedélyezéséhez.

### <a name="create-a-nat-virtual-network-switch"></a>NAT virtuális hálózati kapcsoló létrehozása

1. Az Azure virtuális gép, nyissa meg a PowerShell rendszergazdaként.
   
2. Hozzon létre egy belső kapcsolót.

    ```powershell
    New-VMSwitch -Name "InternalNAT" -SwitchType Internal
    ```

3. Tekintse meg a kapcsoló tulajdonságait, és jegyezze fel az új adapter ifIndex indexét.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Vegye figyelembe az "ifIndex" a virtuális kapcsoló, amit most létrehozott.
    
4. Hozzon létre egy IP-címet a NAT-átjáróhoz.
    
Az átjáró konfigurálásához a hálózattal kapcsolatos információkra van szükség:    
  * IP-cím – A NAT-átjáró IP-címe a virtuális hálózati alhálózat alapértelmezett átjárócímeként használandó IPv4- vagy IPv6-címet adja meg. A generikus űrlap az a.b.c.1 (például "192.168.0.1"). Bár a végső pozíciónak nem kell 0,1-nek lennie, általában az (az előtag hossza alapján). Általában az 1918-as számú RFC privát hálózati címterületet kell használnia. 
  * ElőtagLength - Az alhálózati előtag hossza határozza meg a helyi alhálózat méretét (alhálózati maszk). Az alhálózati előtag hossza 0 és 32 közötti egész érték lesz. 0 lenne térkép az egész internet, 32 csak akkor teszi lehetővé egy térképes IP. A közös értékek 24 és 12 között mozognak attól függően, hogy hány IP-t kell csatolni a nat-hoz. A közös előtaghossz 24 - ez egy 255.255.255.0 alhálózati maszk.
  * InterfaceIndex - **ifIndex** az előző lépésben létrehozott virtuális kapcsoló interfészindexe. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>A NAT-hálózat létrehozása

Az átjáró konfigurálásához információt kell megadnia a hálózatról és a NAT átjáróról:
  * Name - Ez a NAT-hálózat neve. 
  * InternalIPInterfaceAddressPrefix - A NAT alhálózati előtag a NAT átjáró IP-előtagja felülről, valamint a NAT alhálózati előtag hossza felülről. Az általános képernyő a.b.c.0/NAT alhálózati előtag hossza lesz. 

A PowerShellben hozzon létre egy új NAT-hálózatot.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>A vendég virtuális gép létrehozása

>[!IMPORTANT] 
>
>Az Azure vendégügynök nem támogatott beágyazott virtuális gépeken, és problémákat okozhat mind a gazdagép, mind a beágyazott virtuális gépek. Ne telepítse az Azure-ügynököt beágyazott virtuális gépekre, és ne használjon lemezképet a beágyazott virtuális gépek létrehozásához, amelyek már telepítve van az Azure-vendégügynök.

1. Nyissa meg a Hyper-V Manager t, és hozzon létre egy új virtuális gépet. Állítsa be a virtuális gépet úgy, hogy a létrehozott új belső hálózatot használja.
    
    ![Hálózati konfiguráció](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Telepítsen egy operációs rendszert a vendég virtuális gépre.
    
    >[!NOTE] 
    >
    >A virtuális gépre történő telepítéshez az operációs rendszer telepítéséhez telepítési adathordozóra van szükség. Ebben az esetben a Windows 10 Enterprise-t használjuk.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>IP-cím hozzárendelése a vendég virtuális géphez

Ip-címet rendelhet a vendég virtuális géphez, vagy manuálisan állít be egy statikus IP-címet a vendég virtuális gépen, vagy konfigurálja a DHCP-t az Azure virtuális gépen az IP-cím dinamikus hozzárendeléséhez.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>1. lehetőség: A DHCP konfigurálása ip-cím dinamikus hozzárendeléséhez a vendég virtuális géphez
Az alábbi lépéseket követve konfigurálhatja a DHCP-t az állomás virtuális gépen a dinamikus címhozzárendeléshez.

#### <a name="install-dchp-server-on-the-azure-vm"></a>DcHP-kiszolgáló telepítése az Azure virtuális gépre

1. Nyissa meg a Kiszolgálókezelőt. Az irányítópulton kattintson a **Szerepkörök és szolgáltatások hozzáadása**elemre. A Szerepkörök és szolgáltatások hozzáadása varázsló jelenik meg.
  
2. A varázslóban kattintson a **Tovább** gombra a Kiszolgálói szerepkörök lapig.
  
3. Jelölje be a **DHCP-kiszolgáló** jelölőnégyzetet, kattintson a **Szolgáltatások hozzáadása**gombra, majd a **Tovább** gombra, amíg be nem fejezi a varázslót.
  
4. Kattintson a **Telepítés gombra.**

#### <a name="configure-a-new-dhcp-scope"></a>Új DHCP-hatókör konfigurálása

1. Nyissa meg a DHCP-kezelőt.
  
2. A navigációs ablakban bontsa ki a kiszolgáló nevét, kattintson a jobb gombbal az **IPv4**elemre, és válassza az **Új hatókör parancsot.** Megjelenik az Új hatókör varázsló, kattintson a **Tovább**gombra.
  
3. Adja meg a hatókör nevét és leírását, majd kattintson a **Tovább**gombra.
  
4. Adja meg a DCHP-kiszolgáló IP-tartományát (például 192.168.0.100 és 192.168.0.200).
  
5. Kattintson a **Tovább** gombra az Alapértelmezett átjáró lapig. Írja be a korábban létrehozott IP-címet (például 192.168.0.1) alapértelmezett átjáróként, majd kattintson a **Hozzáadás gombra.**
  
6. Kattintson a **Tovább** gombra, amíg a varázsló be nem fejeződik, meghagyva az összes alapértelmezett értéket, majd kattintson a **Befejezés**gombra.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>2. lehetőség: Statikus IP-cím manuális beállítása a vendég virtuális gépen
Ha nem úgy állította be a DHCP-t, hogy dinamikusan rendeljen IP-címet a vendég virtuális géphez, az alábbi lépésekkel állíthat be statikus IP-címet.

1. Az Azure virtuális gép, nyissa meg a PowerShell rendszergazdaként.

2. Kattintson a jobb gombbal a vendég virtuális gépre, és válassza a Csatlakozás menüpontot.

3. Jelentkezzen be a vendég virtuális gépre.

4. A vendég virtuális gépen nyissa meg a hálózati és megosztási központot.

5. Konfigurálja a hálózati adaptert az előző szakaszban létrehozott NAT-hálózat tartományán belüli címhez.

Ebben a példában a 192.168.0.0/24 tartományban lévő címet fogja használni.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Kapcsolat tesztelése a vendég virtuális gépen

A vendég virtuális gépen nyissa meg a böngészőt, és lépjen egy weblapra.
    ![VendégVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

## <a name="set-up-intranet-connectivity-for-the-guest-virtual-machine"></a>Intranetes kapcsolat beállítása a vendég virtuális géphez

A vendégvirtuális gépek és az Azure-beli virtuális gépek közötti transzparens kapcsolat engedélyezésével kapcsolatos tudnivalókért tekintse meg [ezt a dokumentumot.](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization-azure-virtual-network)
