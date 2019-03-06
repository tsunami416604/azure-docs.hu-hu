---
title: F5 adatgyűjtést az Azure-Sentinel-Előzetesében |} A Microsoft Docs
description: Ismerje meg, hogyan gyűjtheti össze az Azure-Sentinel F5 adatokat.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 552d78869a9b4b7eb65497bd0c5e6efe5f7aa8c9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57434708"
---
# <a name="connect-your-f5-appliance"></a>Csatlakozás az F5 készülék

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Csatlakozhat az Azure-Sentinel bármely F5 készülék Syslog CEF, a naplófájlokat menti. Az Azure Sentinel-integráció segítségével könnyedén futtathat elemzések és lekérdezések között a naplófájl adatai az F5 billentyűt. Hogyan Azure Sentinel-fogadnak CEF-adatok a további információkért lásd: [csatlakoztatása CEF berendezések](connect-common-event-format.md).

> [!NOTE]
> - Adatokat a munkaterület, amely futtatja az Azure-Sentinel a földrajzi helyen kell tárolni.

## <a name="step-1-connect-your-f5-appliance-using-an-agent"></a>1. lépés: Csatlakozás az F5 készülék-ügynök használatával

Az F5 berendezés csatlakozik Azure Sentinel-, az ügynököt egy dedikált gépre telepíti kell (virtuális gép vagy a helyszínen) a készüléket és a Sentinel-Azure közötti kommunikáció támogatásához. Az ügynök deploly automatikusan vagy manuálisan is. Automatikus központi telepítési csak akkor használható, ha dedikált számítógépe egy új virtuális Gépet hoz létre az Azure-ban. 

Azt is megteheti telepítheti az ügynököt manuálisan a meglévő Azure virtuális gép, egy virtuális gépen egy másik felhőben vagy a helyszíni gépen.

A hálózati diagram mindkét lehetőség előnyeivel, olvassa el [adatforrások csatlakoztatása](connect-data-sources.md#agent-options).

### <a name="deploy-the-agent-in-azure"></a>Telepítse az ügynököt az Azure-ban

1. Az Azure-Sentinel-portálon kattintson a **adatgyűjtés** válassza ki a készüléket. 

1. A **Linux Syslog-ügynök konfigurációjának**:
   - Válasszon **automatikus központi telepítési** szeretné-e a fent leírtak szerint, hozzon létre egy új gépet, amely előre telepítve van az Azure Sentinel-ügynökkel, és tartalmazza a konfiguráció szükséges. Válassza ki **automatikus központi telepítési** kattintson **automatikus ügynöktelepítés**. Ekkor megjelenik a vásárlás lap, amely automatikusan csatlakozik a munkaterülethez, a dedikált virtuális gépek. A virtuális gép egy **D2s v3-as standard (2 vcpu-k, 8 GB memória)** és a egy nyilvános IP-címmel rendelkezik.
      1. Az a **egyéni üzembe helyezés** lapon, adja meg az adatokat, és válassza ki a felhasználónevet és jelszót és elfogadja a feltételeket és kikötéseket, a virtuális gép vásárolhat.
      1. Konfigurálja a berendezést, a lapon szereplő beállításokkal, mint a naplókat. Az általános Common Event Format-összekötőhöz használja ezeket a beállításokat:
         - Protokoll = UDP
         - Port = 514-es
         - Létesítmény = helyi – 4
         - Formátum = CEF
  - Válasszon **manuális üzembe helyezési** Ha egy meglévő virtuális gép használja a dedikált Linux-gép, amelyre az Azure Sentinel-ügynököt kell telepíteni szeretné. 
      1. A **a Syslog-ügynök letöltése és telepítése**válassza **Azure-beli Linuxos virtuális gép**. 
      1. Az a **virtuális gépek** képernyő, amely megnyílik, válassza ki a gépet, használja, és kattintson a kívánt **Connect**.
      1. Az összekötő képernyőn alatt **és előre Syslog konfigurálása**állítsa be a Syslog démon e **rsyslog.d** vagy **syslog-ng**. 
      1. Másolja ezeket a parancsokat, és futtassa őket a berendezés:
          - Ha a kiválasztott rsyslog.d:
              
            1. Mondja el a Syslog démon a Syslog-üzeneteket küldhet az Azure Sentinel-ügynök használatával a 25226-os- és a létesítmény local_4 figyelni. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. Töltse le és telepítse a [security_events konfigurációs fájl](https://aka.ms/asi-syslog-config-file-linux) , konfigurálja a Syslog-ügynök a 25226-os-figyelik. `wget -P /etc/opt/microsoft/omsagent/{workspace GUID}/conf/omsagent.d/ -O security_events.conf "https://aka.ms/syslog-config-file-linux"`
            
            1. A syslog démon újraindításához `sudo service rsyslog restart`
             
          - Ha a syslog-ng választotta:

              1. Mondja el a Syslog démon a Syslog-üzeneteket küldhet az Azure Sentinel-ügynök használatával a 25226-os- és a létesítmény local_4 figyelni. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. Töltse le és telepítse a [security_events konfigurációs fájl](https://aka.ms/asi-syslog-config-file-linux) , konfigurálja a Syslog-ügynök a 25226-os-figyelik. `wget -P /etc/opt/microsoft/omsagent/{workspace GUID}/conf/omsagent.d/ -O security_events.conf "https://aka.ms/syslog-config-file-linux"`

              3. A syslog démon újraindításához `sudo service syslog-ng restart`
      2. Indítsa újra a Syslog-ügynök a következő paranccsal: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Győződjön meg arról, hogy nincsenek hibák az ügynöknaplóban Ez a parancs futtatásával: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>A helyi Linux-kiszolgálón az ügynök telepítése

Ha nem használja az Azure, ügynököt manuálisan telepíti az Azure-Sentinel futtatása egy dedikált Linux-kiszolgálón.


1. Az Azure-Sentinel-portálon kattintson a **adatgyűjtés** válassza ki a készüléket.
1. Egy dedikált Linux rendszerű virtuális gép létrehozása alatt **Linux Syslog-ügynök konfigurációjának** válasszon **manuális üzembe helyezési**.
   1. A **a Syslog-ügynök letöltése és telepítése**válassza **nem Azure-beli Linux rendszerű gép**. 
   1. Az a **közvetlen ügynök** képernyő, amely megnyílik, válassza ki **Linux-ügynök** töltse le az ügynököt, vagy letöltheti a Linux rendszerű számítógépen az alábbi paranccsal:   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
    3. Az összekötő képernyőn alatt **és előre Syslog konfigurálása**állítsa be a Syslog démon e **rsyslog.d** vagy **syslog-ng**. 
    4. Másolja ezeket a parancsokat, és futtassa őket a berendezés:
       - Ha az rsyslog választotta:
          1. Mondja el a Syslog démon a Syslog-üzeneteket küldhet az Azure Sentinel-ügynök használatával a 25226-os- és a létesítmény local_4 figyelni. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
          2. Töltse le és telepítse a [security_events konfigurációs fájl](https://aka.ms/asi-syslog-config-file-linux) , konfigurálja a Syslog-ügynök a 25226-os-figyelik. `wget -P /etc/opt/microsoft/omsagent/{workspace GUID}/conf/omsagent.d/ -O security_events.conf "https://aka.ms/syslog-config-file-linux"`
          3. A syslog démon újraindításához `sudo service rsyslog restart`
       - Ha a syslog-ng választotta:
            1. Mondja el a Syslog démon a Syslog-üzeneteket küldhet az Azure Sentinel-ügynök használatával a 25226-os- és a létesítmény local_4 figyelni. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. Töltse le és telepítse a [security_events konfigurációs fájl](https://aka.ms/asi-syslog-config-file-linux) , konfigurálja a Syslog-ügynök a 25226-os-figyelik. `wget -P /etc/opt/microsoft/omsagent/{workspace GUID}/conf/omsagent.d/ -O security_events.conf "https://aka.ms/syslog-config-file-linux"`
            3. A syslog démon újraindításához `sudo service syslog-ng restart`
    5. Indítsa újra a Syslog-ügynök a következő paranccsal: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
    6. Győződjön meg arról, hogy nincsenek hibák az ügynöknaplóban Ez a parancs futtatásával: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-f5-logs-to-the-syslog-agent"></a>2. lépés: F5 naplókat továbbítani a Syslog-ügynök

Syslog-üzeneteket az Azure-munkaterülethez a Syslog-ügynökön keresztül a CEF-formátumban továbbítani F5 konfigurálása:

Nyissa meg az F5 [alkalmazás biztonsági eseménynaplózás konfigurálásához](https://aka.ms/asi-syslog-f5-forwarding), és kövesse az utasításokat, állítsa be a távoli naplózás, az alábbiak szerint:
  - Állítsa be a **távoli tárolótípus** való **CEF**.
  - Állítsa be a **protokoll** való **UDP**.
  - Állítsa be a **IP-cím** Syslog server IP-címet.
  - Állítsa be a **portszám** való **514-es**, vagy a port beállítása az ügynök által használandó.
  - Állítsa be a **létesítmény** megfelelően állítsa be a Syslog-ügynök (alapértelmezés szerint az ügynök megadja ezt **local4**).
  - Beállíthatja a **lekérdezés-karakterlánc maximális mérete** állítsa be az ügynök mérete.

## <a name="step-3-validate-connectivity"></a>3. lépés: Kapcsolat ellenőrzése

Upwards of mindaddig, amíg megjelennek a Log Analytics indítása a naplók 20 percig is eltarthat. 

1. Győződjön meg arról, hogy a naplók a Syslog-ügynök a megfelelő porthoz van első. Futtassa ezt a parancsot a Syslog-ügynök gépen: `tcpdump -A -ni any  port 514 -vv` Ez a parancs megjeleníti a naplókat, amely az eszközről a Syslog-géphez streameli. Győződjön meg arról, hogy a forrás-berendezés a megfelelő port és a megfelelő létesítmény naplók érkeznek.
2. Ellenőrizze, hogy nincs-e a Syslog-démont és az ügynök közötti kommunikációhoz. Futtassa ezt a parancsot a Syslog-ügynök gépen: `tcpdump -A -ni any  port 25226 -vv` Ez a parancs megjeleníti a naplókat, amely az eszközről a Syslog-géphez streameli. Győződjön meg arról, hogy a naplók is fogadja az ügynökön.
3. Ha sikeres eredményt adott mindkét azokat a parancsokat, ellenőrizze a Log Analyticsben, hogy tekintse meg, ha a naplók érkezési. Ezek a készülékek a streamelt minden eseményt a Log Analytics a nyers formában jelennek meg `CommonSecurityLog ` típusa.
1. Ha nincsenek hibák, vagy ha a naplók nem érkező ellenőrzéséhez tekintse meg `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
4. Győződjön meg arról, hogy a Syslog-üzenet alapértelmezett mérete legfeljebb 2048 bájtok száma (2KB). Ha naplók túl hosszú, frissítse a security_events.conf ezzel a paranccsal: `message_length_limit 4096`



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan Azure Sentinel-F5 berendezések csatlakozni. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).

