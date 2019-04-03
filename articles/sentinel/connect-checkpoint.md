---
title: Azure-on Előzetesben Sentinel-adatok gyűjtése a Check Point |} A Microsoft Docs
description: Ismerje meg az Azure-Sentinel a Check Point adatainak gyűjtéséről.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 3229233d-400d-4971-8d76-eaa0d6591d75
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/6/2019
ms.author: rkarlin
ms.openlocfilehash: 1fb4f9165be03a7fc3cd055ef616dcfadb58ac9d
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58876496"
---
# <a name="connect-your-check-point-appliance"></a>A Check Point berendezés csatlakoztatása

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Csatlakozhat az Azure-Sentinel bármely a Check Point készülék Syslog CEF, a naplófájlokat menti. Az Azure Sentinel-integráció segítségével könnyedén futtathat elemzések és lekérdezések a naplófájl adatai között a Check Point. Hogyan Azure Sentinel-fogadnak CEF-adatok a további információkért lásd: [csatlakoztatása CEF berendezések](connect-common-event-format.md).

> [!NOTE]
> - Adatokat a munkaterület, amely futtatja az Azure-Sentinel a földrajzi helyen kell tárolni.

## <a name="step-1-connect-your-check-point-appliance-using-an-agent"></a>1. lépés: A Check Point berendezés használatával egy ügynök csatlakoztatása

A Check Point berendezés csatlakozni az Azure-Sentinel, szüksége egy dedikált gépen (VM vagy helyszíni) a készüléket és a Sentinel-Azure közötti kommunikáció támogatásához az ügynökök telepítéséhez. Az ügynök automatikusan vagy manuálisan telepítheti. Automatikus központi telepítési csak akkor használható, ha dedikált számítógépe egy új virtuális Gépet hoz létre az Azure-ban. 

Azt is megteheti telepítheti az ügynököt manuálisan a meglévő Azure virtuális gép, egy virtuális gépen egy másik felhőben vagy a helyszíni gépen.

A hálózati diagram mindkét lehetőség előnyeivel, olvassa el [adatforrások csatlakoztatása](connect-data-sources.md).

### <a name="deploy-the-agent-in-azure"></a>Telepítse az ügynököt az Azure-ban

1. Az Azure-Sentinel-portálon kattintson a **adatgyűjtés** válassza ki a készüléket. 

1. A **Linux Syslog-ügynök konfigurációjának**:
   - Válasszon **automatikus központi telepítési** szeretné-e a fent leírtak szerint, hozzon létre egy új gépet, amely előre telepítve van az Azure Sentinel-ügynökkel, és tartalmazza a konfiguráció szükséges. Válassza ki **automatikus központi telepítési** kattintson **automatikus ügynöktelepítés**. Ekkor megjelenik a vásárlás lap, amely automatikusan csatlakozik a munkaterület dedikált virtuális gép számára. A virtuális gép egy **D2s v3-as standard (2 vcpu-k, 8 GB memória)** és a egy nyilvános IP-címmel rendelkezik.
     1. Az a **egyéni üzembe helyezés** lapon, adja meg az adatokat, és válassza ki a felhasználónevet és jelszót és elfogadja a feltételeket és kikötéseket, a virtuális gép vásárolhat.
      
        1. Futtassa ezeket a parancsokat, győződjön meg arról, hogy minden a Check Point naplók az Azure Sentinel-ügynököt kell hozzárendelni, a Syslog ügynök gépen:
           - Ha Syslog-ng használ, futtassa a következő parancsokat (vegye figyelembe, hogy a Syslog-ügynök újraindítása):
            
                sudo bash - c "printf" f_local4_oms szűrése {facility(local4);}; \ n cél security_oms {tcp (\"127.0.0.1\" port(25226));}; \n log {source(src); filter(f_local4_oms); destination(security_oms);}; \n\nfilter f_msg_oms {megfelelő (\"a Check Point\" (érték\" ÜZENET\")); }; \n cél security_msg_oms {tcp (\"127.0.0.1\" port(25226));}; \n log {source(src); filter(f_msg_oms); destination(security_msg_oms);}; "> /etc/syslog-ng/security-config-omsagent.conf"

             A Syslog démon újraindításához: `sudo service syslog-ng restart`
           - Ha rsyslog használ, futtassa a következő parancsokat (vegye figyelembe, hogy a Syslog-ügynök újraindítása):
                    
                 sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"
             A Syslog démon újraindításához: `sudo service rsyslog restart`

   - Válasszon **manuális üzembe helyezési** Ha egy meglévő virtuális gép használja a dedikált Linux-gép, amelyre az Azure Sentinel-ügynököt kell telepíteni szeretné. 
      1. A **a Syslog-ügynök letöltése és telepítése**válassza **Azure-beli Linuxos virtuális gép**. 
      1. Az a **virtuális gépek** képernyő, amely megnyílik, válassza ki a gépet, használja, és kattintson a kívánt **Connect**.
      1. Az összekötő képernyőn alatt **és előre Syslog konfigurálása**állítsa be a Syslog démon e **rsyslog.d** vagy **syslog-ng**. 
      1. Másolja ezeket a parancsokat, és futtassa őket a berendezés:
          - Ha a kiválasztott rsyslog.d:
              
            1. Mondja el a Syslog démon létesítmény local_4 és a "Check Point" figyelésére és a Syslog-üzeneteket küldhet az Azure Sentinel-ügynök 25226-os-használatával. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
            2. Töltse le és telepítse a [security_events konfigurációs fájl](https://aka.ms/asi-syslog-config-file-linux) , konfigurálja a Syslog-ügynök a 25226-os-figyelik. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Ahol {0} le kell cserélni a munkaterület GUID azonosítója.
           
            1. A syslog démon újraindításához `sudo service rsyslog restart`
             
          - Ha a syslog-ng választotta:

              1. Mondja el a Syslog démon létesítmény local_4 és a "Check Point" figyelésére és a Syslog-üzeneteket küldhet az Azure Sentinel-ügynök 25226-os-használatával. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              2. Töltse le és telepítse a [security_events konfigurációs fájl](https://aka.ms/asi-syslog-config-file-linux) , konfigurálja a Syslog-ügynök a 25226-os-figyelik. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Ahol {0} le kell cserélni a munkaterület GUID azonosítója.

              3. A syslog démon újraindításához `sudo service syslog-ng restart`
      2. Indítsa újra a Syslog-ügynök a következő paranccsal: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Győződjön meg arról, hogy nincsenek hibák az ügynöknaplóban Ez a parancs futtatásával: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>A helyi Linux-kiszolgálón az ügynök telepítése

Ha nem használja az Azure, ügynököt manuálisan telepíti az Azure-Sentinel futtatása egy dedikált Linux-kiszolgálón.

1. Egy dedikált Linux rendszerű virtuális gép létrehozása alatt **Linux Syslog-ügynök konfigurációjának** válasszon **manuális üzembe helyezési**.
   1. A **a Syslog-ügynök letöltése és telepítése**válassza **nem Azure-beli Linux rendszerű gép**. 
   1. Az a **közvetlen ügynök** képernyő, amely megnyílik, válassza ki **Linux-ügynök** töltse le az ügynököt, vagy letöltheti a Linux rendszerű számítógépen az alábbi paranccsal:   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`
      1. Az összekötő képernyőn alatt **és előre Syslog konfigurálása**állítsa be a Syslog démon e **rsyslog.d** vagy **syslog-ng**. 
      1. Másolja ezeket a parancsokat, és futtassa őket a berendezés:
         - Ha a kiválasztott **rsyslog**:
           1. Mondja el a Syslog démon létesítmény local_4 és a "Check Point" figyelésére és a Syslog-üzeneteket küldhet az Azure Sentinel-ügynök 25226-os-használatával. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Check Point\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
           2. Töltse le és telepítse a [security_events konfigurációs fájl](https://aka.ms/asi-syslog-config-file-linux) , konfigurálja a Syslog-ügynök a 25226-os-figyelik. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Ahol {0} le kell cserélni a munkaterület GUID azonosítója.
           3. A syslog démon újraindításához `sudo service rsyslog restart`
         - Ha a kiválasztott **syslog-ng**:
            1. Mondja el a Syslog démon létesítmény local_4 és a "Check Point" figyelésére és a Syslog-üzeneteket küldhet az Azure Sentinel-ügynök 25226-os-használatával. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            2. Töltse le és telepítse a [security_events konfigurációs fájl](https://aka.ms/asi-syslog-config-file-linux) , konfigurálja a Syslog-ügynök a 25226-os-figyelik. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` Ahol {0} le kell cserélni a munkaterület GUID azonosítója.
            3. A syslog démon újraindításához `sudo service syslog-ng restart`
      1. Indítsa újra a Syslog-ügynök a következő paranccsal: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Győződjön meg arról, hogy nincsenek hibák az ügynöknaplóban Ez a parancs futtatásával: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-check-point-logs-to-the-syslog-agent"></a>2. lépés: A Syslog-ügynök előre a Check Point-naplók

Konfigurálja a Check Point berendezés továbbítani tudja a Syslog-üzeneteket az Azure-munkaterülethez a Syslog-ügynökön keresztül CEF formátumban.

1. Lépjen a [ellenőrizze a pont a Tevékenységnaplói exportálás](https://aka.ms/asi-syslog-checkpoint-forwarding).
2. Görgessen le a **alapszintű központi telepítést** és kövesse az utasításokat a kapcsolatot, az alábbiak szerint:
   - Állítsa be a **Syslog portjának** való **514-es** vagy az ügynöknél beállított port.
     - Cserélje le a **neve** és **-célkiszolgáló IP-cím** a parancssori felületen, a Syslog-ügynök neve és IP-címet.
     - A formátum beállítása **CEF**.
3. Ha R77.30 vagy R80.10 verzióját használja, akár görgessen **telepítések** , és kövesse az utasításokat követve telepítse a napló exportáló-verzióra vonatkozó.
 
## <a name="step-3-validate-connectivity"></a>3. lépés: Kapcsolat ellenőrzése

Upwards of mindaddig, amíg megjelennek a Log Analytics indítása a naplók 20 percig is eltarthat. 

1. Győződjön meg arról, hogy a naplók a Syslog-ügynök a megfelelő porthoz van első. Futtassa ezt a parancsot a Syslog-ügynök gépen: `tcpdump -A -ni any  port 514 -vv` Ez a parancs megjeleníti a naplókat, amely az eszközről a Syslog-géphez streameli. Győződjön meg arról, hogy a forrás-berendezés a megfelelő port és a megfelelő létesítmény naplók érkeznek.
2. Ellenőrizze, hogy nincs-e a Syslog-démont és az ügynök közötti kommunikációhoz. Futtassa ezt a parancsot a Syslog-ügynök gépen: `tcpdump -A -ni any  port 25226 -vv` Ez a parancs megjeleníti a naplókat, amely az eszközről a Syslog-géphez streameli. Győződjön meg arról, hogy a naplók is fogadja az ügynökön.
3. Ha sikeres eredményt adott mindkét azokat a parancsokat, ellenőrizze a Log Analyticsben, hogy tekintse meg, ha a naplók érkezési. Ezek a készülékek a streamelt minden eseményt a Log Analytics a nyers formában jelennek meg `CommonSecurityLog` típusa.

4. Ügyeljen arra, hogy futtassa a következő parancsokat:
  
   - Ha Syslog-ng használ, futtassa a következő parancsokat (vegye figyelembe, hogy a Syslog-ügynök újraindítása):

         sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Check Point\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"
        A Syslog démon újraindításához: `sudo service syslog-ng restart`

   - Ha rsyslog használ, futtassa a következő parancsokat (vegye figyelembe, hogy a Syslog-ügynök újraindítása): 

         sudo bash -c "printf 'local4.debug @127.0.0.1:25226\n\n:msg, contains, "Check Point" @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"
     A Syslog démon újraindításához: `sudo service rsyslog restart`

1. Ha nincsenek hibák, vagy ha a naplók nem érkező ellenőrzéséhez tekintse meg `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
4. Győződjön meg arról, hogy a Syslog-üzenet alapértelmezett mérete legfeljebb 2048 bájtok száma (2KB). Ha naplók túl hosszú, frissítse a security_events.conf ezzel a paranccsal: `message_length_limit 4096`



## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtudhatta, hogyan szeretne csatlakozni a Check Point berendezések Azure Sentinel. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).

