---
title: Azure-beli hálózati biztonsági csoport folyamatnaplóit - Graylog elemzése |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti és elemezheti a hálózati biztonsági csoport folyamatnaplóit az Azure Network Watcher és a Graylog használatával.
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
ms.date: 09/19/2017
ms.author: mareat
ms.openlocfilehash: 3030fdcec95d91b75974465ad30f707837263367
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50414777"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Kezelheti és elemezheti a hálózati biztonsági csoport folyamatnaplóit az Azure Network Watcher és a Graylog használatával

[Hálózati biztonsági csoport folyamatnaplóit](network-watcher-nsg-flow-logging-overview.md) segítségével megismerheti a bejövő és kimenő IP-forgalom az Azure hálózati adapterek információkat tartalmaznak. Forgalmi naplók megjelenítése a kimenő és bejövő forgalom az egy hálózati biztonsági csoport a szabály alapon történik, a hálózati adaptert a folyamat vonatkozik, 5-ször több információt (forrás és cél IP-cím, forrás és a cél-Port, protokoll) a folyamatot, és ha engedélyezett vagy tiltott a forgalom .

Számos hálózati biztonsági csoportok csoportforgalom naplózása engedélyezve van az a hálózat lehet. Számos hálózati biztonsági csoportok csoportforgalom naplózása engedélyezve van a teszik nehézkes elemezheti és a naplók hasznosításához. Ez a cikk a hálózati biztonsági csoport folyamatnaplóit Graylog, egy nyílt forráskódú log management és az elemző eszköz és a Logstash, egy nyílt forráskódú kiszolgálóoldali adatfeldolgozó folyamat segítségével központilag kezelheti a megoldást kínál.

## <a name="scenario"></a>Forgatókönyv

Hálózati biztonsági csoport folyamatnaplóit engedélyezve vannak a Network Watcher használatával. A folyamat naplók folyamatot az Azure blob storage. A Logstash beépülő modul csatlakoztatása és dolgozza fel a Folyamatnaplók blob storage-ból, és elküldheti azokat Graylog szolgál. A Folyamatnaplók Graylog vannak tárolva, ha azok elemezhetné és személyre szabott irányítópultokat, vizualizálja.

![Graylog munkafolyamat](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>A telepítés lépései

### <a name="enable-network-security-group-flow-logging"></a>Engedélyezze a hálózati biztonsági csoportforgalom naplózása

Ebben a forgatókönyvben a hálózati biztonsági csoportforgalom naplózása legalább egy hálózati biztonsági csoport a fiókban engedélyezni kell rendelkeznie. Engedélyezésével kapcsolatos hálózati biztonsági csoport folyamatnaplóit, tekintse meg a következő cikket [csoportforgalom naplózása a hálózati biztonsági csoportok bemutatása](network-watcher-nsg-flow-logging-overview.md).

### <a name="setting-up-graylog"></a>Graylog beállítása

Ebben a példában Graylog és a Logstash az Azure-ban üzembe helyezett egy Ubuntu 14.04-es kiszolgálón vannak konfigurálva.

- Tekintse meg a [dokumentáció](http://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) Graylog, a szükséges részletes utasításokról telepítése Ubuntu-kiszolgálóra.
- Győződjön meg arról is konfigurálhatja a Graylog webes felületén a következő, a [dokumentáció](http://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif).

Ebben a példában a minimális Graylog beállítása (vagyis) egyetlen példánya egy Graylog), de Graylog is tervezésnek, igényeinek megfelelően függően a rendszer és az éles erőforrások méretezése érdekében. Architekturális szempontok vagy egy részletes architekturális útmutató további információkért lásd: a Graylog [dokumentáció](http://docs.graylog.org/en/2.2/pages/architecture.html) és [architekturális útmutató](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture).

Graylog platform és beállítások függően többféleképpen is telepíthető. Lehetséges telepítési módszerek teljes listájáért tekintse meg a Graylog hivatalos [dokumentáció](http://docs.graylog.org/en/2.2/pages/installation.html). A Graylog kiszolgálóalkalmazás Linux-disztribúciót futtat, és előfeltételei a következők:

-  Java használata 8 vagy újabb – [Azul Azure JDK-dokumentáció](https://aka.ms/azure-jdks)
-  Rugalmas keresés 2.x (2.1.0 vagy újabb) – [Elasticsearch-dokumentáció](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  2.4-es vagy újabb – MongoDB [MongoDB-dokumentáció](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>A Logstash telepítéséhez

A flow rekord szintre JSON formátumú Folyamatnaplók simítják Logstash szolgál. A folyamat-Naplók az egybesimítás egyszerűbbé teszi a naplók rendszerezése és a Keresés a Graylog.

1. A Logstash telepítéséhez futtassa a következő parancsokat:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. A folyamat-naplók elemzése, és elküldheti azokat Graylog a Logstash konfigurálása. Hozzon létre egy Logstash.conf fájlt:

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. Adja hozzá a következő tartalmat a fájlhoz. Módosítsa a kiemelt értékeket, hogy a tárfiók részleteit:

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
A Logstash konfigurációs fájlt a megadott három részből áll: a bemeneti, szűrheti és kimenete. A bemeneti szakasz jelöli meg a naplók, amely a Logstash dolgozza fel a bemeneti forrás – ebben az esetben fog egy Azure-blogban bemeneti beépülő moduljának használata (a következő lépésben telepítve), amely lehetővé teszi számunkra, hogy a hálózati biztonsági csoport a folyamat eléréséhez jelentkezzen a blob storage-ban tárolt JSON-fájlokat.

Úgy, hogy minden egyes folyamat rekord és a hozzájuk tartozó tulajdonságok külön Logstash esemény válik a szakaszban majd minden egyes folyamat naplófájl simítja egybe.

Végül a kimeneti szakaszban továbbítja a Graylog kiszolgálóhoz Logstash eseményekhez. A konkrét igényeinek megfelelően van szüksége, a Logstash konfigurációs fájlt, szükség szerint módosíthatja.

   > [!NOTE]
   > Az előző konfigurációs fájl azt feltételezi, hogy a Graylog kiszolgáló konfigurálva van a helyi gazdagép visszacsatolási IP-cím 127.0.0.1. Ha nem, akkor ügyeljen arra, hogy a gazdagép paraméter, a kimeneti szakaszban módosítsa a megfelelő IP-címet.

A Logstash telepítéséhez további utasításokért lásd: a Logstash [dokumentáció](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>A Logstash beépülő modul az Azure blob storage-bA bemeneti telepítése

A Logstash beépülő modul lehetővé teszi, hogy közvetlenül a forgalmi naplók elérését a megadott blob storage-fiók. Az alapértelmezett Logstash telepítési könyvtárában (ez megkülönbözteti a kis /usr/share/logstash/bin) származó, a beépülő modul telepítéséhez futtassa a következő parancsot:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

A beépülő modul kapcsolatos további információkért lásd: a [dokumentáció](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Állítson be kapcsolatot a Logstashből Graylog

Most, hogy a Folyamatnaplók használatával Logstash kapcsolatot hozott létre, és állítsa be a Graylog kiszolgálót, elfogadja a bejövő naplófájlok Graylog konfigurálnia kell.

1. Keresse meg a Graylog kiszolgálói webes felület, a hozzá tartozó konfigurált URL-cím használatával. A felületen érheti el a böngészőben a irányítja `http://<graylog-server-ip>:9000/`

2. A konfiguráció lapon válassza a **rendszer** legördülő menüből a felső navigációs sáv jobb, és kattintson **bemenetek**.
   Másik lehetőségként lépjen `http://<graylog-server-ip>:9000/system/inputs`

   ![Első lépések](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. Az új beviteli indításához válassza *GELF UDP* a a **válassza ki a bemeneti** legördülő menüben, majd töltse ki az űrlapot. Graylog bővített naplóformátumban GELF jelöli. Graylog GELF formátumban dolgozza ki. A maga előnyei kapcsolatos további információkért tekintse meg a Graylog [dokumentáció](http://docs.graylog.org/en/2.2/pages/gelf.html).

   Ellenőrizze, hogy a konfigurált a Graylog kiszolgáló IP-címhez a bemeneti kötést. Az IP-címnek egyeznie kell a **gazdagép** mezőjét, a Logstash konfigurációs fájlt UDP kimenetét. Az alapértelmezett port legyen *12201*. Győződjön meg arról, a port megegyezik a **port** az UDP mezője kimeneti a Logstash konfigurációs fájlt a kijelölt.

   ![Bemenetek](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   A bemeneti indításához után meg kell jelennie meg fog jelenni a **helyi bemenetek** részben, a következő képen látható módon:

   ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Graylog üzenet bemenetek kapcsolatos további információkért tekintse meg a [dokumentáció](http://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4. Után ezek a konfigurációk volna végbemenniük, elkezdheti a Logstash forgalmi naplók a következő paranccsal beolvasott megkezdéséhez: `sudo systemctl start logstash.service`.

### <a name="search-through-graylog-messages"></a>Graylog üzenetek kereshet

Lehetővé teszi a Graylog kiszolgáló gyűjtheti az üzeneteket egy kis ideig, után is tudja az üzenetek kereshet. Az üzenetek küldését a Graylog kiszolgálóval, ellenőrizze a **bemenetek** konfigurációs lapon kattintson a "**Show fogadott üzeneteket**" gombra a GELF UDP bemeneti létrehozott. Átirányítjuk egy képernyő, amely a következő képhez hasonlóan néz ki: 

![Hisztogram](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

A kék "% {Message}" hivatkozásra kattintva kibontja a minden egyes folyamat rekord paramétereinek megjelenítéséhez üzenetet, az alábbi ábrán látható módon:

![Üzenetek](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Alapértelmezés szerint az összes üzenet mezők szerepelnek a keresés Ha nem adja meg egy meghatározott üzenet mezővel rákereshet az. Ha szeretne keresni az adott hibaüzenetek (vagyis) – a folyamat rekordokat egy meghatározott forrás IP-cím) is használhatja a Graylog search lekérdezési nyelvet, [dokumentált](http://docs.graylog.org/en/2.2/pages/queries.html)

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Elemezheti a hálózati biztonsági csoport folyamatnaplóit Graylog használatával

Most, hogy Graylog, fut, a jobb megértése érdekében a flow naplózási adatokat használhatja egyes funkcióit. Egy ilyen úgy, hogy az irányítópultok használata az adatok meghatározott nézeteket hozhat létre.

### <a name="create-a-dashboard"></a>Irányítópult létrehozása

1. A felső navigációs sávon válassza **irányítópultok** , vagy navigáljon arra `http://<graylog-server-ip>:9000/dashboards/`

2. Itt kattintson a zöld **létrehozás irányítópult** gombra, és töltse ki a rövid űrlapot a title és az irányítópulton leírása. Nyomja le az **mentése** gombra az új irányítópult létrehozásához. A következő képhez hasonló irányítópult jelenik meg:

    ![Irányítópultok](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Widgetek hozzáadása

Kattintson az irányítópult jelenik meg, de jelenleg annak üres, mivel azt még nem adott hozzá minden widget címe. Egy egyszerű és a hasznos widget hozzáadása az irányítópulthoz van **gyors értékek** diagramok, amelyek a kijelölt mező, illetve ezek az értékek listájának megjelenítéséhez.

1. Lépjen vissza a keresési eredmények a Folyamatnaplók kiválasztásával fogadó UDP bemeneti **keresési** a felső navigációs sávban.

2. Alatt a **keresési eredmény** a képernyő bal oldali panelen, keresse meg a **mezők** fülre, amely minden bejövő üzenetet a folyamat rekord a különböző mezők listája.

3. Válassza ki bármelyik kívánt paramétert, amelyben képi megjelenítése érdekében (ebben a példában az IP-adatforrás ki van jelölve). Lehetséges widgetek listájának megjelenítése a mező balra legördülő nyílra, majd jelölje ki **gyors értékek** a widget létrehozásához. Az alábbi képen hasonló kell megjelennie:

   ![Forrás IP-címe](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. Innen kiválaszthatja a **hozzáadása irányítópulthoz** a vezérlő jobb felső sarkában található gombra, és válassza ki a megfelelő irányítópultot hozzáadásához.

5. Lépjen vissza az irányítópultra, az imént hozzáadott widget megtekintéséhez.

   Egyéb widgetek hisztogramok és száma például számos adhat hozzá az irányítópulton követheti a fontos mérőszámokat, például a minta-irányítópult az alábbi képen is látható:

   ![Flowlogs irányítópult](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    További magyarázat az irányítópultokon és widgetek más típusú, tekintse meg a Graylog [dokumentáció](http://docs.graylog.org/en/2.2/pages/dashboards.html).

A Network Watcher integrációval Graylog, kezelheti és jelenítheti meg a hálózati biztonsági csoport folyamatnaplóit egy kényelmes és központosított módja most már rendelkezik. Graylog számos más hatékony funkciókat, például a Streamek és a riasztásokat, amelyek tovább kezelheti a forgalmi naplók és jobb megértése érdekében a hálózati forgalmat is használható. Most, hogy állítsa be, és Fedezze fel a többi funkció által kínált továbbra is csatlakozik az Azure-ba, nyugodtan Graylog.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan jelenítheti meg a hálózati biztonsági csoport folyamatnaplóit, ha a Power bi-JAL funkcionáló [Visualize hálózati biztonsági csoport és a Power BI forgalomnaplók](network-watcher-visualize-nsg-flow-logs-power-bi.md).
