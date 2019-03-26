---
title: A Virtual Network – Azure HDInsight kiterjesztése
description: Ismerje meg, hogyan csatlakozhat Azure Virtual Network HDInsight egyéb felhőalapú erőforrások, vagy az adatközpontban található erőforrások
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: b9462866f0be62d288e121b71119d0f9885a0d39
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439413"
---
# <a name="extend-azure-hdinsight-using-an-azure-virtual-network"></a>Az Azure Virtual Network használata Azure HDInsight kiterjesztése

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

> [!IMPORTANT]  
> 2019. február 28., miután a hálózati erőforrások (például a hálózati adapterek, LBs stb.) egy virtuális hálózaton létrehozott új fürtök esetében a HDInsight fürt azonos erőforráscsoportban jön létre. Korábban ezeket az erőforrásokat kiépített virtuális hálózat az erőforráscsoportban. Nem történik változás az aktuális futó fürtök és ezeket a virtuális hálózat nélkül létrehozott fürtök.

Ismerje meg, hogyan használható a HDInsight- [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Az Azure Virtual Network használatával lehetővé teszi, hogy a következő esetekben:

* Csatlakozás a HDInsight közvetlenül a helyi hálózatról.

* HDInsight-adatokhoz való csatlakozásról tárolja egy Azure-beli virtuális hálózathoz.

* Közvetlen hozzáférés [Apache Hadoop](https://hadoop.apache.org/) szolgáltatásokat, amelyek nem érhető el nyilvánosan az interneten keresztül. Ha például [Apache Kafka](https://kafka.apache.org/) API-k vagy a [Apache HBase](https://hbase.apache.org/) Java API-t.

> [!WARNING]  
> A jelen dokumentumban lévő információk a TCP/IP-hálózati ismeretét igényli. Ha nem ismeri a TCP/IP-hálózat, meg kell partneri valakivel, aki a módosításokat éles hálózati környezetben a végrehajtása előtt.

> [!IMPORTANT]  
> Ha a csatlakozás részletes útmutatást HDInsight, a helyszíni hálózat az Azure Virtual Network használatával, lásd: a [HDInsight csatlakoztatása a helyszíni hálózathoz](connect-on-premises-network.md) dokumentumot.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="planning"></a>Tervezés

A kérdéseket, amelyeket a HDInsight telepítése egy virtuális hálózat megtervezése során választ kell adnia a következők:

* Telepítse a HDInsight egy meglévő virtuális hálózatban kell? Vagy új hálózat létrehozásakor?

    Ha egy meglévő virtuális hálózatot használ, szükség lehet a hálózati konfiguráció módosításához a HDInsight telepítése előtt. További információkért lásd: a [HDInsight hozzáadása egy meglévő virtuális hálózatot](#existingvnet) szakaszban.

* Szeretné a virtuális hálózat, amely tartalmazza a HDInsight egy másik virtuális hálózathoz vagy a helyszíni hálózat csatlakoztatása?

    Segítségével egyszerűen dolgozhat erőforrások hálózatok között, szükség lehet egy egyéni DNS létrehozása és konfigurálása a DNS-továbbítást. További információkért lásd: a [networkök összekapcsolása](#multinet) szakaszban.

* Biztosan korlátozza/átirányítási HDInsight bejövő vagy kimenő forgalmat?

    HDInsight üzemmódban kommunikáció az adott IP-címek az Azure-adatközpontban. Is találhatók, amelyek az ügyfél-kommunikációhoz tűzfalon keresztül kell engedélyezett számos portot. További információkért lásd: a [hálózati forgalom szabályozása](#networktraffic) szakaszban.

## <a id="existingvnet"></a>HDInsight hozzáadása egy meglévő virtuális hálózatot

Kövesse a lépéseket ebben a szakaszban egy új HDInsight hozzáadása egy meglévő Azure virtuális hálózat felderítése.

> [!NOTE]  
> Nem adhat hozzá egy meglévő HDInsight-fürtöt egy virtuális hálózatban.

1. Használja a klasszikus vagy Resource Manager üzemi modell esetében a virtuális hálózat?

    HDInsight 3.4-es és újabb egy Resource Manager virtuális hálózatra van szükség. HDInsight korábbi verzióiban egy klasszikus virtuális hálózat szükséges.

    Ha a meglévő hálózat egy klasszikus virtuális hálózat, ezután kell hozzon létre egy Resource Manager virtuális hálózatot, és hogyan csatlakozhat a két. [Klasszikus virtuális hálózatok csatlakoztatása új virtuális hálózatokhoz](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Miután csatlakozott, a Resource Manager-hálózaton telepített HDInsight kezelheti a klasszikus hálózati erőforrásokhoz.

2. Használják-e a kényszerített bújtatást? Kényszerített bújtatás az alhálózat-beállítással, amely kényszeríti a kimenő internetes forgalmat egy eszközön, az ellenőrzés és naplózás. HDInsight nem támogatja a kényszerített bújtatás. Távolítsa el a kényszerített bújtatás HDInsight egy meglévő alhálózat egy környezetbe való üzembe helyezés előtt, vagy hozzon létre egy új alhálózatot a HDInsight nem kényszerített bújtatással.

3. Használják a hálózati biztonsági csoportok, a felhasználó által megadott útvonalakat vagy a virtuális hálózati berendezések irányuló forgalom korlátozásához, vagy onnan máshová a virtuális hálózaton?

    Felügyelt szolgáltatásként a HDInsight több IP-címek az Azure-adatközpontban korlátlan hozzáférést igényel. Engedélyezi a kommunikációt az ezen IP-címek, bármely meglévő hálózati biztonsági csoportok vagy a felhasználó által megadott útvonalak frissítése.

    HDInsight különböző portok használata több szolgáltatások üzemelteti. Ne blokkolják ezeket a portokat a forgalmat. Engedélyezett a virtuális készülék tűzfalak, portok listáját tekintse meg a biztonsági szakaszt.

    A meglévő biztonsági konfiguráció, használja a következő Azure PowerShell vagy a klasszikus Azure CLI-parancsokat:

    * Network security groups (Hálózati biztonsági csoportok)

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-Aznetworksecuritygroup -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network nsg list --resource-group $RESOURCEGROUP
        ```

        További információkért lásd: a [hálózati biztonsági csoportok hibaelhárítása](../virtual-network/diagnose-network-traffic-filter-problem.md) dokumentumot.

        > [!IMPORTANT]  
        > Hálózati biztonsági csoport szabályait alkalmazza a rendszer a szabály prioritása alapján. Az első szabály, amely megfelel a forgalmi minták alkalmazzák, és nincs más lépnek érvénybe, hogy a forgalom. A legalacsonyabb a leginkább megengedő szabályok sorrendben. További információkért lásd: a [hálózati biztonsági csoportokkal a hálózati forgalom szűrése](../virtual-network/security-overview.md) dokumentumot.

    * Felhasználó által megadott útvonalak

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-Azroutetable -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network route-table list --resource-group $RESOURCEGROUP
        ```

        További információkért lásd: a [útvonalakkal kapcsolatos hibaelhárítás](../virtual-network/diagnose-network-routing-problem.md) dokumentumot.

4. Hozzon létre egy HDInsight-fürtöt, és válassza ki az Azure Virtual Network konfigurálása során. A fürt létrehozását megértéséhez használja az alábbi dokumentumokban lépéseket:

    * [HDInsight létrehozása az Azure Portalon](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [HDInsight létrehozása az Azure PowerShell-lel](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Klasszikus Azure CLI-vel HDInsight létrehozása](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [A HDInsight használata az Azure Resource Manager-sablon létrehozása](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > Egy választható konfigurációs lépés HDInsight ad hozzá egy virtuális hálózaton. Mindenképpen jelölje ki a virtuális hálózat, a fürt konfigurálása során.

## <a id="multinet"></a>Több hálózat csatlakoztatása

A legnagyobb kihívás több hálózati konfigurációval rendelkező névfeloldás a hálózatok között.

Az Azure névfeloldás kínál Azure-szolgáltatások telepítve vannak a virtuális hálózat. A beépített névfeloldás lehetővé teszi, hogy a HDInsight egy teljesen minősített tartománynevét (FQDN) használatával a következő erőforrások eléréséhez:

* Bármilyen erőforrás, amely az interneten érhető el. Például a Microsoft.com webhelyre mutat, windowsupdate.com.

* Bármilyen erőforrás, amely az azonos Azure Virtual Network használatával a __belső DNS-név__ az erőforrás. Például az alapértelmezett névfeloldás használata esetén a következők példa HDInsight munkavégző csomópontokhoz rendelt belső DNS-nevek:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Mindkét ezek a csomópontok közvetlenül kommunikálhatnak egymással, és a HDInsight, a többi csomópont belső DNS-nevek használatával.

Az alapértelmezett névhozzárendelés does __nem__ lehetővé teszik a HDInsight-erőforrások a hálózatok, a virtuális hálózathoz csatlakozó a nevek feloldásához. Ha például szokás a helyszíni hálózat csatlakoztatása a virtuális hálózat. Csak az alapértelmezett a névfeloldás HDInsight nem fér hozzá a helyszíni hálózaton található erőforrások neve szerint. Ennek az ellenkezője is igaz, a helyszíni hálózati erőforrások nem lehet hozzáférni az erőforrásokhoz a virtuális hálózat neve.

> [!WARNING]  
> Hozzon létre egyéni DNS-kiszolgáló és a virtuális hálózat a használatára a HDInsight-fürt létrehozása előtt konfigurálnia kell.

A virtuális hálózat és a csatlakoztatott hálózatokon lévő erőforrások közötti névfeloldás engedélyezéséhez hajtsa végre a következő műveleteket:

1. Hozzon létre egy egyéni DNS-kiszolgálót az Azure Virtual Network, ha azt tervezi, hogy a HDInsight telepítése.

2. A virtuális hálózat használatához az egyéni DNS-kiszolgáló konfigurálása.

3. Keresse meg az Azure a virtuális hálózat DNS-utótag rendelve. Ez az érték hasonlít a `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. A keresés, a DNS-utótag információkért lásd: a [példa: Egyéni DNS](#example-dns) szakaszban.

4. Configure forwarding between the DNS servers. A konfiguráció attól függ, hogy a távoli hálózat típusát.

   * Ha a távoli hálózat egy helyszíni hálózattal, konfigurálja a DNS módon:
        
     * __Egyéni DNS__ (az a virtuális hálózat esetén):

         * Az Azure rekurzív feloldó (168.63.129.16) a virtuális hálózat DNS-utótagját kérelmeket továbbítsa. Azure virtuális hálózatban lévő erőforrásokra vonatkozó kéréseket kezeli

         * Továbbíthatja a helyi DNS-kiszolgáló minden más kérelemhez. A helyi DNS-ben kezeli az összes többi névfeloldási, még akkor is, kérelmek, az internetes erőforrásokhoz, például a Microsoft.com webhelyre mutat.

     * __A helyszíni DNS__: A kérelmeket továbbítsa a virtuális hálózat DNS-utótag egyéni DNS-kiszolgálóra. Az egyéni DNS-kiszolgáló ezután továbbítja az Azure rekurzív feloldó.

       A konfiguráció útvonalak kérelmek teljesen minősített tartományneveket tartalmazó egyéni DNS-kiszolgálóra a virtuális hálózat DNS-utótagját. Minden más kérelemhez (akár a nyilvános internet-címekhez) a helyi DNS-kiszolgáló kezeli.

   * Ha a távoli hálózat egy másik Azure Virtual Network, konfigurálja a DNS módon:

     * __Egyéni DNS__ (az egyes virtuális hálózatok):

         * A virtuális hálózat DNS-utótagját a kérelmeket a rendszer az egyéni DNS-kiszolgálókra továbbítja. A DNS-ben az egyes virtuális hálózatok feladata a hálózaton belüli erőforrások feloldása.

         * Minden más kérelemhez továbbítja az Azure rekurzív feloldó. A rekurzív feloldó helyi megoldása és az internetes erőforrások felelős.

       A DNS-kiszolgáló esetében minden egyes hálózati továbbítja a kérelmeket a másik alapján DNS-utótagot. Az Azure rekurzív feloldó használata más kérelmek elhárulnak.

     Az egyes konfigurációkhoz egy példa: a [példa: Egyéni DNS](#example-dns) szakaszban.

További információkért lásd: a [virtuális gépek és Szerepkörpéldányok névfeloldása](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) dokumentumot.

## <a name="directly-connect-to-apache-hadoop-services"></a>Közvetlenül kapcsolódhat az Apache Hadoop-szolgáltatásokhoz

Csatlakozhat a fürthöz https://CLUSTERNAME.azurehdinsight.net. A cím egy nyilvános IP-címet használja, ami nem feltétlenül érhető el, ha az NSG-k segítségével az internetről bejövő forgalom korlátozása. Emellett a fürt egy virtuális hálózaton üzembe hozzá tud férni a privát végpont használatával https://CLUSTERNAME-int.azurehdinsight.net. Ez a végpont a fürt hozzáférés a virtuális hálózaton belül egy privát IP-cím mutat.

Az Apache Ambari és a más weblapok, a virtuális hálózaton keresztül csatlakozni, használja az alábbi lépéseket:

1. A HDInsight-fürtcsomópontok keresheti meg a belső teljesen minősített tartománynevet (FQDN), használja a következő módszerek egyikét:

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

    A csomópontok visszaadott listában keresse meg a teljes tartománynév a fő csomópontok, és a teljes tartománynevek használatával csatlakozik az Ambari és más webes szolgáltatásokat. Például `http://<headnode-fqdn>:8080` Ambari eléréséhez.

    > [!IMPORTANT]  
    > Néhány az átjárócsomópontokkal üzemeltetett szolgáltatásokra: csak az egyik csomóponton aktív egyszerre. Próbálja meg a szolgáltatás az egyik fő csomópont és a 404-es hibát ad vissza, ha átvált a fő csomópontot.

2. Annak megállapításához, a csomópont és a portot, amelyet egy szolgáltatás érhető el, tekintse meg a [HDInsight Hadoop-szolgáltatások által használt portok](./hdinsight-hadoop-port-settings-for-services.md) dokumentumot.

## <a id="networktraffic"></a> Hálózati forgalom szabályozása

Egy Azure-beli virtuális hálózatok hálózati forgalmához szabályozhatja a következő módszerekkel:

* **Hálózati biztonsági csoportok** (NSG) lehetővé teszi a hálózati bejövő és kimenő forgalom szűrését. További információkért lásd: a [hálózati biztonsági csoportokkal a hálózati forgalom szűrése](../virtual-network/security-overview.md) dokumentumot.

    > [!WARNING]  
    > HDInsight nem támogatja a kimenő forgalom korlátozása. Az összes kimenő forgalmat engedélyezni kell.

* **Felhasználó által megadott útvonalak** (UDR) definiálhatja a forgalom adatfolyamait a hálózaton lévő erőforrások között. További információkért lásd: a [felhasználó által megadott útvonalak és IP-továbbítás](../virtual-network/virtual-networks-udr-overview.md) dokumentumot.

* **Hálózati virtuális berendezések** replikálni, például a tűzfalak és az útválasztók eszközök működésével. További információkért lásd: a [hálózati berendezések](https://azure.microsoft.com/solutions/network-appliances) dokumentumot.

Felügyelt szolgáltatásként HDInsight a HDinsight állapota nem korlátozott hozzáférésre van szüksége, és felügyeleti szolgáltatásokat, mind a bejövő és kimenő forgalmat a virtuális hálózatról. Az NSG-k és az udr-EK használata esetén győződjön meg róla, hogy ezek a szolgáltatások továbbra is kommunikál HDInsight-fürt.

### <a id="hdinsight-ip"></a> A hálózati biztonsági csoportok és a felhasználó által megadott útvonalak HDInsight

Ha azt tervezi, hogy használatával **hálózati biztonsági csoportok** vagy **felhasználó által megadott útvonalak** szabályozhatja a hálózati forgalom, a HDInsight telepítése előtt a következő műveletek végrehajtásához:

1. Azonosítsa az Azure-régióban, amely a HDInsight használatát tervezi.

2. Azonosíthatja a HDInsight által igényelt IP-címeket. További információkért lásd: a [HDInsight által igényelt IP-címek](#hdinsight-ip) szakaszban.

3. Hozzon létre vagy módosítsa a hálózati biztonsági csoportok vagy a felhasználó által megadott útvonalakat az alhálózatot, amelyet a HDInsight azokat telepíteni szeretné a.

    * __Hálózati biztonsági csoportok__: engedélyezése __bejövő__ port forgalmát __443-as__ IP-címek. Ez biztosítja, hogy HDI-felügyeleti szolgáltatásokat a fürt elérje a külső virtuális hálózaton.
    * __Felhasználó által megadott útvonalak__: Ha azt tervezi, használja az udr-EK, hozzon létre egy útvonalat, minden IP-címhez, és állítsa be a __következő ugrás típusa__ való __Internet__. Minden kimenő forgalmat is engedélyezni a virtuális hálózatról korlátozás nélkül. Például az összes többi forgalom átirányítása az Azure tűzfal vagy a hálózati virtuális berendezés (az Azure-ban üzemeltetett) figyelési célból, de nem lesznek letiltva a kimenő forgalmat.

További információ a hálózati biztonsági csoportok vagy felhasználó által megadott útvonalakat a következő dokumentációban tekintheti meg:

* [Hálózati biztonsági csoport](../virtual-network/security-overview.md)

* [Felhasználó által megadott útvonalak](../virtual-network/virtual-networks-udr-overview.md)

#### <a name="forced-tunneling-to-on-premise"></a>Kényszerített bújtatás helyszíni

Kényszerített bújtatás egy felhasználó által meghatározott útválasztási konfigurációja ahol alhálózatból származó összes forgalom kényszerített egy adott hálózaton vagy a helyen, például a helyszíni hálózathoz. HDInsight does __nem__ támogatási kényszerített bújtatás a helyszíni hálózatokhoz. Ha Azure tűzfal vagy egy Azure-ban üzemeltetett hálózati virtuális készüléket használ, udr-EK segítségével rá, ellenőrzési célból átirányítja a forgalmat, és lehetővé teszi minden kimenő forgalmat.

## <a id="hdinsight-ip"></a> Szükséges IP-címek

> [!IMPORTANT]  
> Az Azure-állapot és a felügyeleti szolgáltatások kommunikálni a HDInsight képesnek kell lennie. A hálózati biztonsági csoportok vagy a felhasználó által megadott útvonalakat, ha ezeknek a szolgáltatásoknak a HDInsight eléréséhez az IP-címekről érkező forgalom.
>
> Ha nem használja a hálózati biztonsági csoportok vagy felhasználó által megadott útvonalak forgalom szabályozása, figyelmen kívül hagyhatja ebben a szakaszban.

Ha hálózati biztonsági csoportokat használ, engedélyeznie kell az Azure állapota és a felügyeleti szolgáltatások elérni a HDInsight-fürtök 443-as porton érkező forgalmat. Az alhálózaton belüli virtuális gépek közötti forgalmat is engedélyeznie kell. Az alábbi lépések segítségével engedélyezni kell az IP-címek keresése:

1. Mindig engedélyeznie kell a következő IP-címekről érkező forgalmat:

    | Forrás IP-címe | Célport | Irány |
    | ---- | ----- | ----- |
    | 168.61.49.99 | 443 | Bejövő |
    | 23.99.5.239 | 443 | Bejövő |
    | 168.61.48.131 | 443 | Bejövő |
    | 138.91.141.162 | 443 | Bejövő |

2. Ha a HDInsight-fürt a következő régiók valamelyikében van, majd engedélyeznie kell a régió felsorolt IP-címekről érkező forgalmat:

    > [!IMPORTANT]  
    > Ha nem szerepel az Azure-régiót használ, csak használja az 1. lépésben négy IP-címek.

    | Ország | Régió | Engedélyezett forrás IP-címei | Engedélyezett a céloldali port | Irány |
    | ---- | ---- | ---- | ---- | ----- |
    | Ázsia | Kelet-Ázsia | 23.102.235.122</br>52.175.38.134 | 443 | Bejövő |
    | &nbsp; | Délkelet-Ázsia | 13.76.245.160</br>13.76.136.249 | 443 | Bejövő |
    | Ausztrália | Kelet-Ausztrália | 104.210.84.115</br>13.75.152.195 | 443 | Bejövő |
    | &nbsp; | Délkelet-Ausztrália | 13.77.2.56</br>13.77.2.94 | 443 | Bejövő |
    | Brazília | Dél-Brazília | 191.235.84.104</br>191.235.87.113 | 443 | Bejövő |
    | Kanada | Kelet-Kanada | 52.229.127.96</br>52.229.123.172 | 443 | Bejövő |
    | &nbsp; | Közép-Kanada | 52.228.37.66</br>52.228.45.222 | 443 | Bejövő |
    | Kína | Észak-Kína | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | 443 | Bejövő |
    | &nbsp; | Kelet-Kína | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | 443 | Bejövő |
    | &nbsp; | Észak-Kína 2 | 40.73.37.141</br>40.73.38.172 | 443 | Bejövő |
    | &nbsp; | Kelet-Kína 2 | 139.217.227.106</br>139.217.228.187 | 443 | Bejövő |
    | Európa | Észak-Európa | 52.164.210.96</br>13.74.153.132 | 443 | Bejövő |
    | &nbsp; | Nyugat-Európa| 52.166.243.90</br>52.174.36.244 | 443 | Bejövő |
    | Franciaország | Közép-Franciaország| 20.188.39.64</br>40.89.157.135 | 443 | Bejövő |
    | Németország | Közép-Németország | 51.4.146.68</br>51.4.146.80 | 443 | Bejövő |
    | &nbsp; | Északkelet-Németország | 51.5.150.132</br>51.5.144.101 | 443 | Bejövő |
    | India | Közép-India | 52.172.153.209</br>52.172.152.49 | 443 | Bejövő |
    | &nbsp; | Dél-India | 104.211.223.67<br/>104.211.216.210 | 443 | Bejövő |
    | Japán | Kelet-Japán | 13.78.125.90</br>13.78.89.60 | 443 | Bejövő |
    | &nbsp; | Nyugat-Japán | 40.74.125.69</br>138.91.29.150 | 443 | Bejövő |
    | Korea | Korea középső régiója | 52.231.39.142</br>52.231.36.209 | 433 | Bejövő |
    | &nbsp; | Korea déli régiója | 52.231.203.16</br>52.231.205.214 | 443 | Bejövő
    | Egyesült Királyság | Az Egyesült Királyság nyugati régiója | 51.141.13.110</br>51.141.7.20 | 443 | Bejövő |
    | &nbsp; | Az Egyesült Királyság déli régiója | 51.140.47.39</br>51.140.52.16 | 443 | Bejövő |
    | Egyesült Államok | USA középső régiója | 13.67.223.215</br>40.86.83.253 | 443 | Bejövő |
    | &nbsp; | USA keleti régiója | 13.82.225.233</br>40.71.175.99 | 443 | Bejövő |
    | &nbsp; | USA északi középső régiója | 157.56.8.38</br>157.55.213.99 | 443 | Bejövő |
    | &nbsp; | USA nyugati középső régiója | 52.161.23.15</br>52.161.10.167 | 443 | Bejövő |
    | &nbsp; | USA nyugati régiója | 13.64.254.98</br>23.101.196.19 | 443 | Bejövő |
    | &nbsp; | USA nyugati régiója, 2. | 52.175.211.210</br>52.175.222.222 | 443 | Bejövő |

    Az IP-címek az Azure Government használatára vonatkozó információért lásd: a [Azure Government intelligencia és elemzés](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics) dokumentumot.

3. Is engedélyeznie kell a hozzáférést a __168.63.129.16__. Ez a cím az Azure rekurzív feloldó. További információkért lásd: a [névfeloldás virtuális gépek és a szerepkör példányai](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) dokumentumot.

További információkért lásd: a [hálózati forgalom szabályozása](#networktraffic) szakaszban.

Ha a felhasználó által definiált routes(UDRs) használ, adjon meg egy útvonalat, és engedélyezi a kimenő forgalmat a virtuális hálózatról a fenti IP-címek a következő ugrás "Internet" értékre.
    
## <a id="hdinsight-ports"></a> Szükséges portok

Ha a kíván használni egy **tűzfal** és hozzáférjen a fürthöz kívül bizonyos portokon kell, hogy előfordulhat, hogy engedélyezi a forgalmat az ezeket a portokat a forgatókönyvhöz szükséges. Alapértelmezés szerint a portok nincsenek különleges engedélyezési mindaddig, amíg az előző szakaszban ismertetett azure felügyeleti adatforgalmat engedélyezett 443-as portot a fürt eléréséhez szükséges.

Adott szolgáltatások portok listáját lásd: a [HDInsight az Apache Hadoop-szolgáltatások által használt portok](hdinsight-hadoop-port-settings-for-services.md) dokumentumot.

A virtuális készülékek vonatkozó tűzfalszabályok további információkért lásd: a [virtuális berendezésre telepítik](../virtual-network/virtual-network-scenario-udr-gw-nva.md) dokumentumot.

## <a id="hdinsight-nsg"></a>Példa: a hálózati biztonsági csoportok a HDInsight

Ebben a szakaszban szereplő példák bemutatják, hogyan hozhat létre hálózati biztonsági csoportszabályok, amelyek lehetővé teszik a HDInsight az Azure felügyeleti szolgáltatásokkal kommunikálni. Használata előtt a példákat, módosítsa az IP-címek egyeznek azokkal az Azure-régiót használ. Ezt az információt talál a [a hálózati biztonsági csoportok és a felhasználó által megadott útvonalak HDInsight](#hdinsight-ip) szakaszban.

### <a name="azure-resource-management-template"></a>Az Azure Resource Management-sablonnal

Az alábbi Resource Management-sablont hoz létre egy virtuális hálózatot, amely korlátozza a bejövő forgalmat, de lehetővé teszi, hogy a HDInsight által igényelt IP-címekről érkező forgalom. Ez a sablon is létrehoz egy HDInsight-fürt a virtuális hálózatban.

* [Egy biztonságos Azure virtuális hálózaton és a egy HDInsight Hadoop-fürt üzembe helyezése](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

> [!IMPORTANT]  
> Módosítsa megfelelően az Azure-régió használata ebben a példában használt IP-címeket. Ezt az információt talál a [a hálózati biztonsági csoportok és a felhasználó által megadott útvonalak HDInsight](#hdinsight-ip) szakaszban.

### <a name="azure-powershell"></a>Azure PowerShell

A következő PowerShell-parancsfájl használatával hozzon létre egy virtuális hálózatot, amely korlátozza a bejövő forgalmat, és lehetővé teszi az észak-európai régióban IP-címekről érkező forgalom.

> [!IMPORTANT]  
> Módosítsa megfelelően az Azure-régió használata ebben a példában használt IP-címeket. Ezt az információt talál a [a hálózati biztonsági csoportok és a felhasználó által megadott útvonalak HDInsight](#hdinsight-ip) szakaszban.

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"
# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName
# Get the region the Virtual network is in.
$location = $vnet.Location
# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `
# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg
# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

> [!IMPORTANT]  
> Ez a példa bemutatja, hogyan szabályok engedélyezik a bejövő forgalmat a szükséges IP-címek hozzáadása. Nem tartalmaz egy szabályt, amely korlátozza a más forrásból származó bejövő hozzáférést.
>
> A következő példa bemutatja, hogyan engedélyezheti az internetről érkező SSH-hozzáférés:
>
> ```powershell
> Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
> ```

### <a name="azure-classic-cli"></a>Az Azure klasszikus parancssori felület

Az alábbi lépések segítségével hozzon létre egy virtuális hálózatot, amely korlátozza a bejövő forgalmat, de lehetővé teszi, hogy a HDInsight által igényelt IP-címekről érkező forgalom.

1. A következő paranccsal hozzon létre egy új hálózati biztonsági csoport nevű `hdisecure`. Cserélje le **RESOURCEGROUPNAME** az erőforráscsoport, amely tartalmazza az Azure Virtual Network. Cserélje le **hely** a hely (régió), amely a csoport sikeresen létrehozva.

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    A csoport létrehozása után az új csoport tájékoztatást kap.

2. Használja a következő használatával adhat szabályokat az új hálózati biztonsági csoportot, amely engedélyezi a bejövő kommunikációt az az Azure HDInsight állapotát és a felügyeleti szolgáltatás a 443-as porton. Cserélje le **RESOURCEGROUPNAME** az erőforráscsoport, amely tartalmazza az Azure virtuális hálózat nevére.

    > [!IMPORTANT]  
    > Módosítsa megfelelően az Azure-régió használata ebben a példában használt IP-címeket. Ezt az információt talál a [a hálózati biztonsági csoportok és a felhasználó által megadott útvonalak HDInsight](#hdinsight-ip) szakaszban.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Lekérdezheti a hálózati biztonsági csoport egyedi azonosítója, használja a következő parancsot:

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    Ez a parancs egy értéket ad vissza az alábbi szöveghez hasonló:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    A parancsban azonosító körüli dupla idézőjel használja, ha nem a várt eredményt kap.

4. Az alábbi parancs segítségével alkalmazhatja a hálózati biztonsági csoport egy alhálózatot. Cserélje le a __GUID__ és __RESOURCEGROUPNAME__ az előző lépésben által visszaadott értékeket olyanokra cserélni. Cserélje le __VNETNAME__ és __SUBNETNAME__ virtuális hálózat nevére, illetve, hogy a létrehozni kívánt alhálózat neve.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Ez a parancs befejeződése után telepítheti a HDInsight a virtuális hálózatban.

> [!IMPORTANT]  
> Ezeket a lépéseket csak nyissa meg az Azure-felhőben HDInsight állapotát és a felügyeleti szolgáltatáshoz való hozzáférést. A HDInsight fürt a virtuális hálózaton kívül bármely más hozzáférés le lesz tiltva. Ahhoz, hogy a virtuális hálózaton kívülről való eléréshez, hozzá kell adnia a további hálózati biztonsági csoportokra vonatkozó szabályokat.
>
> A következő példa bemutatja, hogyan engedélyezheti az internetről érkező SSH-hozzáférés:
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
> ```

## <a id="example-dns"></a> Példa: DNS-konfiguráció

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>A névfeloldás virtuális hálózat és a egy csatlakoztatott helyi hálózat között

Ebben a példában feltételezésekre a következő:

* Rendelkezik egy Azure virtuális hálózat, amely kapcsolódik a helyszíni hálózathoz egy VPN-átjáró használatával.

* Az egyéni DNS-kiszolgáló a virtuális hálózatban fut a Linux vagy Unix operációs rendszerként.

* [Kötési](https://www.isc.org/downloads/bind/) az egyéni DNS-kiszolgáló telepítve van.

Az egyéni DNS-kiszolgálón a virtuális hálózat:

1. Azure PowerShell vagy a klasszikus Azure CLI használatával keresse meg a virtuális hálózat DNS-utótag:

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Az egyéni DNS-kiszolgálón a virtuális hálózat, használja a következő szöveget a tartalmát, a `/etc/bind/named.conf.local` fájlt:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Cserélje le a `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` értéket a virtuális hálózat DNS-utótagját.

    Ez a konfiguráció az Azure rekurzív feloldó irányítja a virtuális hálózat DNS-utótag összes DNS-kérésekre.

2. Az egyéni DNS-kiszolgálón a virtuális hálózat, használja a következő szöveget a tartalmát, a `/etc/bind/named.conf.options` fájlt:

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Cserélje le a `10.0.0.0/16` érték és az IP-címtartományt a virtuális hálózat. Ez a bejegyzés lehetővé teszi, hogy a nevet feloldási kérések címek ebbe a tartományba.

    * Adja hozzá az IP-címtartományt a helyszíni hálózat a `acl goodclients { ... }` szakaszban.  bejegyzés a helyszíni hálózat lehetővé teszi a névfeloldási erőforrásokból.
    
    * Cserélje le az értéket `192.168.0.1` a helyi DNS-kiszolgáló IP-címmel. Ez a bejegyzés a helyi DNS-kiszolgáló minden más kérelemhez DNS irányítja.

3. A konfiguráció használatához indítsa újra a kötés. Például: `sudo service bind9 restart`.

4. A feltételes továbbítók hozzáadása a helyi DNS-kiszolgáló. A feltételes továbbító az 1. lépésben a DNS-utótag kérést küld az egyéni DNS-kiszolgáló konfigurálása.

    > [!NOTE]  
    > Tekintse át a DNS-szoftver bírálattal felvétele a feltételes továbbítók lévő dokumentációját.

Ezek a lépések elvégzése után, vagy teljesen minősített tartománynevet (FQDN) használatával hálózatban lévő erőforrásokra csatlakozhat. HDInsight most már telepítheti a virtuális hálózatban.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Csatlakoztatott virtuális hálózatok közötti névfeloldás

Ebben a példában feltételezésekre a következő:

* Csatlakoztatott vagy VPN-átjáró használatával vagy a társviszony-létesítés két Azure virtuális hálózat rendelkezik.

* A mindkét hálózat egyéni DNS-kiszolgáló fut Linux vagy Unix operációs rendszerként.

* [Kötési](https://www.isc.org/downloads/bind/) telepítve van az egyéni DNS-kiszolgálók.

1. Azure PowerShell vagy a klasszikus Azure CLI segítségével mindkét virtuális hálózat DNS-utótagját keresése:

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Használja a következő szöveget a tartalmát, a `/etc/bind/named.config.local` fájlt az egyéni DNS-kiszolgálón. A módosítás végrehajtása mindkét virtuális hálózat egyéni DNS-kiszolgálón.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Cserélje le a `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` érték és a DNS-utótagja a __más__ virtuális hálózatot. Ez a bejegyzés irányítja a kérelmeket a távoli hálózat DNS-utótag az egyéni a hálózathoz tartozó DNS-hez.

3. Az egyéni DNS-kiszolgálókon a két virtuális hálózatnak, használja a következő szöveget a tartalmát, a `/etc/bind/named.conf.options` fájlt:

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver         
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
   * Cserélje le a `10.0.0.0/16` és `10.1.0.0/16` értékek IP-címtartományok a virtuális hálózatok. Ez a bejegyzés lehetővé teszi, hogy erőforrásokat az egyes hálózatok, hogy a DNS-kiszolgálók kéréseket.

     Az Azure rekurzív feloldó, amelyek nem a virtuális hálózatok (például microsoft.com) DNS-utótagot a kérések kezeli.

4. A konfiguráció használatához indítsa újra a kötés. Ha például `sudo service bind9 restart` mindkét DNS-kiszolgálókon.

Ezek a lépések elvégzése után a teljes tartománynevek (FQDN) használatával virtuális hálózatban lévő erőforrásokra csatlakozhat. HDInsight most már telepítheti a virtuális hálózatban.

## <a name="next-steps"></a>További lépések

* Konfigurálása a helyszíni hálózat csatlakoztatása a HDInsight egy teljes körű példa: [HDInsight csatlakoztatása a helyszíni hálózathoz](./connect-on-premises-network.md).
* Az Azure virtuális hálózatok az Apache Hbase-fürtök konfigurálásához lásd: [Apache HBase-fürtök létrehozása az Azure virtuális hálózaton található HDInsight](hbase/apache-hbase-provision-vnet.md).
* Az Apache HBase-georeplikáció konfigurálása, lásd: [Apache HBase-fürt replikációja az Azure virtuális hálózatok beállítása](hbase/apache-hbase-replication.md).
* Azure virtuális hálózataiban működő további információkért lásd: a [Azure Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md).

* Hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](../virtual-network/security-overview.md).

* További információ a felhasználó által megadott útvonalak: [felhasználó által megadott útvonalak és IP-továbbítás](../virtual-network/virtual-networks-udr-overview.md).
