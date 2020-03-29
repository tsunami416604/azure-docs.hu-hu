---
title: InvalidNetworkConfigurationErrorCode hiba – Azure HDInsight
description: A sikertelen fürtlétrehozásak különböző okai az InvalidNetworkConfigurationErrorCode segítségével az Azure HDInsightban
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 6dd4db999cb130c9816ad023888a4333e968c224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720384"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>A fürt létrehozása sikertelen az InvalidNetworkConfigurationErrorCode szolgáltatással az Azure HDInsightban

Ez a cikk az Azure HDInsight-fürtökkel való kommunikáció során felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

Ha a "Virtuális hálózat konfigurációja nem kompatibilis a HDInsight-követelménnyel" című hibakódot `InvalidNetworkConfigurationErrorCode` látja, az általában a fürt virtuális hálózati [konfigurációjával](../hdinsight-plan-virtual-network-deployment.md) kapcsolatos problémát jelez. A hiba leírásának többi része alapján kövesse az alábbi szakaszokat a probléma megoldásához.

## <a name="hostname-resolution-failed"></a>"A állomásnév feloldása nem sikerült"

### <a name="issue"></a>Probléma

A hiba leírása a következőt tartalmazza: "A állomásnév feloldása nem sikerült".

### <a name="cause"></a>Ok

Ez a hiba az egyéni DNS-konfigurációval kapcsolatos problémára utal. A virtuális hálózaton belüli DNS-kiszolgálók továbbíthatják a DNS-lekérdezéseket az Azure rekurzív feloldóinak az adott virtuális hálózaton belüli állomásnevek feloldásához (a részleteket [lásd: Névfeloldás a virtuális hálózatokban).](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) Az Azure rekurzív feloldóihoz való hozzáférés a virtuális IP 168.63.129.16-on keresztül érhető el. Ez az IP csak az Azure virtuális gépekről érhető el. Így nem fog működni, ha onprem DNS-kiszolgálót használ, vagy a DNS-kiszolgáló egy Azure virtuális gép, amely nem része a fürt virtuális hálózatának.

### <a name="resolution"></a>Megoldás:

1. Ssh a virtuális gép, amely része a fürt, és futtassa a parancsot `hostname -f`. Ez visszaadja a gazdagép teljesen minősített tartománynevét (a `<host_fqdn>` továbbiakban: az alábbi utasítások).

1. Ezután futtassa a `nslookup <host_fqdn>` `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`parancsot (például ). Ha ez a parancs IP-címre oldja fel a nevet, az azt jelenti, hogy a DNS-kiszolgáló megfelelően működik. Ebben az esetben vessen fel egy támogatási esetet a HDInsight segítségével, és kivizsgáljuk a problémát. A támogatási esetben adja meg a végrehajtott hibaelhárítási lépéseket. Ez segít a probléma gyorsabb megoldásában.

1. Ha a fenti parancs nem ad vissza `nslookup <host_fqdn> 168.63.129.16` IP-címet, akkor futtassa (például `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`). Ha ez a parancs képes feloldani az IP-címet, az azt jelenti, hogy a DNS-kiszolgáló nem továbbítja a lekérdezést az Azure DNS-ére, vagy nem olyan virtuális gép, amely ugyanannak a virtuális hálózatnak a része, mint a fürt.

1. Ha nem rendelkezik olyan Azure-gépekkel, amelyek egyéni DNS-kiszolgálóként működhetnek a fürt virtuális hálózatában, akkor először hozzá kell adnia ezt. Hozzon létre egy virtuális gépet a virtuális hálózatban, amely DNS-továbbítóként lesz konfigurálva.

1. Miután telepített egy virtuális gépet a virtuális hálózatban, konfigurálja a DNS-továbbítási szabályokat ezen a virtuális gépen. Továbbítsa az összes iDNS-névfeloldási kérelmet a 168.63.129.16-ra, a többit pedig a DNS-kiszolgálóra. [Íme](../hdinsight-plan-virtual-network-deployment.md) egy példa az egyéni DNS-kiszolgáló beállítására.

1. Adja hozzá a virtuális gép IP-címét a virtuális hálózat DNS-konfigurációjának első DNS-bejegyzéseként.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Nem sikerült csatlakozni az Azure Storage-fiókhoz"

### <a name="issue"></a>Probléma

A hiba leírása tartalmazza a "Nem sikerült csatlakozni az Azure Storage-fiókhoz" vagy "Nem sikerült csatlakozni az Azure SQL-hez".

### <a name="cause"></a>Ok

Az Azure Storage és az SQL nem rendelkezik rögzített IP-címekkel, ezért engedélyeznikell a kimenő kapcsolatokat az összes IP-szolgáltatóhoz, hogy engedélyezze ezeknek a szolgáltatásoknak a elérését. A pontos megoldási lépések attól függnek, hogy hálózati biztonsági csoportot (NSG) vagy felhasználó által definiált szabályokat (UDR) állított-e be. A [hálózati forgalom hdinsight-mal való szabályozásáról a hálózati biztonsági csoportokkal és a felhasználó által definiált útvonalakkal](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip) című szakaszban tájékformulatot talál ezekről a konfigurációkról.

### <a name="resolution"></a>Megoldás:

* Ha a fürt [hálózati biztonsági csoportot (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md)használ.

    Nyissa meg az Azure Portalon, és azonosítsa az nsg, amely társítva van az alhálózat, ahol a fürt üzembe helyezése. A **Kimenő biztonsági szabályok** szakaszban, engedélyezése kimenő internet-hozzáférés korlátozás nélkül (vegye figyelembe, hogy egy kisebb **prioritási** szám itt azt jelenti, magasabb prioritás). Az **alhálózatok** szakaszban ellenőrizze azt is, hogy ez az NSG alkalmazható-e a fürt alhálózatára.

* Ha a fürt [felhasználó által definiált útvonalakat (UDR)](../../virtual-network/virtual-networks-udr-overview.md)használ.

    Nyissa meg az Azure Portalon, és azonosítsa az útvonaltáblát, amely a fürt üzembe helyezésének alhálózatához van társítva. Miután megtalálta az alhálózat útvonaltáblája, vizsgálja meg az **útvonalszakaszt.**

    Ha vannak definiálva útvonalak, győződjön meg arról, hogy vannak útvonalak az IP-címekhez ahhoz a régióhoz, ahol a fürtöt telepítették, és az egyes útvonalak **NextHopType** az **Internet**. A fent említett cikkben minden szükséges IP-címhez meg kell határozni egy útvonalat.

---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"A virtuális hálózat konfigurációja nem kompatibilis a HDInsight-követelményekkel"

### <a name="issue"></a>Probléma

A hibaleírások a következőhöz hasonló üzeneteket tartalmaznak:

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>Ok

Valószínűleg probléma az egyéni DNS-beállítással.

### <a name="resolution"></a>Megoldás:

Ellenőrizze, hogy a 168.63.129.16 az egyéni DNS-láncban van-e. A virtuális hálózaton belüli DNS-kiszolgálók továbbíthatják a DNS-lekérdezéseket az Azure rekurzív feloldóinak az adott virtuális hálózaton belüli állomásnevek feloldásához. További információt a Névfeloldás a virtuális hálózatokban című [témakörben talál.](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) Az Azure rekurzív feloldóihoz való hozzáférés a virtuális IP 168.63.129.16-on keresztül érhető el.

1. Az [ssh paranccsal](../hdinsight-hadoop-linux-use-ssh-unix.md) csatlakozhat a fürthöz. Az alábbi parancs szerkesztésével cserélje le a CLUSTERNAME-t a fürt nevére, majd írja be a parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Hajtsa végre a következő parancsot:

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    Ennek nagyjából a következőképpen kell kinéznie:

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    Az eredmény alapján - válasszon egyet a következő lépések közül:

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 nem szerepel a listán

**1. lehetőség**  
Adja hozzá a 168.63.129.16-ot a virtuális hálózat első egyéni DNS-eként az [Azure HDInsight virtuális hálózatának megtervezése](../hdinsight-plan-virtual-network-deployment.md)című részben leírt lépések segítségével. Ezek a lépések csak akkor alkalmazhatók, ha az egyéni DNS-kiszolgáló Linuxon fut.

**2. lehetőség**  
Dns-kiszolgáló virtuális gépének telepítése a virtuális hálózathoz. Ez a következő lépéseket foglalja magában:

* Hozzon létre egy virtuális gépet a virtuális hálózatban, amely DNS-továbbítóként lesz konfigurálva (lehet Linux vagy Windows VM).
* Konfigurálja a DNS-továbbítási szabályokat ezen a virtuális gépen (továbbítsa az összes iDNS-névfeloldási kérelmet a 168.63.129.16-ra, a többit pedig a DNS-kiszolgálóra).
* Adja hozzá a virtuális gép IP-címét a virtuális hálózat DNS-konfigurációjának első DNS-bejegyzéseként.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 szerepel a listán

Ebben az esetben hozzon létre egy támogatási esetet a HDInsight segítségével, és kivizsgáljuk a problémát. A támogatási eset tartalmazza az alábbi parancsok eredményét. Ez segít nekünk a probléma gyorsabb kivizsgálásában és megoldásában.

A főcsomópont ssh munkamenetéből szerkesztse, majd futtassa a következőket:

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```

---

### <a name="next-steps"></a>További lépések

Ha nem látta a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikébe:

* Válaszokat kaphat az Azure szakértőitől az [Azure közösségi támogatásán](https://azure.microsoft.com/support/community/)keresztül.

* Lépjen [@AzureSupport](https://twitter.com/azuresupport) kapcsolatba a hivatalos Microsoft Azure-fiókkal, amely javítja az ügyfélélményt azáltal, hogy az Azure-közösséget a megfelelő erőforrásokhoz, válaszokhoz, támogatáshoz és szakértőkhöz csatlakoztatja.

* Ha további segítségre van szüksége, támogatási kérelmet nyújthat be az [Azure Portalról.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Válassza a **menüsor Támogatás parancsát,** vagy nyissa meg a **Súgó + támogatási** központot. További információkért tekintse [át az Azure-támogatási kérelem létrehozása című áttekintést.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Az Előfizetés-kezelés hez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetésrészét képezi, a technikai támogatást pedig az [Azure-támogatási csomagok](https://azure.microsoft.com/support/plans/)egyike biztosítja.
