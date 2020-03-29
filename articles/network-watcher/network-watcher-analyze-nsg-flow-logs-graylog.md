---
title: Az Azure hálózati biztonsági csoport folyamatnaplóinak elemzése - Graylog | Microsoft dokumentumok
description: Ismerje meg, hogyan kezelheti és elemezheti a hálózati biztonsági csoport folyamatnaplóit az Azure-ban a Network Watcher és a Graylog használatával.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: damendo
ms.openlocfilehash: 1e597a81967a8fb6be2959d53e65ad01135e5e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842903"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Hálózati biztonsági csoportfolyamat-naplók kezelése és elemzése az Azure-ban a Network Watcher és a Graylog használatával

[A hálózati biztonsági csoport folyamatnaplói](network-watcher-nsg-flow-logging-overview.md) olyan információkat tartalmaznak, amelyek segítségével megismerheti az Azure hálózati csatolói nak be- és kimenő IP-forgalmat. A folyamatnaplók hálózati biztonsági csoportszabály alapján jelenítik meg a kimenő és bejövő folyamatokat, a folyamat által alkalmazott hálózati illesztést, az 5-hangú információkat (Forrás/cél IP, Forrás/cél port, Protokoll) a folyamatról, és ha a forgalmat engedélyezték vagy megtagadták.

A hálózatban számos hálózati biztonsági csoport található, ha a folyamatnaplózás engedélyezve van. Több hálózati biztonsági csoportok folyamatnaplózás engedélyezve lehet, hogy nehézkes elemezni, és betekintést nyerhetnek a naplókat. Ez a cikk megoldást nyújt a hálózati biztonsági csoport folyamatnaplóinak központi kezelésére a Graylog, egy nyílt forráskódú naplókezelő és -elemző eszköz, valamint a Logstash, egy nyílt forráskódú kiszolgálóoldali adatfeldolgozási folyamat használatával.

> [!Warning]
> A következő lépések az 1-es verziójú folyamatnaplókkal működnek. További információt a [Hálózati biztonsági csoportok folyamatnaplózásának bemutatása című témakörben talál.](network-watcher-nsg-flow-logging-overview.md) A következő utasítások módosítás nélkül nem működnek a naplófájlok 2-es verziójával.

## <a name="scenario"></a>Forgatókönyv

A hálózati biztonsági csoport folyamatnaplói engedélyezve vannak a Hálózatfigyelő használatával. A folyamatnaplók beáramlanak az Azure blob storage-ba. A Logstash plugin segítségével csatlakozhat, és feldolgozza a folyamatnaplókat a blob storage-ból, és elküldi őket a Graylognak. Miután a folyamatnaplók at graylog tárolja, elemezhetők és testre szabott irányítópultokká jeleníthetők.

![Graylog munkafolyamat](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Telepítési lépések

### <a name="enable-network-security-group-flow-logging"></a>Hálózati biztonsági csoport folyamatnaplózásának engedélyezése

Ebben az esetben a fiók legalább egy hálózati biztonsági csoportján engedélyezni kell a hálózati biztonsági csoport folyamatnaplózását. A hálózati biztonsági csoport folyamatnaplóinak engedélyezésével kapcsolatos tudnivalókat a Következő cikk [A hálózati biztonsági csoportok folyamatnaplózása című cikkismerteti.](network-watcher-nsg-flow-logging-overview.md)

### <a name="setting-up-graylog"></a>A Szürkenapló beállítása

Ebben a példában a Graylog és a Logstash egy Ubuntu 14.04-es kiszolgálón van konfigurálva, amely et az Azure-ban telepítettek.

- Tekintse meg a [Dokumentációt](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) Graylog, lépésről lépésre, hogyan telepíthető az Ubuntu.
- Győződjön meg arról is, hogy konfigurálja a Graylog webes felületet a [dokumentáció](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif)t követve.

Ez a példa a graylog minimális beállítást használja (azaz a Graylog egyetlen példánya), de a Graylog a rendszertől és a termelési igényektől függően az erőforrások közötti skálázásra is tervezhető. Az építészeti szempontokról vagy egy mély építészeti útmutatóról a Graylog [dokumentációs](https://docs.graylog.org/en/2.2/pages/architecture.html) és építészeti útmutatójában olvashat [bővebben.](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture)

Graylog lehet telepíteni sokféleképpen, attól függően, hogy a platform és a preferenciák. A lehetséges telepítési módszerek teljes listáját a Graylog hivatalos [dokumentációjában](https://docs.graylog.org/en/2.2/pages/installation.html)találja. A Graylog kiszolgálóalkalmazás Linux disztribúciókon fut, és a következő előfeltételekkel rendelkezik:

-  Java SE 8 vagy újabb – [Azul Azure JDK dokumentáció](https://aka.ms/azure-jdks)
-  Rugalmas keresés 2.x (2.1.0 vagy újabb) - [Elasticsearch telepítési dokumentáció](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2.4 vagy újabb – [MongoDB telepítési dokumentáció](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Logstash telepítése

A logstash a JSON-formázott folyamatnaplók folyamati tuple-szintre simítására szolgál. A folyamatnaplók összeolvasztása megkönnyíti a naplók rendszerezését és keresését a Graylogban.

1. A Logstash telepítéséhez futtassa a következő parancsokat:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. Állítsa be a Logstash-t a folyamatnaplók elemzéséhez és a Graylognak való elküldéséhez. Logstash.conf fájl létrehozása:

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. Adja hozzá a következő tartalmat a fájlhoz. Módosítsa a kiemelt értékeket, hogy azok tükrözzék a tárfiók adatait:

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
   A megadott Logstash konfigurációs fájl három részből áll: a bemenetből, a szűrőből és a kimenetből. A bemeneti szakasz a logstash által feldolgozandó naplók bemeneti forrását jelöli – ebben az esetben egy Azure-blog bemeneti beépülő modult fog használni (a következő lépésekben telepítve), amely lehetővé teszi számunkra a hálózati biztonsági csoport folyamatnaplójának JSON-fájljainak elérését a blobstorage-ban.

A szűrőszakasz ezután összeolvasztja az egyes folyamatnapló-fájlokat, így minden egyes folyamattörzs és a hozzátartozók külön Logstash-eseményté válnak.

Végül a kimeneti szakasz továbbítja az egyes Logstash-eseményeket a Graylog-kiszolgálónak. Az Ön igényeinek megfelelően szükség szerint módosítsa a Logstash konfigurációs fájlt.

   > [!NOTE]
   > Az előző konfigurációs fájl feltételezi, hogy a Graylog kiszolgáló a 127.0.0.1 helyi állomás visszacsatolási IP-címén van konfigurálva. Ha nem, mindenképpen módosítsa a kimeneti szakaszban lévő állomásparamétert a megfelelő IP-címre.

A Logstash telepítésével kapcsolatos további tudnivalókat a Logstash [dokumentációjában](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)találja.

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Az Azure blob storage Logstash bemeneti beépülő moduljának telepítése

A Logstash beépülő modul lehetővé teszi, hogy közvetlenül hozzáférjen a folyamatnaplókhoz a kijelölt blobtár-fiókból. A beépülő modul telepítéséhez az alapértelmezett Logstash telepítési könyvtárból (ebben az esetben a /usr/share/logstash/bin) futtassa a következő parancsot:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

A beépülő modulról további információt a [dokumentációban](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)talál.

### <a name="set-up-connection-from-logstash-to-graylog"></a>Kapcsolat beállítása a Logstash és a Graylog között

Most, hogy létrehozott egy kapcsolatot a folyamatnaplók segítségével Logstash és állítsa be a Graylog kiszolgáló, be kell állítania Graylog, hogy elfogadja a bejövő naplófájlokat.

1. Keresse meg a Graylog Server webes felületét a hozzá beállított URL-cím használatával. A felületet úgy érheti el, hogy a böngészőt a`http://<graylog-server-ip>:9000/`

2. A konfigurációs lapra való ugráshoz válassza a jobb felső navigációs sáv **Rendszer** legördülő menüjét, majd kattintson a **Bemenetek parancsra.**
   Másik lehetőségként keresse meg a`http://<graylog-server-ip>:9000/system/inputs`

   ![Első lépések](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. Az új bevitel elindításához válassza a *GELF UDP* lehetőséget a **Bevitel kiválasztása** legördülő menüben, majd töltse ki az űrlapot. A GELF a Graylog Extended Log Format rövidítése. A GELF formátumot a Graylog fejlesztette ki. További információ előnyeiről a Graylog [dokumentációjában](https://docs.graylog.org/en/2.2/pages/gelf.html)olvashat bővebben.

   Győződjön meg arról, hogy a bemenetet ahhoz az IP-címhez köti, amelyen a Graylog kiszolgálót konfigurálta. Az IP-címnek **host** meg kell egyeznie a Logstash konfigurációs fájl UDP-kimenetének állomásmezőjével. Az alapértelmezett port nak *12201-nek*kell lennie. Győződjön meg **port** arról, hogy a port megegyezik a Logstash konfigurációs fájlban kijelölt UDP-kimenet portmezőjével.

   ![Bemenetek](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   Miután elindította a bemenetet, látnia kell, hogy megjelenik a **Helyi bemenetek** szakaszban, ahogy az az alábbi képen látható:

   ![](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Ha többet szeretne megtudni a Graylog üzenetbevitelről, olvassa el a [dokumentációt.](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs)

4. Miután ezek a konfigurációk elkészültek, a Logstash indításával a következő `sudo systemctl start logstash.service`paranccsal kezdheti meg az olvasást a folyamatnaplókban: .

### <a name="search-through-graylog-messages"></a>Keresés a Graylog üzenetekben

Miután egy kis időt a Graylog szerver gyűjteni üzeneteket, akkor tudja keresni az üzeneteket. A Graylog kiszolgálónak küldött üzenetek ellenőrzéséhez a **Bemenetek konfigurációs** lapjáról kattintson a létrehozott GELF UDP-bevitel "**A fogadott üzenetek megjelenítése**" gombjára. A képernyő az alábbihoz hasonló képernyőre irányítja: 

![Hisztogram](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

A kék "%{Message}" hivatkozásra kattintva az egyes üzenetek kibővülnek, hogy megjelenjenek az egyes folyamat-törzsek paraméterei, ahogy az az alábbi képen látható:

![Üzenetek](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Alapértelmezés szerint az összes üzenetmező szerepel a keresésben, ha nem jelöl ki egy adott üzenetmezőt, amelyet keresni szeretne. Ha konkrét üzeneteket szeretne keresni (azaz - flow tuples egy adott forrás IP) használhatja a Graylog keresési lekérdezés [nyelve dokumentált](https://docs.graylog.org/en/2.2/pages/queries.html)

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Hálózati biztonsági csoport folyamatnaplóinak elemzése a Graylog használatával

Most, hogy a Graylog futást állított be, a folyamatnapló adatainak jobb megértéséhez használhatja annak néhány funkcióját. Ennek egyik módja az irányítópultok használata az adatok adott nézeteinek létrehozásához.

### <a name="create-a-dashboard"></a>Irányítópult létrehozása

1. A felső navigációs sávon válassza az **Irányítópultok** lehetőséget, vagy keresse meg a`http://<graylog-server-ip>:9000/dashboards/`

2. Innen kattintson a zöld **Irányítópult létrehozása** gombra, és töltse ki a rövid űrlapot az irányítópult címével és leírásával. Nyomja meg a **Mentés** gombot az új irányítópult létrehozásához. Az alábbihoz hasonló irányítópult jelenik meg:

    ![Irányítópultok](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Widgetek hozzáadása

Az irányítópult címére kattintva megtekintheti, de jelenleg üres, mivel nem adtunk hozzá widgeteket. Az irányítópulthoz egyszerűen és hasznosan hozzáadható **gyorsértékek** diagramok, amelyek a kijelölt mező értékeinek listáját és eloszlását jelenítik meg.

1. A felső navigációs **sávkeresés** gombjának kiválasztásával visszakeresheti a folyamatnaplókat fogadó UDP-bemenet keresési eredményeit.

2. A **Keresés eredménypanelje** alatt keresse meg a **Mezők** lapot, amely felsorolja az egyes bejövő folyamat-törzsüzenetek különböző mezőit.

3. Válassza ki a kívánt paramétert, amelyben meg szeretné jeleníteni (ebben a példában az IP-forrás van kiválasztva). A lehetséges widgetek listájának megjelenítéséhez kattintson a mező tőlbalra található kék legördülő nyílra, majd válassza a **Gyorsértékek** lehetőséget a widget létrehozásához. Az alábbihoz hasonló képet kell látnia:

   ![Forrás IP-címe](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. Itt kiválaszthatja a **Hozzáadás** az irányítópulthoz gombot a widget jobb felső sarkában, és kiválaszthatja a hozzáadni kívánt irányítópultot.

5. Az imént hozzáadott felületi elem megtekintéséhez lépjen vissza az irányítópultra.

   Számos más widgetet, például hisztogramokat adhat hozzá, és megszámol az irányítópulton a fontos mérőszámok, például az alábbi képen látható mintairányítópult nyomon követéséhez:

   ![Flowlogs irányítópult](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Az irányítópultokkal és a más típusú widgetekkel kapcsolatos további információkért tekintse meg a Graylog [dokumentációját.](https://docs.graylog.org/en/2.2/pages/dashboards.html)

A Network Watcher és a Graylog integrálásával mostmár kényelmes és központosított módon kezelheti és jelenítheti meg a hálózati biztonsági csoport folyamatnaplóit. A Graylog számos más hatékony funkcióval is rendelkezik, például adatfolyamokkal és riasztásokkal, amelyek a folyamatnaplók további kezelésére és a hálózati forgalom jobb megértésére is használhatók. Most, hogy beállította és csatlakoztatta az Azure-hoz a Graylogszolgáltatást, nyugodtan folytassa az általa kínált egyéb funkciók feltárását.

## <a name="next-steps"></a>További lépések

Megtudhatja, hogy miként jelenítheti meg a hálózati biztonsági csoport folyamatnaplóit a Power BI segítségével, ha ellátogat a [Visualize hálózati biztonsági csoportfolyamatainak naplóiba a Power BI-val.](network-watcher-visualize-nsg-flow-logs-power-bi.md)
