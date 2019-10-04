---
title: A Palo Alto Networks-adatkapcsolatok és az Azure Sentinel összekötése | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztathatók a Palo Alto-hálózatok az Azure Sentinel szolgáltatáshoz.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 659f36a036d8a165b0c2b28830ae2312adb56c56
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240239"
---
# <a name="connect-your-palo-alto-networks-appliance"></a>A Palo Alto Networks-berendezés összekötése



Az Azure Sentinel bármely Palo Alto Network-készülékhez csatlakoztatható, ha a naplófájlokat syslog Common Error Format (CEF) formátumban menti. Az Azure Sentinel szolgáltatással való integráció lehetővé teszi, hogy könnyedén futtasson elemzéseket és lekérdezéseket a Palo Alto hálózatokból származó naplófájlokban tárolt adatok között. További információ arról, hogy az Azure Sentinel hogyan tölt be CEF-adatokat, lásd: [CEF-készülékek összekapcsolása](connect-common-event-format.md).

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="step-1-connect-your-palo-alto-networks-appliance-using-an-agent"></a>1\. lépés: A Palo Alto Networks-berendezés összekötése ügynök használatával

A Palo Alto Networks-berendezés az Azure Sentinelhez való összekapcsolásához telepítenie kell egy ügynököt egy dedikált gépre (VM vagy helyszíni) a készülék és az Azure Sentinel közötti kommunikáció támogatásához. 

Azt is megteheti, hogy manuálisan telepítheti az ügynököt egy meglévő Azure-beli virtuális gépre, egy másik felhőben lévő virtuális gépre vagy egy helyszíni gépre.

> [!NOTE]
> Győződjön meg arról, hogy a cég biztonsági szabályzata szerint konfigurálja a gép biztonságát. Konfigurálhatja például a hálózatot úgy, hogy az megfeleljen a vállalati hálózati biztonsági házirendnek, és módosítsa a démon portjait és protokollait úgy, hogy azok megfeleljenek a követelményeinek. 

Ha mindkét lehetőséghez hálózati diagramot szeretne látni, [](connect-data-sources.md#agent-options)tekintse meg az adatforrások összekapcsolását ismertető témakört.


### <a name="deploy-the-agent"></a>Az ügynök üzembe helyezése 

1. Az Azure Sentinel-portálon kattintson az **adatösszekötők** lehetőségre, válassza a **Palo Alto Networks** elemet, majd **nyissa meg az összekötő lapot**. 

1. **A syslog-ügynök letöltése és telepítése**alatt válassza ki a gép típusát, akár az Azure-t, akár a helyszínen. 
1. A megnyíló **virtuális gépek** képernyőn válassza ki a használni kívánt gépet, és kattintson a **Kapcsolódás**gombra.
1. Ha az **ügynök letöltése és telepítése Azure Linux rendszerű virtuális gépekhez**lehetőséget választja, válassza ki a gépet, és kattintson a **kapcsolat**elemre. Ha az **ügynök letöltése és telepítése nem Azure-beli Linux rendszerű virtuális gépekhez**lehetőséget választotta, a **közvetlen ügynök** képernyőjén futtassa a parancsfájlt a letöltés és a bevezetési **ügynök a Linux rendszerhez**című szakaszban.      1. Az összekötő képernyőjén a **syslog konfigurálása és továbbítása**területen állítsa be, hogy a syslog démon **rsyslog. d** vagy **syslog-ng**. 
1. Másolja ezeket a parancsokat, és futtassa őket a készüléken:
     - Ha a rsyslog. d elemet választotta:
              
       1. Tájékoztassa a syslog démont, hogy figyelje a létesítmény local_4, és küldje el a syslog-üzeneteket az Azure Sentinel-ügynöknek az 25226-es port használatával. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
       2. Töltse le és telepítse a [security_events konfigurációs fájlt](https://aka.ms/asi-syslog-config-file-linux) , amely a syslog-ügynököt konfigurálja a 25226-es porton való figyeléshez. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Hol {0} kell helyettesíteni a munkaterület GUID azonosítóját.
            
       1. A syslog démon újraindítása`sudo service rsyslog restart`
             
    - Ha a syslog-ng beállítást választotta:

         1. Tájékoztassa a syslog démont, hogy figyelje a létesítmény local_4, és küldje el a syslog-üzeneteket az Azure Sentinel-ügynöknek az 25226-es port használatával. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
         2. Töltse le és telepítse a [security_events konfigurációs fájlt](https://aka.ms/asi-syslog-config-file-linux) , amely a syslog-ügynököt konfigurálja a 25226-es porton való figyeléshez. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Hol {0} kell helyettesíteni a munkaterület GUID azonosítóját.

         3. A syslog démon újraindítása`sudo service syslog-ng restart`
 1. Indítsa újra a syslog-ügynököt a következő parancs használatával:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
 1. A következő parancs futtatásával ellenőrizze, hogy nincsenek-e hibák az ügynök naplójában:`tail /var/opt/microsoft/omsagent/log/omsagent.log`


 
## <a name="step-2-forward-palo-alto-networks-logs-to-the-syslog-agent"></a>2\. lépés: A Palo Alto hálózati naplók továbbítása a syslog-ügynökbe

A Palo Alto-hálózatok konfigurálása a syslog-üzenetek CEF formátumban való továbbításához az Azure-munkaterületen a syslog-ügynök használatával:
1.  Nyissa meg a [Common Event Format (CEF) konfigurációs útmutatóit](https://docs.paloaltonetworks.com/resources/cef) , és töltse le a készülék típusának PDF-fájlját. A CEF események gyűjtéséhez kövesse az útmutató utasításait a Palo Alto Networks-berendezés beállításához. 

1.  Lépjen a [syslog-figyelés konfigurálása](https://aka.ms/asi-syslog-paloalto-forwarding) elemre, és kövesse a 2. és a 3. lépést a Palo Alto Networks-készülékről az Azure sentinelre történő CEF-továbbítás konfigurálásához.

    1. Győződjön meg arról, hogy a **syslog-kiszolgáló formátumát** a **BSD**értékre állítja be.
    1. Ügyeljen arra, hogy a **rendelkezésre állási számot** a syslog-ügynökön beállított értékre állítsa be.

       > [!NOTE]
       > Előfordulhat, hogy a PDF-fájl másolási/beillesztési műveletei megváltoztatják a szöveget, és véletlenszerű karaktereket szúrnak be. Ennek elkerüléséhez másolja a szöveget egy Szerkesztőbe, és távolítson el minden olyan karaktert, amely megtöri a napló formátumát a beillesztés előtt, ahogy az ebben a példában látható.
 
        ![CEF szöveges másolási probléma](./media/connect-cef/paloalto-text-prob1.png)

2. Ha a Log Analytics vonatkozó sémát szeretné használni a Palo Alto Networks-eseményekhez, keresse meg a **CommonSecurityLog**.

## <a name="step-3-validate-connectivity"></a>3\. lépés: Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 

1. Győződjön meg arról, hogy a megfelelő eszközt használja. A létesítménynek meg kell egyeznie a készüléken és az Azure Sentinelben. Megtekintheti, hogy melyik Facility-fájlt használja az Azure Sentinelben, és hogyan módosíthatja `security-config-omsagent.conf`azt a fájlban. 

2. Győződjön meg arról, hogy a naplók a syslog-ügynök jobb portjára kerülnek. Futtassa ezt a parancsot a syslog-ügynök gépén: `tcpdump -A -ni any  port 514 -vv`Ez a parancs megjeleníti azokat a naplókat, amelyek az eszközről a syslog-gépre mutatnak. Győződjön meg arról, hogy a megfelelő porton és a megfelelő eszközön érkeznek naplók a forrás készülékről.

3. Győződjön meg arról, hogy a küldött naplók megfelelnek az [RFC 3164](https://tools.ietf.org/html/rfc3164)-nek.

4. A syslog-ügynököt futtató számítógépen győződjön meg róla, hogy a (z) 514-es, 25226-as `netstat -a -n:`portok nyitva és figyelve vannak a parancs használatával. További információ a parancs használatáról: [netstat (8) – Linux man oldal](https://linux.die.net/man/8/netstat). Ha a figyelő megfelelően működik, a következőt fogja látni:

   ![Azure Sentinel-portok](./media/connect-cef/ports.png) 

5. Győződjön meg arról, hogy a démon úgy van beállítva, hogy figyelje a 514-es portot, amelyen a naplókat küldi.
    - Rsyslog esetén:<br>Győződjön meg arról, hogy `/etc/rsyslog.conf` a fájl tartalmazza ezt a konfigurációt:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      További információkért lásd [: imudp: UDP syslog bemeneti modul](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) és [imtcp: TCP syslog bemeneti modul](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module)

   - Syslog-ng esetén:<br>Győződjön meg arról, hogy `/etc/syslog-ng/syslog-ng.conf` a fájl tartalmazza ezt a konfigurációt:

           # source s_network {
            network( transport(UDP) port(514));
             };
     További információ: [syslog-ng nyílt forráskódú kiadás 3,16 – felügyeleti útmutató](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Győződjön meg arról, hogy van-e kommunikáció a syslog démon és az ügynök között. Futtassa ezt a parancsot a syslog-ügynök gépén: `tcpdump -A -ni any  port 25226 -vv`Ez a parancs megjeleníti azokat a naplókat, amelyek az eszközről a syslog-gépre mutatnak. Győződjön meg arról, hogy a naplók is érkeznek az ügynökön.

6. Ha mindkét parancs sikeres eredményt adott, ellenőrizze, hogy a naplók érkeznek-e a Log Analytics. Az ezekről a készülékekről továbbított összes esemény nyers formában jelenik meg log Analytics `CommonSecurityLog` a típus területen.

7. Ellenőrizze, hogy vannak-e hibák, vagy ha a naplók nem érkeznek meg, `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`tekintse meg a következőt:. Ha azt szeretné, hogy a napló formátuma nem felel meg a `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` hibáknak, ugorjon `security_events.conf`a fájlra, és ellenőrizze, hogy a naplók megfelelnek-e a fájlban látható regex-formátumnak.

8. Győződjön meg arról, hogy a syslog-üzenet alapértelmezett mérete 2048 bájtra van korlátozva (2 KB). Ha a naplók túl hosszúak, frissítse a security_events. conf fájlt a következő parancs használatával:`message_length_limit 4096`








## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a Palo Alto hálózati berendezések az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).

