---
title: Az Azure Sentinel hez való kapcsolódás ellenőrzése | Microsoft dokumentumok
description: Ellenőrizze a biztonsági megoldás kapcsolatát, és győződjön meg arról, hogy a CEF-üzenetek továbbítása folyamatban van az Azure Sentinel számára.
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
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: 6b91e36ee09aa855c119add2c0eb268cf8b97393
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731817"
---
# <a name="step-3-validate-connectivity"></a>3. LÉPÉS: A kapcsolat ellenőrzése

Miután üzembe helyezte a naplótovábbító (az 1. lépésben), és konfigurálta a biztonsági megoldás, hogy küldje el cef üzeneteket (a 2. lépésben), kövesse az alábbi utasításokat a biztonsági megoldás és az Azure Sentinel közötti kapcsolat ellenőrzéséhez. 

## <a name="prerequisites"></a>Előfeltételek

- Emelt szintű engedélyekkel (sudo) kell rendelkeznie a naplótovábbító gépen.

- A Pythonnak telepítve kell lennie a naplótovábbító-gépen.<br>
A `python –version` parancs segítségével ellenőrizze.

## <a name="how-to-validate-connectivity"></a>A kapcsolat ellenőrzése

1. Az Azure Sentinel navigációs menüjében nyissa **meg a Naplók ablakot.** Futtasson egy lekérdezést a **CommonSecurityLog** séma használatával, és nézze meg, hogy kap-e naplókat a biztonsági megoldásból.<br>
Ne feledje, hogy körülbelül 20 percet vesz igénybe, amíg a naplók kezdenek megjelenni a **Log Analytics.** 

1. Ha nem lát eredményt a lekérdezésből, ellenőrizze, hogy a biztonsági megoldásból események jönnek-e létre, vagy próbáljon meg generálni néhányat, és ellenőrizze, hogy azok továbbítása a kijelölt Syslog szállítmányozó gépre van-e. 

1. Futtassa a következő parancsfájlt a naplótovábbítón a biztonsági megoldás, a naplótovábbító és az Azure Sentinel közötti kapcsolat ellenőrzéséhez. Ez a parancsfájl ellenőrzi, hogy a démon figyeli-e a megfelelő portokat, hogy a továbbítás megfelelően van-e konfigurálva, és hogy semmi sem blokkolja a démon és a Log Analytics-ügynök közötti kommunikációt. Azt is küld álüzeneteket "TestCommonEventFormat", hogy ellenőrizze a végpontok közötti kapcsolat. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`

## <a name="validation-script-explained"></a>Az érvényesítési parancsfájl magyarázata

Az érvényesítési parancsfájl a következő ellenőrzéseket hajtja végre:

# <a name="rsyslog-daemon"></a>[rsyslog démon](#tab/rsyslog)

1. Ellenőrzi, hogy a fájl<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    létezik, és érvényes.

1. Ellenőrzi, hogy a fájl a következő szöveget tartalmazza-e:

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

1. Ellenőrzi, hogy vannak-e olyan biztonsági fejlesztések a számítógépen, amelyek blokkolhatják a hálózati forgalmat (például a gazdatűzfalat).

1. Ellenőrzi, hogy a syslog démon (rsyslog) megfelelően van-e konfigurálva, hogy cef-ként (regex használatával) azonosító üzeneteket küldjön a 25226-os TCP-porton lévő Log Analytics-ügynöknek:

    - Konfigurációs fájl:`/etc/rsyslog.d/security-config-omsagent.conf`

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. Ellenőrzi, hogy a syslog démon adatokat fogad-e az 514-es porton

1. A szükséges kapcsolatok létrehozásának ellenőrzése: tcp 514 az adatok fogadásához, tcp 25226 a syslog démon és a Log Analytics-ügynök közötti belső kommunikációhoz

1. MOCK adatokat küld a localhost 514-es portjára. Ezeknek az adatoknak megfigyelhetőnek kell lenniük az Azure Sentinel-munkaterületen a következő lekérdezés futtatásával:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

# <a name="syslog-ng-daemon"></a>[syslog-ng démon](#tab/syslogng)

1. Ellenőrzi, hogy a fájl<br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    létezik, és érvényes.

1. Ellenőrzi, hogy a fájl a következő szöveget tartalmazza-e:

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

1. Ellenőrzi, hogy vannak-e olyan biztonsági fejlesztések a számítógépen, amelyek blokkolhatják a hálózati forgalmat (például a gazdatűzfalat).

1. Ellenőrzi, hogy a syslog démon (syslog-ng) megfelelően van-e konfigurálva, hogy cef-ként (regex használatával) azonosító üzeneteket küldjön a 25226-os TCP-porton lévő Log Analytics-ügynöknek:

    - Konfigurációs fájl:`/etc/syslog-ng/conf.d/security-config-omsagent.conf`

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. Ellenőrzi, hogy a syslog démon adatokat fogad-e az 514-es porton

1. A szükséges kapcsolatok létrehozásának ellenőrzése: tcp 514 az adatok fogadásához, tcp 25226 a syslog démon és a Log Analytics-ügynök közötti belső kommunikációhoz

1. MOCK adatokat küld a localhost 514-es portjára. Ezeknek az adatoknak megfigyelhetőnek kell lenniük az Azure Sentinel-munkaterületen a következő lekérdezés futtatásával:

        CommonSecurityLog
        | where DeviceProduct == "MOCK"

---

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja a CEF-készülékeket az Azure Sentinelhez. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats.md)
- Az adatok figyeléséhez [használjon munkafüzeteket.](tutorial-monitor-your-data.md)

