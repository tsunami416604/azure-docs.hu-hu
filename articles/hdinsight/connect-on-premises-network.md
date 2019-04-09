---
title: Az Azure HDInsight csatlakoztatása a helyszíni hálózathoz
description: Útmutató egy HDInsight-fürt létrehozása az Azure Virtual Networkhöz, és hogyan csatlakoztathatja a helyszíni hálózathoz. Útmutató a HDInsight és a helyszíni hálózat között névfeloldás konfigurálása egyéni DNS-kiszolgáló használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/04/2019
ms.openlocfilehash: 52fe8c05101f9647549acec276f0bdb9fa52d1c7
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256804"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>A HDInsight csatlakoztatása a helyszíni hálózathoz

Ismerje meg a HDInsight csatlakoztatása a helyszíni hálózathoz az Azure Virtual Networkökhöz és VPN-átjáró. Ebben a dokumentumban található tervezési tudnivalókat tartalmaz:

* HDInsight használata az Azure Virtual Network, amely a helyszíni hálózathoz csatlakozik.
* Konfigurálja a DNS-névfeloldás a virtuális hálózat és a helyszíni hálózat között.
* A HDInsight internet-hozzáférés korlátozása a hálózati biztonsági csoportok konfigurálása.
* A portok HDInsight biztosítja a virtuális hálózaton.

## <a name="overview"></a>Áttekintés

A csatlakoztatott hálózati kommunikációhoz név szerint a HDInsight és az erőforrások engedélyezéséhez hajtsa végre a következő műveleteket:

* Azure virtuális hálózat létrehozása.
* Hozzon létre egy egyéni DNS-kiszolgálót az Azure Virtual Network.
* Az egyéni DNS-kiszolgáló használata helyett az alapértelmezett Azure rekurzív feloldó virtuális hálózat konfigurálása.
* Az egyéni DNS-kiszolgáló és a helyi DNS-kiszolgáló közötti továbbítás konfigurálása.

Ez a konfiguráció lehetővé teszi, hogy a következő viselkedés:

* A teljesen minősített tartománynevek, amelyek a DNS-utótag kérelmek __a virtuális hálózat__ egyéni DNS-kiszolgálóra továbbítja. Az egyéni DNS-kiszolgáló ezután továbbítja a kérelmeket a az Azure rekurzív feloldó, amely az IP-címet adja vissza.
* Minden más kérelemhez továbbítja a helyi DNS-kiszolgáló. A nyilvános internetes erőforrások – például microsoft.com még akkor is, kérelmek továbbítja a névfeloldáshoz a helyi DNS-kiszolgáló.

Az alábbi ábrán a zöld sorai, amely a virtuális hálózat DNS-utótagját erőforrásokra vonatkozó kéréseket. Kék vonal a helyszíni hálózat vagy a nyilvános interneten erőforrásokra vonatkozó kéréseket.

![DNS-kérések az itt bemutatott konfiguráció feloldási módját ábrázoló diagram](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>Előfeltételek

* Egy SSH-ügyfél. További információkért lásd: [HDInsight (az Apache Hadoop) SSH-val csatlakozhat](./hdinsight-hadoop-linux-use-ssh-unix.md).
* Ha a PowerShell használatával, akkor a [AZ modul](https://docs.microsoft.com/powershell/azure/overview).
* Ha szeretnék használni az Azure CLI-vel, és még nem telepítette azt, lásd: [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-virtual-network-configuration"></a>Virtuális hálózati konfiguráció létrehozása

A következő dokumentumok segítségével megtudhatja, hogyan hozhat létre egy Azure virtuális hálózat, amely a helyszíni hálózathoz csatlakozik:

* [Az Azure Portal használata](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Az Azure PowerShell használata](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Az Azure parancssori felület használata](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Create custom DNS server

> [!IMPORTANT]  
> Kell létrehozni, és a DNS-kiszolgáló konfigurálása a virtuális hálózatban a HDInsight telepítése előtt.

Ezek a lépések használják a [az Azure portal](https://portal.azure.com) egy Azure virtuális gép létrehozásához. Egyéb módokon is létrehozhat egy virtuális gépet, lásd: [virtuális gép létrehozása – Azure CLI-vel](../virtual-machines/linux/quick-create-cli.md) és [virtuális gép létrehozása – Azure PowerShell-lel](../virtual-machines/linux/quick-create-powershell.md).  Használó Linuxos virtuális gép létrehozása a [kötési](https://www.isc.org/downloads/bind/) DNS szoftver, kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
  
2. Lépjen a bal oldali menüből **+ erőforrás létrehozása** > **számítási** > **Ubuntu Server 18.04 LTS**.

    ![Egy Ubuntu virtuális gép létrehozása](./media/connect-on-premises-network/create-ubuntu-vm.png)

3. Az a __alapjai__ lapra, adja meg a következőket:  
  
    | Mező | Érték |
    | --- | --- |
    |Előfizetés |Válassza ki a megfelelő előfizetést.|
    |Erőforráscsoport |Válassza ki a korábban létrehozott virtuális hálózatot tartalmazó erőforráscsoportot.|
    |Virtuális gép neve | Adjon meg egy rövid nevet, amely azonosítja a virtuális gépet. Ez a példa **DNSProxy**.|
    |Régió | Válasszon ugyanabban a régióban, a korábban létrehozott virtuális hálózatot.  Nem minden Virtuálisgép-méret érhető el minden régióban.  |
    |Rendelkezésre állási beállításai |  Válassza ki a kívánt szintű rendelkezésre állást.  Az Azure kínál lehetőségek rendelkezésre állását és rugalmasságát, az alkalmazások kezeléséhez.  A Megoldástervezés adatközpont szolgáltatáskimaradásokat és karbantartási események az alkalmazások és adatok védelme a replikált virtuális gépek rendelkezésre állási zónák vagy rendelkezésre állási csoportok használatával. Ez a példa **szükséges infrastruktúra redundancia**. |
    |Kép | Maradjon a **Ubuntu Server 18.04 LTS**. |
    |Hitelesítés típusa | __Jelszó__ vagy __nyilvános SSH-kulcs__: Az SSH-fiókhoz tartozó hitelesítési módszer. Azt javasoljuk, nyilvános kulcsok, mivel ezek biztonságosabb. Ez a példa **jelszó**.  További információkért lásd: a [létrehozása és használata Linux rendszerű virtuális gépek SSH-kulcsok](../virtual-machines/linux/mac-create-ssh-keys.md) dokumentumot.|
    |Felhasználónév |Adja meg a virtuális gép rendszergazdai felhasználóneve.  Ez a példa **sshuser**.|
    |Jelszó vagy SSH nyilvános kulcs | A rendelkezésre álló mező határozza meg a választott **hitelesítési típus**.  Adja meg a megfelelő értékre.|
    |Nyilvános bejövő portok|Válassza ki **lehetővé teszi a kiválasztott portok**. Válassza ki **SSH (22)** származó a **bejövő portok kiválasztása** legördülő listából.|

    ![Alapszintű virtuálisgép-konfiguráció](./media/connect-on-premises-network/vm-basics.png)

    Más bejegyzések hagyja az alapértelmezett értéken, és válassza ki a **hálózatkezelés** fülre.

4. Az a **hálózatkezelés** lapra, adja meg a következőket:

    | Mező | Érték |
    | --- | --- |
    |Virtuális hálózat | Válassza ki a korábban létrehozott virtuális hálózat.|
    |Alhálózat | Válassza ki a korábban létrehozott virtuális hálózat alapértelmezett alhálózat. Tegye __nem__ használják a VPN-átjáró-alhálózatot válassza ki.|
    |Nyilvános IP-cím | A töltve értéket használja.  |

    ![A virtuális hálózati beállítások](./media/connect-on-premises-network/virtual-network-settings.png)

    Más bejegyzések hagyja az alapértelmezett értéken, és válassza ki a **felülvizsgálat + létrehozása**.

5. A a **felülvizsgálat + létrehozása** lapon jelölje be **létrehozása** a virtuális gép létrehozásához.

### <a name="review-ip-addresses"></a>Tekintse át az IP-címek
A virtuális gép létrehozása után kapni fog egy **üzembe helyezés sikeres** értesítés egy **erőforrás megnyitása** gombra.  Válassza ki **erőforrás megnyitása** , nyissa meg az új virtuális gépet.  Az alapértelmezett nézetben az új virtuális gép kövesse az alábbi lépéseket a társított IP-címek azonosításához:

1. A **beállítások**válassza **tulajdonságok**.

2. Vegye figyelembe a tartozó értékeket **nyilvános IP-cím/DNS-NÉVCÍMKE** és **MAGÁNHÁLÓZATI IP-címet** későbbi használatra.

   ![Nyilvános és privát IP-címek](./media/connect-on-premises-network/vm-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Telepítse és konfigurálja a kötés (DNS-szoftver)

1. Az SSH használatával csatlakozhat a __nyilvános IP-cím__ a virtuális gép. Cserélje le `sshuser` a virtuális gép létrehozásakor megadott SSH felhasználói fiókkal. Az alábbi példa egy virtuális gép 40.68.254.142 csatlakozik:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. Kötési telepítéséhez használja az SSH-munkamenetből a következő parancsokat:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Továbbítsa a névfeloldási kérelmeket a a helyi DNS-kiszolgáló Bind konfigurálásához használja a következő szöveget a tartalmát, a `/etc/bind/named.conf.options` fájlt:

        acl goodclients {
            10.0.0.0/16; # Replace with the IP address range of the virtual network
            10.1.0.0/16; # Replace with the IP address range of the on-premises network
            localhost;
            localnets;
        };

        options {
                directory "/var/cache/bind";

                recursion yes;

                allow-query { goodclients; };

                forwarders {
                192.168.0.1; # Replace with the IP address of the on-premises DNS server
                };

                dnssec-validation auto;

                auth-nxdomain no;    # conform to RFC1035
                listen-on { any; };
        };

    > [!IMPORTANT]  
    > Cserélje le az értékeket a `goodclients` az IP-címtartományt a virtuális hálózat és a helyszíni hálózat szakaszt. Ez a szakasz határozza meg a címeket, a DNS-kiszolgáló fogadja a kéréseit.
    >
    > Cserélje le a `192.168.0.1` bejegyzést a `forwarders` a helyi DNS-kiszolgáló IP-címmel rendelkező szakaszában. Ez a bejegyzés a megoldás a helyi DNS-kiszolgáló DNS-kérésekre irányítja.

    Ez a fájl szerkesztéséhez használja a következő parancsot:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Mentse a fájlt, használja a __Ctrl + X__, __Y__, majd __Enter__.

4. Az SSH-munkamenetből a következő paranccsal:

    ```bash
    hostname -f
    ```

    Ez a parancs egy értéket ad vissza az alábbi szöveghez hasonló:

    ```output
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    A `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` szöveg a __DNS-utótag__ ehhez a virtuális hálózathoz. Mentse ezt az értéket, mivel később még használni fogjuk.

5. Kötési feloldani a DNS-nevek a virtuális hálózaton belüli erőforrásokhoz konfigurálásához használja a következő szöveget a tartalmát, a `/etc/bind/named.conf.local` fájlt:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]  
    > Cserélje le a `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` a korábban kapott a DNS-utótagot.

    Ez a fájl szerkesztéséhez használja a következő parancsot:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Mentse a fájlt, használja a __Ctrl + X__, __Y__, majd __Enter__.

6. Kötési indításához használja a következő parancsot:

    ```bash
    sudo service bind9 restart
    ```

7. Győződjön meg arról, hogy a kötés képes névfeloldásra a helyszíni hálózati erőforrások, használja a következő parancsokat:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > Cserélje le `dns.mynetwork.net` az erőforrás a helyszíni hálózat teljesen minősített tartománynevét (FQDN).
    >
    > Cserélje le `10.0.0.4` együtt a __belső IP-cím__ az egyéni DNS-kiszolgáló a virtuális hálózatban.

    A válasz jelenik meg az alábbi szöveghez hasonló:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>Virtuális hálózat használatához az egyéni DNS-kiszolgáló konfigurálása

A virtuális hálózat használatához az egyéni DNS-kiszolgáló helyett az Azure rekurzív feloldó konfigurálásához használja az alábbi lépéseket a a [az Azure portal](https://portal.azure.com):

1. Lépjen a bal oldali menüből **minden szolgáltatás** > **hálózatkezelés** > **virtuális hálózatok**.

2. Válassza ki a virtuális hálózat a listából, ekkor megnyílik az alapértelmezett nézet a virtuális hálózat.  

3. Az alapértelmezett nézet a csoportban **beállítások**, jelölje be **DNS-kiszolgálók**.  

4. Válassza ki __egyéni__, és adja meg a **MAGÁNHÁLÓZATI IP-címet** az egyéni DNS-kiszolgáló.   

5. Kattintson a __Mentés__ gombra.  <br />  

    ![Az egyéni DNS-kiszolgáló a hálózat beállítása](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>A helyi DNS-kiszolgáló konfigurálása

Az előző szakaszban konfigurált továbbítja a kérelmeket a helyi DNS-kiszolgáló egyéni DNS-kiszolgáló. Ezután konfigurálnia kell a helyi DNS-kiszolgáló továbbítja a kérelmeket az egyéni DNS-kiszolgáló.

A DNS-kiszolgáló konfigurálásának lépéseit tekintse meg a DNS-kiszolgáló szoftver dokumentációját. Keresse meg a lépéseket, hogyan kell beállítani egy __feltételes továbbítók__.

Feltételes továbbítás csak továbbítja a kéréseket a kapcsolatspecifikus DNS-utótag számára. Ebben az esetben konfigurálnia kell a virtuális hálózat DNS-utótag egy továbbítót. Ennek az utótagnak kérelmeket, továbbítani kell az egyéni DNS-kiszolgáló IP-címét. 

A következő szöveg egy feltételes továbbítók konfigurációja példája a **kötési** DNS szoftvereket:

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

A DNS használatával kapcsolatos információk **Windows Server 2016**, tekintse meg a [Add-DnsServerConditionalForwarderZone](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone) dokumentáció...

Miután konfigurálta a helyi DNS-kiszolgáló, `nslookup` a helyszíni hálózatról, győződjön meg arról, hogy fel tudja oldani a virtuális hálózat nevét. Az alábbi példa 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

Ebben a példában a helyi DNS-kiszolgáló 196.168.0.4 oldani a nevet az egyéni DNS-kiszolgáló használ. Cserélje le az IP-címet a helyi DNS-kiszolgáló esetében. Cserélje le a `dnsproxy` cím az az egyéni DNS-kiszolgáló teljesen minősített tartománynevét.

## <a name="optional-control-network-traffic"></a>Nem kötelező: Hálózati forgalom szabályozása

Hálózati biztonsági csoportok (NSG) vagy a felhasználó által megadott útvonalak (UDR) segítségével szabályozhatja a hálózati forgalmat. Az NSG-k lehetővé teszik a bejövő és kimenő forgalom szűrése és az adatforgalom engedélyezéséhez vagy letiltásához a. Udr-EK engedélyezése szabályozható a virtuális hálózat, az internet és a helyszíni hálózati erőforrások közötti forgalom adatfolyamait.

> [!WARNING]  
> HDInsight bejövő hozzáférést az adott IP-címek az Azure-felhőben, és korlátlan kimenő hozzáférést igényel. NSG-k vagy udr-EK használata a forgalom szabályozása, akkor végezze el az alábbi lépéseket:

1. Az IP-címek keresése, amely tartalmazza a virtuális hálózat helyét. Hely szerint a szükséges IP-címek listáját lásd: [szükséges IP-címek](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip).

2. Az 1. lépésben azonosított IP-címek, a bejövő adatforgalom engedélyezésére IP-Címre a címeket.

   * Ha használ __NSG__: Lehetővé teszi __bejövő__ port forgalmát __443-as__ az IP-címek.
   * Ha használ __UDR__: Állítsa be a __következő Ugrás__ mutató útvonal típusú __Internet__ az IP-címek.

Az NSG-k létrehozása az Azure PowerShell vagy az Azure parancssori felület használatának példája, tekintse meg a [HDInsight kiterjesztése az Azure Virtual Network szolgáltatással](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-nsg) dokumentumot.

## <a name="create-the-hdinsight-cluster"></a>A HDInsight-fürt létrehozása

> [!WARNING]  
> A virtuális hálózaton található HDInsight telepítése előtt konfigurálnia kell az egyéni DNS-kiszolgáló.

Kövesse a [hozzon létre egy HDInsight-fürtön az Azure Portalon](./hdinsight-hadoop-create-linux-clusters-portal.md) dokumentum hozhat létre HDInsight-fürtöt.

> [!WARNING]  
> * Fürt létrehozása során ki kell választania, amely tartalmazza a virtuális hálózat helyét.
> * Az a __speciális beállítások__ rész-konfiguráció, jelöljön ki a virtuális hálózatot és alhálózatot, amelyet korábban hozott létre.

## <a name="connecting-to-hdinsight"></a>A HDInsight csatlakoztatása

A legtöbb dokumentáció a HDInsight feltételezi, hogy a fürt a hozzáférést az interneten keresztül. Például hogy a `https://CLUSTERNAME.azurehdinsight.net` címen tud csatlakozni a fürthöz. A cím, amely nem érhető el, ha használta az NSG-k vagy udr-EK hozzáférés korlátozása az internetről a nyilvános átjárót használja.

Néhány dokumentáció is hivatkozik `headnodehost` történő csatlakozás a fürthöz SSH-munkamenetből. Ez a cím csak a fürt csomópontjainak érhető el, és már nem használható a virtuális hálózaton keresztül csatlakozó ügyfeleken.

A virtuális hálózaton keresztül, közvetlenül kapcsolódhat a HDInsight, használja az alábbi lépéseket:

1. Fedezze fel a HDInsight-fürtcsomópontok belső teljesen minősített tartományneveket, használja a következő módszerek egyikét:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. Annak megállapításához, a port, amelyet egy szolgáltatás érhető el, tekintse meg a [HDInsight az Apache Hadoop-szolgáltatások által használt portok](./hdinsight-hadoop-port-settings-for-services.md) dokumentumot.

    > [!IMPORTANT]  
    > Néhány az átjárócsomópontokkal üzemeltetett szolgáltatásokra: csak az egyik csomóponton aktív egyszerre. Ha az egyik fő csomópont egy szolgáltatás eléréséhez meg, és akkor sem jár sikerrel, váltson a fő csomópontot.
    >
    > Például az Apache Ambari csak akkor aktív, az egyik fő csomópont egyszerre. Ha a 404-es hibát ad vissza, próbálja ki fér hozzá az Ambari az egyik fő csomópont, majd futó a fő csomópontot.

## <a name="next-steps"></a>További lépések

* A HDInsight segítségével a virtuális hálózat további információkért lásd: [HDInsight kiterjesztése az Azure-beli virtuális hálózatok használatával](./hdinsight-extend-hadoop-virtual-network.md).

* Azure virtuális hálózataiban működő további információkért lásd: a [Azure Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md).

* Hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](../virtual-network/security-overview.md).

* További információ a felhasználó által megadott útvonalak: [felhasználó által megadott útvonalak és IP-továbbítás](../virtual-network/virtual-networks-udr-overview.md).
