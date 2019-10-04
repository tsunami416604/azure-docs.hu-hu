---
title: Adatgyűjtés a Azure Monitor gyűjtött adatokból | Microsoft Docs
description: A Collected egy nyílt forráskódú linuxos démon, amely rendszeres időközönként adatokat gyűjt az alkalmazásokból és a rendszerszintű adatokból.  Ez a cikk a Azure Monitor gyűjtött adatok gyűjtésével kapcsolatos információkat tartalmazza.
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
ms.openlocfilehash: b1f02e01fef95bdd06930aa30479dd16d40675ce
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71812557"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-azure-monitor"></a>Adatgyűjtés a Linux-ügynököktől a Azure Monitor-ban gyűjtött adatokból
[](https://collectd.org/) A Collected egy nyílt forráskódú linuxos démon, amely rendszeresen gyűjti a teljesítmény-mérőszámokat az alkalmazásokból és a rendszerszintű információkból. Például az alkalmazások közé tartozik a Java virtuális gép (JVM), a MySQL-kiszolgáló és az Nginx. Ez a cikk a Azure Monitor gyűjtött teljesítményadatok gyűjtésével kapcsolatos információkat tartalmazza.

Az elérhető beépülő modulok teljes listája megtalálható a [plugins táblázatában](https://collectd.org/wiki/index.php/Table_of_Plugins).

![Adatgyűjtés – áttekintés](media/data-sources-collectd/overview.png)

A Linux rendszerhez készült Log Analytics-ügynök a következő összegyűjtési konfigurációt tartalmazza, hogy átirányítsa a gyűjtött adatokat a linuxos Log Analytics-ügynöknek.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Emellett, ha a 5,5 előtt összegyűjtött verziót használ, a következő konfigurációt használja helyette.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

A gyűjtött konfiguráció az alapértelmezett @ no__t-0 beépülő modullal küldi el a teljesítmény-mérőszám adatait az 26000-as porton keresztül, hogy Log Analytics a Linux-ügynöknek. 

> [!NOTE]
> Ha szükséges, a port egy egyéni által megadott portra is konfigurálható.

A Linux rendszerhez készült Log Analytics-ügynök az 26000-es portot is figyeli a begyűjtött metrikák esetében, majd átalakítja őket Azure Monitor séma-metrikára. A következő a Linux-konfiguráció Log Analytics ügynöke `collectd.conf`.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>

> [!NOTE]
> Alapértelmezés szerint a rendszer 10 másodperces [időközönként](https://collectd.org/wiki/index.php/Interval)olvasási értékre állítja az adatokat. Mivel ez közvetlenül befolyásolja a Azure Monitor naplókba elküldett adatok mennyiségét, előfordulhat, hogy a begyűjtött konfiguráción belül kell megadnia ezt az intervallumot, hogy jó egyensúlyt lehessen teremteni a figyelési követelmények és a kapcsolódó költségek és a Azure Monitor naplók használata között.

## <a name="versions-supported"></a>Támogatott verziók
- A Azure Monitor jelenleg a 4,8-es és újabb verziójú gyűjtést támogatja.
- Log Analytics ügynök a Linux v 1.1.0-217-es vagy újabb verzióhoz szükséges a begyűjtött metrika-gyűjteményhez.


## <a name="configuration"></a>Konfiguráció
Az alábbi alapszintű lépések a begyűjtött adatok gyűjtésének konfigurálására szolgálnak Azure Monitorban.

1. A begyűjtött adatok küldésével adatokat küldhet a linuxos Log Analytics-ügynöknek a write_http beépülő modullal.  
2. Konfigurálja a Linux Log Analytics-ügynökét, hogy figyelje a gyűjtött adatokat a megfelelő porton.
3. A Linux-ügynök összegyűjtése és Log Analytics-ügynök újraindítása.

### <a name="configure-collectd-to-forward-data"></a>A begyűjtött adatok továbbításának beállítása 

1. Ahhoz, hogy a gyűjtött adatokat a Linux Log Analytics ügynökéhez irányítsa, `oms.conf` értéket kell hozzáadni a begyűjtött konfigurációs könyvtárhoz. A fájl célja a számítógép linuxos disztribúciója.

    Ha a gyűjtött konfigurációs könyvtár a/etc/collectd.d/-ben található:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Ha a gyűjtött konfigurációs könyvtár a/etc/collectd/collectd.conf.d/-ben található:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >A 5,5 előtti összegyűjtött verzióknál a fent látható módon módosítania kell a címkéket `oms.conf`-ban.
    >

2. Másolja a Collected. conf fájlt a kívánt munkaterület omsagent-konfigurációs könyvtárába.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. A következő parancsokkal indítsa újra a gyűjtést és Log Analytics a Linux-ügynököt.

    sudo szolgáltatás által összegyűjtött újraindítás sudo/opt/Microsoft/omsagent/bin/service_control újraindítás

## <a name="collectd-metrics-to-azure-monitor-schema-conversion"></a>Azure Monitor a séma átalakítására vonatkozó mérőszámok gyűjtése
A Log Analytics-ügynök által már összegyűjtött infrastruktúra-metrikák közötti ismerős modell fenntartásához, valamint a következő séma-hozzárendeléssel gyűjtött új metrikák használata:

| Begyűjtött metrika mező | Azure Monitor mező |
|:--|:--|
| `host` | Számítógép |
| `plugin` | Nincsenek |
| `plugin_instance` | Példány neve<br>Ha a **plugin_instance** értéke *Null* , akkor példánynév = "*összesen*" |
| `type` | ObjectName |
| `type_instance` | CounterName<br>Ha a **type_instance** értéke *Null* , akkor a CounterName =**blank** |
| `dsnames[]` | CounterName |
| `dstypes` | Nincsenek |
| `values[]` | Kártyabirtokos számlájának megterhelését |

## <a name="next-steps"></a>További lépések
* Ismerje meg [lekérdezések naplózását](../log-query/log-query-overview.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez. 
* Használat [egyéni mezők](custom-fields.md) syslog-rekord származó adatok elemzése az egyes mezőket.
