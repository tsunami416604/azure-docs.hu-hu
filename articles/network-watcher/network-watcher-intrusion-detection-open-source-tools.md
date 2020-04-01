---
title: Hálózati behatolásészlelés végrehajtása nyílt forráskódú eszközökkel
titleSuffix: Azure Network Watcher
description: Ez a cikk azt ismerteti, hogy miként használhatók az Azure Network Watcher és a nyílt forráskódú eszközök a hálózati behatolásészleléshez
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 1bd823d94552d1e920b367b6576b0e3bb74aefb2
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80474925"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Hálózati behatolásészlelés végrehajtása a Network Watcher és a nyílt forráskódú eszközök segítségével

A csomagrögzítés kulcsfontosságú összetevő a hálózati behatolásérzékelő rendszerek (IDS) megvalósításához és a hálózati biztonsági figyelés (NSM) végrehajtásához. Számos nyílt forráskódú IDS-eszköz létezik, amelyek feldolgozzák a csomagrögzítéseket, és megkeresik a lehetséges hálózati behatolások és rosszindulatú tevékenységek aláírását. A Network Watcher által biztosított csomagrögzítések segítségével elemezheti a hálózatot a káros behatolások és biztonsági rések szempontjából.

Az egyik ilyen nyílt forráskódú eszköz a Suricata, egy IDS-motor, amely szabálykészleteket használ a hálózati forgalom figyelésére, és riasztást ad ki, ha gyanús események történnek. A Suricata többszálas motort kínál, ami azt jelenti, hogy nagyobb sebességgel és hatékonysággal képes hálózati forgalomelemzést végezni. További részletek a Suricata és annak képességeit, https://suricata-ids.org/látogasson el a honlapon a .

## <a name="scenario"></a>Forgatókönyv

Ez a cikk bemutatja, hogyan állíthatja be a környezetet a hálózati behatolásészlelés hálózati figyelő, a Suricata és a rugalmas verem használatával történő végrehajtásához. A Network Watcher biztosítja a hálózati behatolásészleléshez használt csomagrögzítéseket. A Suricata feldolgozza a csomag rögzítése és riasztásait olyan csomagok alapján, amelyek megfelelnek a fenyegetések adott szabálykészletének. Ezek a riasztások a helyi számítógépen egy naplófájlban tárolódnak. A rugalmas verem használatával a Suricata által létrehozott naplók indexelhetők és használhatók kibana irányítópult létrehozásához, amely lehetővé teszi a naplók vizuális megjelenítését, és olyan eszközöket biztosít, amelyek segítségével gyorsan betekintést nyerhet a potenciális hálózati biztonsági résekbe.  

![egyszerű webes alkalmazás forgatókönyv][1]

Mindkét nyílt forráskódú eszköz beállítható egy Azure virtuális gép, amely lehetővé teszi, hogy ezt az elemzést a saját Azure hálózati környezetben.

## <a name="steps"></a>Lépések

### <a name="install-suricata"></a>Suricata telepítése

Minden más telepítési módszer esetén látogasson el ahttps://suricata.readthedocs.io/en/suricata-5.0.2/quickstart.html#installation

1. A virtuális gép parancssori terminálján futtassa a következő parancsokat:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. A telepítés ellenőrzéséhez futtassa a parancsot `suricata -h` a parancsok teljes listájának megtekintéséhez.

### <a name="download-the-emerging-threats-ruleset"></a>Az Új onnan kifelmerülő fenyegetések szabályrendszerének letöltése

Ebben a szakaszban nincsenek szabályaink, hogy Suricatát irányítsa. Létrehozhat saját szabályokat, ha a hálózatot konkrét fenyegetéseket szeretne észlelni, vagy számos szolgáltatótól, például az Új fenyegetésektől vagy a Snort VRT-szabályaitól származó fejlett szabálykészleteket is használhat. Használjuk a szabadon hozzáférhető Új fenyegetések ruleset itt:

Töltse le a szabálykészletet, és másolja őket a könyvtárba:

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Csomagrögzítés feldolgozása a Suricata segítségével

A csomagrögzítéss műveletek Suricata használatával történő feldolgozásához futtassa a következő parancsot:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Az eredményül kapott riasztások ellenőrzéséhez olvassa el a fast.log fájlt:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>A rugalmas verem beállítása

Míg a Suricata által termelt naplók értékes információkat tartalmaznak arról, hogy mi történik a hálózatunkon, ezek a naplófájlok nem a legkönnyebben olvashatók és érthetőek. A Suricata és az elasztikus verem összekapcsolásával létrehozhatunk egy Kibana irányítópultot, amely lehetővé teszi számunkra a keresést, grafikont, elemzést és a naplókból származó elemzéseket.

#### <a name="install-elasticsearch"></a>Rugalmas keresés telepítése

1. Az 5.0-s és újabb verziórugalmas verem java 8-as verzióját igényli. Futtassa `java -version` a parancsot, hogy ellenőrizze a verziót. Ha nincs telepítve java, olvassa el az [Azure által támogatott JDK-k dokumentációját.](https://aka.ms/azure-jdks)

1. Töltse le a megfelelő bináris csomagot a rendszer:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Egyéb telepítési módszerek találhatók [Elasticsearch telepítés](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Ellenőrizze, hogy az Elasticsearch fut-e a következő paranccsal:

    ```
    curl http://127.0.0.1:9200
    ```

    Meg kell látni a választ hasonló ez:

    ```
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

A Rugalmas keresés telepítésével kapcsolatos további útmutatásért olvassa el a [Telepítési](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Logstash telepítése

1. A Logstash telepítéséhez futtassa a következő parancsokat:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Ezután be kell állítanunk a Logstash-t, hogy olvasson az eve.json fájl kimenetéből. Logstash.conf fájl létrehozása a következő használatával:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Adja hozzá a következő tartalmat a fájlhoz (ellenőrizze, hogy az eve.json fájl elérési útja helyes-e):

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. Győződjön meg arról, hogy a megfelelő engedélyeket adja meg az eve.json fájlnak, hogy a Logstash be tudja adni a fájlt.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. A Logstash elindításához futtassa a következő parancsot:

    ```
    sudo /etc/init.d/logstash start
    ```

A Logstash telepítésével kapcsolatos további utasításokért tekintse meg a [hivatalos dokumentációt](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>A Kibana telepítése

1. A Kibana telepítéséhez futtassa a következő parancsokat:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. A Kibana futtatásához használja a következő parancsokat:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. A Kibana webes felületének megtekintéséhez keresse meg a`http://localhost:5601`
1. Ebben a forgatókönyvben a Suricata naplókhoz használt indexminta "logstash-*"

1. Ha távolról szeretné megtekinteni a Kibana irányítópultot, hozzon létre egy bejövő NSG-szabályt, amely lehetővé teszi az **5601-es port**elérését.

### <a name="create-a-kibana-dashboard"></a>Kibana irányítópult létrehozása

Ebben a cikkben egy minta irányítópultot biztosítottunk a riasztások ban szereplő trendek és részletek megtekintéséhez.

1. Töltse le az irányítópult-fájlt [itt](https://aka.ms/networkwatchersuricatadashboard), a vizualizációs fájlt [itt](https://aka.ms/networkwatchersuricatavisualization)és a mentett keresési fájlt [itt.](https://aka.ms/networkwatchersuricatasavedsearch)

1. A Kibana **Kezelés** lapján keresse meg a **Mentett objektumok lapot,** és importálja mindhárom fájlt. Ezután az **Irányítópult** lapon megnyithatja és betöltheti a mintairányítópultot.

Saját vizualizációkat és irányítópultokat is létrehozhat, amelyek a saját érdeklődésre számot tartó mutatókra vannak szabva. További információ a Kibana-képi megjelenítések létrehozásáról a Kibana [hivatalos dokumentációjából.](https://www.elastic.co/guide/en/kibana/current/visualize.html)

![kibana műszerfal][2]

### <a name="visualize-ids-alert-logs"></a>IDS-riasztási naplók megjelenítése

A mintairányítópult a Suricata riasztási naplók számos vizualizációját tartalmazza:

1. Figyelmeztetések geoip – egy térkép, amely bemutatja a riasztások eloszlását a származási ország/régió szerint a földrajzi elhelyezkedés alapján (ip szerint meghatározva)

    ![geo ip][3]

1. Top 10 riasztások – a 10 leggyakoribb aktivált riasztások összegzése és azok leírása. Az adott riasztásra vonatkozó információk az irányítópulton lefelé szűri az irányítópultot.

    ![4. kép][4]

1. Riasztások száma – a szabályrendszer által kiváltott riasztások teljes száma

    ![5. kép][5]

1. Top 20 Forrás/Cél IP/Port – kördiagramok, amelyek a 20 legnépszerűbb IP-t és portot mutatják, amelyeken a riasztások aktiválódtak. Szűrheti az egyes IP-k/portok szűrését, hogy hány és milyen típusú riasztások aktiválódjanak.

    ![6. kép][6]

1. Riasztás összegzése – egy táblázat, amely az egyes riasztások konkrét részleteit foglalja össze. A táblázat testreszabásával az egyes riasztások hoz egyéb fontos paramétereket jeleníthet meg.

    ![kép 7][7]

Az egyéni vizualizációk és irányítópultok létrehozásáról további dokumentációt [a Kibana hivatalos dokumentációjában](https://www.elastic.co/guide/en/kibana/current/introduction.html)talál.

## <a name="conclusion"></a>Összegzés

A Network Watcher és a nyílt forráskódú IDS-eszközök, például a Suricata által biztosított csomagrögzítések kombinálásával a fenyegetések széles köréhez hálózati behatolásészlelést végezhet. Ezek az irányítópultok lehetővé teszik a trendek és anomáliák gyors észlelését a hálózaton belül, valamint az adatokba való betekintést a riasztások, például a rosszindulatú felhasználói ügynökök vagy a sebezhető portok alapvető okainak felderítéséhez. Ezzel a kinyert adatokkal megalapozott döntéseket hozhat arról, hogyan reagáljon a hálózatra, és hogyan védje meg a hálózatot a káros behatolási kísérletektől, és hozzon létre szabályokat a hálózat ba való jövőbeli behatolások megakadályozására.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan aktiválhatja a csomagrögzítéseket a riasztások alapján, ha ellátogat [a Csomagrögzítés használata proaktív hálózati figyeléshez](network-watcher-alert-triggered-packet-capture.md) az Azure Functions funkcióval című oldalba.

Megtudhatja, hogy miként jelenítheti meg az NSG-folyamatnaplókat a Power BI-val a [Visualize NSG-folyamatok naplóinak megjelenítéséhez a Power BI-val](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png
