---
title: Kimenő hálózati forgalomkorlátozás konfigurálása – Azure HDInsight
description: Ismerje meg, hogyan konfigurálhatja a kimenő hálózati forgalom korlátozását az Azure HDInsight-fürtökhöz.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 6e0c98cffef06fb6d6345fc2b23bbc22715909b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370185"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Kimenő hálózati forgalom konfigurálása az Azure HDInsight-fürtökhöz tűzfal használatával

Ez a cikk a HDInsight-fürtről az Azure Firewall használatával érkező kimenő forgalom védelméhez szükséges lépéseket ismerteti. Az alábbi lépések feltételezik, hogy egy Azure tűzfal konfigurálása egy meglévő fürthöz. Ha új fürtöt telepít, és tűzfal mögé helyezi, először hozza létre a HDInsight-fürtöt és alhálózatot, majd kövesse az útmutatóban ismertetett lépéseket.

## <a name="background"></a>Háttér

Az Azure HDInsight-fürtök általában a saját virtuális hálózatában vannak telepítve. A fürt a virtuális hálózaton kívüli olyan szolgáltatásoktól függ, amelyek megfelelő működéséhez hálózati hozzáférés szükséges.

Számos függőség ek, amelyek megkövetelik a bejövő forgalmat. A bejövő felügyeleti forgalom nem küldhető tűzfaleszközön keresztül. A forgalom forráscímei ismertek és [itt](hdinsight-management-ip-addresses.md)vannak közzétéve. Ezekkel az adatokkal hálózati biztonsági csoport (NSG) szabályokat is létrehozhat a fürtök befelé irányuló forgalom ának védelméhez.

A HDInsight kimenő forgalmi függőségek szinte teljes mértékben definiálva vannak a teljes tartománynevekkel, amelyek mögött nincs statikus IP-cím. A statikus címek hiánya azt jelenti, hogy a hálózati biztonsági csoportok (NSG-k) nem használhatók a fürt kimenő forgalmának zárolására. A címek elég gyakran változnak ahhoz, hogy az aktuális névfeloldás alapján ne lehet szabályokat beállítani, és ezzel az NSG-szabályok beállítására.

A kimenő címek biztonságossá tétele olyan tűzfaleszköz használata, amely a tartománynevek alapján szabályozhatja a kimenő forgalmat. Az Azure Firewall korlátozhatja a kimenő HTTP- és HTTPS-forgalmat a cél- vagy [teljes tartománynév-címkék](../firewall/fqdn-tags.md)alapján.

## <a name="configuring-azure-firewall-with-hdinsight"></a>Az Azure tűzfal konfigurálása a HDInsight segítségével

A meglévő HDInsight-adatokból az Azure Tűzfallal való zárolásának lépései a következők:

1. Hozzon létre egy alhálózatot.
1. Hozzon létre egy tűzfalat.
1. Alkalmazásszabályok hozzáadása a tűzfalhoz
1. Hálózati szabályok hozzáadása a tűzfalhoz.
1. Útválasztási tábla létrehozása.

### <a name="create-new-subnet"></a>Új alhálózat létrehozása

Hozzon létre egy **AzureFirewallSubnet** nevű alhálózatot abban a virtuális hálózatban, ahol a fürt létezik.

### <a name="create-a-new-firewall-for-your-cluster"></a>Új tűzfal létrehozása a fürt számára

Hozzon létre egy **Test-FW01** nevű tűzfalat **a tűzfal üzembe helyezése az** [oktatóanyagból: Az Azure Firewall telepítése és konfigurálása az Azure Portal használatával](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)című lépéseivel.

### <a name="configure-the-firewall-with-application-rules"></a>A tűzfal konfigurálása alkalmazásszabályokkal

Hozzon létre egy alkalmazásszabály-gyűjteményt, amely lehetővé teszi a fürt számára a fontos kommunikáció küldését és fogadását.

1. Válassza ki az új **teszt-FW01** tűzfalat az Azure Portalon.

1. Keresse meg a **Beállítások** > **szabályok** > **Alkalmazásszabály-gyűjtemény** > **+ Alkalmazásszabály-gyűjtemény hozzáadása**lehetőséget.

    ![Cím: Alkalmazásszabály-gyűjtemény hozzáadása](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. Az **Alkalmazásszabály-gyűjtemény hozzáadása** képernyőn adja meg a következő információkat:

    **Felső rész**

    | Tulajdonság|  Érték|
    |---|---|
    |Név| FwAppRule|
    |Prioritás|200|
    |Műveletek|Engedélyezés|

    **Teljes tartománynév-címkék szakasz**

    | Név | Forrás címe | Teljes tartománynév-címke | Megjegyzések |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate és HDInsight | Hdi-szolgáltatásokhoz szükséges |

    **Cél teljes tartománynaszakasz**

    | Név | Forráscímek | protokoll:port | Cél FQDNS | Megjegyzések |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https:443 | login.windows.net | Engedélyezi a Windows bejelentkezési tevékenységét |
    | Rule_3 | * | https:443 | login.microsoftonline.com | Engedélyezi a Windows bejelentkezési tevékenységét |
    | Rule_4 | * | https:443,http:80 | storage_account_name.blob.core.windows.net | Cserélje `storage_account_name` le a tényleges tárfiók nevét. Ha a fürt támogatja a WASB, majd adjunk hozzá egy szabályt WASB. Ha csak https-kapcsolatokat szeretne használni, győződjön meg arról, hogy a ["biztonságos átvitel szükséges"](../storage/common/storage-require-secure-transfer.md) engedélyezve van a tárfiókban. |

   ![Cím: Adja meg az alkalmazásszabály gyűjteményének részleteit](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. Válassza a **Hozzáadás** lehetőséget.

### <a name="configure-the-firewall-with-network-rules"></a>A tűzfal konfigurálása hálózati szabályokkal

Hozza létre a hálózati szabályokat a HDInsight-fürt megfelelő konfigurálásához.

1. Az előző lépéstől folytatva keresse meg a **Hálózati szabálygyűjtemény** > **+ Hálózati szabálygyűjtemény hozzáadása .**

1. A **Hálózati szabály gyűjteményének hozzáadása** képernyőn adja meg a következő információkat:

    **Felső rész**

    | Tulajdonság|  Érték|
    |---|---|
    |Név| FwNetRule|
    |Prioritás|200|
    |Műveletek|Engedélyezés|

    **IP-címek szakasz**

    | Név | Protocol (Protokoll) | Forráscímek | Célcímek | Célportok | Megjegyzések |
    | --- | --- | --- | --- | --- | --- |
    | Rule_1 | UDP | * | * | 123 | Időszolgáltatás |
    | Rule_2 | Bármelyik | * | DC_IP_Address_2. DC_IP_Address_1. | * | Vállalati biztonsági csomag (ESP) használata esetén, majd adjon hozzá egy hálózati szabályt az IP-címek szakaszban, amely lehetővé teszi az ESP-fürtök AAD-DS szolgáltatással való kommunikációt. A tartományvezérlők IP-címeit az AAD-DS szakasz AAD-DS szakaszában találja a portálon |
    | Rule_3 | TCP | * | A Data Lake Storage-fiók IP-címe | * | Ha az Azure Data Lake Storage-t használja, hozzáadhat egy hálózati szabályt az IP-címek szakaszban az ADLS Gen1 és gen2 SNI-probléma megoldásához. Ez a beállítás a forgalmat a tűzfalra irányítja, ami magasabb költségeket eredményezhet a nagy adatterhelések esetén, de a rendszer naplózza és naplózhatja a tűzfalnaplókban a forgalmat. Határozza meg a Data Lake Storage-fiók IP-címét. Powershell-parancs használatával feloldhatja `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` a teljes tartománynnnt egy IP-címre.|
    | Rule_4 | TCP | * | * | 12000 | (Nem kötelező) Ha log analytics szolgáltatást használ, majd hozzon létre egy hálózati szabályt az IP-címek szakaszban a Log Analytics-munkaterülettel való kommunikáció engedélyezéséhez. |

    **Szolgáltatás címkék szakasz**

    | Név | Protocol (Protokoll) | Forráscímek | Szolgáltatáscímkék | Célportok | Megjegyzések |
    | --- | --- | --- | --- | --- | --- |
    | Rule_7 | TCP | * | SQL | 1433 | Konfiguráljon egy hálózati szabályt az SQL Szolgáltatáscímkék szakaszában, amely lehetővé teszi az SQL-forgalom naplózását és naplózását, kivéve, ha a HDInsight alhálózaton konfigurálta az SQL Server szolgáltatásvégpontjait, amely megkerüli a tűzfalat. |

   ![Cím: Alkalmazásszabály-gyűjtemény megadása](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. Válassza a **Hozzáadás** lehetőséget.

### <a name="create-and-configure-a-route-table"></a>Útvonaltábla létrehozása és konfigurálása

Hozzon létre egy útvonaltáblát a következő bejegyzésekkel:

* Minden IP-cím [az Egészségügyi és felügyeleti szolgáltatásokból: Minden olyan régió,](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) ahol a következő ugrástípusú internet **van.**

* Két IP-cím annak a régiónak, ahol a fürt az Állapot és felügyeleti szolgáltatások szolgáltatásból jön [létre: Adott régiók](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions) a következő ugrástípusú **internettel.**

* Egy Virtual Appliance-útvonal a 0.0.0.0/0 IP-címhez, a következő ugrás pedig az Azure Tűzfal privát IP-címe.

Ha például az USA "USA keleti régiójában" létrehozott fürt útvonaltábláját szeretné konfigurálni, kövesse az alábbi lépéseket:

1. Válassza ki az Azure-firewall **Test-FW01-et.** Másolja a **magánjellegű IP-címet** az **Áttekintés** lapon. Ebben a példában a **minta címe 10.0.2.4**.

1. Ezután lépjen a **Minden szolgáltatás** > **hálózati** > **útvonaltáblára** és **az Útvonaltábla létrehozása elemre.**

1. Az új útvonalon keresse meg a **Beállítások** > **útvonalak** > **+ Hozzáadás**lehetőséget. Adja hozzá a következő útvonalakat:

| Útvonal neve | Címelőtag | A következő ugrás típusa | A következő ugrás címe |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | NA |
| 23.99.5.239 | 23.99.5.239/32 | Internet | NA |
| 168.61.48.131 | 168.61.48.131/32 | Internet | NA |
| 138.91.141.162 | 138.91.141.162/32 | Internet | NA |
| 13.82.225.233 | 13.82.225.233/32 | Internet | NA |
| 40.71.175.99 | 40.71.175.99/32 | Internet | NA |
| 0.0.0.0 | 0.0.0.0/0 | Virtuális berendezés | 10.0.2.4 |

Az útvonaltábla konfigurációjának befejezése:

1. Rendelje hozzá a létrehozott útvonaltáblát a HDInsight-alhálózathoz a **Beállítások**csoport **Alhálózatok** elemének kiválasztásával.

1. Válassza a **+ Társítás**lehetőséget.

1. A **Társalhálózat** képernyőn válassza ki azt a virtuális hálózatot, amelybe a fürtöt létrehozták, és azt az **alhálózatot,** amelyet a HDInsight-fürthöz használt.

1. Válassza **az OK gombot.**

## <a name="edge-node-or-custom-application-traffic"></a>Él-csomópont vagy egyéni alkalmazásforgalom

A fenti lépések lehetővé teszik a fürt működését problémák nélkül. Továbbra is konfigurálnia kell a függőségeket a peremhálózati csomópontokon futó egyéni alkalmazások nak, ha vannak ilyenek.

Az alkalmazásfüggőségeket azonosítani kell, és hozzá kell adni az Azure tűzfalhoz vagy az útvonaltáblához.

Az aszimmetrikus útválasztási problémák elkerülése érdekében útvonalakat kell létrehozni az alkalmazásforgalmához.

Ha az alkalmazások más függőségekkel rendelkeznek, hozzá kell adni őket az Azure tűzfalhoz. Alkalmazásszabályok létrehozása a HTTP/HTTPS-forgalom és a hálózati szabályok engedélyezéséhez minden máshoz.

## <a name="logging-and-scale"></a>Naplózás és méretezés

Az Azure Firewall naplókat küldhet néhány különböző tárolórendszernek. A tűzfal naplózásának konfigurálásával kapcsolatos útmutatásért kövesse az [Oktatóanyag: Az Azure tűzfal naplóinak és metrikáinak figyelése](../firewall/tutorial-diagnostics.md)című témakör lépéseit.

A naplózási beállítás befejezése után, ha adatokat naplóz a Log Analytics szolgáltatásba, megtekintheti a blokkolt forgalmat egy lekérdezéssel, például a következőkkel:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Az Azure-tűzfal integrálása az Azure Monitor-naplók hasznos, ha először egy alkalmazás működik, ha nem ismeri az összes alkalmazás-függőségek. Az Azure Monitor naplóiról az [Azure Monitor adatainak elemzése az Azure Monitorban](../azure-monitor/log-query/log-query-overview.md) című naplóból tudhat meg többet.

Az Azure Firewall méretezési korlátairól és a kérelmek növeléséről ebben a [dokumentumban](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) vagy a [gyakori kérdések](../firewall/firewall-faq.md)című témakörben olvashat.

## <a name="access-to-the-cluster"></a>Hozzáférés a fürthöz

Miután a tűzfal sikeresen beállította, használhatja`https://CLUSTERNAME-int.azurehdinsight.net`a belső végpont ( ) eléréséhez ambari belülről a virtuális hálózat.

A nyilvános végpont (`https://CLUSTERNAME.azurehdinsight.net`) vagy ssh`CLUSTERNAME-ssh.azurehdinsight.net`végpont ( használatához győződjön meg arról, hogy a megfelelő útvonalak vannak az útvonaltáblában és az NSG-szabályokban az [itt](../firewall/integrate-lb.md)ismertetett aszimmetrikus útválasztási probléma elkerülése érdekében . Ebben az esetben engedélyeznie kell az ügyfél IP-címét a bejövő NSG-szabályokban, és hozzá kell adnia `internet`a felhasználó által definiált útvonaltáblához a következő ugrásbeállítással. Ha ez nincs megfelelően beállítva, időtúllépést fog látni.

## <a name="configure-another-network-virtual-appliance"></a>Másik hálózati virtuális készülék konfigurálása

> [!Important]
> A következő információk **csak** akkor szükségesek, ha az Azure Firewalltől eltérő hálózati virtuális berendezést (NVA) kíván konfigurálni.

Az előző utasítások segítségével konfigurálhatja az Azure Firewall a HDInsight-fürt kimenő forgalmának korlátozására. Az Azure Firewall automatikusan konfigurálva van, hogy a forgalom számos gyakori fontos forgatókönyvek. Ha egy másik hálózati virtuális készüléket szeretne használni, manuálisan kell konfigurálnia számos további szolgáltatást. A hálózati virtuális berendezés konfigurálásakor tartsa szem előtt az alábbiakat:

* A szolgáltatásvégpontra képes szolgáltatásokat szolgáltatásvégpontokkal kell konfigurálni.
* Az IP-címfüggőségek nem HTTP/S-forgalomra (tcp- és UDP-forgalomra egyaránt) szolgálnak.
* Az FQDN HTTP/HTTPS végpontok az NVA-eszközbe helyezhetők.
* A helyettesítő HTTP/HTTPS-végpontok olyan függőségek, amelyek számos minősítőtől függően változhatnak.
* Rendelje hozzá a létrehozott útvonaltáblát a HDInsight-alhálózathoz.

### <a name="service-endpoint-capable-dependencies"></a>Végpontra képes függőségek szolgáltatása

| **Végpont** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>IP-címfüggőségek

| **Végpont** | **Részletek** |
|---|---|
| \*:123 | NTP óra ellenőrzés. A forgalmat a 123-as port több végpontján ellenőrzik |
| [Itt](hdinsight-management-ip-addresses.md) közzétett IP-k | Ezek a HDInsight szolgáltatás |
| AAD-DS privát IP-k ESP-fürtökhöz |
| \*:16800 kms Windows aktiváláshoz |
| \*12000 a Log Analytics szolgáltatáshoz |

#### <a name="fqdn-httphttps-dependencies"></a>Teljes qdn HTTP/HTTPS-függőségek

> [!Important]
> Az alábbi lista csak néhányat ad a legfontosabb teljes tartománynévből. Az NVA konfigurálásához szükséges teljes tartománynév-listát [ebben a fájlban](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)kaphatja meg.

| **Végpont**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>További lépések

* [Azure HDInsight virtuális hálózati architektúra](hdinsight-virtual-network-architecture.md)
