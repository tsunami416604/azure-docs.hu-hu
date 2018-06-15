---
title: Különböző Azure verem szoftverfejlesztői készlet környezetekben két virtuális hálózat közötti pont-pont VPN-kapcsolat létrehozása |} Microsoft Docs
description: Lépésről lépésre, amely egycsomópontos Azure verem szoftverfejlesztői készlet két környezet közötti pont-pont VPN-kapcsolat létrehozásához használja a felhő rendszergazdájához.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/10/2017
ms.author: brenduns
ms.reviewer: scottnap
ms.openlocfilehash: e6520da6b866ebddd66604dd8f27acfc5a9bcef4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32159851"
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>Különböző Azure verem szoftverfejlesztői készlet környezetekben két virtuális hálózat közötti pont-pont VPN-kapcsolat létrehozása
## <a name="overview"></a>Áttekintés
Ez a cikk bemutatja, hogyan két külön Azure verem szoftverfejlesztői készlet környezetben két virtuális hálózat közötti pont-pont VPN-kapcsolatot. Konfigurálhatja a kapcsolatokat, amíg megtudhatja, hogyan működnek a VPN-átjárók Azure-készletben.

### <a name="connection-diagram"></a>Kapcsolati diagram
Az alábbi ábrán látható, hogy mi a kapcsolat konfigurációs kell hasonlítania amikor elkészült.

![Pont-pont VPN-kapcsolat konfigurációja](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>Előkészületek
A kapcsolat a konfigurálás befejezéséhez gondoskodjon arról, hogy a következő elemek megkezdése előtt:

* Két kiszolgálót és egyéb előfeltételeket, amelyek megfelelnek az Azure verem szoftverfejlesztői készlet hardverkövetelmények a [gyors üzembe helyezés: az Azure verem szoftverfejlesztői készlet kiértékelése](azure-stack-deploy-overview.md). 
* A [Azure verem szoftverfejlesztői készlet](https://azure.microsoft.com/overview/azure-stack/try/) központi telepítési csomagot.

## <a name="deploy-the-azure-stack-development-kit-environments"></a>Az Azure verem szoftverfejlesztői készlet környezetek központi telepítése
Fejezze be a kapcsolat konfigurációját, két Azure verem szoftverfejlesztői készlet környezetet kell telepítenie.
> [!NOTE] 
> Minden Azure verem szoftverfejlesztői készlet, amely központilag, kövesse a [üzembe helyezési utasítások](azure-stack-run-powershell-script.md). Ebben a cikkben az Azure verem szoftverfejlesztői készlet környezetek nevezzük *POC1* és *POC2*.


## <a name="prepare-an-offer-on-poc1-and-poc2"></a>Az ajánlat POC1 és POC2 előkészítése
POC1 és POC2 készítse elő az ajánlatot, hogy egy felhasználó az ajánlat előfizetni, és a virtuális gépek telepítése. Ajánlat létrehozásával kapcsolatos további információkért lásd: [virtuális gépek Azure verem felhasználók számára történő elérhetővé](azure-stack-tutorial-tenant-vm.md).

## <a name="review-and-complete-the-network-configuration-table"></a>Tekintse át és végezze el a hálózati konfigurációs tábla
A következő táblázat összefoglalja a hálózati konfigurációt a mindkét Azure verem szoftverfejlesztői készlet környezetben. Az eljárással, amely akkor jelenik meg, a külső BGPNAT címet adott-e a hálózat hozzáadása a táblázat után.

**Hálózati konfiguráció tábla**
|   |POC1|POC2|
|---------|---------|---------|
|Virtuális hálózat neve     |VNET-01|VNET-02 |
|Virtuális hálózat címtere |10.0.10.0/23|10.0.20.0/23|
|Alhálózat neve     |Alhálózat-01|Alhálózat-02|
|Alhálózati címtartomány|10.0.10.0/24 |10.0.20.0/24 |
|Átjáró alhálózata     |10.0.11.0/24|10.0.21.0/24|
|Külső BGPNAT cím     |         |         |

> [!NOTE]
> A példa környezetben BGPNAT IP-címek a következők: a POC1 10.16.167.195 és 10.16.169.131 POC2 a. Az alábbi eljárás segítségével határozza meg a külső BGPNAT IP-címek az Azure verem szoftverfejlesztői készlet gazdagépek, és adja őket a hálózati konfiguráció előző táblázatban.


### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>Az IP-cím beszerzése a NAT virtuális gépének külső adapteréről
1. Jelentkezzen be a Azure verem fizikai gép POC1 számára.
2. Szerkessze a következő Powershell-kódjába lecseréli a rendszergazda jelszavát, és futtassa a kódot a Koncepció állomáson:

   ```powershell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "AzS-bgpnat01" `
    -Password $Password
   ```
3. Az IP-cím hozzáadása a hálózati konfigurációs táblázat, amely akkor jelenik meg az előző szakaszban.

4. Ismételje meg ezt a műveletet az POC2.

## <a name="create-the-network-resources-in-poc1"></a>A hálózati erőforrások POC1 létrehozása
Most már a POC1 hálózati erőforrásokhoz, hogy be kell állítani a átjárók másolatot hoz létre. Az alábbi utasítások alapján megjelenítése az erőforrás létrehozása a felhasználói portál használatával. Az erőforrások létrehozásához használhatja a PowerShell-kódot.

![Erőforrások létrehozásához használt munkafolyamat](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>Jelentkezzen be a bérlő
Szolgáltatás-rendszergazda bejelentkezhet hez bérlőként a tervek, ajánlatok és-előfizetések, amelyek a bérlők használhatja. Ha még nem rendelkezik egy, [bérlői fiók létrehozása](azure-stack-add-new-user-aad.md) regisztráció előtt.

### <a name="create-the-virtual-network-and-vm-subnet"></a>A virtuális hálózat és a virtuálisgép-alhálózat létrehozása
1. A bérlői fiók használatával jelentkezzen be a felhasználói portálra.
2. Válassza ki a felhasználói portál **új**.

    ![Új virtuális hálózat létrehozása](media/azure-stack-create-vpn-connection-one-node-tp2/image3.png)

3. Ugrás a **piactér**, majd válassza ki **hálózati**.
4. Válassza ki **virtuális hálózati**.
5. A **neve**, **Címtéren**, **alhálózati név**, és **alhálózati címtartományt**, használja az értékeket, amelyeket korábban a hálózati jelenik meg konfigurációs tábla.
6. A **előfizetés**, az előfizetés korábban létrehozott jelenik meg.
7. A **erőforráscsoport**, hozzon létre egy erőforráscsoportot, vagy ha már rendelkezik egy, válassza ki a **meglévő**.
8. Ellenőrizze az alapértelmezett helyet.
9. Válassza a **Rögzítés az irányítópulton** lehetőséget.
10. Kattintson a **Létrehozás** gombra.

### <a name="create-the-gateway-subnet"></a>Az átjáróalhálózat létrehozása
1. Az irányítópulton nyissa meg a korábban létrehozott VNET-01 virtuális hálózati erőforráshoz.
2. A **Beállítások** panelen válassza az **Alhálózatok** elemet.
3. A virtuális hálózati átjáró-alhálózatot hozzáadásához válassza **Átjáróalhálózatot**.
   
    ![Átjáró alhálózatának hozzáadása](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. Alapértelmezés szerint az alhálózat neve legyen **GatewaySubnet**.
   Átjáró alhálózatok olyan speciális. A megfelelő működéshez kell használniuk a *GatewaySubnet* nevét.
5. A **-címtartományt**, győződjön meg arról, hogy a cím **10.0.11.0/24**.
6. Válassza ki **OK** az átjáró alhálózatának létrehozásához.

### <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása
1. Válassza ki az Azure-portálon **új**. 
2. Ugrás a **piactér**, majd válassza ki **hálózati**.
3. Válassza ki a listáról a hálózati erőforrások **virtuális hálózati átjáró**.
4. A **neve**, adja meg **GW1**.
5. Válassza ki a **virtuális hálózati** elem egy virtuális hálózatot választ.
   Válassza ki **VNET-01** a listából.
6. Válassza ki a **nyilvános IP-cím** menüpont. Ha a **nyilvános IP-cím kiválasztása** panel nyílik, jelölje be **hozzon létre új**.
7. A **neve**, adja meg **GW1-PiP**, majd válassza ki **OK**.
8.  Alapértelmezés szerint a **VPN-típus**, **útválasztó-alapú** van kiválasztva.
    Tartsa a **útválasztó-alapú** VPN-típus.
9. Ellenőrizze, hogy az **Előfizetés** és a **Hely** mező értéke helyes-e. Rögzítheti az irányítópulton az erőforrást. Kattintson a **Létrehozás** gombra.

### <a name="create-the-local-network-gateway"></a>A helyi hálózati átjáró létrehozása
A *helyi hálózati átjárók* megvalósítása ebben az Azure Stack értékelési telepítésben kissé eltér egy tényleges Azure-telepítéstől.

Az Azure-telepítés a helyi hálózati átjáró egy (a bérlő) a helyszíni fizikai eszköz, a virtuális hálózati átjáró az Azure-ban való kapcsolódáshoz használt jelöli. Az Azure-verem értékelési telepítést a kapcsolat mindkét végén virtuális hálózati átjárók!

Egy gondolja, hogy a több általános módja, hogy a helyi hálózati átjáró erőforrás mindig jelzi a távoli átjáró, a kapcsolat másik végén. Az Azure verem szoftverfejlesztői készlet tervezésétől miatt meg kell adnia a külső hálózati adapter IP-címét a hálózati címfordítást (NAT) a többi Azure verem szoftverfejlesztői készlet a nyilvános IP-címet a virtuális gép helyi hálózati átjáró. Ezután hozzon létre NAT-hozzárendelések a győződjön meg arról, hogy megfelelően vannak-e csatlakoztatva a attribútumának mindkét ends végpontjához NAT virtuális Gépre.


### <a name="create-the-local-network-gateway-resource"></a>A helyi hálózati átjáró létrehozása
1. Jelentkezzen be a Azure verem fizikai gép POC1 számára.
2. Válassza ki a felhasználói portál **új**.
3. Ugrás a **piactér**, majd válassza ki **hálózati**.
4. Válassza ki a listáról az erőforrások **helyi hálózati átjáró**.
5. A **neve**, adja meg **POC2-GW**.
6. A **IP-cím**, adja meg a külső BGPNAT címét POC2. Ez a cím jelenik meg a korábban a hálózati konfiguráció táblázatban.
7. A **Címterület**, adja meg a később létrehozott POC2 virtuális hálózat a címtér, **10.0.20.0/23**.
8. Ellenőrizze, hogy a **előfizetés**, **erőforráscsoport**, és **hely** helyes-e, majd válassza ki **létrehozása**.

### <a name="create-the-connection"></a>A kapcsolat létrehozása
1. Válassza ki a felhasználói portál **új**.
2. Ugrás a **piactér**, majd válassza ki **hálózati**.
3. Válassza ki a listáról az erőforrások **kapcsolat**.
4. Az a **alapjai** -beállítások panelen a a **kapcsolattípus**, jelölje be **pont-pont (IPSec)**.
5. Válassza ki a **előfizetés**, **erőforráscsoport**, és **hely**, majd válassza ki **OK**.
6. Az a **beállítások** panelen válassza **virtuális hálózati átjáró**, majd válassza ki **GW1**.
7. Válassza ki **helyi hálózati átjáró**, majd válassza ki **POC2-GW**.
8. A **kapcsolatnév**, adja meg **POC1-POC2**.
9. A **megosztott kulcsot (PSK)**, adja meg **12345**, majd válassza ki **OK**.
10. Az a **összegzés** panelen válassza **OK**.

### <a name="create-a-vm"></a>Virtuális gép létrehozása
A VPN-kapcsolaton keresztül átvitt adatok ellenőrzéséhez szüksége van a virtuális gépek adatokat küldeni és fogadni az egyes Azure verem szoftverfejlesztői készlet. Most hozzon létre egy virtuális gépet POC1, majd a virtuális hálózaton, tegye meg a Virtuálisgép-alhálózatot.

1. Válassza ki az Azure-portálon **új**.
2. Ugrás a **piactér**, majd válassza ki **számítási**.
3. A virtuálisgép-lemezképeket, jelölje ki a **Windows Server 2016 Datacenter próbaverzió** kép.
4. Az a **alapjai** panelen, a **neve**, adja meg **VM01**.
5. Adjon meg egy érvényes felhasználónevet és jelszót. Ez a fiók használatával jelentkezzen be a virtuális gép létrehozása után.
6. Adjon meg egy **előfizetés**, **erőforráscsoport**, és **hely**, majd válassza ki **OK**.
7. Az a **mérete** paneljét, ebben az esetben válassza ki a virtuális gép méretét, és válassza **válasszon**.
8. Az a **beállítások** panelen fogadja el az alapértelmezett beállításokat. Győződjön meg arról, hogy a **VNET-01** virtuális hálózat van kiválasztva. Ellenőrizze, hogy az alhálózat **10.0.10.0/24**. Ezután kattintson az **OK** gombra.
9. Az a **összegzés** panelt, tekintse át a beállításokat, majd válassza ki **OK**.



## <a name="create-the-network-resources-in-poc2"></a>A hálózati erőforrások POC2 létrehozása

A következő lépés, ha a hálózati erőforrásokhoz a POC2. Az alábbi utasítások alapján bemutatják, hogyan hozhat létre az erőforrásokat a felhasználói portál használatával.

### <a name="sign-in-as-a-tenant"></a>Jelentkezzen be a bérlő
Szolgáltatás-rendszergazda bejelentkezhet hez bérlőként a tervek, ajánlatok és-előfizetések, amelyek a bérlők használhatja. Ha még nem rendelkezik egy, [bérlői fiók létrehozása](azure-stack-add-new-user-aad.md) regisztráció előtt.

### <a name="create-the-virtual-network-and-vm-subnet"></a>A virtuális hálózat és a virtuálisgép-alhálózat létrehozása

1. Jelentkezzen be egy bérlői fiók használatával.
2. Válassza ki a felhasználói portál **új**.
3. Ugrás a **piactér**, majd válassza ki **hálózati**.
4. Válassza ki **virtuális hálózati**.
5. A korábban a hálózati konfiguráció táblázatban szereplő információk segítségével azonosítsa azokat az értékeket a POC2 a **neve**, **Címtéren**, **alhálózati név**, és **Alhálózati címtartományt**.
6. A **előfizetés**, az előfizetés korábban létrehozott jelenik meg.
7. A **erőforráscsoport**, hozzon létre egy új erőforráscsoportot, vagy ha már rendelkezik egy, **meglévő**.
8. Az alapértelmezett ellenőrzése **hely**.
9. Válassza a **Rögzítés az irányítópulton** lehetőséget.
10. Kattintson a **Létrehozás** gombra.

### <a name="create-the-gateway-subnet"></a>Hozza létre az átjáró-alhálózatot.
1. Nyissa meg a létrehozott virtuális hálózati erőforráshoz (**VNET-02**) az irányítópulton.
2. A **Beállítások** panelen válassza az **Alhálózatok** elemet.
3. Válassza ki **átjáróalhálózatot** hozzáadása egy átjáró-alhálózatot a virtuális hálózathoz.
4. Az alhálózat neve alapértelmezés szerint **GatewaySubnet**.
   Az átjáró-alhálózatok egyediek, és a megfelelő működéshez ezzel az adott névvel kell rendelkezniük.
5. Az a **-címtartományt** mezőbe a címe **10.0.21.0/24**.
6. Válassza ki **OK** az átjáró alhálózatának létrehozásához.

### <a name="create-the-virtual-network-gateway"></a>Virtuális hálózati átjáró létrehozása
1. Válassza ki az Azure-portálon **új**.  
2. Ugrás a **piactér**, majd válassza ki **hálózati**.
3. Válassza ki a listáról a hálózati erőforrások **virtuális hálózati átjáró**.
4. A **neve**, adja meg **GW2**.
5. Virtuális hálózat kiválasztásához jelölje ki a **virtuális hálózati**. Válassza ki **VNET-02** a listából.
6. Válassza ki **nyilvános IP-cím**. Ha a **nyilvános IP-cím kiválasztása** panel nyílik, jelölje be **hozzon létre új**.
7. A **neve**, adja meg **GW2-PiP**, majd válassza ki **OK**.
8. Alapértelmezés szerint a **VPN-típus**, **útválasztó-alapú** van kiválasztva.
    Tartsa a **útválasztó-alapú** VPN-típus.
9. Ellenőrizze, hogy az **Előfizetés** és a **Hely** mező értéke helyes-e. Rögzítheti az irányítópulton az erőforrást. Kattintson a **Létrehozás** gombra.

### <a name="create-the-local-network-gateway-resource"></a>A helyi hálózati átjáró létrehozása

1. Válassza a POC2 felhasználói portál **új**. 
4. Ugrás a **piactér**, majd válassza ki **hálózati**.
5. Válassza ki a listáról az erőforrások **helyi hálózati átjáró**.
6. A **neve**, adja meg **POC1-GW**.
7. A **IP-cím**, adja meg a külső BGPNAT címét, amely korábban a hálózati konfigurációs táblában szereplő POC1.
8. A **Címterület**, POC1, adja meg a **10.0.10.0/23** címtere **VNET-01**.
9. Ellenőrizze, hogy a **előfizetés**, **erőforráscsoport**, és **hely** helyes-e, majd válassza ki **létrehozása**.

## <a name="create-the-connection"></a>A kapcsolat létrehozása
1. Válassza ki a felhasználói portál **új**. 
2. Ugrás a **piactér**, majd válassza ki **hálózati**.
3. Válassza ki a listáról az erőforrások **kapcsolat**.
4. A a **alapvető** -beállítások panelen a a **kapcsolattípus**, válassza a **pont-pont (IPSec)**.
5. Válassza ki a **előfizetés**, **erőforráscsoport**, és **hely**, majd válassza ki **OK**.
6. Az a **beállítások** panelen válassza **virtuális hálózati átjáró**, majd válassza ki **GW2**.
7. Válassza ki **helyi hálózati átjáró**, majd válassza ki **POC1-GW**.
8. A **kapcsolatnév**, adja meg **POC2-POC1**.
9. A **megosztott kulcsot (PSK)**, adja meg **12345**. Ha egy másik értéket választja, ne feledje, hogy az informatikai *kell* felel meg a megosztott kulcs, amelyet a POC1 hozott létre. Kattintson az **OK** gombra.
10. Tekintse át a **összegzés** panelt, és válassza **OK**.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Most hozzon létre egy virtuális gépet POC2, és a Virtuálisgép-alhálózatot a virtuális hálózat helyezése.

1. Válassza ki az Azure-portálon **új**.
2. Ugrás a **piactér**, majd válassza ki **számítási**.
3. A virtuálisgép-lemezképeket, jelölje ki a **Windows Server 2016 Datacenter próbaverzió** kép.
4. Az a **alapjai** panelen a **neve**, adja meg **VM02**.
5. Adjon meg egy érvényes felhasználónevet és jelszót. Ez a fiók használatával jelentkezzen be a virtuális gép létrehozása után.
6. Adjon meg egy **előfizetés**, **erőforráscsoport**, és **hely**, majd válassza ki **OK**.
7. Az a **mérete** panelen válassza a virtuális gép méretének ehhez a példányhoz, és válassza **válasszon**.
8. Az a **beállítások** panelen elfogadhatja az alapértelmezett beállításokat. Győződjön meg arról, hogy a **VNET-02** virtuális hálózat van kiválasztva, és ellenőrizze, hogy az alhálózat **10.0.20.0/24**. Kattintson az **OK** gombra.
9. Tekintse át a beállításokat a a **összegzés** panelt, és válassza **OK**.

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>A NAT virtuális gépeket konfigurálhatja az egyes Azure verem szoftverfejlesztői készlet átjáró egyesítésre alkalmas
Mivel az Azure verem szoftverfejlesztői készlet önálló és elkülönül a hálózaton, amelyek a fizikai gazdagépen van telepítve, a *külső* VIP-hálózati átjáró csatlakozó nincs ténylegesen külső. Ehelyett a virtuális IP-hálózati rejtett hálózati címfordítást végző útválasztó mögött. 

Az útválasztó egy Windows Server virtuális gép neve nem *AzS-bgpnat01*, amelyen fut az Útválasztás és távelérés szolgáltatás (RRAS) szerepkör, az Azure verem szoftverfejlesztői készlet infrastruktúrában. NAT kell konfigurálnia a AzS-bgpnat01 virtuális gépen elhelyezve lehetővé teheti a a pont-pont VPN-kapcsolat mindkét végén csatlakozni. 

A VPN-kapcsolat konfigurálása, létre kell hoznia egy statikus NAT térkép útvonalat, amely a külső felület BGPNAT virtuális gépen van leképezve a VIP, a peremhálózati átjáró készlet. Statikus NAT-leképezés útvonal szükség az egyes portok a VPN-kapcsolatot.

> [!NOTE]
> Ez a konfiguráció csak Azure verem szoftverfejlesztői készlet környezetben szükség.
> 
> 

### <a name="configure-the-nat"></a>A NAT konfigurálása
> [!IMPORTANT]
> Ezt az eljárást kell végrehajtani *mindkét* Azure verem szoftverfejlesztői készlet környezetekben.

1. Határozza meg a **belső IP-cím** a következő PowerShell-parancsfájl használatára. Nyissa meg a virtuális hálózati átjáró (GW1 és GW2), majd a a **áttekintése** panelen, mentse a értéke a **nyilvános IP-cím** későbbi használatra.
![Belső IP-cím](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)
2. Jelentkezzen be a Azure verem fizikai gép POC1 számára.
3. Másolja, és a következő PowerShell-parancsfájl szerkesztése. Minden Azure verem szoftverfejlesztői készlet a NAT konfigurálásához futtassa a parancsfájlt egy rendszergazda jogú Windows PowerShell ISE. A parancsfájlban, adjon meg értékeket a *külső BGPNAT cím* és *belső IP-cím* helyőrzők:

   ```powershell
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

4. Ismételje meg ezt a műveletet az POC2.

## <a name="test-the-connection"></a>A kapcsolat tesztelése
Most, hogy a helyek kapcsolat létrejött, ellenőriznie kell, hogy az átfutó forgalom kaphat. Ellenőrizze, hogy jelentkezzen be az egyik Azure verem szoftverfejlesztői készlet környezetben létrehozott virtuális gépek közül. Ezt követően Pingelje meg a környezetben létrehozott virtuális gépre. 

Győződjön meg arról, hogy a helyek kapcsolaton keresztül a forgalom küldése, győződjön meg arról, hogy a virtuális gép távoli alhálózaton, nem a VIP közvetlen IP-címet (DIP) címének pingelése. Ehhez az szükséges, a DIP cím, a kapcsolat másik végén található. A későbbi használat céljából menteni.

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>Jelentkezzen be a bérlő POC1 a virtuális gép
1. Jelentkezzen be a Azure verem fizikai gép POC1 a, és a bérlői fiók használatával jelentkezzen be a felhasználói portálra.
2. Válassza ki a bal oldali navigációs sáv **számítási**.
3. A virtuális gépek listájának megtekintéséhez keresse **VM01** korábban létrehozott, és állítsa be azt.
4. A virtuális gép paneljén kattintson **Connect**, majd nyissa meg a VM01.rdp fájlt.
   
     ![A Csatlakozás gombra](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)
5. Jelentkezzen be a virtuális gép létrehozásakor beállított fiókot.
6. Nyisson meg egy emelt szintű **Windows PowerShell** ablak.
7. Adja meg **ipconfig/all**.
8. A kimenetben keresse meg a **IPv4-cím**, majd mentse a cím későbbi használatra. Ez az a cím, amely a POC2 ping. Ebben a példakörnyezetben a cím **10.0.10.4**, de az Ön környezetében ettől eltérő lehet. Alá kell tartozniuk a **10.0.10.0/24** korábban létrehozott alhálózati.
9. Hozzon létre egy tűzfalszabályt, amely lehetővé teszi, hogy a virtuális gép pingelésre, futtassa a következő PowerShell-parancsot:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>Jelentkezzen be a bérlő POC2 a virtuális gép
1. Jelentkezzen be a Azure verem fizikai gép POC2 a, és a bérlői fiók használatával jelentkezzen be a felhasználói portálra.
2. A bal oldali navigációs sávon kattintson **számítási**.
3. A virtuális gépek listájának található **VM02** korábban létrehozott, és állítsa be azt.
4. A virtuális gép paneljén kattintson a **Csatlakozás** elemre.
5. Jelentkezzen be a virtuális gép létrehozásakor beállított fiókot.
6. Nyisson meg egy emelt szintű **Windows PowerShell** ablak.
7. Adja meg **ipconfig/all**.
8. Egy IPv4-címet, amely kell megjelennie **10.0.20.0/24**. Példa környezetben, a cím az **10.0.20.4**, de lehet, hogy a cím különböző.
9. Hozzon létre egy tűzfalszabályt, amely lehetővé teszi, hogy a virtuális gép pingelésre, futtassa a következő PowerShell-parancsot:

   ```powershell
   New-NetFirewallRule `
    –DisplayName “Allow ICMPv4-In” `
    –Protocol ICMPv4
   ```

10. A virtuális gépről a POC2 Pingelje meg a virtuális gép POC1, az alagúton keresztül. Ehhez az szükséges, a DIP-ről a VM01 feljegyzett pingelést.
   Példa környezetben, ami **10.0.10.4**, de ügyeljen arra, hogy pingelni a címre, amelyet a tesztkörnyezetben. A következőhöz hasonló eredmény kell megjelennie:
   
    ![Sikeres ping](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. A távoli virtuális gépről a válasz azt jelzi, hogy a sikeres vizsgálat! A virtuális gép ablak bezárása A kapcsolat tesztelésére, próbálja meg más típusú adatátvitelek például egy fájl másolása.

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>Adatátviteli statisztika megtekintése az átjárókapcsolaton keresztül
Ha szeretné tudni, hogy mennyi adatot továbbítja a helyek kapcsolaton keresztül, ezek az információk érhető el a **kapcsolat** panelen. Ez a vizsgálat módja is egy másik győződjön meg arról, hogy az imént a telefonjára küldött ping ténylegesen merült fel a VPN-kapcsolaton keresztül.

1. Be van jelentkezve POC2 a bérlői virtuális gépre, a bérlői fiók használatával jelentkezzen be a felhasználói portálra.
2. Nyissa meg a **összes erőforrás**, majd válassza ki a **POC2-POC1** kapcsolat. **Kapcsolatok** jelenik meg.
4. Az a **kapcsolat** panelen, a statisztikája **adatok** és **kimenő adatforgalmat** jelennek meg. Az alábbi képernyőképen a nagy mennyiségű rendelnek a további fájlok átvitele. Néhány nullától eltérő értéket kell megjelennie.
   
    ![Bejövő és kimenő adatforgalom](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
