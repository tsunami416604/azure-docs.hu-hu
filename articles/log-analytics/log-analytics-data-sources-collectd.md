---
title: OMS Log Analytics az összegyűjtött adatokat gyűjteni |} A Microsoft Docs
description: Összegyűjtött egy nyílt forráskódú Linux-démon, amely rendszeres időközönként alkalmazások és a rendszer szintű adatait gyűjti az adatokat.  Ez a cikk információkat biztosít a Log Analytics összegyűjtött adatok gyűjtését.
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
ms.date: 05/02/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: eb053ef8fc66ff9d71a9576b71eb4edfcd688638
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041290"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-log-analytics"></a>Összegyűjtött adatok gyűjtésére a Linux-ügynökök a Log Analyticsben
[Összegyűjtött](https://collectd.org/) van egy nyílt forráskódú Linux-démon rendszeres időközönként által gyűjtött teljesítmény-mérőszámok az alkalmazások és a rendszer szintű adatait. Példa alkalmazások közé tartozik, a Java virtuális gép (JVM), a MySQL-kiszolgáló és az nginx-et. Ez a cikk teljesítményadatok gyűjtése a Log Analytics az összegyűjtött információkat biztosít.

Az elérhető beépülő modulok tartalmazó teljes listát található [beépülő modulok tábla](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Összegyűjtött áttekintése](media/log-analytics-data-sources-collectd/overview.png)

A következő összegyűjtött konfigurációs tartalmazza az OMS-ügynök Linux-útvonal összegyűjtött adatokat az OMS-ügynök Linux rendszeren.

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

Az összegyűjtött konfigurációs használja az alapértelmezett`write_http` beépülő modul használatával teljesítmény metrikaadatok küldése 26000 porton keresztül az OMS-ügynök Linux rendszeren. 

> [!NOTE]
> Ez a port beállítható úgy, hogy egy egyénileg meghatározott portot szükség esetén.

A Linuxhoz készült OMS-ügynök is a összegyűjtött metrikák 26000 portot figyeli, és konvertálja azokat OMS séma metrikákat. Az alábbiakban található az OMS-ügynök Linux konfiguráció `collectd.conf`.

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
- Az OMS-ügynök Linux v1.1.0-217 vagy újabb összegyűjtött metrika gyűjtemény szükség.


## <a name="configuration"></a>Konfiguráció
Az alábbiakban az összegyűjtött adatok gyűjtésének konfigurálása a Log Analytics alapvető lépéseit.

1. Állítsa be az összegyűjtött adatokat küldeni az OMS-ügynök a write_http beépülő moduljával Linux rendszeren.  
2. Az összegyűjtött adatokat a megfelelő port figyelésére a Linuxhoz készült OMS-ügynök konfigurálása.
3. Indítsa újra az összegyűjtött és a Linuxhoz készült OMS-ügynök.

### <a name="configure-collectd-to-forward-data"></a>Összegyűjtött adatok továbbítására konfigurálása 

1. Az útvonal összegyűjtött adatokat az OMS-ügynök Linux rendszeren `oms.conf` által összegyűjtött konfigurációs könyvtárba kell hozzáadni. Ez a fájl és a Linux-disztribúció, a gép függ.

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

3. Indítsa újra összegyűjtött és az OMS-ügynök Linux rendszeren az alábbi parancsokkal.

    sudo szolgáltatás összegyűjtött sudo /opt/microsoft/omsagent/bin/service_control újraindítás

## <a name="collectd-metrics-to-log-analytics-schema-conversion"></a>A Log Analytics-séma átalakítását összegyűjtött metrikák
Összegyűjtött által gyűjtött egy jól ismert modell infrastruktúra metrikák Linuxhoz készült OMS-ügynök által már összegyűjtött és az új mérőszámok között a következő séma-hozzárendelés fenntartásához szolgál:

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
* Ismerje meg [naplókereséseket](log-analytics-log-searches.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez. 
* Használat [egyéni mezők](log-analytics-custom-fields.md) syslog-rekord származó adatok elemzése az egyes mezőket.

