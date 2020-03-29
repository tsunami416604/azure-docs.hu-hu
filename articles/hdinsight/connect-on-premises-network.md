---
title: Az Azure HDInsight csatlakoztatása a helyszíni hálózathoz
description: Ismerje meg, hogyan hozhat létre HDInsight-fürtöt egy Azure virtuális hálózatban, majd csatlakoztathatja azt a helyszíni hálózathoz. Megtudhatja, hogy miként konfigurálhatja a névfeloldást a HDInsight és a helyszíni hálózat között egyéni DNS-kiszolgáló használatával.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: 2ed7a5b9c81d1b50f80f379a88688b69c49ed382
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897918"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>A HDInsight csatlakoztatása a helyszíni hálózathoz

Ismerje meg, hogyan csatlakoztathatja a HDInsightot a helyszíni hálózathoz az Azure virtuális hálózatok és egy VPN-átjáró használatával. Ez a dokumentum tervezési információkat tartalmaz:

* HdInsight használata egy Azure virtuális hálózat, amely csatlakozik a helyszíni hálózathoz.
* Dns-névfeloldás konfigurálása a virtuális hálózat és a helyszíni hálózat között.
* Hálózati biztonsági csoportok konfigurálása a HDInsight internet-hozzáféréskorlátozása érdekében.
* A HDInsight által a virtuális hálózaton biztosított portok.

## <a name="overview"></a>Áttekintés

Ahhoz, hogy a HDInsight és az összekapcsolt hálózat erőforrásai név szerint kommunikálhassanak, a következő műveleteket kell végrehajtania:

1. Hozzon létre Azure virtuális hálózatot.
1. Hozzon létre egy egyéni DNS-kiszolgálót az Azure virtuális hálózatban.
1. Állítsa be a virtuális hálózatot úgy, hogy az alapértelmezett Azure Rekurzív resolver helyett az egyéni DNS-kiszolgálót használja.
1. Konfigurálja az egyéni DNS-kiszolgáló és a helyszíni DNS-kiszolgáló közötti továbbítást.

Ezek a konfigurációk a következő viselkedést teszik lehetővé:

* A __virtuális hálózat__ DNS-utótaggal rendelkező, teljesen minősített tartománynevekre vonatkozó kérelmeket a rendszer továbbítja az egyéni DNS-kiszolgálónak. Az egyéni DNS-kiszolgáló ezután továbbítja ezeket a kérelmeket az Azure Rekurzív resolver, amely visszaadja az IP-címet.
* Az összes többi kérés továbbításra kerülnek a helyszíni DNS-kiszolgálóra. Még a nyilvános internetes erőforrásokra, például a microsoft.com vonatkozó kéréseket is továbbítja a helyszíni DNS-kiszolgálónév-feloldáscéljából.

A következő ábrán a zöld vonalak olyan erőforrásokra vonatkozó kérelmek, amelyek a virtuális hálózat DNS-utótagjával végződnek. A kék vonalak a helyszíni hálózat vagy a nyilvános internet erőforrásainak kérései.

![A DNS-kérelmek konfigurációban történő megoldásának diagramja](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>Előfeltételek

* Egy SSH-ügyfél. További információ: [Csatlakozás a HDInsighthoz (Apache Hadoop) az SSH használatával.](./hdinsight-hadoop-linux-use-ssh-unix.md)
* A PowerShell használata esetén szüksége lesz az [AZ-modulra.](https://docs.microsoft.com/powershell/azure/overview)
* Ha használni szeretné az Azure CLI-t, és még nem telepítette, olvassa el [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)című témakört.

## <a name="create-virtual-network-configuration"></a>Virtuális hálózati konfiguráció létrehozása

Az alábbi dokumentumok ból megtudhatja, hogyan hozhat létre helyszíni hálózathoz csatlakoztatott Azure virtuális hálózatot:

* [Az Azure Portal használata](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Az Azure PowerShell használata](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Az Azure parancssori felület használata](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Egyéni DNS-kiszolgáló létrehozása

> [!IMPORTANT]  
> A HDInsight virtuális hálózatra történő telepítése előtt létre kell hoznia és konfigurálnia kell a DNS-kiszolgálót.

Ezek a lépések az [Azure Portal használatával](https://portal.azure.com) hozzon létre egy Azure virtuális gép. A virtuális gépek létrehozásának egyéb módjairól a [Virtuális gép létrehozása – Azure CLI](../virtual-machines/linux/quick-create-cli.md) és [Virtuálisgép létrehozása – Azure PowerShell című témakörben található.](../virtual-machines/linux/quick-create-powershell.md)  A [Bind](https://www.isc.org/downloads/bind/) DNS-szoftvert használó Linux virtuális gép létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
  
1. A felső menüben válassza a **+ Erőforrás létrehozása**lehetőséget.

    ![Ubuntu virtuális gép létrehozása](./media/connect-on-premises-network/azure-portal-create-resource.png)

1. Válassza a**Virtuális gép** **számítása** > lehetőséget a **Virtuálisgép létrehozása** lapra.

1. Az __Alapok__ lapon adja meg a következő adatokat:  
  
    | Mező | Érték |
    | --- | --- |
    |Előfizetés |Válassza ki a megfelelő előfizetést.|
    |Erőforráscsoport |Válassza ki azt az erőforráscsoportot, amely a korábban létrehozott virtuális hálózatot tartalmazza.|
    |Virtuális gép neve | Adjon meg egy rövid nevet, amely azonosítja ezt a virtuális gépet. Ez a példa **a DNSProxy -t**használja.|
    |Régió | Válassza ki ugyanazt a régiót, amely et korábban létrehozott.  Nem minden virtuális gép mérete érhető el minden régióban.  |
    |Rendelkezésre állási beállítások |  Válassza ki a kívánt rendelkezésre állási szintet.  Az Azure számos lehetőséget kínál az alkalmazások rendelkezésre állásának és rugalmasságának kezelésére.  Tervezze meg a megoldást, hogy replikált virtuális gépeket használjon a rendelkezésre állási zónákban vagy a rendelkezésre állási csoportokban az alkalmazások és az adatok adatközpont-kimaradások és karbantartási események elleni védelméhez. Ez a példa nem használ **infrastruktúra-redundanciát.** |
    |Kép | Hagyja az **Ubuntu Server 18.04 LTS**. |
    |Hitelesítés típusa | __Jelszó__ vagy __SSH nyilvános kulcs__: Az SSH-fiók hitelesítési módja. Nyilvános kulcsok használatát javasoljuk, mivel azok biztonságosabbak. Ez a példa **a Jelszó .**  További információ: [SSH-kulcsok létrehozása és használata Linux os virtuális gépekhez](../virtual-machines/linux/mac-create-ssh-keys.md) dokumentum.|
    |Felhasználónév |Adja meg a virtuális gép rendszergazdai felhasználónevét.  Ez a példa **az sshuser t**használja.|
    |Jelszó vagy SSH nyilvános kulcs | A rendelkezésre álló mezőt a **Hitelesítés típusának választása**határozza meg.  Adja meg a megfelelő értéket.|
    |Nyilvános bejövő portok|Válassza **a Kijelölt portok engedélyezése**lehetőséget. Ezután válassza az **SSH (22)** lehetőséget a **Bejövő portok kiválasztása** legördülő listából.|

    ![A virtuális gép alapkonfigurációja](./media/connect-on-premises-network/virtual-machine-basics.png)

    Hagyja a többi bejegyzést az alapértelmezett értékeken, majd válassza a **Hálózat** lapot.

4. A **Hálózat** lapon adja meg a következő adatokat:

    | Mező | Érték |
    | --- | --- |
    |Virtuális hálózat | Válassza ki a korábban létrehozott virtuális hálózatot.|
    |Alhálózat | Válassza ki a korábban létrehozott virtuális hálózat alapértelmezett alhálózatát. __Ne__ válassza ki a VPN-átjáró által használt alhálózatot.|
    |Nyilvános IP-cím | Használja az automatikusan feltöltött értéket.  |

    ![HDInsight virtuális hálózati beállítások](./media/connect-on-premises-network/virtual-network-settings.png)

    Hagyja a többi bejegyzést az alapértelmezett értékeken, majd válassza a **Véleményezés + create lehetőséget.**

5. A **Véleményezés + létrehozás** lapon válassza a **Létrehozás** lehetőséget a virtuális gép létrehozásához.

### <a name="review-ip-addresses"></a>IP-címek áttekintése

A virtuális gép létrehozása után kap egy **telepítési sikeres** értesítést egy Ugrás az **erőforrásra** gombbal.  Válassza **az Ugrás erőforrásra** lehetőséget az új virtuális géphez való ugráshoz.  Az új virtuális gép alapértelmezett nézetéből kövesse az alábbi lépéseket a társított IP-címek azonosításához:

1. A **Beállítások lapon**válassza a Tulajdonságok **lehetőséget.**

2. Vegye figyelembe a **NYILVÁNOS IP-CÍM/DNS-névcímke** és a **SAJÁT IP-CÍM értékeit** későbbi használatra.

   ![Nyilvános és privát IP-címek](./media/connect-on-premises-network/virtual-machine-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>A Bind (DNS-szoftver) telepítése és konfigurálása

1. Az SSH használatával csatlakozhat a virtuális gép __nyilvános IP-címéhez.__ Cserélje `sshuser` le a virtuális gép létrehozásakor megadott SSH felhasználói fiókkal. A következő példa 40.68.254.142-es sebességgel csatlakozik egy virtuális géphez:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. A Bind telepítéséhez használja az SSH-munkamenet következő parancsait:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Ha be szeretné állítani a Bind névfeloldási kérelmek továbbítását a helyszíni `/etc/bind/named.conf.options` DNS-kiszolgálóra, használja a következő szöveget a fájl tartalmaként:

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
    > Cserélje le a `goodclients` szakasz értékeit a virtuális hálózat és a helyszíni hálózat IP-címtartományára. Ez a szakasz azokat a címeket határozza meg, amelyekről a DNS-kiszolgáló fogadja a kéréseket.
    >
    > Cserélje `192.168.0.1` le a `forwarders` szakaszban lévő bejegyzést a helyszíni DNS-kiszolgáló IP-címére. Ez a bejegyzés a DNS-kérelmeket a helyszíni DNS-kiszolgálóra irányítja megoldásra.

    A fájl szerkesztéséhez használja a következő parancsot:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    A fájl mentéséhez használja a __Ctrl+X,__ __Y__, majd __az Enter billentyűkombinációt.__

4. Az SSH-munkamenetből használja a következő parancsot:

    ```bash
    hostname -f
    ```

    Ez a parancs a következő höz hasonló értéket ad vissza:

    ```output
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    A `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` szöveg a virtuális hálózat __DNS-utótagja.__ Mentse ezt az értéket, ahogy azt később használja.

5. Ha a Kötés beállítást a virtuális hálózaton belüli erőforrások DNS-neveinek `/etc/bind/named.conf.local` feloldására szeretné konfigurálni, használja a következő szöveget a fájl tartalmaként:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]  
    > A lekért `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` DNS-utótagot a korábban beolvasott DNS-utótagra kell cserélni.

    A fájl szerkesztéséhez használja a következő parancsot:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    A fájl mentéséhez használja a __Ctrl+X,__ __Y__, majd __az Enter billentyűkombinációt.__

6. A Bind elindításához használja a következő parancsot:

    ```bash
    sudo service bind9 restart
    ```

7. Annak ellenőrzéséhez, hogy a kötés képes-e feloldani a helyszíni hálózat erőforrásainak nevét, használja a következő parancsokat:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > Cserélje `dns.mynetwork.net` le a helyszíni hálózat egyik erőforrásának teljesen minősített tartománynevét (FQDN).
    >
    > Cserélje `10.0.0.4` le a virtuális hálózat egyéni __DNS-kiszolgálójának belső IP-címét.__

    A válasz a következő szöveghez hasonló:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>A virtuális hálózat konfigurálása az egyéni DNS-kiszolgáló használatára

Ha úgy szeretné konfigurálni a virtuális hálózatot, hogy az Azure rekurzív feloldója helyett az egyéni DNS-kiszolgálót használja, kövesse az [Azure Portal](https://portal.azure.com)következő lépéseit:

1. A bal oldali menüben keresse meg az **Összes szolgáltatás** > **hálózati** > virtuális**hálózatok**.

2. Válassza ki a virtuális hálózatot a listából, amely megnyitja a virtuális hálózat alapértelmezett nézetét.  

3. Az alapértelmezett nézet **Beállítások**csoportjában válassza a **DNS-kiszolgálók lehetőséget.**  

4. Válassza az __Egyéni__lehetőséget, és adja meg az egyéni DNS-kiszolgáló **PRIVÁT IP-címét.**

5. Kattintson a __Mentés__ gombra.  <br />  

    ![A hálózat egyéni DNS-kiszolgálójának beállítása](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>Helyszíni DNS-kiszolgáló konfigurálása

Az előző szakaszban úgy konfigurálta az egyéni DNS-kiszolgálót, hogy a kérelmeket továbbítsa a helyszíni DNS-kiszolgálónak. Ezután konfigurálnia kell a helyszíni DNS-kiszolgálót, hogy továbbítsa a kérelmeket az egyéni DNS-kiszolgálónak.

A DNS-kiszolgáló konfigurálásával kapcsolatos konkrét lépéseket a DNS-kiszolgáló szoftverének dokumentációjában találja. Keresse meg a feltételes továbbító konfigurálásának __lépéseit.__

A feltételes továbbítás csak egy adott DNS-utótagra vonatkozó kérelmeket továbbít. Ebben az esetben be kell állítania egy továbbítóa a virtuális hálózat DNS-utótag. Az utótagra vonatkozó kérelmeket az egyéni DNS-kiszolgáló IP-címére kell továbbítani. 

A következő szöveg a **Bind** DNS szoftver feltételes továbbítókonfigurációjának példája:

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

A DNS Windows **Server 2016**rendszeren való használatáról az [Add-DnsServerConditionalForwarderZone](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone) dokumentációjában olvashat...

Miután konfigurálta a helyszíni DNS-kiszolgálót, `nslookup` a helyszíni hálózatból ellenőrizheti, hogy fel tudja-e oldani a virtuális hálózatban lévő neveket. A következő példa 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

Ez a példa a 196.168.0.4-es helyszíni DNS-kiszolgálót használja az egyéni DNS-kiszolgáló nevének feloldásához. Cserélje le az IP-címet a helyszíni DNS-kiszolgálóra vonatkozóra. Cserélje `dnsproxy` le a címet az egyéni DNS-kiszolgáló teljesen minősített tartománynevére.

## <a name="optional-control-network-traffic"></a>Nem kötelező: A hálózati forgalom szabályozása

A hálózati biztonsági csoportok (NSG) vagy a felhasználó által definiált útvonalak (UDR) segítségével szabályozhatja a hálózati forgalmat. Az NSG-k lehetővé teszik a bejövő és kimenő forgalom szűrését, valamint a forgalom engedélyezését vagy megtagadását. Az UD-k lehetővé teszik a virtuális hálózat, az internet és a helyszíni hálózat erőforrásai közötti forgalom szabályozását.

> [!WARNING]  
> A HDInsight bejövő hozzáférést igényel az Azure-felhőben lévő adott IP-címekről, és korlátlan kimenő hozzáférést igényel. Ha NSG-ket vagy UDRs-t használ a forgalom szabályozására, a következő lépéseket kell végrehajtania:

1. Keresse meg a virtuális hálózatot tartalmazó hely IP-címeit. A szükséges IP-címek hely szerint történő listáját a [Kötelező IP-címek](./hdinsight-management-ip-addresses.md)című témakörben tetszés szerint kell megadni.

2. Az 1.

   * __NSG__használata esetén: __Bejövő__ forgalom engedélyezése a __443-as__ porton az IP-címekhez.
   * __UDR__használata esetén : Állítsa be az __IP-címek internethez__ vezető útvonal __következő ugrás__ típusát.

Az Azure PowerShell vagy az Azure CLI nsg-ek létrehozásához való használatának példáját lásd a [HDInsight kiterjesztése az Azure virtuális hálózatokkal](hdinsight-create-virtual-network.md#hdinsight-nsg) című dokumentumban.

## <a name="create-the-hdinsight-cluster"></a>A HDInsight-fürt létrehozása

> [!WARNING]  
> A HDInsight virtuális hálózatba való telepítése előtt konfigurálnia kell az egyéni DNS-kiszolgálót.

A [HDInsight-fürt létrehozása az Azure Portal-dokumentum használatával a HDInsight-fürt létrehozása](./hdinsight-hadoop-create-linux-clusters-portal.md) lépéseket használja.

> [!WARNING]  
> * A fürt létrehozása során ki kell választania a virtuális hálózatot tartalmazó helyet.
> * A konfiguráció __Speciális beállítások__ részében ki kell választania a korábban létrehozott virtuális hálózatot és alhálózatot.

## <a name="connecting-to-hdinsight"></a>Csatlakozás a HDInsighthoz

A HDInsight legtöbb dokumentációja feltételezi, hogy az interneten keresztül rendelkezik hozzáféréssel a fürthöz. Például hogy a `https://CLUSTERNAME.azurehdinsight.net` címen tud csatlakozni a fürthöz. Ez a cím a nyilvános átjárót használja, amely nem érhető el, ha NSG-ket vagy UDRs-t használt az internetről való hozzáférés korlátozására.

Egyes dokumentációk `headnodehost` is hivatkozik, amikor csatlakozik a fürthöz egy SSH-munkamenet. Ez a cím csak a fürt önkiszolgáló csomópontjairól érhető el, és nem használható a virtuális hálózaton keresztül csatlakoztatott ügyfeleken.

Ha közvetlenül szeretne csatlakozni a HDInsighthoz a virtuális hálózaton keresztül, kövesse az alábbi lépéseket:

1. A HDInsight fürtcsomópontok belső, teljesen minősített tartománynevének felderítéséhez használja az alábbi módszerek egyikét:

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

2. Annak megállapításához, hogy a port, amelyen egy szolgáltatás elérhető, tekintse meg az [Apache Hadoop-szolgáltatások által használt portok](./hdinsight-hadoop-port-settings-for-services.md) hdinsight-dokumentumban.

    > [!IMPORTANT]  
    > A fő csomópontokon tárolt egyes szolgáltatások egyszerre csak egy csomóponton aktívak. Ha megpróbál hozzáférni egy szolgáltatáshoz az egyik főcsomóponton, és nem sikerül, váltson át a másik főcsomópontra.
    >
    > Például az Apache Ambari egyszerre csak egy főcsomóponton aktív. Ha megpróbál hozzáférni az Ambari-hoz az egyik főcsomóponton, és 404-es hibát ad vissza, akkor a másik főcsomóponton fut.

## <a name="next-steps"></a>További lépések

* A HDInsight virtuális hálózatban való használatáról az [Azure HDInsight-fürtök virtuális hálózati üzembe helyezésének megtervezése](./hdinsight-plan-virtual-network-deployment.md)című témakörben talál további információt.

* Az Azure virtuális hálózatokkal kapcsolatos további információkért tekintse meg az [Azure virtuális hálózat áttekintését.](../virtual-network/virtual-networks-overview.md)

* A hálózati biztonsági csoportokról további információt a Hálózati biztonsági csoportok című [témakörben](../virtual-network/security-overview.md)talál.

* A felhasználó által definiált útvonalakról a [Felhasználó által definiált útvonalakról és az IP-továbbításról](../virtual-network/virtual-networks-udr-overview.md)talál további információt.
