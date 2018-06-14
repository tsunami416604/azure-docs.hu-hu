---
title: A Naplóelemzési OMS egyéni JSON-adatok gyűjtése |} Microsoft Docs
description: Egyéni JSON-adatforrások az OMS-ügynök használatával Linux Log Analyticshez gyűjthetők össze.  Az egyéni adatforrások egyszerű parancsfájlok JSON vissza például a curl vagy FluentD tartozó 300 + beépülő modulok egyike lehet. Ez a cikk ismerteti az adatgyűjtés szükséges beállításokat.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: 800ee1269556e7c2d56fbbf2b497c10509b5c78c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23855221"
---
# <a name="collecting-custom-json-data-sources-with-the-oms-agent-for-linux-in-log-analytics"></a>Linux Log Analyticshez az egyéni OMS-ügynökkel rendelkező JSON-adatforrások gyűjtése
Egyéni JSON-adatforrások az OMS-ügynök használatával Linux Log Analyticshez gyűjthetők össze.  Az egyéni adatforrások lehet egyszerű parancsfájlok JSON például visszaadó [curl](https://curl.haxx.se/) vagy az egyik [FluentD tartozó 300 + beépülő modulok](http://www.fluentd.org/plugins/all). Ez a cikk ismerteti az adatgyűjtés szükséges beállításokat.

> [!NOTE]
> Linux v1.1.0 OMS-ügynököt-217 + szükség egyéni JSON-adatok

## <a name="configuration"></a>Konfiguráció

### <a name="configure-input-plugin"></a>Bemeneti beépülő modul konfigurálása

A Naplóelemzési JSON-adatok gyűjtéséhez hozzáadása `oms.api.` elejére egy bemeneti beépülő modul egy FluentD címkét.

Például az alábbiakban látható egy külön konfigurációs fájl `exec-json.conf` a `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.  Ez a beépülő modul FluentD használ `exec` 30 másodpercenként futtatásához a curl-parancsot.  Ez a parancs kimenetében gyűjti a JSON kimeneti beépülő modul.

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
A konfigurációs fájl hozzáadása az `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` rendelkezik, a tulajdonjogát, a következő paranccsal módosítani kell.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Kimeneti beépülő modul konfigurálása 
A következő kimeneti beépülő modul konfiguráció hozzáadása a fő konfigurációs `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` vagy egy külön konfigurációs fájlban elhelyezni`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

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

### <a name="restart-oms-agent-for-linux"></a>Indítsa újra az OMS-ügynököt a Linux rendszerhez
Indítsa újra az OMS-ügynököt a Linux-szolgáltatás a következő paranccsal.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Kimenet
Az adatgyűjtést a Naplóelemzési rekord típussal rendelkező `<FLUENTD_TAG>_CL`.

Például az egyéni címke `tag oms.api.tomcat` a típusú rekord Naplóelemzési `tomcat_CL`.  Az összes rekord a következő naplófájl-keresési az ilyen típusú lehetett beolvasni.

    Type=tomcat_CL

Beágyazott JSON-adatok források támogatottak, de az indexelt kijelentkezés mező alapján. Például a következő JSON nem ad vissza egy Naplóelemzési keresést `tag_s : "[{ "a":"1", "b":"2" }]`.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>Következő lépések
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) az adatforrások és a megoldások gyűjtött adatok elemzésére. 
 