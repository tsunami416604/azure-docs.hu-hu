---
title: Egyéni JSON-adatok gyűjtése az Azure Monitorban | Microsoft dokumentumok
description: Egyéni JSON-adatforrások gyűjthetők az Azure Monitor a Log Analytics Ügynök Linuxhoz.  Ezek az egyéni adatforrások lehetnek egyszerű szkriptek visszatérő JSON, mint a curl vagy az egyik FluentD a 300 + dugó. Ez a cikk az adatgyűjtéshez szükséges konfigurációt ismerteti.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: 49eb3fa22bc9afffb9e93f3152cdc00323b76d41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662161"
---
# <a name="collecting-custom-json-data-sources-with-the-log-analytics-agent-for-linux-in-azure-monitor"></a>Egyéni JSON-adatforrások gyűjtése a Linux-alapú Log Analytics-ügynökkel az Azure Monitorban
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Egyéni JSON-adatforrások gyűjthetők az [Azure Monitor](data-platform.md) a Log Analytics-ügynök Linuxhoz.  Ezek az egyéni adatforrások lehetnek egyszerű szkriptek visszatérő JSON, mint a [curl](https://curl.haxx.se/) vagy az egyik [FluentD a 300 + dugó](https://www.fluentd.org/plugins/all). Ez a cikk az adatgyűjtéshez szükséges konfigurációt ismerteti.


> [!NOTE]
> Log Analytics ügynök Linux v1.1.0-217+ egyéni JSON-adatok

## <a name="configuration"></a>Konfiguráció

### <a name="configure-input-plugin"></a>Bemeneti bővítmény konfigurálása

JSON-adatok gyűjtése az Azure `oms.api.` Monitorban, add hozzá a kezdéshez egy FluentD címke egy bemeneti plugin.

Az alábbiakban például egy `exec-json.conf` `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`külön konfigurációs fájl látható a alkalmazásban.  Ez a FluentD `exec` beépülő modul segítségével 30 másodpercenként futtatható a curl parancs.  A parancs kimenetét a JSON kimeneti bővítmény gyűjti össze.

```
<source>
  type exec
  command 'curl localhost/json.output'
  format json
  tag oms.api.httpresponse
  run_interval 30s
</source>

<match oms.api.httpresponse>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api_httpresponse*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```
A csoportban `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` hozzáadott konfigurációs fájl tulajdonosának a következő paranccsal meg kell változtatnia a tulajdonjogát.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Kimeneti bővítmény konfigurálása 
Adja hozzá a következő kimeneti `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` beépülő modul konfigurációját a fő konfigurációhoz a következő konfigurációs fájlban vagy`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

```
<match oms.api.**>
  type out_oms_api
  log_level info

  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms_api*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
```

### <a name="restart-log-analytics-agent-for-linux"></a>A Log Analytics ügynök újraindítása Linuxra
Indítsa újra a Log Analytics ügynök Linux szolgáltatás a következő paranccsal.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Kimenet
Az adatokat az Azure Monitorban gyűjtjük, a bejegyzéstípussal. `<FLUENTD_TAG>_CL`

Az Azure Monitor `tag oms.api.tomcat` egyéni címkéje például `tomcat_CL`a .  Az ilyen típusú összes rekordot a következő naplólekérdezéssel is bekérheti.

    Type=tomcat_CL

A beágyazott JSON-adatforrások támogatottak, de a szülőmezőn kívül vannak indexelve. Például a következő JSON-adatok at adja `tag_s : "[{ "a":"1", "b":"2" }]`vissza a naplólekérdezés .

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>További lépések
* Ismerje meg a [naplólekérdezéseket](../log-query/log-query-overview.md) az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez. 
