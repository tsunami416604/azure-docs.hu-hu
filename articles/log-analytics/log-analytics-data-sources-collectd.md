---
title: Adatgyűjtés a CollectD az OMS szolgáltatáshoz |} Microsoft Docs
description: CollectD egy nyílt forráskódú Linux-démon, amely rendszeres időközönként gyűjti az adatokat az alkalmazások és a rendszer a szintre vonatkozó információ.  Ez a cikk tájékoztatást nyújt a Naplóelemzési CollectD adatainak begyűjtése.
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
ms.component: na
ms.openlocfilehash: 59b6f8b82d0f714d4526147b42f68e14bf0aa2bd
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127696"
---
# <a name="collect-data-from-collectd-on-linux-agents-in-log-analytics"></a>Gyűjti az adatokat a CollectD Naplóelemzési Linux-ügynökök
[CollectD](https://collectd.org/) van egy nyílt forráskódú Linux-démon rendszeresen teljesítménymutatók az összegyűjtő alkalmazások és a rendszer a szintre vonatkozó információ. Példa alkalmazások közé tartoznak a Java virtuális gép (JVM), a MySQL-kiszolgáló és a Nginx. Ez a cikk tájékoztatást nyújt teljesítményadatok összegyűjtése a Naplóelemzési CollectD.

Elérhető beépülő modulok teljes listája megtalálható [beépülő modulok tábla](https://collectd.org/wiki/index.php/Table_of_Plugins).

![CollectD áttekintése](media/log-analytics-data-sources-collectd/overview.png)

A következő CollectD konfiguráció megtalálható az OMS-ügynököt Linux útvonal CollectD az adatokat az OMS-ügynököt Linux.

    LoadPlugin write_http

    <Plugin write_http>
         <Node "oms">
         URL "127.0.0.1:26000/oms.collectd"
         Format "JSON"
         StoreRates true
         </Node>
    </Plugin>

Emellett ha egy collectD verzióját használja, mielőtt 5.5 helyette használja az alábbi konfigurációt.

    LoadPlugin write_http

    <Plugin write_http>
       <URL "127.0.0.1:26000/oms.collectd">
        Format "JSON"
         StoreRates true
       </URL>
    </Plugin>

Az alapértelmezett értéket használja, a CollectD konfigurációs`write_http` adatokat küldeni a teljesítmény metrika 26000 porton keresztül OMS-ügynököt a Linux rendszerhez használt beépülő modult. 

> [!NOTE]
> Ez a port beállítható úgy, hogy egy egyénileg definiált port szükség esetén.

Linux OMS-ügynököt is porton figyel 26000 CollectD metrikáihoz, és majd alakítja át ezeket OMS séma metrikákat. Az alábbiakban található a Linux-konfigurációhoz OMS-ügynököt `collectd.conf`.

    <source>
      type http
      port 26000
      bind 127.0.0.1
    </source>

    <filter oms.collectd>
      type filter_collectd
    </filter>


## <a name="versions-supported"></a>Támogatott verziók
- Naplóelemzési jelenleg CollectD 4.8 verzió vagy újabb verzió.
- A Linux v1.1.0-217 vagy újabb OMS-ügynököt CollectD metrika gyűjtemény szükség.


## <a name="configuration"></a>Konfiguráció
Az alábbi lépések alapvető Naplóelemzési CollectD adatgyűjtés konfigurálásához.

1. Szeretnék adatokat küldeni a az OMS-ügynököt a write_http beépülő modul használata Linux CollectD konfigurálása.  
2. A CollectD adatok a megfelelő porton figyeljen Linux az OMS-ügynök konfigurálása.
3. Indítsa újra a CollectD és Linux OMS-ügynököt.

### <a name="configure-collectd-to-forward-data"></a>Adatok CollectD konfigurálása 

1. Az OMS-ügynököt, Linux, az útvonal CollectD adatokat `oms.conf` hozzá kell adni a CollectD tartozó konfigurációs könyvtárba. Ez a fájl és a gép a Linux distro függ.

    Ha a CollectD konfigurációs könyvtár /etc/collectd.d/ találhatók:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd.d/oms.conf

    Ha a CollectD konfigurációs könyvtár /etc/collectd/collectd.conf.d/ találhatók:

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/oms.conf /etc/collectd/collectd.conf.d/oms.conf

    >[!NOTE]
    >A címkék módosításához hogy CollectD verzióihoz 5.5 előtt `oms.conf` a fentiek szerint.
    >

2. Collectd.conf másolja a kívánt munkaterület omsagent konfigurációs könyvtárba.

        sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/collectd.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/
        sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/collectd.conf

3. Indítsa újra a CollectD és az OMS-ügynököt Linux az alábbi parancsokkal.

    sudo szolgáltatás collectd sudo /opt/microsoft/omsagent/bin/service_control újraindítás

## <a name="collectd-metrics-to-log-analytics-schema-conversion"></a>A Naplóelemzési séma alakításához CollectD metrikák
CollectD által gyűjtött egy ismerős modell infrastruktúra mérőszámokat már OMS-ügynök a Linux és az új mérőszámok között a következő séma-hozzárendeléséhez karbantartásához használja:

| CollectD metrika mező | Log Analytics mező |
|:--|:--|
| gazdagép | Computer |
| beépülő modul | None |
| plugin_instance | Példány neve<br>Ha **plugin_instance** van *null* majd példánynév = "*_Total*" |
| type | ObjectName |
| type_instance | CounterName<br>Ha **type_instance** van *null* majd CounterName =**üres** |
| dsnames] | CounterName |
| dstypes | None |
| [érték] | Ellenértéknek |

## <a name="next-steps"></a>További lépések
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) az adatforrások és a megoldások gyűjtött adatok elemzésére. 
* Használjon [egyéni mezők](log-analytics-custom-fields.md) syslog rekordokban levő adatok elemzése az egyes mezőkbe.

