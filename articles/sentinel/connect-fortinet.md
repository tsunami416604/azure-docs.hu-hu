---
title: Fortinet-adatkapcsolatok összekötése az Azure Sentinel előzetes verziójával | Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat a Fortinet-adatbázisokhoz az Azure Sentinel szolgáltatáshoz.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: 339b8c1b59720989016f68fdb94fae30c26b42f0
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68679285"
---
# <a name="connect-your-fortinet-appliance"></a>A Fortinet-berendezés összekötése

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk. Nem ajánlott éles környezetben üzemelő számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Sentinel bármely Fortinet-készülékhez csatlakoztatható, ha a naplófájlokat syslog Common Event Format (CEF) formátumban menti. Az Azure Sentinel szolgáltatással való integráció révén könnyedén futtathatja az elemzéseket és a lekérdezéseket a naplófájlokban tárolt adatok között a Fortinet. További információ arról, hogy az Azure Sentinel hogyan tölt be CEF-adatokat, lásd: [CEF-készülékek](connect-common-event-format.md)összekapcsolása.

> [!NOTE]
> Az adattárolást annak a munkaterületnek a földrajzi helye tárolja, amelyen az Azure Sentinel alkalmazást futtatja.

## <a name="step-1-connect-your-fortinet-appliance-by-using-an-agent"></a>1\. lépés: Fortinet-berendezés összekötése ügynök használatával

Ha a Fortinet-berendezést az Azure Sentinelhez szeretné kapcsolni, telepítsen egy ügynököt egy dedikált virtuális gépre vagy egy helyszíni gépre a készülék és az Azure Sentinel közötti kommunikáció támogatásához. 

Az ügynököt manuálisan is üzembe helyezheti egy meglévő Azure-beli virtuális gépen, egy másik felhőben lévő virtuális gépen vagy egy helyszíni gépen.

> [!NOTE]
> Győződjön meg arról, hogy a cég biztonsági szabályzata szerint konfigurálja a gép biztonságát. Konfigurálhatja például a hálózatot úgy, hogy az megfeleljen a vállalati hálózati biztonsági házirendnek, és módosítsa a démon portjait és protokollait úgy, hogy azok megfeleljenek a követelményeinek. 

Ha mindkét lehetőséghez hálózati diagramot szeretne látni, [](connect-data-sources.md#agent-options)tekintse meg az adatforrások összekapcsolását ismertető témakört.

### <a name="deploy-the-agent"></a>Az ügynök üzembe helyezése

1. Az Azure Sentinel portálon kattintson az adatösszekötők elemre, és válassza a **Fortinet** , majd az **összekötő lap megnyitása**lehetőséget. 

1. **A syslog-ügynök letöltése és telepítése**alatt válassza ki a gép típusát, akár az Azure-t, akár a helyszínen. 
1. A megnyíló **virtuális gépek** képernyőn válassza ki a használni kívánt gépet, és kattintson a **Kapcsolódás**gombra.
1. Ha az **ügynök letöltése és telepítése Azure Linux rendszerű virtuális gépekhez**lehetőséget választja, válassza ki a gépet, és kattintson a **kapcsolat**elemre. Ha az **ügynök letöltése és telepítése nem Azure-beli Linux rendszerű virtuális gépekhez**lehetőséget választotta, a **közvetlen ügynök** képernyőjén futtassa a parancsfájlt a letöltés és a bevezetési **ügynök a Linux rendszerhez**című szakaszban.
1. Az összekötő képernyőjén a **syslog konfigurálása és továbbítása**területen állítsa be, hogy a syslog démon **rsyslog. d** vagy **syslog-ng**. 
1. Másolja ezeket a parancsokat, és futtassa őket a készüléken:
   - Ha a rsyslog. d elemet választotta:
            
     1. Tájékoztassa a syslog démont, hogy figyelje a létesítmény local_4, és küldje el a syslog-üzeneteket az Azure Sentinel-ügynöknek az 25226-es port használatával. Használja ezt a parancsot:`sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
     1. Töltse le és telepítse a [security_events konfigurációs fájlt](https://aka.ms/asi-syslog-config-file-linux) , amely a syslog-ügynököt konfigurálja a 25226-es porton való figyeléshez. Használja ezt a parancsot `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` : {0} hol kell cserélni a munkaterület GUID azonosítóját.
     1. Indítsa újra a syslog démont a következő parancs használatával:`sudo service rsyslog restart`
            
   - Ha a syslog-ng beállítást választotta:

      1. Tájékoztassa a syslog démont, hogy figyelje a létesítmény local_4, és küldje el a syslog-üzeneteket az Azure Sentinel-ügynöknek az 25226-es port használatával. Használja ezt a parancsot:`sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      1. Töltse le és telepítse a [security_events konfigurációs fájlt](https://aka.ms/asi-syslog-config-file-linux) , amely a syslog-ügynököt konfigurálja a 25226-es porton való figyeléshez. Használja ezt a parancsot `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` : {0} hol kell cserélni a munkaterület GUID azonosítóját.
      1. Indítsa újra a syslog démont a következő parancs használatával:`sudo service syslog-ng restart`
1. Indítsa újra a syslog-ügynököt a következő parancs használatával:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. A következő parancs futtatásával ellenőrizze, hogy nincsenek-e hibák az ügynök naplójában:`tail /var/opt/microsoft/omsagent/log/omsagent.log`

 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>2\. lépés: Fortinet-naplók továbbítása a syslog-ügynökhöz

Konfigurálja a Fortinet a syslog-üzenetek CEF formátumban való továbbításához az Azure-munkaterületen a syslog-ügynök használatával.

1. Nyissa meg a CLI-t a Fortinet készüléken, és futtassa a következő parancsokat:

        config log syslogd setting
        set format cef
        set facility <facility_name>
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Cserélje le a kiszolgáló **IP-címét** az ügynök IP-címére.
    - Állítsa be a **facility_name** az ügynökben konfigurált létesítmény használatára. Alapértelmezés szerint az ügynök beállítja ezt a local4.
    - Állítsa a **syslog portot** **514** -re vagy az ügynökön beállított portra.
    - Ha a CEF formátumot szeretné engedélyezni a korai FortiOS-verziókban, előfordulhat, hogy futtatnia kell a **CSV**-letiltási parancsot.
 
   > [!NOTE] 
   > További információért keresse fel a [Fortinet dokumentumtárat](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Válassza ki a verziót, és használja a **kézikönyv** és a **napló üzenetének hivatkozását**.

 Ha a megfelelő sémát szeretné használni Azure Monitor Log Analytics a Fortinet-eseményekhez, `CommonSecurityLog`keresse meg a következőt:.


## <a name="step-3-validate-connectivity"></a>3\. lépés: Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem jelennek a Log Analyticsban. 

1. Győződjön meg arról, hogy a megfelelő eszközt használja. A létesítménynek meg kell egyeznie a készüléken és az Azure Sentinelben. Megtekintheti, hogy melyik Facility-fájlt használja az Azure Sentinelben, és hogyan módosíthatja `security-config-omsagent.conf`azt a fájlban. 

2. Győződjön meg arról, hogy a naplók a syslog-ügynök jobb portjára kerülnek. Futtassa ezt a parancsot a syslog-ügynök gépén `tcpdump -A -ni any  port 514 -vv`:. Ez a parancs megjeleníti azokat a naplókat, amelyek az eszközről a syslog-gépre áramlanak. Győződjön meg arról, hogy a megfelelő porton és a megfelelő eszközön érkeznek naplók a forrás készülékről.

3. Győződjön meg arról, hogy a küldött naplók megfelelnek az [RFC 3164](https://tools.ietf.org/html/rfc3164)-nek.

4. Ellenőrizze, hogy a syslog-ügynököt futtató számítógépen a 514-es és a 25226-es portok nyitva vannak- `netstat -a -n:`e, és hallgassa meg a parancsot a parancs használatával. További információ a parancs használatáról: [netstat (8) – Linux man oldal](https://linux.die.net/man/8/netstat). Ha a figyelő megfelelően működik, a következőt látja:

   ![Azure Sentinel-portok](./media/connect-cef/ports.png) 

5. Győződjön meg arról, hogy a démon úgy van beállítva, hogy figyelje a 514-es portot, amelyen a naplókat küldi.
    - Rsyslog esetén:<br>Győződjön meg arról, hogy `/etc/rsyslog.conf` a fájl tartalmazza ezt a konfigurációt:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      További információkért lásd [: imudp: UDP syslog bemeneti modul](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) és [imtcp: TCP syslog bemeneti modul](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module).

   - Syslog-ng esetén:<br>Győződjön meg arról, hogy `/etc/syslog-ng/syslog-ng.conf` a fájl tartalmazza ezt a konfigurációt:

           # source s_network {
            network( transport(UDP) port(514));
             };
     További információkért lásd [: imudp: UDP syslog bemeneti modul](https://rsyslog.readthedocs.io/en/latest/configuration/modules/imudp.html) és [syslog-ng nyílt forráskódú kiadás 3,16 – felügyeleti útmutató](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Győződjön meg arról, hogy van-e kommunikáció a syslog démon és az ügynök között. Futtassa ezt a parancsot a syslog-ügynök gépén `tcpdump -A -ni any  port 25226 -vv`:. Ez a parancs megjeleníti azokat a naplókat, amelyek az eszközről a syslog-gépre áramlanak. Győződjön meg arról, hogy a naplók is érkeznek az ügynökön.

6. Ha mindkét parancs sikeres eredményt adott, ellenőrizze, hogy a naplók érkeznek-e a Log Analytics. Az ezekről a készülékekről továbbított összes esemény nyers formában jelenik meg log Analytics `CommonSecurityLog` a típus területen.

7. Ellenőrizze, hogy vannak-e hibák, vagy ha a naplók nem érkeznek meg, `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`tekintse meg a következőt:. Ha azt szeretné `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` , hogy a napló formátuma nem felel meg a hibáknak, ugorjon `security_events.conf`a fájlra, és tekintse meg a fájlt. Győződjön meg arról, hogy a naplók egyeznek a fájlban látható regex-formátummal.

8. Győződjön meg arról, hogy a syslog-üzenet alapértelmezett mérete 2048 bájtra van korlátozva (2 KB). Ha a naplók túl hosszúak, frissítse a security_events. conf fájlt a következő parancs használatával:`message_length_limit 4096`

10. Ha az ügynök nem fogadja a Fortinet-naplókat, futtassa ezt a parancsot attól függően, hogy milyen típusú syslog-démont használ, majd állítsa be a létesítményt, és állítsa be a naplókat, hogy megkeresse a Fortinet szót a naplókban:
       - rsyslog. d:`sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`

     Indítsa újra a syslog démont a következő parancs használatával:`sudo service rsyslog restart`
       - syslog-ng:`sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      
     Indítsa újra a syslog démont a következő parancs használatával:`sudo service syslog-ng restart`

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan csatlakoztathatók a Fortinet-készülékek az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).

