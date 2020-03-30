---
title: Rövid útmutató – Hozzon létre egy Azure VMware vm-et magánfelhőben – Azure VMware-megoldás a CloudSimple-től
description: Bemutatja, hogyan hozhat létre Egy Azure VMware virtuális gép a CloudSimple private cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ac818cfd267b781366c0e32c9f93cc885dff99c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77566148"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>VMware virtuális gépek létrehozása a magánfelhőben

Virtuális gépek létrehozása a magánfelhőben, először is az Azure Portalon a CloudSimple portál eléréséhez.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

## <a name="access-the-cloudsimple-portal"></a>Hozzáférés a CloudSimple portáljához

1. Válassza az **Összes szolgáltatás** elemet.
2. Keresse meg a **CloudSimple szolgáltatásokat.**
3. Válassza ki azt a CloudSimple szolgáltatást, amelyen létre szeretné hozni a magánfelhőt.
4. Az **Áttekintés** lapon kattintson **az Ugrás a CloudSimple portálra** egy új böngészőlap megnyitásához a CloudSimple portálhoz.  Ha a rendszer kéri, jelentkezzen be az Azure bejelentkezési hitelesítő adataival.  

    ![A CloudSimple portál elindítása](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>VCenter web-ui indítása

Most már elindíthatja a virtuális gépek és házirendek virtuális gépek és házirendek beállításához.

A vCenter eléréséhez indítsa el a CloudSimple portálon. A Kezdőlap Gyakori **feladatok területén**kattintson a **VSphere-ügyfél indítása gombra.**  Válassza a magánfelhőt, majd kattintson a **VSphere-ügyfél indítása** a magánfelhőben parancsra.

   ![VSphere kliens indítása](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>ISO- vagy vSphere-sablon feltöltése

  > [!WARNING]
  > Az ISO feltöltéshez használja a vSphere HTML5 klienst.  A Flash-ügyfél használata hibát okozhat.

1. Szerezze be az ISO vagy vSphere sablont, amelyet fel szeretne tölteni a vCenterbe egy virtuális gép létrehozásához, és elérhetővé teszi a helyi rendszeren.
2. A vCenterben kattintson a **Lemez** ikonra, és válassza **a vsanDatastore**lehetőséget. Kattintson **a Fájlok,** majd **az Új mappa gombra.**
    ![vCenter ISO](media/vciso00.png)

3. Hozzon létre egy mappát "ISO és sablonok" címmel.

4. Nyissa meg az ISO-k és sablonok ISO-k mappáját, és kattintson **a Fájlok feltöltése gombra.** Kövesse a képernyőn megjelenő utasításokat az ISO feltöltéséhez.

## <a name="create-a-virtual-machine-in-vcenter"></a>Virtuális gép létrehozása a vCenterben

1. A vCenterben kattintson a **Gazdagépek és fürtök** ikonra.

2. Kattintson a jobb gombbal **a Számítási feladatok elemre,** és válassza az **Új virtuális gép parancsot.**
    ![Új virtuális gép](media/vcvm01.png)

3. Válassza **az Új virtuális gép létrehozása lehetőséget,** és kattintson a **Tovább**gombra.
    ![Új virtuális gép](media/vcvm02.png)

4. Nevezze el a gépet, válassza ki a **számítási feladatok virtuális gépének** helyét, és kattintson a **Tovább**gombra.
    ![Új virtuális gép](media/vcvm03.png)

5. Jelölje ki a **Számítási számítási** erőforrást, és kattintson a **Tovább gombra.**
    ![Új virtuális gép](media/vcvm04.png)

6. Válassza **a vsanDatastore lehetőséget,** és kattintson a **Tovább**gombra.
    ![Új virtuális gép](media/vcvm05.png)

7. Tartsa meg az alapértelmezett ESXi 6.5 kompatibilitási beállítást, és kattintson a **Tovább**gombra.
    ![Új virtuális gép](media/vcvm06.png)

8. Válassza ki a létrehozni kívánt virtuális gép ISO-jának vendég operációs rendszerét, és kattintson a **Tovább**gombra.
    ![Új virtuális gép](media/vcvm07.png)

9. Adja meg a merevlemez és a hálózati beállításokat. Új CD/DVD-meghajtó esetén válassza **az Adattár ISO-fájl jave**lehetőséget.  Ha engedélyezni szeretné a nyilvános IP-címről erre a virtuális gépre irányuló forgalmat, válassza ki a hálózatot **vm-1-ként.**
    ![Új virtuális gép](media/vcvm08.png)

10. Megnyílik egy kijelölési ablak. Jelölje ki az ISO-k és sablonok mappába korábban feltöltött fájlt, és kattintson az **OK**gombra.
    ![Új virtuális gép](media/vcvm10.png)

11. Tekintse át a beállításokat, és kattintson az **OK** gombra a virtuális gép létrehozásához.
    ![Új virtuális gép](media/vcvm11.png)

A virtuális gép most hozzáadódik a számítási számítási erőforrásokhoz, és készen áll a használatra. 
![Új virtuális gép](media/vcvm12.png)

Az alapbeállítás befejeződött. A privát felhő használatát a helyszíni virtuálisgép-infrastruktúra használatához hasonlóan kezdheti el használni.

A következő szakaszok nem kötelező információkat tartalmaznak a DNS- és DHCP-kiszolgálók magánfelhőbeli számítási feladatokhoz való beállításáról és az alapértelmezett hálózati konfiguráció módosításáról.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Felhasználók és identitásforrások hozzáadása a vCenterhez (nem kötelező)

A CloudSimple alapértelmezett vCenter felhasználói fiókot rendel felhasználónévvel. `cloudowner@cloudsimple.local` Az első lépésekhez nincs szükség további fiókbeállításokra.  A CloudSimple általában hozzárendeli a rendszergazdáknak a normál műveletek végrehajtásához szükséges jogosultságokat.  Állítsa be a helyszíni active directoryt vagy az Azure AD-t [további identitásforrásként](set-vcenter-identity.md) a magánfelhőben.

## <a name="create-a-dns-and-dhcp-server-optional"></a>DNS- és DHCP-kiszolgáló létrehozása (nem kötelező)

A magánfelhő-környezetben futó alkalmazások és munkaterhelések névfeloldást és DHCP-szolgáltatásokat igényelnek a keresgéléshez és az IP-címhozzárendeléshez. A szolgáltatások biztosításához megfelelő DHCP- és DNS-infrastruktúrára van szükség. Konfigurálhatja a virtuális gépet a vCenterben, hogy ezeket a szolgáltatásokat a privát felhőbeli környezetben biztosítsa.

Előfeltételek

* Elosztott portcsoport VLAN-nal konfigurálva

* A telepítés irányítása a helyszíni vagy az internetalapú DNS-kiszolgálókra

* Virtuálisgép-sablon vagy ISO virtuális gép létrehozásához

Az alábbi hivatkozások útmutatást nyújtanak a DHCP- és DNS-kiszolgálók Linuxon és Windows rendszeren történő beállításához.

#### <a name="linux-based-dns-server-setup"></a>Linux-alapú DNS-kiszolgáló beállítása

A Linux különböző csomagokat kínál a DNS-kiszolgálók beállításához.  Az alábbi hivatkozás a nyílt forráskódú BIND DNS-kiszolgáló beállítására vonatkozó utasításokra mutat.

[Példa beállítására](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Windows-alapú telepítés

Ezek a Microsoft-témakörök azt ismertetik, hogyan állítható be a Windows-kiszolgáló DNS-kiszolgálóként és DHCP-kiszolgálóként.

[Windows Server DNS-kiszolgálóként](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server DHCP-kiszolgálóként](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Hálózati konfiguráció testreszabása (nem kötelező)

A Network-lapok a CloudSimple portálon lehetővé teszik a tűzfaltáblák és a virtuális gépek nyilvános IP-címeinek konfigurációjának megadását.

### <a name="allocate-public-ips"></a>Nyilvános IP-k kiosztása

1. Keresse meg **a Network > Nyilvános IP-címet** a CloudSimple portálon.
2. Kattintson **a Nyilvános IP lefoglalása gombra.**
3. Adjon meg egy nevet az IP-címbejegyzés azonosításához.
4. Tartsa meg az alapértelmezett helyet.
5. A csúszkával szükség esetén módosíthatja az tétlen időoutot.
6. Adja meg azt a helyi IP-címet, amelyhez nyilvános IP-címet kíván rendelni.
7. Szükség esetén adjon meg egy társított DNS-nevet.
8. Kattintson a **Done** (Kész) gombra.

    ![Nyilvános IP-cím](media/quick-create-pc-public-ip.png)

Megkezdődik a nyilvános IP-cím felosztása. A tevékenység állapotát a Tevékenység **> tevékenységek** lapon ellenőrizheti. Ha a foglalás befejeződött, az új bejegyzés megjelenik a Nyilvános IP-oldalakon.

A virtuális gép, amelyhez ezt az IP-címet le kell képeznie kell kell, a fent megadott helyi címmel kell konfigurálni. Az IP-cím konfigurálásának eljárása a virtuális gép operációs rendszerére vonatkozik. A megfelelő eljárásról a virtuális gép operációs rendszerének dokumentációjában tájékozódhat.

#### <a name="example"></a>Példa

Például itt vannak az Ubuntu 16.04 részletei.

Adja hozzá a statikus metódust az inet address family konfigurációhoz az /etc/network/interfaces fájlban. Módosítsa a cím-, hálózati maszk- és átjáróértékeket. Ebben a példában az eth0-összeköttetést, a 192.168.24.10 belső IP-címet, a 192.168.24.1 átjárócímet és a 255.255.255.0 hálózati maszkot használjuk. Az Ön környezetében a rendelkezésre álló alhálózati információk az üdvözlő e-mailben érhetők el.

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
Manuálisan tiltsa le a felületet.

```
sudo ifdown eth0
```
Manuálisan engedélyezze újra a felületet.

```
sudo ifup eth0
```

Alapértelmezés szerint az internetről érkező összes bejövő forgalom **megtagadva.** Ha más portot szeretne megnyitni, hozzon létre egy [tűzfaltáblát.](firewall.md)

Miután egy belső IP-címet statikus IP-címként konfigurált, ellenőrizze, hogy a virtuális gépről elérheti-e az internetet.

```
ping 8.8.8.8
```
Ellenőrizze azt is, hogy a virtuális gép az internetről a nyilvános IP-cím használatával érhető el.

Győződjön meg arról, hogy a virtuális gép iptable szabályai nem blokkolja a 80-as bejövő portot.
        
```
netstat -an | grep 80
```

Indítsa el a 80-as porton figyelő http-kiszolgálót.
       
```
python2.7 -m SimpleHTTPServer 80
```

vagy

```
python3 -m http.server 80
```
Indítson el egy böngészőt az asztalon, és irányítsa a 80-as portra a nyilvános IP-címhez a virtuális gépen lévő fájlok tallózásához.

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Alapértelmezett CloudSimple tűzfalszabályok nyilvános IP-címhez

* VPN-forgalom: A VPN és az összes számításifeladatok hálózata és felügyeleti hálózata közötti összes forgalom engedélyezett.
* Privát felhő belső forgalma: Minden kelet-nyugati forgalom között (-ból/-a) számítási feladatok hálózatok és a felügyeleti hálózat (fent látható) engedélyezett.
* Internetes forgalom:
  * Az internetről érkező összes bejövő forgalom nem érhető el a számítási feladatok hálózataitól és a felügyeleti hálózattól.
  * A számítási feladatok hálózatairól vagy a felügyeleti hálózatról az internetre irányuló összes kimenő forgalom engedélyezett.

A tűzfalszabályok szolgáltatással módosíthatja a forgalom biztonságossági módját is. További információt a [Tűzfaltáblák és -szabályok beállítása](firewall.md)című témakörben talál.

## <a name="install-solutions-optional"></a>Megoldások telepítése (nem kötelező)

Megoldásokat telepíthet a CloudSimple Private Cloud szolgáltatásra, hogy teljes mértékben kihasználhassa a privát felhőbeli vCenter-környezet előnyeit. Beállíthatja a biztonsági mentést, a vészhelyreállítást, a replikációt és más funkciókat a virtuális gépek védelmére. Ilyenek például a VMware Site Recovery Manager (VMware SRM) és a Veeam Backup & replication.

A megoldás telepítéséhez korlátozott időtartamra további jogosultságokat kell kérnie. Lásd: [Jogosultságok elmélyítése](escalate-private-cloud-privileges.md).

## <a name="next-steps"></a>További lépések

* [VMware rendszerű virtuális gépek felhasználása az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* [Csatlakozás helyszíni hálózathoz az Azure ExpressRoute használatával](on-premises-connection.md)
* [VPN-átjárók beállítása a CloudSimple hálózaton](vpn-gateway.md)
