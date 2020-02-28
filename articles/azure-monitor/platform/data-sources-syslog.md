---
title: Syslog-üzenetek összegyűjtése és elemzése a Azure Monitorban | Microsoft Docs
description: Syslog-esemény naplózása protokoll, amely Linux közös. Ez a cikk ismerteti, hogyan konfigurálhatja a syslog-üzenetek gyűjteményét Log Analytics és a létrehozott rekordok részleteit.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/22/2019
ms.openlocfilehash: 8d68a8d6d28d79c50a92cd2d18df2abab26c30ec
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670491"
---
# <a name="syslog-data-sources-in-azure-monitor"></a>Syslog-adatforrások a Azure Monitorban
Syslog-esemény naplózása protokoll, amely Linux közös. Alkalmazások küld üzeneteket, amelyek a helyi számítógépen tárolt vagy a Syslog-gyűjtő lett elküldve. A Linux rendszerhez készült Log Analytics-ügynök telepítésekor a helyi syslog démont úgy konfigurálja, hogy továbbítsa az üzeneteket az ügynöknek. Az ügynök ezután elküldi az üzenetet, hogy Azure Monitor, ahol létrejön egy megfelelő rekord.  

> [!NOTE]
> Azure Monitor támogatja a rsyslog vagy syslog-ng által küldött üzenetek gyűjteményét, ahol a rsyslog az alapértelmezett démon. Red Hat Enterprise Linux, CentOS és Oracle Linux-verzió (sysklog) 5-ös verzióját az alapértelmezett syslog démon nem támogatott a syslog-események gyűjtése. A rendszernapló-adatok ezen disztribúciók ezen verziójából való összegyűjtéséhez a [rsyslog démont](http://rsyslog.com) telepíteni és konfigurálni kell a sysklog lecserélése érdekében.
>
>

![A rendszernaplók gyűjtése](media/data-sources-syslog/overview.png)

A syslog-gyűjtő a következő létesítményeket támogatja:

* egalizálás
* Felhasználó
* mail
* démon
* Auth
* syslog
* LPR
* Hírek
* UUCP
* cron
* authpriv
* FTP
* local0 – local7

Bármilyen más létesítmény esetében [konfigurálja az egyéni naplók adatforrását](data-sources-custom-logs.md) Azure monitor.
 
## <a name="configuring-syslog"></a>Syslog konfigurálása
A Linux rendszerhez készült Log Analytics-ügynök csak a konfigurációjában megadott létesítményekkel és megszakításokkal gyűjt eseményeket. Syslog konfigurálhatja az Azure Portalon keresztül, vagy konfigurációs fájlokat a Linux-ügynökök kezelése.

### <a name="configure-syslog-in-the-azure-portal"></a>Syslog konfigurálása az Azure Portalon
Konfigurálja a syslog-t a [Speciális beállítások adatok menüjéből](agent-data-sources.md#configuring-data-sources). Ez a konfiguráció minden egyes Linux-ügynök a konfigurációs fájlt érkeznek.

Új létesítmény hozzáadásához először válassza az **alábbi konfiguráció alkalmazása a saját gépekre** lehetőséget, majd írja be a nevét, és kattintson a **+** elemre. Minden funkció esetében csak a kiválasztott súlyossági szinten pedig a üzenetek összegyűjtött.  Ellenőrizze a súlyossági szint esetén csak az adott létesítmény gyűjteni kívánt számára. Szűrő üzenetek további feltételeket nem tud biztosítani.

![Syslog konfigurálása](media/data-sources-syslog/configure.png)

Alapértelmezés szerint az összes konfigurációs módosítást automatikusan leküld az összes ügynököt. Ha minden Linux-ügynökön manuálisan szeretné konfigurálni a syslog-t, törölje a jelet az *alábbi konfiguráció alkalmazása a saját gépekre*jelölőnégyzetből.

### <a name="configure-syslog-on-linux-agent"></a>Syslog konfigurálása Linux-ügynök
Ha a [log Analytics-ügynök Linux-ügyfélre van telepítve](../../azure-monitor/learn/quick-collect-linux-computer.md), egy alapértelmezett syslog-konfigurációs fájlt telepít, amely meghatározza a begyűjtött üzenetek létesítményét és súlyosságát. Ehhez a fájlhoz, a konfiguráció módosítása módosíthatja. A konfigurációs fájl attól függően változik, a Syslog-démont, amely az ügyfél telepítve.

> [!NOTE]
> Ha szerkeszti a syslog-konfiguráció, a syslog démon a módosítások érvénybe léptetéséhez újra kell indítani.
>
>

#### <a name="rsyslog"></a>rsyslog
A rsyslog konfigurációs fájlja a következő helyen található: **/etc/rsyslog.d/95-omsagent.conf**. Alább láthatók a saját alapértelmezett tartalmát. Ez a figyelmeztetés vagy magasabb szintű minden lehetőséget a helyi ügynök által küldött syslog-üzeneteket gyűjti.

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

Eltávolíthatja a létesítmény eltávolítása a konfigurációs fájl vonatkozó szakaszát. A súlyossági szint esetén csak az adott létesítmény módosítása az adott létesítmény bejegyzés által gyűjtött korlátozhatja. Például korlátozhatja a felhasználói üzenetek adatcserével rendelkező létesítményben egy hiba vagy magasabb szintű súlyosság kell módosítania a konfigurációs fájl a következő sort:

    user.error    @127.0.0.1:25224


#### <a name="syslog-ng"></a>Syslog-ng
A syslog-ng konfigurációs fájlja a következő helyen található: **/etc/syslog-ng/syslog-ng.conf**.  Alább láthatók a saját alapértelmezett tartalmát. Ez a syslog-üzeneteket küldött összes létesítmények és az összes súlyossági szinten pedig a helyi ügynök gyűjti.   

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

Eltávolíthatja a létesítmény eltávolítása a konfigurációs fájl vonatkozó szakaszát. Korlátozhatja a súlyossági szint esetén csak az adott létesítmény gyűjtött ehhez távolítsa el a listából.  Például korlátozhatja a felhasználói képessége csak figyelmeztetési és kritikus fontosságú üzenetek, ugyanúgy módosíthatja, hogy a konfigurációs fájl a következő szakaszban:

    #OMS_facility = user
    filter f_user_oms { level(alert,crit) and facility(user); };
    log { source(src); filter(f_user_oms); destination(d_oms); };


### <a name="collecting-data-from-additional-syslog-ports"></a>Adatgyűjtés a további Syslog-portok
A Log Analytics ügynök a 25224-as porton a helyi ügyfélen figyeli a syslog-üzeneteket.  Az ügynök telepítve van, ha syslog alapértelmezett konfigurációja a alkalmazni, és a következő helyen található:

* Rsyslog: `/etc/rsyslog.d/95-omsagent.conf`
* Syslog-ng: `/etc/syslog-ng/syslog-ng.conf`

A portszám két konfigurációs fájlok létrehozásával módosíthatja: egy FluentD konfigurációs fájl és a egy rsyslog – vagy – syslog-ng fájl függően a Syslog démon telepítette.  

* A Fluent konfigurációs fájlnak egy új fájlnak kell lennie, amely a ben található: `/etc/opt/microsoft/omsagent/conf/omsagent.d`, majd cserélje le a **port** bejegyzésben szereplő értéket az egyéni portszámra.

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

* A rsyslog hozzon létre egy új konfigurációs fájlt a következő helyen: `/etc/rsyslog.d/`, és cserélje le a (z)% SYSLOG_PORT% értéket az egyéni portszámra.  

    > [!NOTE]
    > Ha módosítja ezt az értéket a `95-omsagent.conf`konfigurációs fájlban, akkor a rendszer felülírja, ha az ügynök alapértelmezett konfigurációt alkalmaz.
    >

        # OMS Syslog collection for workspace %WORKSPACE_ID%
        kern.warning              @127.0.0.1:%SYSLOG_PORT%
        user.warning              @127.0.0.1:%SYSLOG_PORT%
        daemon.warning            @127.0.0.1:%SYSLOG_PORT%
        auth.warning              @127.0.0.1:%SYSLOG_PORT%

* A syslog-ng konfigurációt úgy kell módosítani, hogy átmásolja az alább látható példa konfigurációt, és hozzáadja az egyéni módosított beállításokat a syslog-ng. conf konfigurációs fájl végéhez `/etc/syslog-ng/`található. Ne **használja a** **(z)% WORKSPACE_ID% _oms** vagy a **(z)% WORKSPACE_ID_OMS**alapértelmezett címkét, adjon meg egy egyéni címkét a módosítások megkülönböztetése érdekében.  

    > [!NOTE]
    > Ha módosítja az alapértelmezett értékeket a konfigurációs fájlban, ezek felülírja, ha az ügynök alapértelmezett konfigurációja a érvényes.
    >

        filter f_custom_filter { level(warning) and facility(auth; };
        destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
        log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };

A módosítások elvégzése után újra kell indítani a syslog és a Log Analytics Agent szolgáltatást, hogy a konfigurációs változások életbe léptetése megtörténjen.   

## <a name="syslog-record-properties"></a>Syslog-rekord tulajdonságai
A syslog-rekordok rendelkeznek **syslog** típussal, és rendelkeznek a következő táblázatban szereplő tulajdonságokkal.

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Az esemény gyűjtötte a program a számítógép. |
| Szolgáltatás |A rendszer által generált üzenet részeként határozza meg. |
| HostIP |A rendszer az üzenetet küldő IP-címe. |
| Állomásnév |A rendszer az üzenetet küldő neve. |
| Súlyossági szint |Az esemény súlyossági szintje. |
| SyslogMessage |Az üzenet szövege. |
| ProcessID |A folyamat által generált üzenet azonosítója. |
| eventTime |Dátum és idő, amelyik az esemény jött létre. |

## <a name="log-queries-with-syslog-records"></a>Syslog-rekord log lekérdezéseket.
Az alábbi táblázat a rekordok Syslog lekérő lekérdezések log különböző példákat.

| Lekérdezés | Leírás |
|:--- |:--- |
| Rendszernapló |Minden Syslog. |
| Syslog &#124; , err == "error" |Minden Syslog-rekord a hiba súlyossága |
| Syslog &#124; summarize AggregatedValue = count() összegzése számítógép szerint |A Syslog-Rekordok számlálása számítógép szerint. |
| Syslog &#124; summarize AggregatedValue = count() by létesítmény |Száma a Syslog-rekord létesítmény szerint. |

## <a name="next-steps"></a>Következő lépések
* További információ az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez szükséges [naplók lekérdezéséről](../../azure-monitor/log-query/log-query-overview.md) .
* [Egyéni mezők](../../azure-monitor/platform/custom-fields.md) használatával elemezheti az adatokat a syslog-rekordokból az egyes mezőkbe.
* [Linux-ügynökök konfigurálása](../../azure-monitor/learn/quick-collect-linux-computer.md) más típusú adatok gyűjtéséhez.
