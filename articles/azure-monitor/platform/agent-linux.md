---
title: Linux-számítógépek csatlakoztatása az Azure Monitorhoz | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan csatlakoztathatja a más felhőkben vagy a helyszínen tárolt Linux-számítógépeket az Azure Monitorhoz a Linux-naplóügynökkel.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: 40c279a4beee9fbebe2de7f272fe51d9039f071c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668706"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Linux-számítógépek csatlakoztatása az Azure Monitorhoz

A virtuális gépek vagy fizikai számítógépek figyeléséhez és kezeléséhez a helyi adatközpontban vagy más felhőalapú környezetben az Azure Monitor segítségével, telepítenie kell a Log Analytics-ügynököt, és konfigurálnia kell, hogy jelentsen egy Log Analytics-munkaterületnek. Az ügynök is támogatja a hibrid Runbook feldolgozó szerepkör az Azure Automation.

A Log Analytics-ügynök Linuxhoz az alábbi módszerek egyikével telepíthető. Az egyes módszerek használatával kapcsolatos részleteket a cikk későbbi részében találja meg.

* [Manuálisan töltse le és telepítse](#install-the-agent-manually) az ügynököt. Erre akkor van szükség, ha a Linux-számítógép nem fér hozzá az internethez, és a [Log Analytics-átjárón](gateway.md)keresztül kommunikál az Azure Monitorral vagy az Azure Automationszolgáltatással. 
* Telepítse a Linux-ügynök a GitHubon tárolt [wrapper-script használatával.](#install-the-agent-using-wrapper-script) Ez az ajánlott módszer az ügynök telepítéséhez és frissítéséhez, ha a számítógép közvetlenül vagy proxykiszolgálón keresztül kapcsolódik az internethez.

A támogatott konfiguráció megismeréséhez tekintse meg a [támogatott Linux operációs rendszerek](log-analytics-agent.md#supported-linux-operating-systems) és [hálózati tűzfal konfigurációk](log-analytics-agent.md#network-firewall-requirements) részt.

>[!NOTE]
>A Linuxhoz készült Log Analytics-ügynököt nem lehet úgy konfigurálni, hogy egynél több Log Analytics-munkaterületre is jelentsen. Csak úgy konfigurálható, hogy egyszerre jelentsen a System Center Operations Manager felügyeleti csoportjának és a Log Analytics-munkaterületnek, vagy külön-külön jelentsen.

## <a name="agent-install-package"></a>Ügynök telepítőcsomagja

A Log Analytics-ügynök Linux több csomagból áll. A kiadási fájl a következő csomagokat tartalmazza, amelyek a `--extract` shell köteg paraméterrel való futtatásával érhetők el:

**Csomag** | **Verzió** | **Leírás**
----------- | ----------- | --------------
omsagent | 1.12.15 | A Log Analytics Ügynök Linux
omsconfig között | 1.1.1 | A Log Analytics-ügynök konfigurációs ügynöke
Omi | 1.6.3 | Nyílt felügyeleti infrastruktúra (OMI) - egy könnyű CIM-kiszolgáló. *Ne feledje, hogy az OMI root hozzáférést igényel a szolgáltatás működéséhez szükséges cron feladat futtatásához*
Scx | 1.6.3 | OMI CIM-szolgáltatók az operációs rendszerek teljesítménymutatóihoz
apache-cimprov között | 1.0.1 | Apache HTTP Server teljesítményfigyelő szolgáltató az OMI-hoz. Csak akkor telepítve, ha apache HTTP Server észlelésre kerül.
mysql-cimprov között | 1.0.1 | MySQL Server teljesítményfigyelő szolgáltató az OMI-hoz. Csak akkor települ, ha a Rendszer MySQL/MariaDB kiszolgálót észlel.
docker-cimprov között | 1.0.0 | Docker-szolgáltató az OMI-hoz. Csak akkor telepítve, ha a Docker észlelése.

### <a name="agent-installation-details"></a>Ügynök telepítésének részletei

A Log Analytics ügynök Linux-csomagok telepítése után a következő további rendszerszintű konfigurációs módosítások at alkalmazzuk. Ezek a műtermékek az omsagent csomag eltávolításakor törlődnek.

* Egy nem kiemelt jogosultságú `omsagent` felhasználó neve: jön létre. A démon ezzel a hitelesítő adatokkal fut. 
* A sudoers *közé* fájl `/etc/sudoers.d/omsagent`jön létre a . Ez engedélyezi `omsagent` a syslog és az omsagent démonok újraindítását. Ha a sudo *tartalmazza* az irányelveket nem támogatja a sudo `/etc/sudoers`telepített verziója, akkor ezek a bejegyzések a program a programba kerülnek.
* A syslog konfiguráció javunkra módosul, hogy továbbítsa az események egy részhalmazát az ügynöknek. További információ: [Configure Syslog data collection](data-sources-syslog.md).

Egy figyelt Linux-számítógépen az ügynök `omsagent`a . `omsconfig`a Log Analytics ügynök Linux konfigurációs ügynök, amely 5 percenként új portáloldali konfigurációt keres. Az új és frissített konfiguráció a rendszer a `/etc/opt/microsoft/omsagent/conf/omsagent.conf`rendszer a helyen található ügynök konfigurációs fájljaira vonatkozik.

## <a name="obtain-workspace-id-and-key"></a>A munkaterület-azonosító és -kulcs lekérése

A Linuxhoz készült Log Analytics-ügynök telepítése előtt szüksége lesz a Log Analytics-munkaterület azonosítójára és kulcsára. Ez az információ szükséges az ügynök beállítása során a megfelelő konfigurálása, és győződjön meg arról, hogy sikeresen kommunikálni az Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. Az Azure Portal bal felső sarkában válassza a **Minden szolgáltatás**lehetőséget. A keresőmezőbe írja be a **Log Analytics**kifejezést. Gépelés közben a lista a bevitt adatok alapján szűr. Válassza **a Log Analytics-munkaterületek lehetőséget.**

2. A Log Analytics-munkaterületek listájában jelölje ki a korábban létrehozott munkaterületet. (Lehet, hogy **alapértelmezettLAWorkspace-nek**nevezte el.)

3. Válassza **a Speciális beállítások lehetőséget:**

    ![A Log Analytics speciális beállítások menüje az Azure Portalon](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Válassza ki a **Csatlakoztatott források**, majd a **Linuxos kiszolgálók** elemet.

5. A **Munkaterület-azonosító** és az **Elsődleges kulcs** jobb oldalán lévő érték. Másolja ki és illessze be mindkettőt a kedvenc szerkesztőjébe.

## <a name="install-the-agent-manually"></a>Az ügynök manuális telepítése

A Log Analytics-ügynök Linux hoz egy önkibontható és telepíthető rendszerhéj-csomagban érhető el. Ez a csomag Debian és RPM csomagokat tartalmaz az egyes ügynök-összetevőkhöz, és közvetlenül telepíthető vagy kibontható az egyes csomagok lekéréséhez. Egy csomag x64 és egy x86 architektúrák. 

> [!NOTE]
> Az Azure virtuális gépek, azt javasoljuk, hogy telepítse az ügynök őket az [Azure Log Analytics virtuálisgép-bővítmény](../../virtual-machines/extensions/oms-linux.md) Linuxhoz. 

1. [Töltse le](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) és vigye át a megfelelő csomagot (x64 vagy x86) a Linux virtuális gépre vagy fizikai számítógépre az scp/sftp használatával.

2. Telepítse a köteget `--install` az argumentum segítségével. A telepítés során a Log Analytics-munkaterületre `-w <WorkspaceID>` `-s <workspaceKey>` való alaplaphoz adja meg a korábban másolt paramétereket és paramétereket.

    >[!NOTE]
    >Az `--upgrade` argumentumot akkor kell használnia, ha olyan függő csomagok vannak telepítve, mint az omi, az scx, az omsconfig vagy a régebbi verzióik, ahogy az akkor is előfordulhat, ha a System Center Operations Manager ügynök linuxos verziója már telepítve van. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Ha a Linux-ügynököt úgy szeretné konfigurálni, hogy egy Log Analytics-átjárón keresztül telepítsen és csatlakozzon egy Log Analytics-munkaterülethez, futtassa a következő parancsot a proxy, a munkaterület-azonosító és a munkaterületi kulcs paramétereinek megadásával. Ez a konfiguráció a parancssorban megadható a . `-p [protocol://][user:password@]proxyhost[:port]` A *proxyhost* tulajdonság elfogadja a Log Analytics átjárókiszolgáló teljesen minősített tartománynevét vagy IP-címét.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Ha hitelesítésre van szükség, meg kell adnia a felhasználónevet és a jelszót. Példa: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Ha úgy szeretné konfigurálni a Linux-számítógépet, hogy az Azure Government-felhőben egy Log Analytics-munkaterülethez csatlakozzon, futtassa a következő parancsot, amely korábban másolja a munkaterület-azonosítót és az elsődleges kulcsot.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Ha telepíteni szeretné az ügynökcsomagokat, és úgy szeretné beállítani, hogy egy későbbi időpontban jelentsen egy adott Log Analytics-munkaterületnek, futtassa a következő parancsot:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Ha az ügynök csomagokat az ügynök telepítése nélkül szeretné kivonni a csomagból, futtassa a következő parancsot:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>Az ügynök telepítése burkolóparancsfájllal

A következő lépések konfigurálják az ügynök beállítását az Azure-ban és az Azure Government-felhőben a linuxos számítógépek wrapper parancsfájljának használatával, amelyek közvetlenül vagy proxykiszolgálón keresztül kommunikálhatnak a GitHubon üzemeltetett ügynök letöltéséhez és az ügynök telepítéséhez.  

Ha a Linux-számítógépnek proxykiszolgálón keresztül kell kommunikálnia a Log Analytics szolgáltatással, ez a konfiguráció a parancssorban megadható a . `-p [protocol://][user:password@]proxyhost[:port]` A *protokolltulajdonság* `http` elfogadja `https`vagy , és a *proxyhost* tulajdonság elfogadja a proxykiszolgáló teljesen minősített tartománynevét vagy IP-címét. 

Például:`https://proxy01.contoso.com:30443`

Ha mindkét esetben hitelesítésre van szükség, meg kell adnia a felhasználónevet és a jelszót. Például:`https://user01:password@proxy01.contoso.com:30443`

1. Ha a Linux-számítógépet úgy szeretné konfigurálni, hogy egy Log Analytics-munkaterülethez csatlakozzon, futtassa a következő parancsot a munkaterület-azonosító és az elsődleges kulcs megadásával. A következő parancs letölti, majd az ellenőrzőösszeg érvényesítése után telepíti az ügynököt.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    A következő parancs `-p` tartalmazza a proxyparamétert és a példaszintaxist, ha a proxykiszolgálóhitelesítésre van szükség:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Ha úgy szeretné konfigurálni a Linux-számítógépet, hogy az Azure Government-felhőben csatlakozzon a Log Analytics-munkaterülethez, futtassa a következő parancsot, amely korábban másolja a munkaterület-azonosítót és az elsődleges kulcsot. A következő parancs letölti, majd az ellenőrzőösszeg érvényesítése után telepíti az ügynököt. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    A következő parancs `-p` tartalmazza a proxyparamétert és a példaszintaxist, ha a proxykiszolgálóhitelesítésre van szükség:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Indítsa újra az ügynököt a következő parancs futtatásával: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="upgrade-from-a-previous-release"></a>Frissítés egy korábbi kiadásról

Az 1.0.0-47-es verziótól kezdődően egy korábbi verzióról történő frissítést minden kiadás támogatja. Végezze el a `--upgrade` telepítést a paraméterrel az ügynök összes összetevőjének a legújabb verzióra való frissítéséhez.

## <a name="next-steps"></a>További lépések

- Tekintse át [a Windows és Linux Log Analytics-ügynök kezelése és karbantartása](agent-manage.md) című, az ügynök újrakonfigurálásának, frissítésének és eltávolításának megismerése a virtuális gépről című útmutatót.

- Tekintse [át a Linux-ügynök hibaelhárítása](agent-linux-troubleshoot.md) című témakört, ha problémákat tapasztal az ügynök telepítése vagy kezelése közben.
