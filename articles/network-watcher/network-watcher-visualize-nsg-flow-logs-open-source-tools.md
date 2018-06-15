---
title: Azure hálózati figyelő NSG folyamata naplók nyílt forráskódú eszközökkel megjelenítése |} Microsoft Docs
description: Ezen a lapon NSG folyamata naplók megjelenítése nyílt forráskódú eszközök használatával ismerteti.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: e9b2dcad-4da4-4d6b-aee2-6d0afade0cb8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: f7d51352aa8411e36f4224804c90c2554d4ef9e6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
ms.locfileid: "29394170"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Azure hálózati figyelő NSG folyamata naplók nyílt forráskódú eszközökkel megjelenítése

Hálózati biztonsági csoport folyamata naplók információkkal használható IP-bemenő és kimenő forgalmat a hálózati biztonsági csoportok ismertetése. A folyamat naplók kimenő és bejövő forgalom megjelenítése / szabály alapján, a hálózati adapter a folyamat vonatkozik, a folyamat (forrás vagy a cél IP, forrás vagy a cél Port Protocol), információ 5 rekordos és ha a forgalom lett engedélyez vagy tilt.

A folyamat naplók manuális elemzése és a dcu nehéz lehet. Vannak azonban számos nyílt forráskódú eszköz, amelynek segítségével jelenítheti meg ezeket az adatokat. Ez a cikk ad meg, ezek a naplók a rugalmas készlet, amely lehetővé teszi a gyors index, és jelenítheti meg a folyamat használatával megjelenítheti az adott megoldás Kibana irányítópult bejelentkezik.

## <a name="scenario"></a>Forgatókönyv

Ebben a cikkben egy megoldást, amely lehetővé teszi a hálózati biztonsági csoport folyamata naplók a rugalmas készlet használatával megjelenítheti üzembe helyezünk.  Egy Logstash bemeneti beépülő modul beszerzi a folyamat naplók közvetlenül a tárolási blob, a folyamat naplókat tartalmazó konfigurálva. Ezt követően a rugalmas készlet használatával, a folyamat naplókat fog kell indexelt és Kibana irányítópulton jelenítheti meg az információkat létrehozásához használt.

![forgatókönyv][scenario]

## <a name="steps"></a>Lépések

### <a name="enable-network-security-group-flow-logging"></a>Engedélyezze a hálózati biztonsági csoport folyamata naplózás
Ebben az esetben rendelkeznie kell hálózati biztonsági csoport Flow naplózás legalább egy hálózati biztonsági csoport a fiók engedélyezve. A hálózati biztonsági folyamata naplók engedélyezése útmutatásért tekintse meg a következő cikk [folyamata naplózási a hálózati biztonsági csoportok bemutatása](network-watcher-nsg-flow-logging-overview.md).


### <a name="set-up-the-elastic-stack"></a>A rugalmas készlet beállítása
A rugalmas készlet NSG folyamata naplók összekötésével létrehozhatjuk Kibana irányítópult mi kiválaszthatjuk, hogy a keresés, diagramot, elemzése és elemzések származik a naplókat.

#### <a name="install-elasticsearch"></a>Elasticsearch telepítése

1. A rugalmas készlet 5.0-s verziójáról és a fent Java 8 igényel. Futtassa a parancsot `java -version` a verziójának. Ha nem kell telepíteni, a részletek a dokumentációban találhatók java [Oracle-webhely](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)
1. Töltse le a megfelelő bináris csomagot a rendszer:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Egyéb módszerek található [Elasticsearch telepítése](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Győződjön meg arról, hogy Elasticsearch fut-e a parancsot:

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

Rugalmas keresési telepítése további útmutatásra van szüksége, tekintse meg a lap [telepítése](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Logstash telepítése

1. A következő parancsokat Logstash telepítése:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Ezután azt kell Logstash eléréséhez, és a folyamat naplók elemzése. Hozzon létre egy logstash.conf fájl használatával:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Vegye fel a következő tartalmat a fájlba:

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

További telepítésével kapcsolatos utasításokat Logstash, tekintse meg a [dokumentációs](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>A Logstash bemeneti beépülő modul az Azure blob Storage telepítése

A Logstash beépülő modul lehetővé teszi a kijelölt tárfiókkal közvetlenül elérje a folyamat naplók. A beépülő modul telepítéséhez az alapértelmezett Logstash telepítési könyvtárában (Ez esetben /usr/share/logstash/bin) futtassa a parancsot:

```bash
logstash-plugin install logstash-input-azureblob
```

Logstash elindításához futtassa a parancsot:

```bash
sudo /etc/init.d/logstash start
```

A beépülő modul kapcsolatos további információkért tekintse meg a dokumentációját [Itt](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)

### <a name="install-kibana"></a>Kibana telepítése

1. A következő parancsokat Kibana telepítéséhez:

  ```bash
  curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
  tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
  ```

1. Kibana használja parancsok futtatásához:

  ```bash
  cd kibana-5.2.0-linux-x86_64/
  ./bin/kibana
  ```

1. Lépjen a Kibana webes felület megtekintéséhez `http://localhost:5601`
1. Ebben a forgatókönyvben az index a folyamat használható a mintája "nsg-adatfolyam-logs". Változtassa meg az index minta a logstash.conf fájl a "kimeneti" szakaszában.

1. Ha távolról Kibana irányítópultjának megjelenítése, hozzon létre egy bejövő NSG szabályt, amely engedélyezi webtartalmak elérését **port 5601**.

### <a name="create-a-kibana-dashboard"></a>Kibana irányítópult létrehozása

Ebben a cikkben adtunk egy minta-irányítópult ahhoz, hogy a riasztásokat a trendek és a részletek megtekintéséhez.

![1. ábra][1]

1. Az irányítópult-fájl letöltésére [Itt](https://aka.ms/networkwatchernsgflowlogdashboard), a képi megjelenítés fájl [Itt](https://aka.ms/networkwatchernsgflowlogvisualizations), és a mentett keresés fájl [Itt](https://aka.ms/networkwatchernsgflowlogsearch).

1. A a **felügyeleti** lapon lépjen a Kibana, **objektumok mentése** mindhárom fájlt importálja. Ezután az a **irányítópult** lap megnyitásához, és a minta-irányítópult betöltése.

A saját képi megjelenítések és saját egyik fontos metrikák felé szabott irányítópultok is létrehozhat. További információk Kibana képi megjelenítés létrehozása Kibana tartozó [dokumentációs](https://www.elastic.co/guide/en/kibana/current/visualize.html).

### <a name="visualize-nsg-flow-logs"></a>NSG folyamata naplók megjelenítése

A minta-irányítópult a folyamat naplók több képi megjelenítések biztosítja:

1. Döntési és irányok keresztül időpontjára - adatfolyamok a azokról az az idő alatt adatfolyamok idő adatsorozat diagramjait. Időegység, és mindkét alábbi képi megjelenítést span szerkesztheti. Döntési által adatfolyamok arányát jeleníti meg, engedélyezése vagy megtagadása döntések, amíg a forgalom iránya által a bejövő és kimenő forgalom arányát jeleníti meg. Ezek a látványelemek vizsgálja meg a forgalmi adott idő alatt, és bármely igényeiben jelentkező vagy szokatlan mintákat keressen.

  ![2. ábra][2]

1. Cél/forrásport – által adatfolyamok a tortadiagramok megjelenítő bontásban tartalmazza a megfelelő portokhoz forgalom. Az ebben a nézetben láthatja a leggyakrabban használt portok. Ha egy adott portot a kördiagram belül kattint, a többi irányítópult le, hogy a port adatfolyamok szűrheti.

  ![figure3][3]

1. Több adatfolyamok és legkorábbi napló időben – adatfolyamok rögzített száma és a legkorábbi naplóban rögzített dátumának megjelenítése.

  ![4. ábra][4]

1. Adatfolyamok NSG-t és a szabály – egy oszlopdiagramot belül minden NSG forgalom eloszlását, valamint belüli egyes NSG-szabályok eloszlását jeleníti meg. Itt láthatja, melyik NSG-t és a szabályok jönnek létre a legtöbb forgalmat.

  ![figure5][5]

1. Felső 10 forrás vagy a cél IP-címek – sávdiagramok első 10 forrás és cél IP-címek. Beállíthatja, hogy több vagy kevesebb felső IP-cím megjelenítése a diagramokat. Itt láthatja a leggyakrabban előforduló IP-címek, valamint a forgalom döntési (engedélyezése vagy megtagadása) végrehajtott minden IP felé.

  ![figure6][6]

1. Folyamat rekordokat – ezt a táblázatot jeleníti meg az adatokat belül minden folyamat rekordot, valamint a megfelelő NGS és szabály található.

  ![figure7][7]

A lekérdezés sáv segítségével az irányítópult tetején, szűrheti az adatfolyamok, például az előfizetés-azonosító, erőforráscsoport-sablonok, szabály vagy bármely más változó érdeklő bármely paramétere alapján irányítópult le. További információ a Kibana a lekérdezések és a szűrők, tekintse meg a [dokumentációs](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html)

## <a name="conclusion"></a>Összegzés

A hálózati biztonsági csoport folyamata naplók és a rugalmas készlet együttes, azt kell elérni jelenítheti meg a hálózati forgalom hatékony és testre szabható módszert. Ezek az irányítópultok engedélyezi, hogy gyorsan kapnak, és a hálózati forgalom, valamint a szűrő észrevételeket oszthatnak meg, és vizsgálja meg az összes esetleges rendellenességeket. Kibana használ, ezek az irányítópultok testre szabni, és adott képi megjelenítéseket készíthet, a biztonsági, naplózási és megfelelőségi igényeinek.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan jelenítheti meg az NSG folyamata naplók a Power BI ellátogatva [megjelenítése NSG forgalomáramlás naplók és a Power bi-ban](network-watcher-visualize-nsg-flow-logs-power-bi.md)


<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png
