---
title: Összegyűjti és elemzi az OMS szolgáltatáshoz a Syslog-üzeneteket |} Microsoft Docs
description: A Syslog egy esemény naplózása protokoll, amely Linux közös. Ez a cikk ismerteti a Syslog-üzeneteket gyűjteménye konfigurálása a Naplóelemzési és hoznak létre az OMS-tárházban rekord adatait.
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
ms.date: 09/28/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 011eaf1a4705f9078225b9b871f81b4333b05ee8
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="syslog-data-sources-in-log-analytics"></a>A Naplóelemzési Syslog adatforrások
A Syslog egy esemény naplózása protokoll, amely Linux közös.  Alkalmazások, amelyek a helyi számítógépen tárolt vagy a Syslog-gyűjtőhöz kézbesíteni üzenetet szeretne küldeni.  A Linux OMS-ügynök telepítve van, amikor konfigurálja az üzenetek továbbítására az ügynöknek a helyi Syslog démon.  Az ügynök ezután elküldi az üzenet Naplóelemzési, ahol az OMS-tárházban létrejön egy megfelelő bejegyzés.  

> [!NOTE]
> A Naplóelemzési támogatja az üzeneteket a rsyslog vagy a syslog-ng, ahol rsyslog az alapértelmezett démon gyűjteménye. Syslog-események gyűjtése nem támogatott az alapértelmezett a syslog démon a Red Hat Enterprise Linux-, CentOS, és Oracle Linux verziója (sysklog) 5-ös verzióját. Syslog-adatokat gyűjteni a azokat a terjesztéseket, a jelen verziójában a [rsyslog démon](http://rsyslog.com) telepítve legyen, és cserélje le a sysklog konfigurálva.
>
>

![Syslog-gyűjtemény](media/log-analytics-data-sources-syslog/overview.png)

## <a name="configuring-syslog"></a>Syslog konfigurálása
Az OMS-ügynököt a Linux csak összegyűjti a létesítmények és konfigurációjában megadott súlyosságokkal események.  Syslog konfigurálhatja az Azure portálon keresztül vagy konfigurációs fájlok, a Linux-ügynökök kezelése.

### <a name="configure-syslog-in-the-azure-portal"></a>Konfigurálhatja a rendszernaplót az Azure-portálon
Konfigurálhatja a rendszernaplót a a [Naplóelemzés speciális beállításai adatok menüben](log-analytics-data-sources.md#configuring-data-sources).  Ebben a konfigurációban lévő minden egyes Linux-ügynök konfigurációs fájlt a rendszer.

Hozzáadhat egy új létesítményt a személyes, írja be a nevét, majd **+**.  Minden egyes létesítmény csak a kijelölt súlyosságokkal üzenetek gyűjtenek.  Ellenőrizze a súlyosságokkal számára az adott szolgáltatás, amellyel a szeretne gyűjteni.  Nem adhat meg további feltételeket üzenetek szűrésére.

![Konfigurálhatja a rendszernaplót](media/log-analytics-data-sources-syslog/configure.png)

Összes konfigurációs módosításhoz alapértelmezés szerint automatikusan leküldéssel az összes ügynököt.  Ha szeretné az egyes Linux-ügynök manuálisan konfigurálhatja a rendszernaplót, törölje a jelet *alkalmaz az alábbi konfiguráció a Linuxos gépeimre*.

### <a name="configure-syslog-on-linux-agent"></a>Konfigurálhatja a rendszernaplót a Linux-ügynök
Ha a [OMS-ügynök telepítve van egy Linux-ügyfél](log-analytics-linux-agents.md), egy alapértelmezett syslog-konfigurációs fájl, amely meghatározza a létesítmény és az üzenetek összegyűjtött súlyossága telepíti.  Ez a fájl és a konfiguráció módosítása módosíthatja.  A konfigurációs fájl eltér attól függően, hogy a Syslog démon, amely az ügyfél telepítve van.

> [!NOTE]
> Ha manuálisan szerkeszti a rendszernaplózás konfigurálásánál, a syslog démon a módosítások érvénybe lépéséhez újra kell indítani.
>
>

#### <a name="rsyslog"></a>Rsyslog
A konfigurációs fájl rsyslog itt található: **/etc/rsyslog.d/95-omsagent.conf**.  Alapértelmezett tartalmának alább látható.  Ez gyűjti az összes létesítményekben figyelmeztetés vagy magasabb szintű a helyi ügynök által küldött syslog-üzeneteket.

    kern.warning       @127.0.0.1:25224
    user.warning       @127.0.0.1:25224
    daemon.warning     @127.0.0.1:25224
    auth.warning       @127.0.0.1:25224
    syslog.warning     @127.0.0.1:25224
    uucp.warning       @127.0.0.1:25224
    authpriv.warning   @127.0.0.1:25224
    ftp.warning        @127.0.0.1:25224
    cron.warning       @127.0.0.1:25224
    local0.warning     @127.0.0.1:25224
    local1.warning     @127.0.0.1:25224
    local2.warning     @127.0.0.1:25224
    local3.warning     @127.0.0.1:25224
    local4.warning     @127.0.0.1:25224
    local5.warning     @127.0.0.1:25224
    local6.warning     @127.0.0.1:25224
    local7.warning     @127.0.0.1:25224

A létesítmény eltávolíthatja a szakasz a konfigurációs fájl eltávolításával.  A nyomkövetés alapjául szolgáló összegyűjtött egy adott helyen az adott létesítmény bejegyzés módosításával korlátozható.  Például a felhasználó-létesítményt a személyes üzenetek kiegészített egy hiba vagy magasabb szintű korlátozni ugyanúgy módosíthatja, hogy a konfigurációs fájl a következő sort:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>Syslog-ng
A syslog-ng konfigurációs fájlja a(z) **/etc/syslog-ng/syslog-ng.conf**.  Alapértelmezett tartalmának alább látható.  Ez gyűjti az összes és az összes súlyossági szintet használja a helyi ügynök által küldött syslog-üzeneteket.   

    #
    # Warnings (except iptables) in one file:
    #
    destination warn { file("/var/log/warn" fsync(yes)); };
    log { source(src); filter(f_warn); destination(warn); };

    #OMS_Destination
    destination d_oms { udp("127.0.0.1" port(25224)); };

    #OMS_facility = auth
    filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
    log { source(src); filter(f_auth_oms); destination(d_oms); };

    #OMS_facility = authpriv
    filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
    log { source(src); filter(f_authpriv_oms); destination(d_oms); };

    #OMS_facility = cron
    filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
    log { source(src); filter(f_cron_oms); destination(d_oms); };

    #OMS_facility = daemon
    filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
    log { source(src); filter(f_daemon_oms); destination(d_oms); };

    #OMS_facility = kern
    filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
    log { source(src); filter(f_kern_oms); destination(d_oms); };

    #OMS_facility = local0
    filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
    log { source(src); filter(f_local0_oms); destination(d_oms); };

    #OMS_facility = local1
    filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
    log { source(src); filter(f_local1_oms); destination(d_oms); };

    #OMS_facility = mail
    filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
    log { source(src); filter(f_mail_oms); destination(d_oms); };

    #OMS_facility = syslog
    filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
    log { source(src); filter(f_syslog_oms); destination(d_oms); };

    #OMS_facility = user
    filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };

A létesítmény eltávolíthatja a szakasz a konfigurációs fájl eltávolításával.  A nyomkövetés alapjául szolgáló eltávolítja azokat a listát az adott létesítmény összegyűjtött korlátozhatja.  Például a felhasználó-létesítményt a személyes csak a figyelmeztetési és a kritikus üzenetek korlátozásához ugyanúgy módosíthatja, hogy a konfigurációs fájl a következő részét:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>További Syslog portokat adatainak begyűjtése
Az OMS-ügynököt a helyi ügyfélen a porton 25224 Syslog-üzeneteket figyeli.  Ha az ügynök telepítve van, egy alapértelmezett Rendszernaplózás konfigurálásánál alkalmazza, a következő helyen található:

* Rsyslog: `/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng: `/etc/syslog-ng/syslog-ng.conf`

A portszám két konfigurációs fájlok létrehozásával módosíthatja: egy FluentD konfigurációs fájlt és egy rsyslog-vagy-syslog-ng attól függően, hogy a Syslog démon telepítését.  

* A FluentD konfigurációs fájlban található új fájlnak kell lennie: `/etc/opt/microsoft/omsagent/conf/omsagent.d` , és cserélje le az értéket a **port** az egyéni portszám rendelkező bejegyzés.

        <source>
          type syslog
          port %SYSLOG_PORT%
          bind 127.0.0.1
          protocol_type udp
          tag oms.syslog
        </source>
        <filter oms.syslog.**>
          type filter_syslog
        </filter>

* Rsyslog, létre kell hoznia található új konfigurációs fájl: `/etc/rsyslog.d/` és az értéket % SYSLOG_PORT % cserélje le az egyéni portszámot.  

    > [!NOTE]
    > Ha ezt az értéket a konfigurációs fájl módosítása `95-omsagent.conf`, ha az ügynök érvényes alapértelmezett konfiguráció felülírja.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* A syslog-ng config módosítani kell az alább látható példa konfiguráció másolásával és az egyéni módosított beállítások hozzáadása a syslog-ng.conf konfigurációs fájl végén található `/etc/syslog-ng/`.  Tegye **nem** használja az alapértelmezett címke **% WORKSPACE_ID % _oms** vagy **% WORKSPACE_ID_OMS**, adja meg az egyéni címkék segítségével különböztetheti meg egymástól a módosításokat.  

    > [!NOTE]
    > Ha módosítja a konfigurációs fájlban az alapértelmezett értékeket, akkor felülírja, ha az ügynök érvényes alapértelmezett konfiguráció.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

A módosításokat, a Syslog és az OMS-ügynököt befejezése után szolgáltatást újra kell indítani a konfigurációs módosítások életbe léptetéséhez.   

## <a name="syslog-record-properties"></a>Syslog rekord tulajdonságai
Syslog-rekordok típusa lehet **Syslog** , és a tulajdonságok az alábbi táblázatban.

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Az esemény gyűjtötte a program a számítógép. |
| Létesítmény |Határozza meg az üzenetet generált a rendszer része. |
| HostIP |A rendszer az üzenetet küldő IP-címe. |
| Állomásnév |A rendszer az üzenetet küldő neve. |
| SeverityLevel |Az esemény súlyosságának. |
| SyslogMessage |Az üzenet szövegét. |
| Folyamatazonosító |A folyamat által generált üzenet azonosítója. |
| eventTime |Dátum és idő, az esemény lett létrehozva. |

## <a name="log-queries-with-syslog-records"></a>Syslog-rekordot tartalmazó napló lekérdezések
Az alábbi táblázat példákat különböző Syslog lehívása napló lekérdezések.

| Lekérdezés | Leírás |
|:--- |:--- |
| Rendszernapló |Minden rendszerbejegyzések. |
| Syslog &#124; adott súlyossági szint == "error" |Az összes Syslog rekordot hiba súlyosságát. |
| Syslog &#124; AggregatedValue összefoglalója = count() számítógépenként |Számítógép által bejegyzések száma a Syslog. |
| Syslog &#124; AggregatedValue összefoglalója létesítmény által count() = |A Syslog száma rekordok létesítmény szerint. |

## <a name="next-steps"></a>További lépések
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) az adatforrások és a megoldások gyűjtött adatok elemzésére.
* Használjon [egyéni mezők](log-analytics-custom-fields.md) syslog rekordokban levő adatok elemzése az egyes mezőkbe.
* [Linux-ügynökök konfigurálása](log-analytics-linux-agents.md) más típusú adatok gyűjtéséhez.
