---
title: Rövid útmutató – a Magánfelhő VMware virtuális gép létrehozása
description: Azt ismerteti, hogyan hozhat létre és a VMware virtuális gép CloudSimple privát felhő
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/03/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 33354ce09ad6ba1a9a7c08a8cd3b945f3788011a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595683"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>A Magánfelhő létrehozása VMware virtuális gépek

A Magánfelhő virtuális gépek létrehozásához először az CloudSimple portál elérése az Azure Portalról.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="access-the-cloudsimple-portal"></a>Hozzáférés a CloudSimple portáljához

1. Válassza az **Összes szolgáltatás** elemet.
2. Keresse meg **CloudSimple szolgáltatások**.
3. Válassza ki a CloudSimple szolgáltatást, amelyen szeretné létrehozni a Magánfelhő.
4. Az a **áttekintése** kattintson **a CloudSimple portálon** egy új böngészőlapon CloudSimple portál megnyitásához.  Ha a rendszer kéri, jelentkezzen be az Azure bejelentkezési nevét.  

    ![Indítsa el a CloudSimple portál](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Indítsa el a vCenter web-ui

A virtuális gépek és a házirendek vCenter már el is indíthatja.

VCenter eléréséhez, indítsa el a CloudSimple portálról. A kezdőlapon alatt **gyakori feladatok**, kattintson a **indítsa el a vSphere Client**.  Válassza ki a privát felhőt, és kattintson a **indítsa el a vSphere Client** a privát felhő.

   ![Indítsa el a vSphere Client](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>Egy ISO- vagy vSphere-sablon feltöltése

> [!WARNING]
> ISO tölthet fel a HTML5-alapú vSphere client használja.  Flash-ügyfél használatával hibát eredményezhet.

1. Szerezze be a feltölteni kívánt vcenter-hozzon létre egy virtuális gépet, és hogy elérhető a helyi rendszeren ISO- vagy vSphere-sablont.

2. A vCenter, kattintson a **lemez** ikonra, majd **vsanDatastore**. Kattintson a **fájlok** majd **új mappa**.

    ![vCenter ISO](media/vcenter-create-folder.png)

3. Hozzon létre egy ISO-fájlok tárolására szolgáló mappát.

4. Keresse meg a létrehozott új mappát, és kattintson a **fájlok feltöltése**. Kövesse a képernyőn megjelenő utasításokat követve töltse fel az ISO.

## <a name="create-a-virtual-machine-in-vcenter"></a>A vCenter virtuális gép létrehozása

1. A vCenter, kattintson a **gazdagépek és Gazdagépfürtök** ikonra.

2. Kattintson a jobb gombbal **munkaterhelés** válassza **új virtuális gép**.
    
    ![Virtuális gép létrehozása](media/create-vcenter-virtual-machine-01.png)

3. Válassza ki **hozzon létre új virtuális gépet** kattintson **tovább**.

    ![Új virtuális gép varázsló](media/create-vcenter-virtual-machine-02.png)

4. Neve a gép, jelölje be a **munkaterhelési virtuális gép** mappára, majd kattintson **tovább**.

    ![Név és mappa kiválasztása](media/create-vcenter-virtual-machine-03.png)

5. Válassza ki a **munkaterhelés** számítási erőforrás, és kattintson a **tovább**.

    ![Válassza ki a számítási erőforrás](media/create-vcenter-virtual-machine-04.png)

6. Válassza ki **vsanDatastore** kattintson **tovább**.

    ![Tárhely kiválasztása](media/create-vcenter-virtual-machine-05.png)

7. Hagyja meg az alapértelmezett ESXi 6.5-ös kompatibilitási, és kattintson a **tovább**.

    ![Válassza ki a kompatibilitás](media/create-vcenter-virtual-machine-06.png)

8. Válassza ki az ISO, a virtuális gép vendég operációs rendszer, és kattintson a **tovább**.

    ![Testre szabhatja a vendég operációs rendszer](media/create-vcenter-virtual-machine-07.png)

9. Válassza ki a merevlemez és a hálózati beállítások. Új CD/DVD-meghajtóhoz válassza **adattárolója ISO-fájl**.  Ha azt szeretné, hogy a virtuális gép a nyilvános IP-címről érkező forgalmat, jelölje be a hálózaton: **vm-1**.

    ![Válassza ki a hardver testreszabása](media/create-vcenter-virtual-machine-08.png)

10. A kijelölés ablak nyílik meg. Válassza ki a korábban az ISO és a Sablonok mappába feltöltött fájlt, és kattintson a **OK**.

    ![ISO-Lemezképfájl kiválasztása](media/create-vcenter-virtual-machine-10.png)

11. Tekintse át a beállításokat, és kattintson a **OK** a virtuális gép létrehozásához.

    ![Beállítások áttekintése](media/create-vcenter-virtual-machine-11.png)

A virtuális gép ezzel hozzáadja a számítási feladatok számítási erőforrásokat, és készen áll a használatra. 

![Új virtuális gép vcenter](media/create-vcenter-virtual-machine-12.png)

Az alapszintű beállítás már befejeződött. Megkezdheti a Magánfelhő hogyan használja a helyszíni virtuális gép infrastruktúrát hasonlóak.

Tovább szakaszok beállítása DNS és DHCP-kiszolgálók a privát felhő számítási feladatok és az alapértelmezett hálózati konfigurációjához kötelező információt.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Adja hozzá a felhasználók és az identitás források vcenter (nem kötelező)

CloudSimple rendeli hozzá egy alapértelmezett vCenter felhasználói fiókot a username **cloudowner@cloudsimple.local** . Nincsenek további fiók beállítása nem kell elvégeznie a kezdéshez.  CloudSimple általában rendeli hozzá a rendszergazdák a normál műveletek végrehajtásához szükséges jogosultságokkal.  Állítsa be a helyszíni active directory vagy az Azure ad-hez mint egy [további identitásforrásról](https://docs.azure.cloudsimple.com/set-vcenter-identity/) a privát felhőben.

## <a name="create-a-dns-and-dhcp-server-optional"></a>A DNS és DHCP-kiszolgáló létrehozása (opcionális)

Alkalmazások és a egy privát felhőalapú környezetben futó számítási feladatok névfeloldásra és a keresési és IP-cím hozzárendelése DHCP szolgáltatás van szükség. Adja meg ezeket a szolgáltatásokat a megfelelő DHCP és DNS-infrastruktúra szükséges. A vCenter-e a szolgáltatás a privát felhő környezetben konfigurálhatja úgy a virtuális gépek.

### <a name="prerequisites"></a>Előfeltételek

* Egy elosztott port csoport konfigurált VLAN

* Az útvonal a helyszíni vagy internetes DNS-kiszolgálók beállítása

* Virtuálisgép-sablon vagy virtuális gép létrehozása az ISO

Az alábbi hivatkozások Linux és Windows rendszeren a DHCP és DNS-kiszolgálók beállításához ad útmutatást.

### <a name="linux-based-dns-server-setup"></a>Linux-Based DNS Server Setup

Linux kínál a különböző csomagok DNS-kiszolgálók beállításához.  Itt található egy hivatkozás egy nyílt forráskódú kötési DNS-kiszolgáló beállításával kapcsolatos utasításokat.

[Példák](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

### <a name="windows-based-setup"></a>Windows-alapú telepítés

Ezek a Microsoft a cikkek bemutatják, hogyan állíthat be egy Windows server DNS-kiszolgálóként és DHCP-kiszolgálóként.
<br>
[A Windows Server DNS-kiszolgálóként](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[A Windows Server DHCP-kiszolgálóként](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Testre szabhatja a hálózati konfiguráció (nem kötelező)

A hálózati a CloudSimple portál lehetővé teszi, hogy táblák tűzfal konfigurációját és a virtuális gépek nyilvános IP-címek megadásához.

### <a name="allocate-public-ips"></a>Nyilvános IP-címek lefoglalása

1. Navigáljon a **hálózati > nyilvános IP-cím** a CloudSimple portálon.
2. Kattintson a **nyilvános IP-cím lefoglalása**.
3. Adja meg az IP-címet azonosító nevet.
4. Jelölje be a Magánfelhő helyét.
5. A csúszka segítségével esetén módosítsa az üresjárati időkorlátot.
6. Adja meg, amelyhez egy nyilvános IP-címet hozzárendelni kívánt helyi IP-címét.
7. Adja meg a társított DNS-név, ha szükséges.
8. Kattintson a **Done** (Kész) gombra.

    ![Nyilvános IP-cím](media/quick-create-pc-public-ip.png)

A feladata, hogy a nyilvános IP-cím lefoglalása kezdődik. A feladat állapotának ellenőrzéséhez a **tevékenység > feladatok** lapot. Foglalási befejeződése után az új bejegyzés jelenik meg a nyilvános IP-címek lapra.

A virtuális gép, amelyhez az IP-címet meg kell adni a fent megadott helyi cím konfigurálni kell. Az IP-cím konfigurálása eljárás csak a virtuális gép operációs rendszerének. Tekintse át a virtuális gép operációs rendszerének megfelelő eljárás dokumentációját.

#### <a name="example"></a>Példa

Ha például az alábbiakban az Ubuntu 16.04.

Adja hozzá a statikus metódust a fájlban található konfigurációs családi inet cím ```/etc/network/interfaces```. Módosítsa a címet, hálózati maszk és átjáró. Ebben a példában használjuk a eth0 felület, a belső IP-cím 192.168.24.10, a átjárócím 192.168.24.1 és a hálózati maszk 255.255.255.0. 

Szerkessze a ```interfaces``` fájlt.

```
sudo vi /etc/network/interfaces
```

A következő szakaszban ```interfaces``` fájlt.

```
auto eth0
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```

Tiltsa le a felületet.

```
sudo ifdown eth0
```

Engedélyezze újra a felületet.

```
sudo ifup eth0
```

Alapértelmezés szerint az internetről bejövő összes forgalmat a **megtagadva**. Ha szeretné bármely más port megnyitásához, hozzon létre egy [tűzfal tábla](https://docs.azure.cloudsimple.com/firewall/).

Miután belső IP-cím statikus IP-címeként, győződjön meg arról, hogy az internethez, a virtuális gépen érhető el.

```
ping 8.8.8.8
```

Győződjön meg arról, hogy a virtuális gép a elérheti az interneten nyilvános IP-cím használatával.

Győződjön meg arról, hogy a virtuális gépen (iptable) tűzfal szabályok nem blokkolja-e 80-as porton bejövő.

```
netstat -an | grep 80
```

Indítsa el a http-kiszolgálót, amely a 80-as portot figyeli.
       
```
python2.7 -m SimpleHTTPServer 80
```

vagy

```
python3 -m http.server 80
```

Böngésző elindítására az Ön asztalán, és mutasson a nyilvános IP-címe, keresse meg a fájlokat a virtuális gépen a 80-as porton. 

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Alapértelmezett CloudSimple tűzfalszabályok nyilvános IP-címek

* VPN-forgalmat: (A/az) a VPN és az összes munkaterhelés-hálózatok és felügyeleti hálózat közötti összes forgalom engedélyezett.
* Magánfelhő-forgalom belső: Minden kelet – Nyugat-forgalom között (a /) számítási feladatok hálózatok és a felügyeleti hálózat (lásd fent) használata engedélyezett.
* Internetes forgalmat:
    * Az internetről bejövő összes forgalmat munkaterhelés-hálózatok és a felügyeleti hálózat megtagadva.
    * Minden kimenő forgalom az internethez a számítási feladatok hálózatokat vagy a felügyeleti hálózat használata engedélyezett.

Módosíthatja a forgalom védett, módját is, a tűzfalszabályok szolgáltatással. További információkért lásd: [tűzfal táblák és a szabályok beállítása](https://docs.azure.cloudsimple.com/firewall/).

## <a name="install-solutions-optional"></a>Telepítse a megoldások (nem kötelező)
Megoldások a CloudSimple Magánfelhő teljes mértékben kihasználhatja a Magánfelhő a vCenter-környezet is telepítheti. Beállíthat is biztonsági mentés, vészhelyreállítás, replikációs és más függvényekkel a virtuális gép védelméhez. Ilyenek például a VMware Site Recovery Managert (VMware SRM) és a Veeam biztonsági mentés és a replikáció.

Egy megoldás telepítéséhez korlátozott ideig további jogosultságokat kell igényelnie. Lásd: [eszkalálni a jogosultságokat](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges).

## <a name="next-steps"></a>További lépések

* [VMware virtuális gépek az Azure felhasználása](quickstart-create-vmware-virtual-machine.md)
* [Csatlakozhat a helyszíni hálózatot az Azure ExpressRoute használatával](https://docs.azure.cloudsimple.com/on-premises-connection)
* [VPN-átjárók CloudSimple hálózat beállítása](https://docs.azure.cloudsimple.com/vpn-gateway)
