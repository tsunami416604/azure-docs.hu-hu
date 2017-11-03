---
title: "HDInsight csatlakozni a helyszíni hálózatra - Azure HDInsight |} Microsoft Docs"
description: "Útmutató a HDInsight-fürtök létrehozása egy Azure virtuális hálózatra, és csatlakoztassa a helyszíni hálózat. Megtudhatja, hogyan konfigurálhatja a névfeloldást HDInsight és a helyszíni hálózat között egyéni DNS-kiszolgáló használatával."
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/21/2017
ms.author: larryfr
ms.openlocfilehash: 27a5d0e69ec9c47feab2b23d7c79fe2547edfc08
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="connect-hdinsight-to-your-on-premise-network"></a>HDInsight csatlakozni a helyi hálózatra

Útmutató a HDInsight hálózathoz való kapcsolódáshoz a helyszíni Azure virtuális hálózatok és a VPN-átjáró használatával. Ez a dokumentum a tervezési tudnivalókat tartalmaz:

* Egy Azure virtuális hálózatot, amely a helyszíni hálózat csatlakozik a HDInsight eszközzel.

* Konfigurálja a DNS-név feloldása a virtuális hálózat és a helyszíni hálózat között.

* A HDInsight internet-hozzáférés korlátozása a hálózati biztonsági csoportok konfigurálása.

* A virtuális hálózaton a HDInsight által biztosított portok.

## <a name="create-the-virtual-network-configuration"></a>A virtuális hálózati konfiguráció létrehozása

A következő dokumentumok segítségével megtudhatja, hogyan hozzon létre egy Azure virtuális hálózatot, amely a helyszíni hálózat csatlakozik:
    
* [Az Azure Portal használata](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

* [Az Azure PowerShell használata](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

* [Az Azure parancssori felület használata](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="configure-name-resolution"></a>Névfeloldás konfigurálása

A csatlakoztatott hálózati neve történő kommunikációra HDInsight és erőforrások engedélyezéséhez el kell végeznie a következő műveleteket:

* Hozzon létre egy egyéni DNS-kiszolgáló Azure virtuális hálózatban.

* A virtuális hálózatot az egyéni DNS-kiszolgáló használata helyett az alapértelmezett Azure rekurzív feloldó konfigurálni.

* Konfigurálja az egyéni DNS-kiszolgáló és a helyi DNS-kiszolgáló között.

Ez a konfiguráció lehetővé teszi, hogy az alábbiakra:

* A teljes tartományneveit, amelyek rendelkeznek a DNS-utótag kérelmek __a virtuális hálózat__ jutnak el az egyéni DNS-kiszolgáló. Az egyéni DNS-kiszolgáló majd továbbítja ezeket a kérelmeket az Azure rekurzív feloldó, amely az IP-címet adja vissza.

* Minden más kérelemhez továbbítja a helyi DNS-kiszolgáló. A nyilvános internet erőforrások, például a microsoft.com még akkor is, kérelmeket a rendszer a helyi DNS-kiszolgálót a névfeloldáshoz továbbítja.

Az alábbi ábra a zöld sorai, hogy a virtuális hálózat DNS-utótag erőforrásokra vonatkozó kéréseket. Kék sorai a helyszíni hálózat vagy a nyilvános interneten erőforrásokra vonatkozó kéréseket.

![A konfigurációban a dokumentumban használt DNS-kérelmek feloldása ábrája](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

### <a name="create-a-custom-dns-server"></a>Hozzon létre egy egyéni DNS-kiszolgáló

> [!IMPORTANT]
> Hozzon létre és a DNS-kiszolgáló a HDInsight a virtuális hálózaton történő telepítése előtt konfigurálnia kell.

Linux virtuális gép által használt létrehozásához a [kötési](https://www.isc.org/downloads/bind/) DNS szoftver, kövesse az alábbi lépéseket:

> [!NOTE]
> Az alábbi lépéseket használja a [Azure-portálon](https://portal.azure.com) egy Azure virtuális gép létrehozásához. Egyéb módon hozhat létre a virtuális gép tekintse meg a következő dokumentumokat:
>
> * [Virtuális gép létrehozása – Azure CLI](../virtual-machines/linux/quick-create-cli.md)
> * [Virtuális gép - létrehozása az Azure PowerShell](../virtual-machines/linux/quick-create-portal.md)

1. Az a [Azure-portálon](https://portal.azure.com), jelölje be  __+__ , __számítási__, és __Ubuntu Server 16.04 LTS__.

    ![Egy Ubuntu virtuális gép létrehozása](./media/connect-on-premises-network/create-ubuntu-vm.png)

2. Az a __alapjai__ területen adja meg a következő információkat:

    * __Név__: egy rövid nevet, amely azonosítja a virtuális gépet. Például __DNSProxy__.
    * __Felhasználónév__: SSH-fiókjának nevét.
    * __Nyilvános SSH-kulcs__ vagy __jelszó__: az SSH-fiókhoz tartozó hitelesítési módszer. Azt javasoljuk, nyilvános kulcsok, mert azok biztonságosabb. További információkért lásd: a [Linux virtuális gépek létrehozása és használata SSH-kulcsok](../virtual-machines/linux/mac-create-ssh-keys.md) dokumentum.
    * __Erőforráscsoport__: válasszon __meglévő__, majd válassza ki a korábban létrehozott virtuális hálózat tartalmazó erőforráscsoportot.
    * __Hely__: Jelölje ki a virtuális hálózat ugyanazon a helyen.

    ![Alapszintű virtuálisgép-konfiguráció](./media/connect-on-premises-network/vm-basics.png)

    Más bejegyzések hagyja meg az alapértelmezett értékeket, és válassza ki __OK__.

3. Az a __méret kiválasztása__ területen válassza ki a Virtuálisgép-méretet. A jelen oktatóanyag esetében válassza legkisebb és a legalacsonyabb költséget. A folytatáshoz használja a __válasszon__ gombra.

4. Az a __beállítások__ területen adja meg a következő információkat:

    * __Virtuális hálózati__: válassza ki a korábban létrehozott virtuális hálózatot.

    * __Alhálózati__: Jelölje ki az alapértelmezett alhálózatot a virtuális hálózat. Tegye __nem__ jelölje ki az alhálózatot, a VPN-átjáró által használt.

    * __Diagnosztikai tárfiók__: Válasszon egy meglévő tárfiókot használ, vagy hozzon létre egy újat.

    ![Virtuális hálózati beállítások](./media/connect-on-premises-network/virtual-network-settings.png)

    A többi bejegyzés hagyja az alapértelmezett értéket, majd válasszon __OK__ folytatja.

5. Az a __beszerzési__ szakaszban jelölje be a __beszerzési__ a virtuális gép létrehozása gombra.

6. A virtuális gép létrehozása után annak __áttekintése__ szakasz jelenik meg. Válassza ki a listáról a bal oldali __tulajdonságok__. Mentse a __nyilvános IP-cím__ és __magánhálózati IP-cím__ értékeket. A következő szakaszban fogja használni.

    ![Nyilvános és magánhálózati IP-címek](./media/connect-on-premises-network/vm-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Telepítse és konfigurálja a Bind (DNS szoftver)

1. SSH használatával csatlakozhat a __nyilvános IP-cím__ a virtuális gép. Az alábbi példában egy virtuális gépet, 40.68.254.142 csatlakozik:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Cserélje le `sshuser` a fürt létrehozásakor megadott SSH a felhasználói fiókkal.

    > [!NOTE]
    > Nincsenek az sokféleképpen beszerzése a `ssh` segédprogram. A Linux, Unix és macOS Ez biztosítja az operációs rendszer részeként. Ha Windows használja, fontolja meg az alábbi lehetőségek közül:
    >
    > * [Azure-felhőbe rendszerhéj](../cloud-shell/quickstart.md)
    > * [A Windows 10 Ubuntu bash](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. A kötés telepítéséhez használja az SSH-munkamenetet a következő parancsokat:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Bind továbbítani a névfeloldási kérelmeket a helyszíni DNS-kiszolgáló konfigurálásához használja a következő szöveget a tartalmát a `/etc/bind/named.conf.options` fájlt:

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
    > Cserélje le az értékeket a `goodclients` a virtuális hálózat és a helyi hálózaton az IP-címtartománnyal rendelkező szakasz. Ebben a szakaszban határozza meg a címeket, DNS-kiszolgáló elfogadja az érkező kérelmeket.
    >
    > Cserélje le a `192.168.0.1` bejegyzést a `forwarders` a helyi DNS-kiszolgáló IP-című szakasz. Ez a bejegyzés a helyi DNS-kiszolgálót továbbítja a DNS-kérésekre.

    Ez a fájl szerkesztéséhez használja a következő parancsot:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Mentse a fájlt, használja a __Ctrl + X__, __Y__, majd __Enter__.

4. Az SSH-munkamenetből használja a következő parancsot:

    ```bash
    hostname -f
    ```

    Ez a parancs értéket ad vissza az alábbihoz hasonló:

        dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    A `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` szöveg a __DNS-utótag__ ehhez a virtuális hálózathoz. Mentse ezt az értéket, a rendszer később.

5. Konfigurálja a DNS-nevek a virtuális hálózaton lévő erőforrások köti, használja a következő szöveg tartalmát a `/etc/bind/named.conf.local` fájlt:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]
    > Le kell cserélnie a `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` a korábban kapott DNS-utótagját.

    Ez a fájl szerkesztéséhez használja a következő parancsot:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Mentse a fájlt, használja a __Ctrl + X__, __Y__, majd __Enter__.

6. Indítsa el a kötési, használja a következő parancsot:

    ```bash
    sudo service bind9 restart
    ```

7. Győződjön meg arról, hogy a kötés képes névfeloldásra a helyszíni hálózati erőforrások, az alábbi parancsokat használja:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]
    > Cserélje le `dns.mynetwork.net` a helyszíni hálózat erőforrás teljes tartománynevét (FQDN).
    >
    > Cserélje le `10.0.0.4` rendelkező a __belső IP-cím__ a egyéni DNS-kiszolgáló a virtuális hálózat.

    A válasz megjelenik az alábbihoz hasonló:

        Server:         10.0.0.4
        Address:        10.0.0.4#53

        Non-authoritative answer:
        Name:   dns.mynetwork.net
        Address: 192.168.0.4

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Az egyéni DNS-kiszolgáló használatára a virtuális hálózat konfigurálása

A virtuális hálózat az egyéni DNS-kiszolgáló használata helyett az Azure rekurzív feloldó konfigurálásához használja az alábbi lépéseket:

1. Az a [Azure-portálon](https://portal.azure.com), a virtuális hálózat, majd válassza ki és __DNS-kiszolgálók__.

2. Válassza ki __egyéni__, és írja be a __belső IP-cím__ az egyéni DNS-kiszolgáló. Végül válassza ki __mentése__.

    ![Az egyéni DNS-kiszolgáló a hálózat beállítása](./media/connect-on-premises-network/configure-custom-dns.png)

### <a name="configure-the-on-premises-dns-server"></a>A helyi DNS-kiszolgáló konfigurálása

Az előző szakaszban konfigurálta az egyéni DNS-kiszolgálót a helyi DNS-kiszolgáló kérelmeket továbbítja. Ezután konfigurálnia kell a helyi DNS-kiszolgálót az egyéni DNS-kiszolgáló kérelmeket továbbítja.

A DNS-kiszolgáló konfigurálásának részletes lépéseit tekintse meg a DNS-kiszolgáló szoftver dokumentációját. Keresse meg a lépéseket, hogyan kell beállítani egy __feltételes továbbítók__.

Feltételes továbbítás csak egy kapcsolatspecifikus DNS-utótag kérelmek továbbítja. Ebben az esetben konfigurálnia kell a virtuális hálózat DNS-utótag továbbító. Ez utótag kérelmek továbbítani kell az egyéni DNS-kiszolgáló IP-címét. 

A következő szöveg látható egy példa feltételes továbbító konfigurációját a **kötési** DNS szoftver:

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

A DNS-sel kapcsolatos **Windows Server 2016**, tekintse meg a [Add-DnsServerConditionalForwarderZone](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone) dokumentáció...

Miután konfigurálta a helyi DNS-kiszolgáló, használhat `nslookup` ellenőrzése, hogy a virtuális hálózati nevekkel oldhatja a helyi hálózatról. Az alábbi példa 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

Ebben a példában a helyi DNS-kiszolgáló 196.168.0.4 feloldani a nevet az egyéni DNS-kiszolgáló használja. Cserélje le a helyi DNS-kiszolgáló egy IP-címét. Cserélje le a `dnsproxy` címmel az egyéni DNS-kiszolgáló teljesen minősített tartománynevét.

## <a name="optional-control-network-traffic"></a>Választható lehetőség: Vezérlő hálózati forgalom

Hálózati biztonsági csoportokkal (NSG) vagy a felhasználó által definiált útvonalak (UDR) segítségével szabályozhatja a hálózati forgalom. Az NSG-k lehetővé teszik a bejövő és kimenő forgalmat, és adatforgalom engedélyezéséhez vagy letiltásához a. Udr-EK engedélyezi, hogy hogyan a forgalom a virtuális hálózat, az internethez és a helyszíni hálózati erőforrások között szabályozhatja.

> [!WARNING]
> HDInsight Azure felhőben adott IP-címekről érkező bejövő hozzáférést, és korlátlan kimenő hozzáférést igényel. Ha az NSG-k vagy udr-EK segítségével szabályozza a forgalmat, a következő lépésekkel kell meg:
>
> 1. Az IP-címek keresése, amely tartalmazza a virtuális hálózat helyét. Hely szerint szükséges IP-címek listáját lásd: [szükséges IP-címek](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip).
>
> 2. Az IP-címekről érkező bejövő forgalom engedélyezése.
>
>    * __NSG__: engedélyezése __bejövő__ porton forgalom __443-as__ a a __Internet__.
>    * __UDR__: állítsa be a __következő ugrásaként__ az útvonal típusú __Internet__.

Az NSG-k létrehozásához Azure PowerShell vagy az Azure parancssori felület használatának példája, tekintse meg a [kiterjesztése HDInsight az Azure virtuális hálózatokkal](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-nsg) dokumentum.

## <a name="create-the-hdinsight-cluster"></a>A HDInsight-fürt létrehozása

> [!WARNING]
> A virtuális hálózat HDInsight telepítése előtt konfigurálnia kell az egyéni DNS-kiszolgáló.

Az alábbi témakörben található lépésekkel a [az Azure portál használatával HDInsight-fürtök létrehozása](./hdinsight-hadoop-create-linux-clusters-portal.md) dokumentum hozhat létre HDInsight-fürtöt.

> [!WARNING]
> * Fürt létrehozása során ki kell választania, amely tartalmazza a virtuális hálózat helyét.
>
> * Az a __speciális beállítások__ rész-konfiguráció, ki kell választania a virtuális hálózat és a korábban létrehozott alhálózati.

## <a name="connecting-to-hdinsight"></a>HDInsight csatlakozik

A legtöbb dokumentáció a HDInsight feltételezi, hogy rendelkezik-e a fürt eléréséhez az interneten keresztül. Például, hogy a fürthöz https://CLUSTERNAME.azurehdinsight.net kapcsolódhat. A cím használja a nyilvános átjáró, amely nem érhető el, ha már használta az NSG-k vagy udr-EK hozzáférés korlátozása az internetről.

Bizonyos dokumentációkban is hivatkozik `headnodehost` az SSH-munkamenetet a fürthöz történő csatlakozás során. Ez a cím csak érhető el a fürt, és nem használható a virtuális hálózaton keresztül csatlakozó ügyfeleken.

Közvetlenül csatlakozhat a HDInsight a virtuális hálózaton keresztül, tegye a következőket:

1. Annak megállapításához, a belső teljes tartománynevek a HDInsight-fürtcsomóponton, használja a következő módszerek egyikét:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

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

2. Annak megállapításához, a portot, amelyet a szolgáltatás érhető el, tekintse meg a [a HDInsight Hadoop-szolgáltatás által használt portok](./hdinsight-hadoop-port-settings-for-services.md) dokumentum.

    > [!IMPORTANT]
    > Az átjárócsomópontokkal tárolt egyes szolgáltatások aktívak csak az egyik csomópont egyszerre. Ha megpróbál egy központi csomóponton szolgáltatások elérésére, és sikertelen lesz, a más átjárócsomópont vált.
    >
    > Például Ambari csak egy központi csomóponton aktív egyszerre lehet. Ha egy központi csomóponton Ambari elérése 404 hibaüzenetet ad vissza, majd fut. a más központi csomóponton.

## <a name="next-steps"></a>Következő lépések

* A HDInsight eszközzel virtuális hálózatban további információkért lásd: [kiterjesztése HDInsight Azure virtuális hálózatok használatával](./hdinsight-extend-hadoop-virtual-network.md).

* Azure virtuális hálózataihoz további információkért tekintse meg a [Azure Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md).

* Hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](../virtual-network/virtual-networks-nsg.md).

* A felhasználó által definiált útvonalak további információkért lásd: [felhasználó által definiált útvonalak és IP-továbbítás](../virtual-network/virtual-networks-udr-overview.md).
