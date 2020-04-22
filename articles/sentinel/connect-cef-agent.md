---
title: A naplótovábbító üzembe helyezése a CEF-adatok Azure Sentinelhez való csatlakoztatásához | Microsoft dokumentumok
description: Ismerje meg, hogyan telepítheti az ügynököt a CEF-adatok Azure Sentinelhez való csatlakoztatásához.
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
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731661"
---
# <a name="step-1-deploy-the-log-forwarder"></a>1. lépés: A naplótovábbító telepítése


Ebben a lépésben kijelöli és konfigurálja a Linux-gépet, amely továbbítja a naplókat a biztonsági megoldásból az Azure Sentinel-munkaterületre. Ez a gép lehet egy fizikai vagy virtuális gép a helyszíni környezetben, egy Azure virtuális gép, vagy egy virtuális gép egy másik felhőben. A megadott hivatkozás használatával futtat egy parancsfájlt a kijelölt gépen, amely a következő feladatokat hajtja végre:
- Telepíti a Log Analytics ügynök Linux (más néven az OMS-ügynök) és konfigurálja azt a következő célokra:
    - CEF üzenetek hallgatása a beépített Linux Syslog démonról a 25226-os TCP-porton
    - az üzenetek biztonságos küldése a TLS-en keresztül az Azure Sentinel-munkaterületre, ahol azok elemzésre kerülnek és gazdagodnak

- A beépített Linux Syslog démon (rsyslog.d/syslog-ng) konfigurálása a következő célokra:
    - Syslog üzenetek figyelése a biztonsági megoldásokból az 514-es TCP-porton
    - csak a CEF-ként azonosított üzenetek továbbítása a Localhost Log Analytics-ügynökének a 25226-os TCP-port használatával
 
## <a name="prerequisites"></a>Előfeltételek

- Emelt szintű engedélyekkel (sudo) kell rendelkeznie a kijelölt Linux-gépen.
- A Python-t telepítenikell a Linux gépen.<br>A `python -version` parancs segítségével ellenőrizze.
- A Linux-gép nem csatlakozik egyetlen Azure-munkaterülethez sem a Log Analytics-ügynök telepítése előtt.

## <a name="run-the-deployment-script"></a>A központi telepítési parancsfájl futtatása
 
1. Az Azure Sentinel navigációs menüjében kattintson az **Adatösszekötők parancsra.** Az összekötők listájában kattintson a **Közös eseményformátum (CEF)** csempére, majd az **Összekötő lap megnyitása** gombra a jobb alsó sarokban. 

1. Az **1.2 alatt telepítse a CEF gyűjtőt a Linux gépre**, másolja a Következő szkript futtatása csoportban található linket a **CEF gyűjtő telepítéséhez és alkalmazásához,** vagy az alábbi szövegből:

     `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`

1. A parancsfájl futása közben ellenőrizze, hogy nem kap-e hibaüzenetet vagy figyelmeztető üzenetet.

Tovább a [2.](connect-cef-solution-config.md)

## <a name="deployment-script-explained"></a>A központi telepítési parancsfájl magyarázata

Az alábbiakban a központi telepítési parancsfájl műveletek parancs-by-command leírása.

Válassza ki a syslog démon, hogy a megfelelő leírást.

# <a name="rsyslog-daemon"></a>[rsyslog démon](#tab/rsyslog)

1. **A Log Analytics-ügynök letöltése és telepítése:**

    - Letölti a Telepítési parancsfájlt a Log Analytics (OMS) Linux ügynökhöz<br>
        `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - A Log Analytics-ügynök telepítése<br>
        `sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **A Syslog démon konfigurálása:**

    1. Megnyitja az 514-es portot a TCP-kommunikációhoz a syslog konfigurációs fájl `/etc/rsyslog.conf`használatával.

    1. Úgy állítja be a démont, hogy a 25226-os TCP-porton továbbítsa `security-config-omsagent.conf` a CEF-üzeneteket a `/etc/rsyslog.d/`Log Analytics-ügynöknek, egy speciális konfigurációs fájl beszúrásával a syslog démonkönyvtárba.

        A `security-config-omsagent.conf` fájl tartalma:

            :rawmsg, regex, "CEF\|ASA" ~
            *.* @@127.0.0.1:25226

1. **A Syslog démon újraindítása**

    `service rsyslog restart`

1. **A Log Analytics-ügynök konfigurációjának beállítása a 25226-os porton való figyeléshez és a CEF-üzenetek továbbításához az Azure Sentinelnek**

    1. Letölti a konfigurációt a Log Analytics-ügynök GitHub-tárházból<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Újraindítja a Log Analytics-ügynököt<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

# <a name="syslog-ng-daemon"></a>[syslog-ng démon](#tab/syslogng)

1. **A Log Analytics-ügynök letöltése és telepítése:**

    - Letölti a Telepítési parancsfájlt a Log Analytics (OMS) Linux ügynökhöz<br>`wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh`

    - A Log Analytics-ügynök telepítése<br>`sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com`

1. **A Syslog démon konfigurálása:**

    1. Megnyitja az 514-es portot a TCP-kommunikációhoz a syslog konfigurációs fájl `/etc/syslog-ng/syslog-ng.conf`használatával.

    1. Úgy állítja be a démont, hogy a 25226-os TCP-porton továbbítsa `security-config-omsagent.conf` a CEF-üzeneteket a `/etc/syslog-ng/conf.d/`Log Analytics-ügynöknek, egy speciális konfigurációs fájl beszúrásával a syslog démonkönyvtárba.

        A `security-config-omsagent.conf` fájl tartalma:

            filter f_oms_filter {match(\"CEF\|ASA\" ) ;};
            destination oms_destination {tcp(\"127.0.0.1\" port("25226"));};
            log {source(s_src);filter(f_oms_filter);destination(oms_destination);};

1. **A Syslog démon újraindítása**

    `service syslog-ng restart`

1. **A Log Analytics-ügynök konfigurációjának beállítása a 25226-os porton való figyeléshez és a CEF-üzenetek továbbításához az Azure Sentinelnek**

    1. Letölti a konfigurációt a Log Analytics-ügynök GitHub-tárházból<br>
        `wget -o /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/omsagent.d/security_events.conf`


    1. Újraindítja a Log Analytics-ügynököt<br>
        `/opt/microsoft/omsagent/bin/service_control restart [workspaceID]`

---

## <a name="next-steps"></a>További lépések
Ebben a dokumentumban megtanulta, hogyan telepítheti a Log Analytics-ügynököt a CEF-készülékek Azure Sentinelhez való csatlakoztatásához. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:
- Ismerje meg, hogyan [kaphat betekintést az adatokba és a potenciális fenyegetésekbe.](quickstart-get-visibility.md)
- Az Azure Sentinel segítségével első lépések [a fenyegetések észleléséhez.](tutorial-detect-threats.md)

