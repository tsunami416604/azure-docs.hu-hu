---
title: A naplózási továbbító üzembe helyezése a CEF-adatkapcsolatok Azure Sentinelhez való összekapcsolásához | Microsoft Docs
description: Megtudhatja, hogyan helyezheti üzembe az ügynököt a CEF-adatkapcsolat Azure Sentinelhez való összekapcsolásához.
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
ms.date: 10/01/2020
ms.author: yelevin
ms.openlocfilehash: a54dfa0f2b072d30cac605937a1b623ef9d4051d
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631494"
---
# <a name="step-1-deploy-the-log-forwarder"></a>1. lépés: a naplózási továbbító üzembe helyezése


Ebben a lépésben a Linux-gépet fogja kijelölni és konfigurálni, amely továbbítja a naplókat a biztonsági megoldásból az Azure Sentinel-munkaterületre. Ez a gép lehet fizikai vagy virtuális gép a helyszíni környezetben, egy Azure-beli virtuális gépen vagy egy másik felhőben lévő virtuális gépről. A megadott hivatkozás használatával egy parancsfájlt fog futtatni a kijelölt gépen, amely a következő feladatokat hajtja végre:
- Telepíti a Linux rendszerhez készült Log Analytics-ügynököt (más néven OMS-ügynököt), és a következő célokra konfigurálja azt:
    - CEF üzenetek figyelése a beépített Linux syslog démonból a 25226-as TCP-porton
    - az üzenetek biztonságos küldése a TLS-kapcsolaton keresztül az Azure Sentinel-munkaterületre, ahol a rendszer elemzi és gazdagítja azokat

- A beépített Linux syslog démont (rsyslog. d/syslog-ng) konfigurálja a következő célokra:
    - a biztonsági megoldások syslog-üzeneteinek figyelése a 514-as TCP-porton
    - csak az általa azonosított üzenetek továbbítása a localhost Log Analytics-ügynök CEF a 25226-as TCP-port használatával
 
## <a name="prerequisites"></a>Előfeltételek

- A kijelölt linuxos gépen emelt szintű engedélyekkel (sudo) kell rendelkeznie.
- A Linux gépen telepítve kell lennie a pythonnak.<br>A parancs használatával keresse meg a következőt: `python -version` .
- A Linux rendszerű számítógép nem csatlakoztatható Azure-munkaterületekhez az Log Analytics-ügynök telepítése előtt.

## <a name="run-the-deployment-script"></a>Az üzembe helyezési szkript futtatása
 
1. Az Azure Sentinel navigációs menüjében kattintson az **adatösszekötők**elemre. Az összekötők listájában kattintson a **Common Event Format (CEF)** csempére, majd a jobb alsó sarokban található **összekötő megnyitása lap** gombra. 

1. A **1,2 alatt telepítse a CEF-gyűjtőt a Linux**rendszerű gépre, másolja a **következő szkript futtatásával elérhető hivatkozást a CEF-gyűjtő telepítéséhez és alkalmazásához**, vagy az alábbi szövegből:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. A szkript futtatása közben ellenőrizze, hogy nem kap-e hibaüzenetet vagy figyelmeztető üzenetet.

> [!NOTE]
> **Ugyanazzal a géppel az egyszerű syslog *és* a CEF üzenetek továbbítása**
>
> Ha azt tervezi, hogy ezt a naplózási továbbítót használja a [syslog-üzenetek](connect-syslog.md) és a CEF továbbítására, akkor az események a syslog-és a CommonSecurityLog-táblákba való másolásának elkerülése érdekében:
>
> 1. Minden olyan forrásoldali gépen, amely CEF formátumú naplókat küld a továbbítónak, szerkesztenie kell a syslog konfigurációs fájlját, hogy eltávolítsa a CEF üzenetek küldésére használt létesítményeket. Így a CEF-ben eljuttatott létesítmények nem lesznek elküldve a syslog-ben. Ennek módjával kapcsolatos részletes útmutatásért lásd: [a syslog konfigurálása Linux-ügynökön](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent) .
>
> 1. A következő parancs futtatásával le kell tiltania az ügynök szinkronizálását az Azure Sentinel syslog-konfigurációjával. Ez biztosítja, hogy az előző lépésben végrehajtott konfigurációs módosítás ne legyen felülírva.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

Folytassa a [2. lépéssel: a biztonsági megoldás konfigurálása a CEF-üzenetek továbbítására](connect-cef-solution-config.md) .

## <a name="deployment-script-explained"></a>Üzembe helyezési parancsfájl ismertetése

Az alábbi parancs a telepítési parancsfájl műveleteinek parancssori leírását adja meg.

A megfelelő leírás megtekintéséhez válassza ki a syslog démont.

# <a name="rsyslog-daemon"></a>[rsyslog démon](#tab/rsyslog)

1. **Az Log Analytics-ügynök letöltése és telepítése:**

    - Letölti a Log Analytics (OMS) Linux-ügynök telepítési parancsfájlját.

        ```bash
        wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/
            onboard_agent.sh
        ```

    - Telepíti a Log Analytics ügynököt.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **A Log Analytics-ügynök konfigurációjának beállítása a 25226-es port figyelésére és a CEF üzenetek továbbítására az Azure Sentinel számára:**

    - Letölti a konfigurációt a Log Analytics ügynök GitHub-adattárból.

        ```bash
        wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **A syslog démon konfigurálása:**

    - Megnyitja a 514-es portot a TCP-kommunikációhoz a syslog konfigurációs fájljának használatával `/etc/rsyslog.conf` .

    - Úgy konfigurálja a démont, hogy továbbítsa a CEF üzeneteket a Log Analytics-ügynöknek a 25226-as TCP-porton. ehhez egy speciális konfigurációs fájlt kell beszúrnia `security-config-omsagent.conf` a syslog démon könyvtárába `/etc/rsyslog.d/` .

        A fájl tartalma `security-config-omsagent.conf` :

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. **A syslog démon és a Log Analytics ügynök újraindítása:**

    - Újraindítja a rsyslog démont.
    
        ```bash
        service rsyslog restart
        ```

    - Újraindítja a Log Analytics ügynököt.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **A *számítógép* mező a várt módon történő leképezésének ellenőrzése:**

    - Gondoskodik arról, hogy a syslog forrás *számítógép* mezőjének megfelelően legyen leképezve a log Analytics ügynök a parancs futtatásával és az ügynök újraindításával.

        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" 
            -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/pl ugin/
            filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

# <a name="syslog-ng-daemon"></a>[syslog-ng démon](#tab/syslogng)

1. **Az Log Analytics-ügynök letöltése és telepítése:**

    - Letölti a Log Analytics (OMS) Linux-ügynök telepítési parancsfájlját.

        ```bash
        wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/
            onboard_agent.sh
        ```

    - Telepíti a Log Analytics ügynököt.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **A Log Analytics-ügynök konfigurációjának beállítása a 25226-es port figyelésére és a CEF üzenetek továbbítására az Azure Sentinel számára:**

    - Letölti a konfigurációt a Log Analytics ügynök GitHub-adattárból.

        ```bash
        wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **A syslog démon konfigurálása:**

    - Megnyitja a 514-es portot a TCP-kommunikációhoz a syslog konfigurációs fájljának használatával `/etc/syslog-ng/syslog-ng.conf` .

    - Úgy konfigurálja a démont, hogy továbbítsa a CEF üzeneteket a Log Analytics-ügynöknek a 25226-as TCP-porton. ehhez egy speciális konfigurációs fájlt kell beszúrnia `security-config-omsagent.conf` a syslog démon könyvtárába `/etc/syslog-ng/conf.d/` .

        A fájl tartalma `security-config-omsagent.conf` :

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
        destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **A syslog démon és a Log Analytics ügynök újraindítása:**

    - Újraindítja a syslog-ng démont.
    
        ```bash
        service syslog-ng restart
        ```

    - Újraindítja a Log Analytics ügynököt.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **A *számítógép* mező a várt módon történő leképezésének ellenőrzése:**

    - Gondoskodik arról, hogy a syslog forrás *számítógép* mezőjének megfelelően legyen leképezve a log Analytics ügynök a parancs futtatásával és az ügynök újraindításával.

        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" 
            -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/pl ugin/
            filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```



## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan helyezheti üzembe a Log Analytics-ügynököt a CEF-berendezések Azure Sentinelhez való összekapcsolásához. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).

