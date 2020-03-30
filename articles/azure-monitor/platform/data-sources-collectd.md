---
title: Adatok gyűjtése az Azure Monitorban gyűjtött adatokról | Microsoft dokumentumok
description: A CollectD egy nyílt forráskódú Linux démon, amely rendszeresidőközönként adatokat gyűjt az alkalmazásokból és a rendszerszintű információkból.  Ez a cikk az Azure Monitorban gyűjtött adatok gyűjtéséről nyújt tájékoztatást.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/27/2018
ms.openlocfilehash: b8c09d4ac5d0856eb0d448a1cabd9adc567850c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670610"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Adatok gyűjtése linuxos ügynökökről az Azure Monitorban
[A CollectD](https://collectd.org/) egy nyílt forráskódú Linux démon, amely rendszeres időközönként teljesítménymutatókat gyűjt az alkalmazásoktól és a rendszerszintű információktól. Példa alkalmazások közé tartozik a Java Virtual Machine (JVM), MySQL Server és Nginx. Ez a cikk az Azure Monitorban gyűjtött teljesítményadatok gyűjtéséről nyújt tájékoztatást.

A teljes listát a rendelkezésre álló dugó megtalálható [táblázat plugins](https://collectd.org/wiki/index.php/Table_of_Plugins).

![CollectD – áttekintés](media/data-sources-collectd/overview.png)

A következő Összegyűjtött konfiguráció tartalmazza a Log Analytics ügynök Linux továbbítja az összegyűjtött adatokat a Log Analytics ügynök Linuxra.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Továbbá, ha a collectD 5.5 előtti verzióit használja, használja a következő konfigurációt.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

A CollectD konfiguráció`write_http` az alapértelmezett beépülő modult használja a 26000-es porton keresztüli teljesítménymérő-mérőszámok küldéséhez a Linux-log analytics-ügynöknek. 

> [!NOTE]
> Ez a port szükség esetén egyénileg definiált portra konfigurálható.

A Log Analytics-ügynök Linux is figyeli a 26000-es porton a gyűjtött metrikák, majd átalakítja azokat az Azure Monitor séma metrikák. A következő a Log Analytics `collectd.conf`ügynök Linux konfiguráció .

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>

> [!NOTE]
> A CollectD alapértelmezés szerint 10 másodperces [időközzel](https://collectd.org/wiki/index.php/Interval)olvassa be az értékeket. Mivel ez közvetlenül befolyásolja az Azure Monitor naplóknak küldött adatok mennyiségét, előfordulhat, hogy ezt az intervallumot a CollectD konfiguráción belül kell hangolnia, hogy megfelelő egyensúlyt teremtsen a figyelési követelmények és a kapcsolódó költségek és az Azure Monitor naplók használati költségei között.

## <a name="versions-supported"></a>Támogatott verziók
- Az Azure Monitor jelenleg támogatja a CollectD 4.8-as és újabb verzióját.
- A DataData-ügynök Linux-v1.1.0-217 vagy újabb rendszeren szükséges a CollectD metrikagyűjteményhez.


## <a name="configuration"></a>Konfiguráció
Az alábbiakban az Azure Monitorban az összegyűjtött adatok gyűjtésének konfigurálása alapvető lépésekkel konfigurálható.

1. Állítsa be az Összegyűjtött adatokat a Log Analytics ügynök Linux a write_http plugin használatával.  
2. Állítsa be a Log Analytics-ügynök Linux figyelni a gyűjtött adatokat a megfelelő porton.
3. Indítsa újra a CollectD és a Log Analytics ügynököt Linuxra.

### <a name="configure-collectd-to-forward-data"></a>Az Adatok továbbítása gyűjtött beállítás 

1. Az összegyűjtött adatok linuxos Log Analytics-ügynöknek való átirányításához hozzá `oms.conf` kell adni a CollectD konfigurációs könyvtárához. A fájl célja a gép Linux disztribúciójáttól függ.

    Ha a CollectD konfigurációs könyvtár az /etc/collectd.d/fájlban található:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Ha a CollectD konfigurációs könyvtár az /etc/collectd/collectd.conf.d/:If your CollectD config directory located in /etc/collectd/collectd.conf.d/:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >Az 5.5 előtti collectd verziók esetében `oms.conf` módosítania kell a címkéket a fentiek szerint.
    >

2. Másolja a collectd.conf fájlt a kívánt munkaterület omsagent konfigurációs könyvtárába.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Indítsa újra a CollectD és a Log Analytics ügynököt Linuxra a következő parancsokkal.

    sudo szolgáltatás gyűjtött újraindítás sudo /opt/microsoft/omsagent/bin/service_control újraindítás

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>Összegyűjtött mérőszámok az Azure Monitor séma-konvertálásához
A Log Analytics-ügynök linuxos ügynöke által már összegyűjtött infrastruktúra-metrikák és a CollectD által gyűjtött új metrikák között ismerős modell fenntartása érdekében a következő sémaleképezést használjuk:

| Összegyűjtött metrikus mező | Az Azure Monitor mező |
|:--|:--|
| `host` | Computer |
| `plugin` | None |
| `plugin_instance` | Példány neve<br>Ha **plugin_instance** *null,* akkor a InstanceName="*_Total*" |
| `type` | ObjectName |
| `type_instance` | Ellennév<br>Ha **type_instance** *null,* akkor a CounterName=**üres** |
| `dsnames[]` | Ellennév |
| `dstypes` | None |
| `values[]` | Ellenérték |

## <a name="next-steps"></a>További lépések
* Ismerje meg a [naplólekérdezéseket](../log-query/log-query-overview.md) az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez. 
* [Az Egyéni mezők](custom-fields.md) segítségével a syslog rekordok adatait egyes mezőkké elemezheti.
