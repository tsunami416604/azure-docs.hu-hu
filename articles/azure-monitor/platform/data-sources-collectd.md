---
title: A Log Analytics összegyűjtött adatokat gyűjteni |} A Microsoft Docs
description: Összegyűjtött egy nyílt forráskódú Linux-démon, amely rendszeres időközönként alkalmazások és a rendszer szintű adatait gyűjti az adatokat.  Ez a cikk információkat biztosít a Log Analytics összegyűjtött adatok gyűjtését.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/27/2018
ms.author: magoedte
ms.openlocfilehash: 3ccb307eb028170f2dc65fd7cf90edf3115ae9a7
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54102686"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-log-analytics"></a>Összegyűjtött adatok gyűjtésére a Linux-ügynökök a Log Analyticsben
[Összegyűjtött](https://collectd.org/) van egy nyílt forráskódú Linux-démon rendszeres időközönként által gyűjtött teljesítmény-mérőszámok az alkalmazások és a rendszer szintű adatait. Példa alkalmazások közé tartozik, a Java virtuális gép (JVM), a MySQL-kiszolgáló és az nginx-et. Ez a cikk teljesítményadatok gyűjtése a Log Analytics az összegyűjtött információkat biztosít.

Az elérhető beépülő modulok tartalmazó teljes listát található [beépülő modulok tábla](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Összegyűjtött áttekintése](media/data-sources-collectd/overview.png)

A Log Analytics Linuxhoz készült útvonal összegyűjtött adatokat a Log Analytics-ügynök Linux-ügynök a következő összegyűjtött konfigurációt tartalmazza.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Emellett ha egy összegyűjtött verzióját használja, mielőtt egy 5.5-ös helyette az alábbi konfigurációt használja.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

Az összegyűjtött konfigurációs használja az alapértelmezett`write_http` 26000 porton keresztül küldendő teljesítmény metrikaadatok Linuxhoz készült Log Analytics-ügynök beépülő modult. 

> [!NOTE]
> Ez a port beállítható úgy, hogy egy egyénileg meghatározott portot szükség esetén.

A Linuxhoz készült Log Analytics-ügynököt is a összegyűjtött metrikák 26000 portot figyeli, és konvertálja azokat a Log Analytics-séma metrikák. Az alábbiakban található az a Log Analytics-ügynök Linux konfigurációs `collectd.conf`.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>


## <a name="versions-supported"></a>Támogatott verziók
- A log Analytics jelenleg támogatja az összegyűjtött 4.8 verzió vagy újabb verzió.
- Log Analytics-ügynök Linux v1.1.0-217 vagy újabb összegyűjtött metrika gyűjtemény szükség.


## <a name="configuration"></a>Konfiguráció
Az alábbiakban az összegyűjtött adatok gyűjtésének konfigurálása a Log Analytics alapvető lépéseit.

1. Állítsa be az összegyűjtött adatokat küldeni a Log Analytics-ügynököket a write_http beépülő moduljával Linux rendszeren.  
2. Állítsa be az összegyűjtött adatokat a megfelelő port figyelésére a Linuxhoz készült Log Analytics-ügynököket.
3. Indítsa újra a Linux összegyűjtött és a Log Analytics-ügynököt.

### <a name="configure-collectd-to-forward-data"></a>Összegyűjtött adatok továbbítására konfigurálása 

1. Útvonal összegyűjtött adatokat a Log Analytics-ügynök Linux rendszeren `oms.conf` által összegyűjtött konfigurációs könyvtárba kell hozzáadni. Ez a fájl és a Linux-disztribúció, a gép függ.

    Ha az összegyűjtött konfigurációs könyvtár /etc/collectd.d/ találhatók:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Ha az összegyűjtött konfigurációs könyvtár /etc/collectd/collectd.conf.d/ találhatók:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >5.5-ös összegyűjtött verziókban kell a címkék módosításához `oms.conf` jelennek meg.
    >

2. Collectd.conf a kívánt munkaterület omsagent konfigurációs könyvtárba másolja.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Indítsa újra a következő parancsokat a Linuxhoz készült összegyűjtött és a Log Analytics ügynök.

    sudo szolgáltatás összegyűjtött sudo /opt/microsoft/omsagent/bin/service_control újraindítás

## <a name="collectd-metrics-to-log-analytics-schema-conversion"></a>A Log Analytics-séma átalakítását összegyűjtött metrikák
Egy jól ismert modell között már a Linuxhoz készült Log Analytics-ügynök által gyűjtött infrastruktúra-mérőszámok és az új metrikák, használja a következő séma-hozzárendelés összegyűjtött által gyűjtött karbantartásához:

| Összegyűjtött metrika mező | Log Analytics mező |
|:--|:--|
| gazdagép | Computer |
| Beépülő modul | None |
| plugin_instance | Példány neve<br>Ha **plugin_instance** van *null* majd InstanceName = "*_Total*" |
| type | Objektumnév |
| type_instance | CounterName<br>Ha **type_instance** van *null* majd CounterName =**üres** |
| dsnames] | CounterName |
| dstypes | None |
| értékek] | AVG |

## <a name="next-steps"></a>További lépések
* Ismerje meg [lekérdezések naplózását](../../log-analytics/log-analytics-queries.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez. 
* Használat [egyéni mezők](../../log-analytics/log-analytics-custom-fields.md) syslog-rekord származó adatok elemzése az egyes mezőket.
