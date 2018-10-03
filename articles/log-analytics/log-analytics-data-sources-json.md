---
title: OMS Log Analytics használatával egyéni JSON-adatok gyűjtése |} A Microsoft Docs
description: Egyéni JSON-adatforrások gyűjthetők be a Log Analytics használatával az OMS-ügynök Linux rendszeren.  Ezek az egyéni adatforrások JSON visszaadó curl vagy valamelyik, a FluentD mint 300 beépülő modulok például egyszerű parancsfájlokat is lehet. Ez a cikk ismerteti a adatgyűjteményben szükséges konfigurációnak.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 9725a3df04ef28fc3a076c3c6ca6663e36b186a8
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48040268"
---
# <a name="collecting-custom-json-data-sources-with-the-oms-agent-for-linux-in-log-analytics"></a>Egyéni OMS-ügynökkel rendelkező JSON-adatforrások gyűjtése a Log Analytics linuxhoz
Egyéni JSON-adatforrások gyűjthetők be a Log Analytics használatával az OMS-ügynök Linux rendszeren.  Ezek az egyéni adatforrások lehet például visszaadó JSON egyszerű parancsfájlokat [curl](https://curl.haxx.se/) vagy az egyik [FluentD a mint 300 beépülő modulok](http://www.fluentd.org/plugins/all). Ez a cikk ismerteti a adatgyűjteményben szükséges konfigurációnak.

> [!NOTE]
> Linux v1.1.0 készült OMS-ügynök – 217 + kötelező megadni egyéni JSON-adatok

## <a name="configuration"></a>Konfiguráció

### <a name="configure-input-plugin"></a>A bemeneti beépülő modul konfigurálása

Adja hozzá a Log Analytics JSON-adatok gyűjtéséhez `oms.api.` egy FluentD címkét egy bemeneti beépülő modul elejére.

Ha például az alábbiakban a különálló konfigurációs fájlt `exec-json.conf` a `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`.  Ez a FluentD beépülő modul használja `exec` 30 másodpercenként a curl-parancs futtatásához.  Ez a parancs kimenete a JSON-kimenet beépülő modul gyűjti.

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
A konfigurációs fájl alapján hozzáadott `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/` a tulajdonosa, a következő paranccsal módosítani kell.

`sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/exec-json.conf`

### <a name="configure-output-plugin"></a>Kimeneti beépülő modul konfigurálása 
Adja hozzá a következő kimeneti beépülő modul konfiguráció a fő konfigurációhoz a `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` vagy különálló konfigurációs fájlt helyezni `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/`

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

### <a name="restart-oms-agent-for-linux"></a>Indítsa újra a Linuxhoz készült OMS-ügynök
Indítsa újra az OMS-ügynök Linux-szolgáltatás a következő paranccsal.

    sudo /opt/microsoft/omsagent/bin/service_control restart 

## <a name="output"></a>Kimenet
A történjen az adatgyűjtés a Log Analytics típusú rekord `<FLUENTD_TAG>_CL`.

Ha például az egyéni címke `tag oms.api.tomcat` a Log Analytics típusú rekord `tomcat_CL`.  A típus a következő naplókeresést az összes rekord beolvasása.

    Type=tomcat_CL

Beágyazott JSON-adatok források támogatottak, de az indexelt minden szülő mező alapján. Például a következő JSON-adatokat a Log Analytics-keresést, visszaadott `tag_s : "[{ "a":"1", "b":"2" }]`.

```
{
    "tag": [{
        "a":"1",
        "b":"2"
    }]
}
```


## <a name="next-steps"></a>További lépések
* Ismerje meg [naplókereséseket](log-analytics-log-searches.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez. 
 