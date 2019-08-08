---
title: Rövid útmutató – VMware virtuális gép létrehozása privát felhőben
description: Útmutató VMware virtuális gép létrehozásához a CloudSimple privát felhőben
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/03/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7d21035fc3e9e80344264b9fde21820162f376d3
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816705"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>VMware virtuális gépek létrehozása a saját felhőben

Ahhoz, hogy virtuális gépeket hozzon létre a saját felhőben, először nyissa meg a CloudSimple-portált a Azure Portal.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="access-the-cloudsimple-portal"></a>Hozzáférés a CloudSimple portáljához

1. Válassza az **Összes szolgáltatás** elemet.
2. Keressen rá a **CloudSimple Services**kifejezésre.
3. Válassza ki azt a CloudSimple-szolgáltatást, amelyen létre szeretné hozni a saját Felhőjét.
4. Az **Áttekintés** lapon kattintson **az Ugrás a CloudSimple portálra** elemre, és nyissa meg a CloudSimple-portál új böngésző lapját.  Ha a rendszer kéri, jelentkezzen be az Azure bejelentkezési hitelesítő adataival.  

    ![A CloudSimple-portál elindítása](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>Webes vCenter elindítása – felhasználói felület

Most már elindíthatja a vCenter-t a virtuális gépek és házirendek beállításához.

A vCenter eléréséhez Kezdje a CloudSimple portálról. A Kezdőlap **Általános feladatok**területén kattintson az **vSphere-ügyfél indítása**lehetőségre.  Válassza ki a privát felhőt, majd kattintson a **vSphere-ügyfél indítása** lehetőségre a privát felhőben.

   ![VSphere-ügyfél elindítása](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>ISO-vagy vSphere-sablon feltöltése

> [!WARNING]
> ISO-feltöltésekhez használja a vSphere HTML5-ügyfelet.  A Flash-ügyfél használata hibát okozhat.

1. Szerezze be a vCenter feltölteni kívánt ISO-vagy vSphere-sablont, és hozzon létre egy virtuális gépet, és legyen elérhető a helyi rendszeren.

2. A vCenter-ben kattintson a **lemez** ikonra, és válassza a **vsanDatastore**lehetőséget. Kattintson a **fájlok** , majd az **új mappa**elemre.

    ![vCenter ISO](media/vcenter-create-folder.png)

3. Hozzon létre egy mappát az ISO-fájlok tárolásához.

4. Navigáljon a létrehozott új mappához, és kattintson a **fájlok feltöltése**elemre. Az ISO feltöltéséhez kövesse a képernyőn megjelenő utasításokat.

## <a name="create-a-virtual-machine-in-vcenter"></a>Virtuális gép létrehozása a vCenter-ben

1. A vCenter-ben kattintson a **gazdagépek és fürtök** ikonra.

2. Kattintson a jobb gombbal a **munkaterhelés** elemre, és válassza az **új virtuális gép**lehetőséget.
    
    ![Virtuális gép létrehozása](media/create-vcenter-virtual-machine-01.png)

3. Válassza az **új virtuális gép létrehozása** lehetőséget, majd kattintson a **tovább**gombra.

    ![Új virtuális gép varázsló](media/create-vcenter-virtual-machine-02.png)

4. Nevezze el a gépet, válassza ki a **munkaterhelés virtuális gép** mappáját, majd kattintson a **tovább**gombra.

    ![Név és mappa kiválasztása](media/create-vcenter-virtual-machine-03.png)

5. Válassza ki a **munkaterhelés** számítási erőforrását, és kattintson a **tovább**gombra.

    ![Számítási erőforrás kiválasztása](media/create-vcenter-virtual-machine-04.png)

6. Válassza a **vsanDatastore** lehetőséget, majd kattintson a **tovább**gombra.

    ![Tárhely kiválasztása](media/create-vcenter-virtual-machine-05.png)

7. Tartsa meg az alapértelmezett ESXi 6,5 kompatibilitási beállítást, majd kattintson a **tovább**gombra.

    ![Kompatibilitás kiválasztása](media/create-vcenter-virtual-machine-06.png)

8. Válassza ki az ISO-t a virtuális géphez, és kattintson a **tovább**gombra.

    ![Vendég operációs rendszer testreszabása](media/create-vcenter-virtual-machine-07.png)

9. Válassza a merevlemez és hálózati beállítások lehetőséget. Új CD/DVD-meghajtó esetén válassza az **ADATTÁR ISO**-fájlja lehetőséget.  Ha engedélyezni szeretné a nyilvános IP-címről érkező forgalmat erre a virtuális gépre, válassza a hálózatot **VM-1-** ként.

    ![Hardver testreszabásának kiválasztása](media/create-vcenter-virtual-machine-08.png)

10. Megnyílik egy kiválasztási ablak. Válassza ki az ISO-fájlok és Sablonok mappába korábban feltöltött fájlt, majd kattintson **az OK**gombra.

    ![ISO kiválasztása](media/create-vcenter-virtual-machine-10.png)

11. Tekintse át a beállításokat, majd kattintson az **OK** gombra a virtuális gép létrehozásához.

    ![Beállítások áttekintése](media/create-vcenter-virtual-machine-11.png)

A virtuális gép most már hozzá van adva a munkaterhelés számítási erőforrásaihoz, és készen áll a használatra. 

![Új virtuális gép a vCenter-ben](media/create-vcenter-virtual-machine-12.png)

Az alapszintű telepítés már befejeződött. A saját felhőhöz hasonló módon használhatja a helyszíni virtuális gépek infrastruktúráját.

A következő szakaszban a DNS-és DHCP-kiszolgálók saját Felhőbeli számítási feladatokhoz való beállításával és az alapértelmezett hálózati konfiguráció módosításával kapcsolatos opcionális információk szerepelnek.

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>Felhasználók és identitási források hozzáadása a vCenter (nem kötelező)

A CloudSimple egy alapértelmezett vCenter-felhasználói fiókot rendel a **cloudowner@cloudsimple.local** felhasználónévhez. A kezdéshez nincs szükség további fiók beállítására.  A CloudSimple szabályosan rendeli hozzá a rendszergazdákat a normál működéshez szükséges jogosultságok végrehajtásához.  Állítsa be a helyszíni Active Directoryt vagy az Azure AD-t egy [további személyazonossági forrásként](https://docs.azure.cloudsimple.com/set-vcenter-identity/) a privát felhőben.

## <a name="create-a-dns-and-dhcp-server-optional"></a>DNS-és DHCP-kiszolgáló létrehozása (nem kötelező)

A privát felhőalapú környezetekben futó alkalmazások és munkaterhelések névfeloldást és DHCP-szolgáltatásokat igényelnek a keresési és IP-címek hozzárendeléséhez. Ezeknek a szolgáltatásoknak a biztosításához megfelelő DHCP-és DNS-infrastruktúra szükséges. A vCenter virtuális gépet úgy is konfigurálhatja, hogy ezeket a szolgáltatásokat a saját felhőalapú környezetében adja meg.

### <a name="prerequisites"></a>Előfeltételek

* Elosztott porttartomány konfigurált VLAN-val

* Helyszíni vagy internetalapú DNS-kiszolgálókra beállított útvonal

* Virtuálisgép-sablon vagy ISO virtuális gép létrehozásához

A következő hivatkozások útmutatást nyújtanak a DHCP-és DNS-kiszolgálók Linux és Windows rendszeren történő beállításához.

### <a name="linux-based-dns-server-setup"></a>Linux-alapú DNS-kiszolgáló beállítása

A Linux különböző csomagokat kínál a DNS-kiszolgálók beállításához.  Az alábbi hivatkozás egy nyílt forráskódú kötési DNS-kiszolgáló beállítására vonatkozó útmutatást tartalmaz.

[Példa a telepítőre](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

### <a name="windows-based-setup"></a>Windows-alapú telepítés

Ezek a Microsoft-cikkek a Windows Server DNS-kiszolgálóként és DHCP-kiszolgálóként való beállítását ismertetik.
<br>
[Windows Server DNS-kiszolgálóként](https://docs.microsoft.com/windows-server/networking/dns/dns-top)

[Windows Server DHCP-kiszolgálóként](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>Hálózati konfiguráció testreszabása (nem kötelező)

A CloudSimple-portál hálózati lapjainak segítségével megadhatja a virtuális gépekhez tartozó tűzfalak és nyilvános IP-címek konfigurációját.

### <a name="allocate-public-ips"></a>Nyilvános IP-címek lefoglalása

1. Navigáljon a CloudSimple **-portál hálózati > nyilvános IP** -címére.
2. Kattintson a **nyilvános IP-cím**lefoglalása elemre.
3. Adja meg az IP-cím bejegyzését azonosító nevet.
4. Válassza ki a saját felhő helyét.
5. Ha szükséges, használja a csúszkát az Üresjárati időkorlát módosításához.
6. Adja meg azt a helyi IP-címet, amelyhez nyilvános IP-címet szeretne hozzárendelni.
7. Szükség esetén adjon meg egy társított DNS-nevet.
8. Kattintson a **Done** (Kész) gombra.

    ![Nyilvános IP-cím](media/quick-create-pc-public-ip.png)

A nyilvános IP-cím lefoglalásának feladata megkezdődik. A feladat állapotát a **tevékenység > feladatok** oldalon tekintheti meg. A foglalás befejezésekor az új bejegyzés megjelenik a nyilvános IP-címek lapon.

Azt a virtuális gépet, amelyhez az IP-címet le kell képezni, a fent megadott helyi címmel kell konfigurálni. Az IP-cím konfigurálására szolgáló eljárás a virtuális gép operációs rendszerére jellemző. A megfelelő eljáráshoz olvassa el a virtuális gép operációs rendszerének dokumentációját.

#### <a name="example"></a>Példa

Íme például az Ubuntu 16,04 részletei.

Adja hozzá a statikus metódust az inet-címek családjának konfigurációjához ```/etc/network/interfaces```a fájlban. Módosítsa a címeket, a hálózati maszkot és az átjáró értékeit. Ebben a példában a ETH0 felületet, a belső IP-192.168.24.10, az átjáró 192.168.24.1 és a hálózati maszk 255.255.255.0 használjuk. 

Szerkessze ```interfaces``` a fájlt.

```
sudo vi /etc/network/interfaces
```

Frissítse a ```interfaces``` fájl következő szakaszát.

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

Alapértelmezés szerint a rendszer megtagadja az internetrőlérkező összes bejövő forgalmat. Ha bármilyen más portot szeretne megnyitni, hozzon létre egy [Tűzfalszabály-táblázatot](https://docs.azure.cloudsimple.com/firewall/).

Miután a belső IP-címet statikus IP-címként konfigurálta, ellenőrizze, hogy elérhető-e az Internet a virtuális gépről.

```
ping 8.8.8.8
```

Ellenőrizze, hogy elérhető-e a virtuális gép az internetről a nyilvános IP-cím használatával.

Győződjön meg arról, hogy a virtuális gépen lévő összes tűzfal-(iptables-) szabály nem blokkolja a bejövő 80-as portot.

```
netstat -an | grep 80
```

Indítsa el a 80-es portot figyelő http-kiszolgálót.
       
```
python2.7 -m SimpleHTTPServer 80
```

vagy

```
python3 -m http.server 80
```

Nyisson meg egy böngészőt az asztalon, és mutasson a 80-es portra a nyilvános IP-cím megadásához a virtuális gépen található fájlok tallózásához. 

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>Alapértelmezett CloudSimple tűzfalszabályok a nyilvános IP-címekhez

* VPN-forgalom: A VPN és a számítási feladatok összes hálózata és felügyeleti hálózata közötti összes forgalom engedélyezett.
* Privát felhő belső forgalma: A (forrás-és a) munkaterhelés-hálózatok és a fent látható felügyeleti hálózat közötti összes kelet-nyugati forgalom engedélyezett.
* Internetes forgalom:
    * Az internetről érkező összes bejövő forgalom megtagadva a munkaterhelés-hálózatok és a felügyeleti hálózat számára.
    * Az internetre irányuló összes kimenő forgalom a munkaterhelés-hálózatokból vagy a felügyeleti hálózatból engedélyezett.

A tűzfalszabályok funkció használatával módosíthatja a forgalom védelmét is. További információ: [Tűzfalszabályok és szabályok beállítása](https://docs.azure.cloudsimple.com/firewall/).

## <a name="install-solutions-optional"></a>Megoldások telepítése (nem kötelező)
A saját felhőalapú vCenter-környezete teljes körű kihasználása érdekében telepíthet megoldásokat a saját CloudSimple-felhőbe. Beállíthatja a biztonsági mentést, a vész-helyreállítást, a replikálást és a virtuális gépeket védő egyéb funkciókat. Ilyenek például a VMware Site Recovery Manager (VMware SRM) és a Veeam Backup & replikációja.

Megoldás telepítéséhez további jogosultságokat kell megadnia korlátozott időtartamra. Lásd [](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges): a jogosultságok kiterjesztésének megtalálása.

## <a name="next-steps"></a>További lépések

* [VMware virtuális gépek felhasználása az Azure-ban](quickstart-create-vmware-virtual-machine.md)
* [Kapcsolódás helyszíni hálózathoz az Azure ExpressRoute](https://docs.azure.cloudsimple.com/on-premises-connection)
* [VPN-átjárók beállítása a CloudSimple hálózaton](https://docs.azure.cloudsimple.com/vpn-gateway)
