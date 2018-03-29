---
title: A virtuális hálózattal - Azure HDInsight kiterjesztése |} Microsoft Docs
description: Azure virtuális hálózat használata a HDInsight csatlakozni más felhőalapú erőforrásokat, vagy az adatközpontban lévő erőforrások
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 37b9b600-d7f8-4cb1-a04a-0b3a827c6dcc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/21/2018
ms.author: larryfr
ms.openlocfilehash: b02a4625e3973ca7679d1d2018bd37ff1d2ae2ba
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="extend-azure-hdinsight-using-an-azure-virtual-network"></a>Azure virtuális hálózat használatával Azure HDInsight kiterjesztése

A HDInsight használata egy [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Azure virtuális hálózat használatával lehetővé teszi, hogy a következő esetekben:

* HDInsight csatlakozik egy helyszíni hálózatról.

* A HDInsight-adatokhoz való kapcsolódásról tárolja egy Azure virtuális hálózatot.

* Közvetlen hozzáférés a Hadoop-szolgáltatás által nem biztosított nyilvánosan az interneten keresztül. Például Kafka API-k vagy a HBase Java API-t.

> [!WARNING]
> A jelen dokumentumban szereplő információk megértéséhez a TCP/IP-hálózat igényel. Ha nem ismeri a TCP/IP-hálózat, meg kell felkereshetők a rendszer éles hálózati környezetben módosítások végrehajtása előtt.

> [!IMPORTANT]
> Ha a részletes útmutatót a csatlakozó HDInsight a helyszíni hálózat az Azure virtuális hálózat használatával, lásd: a [a helyi hálózathoz való csatlakozás HDInsight](connect-on-premises-network.md) dokumentum.

## <a name="planning"></a>Tervezés

A kérdésekre kell válaszolnia a HDInsight telepítése egy virtuális hálózat tervezése során a következők:

* HDInsight telepíthető át egy meglévő virtuális hálózathoz kell? Új hálózat létrehozása vagy?

    Ha egy meglévő virtuális hálózatot használ, szükség lehet a hálózati konfiguráció módosításához a HDInsight telepítése előtt. További információkért lásd: a [HDInsight hozzáadása egy meglévő virtuális hálózatot](#existingvnet) szakasz.

* Végrehajtja a virtuális hálózat egy másik virtuális hálózati vagy a helyszíni hálózat HDInsight tartalmazó?

    Segítségével egyszerűen dolgozhat erőforrások hálózatok között, szükség lehet hozzon létre egy egyéni DNS- és DNS-továbbító konfigurálása. További információkért lásd: a [kapcsolódó több hálózatok](#multinet) szakasz.

* Szeretné korlátozni/átirányítás HDInsight bejövő vagy kimenő forgalom?

    A HDInsight az IP-címek az Azure-adatközpontban kommunikálni kell üzemmódban. Van még számos portot, az ügyfél-kommunikációhoz tűzfalon keresztül kell engedélyezni. További információkért lásd: a [hálózati forgalom vezérlése](#networktraffic) szakasz.

## <a id="existingvnet"></a>HDInsight hozzáadása egy meglévő virtuális hálózathoz

Ebben a szakaszban a lépések segítségével egy új HDInsight hozzáadása egy meglévő Azure virtuális hálózat felderítése.

> [!NOTE]
> Nem adhat hozzá egy meglévő HDInsight-fürt virtuális hálózatba.

1. Használja a klasszikus és Resource Manager üzembe helyezési modellben a virtuális hálózat?

    HDInsight 3.4 és nagyobb erőforrás-kezelő virtuális hálózat szükséges. A HDInsight korábbi verzióiban a klasszikus virtuális hálózatot szükséges.

    Ha a meglévő hálózat a klasszikus virtuális hálózatot, majd kell erőforrás-kezelő virtuális hálózat létrehozása és a két csatlakozzon. [A hagyományos Vneteket kapcsolódás új Vnetekhez](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Miután csatlakozott, erőforrás-kezelő hálózatán telepített HDInsight kezelheti a hagyományos hálózati erőforrásokhoz.

2. Használja a kényszerített bújtatás? A kényszerített bújtatás az alhálózat-beállítással, amely arra kényszeríti a kimenő internetforgalom a vizsgálathoz eszközökre és a naplózás. A HDInsight nem támogatja a kényszerített bújtatást. Távolítsa el a kényszerített bújtatás HDInsight egy alhálózatba telepítése előtt, vagy hozzon létre egy új alhálózatot a HDInsight.

3. Használ hálózati biztonsági csoportok, a felhasználó által definiált útvonalak és a virtuális hálózati berendezések korlátozzák a forgalmat a virtuális gépbe vagy onnan a virtuális hálózat számára?

    Felügyelt szolgáltatásként HDInsight az Azure-adatközpont több IP-címeivel korlátlan hozzáférést igényel. Engedélyezi a kommunikációt a IP-címekkel rendelkező, a meglévő hálózati biztonsági csoport vagy felhasználó által definiált útvonalak frissítése.

    HDInsight tároló több szolgáltatásokat, amelyek különféle portokat. Nem blokkolására ezeket a portokat. Engedélyezi a virtuális készülék tűzfalon keresztüli portok listája, tekintse meg a [biztonsági](#security) szakasz.

    A meglévő biztonsági beállítások megkereséséhez használja a következő Azure PowerShell vagy Azure CLI parancsokat:

    * Network security groups (Hálózati biztonsági csoportok)

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-azurermnetworksecuritygroup -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network nsg list --resource-group $RESOURCEGROUP
        ```

        További információkért lásd: a [hibaelhárítása a hálózati biztonsági csoportok](../virtual-network/virtual-network-nsg-troubleshoot-portal.md) dokumentum.

        > [!IMPORTANT]
        > Hálózati biztonsági csoportszabályok szabály prioritási sorrendben alkalmazza. Az első szabály, amely a forgalom bizonyos mintázatnak megfelelő vonatkozik, és nincs más erre a forgalomra alkalmazza. Szabályok a leghatékonyabb a legkevésbé megengedő. További információkért lásd: a [hálózati forgalmat hálózati biztonsági csoportokkal](../virtual-network/virtual-networks-nsg.md) dokumentum.

    * Felhasználó által megadott útvonalak

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-azurermroutetable -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network route-table list --resource-group $RESOURCEGROUP
        ```

        További információkért lásd: a [útvonalak hibaelhárítása](../virtual-network/virtual-network-routes-troubleshoot-portal.md) dokumentum.

4. HDInsight-fürtök létrehozása és konfigurálása során az Azure virtuális hálózatot választ. A következő dokumentumok a lépésekkel Fürtlétrehozási folyamatának ismertetése:

    * [HDInsight létrehozása az Azure Portalon](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [HDInsight létrehozása az Azure PowerShell-lel](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [A HDInsight használata az Azure CLI 1.0 létrehozása](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [HDInsight használata az Azure Resource Manager-sablon létrehozása](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

  > [!IMPORTANT]
  > HDInsight hozzáadása egy virtuális hálózathoz egy opcionális konfigurációs lépésre. Győződjön meg arról, megadhatja a virtuális hálózaton, amikor a fürt konfigurálása.

## <a id="multinet"></a>Több hálózatok csatlakoztatása

A legnagyobb kihívás a több hálózati konfiguráció a hálózatok közötti névfeloldás.

Azure névfeloldást biztosít az Azure virtuális hálózatban telepített szolgáltatások. A beépített névfeloldás lehetővé teszi, hogy a HDInsight egy teljesen minősített tartománynevét (FQDN) használatával a következő erőforrások eléréséhez:

* Bármely erőforrása, amely az interneten érhető el. Ha például a Microsoft.com webhelyre mutat, google.com.

* Az azonos Azure virtuális hálózatban lévő összes erőforrást a __belső DNS-név__ az erőforrás. Például ha az alapértelmezett név feloldása, a következők példa HDInsight munkavégző csomópontokhoz rendelt belső DNS-nevek:

    * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
    * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Mindkét ezeket a csomópontokat közvetlenül kommunikálhatnak egymással, és a Hdinsightban, más csomópontok belső DNS-nevek használatával.

Az alapértelmezett név feloldása does __nem__ engedélyezése a HDInsight a feloldani az erőforrások hálózatokban, amelyek a virtuális hálózathoz csatlakozik. Például esetében gyakori, a helyszíni hálózat a virtuális hálózathoz csatlakozni. Csak az alapértelmezett a névfeloldás HDInsight neve nem tud hozzáférni a helyszíni hálózati erőforrásokhoz. A fordítottja is igaz, a helyszíni hálózati erőforrások neve nem tud hozzáférni a virtuális hálózatán lévő erőforrásokat.

> [!WARNING]
> Az egyéni DNS-kiszolgáló létrehozása és a virtuális hálózat a használatára a HDInsight-fürt létrehozása előtt konfigurálnia kell.

Ahhoz, hogy a névfeloldás a virtuális hálózat és a csatlakoztatott hálózatokon lévő erőforrások között, el kell végeznie a következő műveleteket:

1. Hozzon létre egy egyéni DNS-kiszolgáló az Azure Virtual Network, ahol HDInsight telepítését tervezi.

2. A virtuális hálózatot az egyéni DNS-kiszolgáló használatára konfigurálni.

3. Megállapítja, hogy az Azure hozzárendelése a virtuális hálózat DNS-utótagot. Ez az érték hasonlít `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. Az a DNS-utótag megkereséséről további információkért lásd: a [példa: egyéni DNS](#example-dns) szakasz.

4. Konfigurálja a DNS-kiszolgálók között. A konfiguráció a távoli hálózati típusú függ.

    * Ha a távoli hálózati egy a helyszíni hálózat, konfigurálja a DNS az alábbiak szerint:
        
        * __Egyéni DNS__ (a virtuális hálózaton):

            * A DNS-utótag, hogy az Azure rekurzív feloldó (168.63.129.16) a virtuális hálózat továbbítási kérelmek száma. Azure virtuális hálózatban erőforrásokra vonatkozó kéréseket kezeli

            * A helyi DNS-kiszolgáló minden más kérelemhez továbbítja. A helyszíni DNS kezeli az összes többi feloldási kérések, még akkor is, kérelmek az internetes erőforrásokhoz, például a Microsoft.com webhelyre mutat.

        * __A helyi DNS__: kérelmeket a virtuális hálózat DNS-utótag az egyéni DNS-kiszolgálóra továbbítja. Az egyéni DNS-kiszolgáló majd továbbítja az Azure rekurzív feloldó.

        A konfigurációs útvonalak kérelmek teljes tartománynevek, amelyek tartalmazzák az egyéni DNS-kiszolgáló a virtuális hálózat DNS-utótagját. Minden más kérelemhez (akár még a nyilvános internet címek) a helyi DNS-kiszolgáló kezeli.

    * Ha a távoli hálózati egy másik Azure Virtual Network, konfigurálja a DNS az alábbiak szerint:

        * __Egyéni DNS__ (az egyes virtuális hálózati):

            * A virtuális hálózatok a DNS-utótag kérelmeket a rendszer az egyéni DNS-kiszolgálókra továbbítja. Az egyes virtuális hálózati DNS felelős megoldása érdekében a hálózati erőforrásokat.

            * Minden más kérelemhez továbbítja az Azure rekurzív feloldó. A rekurzív feloldó felelős helyi feloldására és az internetes erőforrásokban.

        A DNS-kiszolgáló az egyes hálózati továbbítja a másikra, kérelmek alapján DNS-utótagot. Más kérések használata az Azure rekurzív feloldó segítségével.

    Minden egyes konfigurációs példáért lásd: a [példa: egyéni DNS](#example-dns) szakasz.

További információkért lásd: a [névfeloldás virtuális gépek és a Szerepkörpéldányok](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) dokumentum.

## <a name="directly-connect-to-hadoop-services"></a>Közvetlenül csatlakozik a Hadoop-szolgáltatás

A legtöbb dokumentáció a HDInsight feltételezi, hogy rendelkezik-e a fürt eléréséhez az interneten keresztül. Például hogy a https://CLUSTERNAME.azurehdinsight.net címen tud csatlakozni a fürthöz. A cím használja a nyilvános átjáró, amely nem érhető el, ha már használta az NSG-k vagy udr-EK hozzáférés korlátozása az internetről.

Ambari és más weblapok a virtuális hálózaton keresztül csatlakozhat, tegye a következőket:

1. Annak megállapításához, a belső teljes tartománynevek (FQDN), a HDInsight-fürtcsomóponton, használja a következő módszerek egyikét:

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

    A visszaadott csomópontok közül a teljes Tartománynevet az átjárócsomópontokkal található, és a teljes tartománynevek használatával Ambari és egyéb webes szolgáltatásokhoz való csatlakozás. Tegyük fel például, `http://<headnode-fqdn>:8080` Ambari eléréséhez.

    > [!IMPORTANT]
    > Az átjárócsomópontokkal tárolt egyes szolgáltatások aktívak csak az egyik csomópont egyszerre. Próbálja meg egy központi csomóponton szolgáltatások elérésére és a 404-es hibaüzenetet ad vissza, ha a többi átjárócsomópont váltani.

2. A csomópont és a portot, amelyet a szolgáltatás elérhető megállapításához lásd: a [a HDInsight Hadoop-szolgáltatás által használt portok](./hdinsight-hadoop-port-settings-for-services.md) dokumentum.

## <a id="networktraffic"></a> Hálózati forgalom vezérlése

Egy Azure virtuális hálózatot a hálózati forgalmat a következő módszerekkel is vezérelhető:

* **Hálózati biztonsági csoportok** (NSG) lehetővé teszi a bejövő és kimenő forgalmat a hálózathoz. További információkért lásd: a [hálózati forgalmat hálózati biztonsági csoportokkal](../virtual-network/virtual-networks-nsg.md) dokumentum.

    > [!WARNING]
    > A HDInsight nem támogatja a kimenő forgalom korlátozása.

* **Felhasználó által definiált útvonalak** (UDR) határozza meg, hogyan a forgalom között a hálózati erőforrásokhoz. További információkért lásd: a [felhasználó által definiált útvonalak és IP-továbbítás](../virtual-network/virtual-networks-udr-overview.md) dokumentum.

* **Virtuális készülékekre** például tűzfalak és az útválasztókat eszközök működésével replikálni. További információkért lásd: a [hálózati berendezések](https://azure.microsoft.com/solutions/network-appliances) dokumentum.

Felügyelt szolgáltatásként HDInsight Azure felhőben Azure állapotát és a felügyeleti szolgáltatások nem korlátozott hozzáférésre van szüksége. Az NSG-k és udr-EK használata esetén győződjön meg róla, hogy a HDInsight ezen szolgáltatások továbbra is kommunikál a HDInsight.

HDInsight szolgáltatások számos portot teszi elérhetővé. Ha egy virtuális készülékre tűzfalat használ, engedélyeznie kell a forgalom a portokon folyik a szolgáltatások. További információkért lásd: a [szükséges portok] szakaszban.

### <a id="hdinsight-ip"></a> A hálózati biztonsági csoportok és a felhasználó által definiált útvonalak HDInsight

Ha a kíván használni **hálózati biztonsági csoportok** vagy **felhasználó által definiált útvonalak** szabályozza a hálózati forgalmat, HDInsight telepítése előtt a következő műveleteket:

1. Azonosítsa a HDInsight használni kívánt Azure-régiót.

2. A HDInsight által megkövetelt IP-címek azonosításához. További információkért lásd: a [HDInsight által megkövetelt IP-címek](#hdinsight-ip) szakasz.

3. Hozzon létre, vagy módosítsa a hálózati biztonsági csoportok vagy a felhasználó által definiált útvonalak az alhálózat, amely a HDInsight telepítését tervezi.

    * __Hálózati biztonsági csoportok__: engedélyezése __bejövő__ porton forgalom __443-as__ IP-címek.
    * __Felhasználó által definiált útvonalak__: hozzon létre egy olyan útvonalat, minden IP-címre, és állítsa be a __a következő ugrás típusa__ való __Internet__.

Hálózati biztonsági csoport vagy felhasználó által definiált útvonalak további információkért lásd az alábbi dokumentáció:

* [Hálózati biztonsági csoport](../virtual-network/virtual-networks-nsg.md)

* [Felhasználó által definiált útvonalak](../virtual-network/virtual-networks-udr-overview.md)

#### <a name="forced-tunneling"></a>Alagúthasználat kényszerítése

A kényszerített bújtatás a beállítás a felhasználói útválasztási ahol alhálózatból származó összes forgalmat egy adott hálózaton vagy a helyre, például a helyszíni hálózat kényszeríti. HDInsight does __nem__ támogatási kényszerített bújtatást.

## <a id="hdinsight-ip"></a> Szükséges IP-címek

> [!IMPORTANT]
> Azure-állapot és a felügyeleti szolgáltatás képes kommunikálni a HDInsight kell lennie. A hálózati biztonsági csoport vagy felhasználó által definiált útvonalakat, ha ezen szolgáltatások HDInsight eléréséhez az IP-címekről érkező forgalom engedélyezésére.
>
> Ha a forgalmat hálózati biztonsági csoport vagy felhasználó által definiált útvonalak nem használja, figyelmen kívül hagyhatja ebben a szakaszban.

Ha hálózati biztonsági csoport vagy felhasználó által definiált útvonalak, engedélyeznie kell a forgalmat az Azure állapot- és felügyeleti szolgáltatással HDInsight eléréséhez. Az alábbi lépések segítségével engedélyezni kell az IP-címek keresése:

1. Mindig engedélyeznie kell a következő IP-címekről érkező adatforgalmat:

    | IP-cím | Engedélyezett port | Irány |
    | ---- | ----- | ----- |
    | 168.61.49.99 | 443 | Bejövő |
    | 23.99.5.239 | 443 | Bejövő |
    | 168.61.48.131 | 443 | Bejövő |
    | 138.91.141.162 | 443 | Bejövő |

2. Ha a HDInsight-fürthöz a következő területek közül, majd engedélyeznie kell a forgalmat a régió felsorolt IP-címekről:

    > [!IMPORTANT]
    > Ha nem szerepel az Azure-régió használ, csak használja az 1. lépés négy IP-címeket.

    | Ország | Régió | Engedélyezett IP-címek | Engedélyezett port | Irány |
    | ---- | ---- | ---- | ---- | ----- |
    | Ázsia | Kelet-Ázsia | 23.102.235.122</br>52.175.38.134 | 443 | Bejövő |
    | &nbsp; | Délkelet-Ázsia | 13.76.245.160</br>13.76.136.249 | 443 | Bejövő |
    | Ausztrália | Kelet-Ausztrália | 104.210.84.115</br>13.75.152.195 | 443 | Bejövő |
    | &nbsp; | Délkelet-Ausztrália | 13.77.2.56</br>13.77.2.94 | 443 | Bejövő |
    | Brazília | Dél-Brazília | 191.235.84.104</br>191.235.87.113 | 443 | Bejövő |
    | Kanada | Kelet-Kanada | 52.229.127.96</br>52.229.123.172 | 443 | Bejövő |
    | &nbsp; | Közép-Kanada | 52.228.37.66</br>52.228.45.222 | 443 | Bejövő |
    | Kína | Észak-Kína | 42.159.96.170</br>139.217.2.219 | 443 | Bejövő |
    | &nbsp; | Kelet-Kína | 42.159.198.178</br>42.159.234.157 | 443 | Bejövő |
    | Európa | Észak-Európa | 52.164.210.96</br>13.74.153.132 | 443 | Bejövő |
    | &nbsp; | Nyugat-Európa| 52.166.243.90</br>52.174.36.244 | 443 | Bejövő |
    | Németország | Közép-Németország | 51.4.146.68</br>51.4.146.80 | 443 | Bejövő |
    | &nbsp; | Északkelet-Németország | 51.5.150.132</br>51.5.144.101 | 443 | Bejövő |
    | India | Közép-India | 52.172.153.209</br>52.172.152.49 | 443 | Bejövő |
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

    Az IP-címek az Azure Government használandó információkért lásd: a [Azure Government Eszközintelligencia + analitika](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics) dokumentum.

3. Ha egy egyéni DNS-kiszolgáló használ a virtuális hálózat, is engedélyeznie kell a hozzáférést a __168.63.129.16__. Ez a cím az Azure rekurzív feloldó. További információkért lásd: a [virtuális gépek és a szerepkör névfeloldását példányok](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) dokumentum.

További információkért lásd: a [hálózati forgalom vezérlése](#networktraffic) szakasz.

## <a id="hdinsight-ports"></a> Szükséges portok

Ha a hálózat használatával kíván **virtuális készülék tűzfal** megvédi a virtuális hálózatot, engedélyeznie kell a kimenő adatforgalmat a következő portokat:

* 53
* 443
* 1433
* 11000-11999
* 14000-14999

A portok adott szolgáltatások listájáért lásd: a [a HDInsight Hadoop-szolgáltatás által használt portok](hdinsight-hadoop-port-settings-for-services.md) dokumentum.

A virtuális készülékek vonatkozó tűzfalszabályok további információkért lásd: a [virtuális berendezésre telepítik](../virtual-network/virtual-network-scenario-udr-gw-nva.md) dokumentum.

## <a id="hdinsight-nsg"></a>Példa: hálózati biztonsági csoportok a hdinsight eszközzel

Ebben a szakaszban szereplő példák bemutatják, hogyan lehet létrehozni a hálózati biztonsági csoportszabályok, amelyek lehetővé teszik a HDInsight az Azure felügyeleti szolgáltatásokkal kommunikálni. A példák használatához állítsa be úgy az IP-címek az Azure-régiót használ megfelelően. Ezt az információt találja a [HDInsight a hálózati biztonsági csoportok és a felhasználó által definiált útvonalak](#hdinsight-ip) szakasz.

### <a name="azure-resource-management-template"></a>Az Azure erőforrás-kezelés sablon

A következő erőforrás-kezelés sablon egy bejövő forgalmát, de lehetővé teszi, hogy a HDInsight által megkövetelt IP-címekről érkező forgalom virtuális hálózatot hoz létre. Ez a sablon is létrehoz egy HDInsight-fürt a virtuális hálózat.

* [A biztonságos Azure virtuális hálózat és egy HDInsight Hadoop-fürt központi telepítése](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

> [!IMPORTANT]
> Módosítsa a megfelelő Azure-régiót használ ebben a példában használt IP-címek. Ezt az információt találja a [HDInsight a hálózati biztonsági csoportok és a felhasználó által definiált útvonalak](#hdinsight-ip) szakasz.

### <a name="azure-powershell"></a>Azure PowerShell

A következő PowerShell-parancsfájl segítségével hozzon létre egy virtuális hálózatot, amely korlátozza a bejövő forgalmat, és lehetővé teszi, hogy az IP-címet az Észak-Európa régió forgalmát.

> [!IMPORTANT]
> Módosítsa a megfelelő Azure-régiót használ ebben a példában használt IP-címek. Ezt az információt találja a [HDInsight a hálózati biztonsági csoportok és a felhasználó által definiált útvonalak](#hdinsight-ip) szakasz.

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"
# Get the Virtual Network object
$vnet = Get-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName
# Get the region the Virtual network is in.
$location = $vnet.Location
# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzureRmNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzureRmNetworkSecurityRuleConfig `
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
    | Add-AzureRmNetworkSecurityRuleConfig `
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
    | Add-AzureRmNetworkSecurityRuleConfig `
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
    | Add-AzureRmNetworkSecurityRuleConfig `
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
    | Add-AzureRmNetworkSecurityRuleConfig `
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
    | Add-AzureRmNetworkSecurityRuleConfig `
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
Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
# Apply the NSG to the subnet
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzureRmVirtual Network
```

> [!IMPORTANT]
> Ez a példa bemutatja, hogyan engedélyezze a bejövő forgalmat a szükséges IP-címek a szabályok hozzáadása. Egy szabályt, amely a más forrásokból bejövő hozzáférés korlátozása nem tartalmaz.
>
> A következő példa bemutatja az internetről SSH-hozzáférés engedélyezése:
>
> ```powershell
> Add-AzureRmNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
> ```

### <a name="azure-cli"></a>Azure CLI

Az alábbi lépések segítségével hozzon létre egy virtuális hálózatot, amely korlátozza a bejövő forgalmat, de lehetővé teszi, hogy a HDInsight által megkövetelt IP-címekről érkező forgalmat.

1. Az alábbi parancs segítségével hozzon létre egy új hálózati biztonsági csoport nevű `hdisecure`. Cserélje le **RESOURCEGROUPNAME** a erőforráscsoporttal, amely tartalmazza az Azure virtuális hálózat. Cserélje le **hely** , amely a csoport létrehozásának a helyét (régió).

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    A csoport létrehozása után az új csoport tájékoztatást kapni.

2. Használja a következő szabályok hozzáadása az új hálózati biztonsági csoportot, amely az Azure HDInsight állapotát és a felügyeleti szolgáltatás a 443-as portot a bejövő kommunikáció. Cserélje le **RESOURCEGROUPNAME** az erőforráscsoport, amely tartalmazza az Azure virtuális hálózat nevével.

    > [!IMPORTANT]
    > Módosítsa a megfelelő Azure-régiót használ ebben a példában használt IP-címek. Ezt az információt találja a [HDInsight a hálózati biztonsági csoportok és a felhasználó által definiált útvonalak](#hdinsight-ip) szakasz.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. A hálózati biztonsági csoport egyedi azonosítója lekéréséhez használja a következő parancsot:

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    Ez a parancs értéket ad vissza az alábbihoz hasonló:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Ha nem a kívánt eredmény elérése érdekében használja a dupla-azonosító idézőjelbe elemet a parancsban.

4. A következő paranccsal egy alhálózatot a hálózati biztonsági csoport vonatkoznak. Cserélje le a __GUID__ és __RESOURCEGROUPNAME__ az értékeket az előző lépésben adja vissza. Cserélje le __VNETNAME__ és __SUBNETNAME__ a virtuálishálózat-névnek és a létrehozni kívánt alhálózat neve.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Ez a parancs után telepítheti a HDInsight a virtuális hálózathoz.

> [!IMPORTANT]
> Ezeket a lépéseket csak nyissa meg az Azure felhőalapú HDInsight állapotát és a felügyeleti szolgáltatás elérését. A virtuális hálózaton kívül a HDInsight-fürt bármely más hozzáférését blokkolja. Ahhoz, hogy a virtuális hálózaton kívülről való eléréshez, azonban további hálózati biztonsági csoport szabályokat kell hozzáadnia.
>
> A következő példa bemutatja az internetről SSH-hozzáférés engedélyezése:
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
> ```

## <a id="example-dns"></a> Példa: DNS-konfiguráció

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Névfeloldás egy virtuális és a csatlakoztatott helyszíni hálózat között

Ebben a példában a következő feltételek teszi:

* Rendelkezik egy Azure virtuális hálózatot, amely egy VPN-átjáró használatával a helyszíni hálózathoz csatlakozik.

* Az egyéni DNS-kiszolgáló a virtuális hálózat fut. Linux vagy Unix operációs rendszert.

* [Kötési](https://www.isc.org/downloads/bind/) az egyéni DNS-kiszolgálóra van telepítve.

Az egyéni DNS-kiszolgálón a virtuális hálózat:

1. Azure PowerShell vagy az Azure CLI segítségével a virtuális hálózat DNS-utótagját található:

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Az egyéni DNS-kiszolgálón a virtuális hálózat, használja a következő szöveget a tartalmát a `/etc/bind/named.conf.local` fájlt:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Cserélje le a `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` értéket a virtuális hálózat DNS-utótagját.

    Ez a konfiguráció összes DNS-kéréseket a virtuális hálózat DNS-utótag az Azure rekurzív feloldó irányítja.

2. Az egyéni DNS-kiszolgálón a virtuális hálózat, használja a következő szöveget a tartalmát a `/etc/bind/named.conf.options` fájlt:

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
    
    * Cserélje le a `10.0.0.0/16` érték és az IP-címtartomány a virtuális hálózat. Ez a bejegyzés lehetővé teszi, hogy a név feloldása kérelmek címek ebbe a tartományba.

    * A helyszíni hálózat az IP-címtartomány hozzáadása a `acl goodclients { ... }` szakasz.  bejegyzés lehetővé teszi, hogy a források névfeloldási a helyszíni hálózat.
    
    * Cserélje le a értékét `192.168.0.1` a helyi DNS-kiszolgáló IP-címmel. Ez a bejegyzés a helyi DNS-kiszolgáló más DNS-kérelmek irányítja.

3. A konfigurációt használja, indítsa újra a kötés. Például: `sudo service bind9 restart`.

4. A feltételes továbbítók hozzáadása a helyi DNS-kiszolgáló. A feltételes továbbító számára, hogy az 1. lépésben a DNS-utótag kérelmeket küldeni az egyéni DNS-kiszolgáló konfigurálása.

    > [!NOTE]
    > A dokumentációban a DNS-szoftver arról, hogyan adhat a feltételes továbbítók.

A lépések elvégzése után csatlakozhat vagy teljes tartománynevét (FQDN) használatával hálózatán lévő erőforrásokat. Most már telepítheti HDInsight létrehozni a virtuális hálózatban.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Két csatlakoztatott virtuális hálózatok közötti névfeloldás

Ebben a példában a következő feltételek teszi:

* VPN-átjáró használatával, vagy a társviszony-létesítés csatlakoztatott két Azure virtuális hálózat rendelkezik.

* Az egyéni DNS-kiszolgáló mindkét hálózatokban fut. Linux vagy Unix operációs rendszert.

* [Kötési](https://www.isc.org/downloads/bind/) az egyéni DNS-kiszolgálókon telepítve van.

1. Azure PowerShell vagy az Azure CLI segítségével mindkét virtuális hálózat DNS-utótagját található:

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Használja a következő szöveget a tartalmát a `/etc/bind/named.config.local` fájlt az egyéni DNS-kiszolgálón. Ennek a módosításnak mindkét virtuális hálózat egyéni DNS-kiszolgálón.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Cserélje le a `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` értéket a DNS-utótagja a __más__ virtuális hálózat. Ez a bejegyzés az egyéni DNS-sel kapcsolatban, hogy a hálózati kérések a távoli hálózati DNS-utótag irányítja.

3. Az egyéni DNS-kiszolgálókon mindkét virtuális hálózatban, használja a következő szöveget a tartalmát, a `/etc/bind/named.conf.options` fájlt:

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
    
    * Cserélje le a `10.0.0.0/16` és `10.1.0.0/16` értékeket az IP-címtartományok a virtuális hálózatok. Ez a bejegyzés lehetővé teszi a DNS-kiszolgálók kérelem létrehozására minden egyes hálózati erőforrások.

    Minden kérést, nem a virtuális hálózatok (Fontos) DNS-utótagokat az Azure rekurzív feloldó kezeli.

4. A konfigurációt használja, indítsa újra a kötés. Például `sudo service bind9 restart` mindkét DNS-kiszolgálókon.

Ezek a lépések végrehajtását követően csatlakozhat a teljes tartománynevek (FQDN) használatával virtuális hálózatán lévő erőforrásokat. Most már telepítheti HDInsight létrehozni a virtuális hálózatban.

## <a name="next-steps"></a>További lépések

* Például egy végpontok közötti egy a helyszíni hálózathoz való kapcsolódáshoz a HDInsight konfigurálása, lásd: [egy a helyszíni hálózathoz való csatlakozás HDInsight](./connect-on-premises-network.md).
* Hbase-fürtökkel konfigurálása az Azure virtuális hálózataihoz, lásd: [hozzon létre HBase-fürtök a HDInsight az Azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* A HBase georeplikáció konfigurálása, lásd: [HBase fürt replikálása az Azure virtuális hálózat beállítása](hbase/apache-hbase-replication.md).
* Azure virtuális hálózataihoz további információkért tekintse meg a [Azure Virtual Network áttekintése](../virtual-network/virtual-networks-overview.md).

* Hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](../virtual-network/virtual-networks-nsg.md).

* A felhasználó által definiált útvonalak további információkért lásd: [felhasználó által definiált útvonalak és IP-továbbítás](../virtual-network/virtual-networks-udr-overview.md).
