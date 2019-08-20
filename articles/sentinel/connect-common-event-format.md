---
title: CEF-adatkapcsolatok összekötése az Azure Sentinel előzetes verziójával | Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat a CEF-adatbázisokhoz az Azure Sentinel szolgáltatáshoz.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cbf5003b-76cf-446f-adb6-6d816beca70f
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/19/2019
ms.author: rkarlin
ms.openlocfilehash: 28def73926294a025d70844e535a0856153ae30a
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69611927"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>A külső megoldás összekötése a közös esemény formátumával

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Sentinel egy külső megoldással is csatlakoztatható, amely lehetővé teszi a naplófájlok mentését a syslog-ben. Ha a készülék lehetővé teszi, hogy a naplókat a syslog Common Event Format (CEF) használatával mentse, az Azure Sentinel-nal való integráció lehetővé teszi, hogy könnyedén futtasson elemzéseket és lekérdezéseket az összes adattal.

> [!NOTE] 
> Az adattárolást annak a munkaterületnek a földrajzi helye tárolja, amelyen az Azure Sentinel alkalmazást futtatja.

## <a name="how-it-works"></a>Működés

Az Azure Sentinel és a CEF készülék közötti kapcsolat három lépésben zajlik:

1. A készüléken ezeket az értékeket úgy kell beállítani, hogy a berendezés a szükséges formátumban küldje el a szükséges naplókat az Azure Sentinel syslog-ügynöknek a Microsoft monitoring Agent alapján. Ezeket a paramétereket módosíthatja a berendezésben, ha az Azure Sentinel-ügynök syslog démonán is módosítja őket.
    - Protokoll = UDP
    - Port = 514
    - Létesítmény = Local4
    - Format = CEF
2. A syslog-ügynök összegyűjti az adatokat, és biztonságos módon küldi el azokat Log Analyticsba, ahol a rendszer elemzi és gazdagítja azokat.
3. Az ügynök egy Log Analytics munkaterületen tárolja az adataikat, így szükség szerint lekérdezhető az elemzés, a korrelációs szabályok és az irányítópultok használatával.

> [!NOTE]
> Az ügynök több forrásból is gyűjthet naplókat, de a dedikált gépre kell telepítenie.


 ![CEF az Azure-ban](./media/connect-cef/cef-syslog-azure.png)

Azt is megteheti, hogy manuálisan telepítheti az ügynököt egy meglévő Azure-beli virtuális gépre, egy másik felhőben lévő virtuális gépre vagy egy helyszíni gépre. 

 ![CEF a helyszínen](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>Biztonsági szempontok

Győződjön meg arról, hogy a cég biztonsági szabályzata szerint konfigurálja a gép biztonságát. Konfigurálhatja például a hálózatot úgy, hogy az megfeleljen a vállalati hálózati biztonsági házirendnek, és módosítsa a démon portjait és protokollait úgy, hogy azok megfeleljenek a követelményeinek. A következő útmutatást követve javíthatja a gép biztonsági konfigurációját:  [biztonságos virtuális gép az Azure-ban](../virtual-machines/linux/security-policy.md), [ajánlott eljárások a hálózati biztonsághoz](../security/fundamentals/network-best-practices.md).

A biztonsági megoldás és a syslog-gép közötti TLS-kommunikáció használatához konfigurálnia kell a syslog démont (rsyslog vagy syslog-ng) a TLS-kommunikációhoz: [A syslog-forgalom titkosítása TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [a naplózási üzenetek titkosítása a TLS-syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)használatával.


## <a name="step-1-configure-your-syslog-vm"></a>1\. lépés: A syslog virtuális gép konfigurálása

Telepítenie kell egy ügynököt egy dedikált linuxos gépen (VM vagy helyszíni) a készülék és az Azure Sentinel közötti kommunikáció támogatásához. 

> [!NOTE]
> Győződjön meg arról, hogy a cég biztonsági szabályzata szerint konfigurálja a gép biztonságát. Konfigurálhatja például a hálózatot úgy, hogy az megfeleljen a vállalati hálózati biztonsági házirendnek, és módosítsa a démon portjait és protokollait úgy, hogy azok megfeleljenek a követelményeinek. 


1. Az Azure Sentinel portálon kattintson az adatösszekötők elemre, és válassza a **Common Event Format (CEF)** lehetőséget, majd **nyissa meg az összekötő lapot**. 

1. **A syslog-ügynök letöltése és telepítése**alatt válassza ki a gép típusát, akár az Azure-t, akár a helyszínen. 
1. A megnyíló **virtuális gépek** képernyőn válassza ki a használni kívánt gépet, és kattintson a **Kapcsolódás**gombra.
1. Ha az **ügynök letöltése és telepítése Azure Linux rendszerű virtuális gépekhez**lehetőséget választja, válassza ki a gépet, és kattintson a **kapcsolat**elemre. Ha az **ügynök letöltése és telepítése nem Azure-beli Linux rendszerű virtuális gépekhez**lehetőséget választotta, a **közvetlen ügynök** képernyőjén futtassa a parancsfájlt a letöltés és a bevezetési **ügynök a Linux rendszerhez**című szakaszban.
1. Az CEF-összekötő képernyőn, a **syslog konfigurálása és továbbítása**területen állítsa be, hogy a syslog démon **rsyslog. d** vagy **syslog-ng**. 
1. Másolja ezeket a parancsokat, és futtassa őket a készüléken:
    - Ha a rsyslog. d elemet választotta:
              
       1. Tájékoztassa a syslog démont, hogy figyelje a létesítmény local_4, és küldje el a syslog-üzeneteket az Azure Sentinel-ügynöknek az 25226-es port használatával. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
       2. Töltse le és telepítse a [security_events konfigurációs fájlt](https://aka.ms/asi-syslog-config-file-linux) , amely a syslog-ügynököt konfigurálja a 25226-es porton való figyeléshez. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Hol {0} kell helyettesíteni a munkaterület GUID azonosítóját.
            
       1. A syslog démon újraindítása`sudo service rsyslog restart`<br> További információt a [rsyslog dokumentációjában](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html) talál.
           
    - Ha a syslog-ng beállítást választotta:
       1. Tájékoztassa a syslog démont, hogy figyelje a létesítmény local_4, és küldje el a syslog-üzeneteket az Azure Sentinel-ügynöknek az 25226-es port használatával. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
       2. Töltse le és telepítse a [security_events konfigurációs fájlt](https://aka.ms/asi-syslog-config-file-linux) , amely a syslog-ügynököt konfigurálja a 25226-es porton való figyeléshez. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`Hol {0} kell helyettesíteni a munkaterület GUID azonosítóját.

        3. A syslog démon újraindítása`sudo service syslog-ng restart` <br>További információt a [syslog-ng dokumentációjában](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/mutual-authentication-using-tls/2) talál.
1. Indítsa újra a syslog-ügynököt a következő parancs használatával:`sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. A következő parancs futtatásával ellenőrizze, hogy nincsenek-e hibák az ügynök naplójában:`tail /var/opt/microsoft/omsagent/log/omsagent.log`

A CEF-események Log Analytics vonatkozó sémájának használatához keresse meg a következőt `CommonSecurityLog`:.

## <a name="step-2-forward-common-event-format-cef-logs-to-syslog-agent"></a>2\. lépés: Common Event Format-(CEF-) naplók továbbítása a syslog-ügynöknek

Állítsa be a biztonsági megoldást, hogy CEF formátumban küldjön syslog-üzeneteket a syslog-ügynöknek. Győződjön meg arról, hogy ugyanazokat a paramétereket használja, mint az ügynök konfigurációjában. Ezek általában a következők:

- 514-es port
- Létesítmény local4

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
Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók a CEF-készülékek az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).

