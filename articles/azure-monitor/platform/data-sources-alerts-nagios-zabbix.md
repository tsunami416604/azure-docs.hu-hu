---
title: Az Azure monitorban a Nagios- és Zabbix-riasztások összegyűjtése |} A Microsoft Docs
description: Nagios- és Zabbix olyan nyílt forráskódú eszközök figyelését. Meg tud gyűjteni riasztások ezek az eszközök az Azure Monitor annak érdekében, hogy együtt más forrásból származó riasztások elemezheti őket.  Ez a cikk ismerteti, hogyan konfigurálhatja a riasztások gyűjtésére ezekben a rendszerekben Linuxhoz készült Log Analytics-ügynököket.
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
ms.openlocfilehash: 0ed6747573edf4c059eb29d28107a22706c52856
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426189"
---
# <a name="collect-alerts-from-nagios-and-zabbix-in-azure-monitor-from-log-analytics-agent-for-linux"></a>Nagios-és Zabbix az Azure Monitor riasztások gyűjtése a Linuxhoz készült Log Analytics-ügynök 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

[Nagios](https://www.nagios.org/) és [Zabbix](https://www.zabbix.com/) nyílt forrású eszközök figyelését. Meg tud gyűjteni riasztások ezeket az eszközöket az Azure Monitor annak érdekében, hogy a napló adatokat más forrásból származó adatokkal elemezheti őket.  Ez a cikk ismerteti, hogyan konfigurálhatja a riasztások gyűjtésére ezekben a rendszerekben Linuxhoz készült Log Analytics-ügynököket.


> [!NOTE]
> [Az Azure Monitor által létrehozott riasztásokat](alerts-overview.md) tárolt külön-külön származó naplóadatokat, és nem érhető el a naplólekérdezések.

 
## <a name="prerequisites"></a>Előfeltételek
A Linuxhoz készült Log Analytics-ügynök verzióra Nagios gyűjtését riasztásai támogatja 4.2.x és Zabbix verzióra 2.x.

## <a name="configure-alert-collection"></a>Riasztások gyűjtésének konfigurálása

### <a name="configuring-nagios-alert-collection"></a>Nagios-riasztások gyűjtésének konfigurálása
Riasztások gyűjtése, hajtsa végre az alábbi lépéseket a Nagios-kiszolgálón.

1. Adja meg a felhasználónak **omsagent** olvasási hozzáférés a Nagios naplófájl `/var/log/nagios/nagios.log`. A csoport tulajdonosa feltéve, hogy a nagios.log fájl `nagios`, a felhasználó hozzáadhat **omsagent** , a **nagios** csoport. 

    sudo usermod -a -G nagios omsagent

2.  Módosítsa a konfigurációs fájlban a következő `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Ellenőrizze, hogy a következő bejegyzések jelen, és nem megjegyzésekkel ki:  

        <source>  
          type tail  
          #Update path to point to your nagios.log  
          path /var/log/nagios/nagios.log  
          format none  
          tag oms.nagios  
        </source>  
      
        <filter oms.nagios>  
          type filter_nagios_log  
        </filter>  

3. A omsagent démon újraindításához

    ```
    sudo sh /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="configuring-zabbix-alert-collection"></a>Zabbix-riasztások gyűjtésének konfigurálása
A riasztások gyűjtésére Zabbix-kiszolgáló, meg kell adnia egy felhasználói és a jelszó *egyszerű szöveges*.  Amíg nem ideális megoldás, azt javasoljuk, hogy Zabbix felhasználó olvasásra vonatkozó riasztásokat csak olvasási engedélyekkel rendelkező hozzon létre.

Riasztások gyűjtése a Nagios-kiszolgálón, hajtsa végre az alábbi lépéseket.

1. Módosítsa a konfigurációs fájlban a következő `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`. Győződjön meg arról, a következő bejegyzések jelen, és nem megjegyzésekkel ki.  Módosítsa a felhasználónév és jelszó értékek Zabbix-környezete számára.

        <source>
         type zabbix_alerts
         run_interval 1m
         tag oms.zabbix
         zabbix_url http://localhost/zabbix/api_jsonrpc.php
         zabbix_username Admin
         zabbix_password zabbix
        </source>

2. A omsagent démon újraindításához

    `sudo sh /opt/microsoft/omsagent/bin/service_control restart`


## <a name="alert-records"></a>Riasztási rekordok
Riasztási rekord lekérése Nagios- és Zabbix használatával [lekérdezések naplózását](../log-query/log-query-overview.md) az Azure monitorban.

### <a name="nagios-alert-records"></a>Nagios-riasztás rekordok

Riasztás Nagios által összegyűjtött rekordokkal rendelkezik egy **típus** , **riasztási** és a egy **SourceSystem** , **Nagios**.  A tulajdonságait az alábbi táblázatban rendelkeznek.

| Tulajdonság | Leírás |
|:--- |:--- |
| `Type` |*Riasztás* |
| `SourceSystem` |*Nagios* |
| `AlertName` |A riasztás neve. |
| `AlertDescription` | A riasztás leírása. |
| `AlertState` | A szolgáltatás vagy a gazdagép állapotát.<br><br>OK<br>FIGYELMEZTETÉS<br>FELFELÉ<br>LEFELÉ |
| `HostName` | A gazdagép által létrehozott, a riasztás neve. |
| `PriorityNumber` | A riasztás prioritását. |
| `StateType` | A riasztás állapotának típusa.<br><br>HELYREÁLLÍTHATÓ - hibát, amely nem elteltével.<br>MEREVLEMEZ - probléma, amely van újra ellenőrizni kell a megadott számú alkalommal.  |
| `TimeGenerated` |Dátum és idő a riasztás létrejött. |


### <a name="zabbix-alert-records"></a>Zabbix-riasztások rekordok
Riasztás Zabbix által összegyűjtött rekordokkal rendelkezik egy **típus** , **riasztási** és a egy **SourceSystem** , **Zabbix**.  A tulajdonságait az alábbi táblázatban rendelkeznek.

| Tulajdonság | Leírás |
|:--- |:--- |
| `Type` |*Riasztás* |
| `SourceSystem` |*Zabbix* |
| `AlertName` | A riasztás neve. |
| `AlertPriority` | A riasztás súlyosságát.<br><br>nem besorolt<br>Információ<br>figyelmeztetés<br>átlag<br>Magas<br>vészhelyreállítás  |
| `AlertState` | A riasztás állapota.<br><br>0 – állapota naprakész.<br>1 – állapota ismeretlen.  |
| `AlertTypeNumber` | Itt adhatja meg, e riasztás hozhat létre több problémát esemény.<br><br>0 – állapota naprakész.<br>1 – állapota ismeretlen.    |
| `Comments` | A riasztás további megjegyzéseket. |
| `HostName` | A gazdagép által létrehozott, a riasztás neve. |
| `PriorityNumber` | A riasztás súlyosságát jelző érték beolvasása.<br><br>0 – nem besorolt<br>1 – információ<br>2 – figyelmeztetés<br>3 – átlag<br>4 – magas<br>5 – vészhelyreállítás |
| `TimeGenerated` |Dátum és idő a riasztás létrejött. |
| `TimeLastModified` |Dátum és idő, a riasztás állapotának módosítása. |


## <a name="next-steps"></a>További lépések
* Ismerje meg [riasztások](alerts-overview.md) az Azure monitorban.
* Ismerje meg [lekérdezések naplózását](../log-query/log-query-overview.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez. 
