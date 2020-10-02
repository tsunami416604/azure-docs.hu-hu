---
title: Az Azure Sentinel kapcsolatának ellenőrzése | Microsoft Docs
description: Ellenőrizze a biztonsági megoldás kapcsolatát, és győződjön meg arról, hogy a CEF üzeneteket továbbítják az Azure Sentinelnek.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: 643b28b2e88f233d2924270511d3c87fa4d9b767
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631630"
---
# <a name="step-3-validate-connectivity"></a>3. lépés: a kapcsolat ellenőrzése

Miután telepítette a napló-továbbítót (az 1. lépésben), és úgy konfigurálta a biztonsági megoldást, hogy CEF üzeneteket küldjön (a 2. lépésben), kövesse ezeket az utasításokat a biztonsági megoldás és az Azure Sentinel közötti kapcsolat ellenőrzéséhez. 

## <a name="prerequisites"></a>Előfeltételek

- Emelt szintű engedélyekkel (sudo) kell rendelkeznie a log továbbító számítógépén.

- Telepítenie kell a Pythont a naplózási továbbító számítógépén.<br>
A parancs használatával keresse meg a következőt: `python –version` .

## <a name="how-to-validate-connectivity"></a>A kapcsolat érvényesítése

1. Az Azure Sentinel navigációs menüjében nyissa meg a **naplók**elemet. Futtasson egy lekérdezést a **CommonSecurityLog** sémával, és ellenőrizze, hogy a biztonsági megoldásból kapott-e naplókat.<br>
Vegye figyelembe, hogy körülbelül 20 percet vesz igénybe, amíg a naplók meg nem jelennek a **log Analyticsban**. 

1. Ha nem látja az eredményeket a lekérdezésből, ellenőrizze, hogy az események a biztonsági megoldásból származnak-e, vagy próbáljon meg generálni, és ellenőrizze, hogy továbbítva vannak-e a kijelölt syslog-továbbító gépnek. 

1. Futtassa a következő szkriptet a naplózási továbbítón a biztonsági megoldás, a naplózási továbbító és az Azure Sentinel közötti kapcsolat vizsgálatához. Ez a szkript ellenőrzi, hogy a démon figyeli-e a megfelelő portokat, hogy a továbbítás megfelelően van-e konfigurálva, és hogy semmi sem blokkolja a démon és a Log Analytics ügynök közötti kommunikációt. Emellett a "TestCommonEventFormat" nevű ál-üzeneteket is elküldi a végpontok közötti kapcsolat vizsgálatához. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>Érvényesítési parancsfájl magyarázata

Az érvényesítési parancsfájl a következő ellenőrzéseket hajtja végre:

# <a name="rsyslog-daemon"></a>[rsyslog démon](#tab/rsyslog)

1. Ellenőrzi, hogy a fájl<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    létezik és érvényes.

1. Ellenőrzi, hogy a fájl tartalmazza-e a következő szöveget:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Ellenőrzi, hogy a tűzfal eseményeihez tartozó Cisco ASA-elemzés a várt módon van-e konfigurálva:

    ```bash
    sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" 
        /opt/microsoft/omsagent/plugin/security_lib.rb && 
        sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Ellenőrzi, hogy a syslog-forrás *számítógép* mezője megfelelően van-e leképezve a log Analytics ügynökben:

    ```bash
    sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" 
        -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/pl ugin/
        filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Ellenőrzi, hogy vannak-e olyan biztonsági fejlesztések a gépen, amelyek blokkolják a hálózati forgalmat (például a gazda tűzfalat).

1. Ellenőrzi, hogy a syslog démon (rsyslog) megfelelően van-e konfigurálva ahhoz, hogy üzeneteket küldjön (amelyet CEF azonosít) a 25226-es TCP-porton található Log Analytics ügynöknek:

    - Konfigurációs fájl: `/etc/rsyslog.d/security-config-omsagent.conf`

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. Újraindítja a syslog démont és a Log Analytics ügynököt:

    ```bash
    service rsyslog restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Ellenőrzi, hogy a szükséges kapcsolatok létrejöttek-e: TCP 514 az adatfogadáshoz, TCP 25226 a syslog démon és a Log Analytics ügynök közötti belső kommunikációhoz:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Ellenőrzi, hogy a syslog démon fogad-e az 514-es porton tárolt adatfogadást, és hogy az ügynök fogad-e adatfogadást a 25226-es

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. A rendszer a (z) 514-as porton küldi el a modell adatokat Az alábbi lekérdezés futtatásával meg kell jelennie az Azure Sentinel munkaterületen:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```

# <a name="syslog-ng-daemon"></a>[syslog-ng démon](#tab/syslogng)

1. Ellenőrzi, hogy a fájl<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    létezik és érvényes.

1. Ellenőrzi, hogy a fájl tartalmazza-e a következő szöveget:

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. Ellenőrzi, hogy a tűzfal eseményeihez tartozó Cisco ASA-elemzés a várt módon van-e konfigurálva:

    ```bash
    sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" 
        /opt/microsoft/omsagent/plugin/security_lib.rb && 
        sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Ellenőrzi, hogy a syslog-forrás *számítógép* mezője megfelelően van-e leképezve a log Analytics ügynökben:

    ```bash
    sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" 
        -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/pl ugin/
        filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Ellenőrzi, hogy vannak-e olyan biztonsági fejlesztések a gépen, amelyek blokkolják a hálózati forgalmat (például a gazda tűzfalat).

1. Ellenőrzi, hogy a syslog démon (syslog-ng) megfelelően van-e konfigurálva ahhoz, hogy üzeneteket küldjön a CEF (regex használatával) az Log Analytics ügynöknek a 25226-es TCP-porton:

    - Konfigurációs fájl: `/etc/syslog-ng/conf.d/security-config-omsagent.conf`

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
        destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. Újraindítja a syslog démont és a Log Analytics ügynököt:

    ```bash
    service syslog-ng restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. Ellenőrzi, hogy a szükséges kapcsolatok létrejöttek-e: TCP 514 az adatfogadáshoz, TCP 25226 a syslog démon és a Log Analytics ügynök közötti belső kommunikációhoz:

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Ellenőrzi, hogy a syslog démon fogad-e az 514-es porton tárolt adatfogadást, és hogy az ügynök fogad-e adatfogadást a 25226-es

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. A rendszer a (z) 514-as porton küldi el a modell adatokat Az alábbi lekérdezés futtatásával meg kell jelennie az Azure Sentinel munkaterületen:

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---

## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók a CEF-készülékek az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.

