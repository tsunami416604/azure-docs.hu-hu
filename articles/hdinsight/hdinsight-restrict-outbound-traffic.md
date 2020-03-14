---
title: Kimenő hálózati forgalom korlátozásának konfigurálása – Azure HDInsight
description: Ismerje meg, hogyan konfigurálhatja az Azure HDInsight-fürtök kimenő hálózati forgalmának korlátozását.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 6e0c98cffef06fb6d6345fc2b23bbc22715909b4
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370185"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Az Azure HDInsight-fürtök kimenő hálózati forgalmának konfigurálása tűzfal használatával

Ez a cikk azokat a lépéseket ismerteti, amelyekkel biztonságossá teheti a HDInsight-fürt kimenő forgalmát Azure Firewall használatával. Az alábbi lépések feltételezik, hogy egy Azure Firewall konfigurál egy meglévő fürthöz. Ha új fürtöt helyez üzembe, és tűzfal mögött található, először hozza létre a HDInsight-fürtöt és az alhálózatot, majd kövesse az útmutató lépéseit.

## <a name="background"></a>Háttér

Az Azure HDInsight-fürtöket általában a saját virtuális hálózatában helyezik üzembe. A fürt a virtuális hálózatán kívüli szolgáltatásokkal kapcsolatos függőségekkel rendelkezik, amelyek hálózati hozzáférést igényelnek a megfelelő működéshez.

Több függőség is van, amelyek bejövő forgalmat igényelnek. A bejövő felügyeleti forgalmat nem lehet tűzfal-eszközön keresztül elküldeni. A forgalomhoz tartozó forrásoldali címek ismertek, és [itt](hdinsight-management-ip-addresses.md)lesznek közzétéve. A hálózati biztonsági csoportokra (NSG) vonatkozó szabályokat ezen információk alapján is létrehozhatja a fürtre irányuló bejövő forgalom biztonságossá tételéhez.

A HDInsight kimenő forgalmának függőségei szinte teljesen meg vannak határozva teljes TARTOMÁNYNEVEk használatával, amelyek nem rendelkeznek statikus IP-címekkel. A statikus címek hiánya azt jelenti, hogy a hálózati biztonsági csoportok (NSG-EK) nem használhatók a fürt kimenő forgalmának zárolására. A címek elég gyakran változnak, hogy az aktuális névfeloldáson alapuló szabályok nem állíthatók be, és ez a NSG-szabályok beállítására használható.

A kimenő címek biztonságossá tételére szolgáló megoldás egy olyan tűzfal-eszköz használata, amely a tartománynevek alapján képes szabályozni a kimenő forgalmat. A Azure Firewall a cél-vagy [FQDN-címkék](../firewall/fqdn-tags.md)teljes tartományneve alapján korlátozhatja a kimenő http-és HTTPS-forgalmat.

## <a name="configuring-azure-firewall-with-hdinsight"></a>Azure Firewall konfigurálása a HDInsight

A meglévő HDInsight a Azure Firewall-mel való kilépésének lezárásához szükséges lépések összefoglalása:

1. Hozzon létre egy alhálózatot.
1. Hozzon létre egy tűzfalat.
1. Alkalmazási szabályok hozzáadása a tűzfalhoz
1. Adja hozzá a hálózati szabályokat a tűzfalhoz.
1. Útválasztási táblázat létrehozása.

### <a name="create-new-subnet"></a>Új alhálózat létrehozása

Hozzon létre egy **AzureFirewallSubnet** nevű alhálózatot abban a virtuális hálózaton, amelyben a fürt létezik.

### <a name="create-a-new-firewall-for-your-cluster"></a>Új tűzfal létrehozása a fürthöz

Hozzon létre egy **test-FW01** nevű tűzfalat a **következő** témakörben ismertetett módon [: Azure Firewall telepítése és konfigurálása a Azure Portal használatával](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>A tűzfal konfigurálása alkalmazás-szabályokkal

Hozzon létre egy alkalmazás-szabálygyűjtemény, amely lehetővé teszi a fürt számára a fontos kommunikáció küldését és fogadását.

1. Válassza ki az új **FW01** tűzfalat a Azure Portal.

1. Navigáljon a **beállítások** > **szabályok** > az **alkalmazási szabályok gyűjteménye** >  **+ alkalmazás-szabály hozzáadása gyűjtemény**elemre.

    ![Title: alkalmazás-szabály gyűjtemény hozzáadása](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. Az **alkalmazási szabály felvétele** képernyőn adja meg a következő információkat:

    **Felső szakasz**

    | Tulajdonság|  Érték|
    |---|---|
    |Name (Név)| FwAppRule|
    |Prioritás|200|
    |Műveletek|Engedélyezés|

    **FQDN-címkék szakasz**

    | Name (Név) | Forrás címe | FQDN címke | Megjegyzések |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate és HDInsight | A HDI-szolgáltatásokhoz szükséges |

    **Cél teljes tartománynevek szakasz**

    | Name (Név) | Forrásoldali címek | Protokoll: Port | Cél teljes tartománynevek | Megjegyzések |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https: 443 | login.windows.net | Engedélyezi a Windows-bejelentkezési tevékenységet |
    | Rule_3 | * | https: 443 | login.microsoftonline.com | Engedélyezi a Windows-bejelentkezési tevékenységet |
    | Rule_4 | * | https: 443, http: 80 | storage_account_name. blob. Core. Windows. net | Cserélje le a `storage_account_name`t a tényleges Storage-fiók nevére. Ha a fürtöt a WASB támogatja, vegyen fel egy szabályt a WASB. Ha csak HTTPS-kapcsolatot szeretne használni, győződjön meg arról, hogy a ["biztonságos átvitel szükséges"](../storage/common/storage-require-secure-transfer.md) beállítás engedélyezve van a Storage-fiókon. |

   ![Title: adja meg az alkalmazási szabály gyűjtésének részleteit](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. Válassza a **Hozzáadás** lehetőséget.

### <a name="configure-the-firewall-with-network-rules"></a>A tűzfal konfigurálása hálózati szabályokkal

Hozza létre a hálózati szabályokat a HDInsight-fürt megfelelő konfigurálásához.

1. Folytassa az előző lépéssel, lépjen a **hálózati szabályok gyűjteménye** >  **+ hálózati szabálygyűjtemény hozzáadása**elemre.

1. A **hálózati szabálygyűjtemény hozzáadása** képernyőn adja meg a következő információkat:

    **Felső szakasz**

    | Tulajdonság|  Érték|
    |---|---|
    |Name (Név)| FwNetRule|
    |Prioritás|200|
    |Műveletek|Engedélyezés|

    **IP-címek szakasz**

    | Name (Név) | Protokoll | Forrásoldali címek | Cél címei | Célportok | Megjegyzések |
    | --- | --- | --- | --- | --- | --- |
    | Rule_1 | UDP | * | * | 123 | Időszolgáltatás |
    | Rule_2 | Bármelyik | * | DC_IP_Address_1, DC_IP_Address_2 | * | Ha Enterprise Security Package-t (ESP) használ, adjon hozzá egy hálózati szabályt az IP-címek szakaszban, amely lehetővé teszi a HRE-DS-vel való kommunikációt az ESP-fürtök esetében. A tartományvezérlők IP-címeit a HRE-DS szakaszban találja a portálon. |
    | Rule_3 | TCP | * | A Data Lake Storage fiókjának IP-címe | * | Ha Azure Data Lake Storage használ, akkor az IP-címek szakaszban hozzáadhat egy hálózati szabályt, amely ADLS Gen1 és a Gen2 SNI probléma megoldásához használható. Ez a beállítás átirányítja a forgalmat a tűzfalra, ami magasabb költségekkel járhat a nagyméretű adatterhelések esetében, a forgalom pedig naplózható és naplózható a tűzfal naplófájljaiban. Határozza meg a Data Lake Storage fiókjának IP-címét. Használhat egy PowerShell-parancsot, például a `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")`t a teljes tartománynév IP-címhez való feloldásához.|
    | Rule_4 | TCP | * | * | 12000 | Választható Ha Log Analytics használ, hozzon létre egy hálózati szabályt az IP-címek szakaszban, hogy engedélyezze a kommunikációt a Log Analytics munkaterülettel. |

    **A szolgáltatás címkéi szakasza**

    | Name (Név) | Protokoll | Forráscímek | Szolgáltatás címkéi | Célport | Megjegyzések |
    | --- | --- | --- | --- | --- | --- |
    | Rule_7 | TCP | * | SQL | 1433 | Konfiguráljon egy hálózati szabályt az SQL-hez tartozó szolgáltatás-címkék szakaszban, amely lehetővé teszi az SQL-forgalom naplózását és naplózását, kivéve, ha a HDInsight alhálózaton a SQL Serverhoz konfigurált szolgáltatási végpontokat, ami megkerüli a tűzfalat. |

   ![Title: adja meg az alkalmazási szabálygyűjtemény gyűjteményét](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. Válassza a **Hozzáadás** lehetőséget.

### <a name="create-and-configure-a-route-table"></a>Útválasztási táblázat létrehozása és konfigurálása

Hozzon létre egy útválasztási táblázatot a következő bejegyzésekkel:

* Az [állapot-és felügyeleti szolgáltatásokból](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) származó összes IP-cím: minden olyan régió, amely egy következő ugrási típusú **internettel**rendelkezik.

* Két IP-cím ahhoz a régióhoz, ahol a fürtöt az [állapot-és felügyeleti szolgáltatásokból](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions) hozták létre: a következő ugrás típusú **internettel**rendelkező régiókat.

* A 0.0.0.0/0 IP-címhez tartozó egyik virtuális készülék útvonala a következő ugrás a Azure Firewall magánhálózati IP-címe.

Ha például az útválasztási táblázatot az USA keleti régiójában létrehozott fürthöz szeretné konfigurálni, kövesse az alábbi lépéseket:

1. Válassza ki az Azure Firewall **test-FW01**. Másolja ki az **Áttekintés** lapon felsorolt **magánhálózati IP-címet** . Ebben a példában a **10.0.2.4 egy mintáját**fogjuk használni.

1. Ezután navigáljon az **összes szolgáltatás** > **hálózatkezelés** > **útválasztási táblák** és **create Route Table**.

1. Az új útvonalon navigáljon a **beállítások** > **útvonalak** >  **+ Hozzáadás**elemre. Adja hozzá a következő útvonalakat:

| Útvonal neve | Címelőtag | A következő ugrás típusa | A következő ugrás címe |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | NA |
| 23.99.5.239 | 23.99.5.239/32 | Internet | NA |
| 168.61.48.131 | 168.61.48.131/32 | Internet | NA |
| 138.91.141.162 | 138.91.141.162/32 | Internet | NA |
| 13.82.225.233 | 13.82.225.233/32 | Internet | NA |
| 40.71.175.99 | 40.71.175.99/32 | Internet | NA |
| 0.0.0.0 | 0.0.0.0/0 | Virtuális berendezés | 10.0.2.4 |

Fejezze be az útválasztási táblázat konfigurációját:

1. Rendelje hozzá az HDInsight-alhálózathoz létrehozott útválasztási táblázatot úgy, hogy kiválasztja az **alhálózatok** lehetőséget a **Beállítások**területen.

1. Válassza a **+ hozzárendelés**lehetőséget.

1. Az **alhálózat hozzárendelése** képernyőn válassza ki azt a virtuális hálózatot, amelyben a fürt létrejött, valamint a HDInsight-fürthöz használt **alhálózatot** .

1. Kattintson az **OK** gombra.

## <a name="edge-node-or-custom-application-traffic"></a>Edge-Node vagy egyéni alkalmazás forgalma

A fenti lépések lehetővé teszik, hogy a fürt problémák nélkül működjön. Továbbra is konfigurálnia kell a függőségeket, hogy azok megfeleljenek az Edge-csomópontokon futó egyéni alkalmazásoknak, ha vannak ilyenek.

Az alkalmazás függőségeit azonosítani kell, és hozzá kell adni a Azure Firewall vagy az útválasztási táblához.

A aszimmetrikus útválasztási problémák elkerülése érdekében létre kell hozni útvonalakat az alkalmazás forgalmához.

Ha alkalmazásai más függőségekkel rendelkeznek, azokat fel kell venni a Azure Firewallba. Alkalmazás-szabályok létrehozása a HTTP/HTTPS-forgalom és a hálózati szabályok minden más számára történő engedélyezéséhez.

## <a name="logging-and-scale"></a>Naplózás és méretezés

Azure Firewall küldhet naplókat néhány különböző tárolási rendszernek. A tűzfal naplózásának konfigurálásával kapcsolatos utasításokért kövesse az [oktatóanyag: Azure Firewall-naplók és-metrikák figyelése](../firewall/tutorial-diagnostics.md)című témakör lépéseit.

Ha elvégezte a naplózást, ha Log Analytics adatokat naplóz, a letiltott forgalmat a következő lekérdezéssel tekintheti meg:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

A Azure Firewall Azure Monitor naplókkal való integrálása akkor hasznos, amikor először kap egy alkalmazást, ha nem ismeri az összes alkalmazás-függőséget. További tudnivalókat Azure Monitor naplók [elemzése a naplózási adatokról Azure monitor](../azure-monitor/log-query/log-query-overview.md)

Ha többet szeretne megtudni a Azure Firewall méretezési korlátairól és a kérések növeléséről, tekintse meg [ezt](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) a dokumentumot, vagy tekintse meg a [gyakori kérdéseket](../firewall/firewall-faq.md).

## <a name="access-to-the-cluster"></a>Hozzáférés a fürthöz

A tűzfal sikeres beállítása után a belső végpont (`https://CLUSTERNAME-int.azurehdinsight.net`) segítségével érheti el a Ambari a virtuális hálózaton belülről.

A nyilvános végpont (`https://CLUSTERNAME.azurehdinsight.net`) vagy SSH-végpont (`CLUSTERNAME-ssh.azurehdinsight.net`) használatához győződjön meg arról, hogy rendelkezik a megfelelő útvonalakkal az útválasztási táblázatban és a NSG-szabályokban, hogy elkerülje az aszimmetrikus útválasztási [probléma magyarázatát](../firewall/integrate-lb.md). Ebben az esetben engedélyeznie kell az ügyfél IP-címét a bejövő NSG-szabályokban, és hozzá kell adnia azt a felhasználó által megadott útválasztási táblázathoz a következő ugrási beállítással `internet`. Ha ez nem megfelelő, időtúllépési hiba jelenik meg.

## <a name="configure-another-network-virtual-appliance"></a>Másik hálózati virtuális berendezés konfigurálása

> [!Important]
> A következő információkra **csak** akkor van szükség, ha nem Azure Firewall hálózati virtuális berendezést (NVA) szeretne konfigurálni.

Az előző utasítások segítséget nyújtanak Azure Firewall konfigurálásához a HDInsight-fürt kimenő forgalmának korlátozásához. A Azure Firewall automatikusan úgy van konfigurálva, hogy a gyakori fontos forgatókönyvek esetében lehetővé tegye a forgalmat. Ha másik hálózati virtuális készüléket szeretne használni, manuálisan kell konfigurálnia néhány további funkciót. A hálózati virtuális berendezés konfigurálása során tartsa szem előtt a következőket:

* A szolgáltatási végponttal kompatibilis szolgáltatásokat a szolgáltatási végpontokkal kell konfigurálni.
* Az IP-címek függőségei nem HTTP/S forgalomra vonatkoznak (TCP-és UDP-forgalom).
* Az FQDN HTTP/HTTPS-végpontok a NVA-eszközön helyezhetők el.
* A helyettesítő HTTP/HTTPS-végpontok olyan függőségek, amelyek számos minősítőtől függően változhatnak.
* Rendelje hozzá a HDInsight-alhálózathoz létrehozott útválasztási táblázatot.

### <a name="service-endpoint-capable-dependencies"></a>Szolgáltatási végpontok számára alkalmas függőségek

| **Végpont** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>IP-címek függőségei

| **Végpont** | **Részletek** |
|---|---|
| \*: 123 | NTP órajel-ellenőrzési. A forgalom a 123-es porton több végponton van bejelölve |
| [Itt](hdinsight-management-ip-addresses.md) közzétett IP-címek | Ezek a HDInsight szolgáltatás |
| HRE – DS magánhálózati IP-címek ESP-fürtökhöz |
| \*: 16800 KMS Windows-aktiváláshoz |
| Log Analytics \*12000 |

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-függőségek

> [!Important]
> Az alábbi lista csak néhányat ad a legfontosabb FQDN-nek. A NVA [ezen a fájlban](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)való konfigurálásához a teljes tartománynevek teljes listáját is beolvashatja.

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

## <a name="next-steps"></a>Következő lépések

* [Azure HDInsight virtuális hálózati architektúra](hdinsight-virtual-network-architecture.md)
