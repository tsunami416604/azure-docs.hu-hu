---
title: "Hajtsa végre a behatolás-észlelés hálózati nyílt forráskódú eszközök és az Azure hálózati figyelőt |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan használja az Azure hálózati figyelőt, és nyissa meg a forrás eszközök hálózati behatolás-észlelés végrehajtására"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: aff1b5f9e8860d3b8dc09b37684bb8a4ac2bf134
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Hajtsa végre a behatolás-észlelés hálózati nyílt forráskódú eszközök és hálózati figyelőt

Csomag rögzíti a végrehajtási hálózati behatolás rendszerek (Azonosítók) és a hálózati biztonsági figyelése (NSM) végrehajtása nyilvános kulcsokra épülő. Számos nyílt forráskódú Azonosítók eszköz, amely feldolgozza a csomag rögzíti, és keresse meg az esetleges hálózati behatolások és a rosszindulatú tevékenységhez van. A csomag használatával rögzíti a megadott hálózati figyelőt, a hálózati biztonsági rések vagy káros behatolások elemezheti.

Egy ilyen nyílt forráskódú eszköz Suricata, egy Azonosítók motor, amely szabálykészletek használ a hálózati forgalom figyelésére, és elindítja a riasztásokat gyanús események előfordulásakor. Suricata kínál a többszálas motor, azaz a hálózati forgalom elemzése a nagyobb sebesség és a hatékonyság műveleteket hajthat végre. Suricata és platformképességei kapcsolatos további részletekért látogasson el a https://suricata-ids.org/ webhelyen.

## <a name="scenario"></a>Forgatókönyv

Ez a cikk ismerteti, hogyan állítsa be a környezetet a hálózati figyelőt, Suricata és a rugalmas készlet hálózati behatolás-észlelés végrehajtására. Hálózati figyelőt tesz lehetővé a hálózati behatolás-észlelés végrehajtására használt csomag rögzíti. Suricata feldolgozza a csomag rögzítésekre és eseményindító riasztások csomagot, amely megfelel az adott szabálykészletben fenyegetések alapján. Ezek a riasztások a naplófájl a helyi számítógépen tárolja. A rugalmas készlet, Suricata által létrehozott naplók segítségével lehet indexelt és Kibana irányítópult, hogy a naplók és a segítségével gyorsan információt kaphat a lehetséges hálózati biztonsági veszélyek vizuális ábrázolását létrehozásához használt.  

![egyszerű webes alkalmazás forgatókönyv][1]

Mindkét nyílt forráskódú eszközök beállítható egy Azure virtuális gépen, hogy lehetővé teszi az elemzést, a saját Azure hálózati környezetben.

## <a name="steps"></a>Lépések

### <a name="install-suricata"></a>Suricata telepítése

Minden egyéb módszer telepítés esetén keresse fel a http://suricata.readthedocs.io/en/latest/install.html

1. A virtuális gép a kívánt parancssori terminált futtassa a következő parancsokat:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. A parancs futtatásával ellenőrizheti a telepítést, `suricata -h` parancsok teljes listájának megtekintéséhez.

### <a name="download-the-emerging-threats-ruleset"></a>A felmerülő veszélyek szabálykészletben letöltése

Ezen a ponton nem találtunk futtatásához Suricata szabályok. A saját szabályokat hozhat létre, ha a hálózat egyes veszélyforrások szeretné észlelni, vagy is fejlesztett használata szabálykészletek szolgáltatók, köztük a felmerülő veszélyek vagy Snort VRT szabályok több szolgáltatásból. Az ingyenesen elérhető felmerülő veszélyek szabálykészletben itt használjuk:

Töltse le a szabálykészletet, és másolja őket abba a könyvtár:

```
wget http://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Folyamat csomagrögzítéseket Suricata

Csomag rögzíti a Suricata, a következő parancsot használja:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Ellenőrizze az eredményül kapott riasztásokat, olvassa el a fast.log fájlt:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>A rugalmas készlet beállítása

Amíg a naplók Suricata előállító mi történik a hálózaton lévő értékes információkat tartalmaznak, a naplófájlok nem Elolvastam és megértettem a legkönnyebben. A rugalmas készlet Suricata összekötésével létrehozhatjuk Kibana irányítópult mi kiválaszthatjuk, hogy a keresés, diagramot, elemzése és elemzések származik a naplókat.

#### <a name="install-elasticsearch"></a>Elasticsearch telepítése

1. A rugalmas készlet 5.0-s verziójáról és a fent Java 8 igényel. Futtassa a parancsot `java -version` a verziójának. Ha nem kell telepíteni, a részletek a dokumentációban találhatók java [Oracle-webhely](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)
1. Töltse le a megfelelő bináris csomagot a rendszer:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Egyéb módszerek található [Elasticsearch telepítése](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Győződjön meg arról, hogy Elasticsearch fut-e a parancsot:

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

Rugalmas keresési telepítése további útmutatásra van szüksége, tekintse meg a lap [telepítése](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Logstash telepítése

1. A következő parancsokat Logstash telepítése:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Ezután azt kell Logstash a kimeneti eve.json fájl olvasásakor. Hozzon létre egy logstash.conf fájl használatával:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Vegye fel a következő tartalmat a fájlba (Győződjön meg róla, hogy a fájl elérési útját eve.json helyes):

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

1. Győződjön meg arról, hogy a eve.json fájl megfelelő jogosultsággal, hogy Logstash fogadására képes, a fájl.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Logstash elindításához futtassa a parancsot:

    ```
    sudo /etc/init.d/logstash start
    ```

További telepítésével kapcsolatos utasításokat Logstash, tekintse meg a [dokumentációs](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>Kibana telepítése

1. A következő parancsokat Kibana telepítéséhez:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Kibana használja parancsok futtatásához:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Lépjen a Kibana webes felület megtekintéséhez`http://localhost:5601`
1. Ebben a forgatókönyvben a index minta a Suricata használható nem "logstash-*"

1. Ha távolról Kibana irányítópultjának megjelenítése, hozzon létre egy bejövő NSG szabályt, amely engedélyezi webtartalmak elérését **port 5601**.

### <a name="create-a-kibana-dashboard"></a>Kibana irányítópult létrehozása

Ebben a cikkben adtunk egy minta-irányítópult ahhoz, hogy a riasztásokat a trendek és a részletek megtekintéséhez.

1. Az irányítópult-fájl letöltésére [Itt](https://aka.ms/networkwatchersuricatadashboard), a képi megjelenítés fájl [Itt](https://aka.ms/networkwatchersuricatavisualization), és a mentett keresés fájl [Itt](https://aka.ms/networkwatchersuricatasavedsearch).

1. A a **felügyeleti** lapon lépjen a Kibana, **objektumok mentése** mindhárom fájlt importálja. Ezután az a **irányítópult** lap megnyitásához, és a minta-irányítópult betöltése.

A saját képi megjelenítések és saját egyik fontos metrikák felé szabott irányítópultok is létrehozhat. További információk Kibana képi megjelenítés létrehozása Kibana tartozó [dokumentációs](https://www.elastic.co/guide/en/kibana/current/visualize.html).

![kibana irányítópult][2]

### <a name="visualize-ids-alert-logs"></a>Riasztási naplók Azonosítók megjelenítése

A minta-irányítópult a Suricata riasztási naplók több képi megjelenítések biztosítja:

1. A riasztások GeoIP – származási (IP által meghatározott) földrajzi helye alapján értesítések eloszlását ábrázoló térkép szerint

    ![földrajzi ip][3]

1. Felső 10 riasztások – a leggyakoribb 10 összegzését kiváltott riasztások és azok leírását. Egyéni riasztást kattintva szűrők le az irányítópulton, hogy az adott riasztásra vonatkozó információk.

    ![Kép: 4][4]

1. Riasztások – száma a ruleset által kiváltott figyelmeztetések teljes száma

    ![Kép: 5][5]

1. Felső 20 forrás vagy a cél IP-címek és portok - megjelenítő a felső 20 IP-címek és portok, hogy a riasztások tortadiagramok indított volt. Végezhet alacsonyabb a meghatározott IP-címek/portokat, hogy hány és milyen típusú riasztások aktiválódnak alatt.

    ![a kép 6][6]

1. Riasztási összegzése – egy tábla minden egyes riasztás részletes összegzése. Ez a táblázat többi paraméter egyik fontos az egyes riasztások megjelenítése testre.

    ![kép 7][7]

Egyéni képi megjelenítések és irányítópultokat hozhat létre további dokumentációiért lásd: [Kibana tartozó dokumentációs](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Összegzés

Csomag kombinálásával rögzíti a megadott hálózati figyelőt, és nyílt forráskódú Azonosítók eszközöket, például a Suricata, esetében fenyegetések számos hálózati behatolásérzékelési hajthat végre. Ezek az irányítópultok engedélyezi, hogy gyorsan direkt trendek és a hálózaton belüli rendellenességek észlelését, jól dig okait, például a rosszindulatú felhasználók ügynökök vagy sebezhető portok riasztások felderítésére adatokká. A kibontott adatok is legmegalapozottabb döntések reagálnak és megvédje a hálózatot káros behatolás kísérletek, és megakadályozza a hálózat jövőbeli behatolások szabályt hoz létre.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan indítható el, látogasson el a riasztások alapján csomag rögzítésekre [csomagrögzítéssel segítségével hajtsa végre az Azure Functions proaktív Hálózatfigyelő](network-watcher-alert-triggered-packet-capture.md)

Megtudhatja, hogyan jelenítheti meg az NSG folyamata naplók a Power BI ellátogatva [megjelenítése NSG forgalomáramlás naplók és a Power bi-ban](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png
