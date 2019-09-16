---
title: A kimenő hálózati forgalom korlátozásának konfigurálása az Azure HDInsight-fürtökhöz
description: Ismerje meg, hogyan konfigurálhatja az Azure HDInsight-fürtök kimenő hálózati forgalmának korlátozását.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 070365c79e14b80c50c70aa3277a6eddd9286a37
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2019
ms.locfileid: "71018748"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall-preview"></a>Az Azure HDInsight-fürtök kimenő hálózati forgalmának konfigurálása tűzfallal (előzetes verzió)

Ez a cikk azokat a lépéseket ismerteti, amelyekkel biztonságossá teheti a HDInsight-fürt kimenő forgalmát Azure Firewall használatával. Az alábbi lépések feltételezik, hogy egy meglévő fürthöz Azure Firewall konfigurál. Ha új fürtöt helyez üzembe, és tűzfal mögött található, először hozza létre a HDInsight-fürtöt és az alhálózatot, majd kövesse az útmutató lépéseit.

## <a name="background"></a>Háttér

Az Azure HDInsight-fürtöket általában a saját virtuális hálózatában helyezik üzembe. A fürt a virtuális hálózatán kívüli szolgáltatásokkal kapcsolatos függőségekkel rendelkezik, amelyek hálózati hozzáférést igényelnek a megfelelő működéshez.

Több függőség is van, amelyek bejövő forgalmat igényelnek. A bejövő felügyeleti forgalmat nem lehet tűzfal-eszközön keresztül elküldeni. A forgalomhoz tartozó forrásoldali címek ismertek, és [itt](hdinsight-management-ip-addresses.md)lesznek közzétéve. A hálózati biztonsági csoportokra (NSG) vonatkozó szabályokat ezen információk alapján is létrehozhatja a fürtre irányuló bejövő forgalom biztonságossá tételéhez.

A HDInsight kimenő forgalmának függőségei szinte teljesen meg vannak határozva teljes TARTOMÁNYNEVEk használatával, amelyek nem rendelkeznek statikus IP-címekkel. A statikus címek hiánya azt jelenti, hogy a hálózati biztonsági csoportok (NSG-EK) nem használhatók a fürt kimenő forgalmának zárolására. A címek elég gyakran változnak, hogy az aktuális névfeloldáson alapuló szabályok nem állíthatók be, és ez a NSG-szabályok beállítására használható.

A kimenő címek biztonságossá tételére szolgáló megoldás egy olyan tűzfal-eszköz használata, amely a tartománynevek alapján képes szabályozni a kimenő forgalmat. A Azure Firewall a cél-vagy [FQDN-címkék](https://docs.microsoft.com/azure/firewall/fqdn-tags)teljes tartományneve alapján korlátozhatja a kimenő http-és HTTPS-forgalmat.

## <a name="configuring-azure-firewall-with-hdinsight"></a>Azure Firewall konfigurálása a HDInsight

A meglévő HDInsight a Azure Firewall-mel való kilépésének lezárásához szükséges lépések összefoglalása:
1. Hozzon létre egy tűzfalat.
1. Alkalmazási szabályok hozzáadása a tűzfalhoz
1. Adja hozzá a hálózati szabályokat a tűzfalhoz.
1. Útválasztási táblázat létrehozása.

### <a name="create-a-new-firewall-for-your-cluster"></a>Új tűzfal létrehozása a fürthöz

1. Hozzon létre egy **AzureFirewallSubnet** nevű alhálózatot abban a virtuális hálózaton, amelyben a fürt létezik. 
1. Hozzon létre egy új **FW01-tesztet** az [oktatóanyag lépéseinek használatával: Azure Firewall üzembe helyezése és konfigurálása a](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall)Azure Portal használatával.

### <a name="configure-the-firewall-with-application-rules"></a>A tűzfal konfigurálása alkalmazás-szabályokkal

Hozzon létre egy alkalmazás-szabálygyűjtemény, amely lehetővé teszi a fürt számára a fontos kommunikáció küldését és fogadását.

Válassza ki az új **FW01** tűzfalat a Azure Portal. Kattintson a **szabályok** elemre, majd a **Beállítások** > **alkalmazás-szabály gyűjtemény** > alkalmazás-szabálygyűjtemény**hozzáadása**lehetőségre.

![Cím Alkalmazásszabály-gyűjtemény hozzáadása](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

Az **alkalmazás-szabály felvétele** képernyőn hajtsa végre a következő lépéseket:

1. Adjon meg egy **nevet**, egy **prioritást**, majd kattintson az **Engedélyezés** elemre a **művelet** legördülő menüből, és írja be a következő szabályokat a **teljes tartománynév címkék szakaszba** :

   | **Name** | **Forrás címe** | **FQDN címke** | **Megjegyzések** |
   | --- | --- | --- | --- |
   | Rule_1 | * | HDInsight és WindowsUpdate | A HDI-szolgáltatásokhoz szükséges |

1. Adja hozzá a következő szabályokat a **cél teljes tartománynevek szakaszhoz** :

   | **Name** | **Forrás címe** | **Protokoll: Port** | **Cél teljes tartománynevek** | **Megjegyzések** |
   | --- | --- | --- | --- | --- |
   | Rule_2 | * | https: 443 | login.windows.net | Engedélyezi a Windows-bejelentkezési tevékenységet |
   | Rule_3 | * | https: 443 | login.microsoftonline.com | Engedélyezi a Windows-bejelentkezési tevékenységet |
   | Rule_4 | * | https: 443, http: 80 | <storage_account_name.blob.core.windows.net> | Ha a fürtöt a WASB támogatja, vegyen fel egy szabályt a WASB. Ha csak HTTPS-kapcsolatot szeretne használni, győződjön meg arról, hogy a ["biztonságos átvitel szükséges"](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) beállítás engedélyezve van a Storage-fiókon. |

1. Kattintson a **Hozzáadás**lehetőségre.

   ![Cím Adja meg az alkalmazási szabály gyűjtésének részleteit](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>A tűzfal konfigurálása hálózati szabályokkal

Hozza létre a hálózati szabályokat a HDInsight-fürt megfelelő konfigurálásához.

1. Válassza ki az új **FW01** tűzfalat a Azure Portal.
1. Kattintson **a** **Beállítások** > hálózatiszabálygyűjtemény > hálózati szabálygyűjtemény**hozzáadása**elemre.
1. A **hálózati szabálygyűjtemény hozzáadása** képernyőn adja meg a **nevet**és a **prioritást**, majd kattintson az **Engedélyezés** elemre a **művelet** legördülő menüből.
1. Hozza létre a következő szabályokat az **IP-címek** szakaszban:

   | **Name** | **Protocol (Protokoll)** | **Forrás címe** | **Cél címe** | **Célport** | **Megjegyzések** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_1 | UDP | * | * | `123` | Időszolgáltatás |
   | Rule_2 | Any | * | DC_IP_Address_1, DC_IP_Address_2 | `*` | Ha Enterprise Security Package (ESP) protokollt használ, adjon hozzá egy hálózati szabályt az IP-címek szakaszban, amely lehetővé teszi a HRE-DS-vel való kommunikációt az ESP-fürtök esetében. A tartományvezérlők IP-címeit a HRE-DS szakaszban találja a portálon. | 
   | Rule_3 | TCP | * | A Data Lake Storage fiókjának IP-címe | `*` | Ha Azure Data Lake Storage használ, akkor az IP-címek szakaszban hozzáadhat egy hálózati szabályt a ADLS Gen1 és a Gen2 SNI kapcsolatos probléma megoldásához. Ezzel a beállítással a rendszer átirányítja a forgalmat a tűzfalra, ami magasabb költségekkel járhat a nagyméretű adatterhelések esetében, a forgalom pedig naplózható és naplózható a tűzfal naplófájljaiban. Határozza meg a Data Lake Storage fiókjának IP-címét. Használhat egy PowerShell-parancsot, például `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` a teljes tartománynevet az IP-címek feloldásához.|
   | Rule_4 | TCP | * | * | `12000` | Választható Ha Log Analytics használ, hozzon létre egy hálózati szabályt az IP-címek szakaszban, hogy engedélyezze a kommunikációt a Log Analytics munkaterülettel. |

1. Hozza létre a következő szabályokat a **szolgáltatás címkék** szakaszában:

   | **Name** | **Protocol (Protokoll)** | **Forrás címe** | **Szolgáltatás címkéi** | **Célport** | **Megjegyzések** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_7 | TCP | * | SQL | `1433` | Konfiguráljon egy hálózati szabályt az SQL-hez tartozó szolgáltatás-címkék szakaszban, amely lehetővé teszi az SQL-forgalom naplózását és naplózását, kivéve, ha a HDInsight alhálózaton olyan SQL Serverhoz konfigurálta a szolgáltatási végpontokat, amelyek megkerülik a tűzfalat. |

1. A hálózati szabálygyűjtemény létrehozásának befejezéséhez kattintson a **Hozzáadás** gombra.

   ![Cím Adja meg az alkalmazási szabálygyűjtemény gyűjteményét](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>Útválasztási táblázat létrehozása és konfigurálása

Hozzon létre egy útválasztási táblázatot a következő bejegyzésekkel:

1. Hat cím a [szükséges HDInsight-felügyeleti IP-címek listájáról](../hdinsight/hdinsight-management-ip-addresses.md) az **Internet**következő ugrásával:
    1. Négy IP-cím minden fürthöz az összes régióban
    1. Két IP-cím, amelyek a fürt létrehozási régiójára vonatkoznak
1. A 0.0.0.0/0 IP-címhez tartozó egyik virtuális készülék útvonala a következő ugrás a Azure Firewall magánhálózati IP-címe.

Ha például az útválasztási táblázatot az USA középső régiójában létrehozott fürthöz szeretné konfigurálni, kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure portálra.
1. Válassza ki az Azure Firewall **test-FW01**. Másolja ki az **Áttekintés** lapon felsorolt **magánhálózati IP-címet** . Ebben a példában a **10.1.1.4 egy mintáját** fogjuk használni
1. Hozzon létre egy új útválasztási táblázatot.
1. Kattintson a **Beállítások**alatt lévő **útvonalak** elemre.
1. A **Hozzáadás** gombra kattintva hozzon létre útvonalakat az alábbi táblázatban szereplő IP-címekhez.

| Útvonalnév | Címelőtag | Következő ugrási típus | Következő ugrási cím |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | NA |
| 23.99.5.239 | 23.99.5.239/32 | Internet | NA |
| 168.61.48.131 | 168.61.48.131/32 | Internet | NA |
| 138.91.141.162 | 138.91.141.162/32 | Internet | NA |
| 13.67.223.215 | 13.67.223.215/32 | Internet | NA |
| 40.86.83.253 | 40.86.83.253/32 | Internet | NA |
| 0.0.0.0 | 0.0.0.0/0 | Virtuális berendezés | 10.1.1.4 |

Fejezze be az útválasztási táblázat konfigurációját:

1. Rendelje hozzá a HDInsight-alhálózathoz a létrehozott útválasztási táblázatot úgy, hogy az **alhálózatok** elemre kattint, majd a **társítás** **lehetőséget** választja.
1. Az **alhálózat hozzárendelése** képernyőn válassza ki azt a virtuális hálózatot, amelyben a fürt létrejött, valamint a HDInsight-fürthöz használt **HDInsight-alhálózatot** .
1. Kattintson az **OK** gombra.

## <a name="edge-node-or-custom-application-traffic"></a>Edge-Node vagy egyéni alkalmazás forgalma

A fenti lépések lehetővé teszik, hogy a fürt problémák nélkül működjön. Továbbra is konfigurálnia kell a függőségeket, hogy azok megfeleljenek az Edge-csomópontokon futó egyéni alkalmazásoknak, ha vannak ilyenek.

Az alkalmazás függőségeit azonosítani kell, és hozzá kell adni a Azure Firewall vagy az útválasztási táblához.

A aszimmetrikus útválasztási problémák elkerülése érdekében létre kell hozni útvonalakat az alkalmazás forgalmához.

Ha alkalmazásai más függőségekkel rendelkeznek, azokat fel kell venni a Azure Firewallba. Alkalmazás-szabályok létrehozása a HTTP/HTTPS-forgalom és a hálózati szabályok minden más számára történő engedélyezéséhez.

## <a name="logging"></a>Naplózás

Azure Firewall küldhet naplókat néhány különböző tárolási rendszernek. A tűzfal naplózásának konfigurálásával kapcsolatos utasításokért kövesse az [oktatóanyag lépéseit: Azure Firewall naplók és metrikák](../firewall/tutorial-diagnostics.md)figyelése.

Ha elvégezte a naplózást, ha Log Analytics adatokat naplóz, a letiltott forgalmat a következő lekérdezéssel tekintheti meg:

```
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Ha nem ismeri az összes alkalmazás-függőséget, akkor hasznos, ha a Azure Firewall Azure Monitor naplókkal való integrációja során először működik. További tudnivalókat Azure Monitor naplók [elemzése a naplózási adatokról Azure monitor](../azure-monitor/log-query/log-query-overview.md)

## <a name="access-to-the-cluster"></a>Hozzáférés a fürthöz
A tűzfal sikeres telepítése után a belső végpont (`https://<clustername>-int.azurehdinsight.net`) használatával férhet hozzá a Ambari a VNET belülről. A nyilvános végpont (`https://<clustername>.azurehdinsight.net`) vagy SSH-végpont (`<clustername>-ssh.azurehdinsight.net`) használatához ellenőrizze, hogy rendelkezik-e a megfelelő útvonalakkal az útválasztási táblázatban és a NSG-szabályok beállításában, hogy elkerülje a assymetric-útválasztási probléma magyarázatát. [](https://docs.microsoft.com/azure/firewall/integrate-lb)

## <a name="configure-another-network-virtual-appliance"></a>Másik hálózati virtuális berendezés konfigurálása

>[!Important]
> A következő információkra **csak** akkor van szükség, ha nem Azure Firewall hálózati virtuális berendezést (NVA) szeretne konfigurálni.

Az előző utasítások segítséget nyújtanak Azure Firewall konfigurálásához a HDInsight-fürt kimenő forgalmának korlátozásához. A Azure Firewall automatikusan úgy van konfigurálva, hogy a gyakori fontos forgatókönyvek esetében lehetővé tegye a forgalmat. Ha másik hálózati virtuális készüléket szeretne használni, manuálisan kell konfigurálnia néhány további funkciót. Tartsa szem előtt az alábbiakat a hálózati virtuális berendezés konfigurálásakor:

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
| \*:123 | NTP órajel-ellenőrzési. A forgalom a 123-es porton több végponton van bejelölve |
| [Itt](hdinsight-management-ip-addresses.md) közzétett IP-címek | Ezek a HDInsight szolgáltatás |
| HRE – DS magánhálózati IP-címek ESP-fürtökhöz |
| \*: 16800 KMS Windows-aktiváláshoz |
| \*12000 Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-függőségek

>[!Important]
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
| azure.archive.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                |
| ocsp.digicert.com:80                                                |

## <a name="next-steps"></a>További lépések

* [Azure HDInsight virtuális hálózati architektúra](hdinsight-virtual-network-architecture.md)
