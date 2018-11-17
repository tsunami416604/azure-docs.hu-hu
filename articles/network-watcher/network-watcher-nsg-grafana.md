---
title: Hálózati biztonsági csoport Flow naplók a Network Watcher és a Grafana használata kezelheti |} A Microsoft Docs
description: Kezelheti, és a hálózati biztonsági csoport folyamat-naplók elemzése az Azure Network Watcher és a Grafana használatával.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: mareat
ms.openlocfilehash: 9e408b45f47cb86191628916124611735f374d9e
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51819034"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Kezelheti és elemezheti a hálózati biztonsági csoportok folyamatnaplóit a Network Watcher és a Grafana használatával

[Hálózati biztonsági csoport (NSG) folyamatnaplóit](network-watcher-nsg-flow-logging-overview.md) információkkal, amelyek segítségével megismerheti a bejövő és kimenő IP-forgalom hálózati adapteren. A folyamat-naplók megjelenítése a kimenő és bejövő forgalom az egy NSG-szabály alapon történik, a hálózati Adaptert a folyamat vonatkozik, a folyamat (a forrás és cél IP-cím, forrás és a cél-Port, protokoll), 5-ször több információt, és ha a forgalom engedélyezett vagy tiltott.

> [!Warning]  
> Az alábbi lépéseket a flow 1-es naplók verzió együttműködve. További információkért lásd: [csoportforgalom naplózása a hálózati biztonsági csoportok bemutatása](network-watcher-nsg-flow-logging-overview.md). Az alábbi utasítások nem fog működni a naplófájlokat, módosítás nélküli 2. verzióban.

Számos NSG-k csoportforgalom naplózása engedélyezve van a használhat a hálózaton. A naplózási adatok mennyisége segítségével elemezheti és a naplók hasznosításához nehézkes. Ez a cikk központilag kezelheti az alábbi NSG-Folyamatnaplók grafanával, egy nyílt forráskódú eszköz, az ElasticSearch, egy elosztott keresési és elemzési motorjára és Logstash, amely egy nyílt forráskódú kiszolgálóoldali adatfeldolgozó folyamat függőségábrázolás megoldást kínál.  

## <a name="scenario"></a>Forgatókönyv

NSG-Folyamatnaplók használatával a Network Watcher engedélyezve vannak, és az Azure blob storage szolgáltatásban tárolódnak. A Logstash beépülő modul csatlakoztatása és dolgozza fel a Folyamatnaplók blob storage-ból, és elküldeni őket az Elasticsearchbe szolgál.  Miután a folyamat-naplók találhatók, az ElasticSearch, azok elemzése és személyre szabott irányítópultok a Grafana az ábrázolt.

![NSG-t Network Watcher Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Telepítési lépések

### <a name="enable-network-security-group-flow-logging"></a>Engedélyezése hálózati biztonsági csoportforgalom naplózása

Ebben a forgatókönyvben rendelkeznie kell hálózati biztonsági csoport Flow naplózás engedélyezve van a legalább egy hálózati biztonsági csoport a fiókjában. Hálózati biztonsági Folyamatnaplók engedélyezésével kapcsolatos útmutatásért tekintse meg a következő cikkben [csoportforgalom naplózása a hálózati biztonsági csoportok bemutatása](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Beállítási szempontok

Ebben a példában az ElasticSearch, Logstash és a Grafana kell beállítani egy Ubuntu 16.04 LTS Server üzembe helyezett Azure-ban. A lehető legkevesebb beállítás mindhárom összetevő futtatásához használható – azokat az összes futtató ugyanazon a virtuális Gépen. Ez a beállítás csak a használható tesztelési és nem kritikus fontosságú számítási feladatokhoz. A Logstash, az Elasticsearch és a Grafana is összes kell tervezésnek egymástól független méretezését számos példányok között. További információkért tekintse meg az egyes összetevők dokumentációját.

### <a name="install-logstash"></a>A Logstash telepítéséhez

A Logstash használatával simítja egybe a JSON formátumban Folyamatnaplók folyamat rekord szintre.

1. A Logstash telepítéséhez futtassa a következő parancsokat:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. A folyamat-naplók elemzése, és küldje el azokat az ElasticSearch, Logstash konfigurálása. Hozzon létre egy Logstash.conf fájlt:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Adja hozzá a következő tartalmat a fájlhoz. Módosítsa a tárfiók nevét és kulcsát, hogy a tárfiók részleteit:

   ```bash
    input {
      azureblob
      {
        storage_account_name => "mystorageaccount"
        storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
        container => "insights-logs-networksecuritygroupflowevent"
        codec => "json"
        # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
        # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
        file_head_bytes => 12
        file_tail_bytes => 2
        # Enable / tweak these settings when event is too big for codec to handle.
        # break_json_down_policy => "with_head_tail"
        # break_json_batch_count => 2
      }
    }
    filter {
      split { field => "[records]" }
      split { field => "[records][properties][flows]"}
      split { field => "[records][properties][flows][flows]"}
      split { field => "[records][properties][flows][flows][flowTuples]"}

      mutate {
        split => { "[records][resourceId]" => "/"}
        add_field => { "Subscription" => "%{[records][resourceId][2]}"
          "ResourceGroup" => "%{[records][resourceId][4]}"
          "NetworkSecurityGroup" => "%{[records][resourceId][8]}" 
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => "," }
        add_field => {
          "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
          "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
          "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
          "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
          "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
          "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
          "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
          "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
        }
        add_field => {
          "time" => "%{[records][time]}"
          "systemId" => "%{[records][systemId]}"
          "category" => "%{[records][category]}"
          "resourceId" => "%{[records][resourceId]}"
          "operationName" => "%{[records][operationName}}"
          "Version" => "%{[records][properties][Version}}"
          "rule" => "%{[records][properties][flows][rule]}"
          "mac" => "%{[records][properties][flows][flows][mac]}"
        }
        convert => {"unixtimestamp" => "integer"}
        convert => {"srcPort" => "integer"}
        convert => {"destPort" => "integer"}
        add_field => { "message" => "%{Message}" }        
      }
 
      date {
        match => ["unixtimestamp" , "UNIX"]
      }
    }
    output {
      stdout { codec => rubydebug }
      elasticsearch {
        hosts => "localhost"
        index => "nsg-flow-logs"
      }
    }
   ```

A Logstash konfigurációs fájlt a megadott három részből áll: a bemeneti, szűrheti és kimenete.
A bemeneti szakasz azt jelzi, hogy a bemeneti forrás dolgozza fel a Logstash – ebben az esetben fogjuk használni az "Azure BLOB" bemeneti beépülő modul (a következő lépésben telepítve), amely lehetővé teszi számunkra, hogy az NSG-t a folyamat JSON naplófájljait a blob storage-ban tárolt naplók. 

Úgy, hogy minden egyes folyamat rekord és a hozzájuk tartozó tulajdonságok külön Logstash esemény válik a szakaszban majd minden egyes folyamat naplófájl simítja egybe.

Végül a kimeneti szakaszban továbbítja az ElasticSearch-kiszolgálóhoz a Logstash eseményekhez. Nyugodtan módosítsa a Logstash konfigurációs fájlt a konkrét igényeinek.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>A Logstash bemeneti beépülő modul telepítéséhez az Azure Blob storage

A Logstash beépülő modul lehetővé teszi, hogy a Folyamatnaplók tud közvetlenül hozzáférni a kijelölt blob storage-fiókból. A plug-ről történő telepítéséhez a Logstash telepítési szereplő alapértelmezett könyvtára (ez megkülönbözteti a kis /usr/share/logstash/bin) futtassa a parancsot:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

A beépülő modul kapcsolatos további információkért lásd: [Logstash bemeneti beépülő modul az Azure Storage-blobokat](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Az ElasticSearch telepítése

A következő parancsfájlt használhatja az ElasticSearch telepítése. Az ElasticSearch telepítésével kapcsolatos információkért lásd: [Elastic Stack](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

```bash
apt-get install apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen -y
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
echo "deb https://packages.elastic.co/elasticsearch/5.x/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-5.x.list
apt-get update && apt-get install elasticsearch
sed -i s/#cluster.name:.*/cluster.name:\ grafana/ /etc/elasticsearch/elasticsearch.yml
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service
```

### <a name="install-grafana"></a>Grafana telepítése

Telepítse és futtassa a Grafana, futtassa a következő parancsokat:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

További telepítési információk: [telepítés debian / Ubuntu](http://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Az ElasticSearch-kiszolgáló hozzáadása adatforrásként

Következő lépésként hozzá kell az ElasticSearch indexet tartalmazó Folyamatnaplók adatforrásként. Egy adatforrás kiválasztásával adhat hozzá **adatforrás hozzáadása** elvégzi a szükséges adatokat az űrlap. A konfiguráció egy minta található a következő képernyőfelvételen látható:

![Adatforrás hozzáadása](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Irányítópult létrehozása

Most, hogy sikeresen konfigurálta a Grafana az ElasticSearch indexet tartalmazó NSG-Folyamatnaplók olvasni, hozhat létre és irányítópultok testreszabása. Hozzon létre egy új irányítópultot, válassza a **létrehozhatja első irányítópultját**. Az alábbi minta gráf konfiguráció Hálózatibiztonságicsoport-szabály alapján szegmentált folyamatokat mutatja be:

![Irányítópult-diagram](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

Az alábbi képernyőképen egy graph és a felső folyamatokat és gyakoriságát a diagram mutatja be. Folyamatok Hálózatibiztonságicsoport-szabály által is látható, és a folyamatok szerint döntési. Grafana az nagymértékben testre szabható, így ajánlatos, hogy az adott figyelési igényeinek jönnek létre. Az alábbi példa bemutatja egy tipikus irányítópultot:

![Irányítópult-diagram](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Összegzés

A Network Watcher és az ElasticSearch és a Grafana integrálásával, kezelése és NSG-Folyamatnaplók, valamint egyéb adatok megjelenítése egy kényelmes és központosított módja most már rendelkezik. Grafana számos egyéb hatékony adatbejegyzéssel szolgáltatás, amely tovább kezelheti a forgalmi naplók és jobb megértése érdekében a hálózati forgalmat is használható. Most, hogy állítsa be, és Fedezze fel a többi funkció által kínált továbbra is csatlakozik az Azure-ba, nyugodtan a Grafana példánnyal rendelkezik.

## <a name="next-steps"></a>További lépések

- További információ [Network Watcher](network-watcher-monitoring-overview.md).

