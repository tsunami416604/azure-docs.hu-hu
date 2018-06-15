---
title: Az Azure hálózati biztonsági csoport folyamata naplóinak - Graylog elemzése |} Microsoft Docs
description: Ismerje meg, hogyan kezelheti és elemezni a hálózati biztonsági csoport folyamata naplók az Azure-ban a hálózati figyelőt és Graylog.
services: network-watcher
documentationcenter: na
author: mareat
manager: vitinnan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: mareat
ms.openlocfilehash: 8d82ffa84c3d75ec3acd102a2de2bdce3718a995
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
ms.locfileid: "26639285"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Kezelése és hálózati biztonsági csoport folyamata naplók az Azure-ban a hálózati figyelőt és Graylog elemzése

[Hálózati biztonsági csoport folyamata naplók](network-watcher-nsg-flow-logging-overview.md) megérteni a bemenő és kimenő Azure hálózati illesztő IP-forgalom segítségével információkat tartalmaznak. Folyamat naplók megjelenítése kimenő és bejövő forgalom a egy egy hálózati biztonsági csoportok szabály alapján, a hálózati illesztő a folyamat vonatkozik, 5 rekordos információ (forrás vagy a cél IP-, forrás vagy a cél Port protokoll) a folyamatot, és ha a forgalom lett engedélyezett vagy megtagadott .

Számos hálózati biztonsági csoportok folyamata naplózás engedélyezve van a hálózat lehet. Folyamat naplózás engedélyezve van a több hálózati biztonsági csoportok teheti nehézkes lehet elemezni, és a naplók a megismerésében. Ez a cikk a hálózati biztonsági csoport folyamata naplók Graylog, egy nyílt forráskódú napló felügyeleti és elemző eszköz és Logstash, egy nyílt forráskódú kiszolgálóoldali adatokat feldolgozó folyamat segítségével központilag kezelheti megoldást kínál.

## <a name="scenario"></a>Forgatókönyv

Hálózati biztonsági csoport folyamata naplók engedélyezve vannak, használja a hálózati figyelőt. Attribútumfolyam naplók folyamata a, az Azure blob storage. Csatlakozás és a folyamat naplók blobtárolóból feldolgozni, és küldje el Graylog Logstash beépülő modul segítségével. A folyamat naplók Graylog vannak tárolva, amennyiben azok elemzése és a személyre szabott irányítópultokat ábrázolt.

! [Graylog munkafolyamat]] (. / media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>A telepítés lépései

### <a name="enable-network-security-group-flow-logging"></a>Hálózati biztonsági csoport folyamata naplózásának engedélyezése

Ebben az esetben a hálózati biztonsági csoport folyamata naplózási legalább egy hálózati biztonsági csoport a fiók engedélyezve kell rendelkeznie. Az engedélyezésével kapcsolatos hálózati biztonsági csoport folyamata naplók, tekintse meg a következő cikk [folyamata naplózási a hálózati biztonsági csoportok bemutatása](network-watcher-nsg-flow-logging-overview.md).

### <a name="setting-up-graylog"></a>Graylog beállítása

Ebben a példában Graylog és a Logstash kiszolgálón Ubuntu 14.04, az Azure-ban telepítve vannak konfigurálva.

- Tekintse meg a [dokumentáció](http://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) Graylog, a részletes útmutatást telepítése Ubuntu helyezik.
- Győződjön meg arról is konfigurálni a Graylog webes felülete a következő a [dokumentáció](http://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

Ez a példa a minimális Graylog telepítő (azaz egyetlen példány futhat egy Graylog), de Graylog is tervezett, attól függően, hogy a rendszer és a termelési erőforrásokon keresztüli méretezési igényeinek. Az architektúra szempontjai vagy egy mély architekturális útmutatója további információkért lásd: Graylog tartozó [dokumentáció](http://docs.graylog.org/en/2.2/pages/architecture.html) és [architekturális útmutatója](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture).

Graylog a platform és a beállítások függően többféleképpen is telepíthetők. Egy lehetséges telepítési módszerek teljes listájáért tekintse meg a Graylog tartozó hivatalos [dokumentáció](http://docs.graylog.org/en/2.2/pages/installation.html). A Graylog kiszolgálóalkalmazás Linux terjesztésekről futtatja, és a következő előfeltételek vonatkoznak:

-   Oracle 8 vagy újabb – Java használata [Oracle-dokumentáció](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)
-   Rugalmas 2.x keresése (2.1.0 vagy újabb)- [Elasticsearch dokumentáció](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-   2.4-es vagy újabb – MongoDB [MongoDB-dokumentáció](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Logstash telepítése

A JSON formátumú folyamata naplók folyamata rekordot szintjét egybesimítására Logstash szolgál. A folyamat naplók egybesimítását megkönnyíti a naplók rendszerezése és Graylog keresni.

1.  Logstash telepítéséhez futtassa a következő parancsokat:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2.  A folyamat naplók elemzése és elküldheti azokat Graylog Logstash konfigurálása. Hozzon létre egy Logstash.conf fájlt:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3.  Vegye fel a következő tartalmat a fájlba. Módosítsa a kijelölt értékek a tárfiókadatok megfelelően:

    ```
    input {
        azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "NrUZmx7pJSKaRJzvQbeiZWi5nBRWOTr7Wwr9DrvK7YtDBrADYxT1y0oEExtSlkDnGRt7qcRiZzEBCCyRYND8SxSt"
            container => "insights-logs-networksecuritygroupflowevent"
            registry_create_policy => "start_over"
            codec => "json"
            file_head_bytes => 21
            file_tail_bytes => 9
            # Possible options: `do_not_break`, `with_head_tail`, `without_head_tail`
            break_json_down_policy  => 'with_head_tail'
            break_json_batch_count => 2
            interval => 5
        }
    }
    
    filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"
    }
    
     mutate {
        split => { "[records][resourceId]" => "/"}
        add_field =>{
                    "Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"
        }
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
        udp {
            host => "127.0.0.1"
            port => 12201
        }
    }
    ```
A megadott Logstash konfigurációs fájl három részből áll: a bemeneti, a szűrő és a kimeneti. A bemeneti szakasz meg, míg a bemeneti forrás, amely Logstash feldolgozza a naplók – ebben az esetben fogjuk használni egy Azure blog bemeneti beépülő modul (a következő lépésekben telepítve), amely lehetővé teszi a hálózati biztonsági csoport folyamata eléréséhez a blob storage-ban tárolt JSON naplófájlok.

A szűrő szakasz majd simítja minden folyamat naplófájl, ekkor minden egyes folyamata rekord és a hozzájuk tartozó tulajdonságok külön Logstash esemény lesz.

Végezetül az output szakasz továbbítja a Graylog kiszolgálóra Logstash eseményekhez. A konkrét igényeinek megfelelően van szüksége, módosítsa szükség szerint a Logstash konfigurációs fájlt.

   > [!NOTE]
   > Az előző konfigurációs fájl feltételezi, hogy a Graylog kiszolgáló úgy van beállítva a helyi állomás visszacsatolási IP-címhez 127.0.0.1. Ha nem, akkor ügyeljen arra, hogy az állomás paraméter a kimeneti szakaszban módosítsa a megfelelő IP-címet.

További Logstash telepítése, lásd: a Logstash [dokumentáció](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>A beépülő modul az Azure blob Storage Logstash bemeneti telepítése

A Logstash beépülő modul lehetővé teszi a folyamat naplók közvetlenül elérje a megadott blob storage-fiók. Az alapértelmezett Logstash telepítési könyvtárból (az az eset /usr/share/logstash/bin), a beépülő modul telepítéséhez futtassa a következő parancsot:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

A beépülő modul kapcsolatos további információkért tekintse meg a [dokumentáció](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Beállítása a kapcsolat a Logstash Graylog

Most, hogy azt meghatározta a kapcsolatot a Logstash használatával folyamata a naplókat, és a Graylog kiszolgáló beállításához igazolnia kell a bejövő naplófájlok fogadásához Graylog konfigurálása.

1.  Nyissa meg a Graylog kiszolgáló webes felület használatával konfigurálta, az URL-CÍMÉT. A felület arra utasíthatja a böngészőben való hozzáféréshez`http://<graylog-server-ip>:9000/`

2.  Nyissa meg a konfigurációs lapot, jelölje be a **rendszer** legördülő menü felső navigációs sáv jobb sávot és kattintson a **bemenetek**.
    Azt is megteheti keresse meg`http://<graylog-server-ip>:9000/system/inputs`

    ![Bevezetés](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3.  Indítsa el az új bemeneti, jelölje be *GELF UDP* a a **válassza ki a bemenetben** legördülő, majd adja ki az űrlapot. GELF Graylog bővített naplóformátumban jelenti. A GELF formátum Graylog dolgozza ki. Az előnyök kapcsolatos további tudnivalókért tekintse meg a Graylog [dokumentáció](http://docs.graylog.org/en/2.2/pages/gelf.html).

    Ügyeljen arra, hogy az IP-cím Graylog kiszolgálóját a konfigurált bemeneti kötése. Az IP-címet meg kell felelnie a **állomás** mezőjét a Logstash konfigurációs fájl UDP kimenetét. Az alapértelmezett port legyen *12201*. Ellenőrizze a port megegyezik-e a **port** az UDP mezője kimeneti Logstash konfigurációs fájlban található.

    ![Bemenetek](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

    Ha a bemeneti elindításához kell megjelennie csoportban jelennek meg a **helyi bemenetek** szakaszában, az alábbi ábrán látható módon:

    ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

    Bejövő Graylog üzeneteket kapcsolatos további tudnivalókért lásd a [dokumentáció](http://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4.  Után ezek a konfigurációk végzett, megkezdheti a Logstash megkezdéséhez olvasási folyamat naplók a következő paranccsal:

    `sudo systemctl start logstash.service`

### <a name="search-through-graylog-messages"></a>Graylog üzenetekben keresése

Miután a némi időbe, a Graylog kiszolgáló gyűjtheti az üzeneteket, is az üzenetek közötti keresésre. A Graylog kiszolgálóját küldött üzenetek ellenőrzése a **bemenetek** konfigurációs lapján kattintson a "**megjelenítése fogadott üzenetek**" gombra a GELF UDP a bemeneti létrehozott. Az alábbi képen hasonló képernyő vannak irányítva: 

![Hisztogram](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

A kék "% {Message}" hivatkozásra kattintva bővíti a minden üzenet minden folyamat rekord paramétereinek megjelenítéséhez, az alábbi ábrán látható módon:

![Üzenetek](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Alapértelmezés szerint az összes üzenet mezők szerepelnek a keresést. Ha nem adja meg egy adott üzenet mezővel rákereshet az. Ha azt szeretné, hogy adott üzenetek (azaz keresése – egy meghatározott folyamata rekordokat forrás IP-címe) is használhatja a Graylog keresési lekérdezés nyelvű [dokumentált](http://docs.graylog.org/en/2.2/pages/queries.html)


## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Hálózati biztonsági csoport folyamata naplóinak használatával Graylog elemzése

Graylog azt beállítása fut, azt használja a funkciók némelyike jobb megértése érdekében a folyamat naplóadatokat. Egy ilyen úgy, hogy az adatok különleges nézeteinek létrehozása irányítópultok segítségével.

### <a name="create-a-dashboard"></a>Irányítópult létrehozása

1.  Válassza ki a felső navigációs sáv **irányítópultok** , vagy navigáljon arra`http://<graylog-server-ip>:9000/dashboards/`

2.  Kattintson a zöld **hozzon létre irányítópult** gombra, majd töltse ki a rövid űrlapot a title és az irányítópult leírása. Elérte a **mentése** az új irányítópult létrehozása gombra. Az alábbi képhez hasonló irányítópult jelenik meg:

    ![Irányítópultok](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Widgetek hozzáadása

Az irányítópult látja, de most az üres, mivel azt még nem vett bármely widgeteket a cím is kattinthat. Az irányítópult hozzáadása egy egyszerű és hasznos típus widget vannak **gyors értékek** diagramok, amelyek a kijelölt mező, és a terjesztési értékek listájának megjelenítéséhez.

1.  Lépjen vissza a keresési eredmények között, a folyamat naplók kiválasztásával fogadó UDP bemeneti **keresési** a felső navigációs sáv a.

2.  Az a **keresési eredmények** a képernyő bal oldali panelen, keresse meg a **mezők** lap, amely minden bejövő Attribútumfolyam rekordot üzenet a mezőket sorolja fel.

3.  Válassza ki a kívánt paramétert használandó megjelenítése (ebben a példában kiválasztottuk, hogy forrás IP-cím). Lehetséges widgeteket listájának megjelenítéséhez kattintson a mező a bal oldali kék legördülő nyílra, majd jelölje ki **gyors értékek** a widget létrehozásához. Az alábbi képen hasonló kell megjelennie:

    ![Forrás IP-címe](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4.  Ott, kiválaszthatja a **irányítópult hozzáadása** gombra, majd a widget jobb felső sarkában, és válassza ki a megfelelő irányítópultot hozzáadásához.

5.  Lépjen vissza az irányítópult megtekintéséhez a widgetet, az előzőekben adott hozzá.

    A fontos metrikák, például a minta-irányítópult a következő ábrán látható nyomon követéséhez irányítópult számos más widgeteket, például a hisztogram és számát is hozzáadhat:

    ![Flowlogs irányítópult](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Irányítópultok és a más típusú widgeteket további magyarázatát, tekintse meg Graylog tartozó [dokumentáció](http://docs.graylog.org/en/2.2/pages/dashboards.html).

Hálózati figyelőt és Graylog integrálásával kényelmes és központosított úgy kezelheti és jelenítheti meg a hálózati biztonsági csoport folyamata naplók most van. Graylog számos egyéb szolgáltatásokat például folyókat, és a riasztásokat, amelyek további a folyamat naplóinak kezeléséhez és jobb megértése érdekében a hálózati forgalmat is használható. Most, hogy Graylog beállítása és nyugodtan kapcsolódik az Azure, a továbbra is más funkcionalitást kínál vizsgálatát.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan jelenítheti meg a hálózati biztonsági csoport folyamata naplók a Power BI ellátogatva [Visualize hálózati biztonsági csoport forgalomáramlás naplók és a Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md).
