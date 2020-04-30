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
ms.date: 04/19/2020
ms.author: yelevin
ms.openlocfilehash: 5a8b97e5bef57b29f388c86628f0af5d05e1724a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81731661"
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
- A Linux gépen telepítve kell lennie a pythonnak.<br>A parancs `python -version` használatával keresse meg a következőt:.
- A Linux rendszerű számítógép nem csatlakoztatható Azure-munkaterületekhez az Log Analytics-ügynök telepítése előtt.

## <a name="run-the-deployment-script"></a>Az üzembe helyezési parancsfájl futtatása
 
1. Az Azure Sentinel navigációs menüjében kattintson az **adatösszekötők**elemre. Az összekötők listájában kattintson a **Common Event Format (CEF)** csempére, majd a jobb alsó sarokban található **összekötő megnyitása lap** gombra. 

1. A **1,2 alatt telepítse a CEF-gyűjtőt a Linux**rendszerű gépre, másolja a **következő szkript futtatásával elérhető hivatkozást a CEF-gyűjtő telepítéséhez és alkalmazásához**, vagy az alábbi szövegből:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. A szkript futtatása közben ellenőrizze, hogy nem kap-e hibaüzenetet vagy figyelmeztető üzenetet.

Folytassa a [2. lépéssel: a biztonsági megoldás konfigurálása a CEF-üzenetek továbbítására](connect-cef-solution-config.md) .

## <a name="deployment-script-explained"></a>Üzembe helyezési parancsfájl ismertetése

Az alábbi parancs a telepítési parancsfájl műveleteinek parancssori leírását adja meg.

A megfelelő leírás megtekintéséhez válassza ki a syslog démont.

# <a name="rsyslog-daemon"></a>[rsyslog démon](#tab/rsyslog)

1. **Az Log Analytics-ügynök letöltése és telepítése:**

    - Letölti a Log Analytics (OMS) Linux-ügynök telepítési parancsfájlját<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - A Log Analytics ügynök telepítése<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **A syslog démon konfigurálása:**

    1. Megnyitja a 514-es portot a TCP-kommunikációhoz `/etc/rsyslog.conf`a syslog konfigurációs fájljának használatával.

    1. Úgy konfigurálja a démont, hogy továbbítsa a CEF üzeneteket a Log Analytics-ügynöknek a 25226-as TCP- `security-config-omsagent.conf` porton. ehhez egy `/etc/rsyslog.d/`speciális konfigurációs fájlt kell beszúrnia a syslog démon könyvtárába.

        A `security-config-omsagent.conf` fájl tartalma:

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. **A syslog démon újraindítása**

    `service rsyslog restart`

1. **A Log Analytics-ügynök konfigurációjának beállítása a 25226-es port figyelésére és a CEF üzenetek továbbítására az Azure Sentinel számára**

    1. A konfiguráció letöltése a Log Analytics Agent GitHub-adattárból<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Újraindítja a Log Analytics ügynököt<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[syslog-ng démon](#tab/syslogng)

1. **Az Log Analytics-ügynök letöltése és telepítése:**

    - Letölti a Log Analytics (OMS) Linux-ügynök telepítési parancsfájlját<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - A Log Analytics ügynök telepítése<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **A syslog démon konfigurálása:**

    1. Megnyitja a 514-es portot a TCP-kommunikációhoz `/etc/syslog-ng/syslog-ng.conf`a syslog konfigurációs fájljának használatával.

    1. Úgy konfigurálja a démont, hogy továbbítsa a CEF üzeneteket a Log Analytics-ügynöknek a 25226-as TCP- `security-config-omsagent.conf` porton. ehhez egy `/etc/syslog-ng/conf.d/`speciális konfigurációs fájlt kell beszúrnia a syslog démon könyvtárába.

        A `security-config-omsagent.conf` fájl tartalma:

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. **A syslog démon újraindítása**

    `service syslog-ng restart`

1. **A Log Analytics-ügynök konfigurációjának beállítása a 25226-es port figyelésére és a CEF üzenetek továbbítására az Azure Sentinel számára**

    1. A konfiguráció letöltése a Log Analytics Agent GitHub-adattárból<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Újraindítja a Log Analytics ügynököt<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan helyezheti üzembe a Log Analytics-ügynököt a CEF-berendezések Azure Sentinelhez való összekapcsolásához. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).

