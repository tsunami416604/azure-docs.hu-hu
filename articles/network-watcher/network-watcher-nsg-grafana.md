---
title: NSG-folyamatnaplók kezelése a Grafana használatával
titleSuffix: Azure Network Watcher
description: A Network Watcher és a Grafana használatával kezelheti és elemezheti a hálózati biztonsági csoport folyamatnaplóit az Azure-ban.
services: network-watcher
documentationcenter: na
author: damendo
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: damendo
ms.openlocfilehash: c48d5a02cdb8ef63904642c6c2c76cb5d61e1f9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840910"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>A Hálózati biztonsági csoport folyamatnaplóinak kezelése és elemzése a Network Watcher és a Grafana használatával

[A hálózati biztonsági csoport (NSG) folyamatnaplói](network-watcher-nsg-flow-logging-overview.md) olyan információkat tartalmaznak, amelyek segítségével megismerheti a hálózati adapterek be- és kimenő IP-forgalmat. Ezek a folyamatnaplók nsg-szabályalapon jelenítik meg a kimenő és bejövő folyamatokat, a folyamatáltal alkalmazott hálózati adaptert, a folyamat 5-hangos adatait (Forrás/cél IP, Forrás/cél port, Protokoll), és ha a forgalmat engedélyezték vagy megtagadták.

> [!Warning]  
> A következő lépések az 1-es verziójú folyamatnaplókkal működnek. További információt a [Hálózati biztonsági csoportok folyamatnaplózásának bemutatása című témakörben talál.](network-watcher-nsg-flow-logging-overview.md) A következő utasítások módosítás nélkül nem működnek a naplófájlok 2-es verziójával.

A hálózatban számos NSG található, ha a folyamatnaplózás engedélyezve van. Ez az összeg a naplózási adatok megnehezíti, hogy elemezhető, és betekintést nyerhetnek a naplókat. Ez a cikk megoldást nyújt ezek az NSG-folyamatnaplók központi kezelésére a Grafana, egy nyílt forráskódú grafikus eszköz, elasticSearch, elosztott kereső- és elemzési motor és a Logstash használatával, amely egy nyílt forráskódú kiszolgálóoldali adatfeldolgozási folyamat.  

## <a name="scenario"></a>Forgatókönyv

Az NSG-folyamatnaplók engedélyezve vannak a Network Watcher használatával, és az Azure blob storage-ban tárolódnak. A Logstash plugin segítségével csatlakozhat, és feldolgozza a folyamatnaplókat a blob storage-ból, és elküldi őket az ElasticSearch-nek.  Miután a folyamatnaplók at tárolt ElasticSearch, elemezhetők, és a Grafana testreszabott irányítópultokká alakíthatók.

![NSG Hálózat Figyelő Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>A telepítés lépései

### <a name="enable-network-security-group-flow-logging"></a>Hálózati biztonsági csoport folyamatnaplózásának engedélyezése

Ebben az esetben a fiók legalább egy hálózati biztonsági csoportján engedélyezni kell a hálózati biztonsági csoport folyamatnaplózását. A hálózati biztonsági folyamatnaplók engedélyezésével kapcsolatos tudnivalókat a Következő cikk [Ismerteti a hálózati biztonsági csoportok folyamatnaplózásával című cikkben.](network-watcher-nsg-flow-logging-overview.md)

### <a name="setup-considerations"></a>Beállítási szempontok

Ebben a példában a Grafana, ElasticSearch és Logstash vannak konfigurálva egy Ubuntu 16.04 LTS Server az Azure-ban telepített. Ez a minimális beállítás mindhárom összetevő futtatásához használható – mindegyik ugyanazon a virtuális gépen fut. Ezt a beállítást csak tesztelésre és nem kritikus számítási feladatokra szabad használni. Logstash, Elasticsearch és Grafana mind úgy tervezhető, hogy egymástól függetlenül méretezhető több példányban. További információt az egyes összetevők dokumentációjában talál.

### <a name="install-logstash"></a>Logstash telepítése

A Logstash segítségével a JSON-formátumú folyamatnaplókat folyamatfüggő szintre simíthatja.

1. A Logstash telepítéséhez futtassa a következő parancsokat:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Állítsa be a Logstash-t a folyamatnaplók elemzéséhez, és küldje el őket az ElasticSearch-nek. Logstash.conf fájl létrehozása a következő használatával:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Adja hozzá a következő tartalmat a fájlhoz. Módosítsa a tárfiók nevét és a hozzáférési kulcsot, hogy az tükrözze a tárfiók adatait:

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

A megadott Logstash konfigurációs fájl három részből áll: a bemenetből, a szűrőből és a kimenetből.
A bemeneti szakasz a logstash által feldolgozott naplók bemeneti forrását jelöli – ebben az esetben egy "azureblob" bemeneti beépülő modult fogunk használni (a következő lépésekben telepítve), amely lehetővé teszi számunkra, hogy hozzáférjünk a blobstorage-ban tárolt NSG-folyamatnapló JSON-fájlokhoz. 

A szűrőszakasz ezután összeolvasztja az egyes folyamatnapló-fájlokat, így minden egyes folyamattörzs és a hozzátartozók külön Logstash-eseményté válnak.

Végül a kimeneti szakasz továbbítja az egyes Logstash-eseményeket az ElasticSearch kiszolgálóra. Nyugodtan módosítsa a Logstash konfigurációs fájlt, hogy megfeleljen az Ön egyedi igényeinek.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Az Azure Blob storage Logstash bemeneti beépülő moduljának telepítése

Ez a Logstash plugin lehetővé teszi, hogy közvetlenül hozzáférjen a folyamatnaplókak a kijelölt blob storage-fiók. A beépülő modul telepítéséhez az alapértelmezett Logstash telepítési könyvtárból (ebben az esetben a /usr/share/logstash/bin) futtassa a parancsot:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

A beépülő modulról további információt az [Azure Storage-blobok Logstash bemeneti beépülő moduljában talál.](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)

### <a name="install-elasticsearch"></a>Rugalmas keresés telepítése

Használhatja a következő parancsfájlt az ElasticSearch telepítéséhez. Az ElasticSearch telepítéséről a [Rugalmas verem című témakörben](https://www.elastic.co/guide/en/elastic-stack/current/index.html)talál.

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

### <a name="install-grafana"></a>Telepítse Grafana

A Grafana telepítéséhez és futtatásához futtassa a következő parancsokat:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

További telepítési információkért [lásd: Telepítés Debian / Ubuntu rendszerre.](https://docs.grafana.org/installation/debian/)

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Az ElasticSearch kiszolgáló hozzáadása adatforrásként

Ezután hozzá kell adnia az ElasticSearch indexet, amely folyamatnaplókat tartalmaz adatforrásként. Adatforrás hozzáadásához válassza az **Adatforrás hozzáadása lehetőséget,** és töltse ki az űrlapot a megfelelő információkkal. Ebből a konfigurációból egy példa az alábbi képernyőképen található:

![Adatforrás hozzáadása](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Irányítópult létrehozása

Most, hogy sikeresen konfigurálta a Grafana-t az NSG-folyamatnaplókat tartalmazó ElasticSearch indexből való olvasásra, létrehozhat és személyre szabhat irányítópultokat. Új irányítópult létrehozásához válassza **az Első irányítópult létrehozása**lehetőséget. A következő mintadiagram-konfiguráció nsg-szabály szerint szegmentált folyamatokat mutat:

![Irányítópult-diagram](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

A következő képernyőkép egy grafikont és egy diagramot ábrázol, amely a felső folyamatokat és azok gyakoriságát mutatja. A folyamatokat az NSG-szabály, a folyamatokat pedig döntéssel is mutatja. A Grafana nagymértékben testreszabható, ezért ajánlatos az adott figyelési igényeknek megfelelő irányítópultokat létrehozni. A következő példa egy tipikus irányítópultot mutat be:

![Irányítópult-diagram](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Összegzés

A Network Watcher és az ElasticSearch és a Grafana integrálásával mostantól kényelmes és központosított módon kezelheti és jelenítheti meg az NSG-folyamatnaplókat, valamint az egyéb adatokat. A Grafana számos más hatékony grafikus funkcióval is rendelkezik, amelyek a folyamatnaplók további kezelésére és a hálózati forgalom jobb megértésére is használhatók. Most, hogy beállított a Grafana-példány, és csatlakozott az Azure-hoz, nyugodtan folytassa az általa kínált egyéb funkciók feltárásával.

## <a name="next-steps"></a>További lépések

- További információ a [Network Watcher](network-watcher-monitoring-overview.md)használatáról.

