---
title: Syslog-üzenetek gyűjtése és elemzése az Azure Monitorban | Microsoft dokumentumok
description: A Syslog egy olyan eseménynaplózási protokoll, amely a Linux közös. Ez a cikk ismerteti, hogyan konfigurálhatja a Syslog üzenetek gyűjteményét a Log Analytics szolgáltatásban, és az általuk létrehozott rekordok részleteit.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/22/2019
ms.openlocfilehash: 8d68a8d6d28d79c50a92cd2d18df2abab26c30ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274722"
---
# <a name="syslog-data-sources-in-azure-monitor"></a>Rendszernapló-adatforrások az Azure Monitorban
A Syslog egy olyan eseménynaplózási protokoll, amely a Linux közös. Az alkalmazások olyan üzeneteket küldenek, amelyek a helyi számítógépen tárolhatók, vagy egy Syslog gyűjtőnek kézbesíthetők. A Linuxos Log Analytics-ügynök telepítésekor a helyi Syslog démont konfigurálja az ügynöknek küldött üzenetek továbbítására. Az ügynök ezután elküldi az üzenetet az Azure Monitornak, ahol a megfelelő rekord jön létre.  

> [!NOTE]
> Az Azure Monitor támogatja a rsyslog vagy syslog-ng által küldött üzenetek gyűjteményét, ahol a rsyslog az alapértelmezett démon. A Red Hat Enterprise Linux, CentOS és Oracle Linux verzió (sysklog) 5-ös verziójának alapértelmezett syslog démonja nem támogatott a syslog eseménygyűjteményesetében. A disztribúciók ezen verziójából származó syslog adatok gyűjtéséhez a [rsyslog démont](http://rsyslog.com) telepíteni kell, és úgy kell konfigurálni, hogy cserélje ki a sysklog-ot.
>
>

![Syslog gyűjtemény](media/data-sources-syslog/overview.png)

A Syslog gyűjtő a következő létesítményeket támogatja:

* Kern
* felhasználó!
* Levelezés
* Daemon
* Auth
* syslog
* Lpr
* news
* Uucp
* Cron
* authpriv között
* ftp
* helyi0-helyi7

Bármely más létesítmény, [konfigurálja az egyéni naplók adatforrás az](data-sources-custom-logs.md) Azure Monitorban.
 
## <a name="configuring-syslog"></a>A Syslog konfigurálása
A Log Analytics-ügynök Linux csak gyűjt eseményeket a konfigurációban megadott létesítményekkel és végtartamokkal. A Syslog konfigurálható az Azure Portalon keresztül, vagy a konfigurációs fájlok kezelése a Linux-ügynökök.

### <a name="configure-syslog-in-the-azure-portal"></a>A Syslog konfigurálása az Azure Portalon
Állítsa be a Syslog konfigurálását az [Adatok menü Speciális beállítások menüjében.](agent-data-sources.md#configuring-data-sources) Ez a konfiguráció minden Linux-ügynök konfigurációs fájljába kerül.

Új létesítményt úgy adhat hozzá, hogy először kiválasztja az Alábbi konfiguráció alkalmazása **+** a **gépeimre** lehetőséget, majd írja be a nevét, és kattintson a lehetőségre. Minden létesítmény, csak üzeneteket a kiválasztott végtartamot kell gyűjteni.  Ellenőrizze a szedésaz adott létesítmény, hogy szeretné gyűjteni. Az üzenetek szűréséhez nem adhat meg további feltételeket.

![Syslog konfigurálása](media/data-sources-syslog/configure.png)

Alapértelmezés szerint az összes konfigurációs módosítás automatikusan lekerül az összes ügyintézőre. Ha a Syslog-ot manuálisan szeretné konfigurálni minden Linux-ügynökön, akkor törölje a jelet az *Alábbi konfiguráció alkalmazása a gépeimre*jelölőnégyzetből.

### <a name="configure-syslog-on-linux-agent"></a>Syslog konfigurálása Linux-ügynökön
Amikor a [Log Analytics-ügynök linuxos ügyfélre van telepítve,](../../azure-monitor/learn/quick-collect-linux-computer.md)egy alapértelmezett syslog konfigurációs fájlt telepít, amely meghatározza az összegyűjtött üzenetek megfelelőságát és súlyosságát. A konfiguráció módosításához módosíthatja ezt a fájlt. A konfigurációs fájl az ügyfél által telepített Syslog démontól függően eltérő.

> [!NOTE]
> Ha módosítja a syslog konfigurációt, újra kell indítania a syslog démont a módosítások érvénybe léptetéséhez.
>
>

#### <a name="rsyslog"></a>rsyslog között
Az rsyslog konfigurációs fájlja az **/etc/rsyslog.d/95-omsagent.conf**. Az alapértelmezett tartalom alább látható. Ez összegyűjti a helyi ügynöktől küldött syslog üzeneteket az összes vagy magasabb szintű figyelmeztetéssel rendelkező létesítményhez.

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

A létesítmény ta- Korlátozhatja az adott létesítményre vonatkozóan gyűjtött szedési állságot a létesítmény bejegyzésének módosításával. Ha például a felhasználói eszközt a hiba súlyosságú vagy annál magasabb típusú üzenetekre szeretné korlátozni, akkor a konfigurációs fájl nak ezt a sorát a következőkre kell módosítani:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>syslog-ng
A syslog-ng konfigurációs fájlja az **/etc/syslog-ng/syslog-ng.conf**helyen található.  Az alapértelmezett tartalom alább látható. Ez összegyűjti syslog küldött üzeneteket a helyi ügynök minden létesítmény és minden severities.   

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

A létesítmény ta- Korlátozhatja az adott létesítményhez gyűjtött szedési állságot, ha eltávolítja őket a listáról.  Ha például a felhasználói eszközt csak riasztási és kritikus üzenetekre szeretné korlátozni, a konfigurációs fájl adott szakaszát a következőkre kell módosítani:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Adatok gyűjtése további Syslog portokról
A Log Analytics-ügynök figyeli a Syslog üzeneteket a helyi ügyfél 25224-es porton.  Az ügynök telepítésekor a rendszer egy alapértelmezett syslog konfigurációt alkalmaz, és a következő helyen található:

* Rsyslog:`/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng:`/etc/syslog-ng/syslog-ng.conf`

A portszámot két konfigurációs fájl létrehozásával módosíthatja: egy FluentD konfigurációs fájlt és egy rsyslog-or-syslog-ng fájlt a telepített Syslog démontól függően.  

* A FluentD konfigurációs fájlnak egy új `/etc/opt/microsoft/omsagent/conf/omsagent.d` fájlnak kell lennie: és le kell cserélnie a **portbejegyzés** értékét az egyéni portszámra.

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

* Az rsyslog esetében hozzon létre egy `/etc/rsyslog.d/` új konfigurációs fájlt a következő helyen, és cserélje le a(z) %SYSLOG_PORT% értéket az egyéni portszámra.  

    > [!NOTE]
    > Ha módosítja ezt az `95-omsagent.conf`értéket a konfigurációs fájlban, a rendszer felülírja, amikor az ügynök alapértelmezett konfigurációt alkalmaz.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* A syslog-ng konfigurációt úgy kell módosítani, hogy az alábbi példakonfigurációt másolja, és hozzáadja az egyéni módosított `/etc/syslog-ng/`beállításokat a syslog-ng.conf konfigurációs fájl végéhez. **Ne** használja az alapértelmezett címkét **%WORKSPACE_ID%_oms** vagy **%WORKSPACE_ID_OMS**, adjon meg egyéni címkét a módosítások megkülönböztetésére.  

    > [!NOTE]
    > Ha módosítja a konfigurációs fájl alapértelmezett értékeit, azok felülíródnak, amikor az ügynök alapértelmezett konfigurációt alkalmaz.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

A módosítások befejezése után a Syslog és a Log Analytics-ügynök szolgáltatás újra kell indítani, hogy a konfigurációs módosítások érvénybe lépnek.   

## <a name="syslog-record-properties"></a>Syslog rekord tulajdonságai
A Syslog rekordok **syslog típusúak,** és az alábbi táblázatban található tulajdonságokkal rendelkeznek.

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Számítógép, amelyből az eseményt gyűjtötték. |
| Létesítmény |A rendszer nek azt a részét határozza meg, amely az üzenetet létrehozta. |
| HostIP |Az üzenetet küldő rendszer IP-címe. |
| HostName |Az üzenetet küldő rendszer neve. |
| Súlyossági szint |Az esemény súlyossági szintje. |
| SyslogMessage üzenet |Az üzenet szövege. |
| ProcessID |Az üzenetet létrehozó folyamat azonosítója. |
| EventTime (Eseményidő) |Az esemény létrehozásának dátuma és időpontja. |

## <a name="log-queries-with-syslog-records"></a>Lekérdezések naplózása Syslog-rekordokkal
Az alábbi táblázat különböző példákat tartalmaz a Syslog rekordokat lekérdező naplólekérdezésekre.

| Lekérdezés | Leírás |
|:--- |:--- |
| Rendszernapló |Minden Syslogs. |
| Syslog &#124; ahol severitylevel == "hiba" |Minden Syslog rekord a hiba súlyosságával. |
| Syslog &#124; összegezi az AggregatedValue = count() számítógép szerint |A Syslog-rekordok száma számítógépenként. |
| Syslog &#124; összegezheti az AggregatedValue = count() -t létesítmény szerint |Syslog-rekordok száma létesítmény szerint. |

## <a name="next-steps"></a>További lépések
* Ismerje meg a [naplólekérdezéseket](../../azure-monitor/log-query/log-query-overview.md) az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez.
* [Az Egyéni mezők](../../azure-monitor/platform/custom-fields.md) segítségével a syslog rekordok adatait egyes mezőkké elemezheti.
* [Konfigurálja a Linux-ügynököket](../../azure-monitor/learn/quick-collect-linux-computer.md) más típusú adatok gyűjtésére.
