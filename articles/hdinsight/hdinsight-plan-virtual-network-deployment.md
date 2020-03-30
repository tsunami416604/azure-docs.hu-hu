---
title: Virtuális hálózat tervezése az Azure HDInsight számára
description: Megtudhatja, hogyan tervezhet egy Azure virtuális hálózati központi telepítést a HDInsight más felhőalapú erőforrásokhoz vagy az adatközpontban lévő erőforrásokhoz való csatlakoztatásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/25/2020
ms.openlocfilehash: 30664d533215cb49fa6f436ec4cf88fa319c3300
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272265"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Virtuális hálózat tervezése az Azure HDInsight számára

Ez a cikk háttér-információkat tartalmaz az [Azure virtuális hálózatok](../virtual-network/virtual-networks-overview.md) (VNets) Azure HDInsight használatával kapcsolatos háttér-információkról. Azt is ismerteti a tervezési és megvalósítási döntéseket, amelyeket meg kell hozni, mielőtt a HDInsight-fürt virtuális hálózat megvalósítása. Miután a tervezési fázis befejeződött, folytathatja a [Virtuális hálózatok létrehozása az Azure HDInsight-fürtökhöz](hdinsight-create-virtual-network.md). A hálózati biztonsági csoportok (NSG-k) és a felhasználó által definiált útvonalak megfelelő konfigurálásához szükséges HDInsight-felügyeleti IP-címekről a [HDInsight-felügyeleti IP-címek](hdinsight-management-ip-addresses.md)című témakörben talál további információt.

Az Azure virtuális hálózat használata a következő forgatókönyveket teszi lehetővé:

* Csatlakozás a HDInsighthoz közvetlenül egy helyszíni hálózatról.
* A HDInsight csatlakoztatása az Azure virtuális hálózat adattáraihoz.
* Közvetlenül elérhető Apache Hadoop szolgáltatások, amelyek nem érhetők el nyilvánosan az interneten keresztül. Például az Apache Kafka API-k vagy az Apache HBase Java API.

> [!IMPORTANT]
> HdInsight-fürt ötvönynyi virtuális hálózatban való létrehozása számos hálózati erőforrást hoz létre, például hálózati adaptereket és terheléselosztókat. **Ne** törölje ezeket a hálózati erőforrásokat, mivel azok szükségesek ahhoz, hogy a fürt megfelelően működjön a virtuális hálózattal.
>
> 2019. február 28.után a virtuális hálózatban létrehozott ÚJ HDInsight-fürtök hálózati erőforrásai (például hálózati adapterek, LB-k stb.) ugyanabban a HDInsight-fürterőforrás-csoportban lesznek kiépítve. Korábban ezek az erőforrások a VNET erőforráscsoportban voltak kiépítve. Nincs változás az aktuálisan futó fürtökben és a virtuális hálózat nélkül létrehozott fürtökben.

## <a name="planning"></a>Tervezés

A HDInsight virtuális hálózatra történő telepítésének tervezésekor az alábbi kérdéseket kell megválaszolnia:

* Telepíti a HDInsightot egy meglévő virtuális hálózatba? Vagy új hálózatot hoz létre?

    Ha meglévő virtuális hálózatot használ, előfordulhat, hogy a HDInsight telepítése előtt módosítania kell a hálózati konfigurációt. További információt a [HDInsight hozzáadása egy meglévő virtuális hálózathoz](#existingvnet) című szakaszban talál.

* Szeretné csatlakoztatni a HDInsightot tartalmazó virtuális hálózatot egy másik virtuális hálózathoz vagy a helyszíni hálózathoz?

    Az erőforrások hálózatokon átnyúló egyszerű működéséhez szükség lehet egyéni DNS létrehozására és a DNS-továbbítás konfigurálására. További információt a [több hálózat csatlakoztatása](#multinet) című szakaszban talál.

* Szeretné korlátozni/átirányítani a bejövő vagy kimenő forgalmat a HDInsightba?

    A HDInsightnak korlátlan kommunikációval kell rendelkeznie az Azure-adatközpontban meghatározott IP-címekkel. Számos portot is engedélyezni kell tűzfalakon keresztül az ügyfélkommunikációhoz. További információt a [hálózati forgalom vezérlésével](#networktraffic) című részben talál.

## <a name="add-hdinsight-to-an-existing-virtual-network"></a><a id="existingvnet"></a>HDInsight hozzáadása meglévő virtuális hálózathoz

Ebben a szakaszban található lépéseket, hogy fedezze fel, hogyan adhat hozzá egy új HDInsight egy meglévő Azure virtuális hálózat.

> [!NOTE]  
> Meglévő HDInsight-fürt nem adható hozzá virtuális hálózathoz.

1. Klasszikus vagy Erőforrás-kezelő telepítési modellt használ a virtuális hálózathoz?

    A HDInsight 3.4-es és nagyobb esetén erőforrás-kezelői virtuális hálózatra van szükség. A HDInsight korábbi verziói klasszikus virtuális hálózatot igényelt.

    Ha a meglévő hálózat klasszikus virtuális hálózat, akkor létre kell hoznia egy Erőforrás-kezelő virtuális hálózatot, majd csatlakoztatnia kell a kettőt. [Klasszikus virtuális hálózatok csatlakoztatása az új virtuális hálózatokhoz.](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)

    Miután csatlakozott, az Erőforrás-kezelő hálózatba telepített HDInsight kölcsönhatásba léphet a klasszikus hálózat erőforrásaival.

2. Használja a hálózati biztonsági csoportokat, a felhasználó által definiált útvonalakat vagy a virtuális hálózati berendezéseket a virtuális hálózatba irányuló vagy azon kívüli forgalom korlátozására?

    Felügyelt szolgáltatásként a HDInsight korlátlan hozzáférést igényel az Azure-adatközponttöbb IP-címéhez. Az IP-címekkel való kommunikáció engedélyezéséhez frissítse a meglévő hálózati biztonsági csoportokat vagy a felhasználó által definiált útvonalakat.

    A HDInsight több szolgáltatást is üzemeltet, amelyek különböző portokat használnak. Ne blokkolja a forgalmat ezekre a kikötőkre. A virtuális berendezés tűzfalain keresztül engedélyezni lehessen portok listáját a Biztonság című szakaszban található.

    A meglévő biztonsági konfiguráció megkereséséhez használja a következő Azure PowerShell- vagy Azure CLI-parancsokat:

    * Network security groups (Hálózati biztonsági csoportok)

        Cserélje `RESOURCEGROUP` le a virtuális hálózatot tartalmazó erőforráscsoport nevére, majd írja be a parancsot:

        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```

        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        További információt a [Hálózati biztonsági csoportok hibaelhárítása](../virtual-network/diagnose-network-traffic-filter-problem.md) című dokumentumban talál.

        > [!IMPORTANT]  
        > A hálózati biztonsági csoport szabályai a szabály prioritása alapján sorrendben kerülnek alkalmazásra. A rendszer a forgalmi mintának megfelelő első szabályt alkalmazza, és az adott forgalomra nem alkalmaz nak másokat. A szabályok megrendelése a legmegengedőbbtől a legkevésbé megengedőig. További információt a [Hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/security-overview.md) című dokumentumban talál.

    * Felhasználó által megadott útvonalak

        Cserélje `RESOURCEGROUP` le a virtuális hálózatot tartalmazó erőforráscsoport nevére, majd írja be a parancsot:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        További információt az [Útvonalak hibaelhárítása](../virtual-network/diagnose-network-routing-problem.md) című dokumentumban talál.

3. Hozzon létre egy HDInsight-fürtöt, és válassza ki az Azure virtuális hálózatot a konfiguráció során. A fürt létrehozási folyamatának megismeréséhez kövesse az alábbi dokumentumok lépéseit:

    * [HDInsight létrehozása az Azure Portalon](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [HDInsight létrehozása az Azure PowerShell-lel](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [HDInsight létrehozása az Azure Classic CLI használatával](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [HDInsight létrehozása Azure Resource Manager-sablonnal](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > HdInsight hozzáadása a virtuális hálózathoz nem kötelező konfigurációs lépés. Ügyeljen arra, hogy a fürt konfigurálásakor válassza ki a virtuális hálózatot.

## <a name="connecting-multiple-networks"></a><a id="multinet"></a>Több hálózat csatlakoztatása

A többhálózati konfiguráció legnagyobb kihívása a hálózatok közötti névfeloldás.

Az Azure névfeloldást biztosít a virtuális hálózatban telepített Azure-szolgáltatásokszámára. Ez a beépített névfeloldás lehetővé teszi a HDInsight számára, hogy teljesen minősített tartománynév (FQDN) használatával csatlakozzon a következő erőforrásokhoz:

* Az interneten elérhető bármely erőforrás. Például microsoft.com, windowsupdate.com.

* Minden olyan erőforrás, amely ugyanabban az Azure virtuális hálózatban található, az erőforrás __belső DNS-nevének__ használatával. Az alapértelmezett névfeloldás használatakor például az alábbi példák a HDInsight munkavégző csomópontokhoz rendelt belső DNS-nevekre:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Mindkét csomópont közvetlenül kommunikálhat egymással, és a HDInsight más csomópontjaival is, belső DNS-nevek használatával.

Az alapértelmezett névfeloldás __nem__ teszi lehetővé a HDInsight számára a virtuális hálózathoz csatlakozó hálózatok erőforrásainak feloldását. Gyakori például a helyszíni hálózat a virtuális hálózathoz való csatlakozása. Csak az alapértelmezett névfeloldás, HDInsight nem tud hozzáférni a helyszíni hálózat erőforrásait név szerint. Ennek az ellenkezője is igaz, a helyszíni hálózat erőforrásai nem férhetnek hozzá a virtuális hálózat erőforrásaihoz név szerint.

> [!WARNING]  
> A HDInsight-fürt létrehozása előtt létre kell hoznia az egyéni DNS-kiszolgálót, és konfigurálnia kell a virtuális hálózatot annak használatára.

A virtuális hálózat és az összekapcsolt hálózatok erőforrásai közötti névfeloldás engedélyezéséhez a következő műveleteket kell végrehajtania:

1. Hozzon létre egy egyéni DNS-kiszolgálót az Azure virtuális hálózatban, ahol a HDInsight telepítését tervezi.

2. Konfigurálja úgy a virtuális hálózatot, hogy az egyéni DNS-kiszolgálót használja.

3. Keresse meg az Azure-hoz rendelt DNS-utótagot a virtuális hálózathoz. Ez az érték `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`hasonló a hoz. A DNS-utótag megkeresésével kapcsolatos további tudnivalókért tekintse meg a [Példa: Egyéni DNS](hdinsight-create-virtual-network.md#example-dns) szakaszt.

4. Konfigurálja a DNS-kiszolgálók közötti továbbítást. A konfiguráció a távoli hálózat típusától függ.

   * Ha a távoli hálózat helyszíni hálózat, konfigurálja a DNS-t az alábbiak szerint:

     * __Egyéni DNS__ (a virtuális hálózatban):

         * Továbbítsa a virtuális hálózat DNS-utótagjára vonatkozó kérelmeket az Azure rekurzív feloldójának (168.63.129.16). Az Azure kezeli a virtuális hálózat ban lévő erőforrásokra vonatkozó kérelmeket

         * Továbbítsa az összes többi kérelmet a helyszíni DNS-kiszolgálónak. A helyszíni DNS kezeli az összes többi névfeloldási kérelmek, még az internetes erőforrások, például a Microsoft.com.

     * __Helyszíni DNS__: A virtuális hálózati DNS-utótag ra vonatkozó kérelmek továbbítása az egyéni DNS-kiszolgálóra. Az egyéni DNS-kiszolgáló ezután továbbítja az Azure rekurzív feloldó.

       Ez a konfiguráció a virtuális hálózat DNS-utónevét tartalmazó, teljesen minősített tartománynevekre vonatkozó kérelmeket az egyéni DNS-kiszolgálóra irányítja. Minden más kérést (még nyilvános internetcímek esetén is) a helyszíni DNS-kiszolgáló kezel.

   * Ha a távoli hálózat egy másik Azure virtuális hálózat, konfigurálja a DNS-t az alábbiak szerint:

     * __Egyéni DNS__ (minden virtuális hálózatban):

         * A virtuális hálózatok DNS-utótagjára vonatkozó kérelmeket a rendszer továbbítja az egyéni DNS-kiszolgálóknak. Az egyes virtuális hálózatok DNS-e felelős a hálózaton belüli erőforrások feloldásáért.

         * Továbbítsa az összes többi kérelmet az Azure rekurzív feloldó. A rekurzív feloldó felelős a helyi és internetes erőforrások megoldásáért.

       Az egyes hálózatok DNS-kiszolgálója a DNS-utótag alapján továbbítja a kérelmeket a másiknak. Más kérelmek feloldása az Azure rekurzív feloldó használatával.

     Az egyes konfigurációkra vonatkozó példát a [Példa: Egyéni DNS](hdinsight-create-virtual-network.md#example-dns) szakasz ismerteti.

További információt a [virtuális gépek és a szerepkörpéldányok névfeloldása](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) című dokumentumban talál.

## <a name="directly-connect-to-apache-hadoop-services"></a>Közvetlen csatlakozás az Apache Hadoop szolgáltatásaihoz

A fürthöz a `https://CLUSTERNAME.azurehdinsight.net`segítségével csatlakozhat. Ez a cím nyilvános IP-címet használ, amely nem érhető el, ha nsg-ket használt az internetről érkező bejövő forgalom korlátozására. Továbbá, ha telepíti a fürtegy virtuális hálózatban elérheti azt `https://CLUSTERNAME-int.azurehdinsight.net`a privát végpont használatával. Ez a végpont a virtuális hálózaton belül egy privát IP-címre oldódik fel a fürthozzáféréshez.

Az Apache Ambari-hoz és más weboldalakhoz a virtuális hálózaton keresztül való csatlakozáshoz kövesse az alábbi lépéseket:

1. A HDInsight-fürtcsomópontok belső, teljesen minősített tartománynevének (FQDN) felderítéséhez használja az alábbi módszerek egyikét:

    Cserélje `RESOURCEGROUP` le a virtuális hálózatot tartalmazó erőforráscsoport nevére, majd írja be a parancsot:

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

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
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    A visszaadott csomópontok listájában keresse meg a főcsomópontok teljes tartománynát, és a teljes tartománynnokkal csatlakozzon az Ambarihoz és más webszolgáltatásokhoz. Például az `http://<headnode-fqdn>:8080` Ambari eléréséhez használható.

    > [!IMPORTANT]  
    > A fő csomópontokon tárolt egyes szolgáltatások egyszerre csak egy csomóponton aktívak. Ha megpróbál hozzáférni egy szolgáltatáshoz az egyik főcsomóponton, és az 404-es hibát ad vissza, váltson át a másik főcsomópontra.

2. A szolgáltatás elérhető csomópontjának és portjának meghatározásához tekintse meg a [Hadoop-szolgáltatások által a HDInsight-dokumentumon használt portokat.](./hdinsight-hadoop-port-settings-for-services.md)

## <a name="controlling-network-traffic"></a><a id="networktraffic"></a>A hálózati forgalom szabályozása

### <a name="techniques-for-controlling-inbound-and-outbound-traffic-to-hdinsight-clusters"></a>A HDInsight-fürtök be- és kimenő forgalmának szabályozására szolgáló technikák

Az Azure virtuális hálózatok hálózati forgalma a következő módszerekkel szabályozható:

* **A hálózati biztonsági csoportok** (NSG) lehetővé teszik a hálózatba irányuló bejövő és kimenő forgalom szűrését. További információt a [Hálózati forgalom szűrése hálózati biztonsági csoportokkal](../virtual-network/security-overview.md) című dokumentumban talál.

* **A hálózati virtuális készülékek** (NVA) csak kimenő forgalommal használhatók. Az nva-k replikálják az eszközök, például a tűzfalak és az útválasztók funkcióit. További információt a Hálózati eszközök című [dokumentumban talál.](https://azure.microsoft.com/solutions/network-appliances)

Felügyelt szolgáltatásként a HDInsight korlátlan hozzáférést igényel a HDInsight állapot- és felügyeleti szolgáltatásaihoz a virtuális hálózatról érkező bejövő és kimenő forgalomhoz egyaránt. NSG-k használatakor biztosítania kell, hogy ezek a szolgáltatások továbbra is kommunikálhassanak a HDInsight-fürttel.

![Az Azure egyéni virtuális hálózatában létrehozott HDInsight-entitások diagramja](./media/hdinsight-plan-virtual-network-deployment/hdinsight-vnet-diagram.png)

### <a name="hdinsight-with-network-security-groups"></a>HDInsight hálózati biztonsági csoportokkal

Ha **hálózati biztonsági csoportokat** kíván használni a hálózati forgalom szabályozására, a HDInsight telepítése előtt hajtsa végre a következő műveleteket:

1. Azonosítsa a HDInsighthoz használni kívánt Azure-régiót.

2. Azonosítsa a HDInsight által a régióban igényelt szolgáltatáscímkéket. További információ: [Network security group (NSG) service tags for Azure HDInsight.](hdinsight-service-tags.md)

3. Hozza létre vagy módosítsa annak az alhálózatnak a hálózati biztonsági csoportjait, amelyekbe a HDInsightot telepíteni kívánja.

    * __Hálózati biztonsági csoportok:__ a __443-as__ __porton bejövő__ forgalmat engedélyezhet az IP-címekről. Ez biztosítja, hogy a HDInsight felügyeleti szolgáltatások a virtuális hálózaton kívülről is elérhetik a fürtöt.

A hálózati biztonsági csoportokról a hálózati biztonsági csoportok áttekintésében olvashat [bővebben.](../virtual-network/security-overview.md)

### <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>HDInsight-fürtök kimenő forgalmának szabályozása

A HDInsight-fürtök kimenő forgalmának szabályozásáról a [Kimenő hálózati forgalom korlátozásának konfigurálása az Azure HDInsight-fürtökhöz](hdinsight-restrict-outbound-traffic.md)című témakörben talál további információt.

#### <a name="forced-tunneling-to-on-premises"></a>Kényszerített bújtatás a helyszíni

A kényszerített bújtatás egy felhasználó által definiált útválasztási konfiguráció, ahol az alhálózatról érkező összes forgalom egy adott hálózatra vagy helyre, például a helyszíni hálózatra kényszerül. A HDInsight __nem__ támogatja a helyszíni hálózatokba irányuló forgalom kényszerített bújtatását.

## <a name="required-ip-addresses"></a><a id="hdinsight-ip"></a>Kötelező IP-címek

Ha hálózati biztonsági csoportokat vagy felhasználó által definiált útvonalakat használ a forgalom szabályozására, olvassa el a [HDInsight-kezelés IP-címeit.](hdinsight-management-ip-addresses.md)

## <a name="required-ports"></a><a id="hdinsight-ports"></a>Kötelező portok

Ha azt tervezi, hogy **egy tűzfalat,** és a fürt kívülről való hozzáférés bizonyos portokon, előfordulhat, hogy engedélyeznie kell a forgalmat azokon a portokon szükséges a forgatókönyv. Alapértelmezés szerint nincs szükség a portok speciális engedélyezési listájára mindaddig, amíg az előző szakaszban ismertetett azure-felügyeleti forgalom elérheti a fürtöt a 443-as porton.

Az adott szolgáltatások portjainak listáját az [Apache Hadoop-szolgáltatások által a HDInsight-dokumentumon használt portok](hdinsight-hadoop-port-settings-for-services.md) című témakörben található.

A virtuális készülékek tűzfalszabályairól további információt a [virtuális berendezés forgatókönyv-dokumentumában](../virtual-network/virtual-network-scenario-udr-gw-nva.md) talál.

## <a name="load-balancing"></a>Terheléselosztás

HDInsight-fürt létrehozásakor egy terheléselosztó is létrejön. A terheléselosztó típusa az [alap termékváltozat szintjén](../load-balancer/concepts-limitations.md#skus)van, amely bizonyos korlátozásokkal rendelkezik. Az egyik ilyen megkötések, hogy ha két virtuális hálózatok különböző régiókban, nem tud csatlakozni az alapvető terheléselosztók. További információkért tekintse meg [a virtuális hálózatok gyakori kérdések: a globális virtuális hálózatok társviszony-létesítéskorlátozásai.](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)

## <a name="transport-layer-security"></a>Átviteli réteg biztonsága

A fürthöz a nyilvános fürtvégponton `https://<clustername>.azurehdinsight.net` keresztül létesített kapcsolatok a fürt átjárócsomópontjain keresztül vannak proxied. Ezek a kapcsolatok a TLS nevű protokoll segítségével vannak biztosítva. A TLS magasabb verzióinak az átjárókon történő érvényesítése növeli a kapcsolatok biztonságát. A TLS újabb verzióinak használatáról a [TLS 1.0 probléma megoldása című témakörben](https://docs.microsoft.com/security/solving-tls1-problem)talál további információt.

Alapértelmezés szerint az Azure HDInsight-fürtök elfogadják a TLS 1.2-es kapcsolatokat nyilvános HTTPS-végpontokon, valamint a régebbi verziókat a visszamenőleges kompatibilitás érdekében. Az Azure Portal vagy egy erőforrás-kezelő sablon használatával szabályozhatja az átjárócsomópontokon támogatott minimális TLS-verziót a fürt létrehozása során. A portálhoz válassza ki a TLS-verziót a **Biztonság + hálózat lapon** a fürt létrehozása során. Az erőforrás-kezelő sablon üzembe helyezéskor használja a **minSupportedTlsVersion** tulajdonságot. Mintasablonról a [HDInsight minimális TLS 1.2 rövid útmutató sablonja olvashat.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls) Ez a tulajdonság három értéket támogat: "1.0", "1.1" és "1.2", amelyek a TLS 1.0+, a TLS 1.1+ és a TLS 1.2+ értékeknek felelnek meg.

> [!IMPORTANT]
> 2020. június 30-tól kezdődően az Azure HDInsight minden HTTPS-kapcsolatra kikényszeríti a TLS 1.2-es vagy újabb verziókat. Azt javasoljuk, hogy győződjön meg arról, hogy minden ügyfele készen áll a TLS 1.2-es vagy újabb verziók kezelésére. További információ: [Azure HDInsight TLS 1.2 Enforcement](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/).

## <a name="next-steps"></a>További lépések

* A kódmintákat és az Azure virtuális hálózatok létrehozásának példáit az [Azure HDInsight-fürtökhöz való virtuális hálózatok létrehozása](hdinsight-create-virtual-network.md)című témakörben talál.
* A HDInsight helyszíni hálózathoz való csatlakozásra való konfigurálásának teljes körű példáját a [HDInsight csatlakoztatása helyszíni hálózathoz](./connect-on-premises-network.md)című témakörben tetszését.
* Az Apache HBase-fürtök azure-beli virtuális hálózatokban történő konfigurálása az [Apache HBase-fürtök létrehozása a HDInsight szolgáltatásban az Azure Virtual Network ben című témakörben található.](hbase/apache-hbase-provision-vnet.md)
* Az Apache HBase georeplikáció konfigurálása az [Apache HBase fürtreplikáció beállítása az Azure virtuális hálózatokban](hbase/apache-hbase-replication.md)című témakörben található.
* Az Azure virtuális hálózatokkal kapcsolatos további információkért tekintse meg az [Azure virtuális hálózat áttekintését.](../virtual-network/virtual-networks-overview.md)
* A hálózati biztonsági csoportokról további információt a Hálózati biztonsági csoportok című [témakörben](../virtual-network/security-overview.md)talál.
* A felhasználó által definiált útvonalakról a [Felhasználó által definiált útvonalakról és az IP-továbbításról](../virtual-network/virtual-networks-udr-overview.md)talál további információt.
