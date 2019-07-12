---
title: Csatlakozás Azure-on Előzetesben Sentinel-Fortinet adatok |} A Microsoft Docs
description: Ismerje meg, hogyan kell csatlakozni a Fortinet adatokat az Azure-Sentinel.
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
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: f3ab4861e874074e7de059c7c50064d53749748c
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611302"
---
# <a name="connect-your-fortinet-appliance"></a>Csatlakozás a Fortinet berendezés

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió nélkül egy szolgáltatói szerződést biztosítunk. Nem ajánlott, az éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információkért lásd: [a Microsoft Azure előzetesekre vonatkozó kiegészítő feltételek](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Csatlakozhat az Azure-Sentinel bármely Fortinet készülék a naplófájlokat menti, Syslog Common Event Format (CEF). Az Azure Sentinel-integrációnak köszönhetően könnyedén futtathat elemzések és lekérdezések a naplófájl adatai között a Fortinet. Hogyan Azure Sentinel-fogadnak CEF-adatok a további információkért lásd: [csatlakoztatása CEF berendezések](connect-common-event-format.md).

> [!NOTE]
> Adatok tárolódnak a munkaterülethez, amelyen futtatja az Azure-Sentinel földrajzi helye.

## <a name="step-1-connect-your-fortinet-appliance-by-using-an-agent"></a>1\. lépés: Csatlakozás a Fortinet berendezés ügynök használatával

A Fortinet berendezés csatlakozni az Azure-Sentinel, dedikált virtuális gép vagy a helyi gépen a készüléket és a Sentinel-Azure közötti kommunikáció támogatásához az ügynök telepítése. Az ügynök automatikusan vagy manuálisan telepítheti. Automatikus központi telepítési csak akkor használható, ha dedikált számítógépe egy új virtuális Gépet hoz létre az Azure-ban.

Az ügynök manuálisan egy meglévő Azure virtuális gép, egy virtuális gépen egy másik felhőben, vagy egy helyszíni gépre is telepítheti.

A hálózati diagram mindkét lehetőség előnyeivel, olvassa el [adatforrások csatlakoztatása](connect-data-sources.md#agent-options).

### <a name="deploy-the-agent-in-azure"></a>Telepítse az ügynököt az Azure-ban

1. Az Azure-Sentinel-portálon válassza **adatösszekötők** válassza ki a készüléket.

1. A **Linux Syslog-ügynök konfigurációjának**:
   - Válasszon **automatikus központi telepítési** szeretné-e, hozzon létre egy új gépet, amely használható az Azure Sentinel-ügynök telepítve van, és minden szükséges, korábban leírt konfiguráció tartalmazza. Válassza ki **automatikus központi telepítési** > **automatikus ügynöktelepítés**. Megjelenik a vásárlás lap, amely automatikusan csatlakozik a munkaterület dedikált virtuális gép számára. A virtuális gép egy **D2s v3-as standard (2 vcpu-k, 8 GB memória)** és a egy nyilvános IP-címmel rendelkezik.
      1. Az a **egyéni üzembe helyezés** lapon adja meg az adatait, adjon meg egy felhasználónevet és jelszót, és ha elfogadja a feltételeket és kikötéseket, a virtuális Gépet vásárolni.
      1. Konfigurálja a berendezést, naplók küldése a kapcsolat oldalon felsorolt beállítások használatával. Az általános Common Event Format-összekötőhöz használja ezeket a beállításokat:
         - Protokoll = UDP
         - Port = 514-es
         - Létesítmény = helyi – 4
         - Formátum = CEF
   - Válasszon **manuális üzembe helyezési** Ha azt szeretné használni a dedikált Linux-gép, amelyre az Azure Sentinel-ügynök telepítve van egy meglévő virtuális Gépet. 
      1. A **a Syslog-ügynök letöltése és telepítése**válassza **Azure-beli Linuxos virtuális gép**. 
      1. Az a **virtuális gépek** képernyő, válassza ki szeretné használni, és válassza ki a gépet **Connect**.
      1. Az összekötő képernyőn alatt **és előre Syslog konfigurálása**állítsa be a Syslog démon e **rsyslog.d** vagy **syslog-ng**. 
      1. Másolja ezeket a parancsokat, és futtassa őket a berendezés:
          - Ha a kiválasztott rsyslog.d:
              
            1. Mondja el a Syslog démon létesítmény local_4 figyelésére és a Syslog-üzeneteket küldhet az Azure Sentinel-ügynök 25226-port használatával. Használja ezt a parancsot: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            1. Töltse le és telepítse a [security_events konfigurációs fájl](https://aka.ms/asi-syslog-config-file-linux) , konfigurálja a Syslog-ügynök a 25226-os-figyelik. Ezzel a paranccsal: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ahol {0} le kell cserélni a munkaterület GUID azonosítója.
            1. A syslog démon újraindításához a következő paranccsal: `sudo service rsyslog restart`
             
          - Ha a syslog-ng választotta:

              1. Mondja el a Syslog démon létesítmény local_4 figyelésére és a Syslog-üzeneteket küldhet az Azure Sentinel-ügynök 25226-port használatával. Használja ezt a parancsot: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              1. Töltse le és telepítse a [security_events konfigurációs fájl](https://aka.ms/asi-syslog-config-file-linux) , konfigurálja a Syslog-ügynök a 25226-os-figyelik. Ezzel a paranccsal: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ahol {0} le kell cserélni a munkaterület GUID azonosítója.
              1. A syslog démon újraindításához a következő paranccsal: `sudo service syslog-ng restart`
      1. Indítsa újra a Syslog-ügynök a következő paranccsal: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Győződjön meg arról, hogy nincsenek hibák az ügynöknaplóban Ez a parancs futtatásával: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Egy helyszíni Linux-kiszolgálón az ügynök telepítése

Ha nem használja az Azure, ügynököt manuálisan telepíti az Azure-Sentinel futtatása egy dedikált Linux-kiszolgálón.

1. Az Azure-Sentinel-portálon válassza **adatösszekötők** válassza ki a készüléket.
1. Egy dedikált Linux rendszerű virtuális gép létrehozása alatt **Linux Syslog-ügynök konfigurációjának** kiválasztása **manuális üzembe helyezési**.

    1. A **a Syslog-ügynök letöltése és telepítése**válassza **nem Azure-beli Linux rendszerű gép**.
    1. Az a **közvetlen ügynök** képernyő, amely megnyílik, válassza ki **Linux-ügynök** töltse le az ügynököt, vagy letöltheti a Linux rendszerű számítógépen az alábbi paranccsal: `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`

       1. Az összekötő képernyőn alatt **és előre Syslog konfigurálása**állítsa be a Syslog démon e **rsyslog.d** vagy **syslog-ng**.
       1. Másolja ezeket a parancsokat, és futtassa őket a berendezés:

          - Ha az rsyslog választotta:

            1. Mondja el a Syslog démon a Syslog-üzeneteket küldhet az Azure Sentinel-ügynök használatával a 25226-os- és a létesítmény local_4 figyelni. Használja ezt a parancsot: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            1. Töltse le és telepítse a [security_events konfigurációs fájl](https://aka.ms/asi-syslog-config-file-linux) , konfigurálja a Syslog-ügynök a 25226-os-figyelik. Ezzel a paranccsal: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ahol {0} le kell cserélni a munkaterület GUID azonosítója.
            1. A syslog démon újraindításához a következő paranccsal: `sudo service rsyslog restart`

          - Ha a syslog-ng választotta:

            1. Mondja el a Syslog démon létesítmény local_4 figyelésére és a Syslog-üzeneteket küldhet az Azure Sentinel-ügynök 25226-port használatával. Használja ezt a parancsot: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            1. Töltse le és telepítse a [security_events konfigurációs fájl](https://aka.ms/asi-syslog-config-file-linux) , konfigurálja a Syslog-ügynök a 25226-os-figyelik. Ezzel a paranccsal: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` ahol {0} le kell cserélni a munkaterület GUID azonosítója.
            1. A syslog démon újraindításához a következő paranccsal: `sudo service syslog-ng restart`

      1. Indítsa újra a Syslog-ügynök a következő paranccsal: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Győződjön meg arról, hogy nincsenek hibák az ügynöknaplóban Ez a parancs futtatásával: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>2\. lépés: A Syslog-ügynök továbbítják a Fortinet naplókat

Konfigurálja a Syslog-üzeneteket az Azure-munkaterülethez a Syslog-ügynökön keresztül a CEF-formátumban továbbítani a Fortinet.

1. Nyissa meg a parancssori felület, a Fortinet-készüléken, és futtassa a következő parancsokat:

        config log syslogd setting
        set format cef
        set facility <facility_name>
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Cserélje le a kiszolgáló **ip-cím** az ügynök IP-címmel.
    - Állítsa be **facility_name** használatára konfigurálta, az ügynök a konstrukció. Alapértelmezés szerint az ügynök állítja ezt local4.
    - Állítsa be a **syslog portjának** való **514-es** vagy a port, állítsa be az ügynökön.
    - CEF formátumban a korai FortiOS verziók engedélyezéséhez szükség van a parancs végrehajtása **tiltsa le a fürt megosztott kötetei szolgáltatás**.
 
   > [!NOTE] 
   > További információkért látogasson el a [Fortinet dokumentumtár](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). A verziónak a kiválasztása, és használja a **kézikönyv** és **üzenet naplózása**.

 Használja a megfelelő sémát az Azure Monitor Log-Analytics, a Fortinet események, keresse meg `CommonSecurityLog`.


## <a name="step-3-validate-connectivity"></a>3\. lépés: Kapcsolat ellenőrzése

Mindaddig, amíg megjelennek a Log Analytics indítása a naplók legfeljebb 20 percig is eltarthat. 

1. Ellenőrizze, hogy a megfelelő funkcióval. A létesítmény meg kell egyeznie a berendezés és az Azure-Sentinel. Az Azure-Sentinel használja, és módosítsa a fájl melyik létesítmény fájlban `security-config-omsagent.conf`. 

2. Győződjön meg arról, hogy a naplók a Syslog-ügynök a megfelelő porthoz van első. Futtassa a következő parancsot a Syslog-ügynök gépen: `tcpdump -A -ni any  port 514 -vv`. Ez a parancs megjeleníti a naplók streamelése az eszközről a Syslog-géphez. Győződjön meg arról, hogy a forrás-berendezés a megfelelő portot és a megfelelő létesítmény naplók érkeznek.

3. Győződjön meg arról, hogy megfelelnek-e a naplókat küld a [RFC 5424](https://tools.ietf.org/html/rfc542).

4. A számítógépen a Syslog-ügynököt futtató ellenőrizze, hogy az 514-es és a 25226 portok nyitva és figyel-e a parancs segítségével `netstat -a -n:`. Ez a parancs használatával kapcsolatos további információkért lásd: [netstat(8) – Linux man lap](https://linux.die.net/man/8/netstat). Ha megfelelően figyel, láthatja:

   ![Az Azure Sentinel-portok](./media/connect-cef/ports.png) 

5. Ellenőrizze a démont értéke 514-es, porton figyelnek, amelyen a naplók küldése folyamatban van.
    - Rsyslog:<br>Győződjön meg arról, hogy a fájl `/etc/rsyslog.conf` ezt a konfigurációt tartalmazza:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      További információkért lásd: [imudp: A modul bemeneti UDP Syslog](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) és [imtcp: TCP, Syslog bemeneti modul](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module).

   - A syslog-ng esetén:<br>Győződjön meg arról, hogy a fájl `/etc/syslog-ng/syslog-ng.conf` ezt a konfigurációt tartalmazza:

           # source s_network {
            network( transport(UDP) port(514));
             };
     További információkért lásd: [imudp: A modul bemeneti UDP Syslog](https://rsyslog.readthedocs.io/en/latest/configuration/modules/imudp.html) és [syslog-ng nyílt forráskódú kiadásának 3.16 - felügyeleti útmutató](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Ellenőrizze, hogy nincs-e a Syslog-démont és az ügynök közötti kommunikációhoz. Futtassa a következő parancsot a Syslog-ügynök gépen: `tcpdump -A -ni any  port 25226 -vv`. Ez a parancs megjeleníti a naplók streamelése az eszközről a Syslog-géphez. Győződjön meg arról, hogy a naplók is fogadja az ügynökön.

6. Ha sikeres eredményt adott mindkét azokat a parancsokat, ellenőrizze a Log Analyticsben, hogy tekintse meg, ha a naplók érkezési. Ezek a készülékek a streamelt minden eseményt a Log Analytics a nyers formában jelennek meg `CommonSecurityLog` típusa.

7. Ellenőrizze, hogy vannak-e hibák, vagy ha a naplók nem érkező, nézze meg `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Allocated a napló formátuma eltérés hibák, Ugrás `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` , és keresse meg a fájlban `security_events.conf`. Győződjön meg arról, hogy a naplók a regex formátumban jelenik meg ez a fájl megfelel-e.

8. Győződjön meg arról, hogy a Syslog-üzenet alapértelmezett mérete legfeljebb 2048 bájtok száma (2 KB). Ha naplók túl hosszú, frissítse a security_events.conf a következő paranccsal: `message_length_limit 4096`

10. Ha nem fogadja a Fortinet naplók az ügynök, futtassa a következő parancsot a Syslog démon használ, állítsa be a létesítmény, és állítsa be a naplókat, keresse meg a naplók a Fortinet word típusától függően:
       - rsyslog.d: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`

     A Syslog démon újraindításához a következő paranccsal: `sudo service rsyslog restart`
       - Syslog-ng: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      
     A Syslog démon újraindításához a következő paranccsal: `sudo service syslog-ng restart`

## <a name="next-steps"></a>További lépések
Ebben a cikkben megtanulta, hogyan szeretne csatlakozni a Fortinet berendezések Azure Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).

