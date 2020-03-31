---
title: NSG-folyamatnaplók megjelenítése - Rugalmas verem
titleSuffix: Azure Network Watcher
description: A Network Watcher és a Elastic Stack használatával kezelheti és elemezheti a hálózati biztonsági csoport folyamatnaplóit az Azure-ban.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: e567994038fb4f71ef86dc577760ecf4699a0b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840638"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Azure Network Watcher NSG-forgalomnaplók vizualizációja nyílt forráskódú eszközök használatával

A hálózati biztonsági csoport folyamatnaplói olyan információkat tartalmaznak, amelyek a hálózati biztonsági csoportok ip-forgalmának megértéséhez és kimenő forgalomhoz használható. Ezek a folyamatnaplók szabályonként jelenítik meg a kimenő és bejövő folyamatokat, a folyamathoz tartozó hálózati adaptert, a folyamat 5 függőadatait (Forrás/cél IP, Forrás/cél port, Protokoll), valamint azt, hogy a forgalmat engedélyezték vagy megtagadták-e.

Ezek a folyamatnaplók nehéz lehet manuálisan elemezni, és betekintést nyerhetnek. Számos nyílt forráskódú eszköz azonban segíthet az adatok megjelenítésében. Ez a cikk megoldást nyújt a naplók megjelenítésére a rugalmas verem használatával, amely lehetővé teszi a folyamatnaplók gyors indexelését és megjelenítését egy Kibana irányítópulton.

> [!Warning]  
> A következő lépések az 1-es verziójú folyamatnaplókkal működnek. További információt a [Hálózati biztonsági csoportok folyamatnaplózásának bemutatása című témakörben talál.](network-watcher-nsg-flow-logging-overview.md) A következő utasítások módosítás nélkül nem működnek a naplófájlok 2-es verziójával.

## <a name="scenario"></a>Forgatókönyv

Ebben a cikkben egy olyan megoldást állítunk be, amely lehetővé teszi a hálózati biztonsági csoport folyamatnaplóinak megjelenítését a rugalmas verem használatával.  A Logstash bemeneti beépülő modul a folyamatnaplókat közvetlenül a folyamatnaplók at tartalmazó tárolási blobból szerzi be a folyamatnaplókat. Ezután a rugalmas verem használatával a folyamatnaplók indexelésre kerülnek, és egy Kibana irányítópult létrehozásához lesznek használva az információk megjelenítéséhez.

![forgatókönyv][scenario]

## <a name="steps"></a>Lépések

### <a name="enable-network-security-group-flow-logging"></a>Hálózati biztonsági csoport folyamatnaplózásának engedélyezése
Ebben az esetben a fiók legalább egy hálózati biztonsági csoportján engedélyezni kell a hálózati biztonsági csoport folyamatnaplózását. A hálózati biztonsági folyamatnaplók engedélyezésével kapcsolatos tudnivalókat a Következő cikk [Ismerteti a hálózati biztonsági csoportok folyamatnaplózásával című cikkben.](network-watcher-nsg-flow-logging-overview.md)

### <a name="set-up-the-elastic-stack"></a>A rugalmas verem beállítása
Az NSG-folyamatnaplók és a rugalmas verem összekapcsolásával létrehozhatunk egy Kibana irányítópultot, amely lehetővé teszi számunkra a keresést, grafikont, elemzést és a naplókból származó elemzéseket.

#### <a name="install-elasticsearch"></a>Rugalmas keresés telepítése

1. Az 5.0-s és újabb verziórugalmas verem java 8-as verzióját igényli. Futtassa `java -version` a parancsot, hogy ellenőrizze a verziót. Ha nincs telepítve java, olvassa el az [Azure által támogatott JDK-k dokumentációját.](https://aka.ms/azure-jdks)
2. Töltse le a megfelelő bináris csomagot a rendszer:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Egyéb telepítési módszerek találhatók [Elasticsearch telepítés](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

3. Ellenőrizze, hogy az Elasticsearch fut-e a következő paranccsal:

    ```bash
    curl http://127.0.0.1:9200
    ```

    Meg kell látni a választ hasonló ez:

    ```json
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

A Rugalmas keresés telepítésével kapcsolatos további útmutatásért olvassa el a [telepítési utasításokat.](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Logstash telepítése

1. A Logstash telepítéséhez futtassa a következő parancsokat:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. Ezután konfigurálnunk kell a Logstash-ot a folyamatnaplók eléréséhez és elemzéséhez. Logstash.conf fájl létrehozása a következő használatával:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Adja hozzá a következő tartalmat a fájlhoz:

   ```
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

     mutate{
      split => { "[records][resourceId]" => "/"}
      add_field => {"Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"}
      convert => {"Subscription" => "string"}
      convert => {"ResourceGroup" => "string"}
      convert => {"NetworkSecurityGroup" => "string"}
      split => { "[records][properties][flows][flows][flowTuples]" => ","}
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
      convert => {"unixtimestamp" => "integer"}
      convert => {"srcPort" => "integer"}
      convert => {"destPort" => "integer"}        
     }

     date{
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

A Logstash telepítésével kapcsolatos további utasításokért tekintse meg a [hivatalos dokumentációt.](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Az Azure blob storage Logstash bemeneti beépülő moduljának telepítése

Ez a Logstash plugin lehetővé teszi, hogy közvetlenül hozzáférjen a folyamatnaplókhoz a kijelölt tárfiókból. A bővítmény telepítéséhez az alapértelmezett Logstash telepítési könyvtárból (ebben az esetben a /usr/share/logstash/bin) futtassa a parancsot:

```bash
logstash-plugin install logstash-input-azureblob
```

A Logstash elindításához futtassa a következő parancsot:

```bash
sudo /etc/init.d/logstash start
```

A bővítményről további információt a [dokumentációban](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)talál.

### <a name="install-kibana"></a>A Kibana telepítése

1. A Kibana telepítéséhez futtassa a következő parancsokat:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. A Kibana futtatásához használja a következő parancsokat:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. A Kibana webes felületének megtekintéséhez keresse meg a`http://localhost:5601`
4. Ebben a forgatókönyvben a folyamatnaplókhoz használt indexminta "nsg-flow-logs". Az indexmintát a logstash.conf fájl "kimenet" szakaszában módosíthatja.
5. Ha távolról szeretné megtekinteni a Kibana irányítópultot, hozzon létre egy bejövő NSG-szabályt, amely lehetővé teszi az **5601-es port**elérését.

### <a name="create-a-kibana-dashboard"></a>Kibana irányítópult létrehozása

A riasztások trendjeinek és részleteinek megtekintéséhez a mintairányítópult az alábbi képen látható:

![1. ábra][1]

Töltse le az [irányítópult-fájlt](https://aka.ms/networkwatchernsgflowlogdashboard), a [vizualizációs fájlt](https://aka.ms/networkwatchernsgflowlogvisualizations)és a [mentett keresési fájlt.](https://aka.ms/networkwatchernsgflowlogsearch)

A Kibana **Kezelés** lapján keresse meg a **Mentett objektumok lapot,** és importálja mindhárom fájlt. Ezután az **Irányítópult** lapon megnyithatja és betöltheti a mintairányítópultot.

Saját vizualizációkat és irányítópultokat is létrehozhat, amelyek a saját érdeklődésre számot tartó mutatókra vannak szabva. További információ a Kibana-képi megjelenítések létrehozásáról a Kibana [hivatalos dokumentációjából.](https://www.elastic.co/guide/en/kibana/current/visualize.html)

### <a name="visualize-nsg-flow-logs"></a>NSG-folyamatnaplók megjelenítése

A mintairányítópult a folyamatnaplók számos vizualizációját tartalmazza:

1. Folyamatok döntés/időbeli irány szerint – idősorozat-grafikonok, amelyek az időszak során a folyamatok számát mutatják. Mindkét vizualizáció időegységét és időtartamát szerkesztheti. A döntés szerint zajló folyamatok a meghozott lehetővé vagy megtagadási döntések arányát mutatják, míg a Flow-k irány szerint a bejövő és a kimenő forgalom arányát. Ezekkel a vizualizációkkal megvizsgálhatja a forgalmi trendeket az idő múlásával, és megkeresheti a tüskéket vagy a szokatlan mintákat.

   ![2. ábra][2]

2. Folyamatok cél/forrás port szerint – kördiagramok, amelyek a megfelelő portjukba irányuló folyamatok bontását mutatják. Ezzel a nézettel láthatja a leggyakrabban használt portokat. Ha a kördiagramon belül egy adott portra kattint, az irányítópult többi része az adott port folyamataira szűr.

   ![3. ábra][3]

3. A folyamatok száma és a legkorábbi naplóidő – mérőszámok, amelyek a rögzített folyamatok számát és a legkorábbi rögzített napló dátumát mutatják.

   ![4. ábra][4]

4. NSG-és szabályszerinti folyamatok – egy sávdiagram, amely bemutatja az egyes NSG-ken belüli folyamatok eloszlását, valamint a szabályok eloszlását az egyes NSG-ken belül. Itt láthatja, hogy melyik NSG és a szabályok generálták a legnagyobb forgalmat.

   ![5. ábra][5]

5. Top 10 Forrás / Cél IP - sáv diagramok mutatja a top 10 forrás és a cél IP.Top 10 Source/Destination IP - bar charts showing the top 10 source and destination IP. Ezeket a diagramokat úgy módosíthatja, hogy többé-kevésbé a legnépszerűbb IP-k jelenjenek meg. Innen láthatja a leggyakrabban előforduló IP-címeket, valamint az egyes IP-k felé hozott közlekedési döntést (lehetővé vagy megtagadva).

   ![6. ábra][6]

6. Flow Tuples – ez a táblázat az egyes folyamat-adatokat, valamint a megfelelő NGS-t és szabályt mutatja.

   ![7. ábra][7]

Az irányítópult tetején lévő lekérdezési sáv használatával szűrheti le az irányítópultot a folyamatok bármely paramétere, például az előfizetés-azonosító, az erőforráscsoportok, a szabály vagy bármely más érdekes változó alapján. A Kibana kérdéseiről és szűrőiről a hivatalos dokumentációban olvashat [bővebben.](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Összegzés

A Hálózati biztonsági csoport folyamatnaplóinak és a rugalmas verem nek a kombinálásával hatékony és testreszabható módot hoztunk létre a hálózati forgalom megjelenítésére. Ezek az irányítópultok lehetővé teszik, hogy gyorsan betekintést nyerjen és megosszon elemzéseket a hálózati forgalomról, valamint szűrje le és vizsgálja meg az esetleges rendellenességeket. A Kibana használatával testre szabhatja ezeket az irányítópultokat, és létrehozhat konkrét vizualizációkat, hogy megfeleljenek a biztonsági, naplózási és megfelelőségi igényeknek.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogy miként jelenítheti meg az NSG-folyamatnaplókat a Power BI-val a [Visualize NSG-folyamatok naplóinak megjelenítéséhez a Power BI-val](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png
