---
title: "Beágyazott virtualizálási Azure virtuális gépek engedélyezése |} Microsoft Docs"
description: "Beágyazott virtualizálási Azure virtuális gépek engedélyezése"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: philmea
manager: timlt
ms.author: philmea
ms.date: 10/09/2017
ms.topic: howto
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: a157e612ca4fca06a57df478eaa20793c8b35d60
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2017
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Egy Azure virtuális gép beágyazott virtualizálás engedélyezése

Beágyazott virtualizálási az Dv3 és Ev3 lépéseket az Azure virtuális gépek esetén támogatott. Ez a funkció helyzeteket, például a fejlesztői, tesztelési, képzés és bemutató környezetekben nagy rugalmasságot biztosít. 

Ez a cikk lépéseit egy Azure virtuális gépen beágyazott virtualizálási engedélyezése és konfigurálása, hogy a Vendég virtuális gép internetkapcsolattal.

## <a name="create-a-dv3-or-ev3-series-azure-vm"></a>Dv3 vagy Ev3 több Azure virtuális gép létrehozása

Hozzon létre egy új Windows Server 2016 Azure virtuális Gépet, és a Dv3 vagy Ev3 sorozatból méret kiválasztása. Győződjön meg arról, hogy elég nagy legyen a Vendég virtuális gépek igények támogatása méret kiválasztása. A jelen példában használjuk D3_v3 Azure virtuális gép méretét. 

Megtekintheti a regionális Dv3 vagy Ev3 adatsorozat virtuális gépek rendelkezésre állásának [Itt](https://azure.microsoft.com/regions/services/).

>[!NOTE]
>
>Egy új virtuális gép létrehozása a részletes utasításokért lásd: [létrehozása és kezelése a Windows virtuális gépek az Azure PowerShell modul](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/tutorial-manage-vm)
    
## <a name="connect-to-your-azure-vm"></a>Csatlakozás az Azure virtuális gép

Hozzon létre egy távoli asztali kapcsolatot a virtuális géppel.

1. Kattintson a **Csatlakozás** gombra a virtuális gép tulajdonságai között. A rendszer létrehoz és letölt egy Remote Desktop Protocol-fájlt (.rdp-fájlt).

2. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz. Ha a rendszer kéri, akkor kattintson a **Csatlakozás** gombra. Mac rendszerben szüksége van egy RDP-kliensre, mint például a Mac App Store áruházban elérhető [távoli asztali ügyfélre](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12).

3. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót, majd kattintson az **OK** gombra.

4. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A csatlakozás folytatásához kattintson az **Igen** vagy **Folytatás** gombra.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Engedélyezze a Hyper-V szolgáltatást az Azure virtuális gépen
Beállíthatja, hogy ezeket a beállításokat manuálisan vagy egy PowerShell-parancsfájl konfigurálásának automatizálásához adtunk.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>1. lehetőség: A PowerShell parancsfájl segítségével beágyazott virtualizálási konfigurálása
Egy PowerShell-parancsfájlt egy Windows Server 2016 gazdagépen beágyazott virtualizálás engedélyezése érhető el a [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). A parancsfájl ellenőrzi a szükséges előfeltételek, és ezután beágyazott virtualizálási konfigurálja az Azure virtuális Géphez. Az Azure virtuális gép újraindítására szükség, a konfigurálás befejezéséhez. Ez a parancsfájl más környezetekben is működik, de nem garantált. Tekintse meg az Azure-on futó beágyazott Virtualization egy élő videó az áttekintésről Azure blogbejegyzést! https://aka.MS/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>2. lehetőség: Beágyazott virtualizálási kézi konfigurálása

1. Az Azure virtuális gépen nyissa meg a Powershellt rendszergazdaként. 

2. Engedélyezze a Hyper-V szolgáltatást és a felügyeleti eszközök.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Ez a parancs az Azure virtuális gép újraindul. A távoli ASZTAL kapcsolaton keresztül a újraindítás során elvész.
    
3. Az Azure virtuális gép újraindítása után kapcsolódjon a virtuális gép RDP Funkciót használnak.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>A Vendég virtuális gép internetkapcsolat beállítása
Hozzon létre egy új virtuális hálózati adaptert a Vendég virtuális gépen, és ahhoz, hogy internetkapcsolat NAT átjáró konfigurálásához.

### <a name="create-a-nat-virtual-network-switch"></a>NAT virtuális hálózati kapcsoló létrehozása

1. Az Azure virtuális gépen nyissa meg a Powershellt rendszergazdaként.
   
2. Belső kapcsolót hoz létre.

    ```powershell
    New-VMSwitch -SwitchName "InternalNATSwitch" -SwitchType Internal
    ```

3. Tekintse meg a kapcsoló tulajdonságait, és jegyezze fel az új adapter ifIndex.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Jegyezze fel a "ifIndex" a most létrehozott virtuális kapcsolóhoz.
    
4. Hozzon létre egy IP-címet a NAT-átjáró.
    
Az átjáró konfigurálásához bizonyos adatok a hálózattal kapcsolatos szükségesek:    
  * IP-cím - az NAT átjáró IP-cím legyen az alapértelmezett átjáró címét, a virtuális hálózati alhálózat IPv4 vagy IPv6-címet adja meg. Az általános űrlapon a.b.c.1 (például "192.168.0.1"). Amíg a végső pozíciója nem kell ikonra.1, akkor általában (alapul előtag hossza). Általában egy a RFC 1918 magánhálózati címtartománnyal kell használnia. 
  * PrefixLength – az alhálózati előtag hossza meghatározása a helyi alhálózat méretét (alhálózati maszk). Az alhálózati előtag hossza 0 és 32 közötti egész számot lesz. 0 volna leképezéséhez az interneten, 32 csak lehetővé teszi egy leképezett IP-cím. Közös és közötti értékeket 24 12 attól függően, hogy hány IP-címet kell csatlakoztatni a hálózati címfordítást. A közös PrefixLength: 24 – Ez a 255.255.255.0 alhálózati maszkkal.
  * InterfaceIndex - **ifIndex** pedig az előző lépésben létrehozott virtuális kapcsoló. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>A NAT-hálózat létrehozása

Az átjáró konfigurálásához szüksége lesz a hálózati és a NAT-átjáró kapcsolatban nyújtanak információt:
  * Név – Ez a NAT-hálózat nevét. 
  * InternalIPInterfaceAddressPrefix - mind a NAT átjáró IP-előtagja, az újabb, valamint a NAT alhálózati előtag hossza a fent a NAT alhálózati előtag ismerteti. Az általános űrlapon a.b.c.0/NAT alhálózati előtaghossz lesz. 

A PowerShellben hozzon létre egy új NAT hálózatot.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>A Vendég virtuális gép létrehozása

1. Nyissa meg a Hyper-V kezelőjében, és hozzon létre egy új virtuális gépet. A létrehozott új belső hálózatot a virtuális gép konfigurálásához.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Operációs rendszer telepítéséhez a Vendég virtuális gépen.
    
    >[!NOTE] 
    >
    >A virtuális Gépre telepítendő operációs rendszer telepítési adathordozója van szüksége. Ebben az esetben használjuk a Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>Rendeljen egy IP-címet a Vendég virtuális gépen

A Vendég virtuális gépen, vagy manuálisan egy statikus IP-cím beállítása a Vendég virtuális gépen vagy a DHCP konfigurálása az Azure virtuális gépen az IP-cím hozzárendelése dinamikusan hozzárendelheti az IP-cím.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>1. lehetőség: A DHCP dinamikusan IP-cím hozzárendelése a Vendég virtuális gép konfigurálása
DHCP konfigurálása a dinamikus cím hozzárendelése a gazdagép virtuális gépen az alábbi lépésekkel.

#### <a name="install-dchp-server-on-the-azure-vm"></a>DHCP-kiszolgáló telepítése az Azure virtuális gépen

1. Nyissa meg a Kiszolgálókezelőt. Az irányítópulton kattintson **szerepkörök és szolgáltatások hozzáadása**. A szerepkörök hozzáadása és szolgáltatások varázsló jelenik meg.
  
2. A varázslóban kattintson a **következő** csak a kiszolgálói szerepkörök lapon.
  
3. Kattintással jelölje ki a **DHCP-kiszolgáló** jelölőnégyzetet, kattintson a **szolgáltatások hozzáadása**, és kattintson a**következő** mindaddig, amíg a varázsló befejezéséhez.
  
4. Kattintson az **Install** (Telepítés) gombra.

#### <a name="configure-a-new-dhcp-scope"></a>Új DHCP-hatókör konfigurálása

1. Nyissa meg a DHCP-kezelőt.
  
2. A navigációs ablaktáblán bontsa ki a kiszolgáló nevét, kattintson a jobb gombbal **IPv4**, és kattintson a **új hatókör**. Az új hatókör varázsló jelenik meg, kattintson a **következő**.
  
3. Adjon meg egy nevet és leírást a hatókör, és kattintson a **következő**.
  
4. Adja meg az IP-tartomány a DHCP-kiszolgáló (például 192.168.0.100 való 192.168.0.200).
  
5. Kattintson a **következő** csak az alapértelmezett átjáró oldalon. Adja meg az imént létrehozott korábbi (például 192.168.0.1) IP-címet alapértelmezett átjáróként.
  
6. Kattintson a **következő** mindaddig, amíg a varázsló befejeződött, hagyja az összes alapértelmezett értéket, majd kattintson az **Befejezés**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>2. lehetőség: Manuális módon állítsa be a statikus IP-címet a Vendég virtuális gépen
Ha a DHCP dinamikusan rendelhető hozzá egy IP-cím toe a Vendég virtuális gépen nincs konfigurálva, kövesse az alábbi lépéseket egy statikus IP-címének beállítása.

1. Az Azure virtuális gépen nyissa meg a Powershellt rendszergazdaként.

2. Kattintson a jobb gombbal a Vendég virtuális gépen, és kattintson a Csatlakozás gombra.

3. Jelentkezzen be a Vendég virtuális gépen.

4. A Vendég virtuális gépen nyissa meg a hálózati és megosztási központ.

5. Konfigurálja a hálózati adaptert egy címet a NAT-hálózat az előző szakaszban létrehozott tartományba esik.

Ebben a példában egy címet fogja használni a 192.168.0.0/24 közé.

## <a name="test-connectivity-in-guest-virtual-machine"></a>Kapcsolat tesztelése a Vendég virtuális gépen

A Vendég virtuális gépen nyissa meg a böngészőt, és keresse meg a weblap.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)
