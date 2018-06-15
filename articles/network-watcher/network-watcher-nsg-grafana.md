---
title: Hálózati biztonsági csoport Flow naplók hálózati figyelőt és Grafana kezelése |} Microsoft Docs
description: Kezelése és hálózati biztonsági csoport az Azure-ban a hálózati figyelőt és Grafana Flow naplóinak elemzése.
services: network-watcher
documentationcenter: na
author: kumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.openlocfilehash: 44cf074223c88b8fa539144c0d948e68ae6cbd13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23864090"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Kezelése és hálózati figyelőt és Grafana hálózati biztonsági csoport folyamata naplóinak elemzése

[Hálózati biztonsági csoport (NSG) folyamat naplók](network-watcher-nsg-flow-logging-overview.md) megérteni a bemenő és kimenő hálózati adapterek IP-forgalmat is használható információkkal. A folyamat naplók megjelenítése kimenő és bejövő forgalom egy egy NSG-szabály alapján, a hálózati adapter a folyamat vonatkozik, a folyamat (forrás vagy a cél IP-, forrás vagy a cél Port protokoll), 5 rekordos információt, és ha a forgalom lett engedélyez vagy tilt.

NSG folyamata naplózás engedélyezve van a hálózat lehet. A naplózási adatok mennyisége nehézkes lehet elemzése és a naplók a dcu teszi. Ez a cikk a NSG folyamata naplók Grafana, egy nyílt forráskódú eszköz, ElasticSearch, egy elosztott keresés és elemzés motor és Logstash, amely egy nyílt forráskódú kiszolgálóoldali adatokat feldolgozó folyamat grafikonozás segítségével központilag kezelheti megoldást kínál.  

## <a name="scenario"></a>Forgatókönyv

NSG folyamata naplók engedélyezve vannak a hálózati figyelőt használ, és az Azure blob Storage tárolóban tárolják. Csatlakozás és a folyamat naplók blobtárolóból feldolgozni, és küldje el ElasticSearch Logstash beépülő modul segítségével.  A folyamat naplók ElasticSearch vannak tárolva, amennyiben azok elemzése és a személyre szabott irányítópultokat a Grafana ábrázolt.

![NSG hálózati figyelő Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Telepítés lépései

### <a name="enable-network-security-group-flow-logging"></a>Engedélyezze a hálózati biztonsági csoport folyamata naplózás

Ebben az esetben rendelkeznie kell hálózati biztonsági csoport Flow naplózás legalább egy hálózati biztonsági csoport a fiók engedélyezve. A hálózati biztonsági folyamata naplók engedélyezése útmutatásért tekintse meg a következő cikk [folyamata naplózási a hálózati biztonsági csoportok bemutatása](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>A telepítő kapcsolatos szempontok

Ebben a példában Grafana ElasticSearch és Logstash Azure szolgáltatásba telepített Ubuntu 16.04 LTS kiszolgálón vannak konfigurálva. A lehető legkevesebb beállítás mindhárom összetevő futtatásához használt – mindegyiken az azonos virtuális gépen. Ez a beállítás csak az használandó tesztelési és a nem kritikus munkaterhelésekhez. Logstash Elasticsearch és Grafana is összes kell tervezett méretezését sok példány között. További információ a dokumentációban a mindhárom összetevőt tartalmazzák.

### <a name="install-logstash"></a>Logstash telepítése

Logstash segítségével egybesimítására a JSON formátumú folyamata naplók folyamata rekordot szintjét.

1. Logstash telepítéséhez futtassa a következő parancsokat:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. A folyamat naplók elemzése és elküldheti azokat ElasticSearch Logstash konfigurálása. Hozzon létre egy Logstash.conf fájl használatával:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Vegye fel a következő tartalmat a fájlba. A tárfiók nevét és a hozzáférési kulcsot a tárfiókadatok megfelelően módosíthatja:

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

A megadott Logstash konfigurációs fájl három részből áll: a bemeneti, a szűrő és a kimeneti. A bemeneti szakasz meg, míg a naplófájlokat, amelyek Logstash fel fogja dolgozni – ebben az esetben fogjuk használni egy "azureblob" bemeneti beépülő modul (a következő lépésekben telepítve), amelyek lehetővé teszik számunkra az NSG folyamata JSON naplófájlok blob storage-ban tárolt eléréséhez bemeneti forrását. 

A szűrő szakasz majd simítja minden folyamat naplófájl, ekkor minden egyes folyamata rekord és a hozzájuk tartozó tulajdonságok külön Logstash esemény lesz.

Végezetül az output szakasz továbbítja a ElasticSearch kiszolgálóra Logstash eseményekhez. Nyugodtan módosítsa a Logstash konfigurációs fájlt. a konkrét igényeinek.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Telepítse a Logstash bemeneti beépülő modul az Azure Blob-tároló

A Logstash beépülő modul lehetővé teszi a folyamat naplók közvetlenül elérje a megadott blob storage-fiók. A beépülő modul, telepíthetnek Logstash telepítési szereplő alapértelmezett könyvtára (Ez esetben /usr/share/logstash/bin) futtassa a parancsot:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

A beépülő modul kapcsolatos további információkért lásd: [Logstash bemeneti beépülő modul az Azure Storage Blobs](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>ElasticSearch telepítése

A következő parancsfájl segítségével ElasticSearch telepítheti. ElasticSearch telepítésével kapcsolatos információkért lásd: [rugalmas készlet](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

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

A telepítés Grafana futtassa a következő parancsokat:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

További információkért lásd: [Debian telepítését / Ubuntu](http://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Adja hozzá a ElasticSearch kiszolgálót adatforrásként

Ezt követően kell folyamata naplókat tartalmazó adatforrásként ElasticSearch indexét. Egy adatforrás kiválasztásával adhat hozzá **adatforrás hozzáadása** és a szükséges adatokat az űrlap befejezése. Ez a konfiguráció mintát az alábbi képernyőfelvételen találhatók:

![Adatforrás hozzáadása](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Irányítópult létrehozása

Most, hogy az NSG folyamata naplókat tartalmazó ElasticSearch index olvasni Grafana sikeresen konfigurálta, hozhat létre és irányítópultokat személyre. Új irányítópult létrehozásához válassza **az első irányítópult létrehozása**. A következő diagram mintakonfiguráció NSG-szabály által szegmentált adatfolyamok jeleníti meg:

![Irányítópult-diagram](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

Az alábbi képernyőfelvételen egy grafikonon és diagramját a felső adatfolyamok és gyakoriságát mutatja be. Adatfolyamok NSG-szabály által is látható, és a folyamatok által döntési. Grafana érték nagy mértékben testre szabható, ezért ajánlott, hogy hozzon létre irányítópultokat az adott figyelési igényeinek. A következő példa bemutatja egy tipikus irányítópultot:

![Irányítópult-diagram](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Összegzés

Hálózati figyelőt integrálásával ElasticSearch és Grafana, kezelése és NSG folyamata naplókat, valamint az egyéb adatok megjelenítése egy kényelmes és központosított módja most már rendelkezik. Grafana számos egyéb is használható további a folyamat naplóinak kezeléséhez és jobb megértése érdekében a hálózati forgalom nyújtó grafikus szolgáltatásokat. Most, hogy egy Grafana példány beállításához és nyugodtan kapcsolódik az Azure, a továbbra is más funkcionalitást kínál vizsgálatát.

## <a name="next-steps"></a>Következő lépések

- További információ [hálózati figyelőt](network-watcher-monitoring-overview.md).

