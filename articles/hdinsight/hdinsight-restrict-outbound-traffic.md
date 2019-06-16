---
title: Kimenő hálózati forgalom korlátozása az Azure HDInsight-fürtök konfigurálása
description: Ismerje meg, hogyan konfigurálhatja a kimenő hálózati forgalom korlátozása az Azure HDInsight-fürtök.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/30/2019
ms.openlocfilehash: 542813e0f82a1a52142a2b82bea3fdb101fdec28
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077177"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall-preview"></a>Kimenő hálózati adatforgalmat tűzfal (előzetes verzió) használata Azure HDInsight-fürtök konfigurálása

Ez a cikk ismerteti, hogy biztonságos Azure tűzfal használata a HDInsight-fürtből származó kimenő forgalom. A következő lépések feltételezik, hogy konfigurál egy Azure-tűzfal egy meglévő fürthöz. Egy új fürtöt helyez üzembe, és egy tűzfal mögött található, a HDInsight-fürt és az alhálózati először hozza létre, és kövesse a jelen útmutató lépéseit.

## <a name="background"></a>Háttér

Az Azure HDInsight-fürtök általában helyezik üzembe a saját virtuális hálózaton. A fürt rendelkezik a megfelelő működéséhez a hálózati hozzáférést igénylő szolgáltatások a virtuális hálózaton kívüli függőségeit.

Több függőségekkel szükséges bejövő forgalmat. A bejövő kezelési forgalom nem lehet elküldeni egy tűzfal eszközön keresztül. Ezt a forgalmat a forrás-címek ismertek és közzétett [Itt](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip). Az ezt az információt a fürtök bejövő forgalmának biztonságossá tétele hálózati biztonsági csoport (NSG) szabályai is létrehozhat.

A HDInsight kimenő forgalom függőségek szinte teljesen teljes tartományneveket, amely nem rendelkezik statikus IP-címek mögöttük van definiálva. A statikus címek hiánya, az azt jelenti, hogy a hálózati biztonsági csoportok (NSG-kkel) való zárolását, így a fürthöz a kimenő forgalom nem használható. A címek elég gyakran, hogy az egyik nem szabályok alapján a jelenlegi névfeloldás beállítása és módosítása, amelyek segítségével az NSG-szabályok beállítása

A megoldás a kimenő címek védelme érdekében, hogy egy tűzfal eszköz, amely a kimenő forgalmat a tartománynevek alapján szabályozhatja. Azure-tűzfalon korlátozhatja a kimenő HTTP és HTTPS-forgalom alapján a teljes Tartománynevét a cél- vagy [FQDN címkék](https://docs.microsoft.com/azure/firewall/fqdn-tags).

## <a name="configuring-azure-firewall-with-hdinsight"></a>HDInsight Azure tűzfal konfigurálása

A lépéseket annak zárolását, így a meglévő HDInsight az Azure-tűzfalon a kimenő forgalom összegzését a következők:
1. Hozzon létre egy tűzfalat.
1. Alkalmazás szabályokat a tűzfal hozzáadása
1. Adja hozzá a hálózati szabályok a tűzfalon.
1. Útválasztási táblázat létrehozása.

### <a name="create-a-new-firewall-for-your-cluster"></a>Hozzon létre egy új tűzfalszabályt a fürt

1. Hozzon létre egy alhálózatot nevű **AzureFirewallSubnet** a virtuális hálózatban, ahol a fürt található. 
1. Hozzon létre egy új tűzfal **Test-FW01** használata a lépések [oktatóanyag: Telepítse és konfigurálja az Azure portal segítségével Azure tűzfal](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>A tűzfal konfigurálása application szabályokkal

Az alkalmazás-szabálygyűjtemény, amely lehetővé teszi, hogy küldeni és fogadni a fontos kommunikáció a fürt létrehozásához.

Válassza ki az új tűzfal **Test-FW01** az Azure Portalról. Kattintson a **szabályok** alatt **beállítások** > **alkalmazás szabálygyűjtemény** > **adja hozzá az alkalmazás szabálygyűjtemény**.

![Cím: Alkalmazás szabály gyűjtemény hozzáadása](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

Az a **adja hozzá az alkalmazás szabálygyűjtemény** képernyőn, a következő lépéseket:

1. Adja meg egy **neve**, **prioritású**, és kattintson a **engedélyezése** a a **művelet** legördülő menüben, majd írja be a következő szabályokat a **FQDN címkék szakasz** :

   | **Name (Név)** | **Forrás címe** | **Teljes tartománynév-címke** | **Megjegyzések** |
   | --- | --- | --- | --- |
   | Rule_1 | * | HDInsight és a Windows Update | HDI szolgáltatásokhoz szükséges |

1. A következő szabályok hozzáadása a **cél teljes tartománynevek szakasz** :

   | **Name (Név)** | **Forrás címe** | **Protokoll: Port** | **Cél teljes TARTOMÁNYNEVEK** | **Megjegyzések** |
   | --- | --- | --- | --- | --- |
   | Rule_2 | * | https:443 | login.windows.net | Lehetővé teszi, hogy a Windows bejelentkezési tevékenység |
   | Rule_3 | * | https:443,http:80 | <storage_account_name.blob.core.windows.net> | Ha a fürt WASB használatával, majd egy szabály hozzáadása a WASB. A csak HTTPS protokollt használó kapcsolatok ellenőrizze, hogy ["biztonságos átvitelre van szükség"](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) engedélyezve van a tárfiókon. |

1. Kattintson a **Hozzáadás**lehetőségre.

   ![Cím: Adja meg az alkalmazás szabály gyűjtemény részletei](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>Konfigurálja a tűzfalat a hálózati szabályok

A hálózati szabályok megfelelően konfigurálja a HDInsight-fürt létrehozásához.

1. Válassza ki az új tűzfal **Test-FW01** az Azure Portalról.
1. Kattintson a **szabályok** alatt **beállítások** > **szabálygyűjtemény hálózati** > **adja hozzá a hálózati szabálygyűjtemény**.
1. A a **adja hozzá a hálózati szabályok gyűjteményéhez** képernyőn írja be egy **neve**, **prioritású**, és kattintson a **engedélyezése** a a **művelet** legördülő menüben.
1. Hozza létre a következő szabályokat a **IP-címek** szakaszban:

   | **Name (Név)** | **Protocol (Protokoll)** | **Forrás címe** | **Célcím** | **Célport** | **Megjegyzések** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_1 | UDP | * | * | `123` | Időszolgáltatás |
   | Rule_2 | Bármely | * | DC_IP_Address_1, DC_IP_Address_2 | `*` | Vállalati biztonsági csomag (ESP) használ, majd vegyen fel egy hálózati IP-címek szakaszában, amely lehetővé teszi a kommunikációt az AAD-DS ESP fürtök esetén. Az IP-címeit az AAD-DS szakaszban lévő tartományvezérlőkre találhatja meg a portálon | 
   | Rule_3 | TCP | * | A Data Lake Storage-fiók IP-cím | `*` | Ha az Azure Data Lake Storage használ, majd hozzá hálózati szabályt az IP-címek szakaszban ADLS Gen1 és Gen2 SNI probléma megoldása érdekében. Ez a beállítás irányítja a forgalmat tűzfal, amelyek nagy mennyiségű adat terhelés magasabb költségeket eredményezhet, de a forgalom naplózza, és a tűzfalnaplók naplózható. A Data Lake-tárfiókra IP-címét határozza meg. Egy powershell-parancsot használhatja például `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` feloldani az FQDN IP-címet.|
   | Rule_4 | TCP | * | * | `12000` | (Nem kötelező) Ha a Log Analytics használ, hozzon létre hálózati szabályt az IP-cím szakasz a Log Analytics-munkaterülethez való kommunikáció engedélyezése. |

1. Hozza létre a következő szabályokat a **Szolgáltatáscímkék** szakaszban:

   | **Name (Név)** | **Protocol (Protokoll)** | **Forrás címe** | **Szolgáltatáscímkék** | **Célport** | **Megjegyzések** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_7 | TCP | * | * | `1433,11000-11999,14000-14999` | A Szolgáltatáscímkék szakaszban hálózati szabályt, amely lehetővé teszi, hogy jelentkezzen és naplózási SQL-forgalmat, kivéve, ha az SQL Server konfigurált Szolgáltatásvégpontok a HDInsight-alhálózatot, amely fog kerülni a tűzfalon az SQL konfigurálása. |

1. Kattintson a **Hozzáadás** a hálózati szabálygyűjtemény létrehozásának befejezéséhez.

   ![Cím: Adja meg az alkalmazás szabály gyűjtemény részletei](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>Létrehozhat és konfigurálhat egy útválasztási táblázatot

Hozzon létre egy útválasztási táblázatot az alábbi bejegyzéseket:

1. A hat a címek [szükséges HDInsight felügyeleti IP-címek listája](../hdinsight/hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) a következő ugrási típusú **Internet**:
    1. Az összes régióban összes fürt négy IP-címek
    1. Két IP-címek, amelyek az a régió, ahol a fürt létrehozása
1. Folyamatban van az Azure-tűzfal magánhálózati IP-cím a következő ugrás IP-cím 0.0.0.0/0 egy virtuális készülék útvonalat.

Például az útvonaltáblát az "USA középső Régiója", az Egyesült Államokbeli régióban létrehozott fürt konfigurálásához kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure portálra.
1. Válassza ki az Azure-tűzfalon **Test-FW01**. Másolás a **magánhálózati IP-cím** megjelenik a **áttekintése** lap. Ebben a példában használjuk egy **10.1.1.4 címét minta**
1. Hozzon létre egy új útvonaltáblát.
1. Kattintson a **útvonalak** alatt **beállítások**.
1. Kattintson a **Hozzáadás** útvonalakat az IP-cím létrehozása az alábbi táblázat a.

| Útvonal neve | Címelőtag | A következő ugrás típusa | A következő ugrás címe |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | n/a |
| 23.99.5.239 | 23.99.5.239/32 | Internet | n/a |
| 168.61.48.131 | 168.61.48.131/32 | Internet | n/a |
| 138.91.141.162 | 138.91.141.162/32 | Internet | n/a |
| 13.67.223.215 | 13.67.223.215/32 | Internet | n/a |
| 40.86.83.253 | 40.86.83.253/32 | Internet | n/a |
| 0.0.0.0 | 0.0.0.0/0 | Virtuális berendezés | 10.1.1.4 |

Az útválasztási táblázat konfigurálása:

1. Az útvonaltábla kattintva létrehozta a HDInsight-alhálózat hozzárendelése **alhálózatok** alatt **beállítások** , majd **társítása**.
1. Az a **alhálózat társítása** képernyő, válassza ki a virtuális hálózatot, amely a fürt létrejött, és a **HDInsight-alhálózat** használja a HDInsight-fürthöz.
1. Kattintson az **OK** gombra.

## <a name="edge-node-or-custom-application-traffic"></a>Edge-node vagy egyéni alkalmazás felé irányuló forgalom

A fenti lépések lehetővé teszi a fürt problémamentesen működjön. Továbbra is szeretné konfigurálni, hogy megfeleljen a releváns csomópontjain futó egyéni alkalmazásaiban, ha van ilyen.

Alkalmazásfüggőségek azonosítani lehet, és az Azure-tűzfal vagy az útvonaltáblát.

Útvonalak létre kell hozni az alkalmazás forgalmának aszimmetrikus útválasztási problémák elkerülése érdekében.

Ha az alkalmazások más függőségekkel rendelkeznek, azokat hozzá kell adni az Azure-tűzfalhoz. Lehetővé teszi a HTTP/HTTPS-forgalmat, és a hálózati szabályok minden más alkalmazás szabályok létrehozásához.

## <a name="logging"></a>Naplózás

Az Azure tűzfal a naplókat elküldheti néhány különböző tárolási rendszerek. Számára történő konfigurálásához kövesse a tűzfal, naplózás lépéseit [oktatóanyag: Azure tűzfal-naplók és metrikák figyelése](../firewall/tutorial-diagnostics.md).

Miután befejezte a a naplózás beállítása, ha a Log Analytics naplózási adatokat, például a következő lekérdezéssel letiltott forgalom tekintheti meg:

```
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Az Azure-tűzfal integrálása az Azure Monitor naplóira akkor hasznos, ha először első egy alkalmazás használata, ha Ön nem ismeri az összes alkalmazás függőségeit. További információ az Azure Monitor naplóinak [naplóadatok elemzése az Azure monitorban](../azure-monitor/log-query/log-query-overview.md)

## <a name="access-to-the-cluster"></a>Hozzáférés a fürthöz
Után sikeresen fel a tűzfal beállításának, használhatja a belső végpont (`https://<clustername>-int.azurehdinsight.net`) eléréséhez az Ambari, a virtuális hálózaton belül. A nyilvános végpont használata (`https://<clustername>.azurehdinsight.net`) vagy ssh végpont (`<clustername>-ssh.azurehdinsight.net`), ellenőrizze, hogy rendelkezik a megfelelő útvonalak az útvonaltáblában lévő és NSG-szabályok beállítása a kifejtett assymetric útválasztási probléma elkerülése érdekében [Itt](https://docs.microsoft.com/azure/firewall/integrate-lb).

## <a name="configure-another-network-virtual-appliance"></a>Egy másik virtuális hálózati berendezések konfigurálása

>[!Important]
> A következő információk **csak** szükséges, ha egy hálózati virtuális készüléket (NVA), nem Azure-tűzfalat konfigurálni szeretné.

Az előző útmutatás segít az Azure-tűzfal konfigurálása a HDInsight-fürtből származó kimenő forgalom korlátozása az. Az Azure tűzfal adatforgalom engedélyezéséhez számos fontos gyakori helyzetek konfigurálása automatikusan történik. Ha szeretne egy másik hálózati virtuális készüléket használja, manuálisan konfigurálnia a további funkciók számos kell. Vegye figyelembe, a következőket a konfigurálása a hálózati virtuális berendezések:

* A Szolgáltatásvégpontok szolgáltatásvégpont kompatibilis szolgáltatások kell konfigurálni.
* IP-cím függőségek olyan nem HTTP/Https-forgalom (TCP és UDP-forgalom).
* Az nva-n eszközt FQDN HTTP/HTTPS-végpontokat is kell elhelyezni.
* Helyettesítő karaktert tartalmazó HTTP/HTTPS-végpontok függőségekkel, eltérőek lehetnek egy minősítők száma alapján.
* Rendelje hozzá az útvonaltábla, amely a HDInsight-alhálózat létrehozása.

### <a name="service-endpoint-capable-dependencies"></a>Képes a szolgáltatásvégpont-függőségek

| **Végpont** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>IP-cím függőségek

| **Végpont** | **Részletek** |
|---|---|
| \*:123 | NTP az óra ellenőrzése. Forgalom be van jelölve, a porton 123 több végpontot |
| IP-címek közzétett [Itt](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) | Ezek a HDInsight szolgáltatás |
| AAD-Címtárszolgáltatás privát IP-címek ESP-fürtök |
| \*: Windows-aktiválás KMS 16800 |
| \*a Log Analytics 12000 |

#### <a name="fqdn-httphttps-dependencies"></a>Teljes tartománynév HTTP/HTTPS-függőségek

>[!Important]
> Az alábbi lista csupán néhány közül a legfontosabb teljes tartományneveket biztosít. A teljes listát a teljes tartománynevek konfigurálása az nva-t is igénybe [ebben a fájlban](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Végpont**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| beállításjegyzék-1.docker.io:443                                              |
| auth.docker.IO:443                                                    |
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

* [Az Azure HDInsight virtuális hálózat architektúrája](hdinsight-virtual-network-architecture.md)
