---
title: Nyílt forráskódú eszközök használatával az Azure Network Watcher NSG forgalmi naplók megjelenítése |} A Microsoft Docs
description: Ez az oldal ismerteti, hogyan lehet NSG forgalmi naplók megjelenítése nyílt forráskódú eszközök használatával.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
ms.assetid: e9b2dcad-4da4-4d6b-aee2-6d0afade0cb8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: mareat
ms.openlocfilehash: 10f76ea58b859611483c99c2f4accec607bc2939
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2018
ms.locfileid: "51822908"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Nyílt forráskódú eszközök használatával az Azure Network Watcher NSG forgalmi naplók megjelenítése

Hálózati biztonsági csoportok folyamatnaplóit, adja meg, amelyek segítségével adatokat bejövő és kimenő IP-forgalmat a hálózati biztonsági csoportok ismertetése. A folyamat-naplók egy szabály alapján, a hálózati Adapterhez, a folyamat vonatkozik, 5 rekord információja (forrás és a cél IP-Címek használatához forrás és a cél-Port, protokoll), és ha a forgalom engedélyezett vagy tiltott bejövő és kimenő folyamatok megjelenítése.

A folyamat-naplók manuális elemzése és következtetések levonásához nehéz lehet. Vannak azonban számos olyan nyílt forráskódú eszközöket, amelyek segítségével az adatok vizualizációjához. Ez a cikk nyújt megoldást jelenítheti meg ezeket a naplókat az Elastic Stacket, ami lehetővé teszi, hogy gyorsan index, és megjelenítheti a folyamat használatával jelentkezik be a Kibana irányítópultját.

> [!Warning]  
> Az alábbi lépéseket a flow 1-es naplók verzió együttműködve. További információkért lásd: [csoportforgalom naplózása a hálózati biztonsági csoportok bemutatása](network-watcher-nsg-flow-logging-overview.md). Az alábbi utasítások nem fog működni a naplófájlokat, módosítás nélküli 2. verzióban.

## <a name="scenario"></a>Forgatókönyv

Ebben a cikkben állítjuk be olyan megoldás, amely lehetővé teszi, hogy az Elastic Stack használatával a hálózati biztonsági csoport forgalmi naplók megjelenítése.  A bemeneti Logstash beépülő modul közvetlenül a storage blobból a Folyamatnaplók tartalmazó konfigurálva a Folyamatnaplók szerezze be. Ezután az Elastic Stack használatával, a forgalmi naplók fog kell indexelve, és az információk megjelenítése a Kibana irányítópultját létrehozásához használt.

![forgatókönyv][scenario]

## <a name="steps"></a>Lépések

### <a name="enable-network-security-group-flow-logging"></a>Engedélyezése hálózati biztonsági csoportforgalom naplózása
Ebben a forgatókönyvben rendelkeznie kell hálózati biztonsági csoport Flow naplózás engedélyezve van a legalább egy hálózati biztonsági csoport a fiókjában. Hálózati biztonsági Folyamatnaplók engedélyezésével kapcsolatos útmutatásért tekintse meg a következő cikkben [csoportforgalom naplózása a hálózati biztonsági csoportok bemutatása](network-watcher-nsg-flow-logging-overview.md).

### <a name="set-up-the-elastic-stack"></a>Az Elastic Stack telepítése
NSG-Folyamatnaplók az Elastic Stack rendelkező csatlakoztatásával létrehozhatjuk a Kibana irányítópultját számunkra, hogy a keresés, gráf, elemzése és ki a naplók milyen tevékenységeket engedélyez.

#### <a name="install-elasticsearch"></a>Az Elasticsearch telepítése

1. Az Elastic Stacket az 5.0-s verzió vagy újabb Java 8 van szükség. Futtassa a parancsot `java -version` a verzió ellenőrzéséhez. Ha nincs telepítve, a java, tekintse meg dokumentációját a a [Azure-suppored segítségével](https://aka.ms/azure-jdks).
2. Töltse le a rendszer a megfelelő bináris csomagot:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Egyéb telepítési módszerek található [Elasticsearch telepítése](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

3. Győződjön meg arról, hogy az Elasticsearch fut-e a paranccsal:

    ```bash
    curl http://127.0.0.1:9200
    ```

    Ez hasonló választ kell megjelennie:

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

A telepítést végző rugalmas keresés további útmutatásért tekintse meg [telepítési utasításokat](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>A Logstash telepítéséhez

1. Futtassa az alábbi parancsokat a Logstash telepítéséhez:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. Ezután azt kell eléréséhez, és a forgalmi naplók elemzése a Logstash konfigurálása. Hozzon létre egy logstash.conf fájlt:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Adja hozzá a fájlhoz a következő tartalommal:

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

A Logstash telepítéséhez további utasításokért tekintse meg a [dokumentációs](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>A Logstash bemeneti beépülő modul telepítéséhez az Azure blob storage-bA

A Logstash beépülő modul lehetővé teszi, hogy közvetlenül a forgalmi naplók elérését a kijelölt tárfiók. Ez a beépülő modul telepítéséhez az alapértelmezett Logstash telepítési könyvtárában (ez megkülönbözteti a kis /usr/share/logstash/bin) futtassa a parancsot:

```bash
logstash-plugin install logstash-input-azureblob
```

Futtassa a parancsot a Logstash indítása:

```bash
sudo /etc/init.d/logstash start
```

A beépülő modullal kapcsolatos további információkért tekintse meg a [dokumentáció](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Kibana telepítése

1. Futtassa a Kibana telepítése a következő parancsokat:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. A Kibana használja a parancsok futtatásával:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Megtekintheti a Kibana webes felületét, navigáljon a `http://localhost:5601`
4. A jelen esetben az index a Folyamatnaplók használt egyik "nsg-folyamat-naplók". Változtassa meg az index minta a logstash.conf fájl "kimeneti" szakaszában.
5. Ha távolról tekintheti meg a Kibana irányítópultját szeretne, hozzon létre bejövő Hálózatibiztonságicsoport-szabály engedélyezi a hozzáférést a **port 5601**.

### <a name="create-a-kibana-dashboard"></a>A Kibana-irányítópult létrehozása

Egy minta-irányítópult a riasztásokat a trendek és a részletek megtekintéséhez a következő képen látható:

![1. ábra][1]

Töltse le a [irányítópult fájl](https://aka.ms/networkwatchernsgflowlogdashboard), a [vizualizációs fájlt](https://aka.ms/networkwatchernsgflowlogvisualizations), és a [adatfájl a keresési mentett](https://aka.ms/networkwatchernsgflowlogsearch).

Alatt a **felügyeleti** lapon lépjen a Kibana, **objektumok mentése** mindhárom fájlt importálja. Majd a **irányítópult** lapot is megnyithatja és a minta-irányítópult betöltése.

A saját vizualizációkat, és azokra az érdekes mérőszám vizsgálatára saját személyre szabott irányítópultokat is létrehozhat. További információk a Kibana-Vizualizációk létrehozása a Kibana által [dokumentációs](https://www.elastic.co/guide/en/kibana/current/visualize.html).

### <a name="visualize-nsg-flow-logs"></a>NSG-forgalom naplóinak megjelenítése

A minta-irányítópult több vizualizációt, a forgalmi naplók biztosítja:

1. Folyamatok szerint határozat vagy iránya Over Time - idő sorozat diagramok megjelenítése az idő alatt folyamatok száma. Az időegység, és mindkét ezek a Vizualizációk span szerkesztheti. Folyamatok döntési szerint jeleníti meg az időarány, amíg engedélyezik vagy megtagadják a döntések, miközben irány szerinti folyamatokat a bejövő és kimenő forgalom arányát jeleníti meg. Ezeket a vizualizációkat tartalmazó idővel vizsgálja meg a forgalom trendeket, és keresse meg az összes adatforgalmi csúcsokhoz és a szokatlan mintákat.

   ![2. ábra][2]

2. A cél/forrásport – folyamatokat a tortadiagramok a megfelelő portok folyamatok áttekintését megjelenítő. Ebben a nézetben látható a leggyakrabban használt portokat. Ha a kördiagramon belül egy adott porton kattint, a rendszer kiszűri az irányítópult a többi le annak a portnak folyamatok.

   ![figure3][3]

3. Folyamatok és legkorábbi naplózási idő –-mérőszámok, rögzített folyamatok száma és a dátum a legkorábbi napló rögzített száma.

   ![4. ábra][4]

4. NSG-t és a szabály – egy oszlopdiagram, amelyen látható a forgalom megoszlása minden NSG-n belül, valamint az egyes NSG-n belül szabályok a terjesztési folyamatokat. Itt láthatja, melyik NSG-t és a szabályokat a legtöbb forgalmat generált.

   ![figure5][5]

5. Az első 10 forrás és cél IP-címek – az első 10 forrás és cél IP-címeket ábrázoló sávdiagramok. Beállíthatja, hogy több vagy kevesebb felső IP-cím megjelenítéséhez a diagramokat. Itt láthatja a leggyakrabban előforduló IP-címek, valamint a forgalom döntést (engedélyezése vagy megtagadása) kerül sor az egyes IP felé.

   ![figure6][6]

6. A folyamat rekordok – az alábbi táblázatban a következő információkat tartalmazza minden egyes folyamat rekord, valamint a megfelelő NGS és szabály.

   ![figure7][7]

Az irányítópult tetején a lekérdezés Keresősáv használatával szűrheti le az irányítópult minden olyan folyamatok, például az előfizetés-azonosító, erőforráscsoportok, szabály vagy bármely más változó a lényeges paraméter alapján. A Kibana a lekérdezések és a szűrők kapcsolatos további információkért tekintse meg a [jelenik meg hivatalos dokumentáció](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Összegzés

A hálózati biztonsági csoportok folyamatnaplóit kombinálásával az Elastic Stackkel végzett, hogy rendelkezik kapja meg hatékony, testre szabható módon vizualizálhatja a hálózati forgalmat. Ezek az irányítópultok lehetővé teszik a gyors és a hálózati forgalom, valamint a szűrő észrevételeket oszthatnak meg és vizsgálja meg a bármely esetleges rendellenességeket. Használja a Kibana, ezek az irányítópultok testre szabni, és bármilyen biztonsági, naplózási és megfelelőségi igényeinek egyedi vizualizációkat hozhat létre.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan jelenítheti meg az NSG-Folyamatnaplók Power BI-jal funkcionáló [megjelenítése NSG-forgalomnaplók Power BI-jal](network-watcher-visualize-nsg-flow-logs-power-bi.md)

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png
