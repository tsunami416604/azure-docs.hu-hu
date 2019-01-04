---
title: Beágyazott virtualizálás az Azure Virtual machines gépeken engedélyezése |} A Microsoft Docs
description: Az Azure Virtual machines gépeken beágyazott virtualizálás engedélyezése
services: virtual-machines-windows
documentationcenter: virtual-machines
author: zr-msft
manager: jeconnoc
ms.author: zarhoads
ms.date: 10/09/2017
ms.topic: howto
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: 1caf8455816bd86411a75821ee33516e8aad5de3
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "54002263"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Egy Azure virtuális Gépen a beágyazott virtualizálás engedélyezése

Beágyazott virtualizálás számos Azure-beli virtuálisgép-családok használata támogatott. Ezt a képességet például fejlesztési, tesztelési, betanítási és bemutató forgatókönyvek támogatása nagyfokú rugalmasságot biztosít.   

Ez a cikk végigvezeti a Hyper-V-beli virtuális gépen engedélyezése és konfigurálása, hogy a Vendég virtuális gép az internetkapcsolat.

## <a name="create-a-nesting-capable-azure-vm"></a>A beágyazási képesek Azure virtuális gép létrehozása

Hozzon létre egy új Windows Server 2016 az Azure virtuális Gépet.  A gyors tengelynek összes v3-as virtuális gép támogatja a beágyazott virtualizálás. A virtuális gép teljes listáját, hogy a támogatási beágyazási méreteit, tekintse meg a [Azure számítási egységek cikk](acu.md).

Fontos, hogy válassza ki a virtuális gép méretét elég nagy legyen a Vendég virtuális gép igényeinek támogatásához. Ebben a példában használjuk egy Azure virtuális gép D3_v3 méretét. 

Megtekintheti a Dv3 és Ev3 sorozatú virtuális gépek mely régiókban [Itt](https://azure.microsoft.com/regions/services/).

>[!NOTE]
>
>Új virtuális gép létrehozásával kapcsolatos részletes utasításokért lásd: [létrehozása és kezelése a Windows virtuális gépek az Azure PowerShell modullal](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
    
## <a name="connect-to-your-azure-vm"></a>Csatlakozzon az Azure virtuális Géphez

Hozzon létre egy távoli asztali kapcsolatot a virtuális géppel.

1. Kattintson a **Csatlakozás** gombra a virtuális gép tulajdonságai között. A rendszer létrehoz és letölt egy Remote Desktop Protocol-fájlt (.rdp-fájlt).

2. Nyissa meg az RDP-fájlt a virtuális géphez való csatlakozáshoz. Ha a rendszer kéri, akkor kattintson a **Csatlakozás** gombra. Mac rendszerben szüksége van egy RDP-kliensre, mint például a Mac App Store áruházban elérhető [távoli asztali ügyfélre](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12).

3. Írja be a virtuális gép létrehozásakor megadott felhasználónevet és jelszót, majd kattintson az **OK** gombra.

4. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A csatlakozás folytatásához kattintson az **Igen** vagy **Folytatás** gombra.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Engedélyezze a Hyper-V szolgáltatást az Azure virtuális gépen
Konfigurálhatja ezeket a beállításokat manuálisan vagy adtunk meg egy PowerShell-parancsprogram segítségével automatizálhatja a konfigurálását.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>1. lehetőség: Beágyazott virtualizálás konfigurálása egy PowerShell-parancsprogram használatával
Egy PowerShell-parancsfájlt egy Windows Server 2016 gazdagépen beágyazott virtualizálás engedélyezése érhető el az [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested). A parancsfájl ellenőrzi az előfeltételeket, és ezután beállítja a beágyazott virtualizálás az Azure virtuális gépen. Az Azure virtuális gép újraindítására szükség a konfiguráció befejezéséhez. Ez a szkript más környezetekben is működhet, azonban nem garantált. Tekintse meg az Azure blog bejegyzésében az élő videó bemutatója a beágyazott virtualizálás az Azure-ban! https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>2. lehetőség: Beágyazott virtualizálás kézi konfigurálása

1. Az Azure virtuális gépen nyissa meg a Powershellt rendszergazdaként. 

2. Engedélyezze a Hyper-V szolgáltatást és a felügyeleti eszközöket.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >Ez a parancs az Azure virtuális gép újraindul. Az RDP-kapcsolatot az újraindítás során elvesznek.
    
3. Az Azure virtuális gép újraindítása után a virtuális gép RDP-vel kapcsolódjon újra.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>A Vendég virtuális gép internet kapcsolat beállítása
Hozzon létre egy új virtuális hálózati adapter a Vendég virtuális gép, és internetes kapcsolat NAT átjáró konfigurálásához.

### <a name="create-a-nat-virtual-network-switch"></a>A NAT virtuális hálózati kapcsoló létrehozása

1. Az Azure virtuális gépen nyissa meg a Powershellt rendszergazdaként.
   
2. Hozzon létre egy belső kapcsoló.

    ```powershell
    New-VMSwitch -Name "InternalNATSwitch" -SwitchType Internal
    ```

3. A kapcsoló tulajdonságainak megtekintése, és jegyezze fel az új adapter a ifIndex.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >Jegyezze fel a "ifIndex" a létrehozott virtuális kapcsolóhoz.
    
4. Hozzon létre egy IP-címet a NAT-átjáróhoz.
    
Az átjáró konfigurálásához, a hálózattal kapcsolatos tudnia kell néhány dolgot:    
  * IP-cím – a NAT-átjáró IP-cím megadja az IPv4 vagy IPv6-címet használja az alapértelmezett átjáró címét a virtuális hálózat alhálózatához. Az általános űrlapon a.b.c.1 (például "192.168.0.1"). Amíg a végső pozíció nem kell lennie a ikonra.1, ez általában akkor (alapján előtag hossza). Általában az RFC 1918 magánhálózati címtartomány kell használnia. 
  * A PrefixLength – az alhálózati előtag hossza határozza meg a helyi alhálózat méretét (alhálózati maszk). Az alhálózati előtag hossza 0 és 32 közötti egész szám lesz. 0 lenne leképezéséhez az interneten, 32 csak egy leképezett IP lehetővé tenné. Közös és közötti értékeket 24-től 12 attól függően, hogy hány IP-címek kell csatolni a hálózati címfordítást. Egy közös PrefixLength 24 – Ez a 255.255.255.0 alhálózati maszkkal.
  * InterfaceIndex - **ifIndex** pedig az előző lépésben létrehozott virtuális kapcsoló. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>A NAT-hálózat létrehozása

Az átjáró konfigurálásához, szüksége lesz a hálózat és a NAT-átjáró kapcsolatos adatok megadása:
  * Név – a NAT-hálózat neve. 
  * InternalIPInterfaceAddressPrefix – a NAT alhálózati előtag ismerteti mind a NAT-átjáró IP-előtag a fent, valamint a NAT alhálózati előtag hossza a fent. Az általános űrlapon a.b.c.0/NAT alhálózati előtaghossz lesz. 

A PowerShell hozzon létre egy új NAT-hálózatot.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>A Vendég virtuális gép létrehozása

1. Nyissa meg a Hyper-V kezelőjében, és hozzon létre egy új virtuális gépet. Konfigurálja a virtuális gép használata a létrehozott új belső hálózatot.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. Operációs rendszer telepítéséhez a Vendég virtuális gépen.
    
    >[!NOTE] 
    >
    >Szüksége lesz a virtuális gépre telepítendő operációs rendszer telepítési adathordozója. Ebben az esetben használjuk a Windows 10 Enterprise.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>IP-cím hozzárendelése a Vendég virtuális gépen

A Vendég virtuális gép kézzel statikus IP-cím beállítása a Vendég virtuális gépen, vagy a DHCP konfigurálása az Azure virtuális gépen az IP-cím hozzárendelése dinamikusan IP-címet rendelhet hozzá.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>1. lehetőség: A DHCP dinamikusan IP-cím hozzárendelése a Vendég virtuális gép konfigurálása
DHCP konfigurálása a gazdagép virtuális gép dinamikus címet osszon meg az alábbi lépésekkel.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Az Azure virtuális Gépen futó DHCP-kiszolgáló telepítése

1. Nyissa meg a Kiszolgálókezelőt. Az irányítópulton kattintson **szerepkörök és szolgáltatások hozzáadása**. A szerepkörök hozzáadása és a szolgáltatások varázsló jelenik meg.
  
2. A varázslóban kattintson a **tovább** csak a kiszolgálói szerepkörök lap.
  
3. Kattintással jelölje ki a **DHCP-kiszolgáló** jelölőnégyzetet, kattintson a **szolgáltatások hozzáadása**, és kattintson a **tovább** mindaddig, amíg a varázsló befejezése.
  
4. Kattintson az **Install** (Telepítés) gombra.

#### <a name="configure-a-new-dhcp-scope"></a>Új DHCP-hatókör konfigurálása

1. Nyissa meg a DHCP-kezelőt.
  
2. A navigációs ablaktáblán bontsa ki a kiszolgáló nevét, kattintson a jobb gombbal **IPv4**, és kattintson a **új hatókör**. Az új hatókör varázsló jelenik meg, kattintson a **tovább**.
  
3. Adjon meg egy nevet és leírást a hatókör, és kattintson a **tovább**.
  
4. A DHCP-kiszolgáló (például 192.168.0.100 való 192.168.0.200) határozza meg egy IP-címtartományt.
  
5. Kattintson a **tovább** amíg az alapértelmezett átjáró oldal. Adja meg a korábban (Ha például 192.168.0.1) létrehozott IP-címet alapértelmezett átjáróként, majd kattintson a **Hozzáadás**.
  
6. Kattintson a **tovább** mindaddig, amíg a varázsló befejeződött, és az összes alapértelmezett értéket, majd kattintson az **Befejezés**.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>2. lehetőség: Manuálisan állítsa be a statikus IP-címet a Vendég virtuális gépen
Ha nem konfigurálta a DHCP dinamikusan IP-cím hozzárendelése a Vendég virtuális gépen, kövesse az alábbi lépéseket egy statikus IP-cím beállítása.

1. Az Azure virtuális gépen nyissa meg a Powershellt rendszergazdaként.

2. Kattintson a jobb gombbal a Vendég virtuális gépen, és kattintson a Csatlakozás gombra.

3. Jelentkezzen be a Vendég virtuális gépen.

4. A Vendég virtuális gépen nyissa meg a hálózati és megosztási központ.

5. Konfigurálja a hálózati adaptert egy címet az előző szakaszban létrehozott NAT-hálózatán tartományán belül.

Ebben a példában a 192.168.0.0/24 címtartományt a címet fogja használni.

## <a name="test-connectivity-in-guest-virtual-machine"></a>A Vendég virtuális gép kapcsolatának tesztelése

A Vendég virtuális gépen nyissa meg a böngészőt, és keresse meg a weblapot.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

## <a name="set-up-intranet-connectivity-for-the-guest-virtual-machine"></a>A Vendég virtuális gép intranetes kapcsolatok beállítása

A Vendég virtuális gépek és Azure virtuális gépek közötti transzparens kapcsolódásának engedélyezése útmutatásért lásd az [ebben a dokumentumban](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization-azure-virtual-network).
