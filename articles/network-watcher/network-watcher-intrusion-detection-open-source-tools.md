---
title: Hálózati behatolásészlelés nyílt forráskódú eszközök és az Azure Network Watcher végrehajtása |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan használhatja az Azure Network Watcher, és nyílt forráskódú eszközök hálózati behatolás felderítése végrehajtásához
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 41b039c6598334d49b9ddb0608d7ad2ae045b223
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901270"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Hajtsa végre a hálózati behatolás felderítése a Network Watcher és a nyílt forráskódú eszközökkel

Csomagrögzítés olyan kulcsfontosságú alkotóeleme a végrehajtási hálózati behatolás-észlelési rendszerek (Azonosítók), és hálózati biztonsági figyelés (NSM) végrehajtása. Nincsenek számos nyílt forráskódú Azonosítók eszközöket, amelyek csomagrögzítés feldolgozni, és keresse a lehetséges hálózati behatolásokat és kártékony tevékenységek aláírásait. A csomag használatával rögzíti a megadott hálózati figyelő, a hálózat bármely káros behatolás vagy biztonsági rések elemezheti.

Egy ilyen nyílt forráskódú eszköz Suricata, olyan Azonosítók motor, amely szabálykészletek használ a hálózati forgalom figyelésére, és értesítést küld, amikor a gyanús események történnek. Suricata kínál több szálon futó motor, ami azt jelenti, elvégezheti a hálózati forgalomelemzés nagyobb sebességet és hatékony kezeléséhez. Suricata vagy képességeivel kapcsolatos további részletekért látogasson el a saját https://suricata-ids.org/.

## <a name="scenario"></a>Forgatókönyv

Ez a cikk bemutatja, hogyan állítsa be a környezetet a Network Watcher Suricata és az Elastic Stack használatával hálózati behatolás felderítése végrehajtásához. A Network Watcher biztosít a csomagrögzítés hálózati behatolás felderítése végrehajtásához használt. A csomagrögzítés és a csomagot, amely megfelel az adott szabálykészletben fenyegetés alapján eseményindító riasztások Suricata dolgozza fel. Ezek a riasztások a naplófájl a helyi gépen vannak tárolva. Az Elastic Stack használatával, Suricata által létrehozott naplók lehetnek indexelés és a Kibana irányítópultját, így a vizuális megjelenítését, a naplók és a egy azt jelenti, hogy a hálózat biztonsági réseinek gyors betekintést létrehozásához használt.  

![egyszerű webes alkalmazás forgatókönyv][1]

Mindkét nyílt forráskódú eszközöket beállíthatja egy Azure virtuális gépen, lehetővé téve, hogy hajtsa végre az elemzést, amelyben a saját Azure hálózati környezetben.

## <a name="steps"></a>Lépések

### <a name="install-suricata"></a>Suricata telepítése

Minden más módszerrel telepítés keresse fel https://suricata.readthedocs.io/en/latest/install.html

1. A virtuális gép a parancssori terminálban futtassa a következő parancsokat:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. A telepítés ellenőrzéséhez futtassa a parancsot `suricata -h` parancsok teljes listájának megtekintéséhez.

### <a name="download-the-emerging-threats-ruleset"></a>Töltse le a veszélyforrásokkal szabálykészletben

Ezen a ponton nincs Suricata futtatásához a szabályokat. A saját szabályokat hozhat létre, ha vannak az egyes veszélyforrások a hálózathoz, amelyet észlelni szeretne, vagy is használatra kifejlesztett szabálykészletek szolgáltatók, például veszélyforrásokkal vagy Snort VRT szabályai szolgáltatásból. Itt a szabadon elérhető veszélyforrásokkal szabálykészletben használt:

Töltse le a szabálykészlet, és másolja őket abba a könyvtárba:

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Folyamat csomagrögzítéseket gyűjtsenek Suricata

Csomag rögzíti a használatával Suricata, futtassa a következő parancsot:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Ellenőrizze az így kapott riasztások, olvassa el a fast.log fájlt:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Az Elastic Stack telepítése

A naplók, amely Suricata értékes információkat tartalmaznak arról, hogy mi történik a hálózaton, miközben ezek a naplófájlok nem a legegyszerűbb értelmezése. Az Elastic Stack csatlakoztatásával Suricata, létrehozhatjuk a Kibana irányítópultját számunkra, hogy a keresés, gráf, elemzése és ki a naplók milyen tevékenységeket engedélyez.

#### <a name="install-elasticsearch"></a>Az Elasticsearch telepítése

1. Az Elastic Stacket az 5.0-s verzió vagy újabb Java 8 van szükség. Futtassa a parancsot `java -version` a verzió ellenőrzéséhez. Ha nincs telepítve, a java, tekintse meg dokumentációját a a [Azure-suppored segítségével](https://aka.ms/azure-jdks).

1. Töltse le a rendszer a megfelelő bináris csomagot:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Egyéb telepítési módszerek található [Elasticsearch telepítése](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Győződjön meg arról, hogy az Elasticsearch fut-e a paranccsal:

    ```
    curl http://127.0.0.1:9200
    ```

    Ez hasonló választ kell megjelennie:

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

A telepítést végző rugalmas keresés további útmutatásért tekintse meg az oldal [telepítése](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>A Logstash telepítéséhez

1. Futtassa az alábbi parancsokat a Logstash telepítéséhez:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. A következő ellenőriznünk kell a Logstash konfigurálása, hogy a kimeneti fájl eve.json olvasni. Hozzon létre egy logstash.conf fájlt:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Adja hozzá a következő tartalmat a fájlhoz (Győződjön meg arról, hogy helyesen szerepel-e a fájl elérési útját eve.json):

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

1. Győződjön meg arról, hogy a megfelelő engedélyekkel ahhoz, hogy a eve.json fájlt úgy, hogy a Logstash betöltheti a fájlt.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Futtassa a parancsot a Logstash indítása:

    ```
    sudo /etc/init.d/logstash start
    ```

A Logstash telepítéséhez további utasításokért tekintse meg a [jelenik meg hivatalos dokumentáció](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>Kibana telepítése

1. Futtassa a Kibana telepítése a következő parancsokat:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. A Kibana használja a parancsok futtatásával:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Megtekintheti a Kibana webes felületét, navigáljon a `http://localhost:5601`
1. A jelen esetben az index-minta a Suricata naplókhoz használt van "logstash-*"

1. Ha távolról tekintheti meg a Kibana irányítópultját szeretne, hozzon létre bejövő Hálózatibiztonságicsoport-szabály engedélyezi a hozzáférést a **port 5601**.

### <a name="create-a-kibana-dashboard"></a>A Kibana-irányítópult létrehozása

Ebben a cikkben adtunk meg egy minta-irányítópult a riasztásokat a trendek és a részletek megtekintéséhez.

1. Töltse le a irányítópult [Itt](https://aka.ms/networkwatchersuricatadashboard), a Vizualizáció fájl [Itt](https://aka.ms/networkwatchersuricatavisualization), és a mentett keresés fájl [Itt](https://aka.ms/networkwatchersuricatasavedsearch).

1. Alatt a **felügyeleti** lapon lépjen a Kibana, **objektumok mentése** mindhárom fájlt importálja. Majd a **irányítópult** lapot is megnyithatja és a minta-irányítópult betöltése.

A saját vizualizációkat, és azokra az érdekes mérőszám vizsgálatára saját személyre szabott irányítópultokat is létrehozhat. További információk a Kibana-Vizualizációk létrehozása a Kibana által [dokumentációs](https://www.elastic.co/guide/en/kibana/current/visualize.html).

![kibana irányítópultját][2]

### <a name="visualize-ids-alert-logs"></a>Azonosítók riasztási naplók megjelenítése

A minta-irányítópult több Vizualizációk Suricata riasztási naplóját biztosítja:

1. Riasztások szerint GeoIP – egy térképként ábrázolja a riasztások azok (IP-cím alapján meghatározott) földrajzi helye alapján származási ország szerint eloszlása

    ![georedundáns ip][3]

1. Az első 10 riasztások – a 10 leggyakoribb aktivált riasztások és azok leírását. Egyéni riasztást kattintva szűri le, hogy az adott riasztásra vonatkozó információk az irányítópulton.

    ![image 4][4]

1. A riasztások – számát a szabálykészletben által aktivált riasztások száma

    ![Kép: 5][5]

1. Felső 20 forrás és a cél IP-címek és portok – a tortadiagramok a legnépszerűbb 20 IP-címek és portok a riasztások megjelenítése a váltotta ki. Szűrhet az adott IP-címek és portok megtekintéséhez, hogy hány és milyen típusú riasztások le vannak aktiválása.

    ![image 6][6]

1. Riasztási összegzés – egy tábla minden egyes riasztás részletes összegzése. Ez a táblázat többi paraméter a lényeges az egyes riasztások megjelenítéséhez testre szabhatja.

    ![kép 7][7]

Az egyéni vizualizációkat és irányítópultokat hoz létre több dokumentációjáért lásd: [Kibana meg hivatalos dokumentáció](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Összegzés

Csomag kombinálásával rögzíti a megadott Network Watcher és a nyílt forráskódú Azonosítók eszközök, például a Suricata fenyegetések számos különböző hálózati behatolás felderítése hajthat végre. Ezek az irányítópultok gyorsan Észreveheti a trendeket és rendellenességeket a hálózaton belüli, az adatok számára az okait riasztásokat, például a rosszindulatú felhasználó ügynökök vagy sebezhető portok jól dig teszi lehetővé. A kinyert adatokat hogy reagálás, és megvédje a hálózatot bármely káros behatolási kísérleteket, és a hálózat jövőbeli behatolások megakadályozását szolgáló szabályok létrehozása megalapozott döntéseket hozhasson.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan aktiválja a riasztások alapján funkcionáló csomagrögzítés [proaktív hálózatfigyelés Azure Functions-függvényekkel, csomagrögzítés használata](network-watcher-alert-triggered-packet-capture.md)

Ismerje meg, hogyan jelenítheti meg az NSG-Folyamatnaplók Power BI-jal funkcionáló [megjelenítése NSG-forgalomnaplók Power BI-jal](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png
