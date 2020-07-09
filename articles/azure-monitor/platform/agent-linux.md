---
title: Linux rendszerű számítógépek összekötése Azure Monitorhoz | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan csatlakoztathatók a más felhőben vagy a helyszínen üzemeltetett Linux rendszerű számítógépek a Log Analytics Azure Monitor-ügynökkel a Linux rendszerhez.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: 9807d6eeb07b953ab75b328ce64c5166ca52dd2a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80637527"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Linux rendszerű számítógépek összekapcsolásának Azure Monitor

A helyi adatközpontban vagy más felhőalapú környezetben lévő virtuális gépek vagy fizikai számítógépek figyeléséhez és kezeléséhez Azure Monitor használatával telepítenie kell az Log Analytics ügynököt, és úgy kell konfigurálnia, hogy egy Log Analytics munkaterületre jelentsen. Az ügynök a Azure Automation hibrid Runbook feldolgozói szerepkörét is támogatja.

A Linux rendszerhez készült Log Analytics-ügynök az alábbi módszerek egyikével telepíthető. Az egyes módszerek használatával kapcsolatos részletes információkat a cikk későbbi részében találja.

* [Manuálisan töltse le és telepítse](#install-the-agent-manually) az ügynököt. Erre akkor van szükség, ha a Linux rendszerű számítógép nem fér hozzá az internethez, és az [log Analytics-átjárón](gateway.md)keresztül kommunikál Azure Monitor vagy Azure Automation. 
* [Telepítse a Linux-ügynököt](#install-the-agent-using-wrapper-script) a githubon üzemeltetett burkoló parancsfájl használatával. Ez az ajánlott módszer az ügynök telepítésére és frissítésére, ha a számítógép közvetlenül vagy egy proxykiszolgálón keresztül kapcsolódik az internethez.

A támogatott konfiguráció megismeréséhez tekintse meg a [támogatott Linux operációs rendszerek](log-analytics-agent.md#supported-linux-operating-systems) és [hálózati tűzfal konfigurációk](log-analytics-agent.md#network-requirements) részt.

>[!NOTE]
>A Linuxhoz készült Log Analytics-ügynököt nem lehet úgy konfigurálni, hogy egynél több Log Analytics-munkaterületre is jelentsen. Csak úgy konfigurálható, hogy egyszerre egy System Center Operations Manager felügyeleti csoportra és Log Analytics munkaterületre, vagy akár egyénileg is jelentsen.

## <a name="agent-install-package"></a>Ügynök telepítési csomagja

A Linux Log Analytics-ügynöke több csomagból áll. A kiadási fájl a következő csomagokat tartalmazza, amelyek a rendszerhéj-csomagnak a paraméterrel való futtatásával érhetők el `--extract` :

**Csomag** | **Verzió** | **Leírás**
----------- | ----------- | --------------
omsagent | 1.12.15 | A Linux rendszerhez készült Log Analytics-ügynök
omsconfig | 1.1.1 | A Log Analytics ügynökhöz tartozó konfigurációs ügynök
OMI | 1.6.3 | Nyissa meg a felügyeleti infrastruktúrát (a-t) – egy könnyű CIM-kiszolgálót. *Vegye figyelembe, hogy a "a" szolgáltatás működéséhez szükséges cron-feladatok futtatásához rendszergazdai hozzáférésre van szükség*
SCX | 1.6.3 | Az operációs rendszer teljesítményére vonatkozó mérőszámok az operációsrendszer-teljesítménymutatók számára
Apache – cimprov | 1.0.1 | Apache HTTP-kiszolgáló Teljesítményfigyelő szolgáltatója a következőhöz:. Csak akkor van telepítve, ha az Apache HTTP-kiszolgálót észlelte.
MySQL – cimprov | 1.0.1 | MySQL-kiszolgáló teljesítmény-figyelési szolgáltatója a következőhöz:. Csak akkor van telepítve, ha a rendszer a MySQL/MariaDB-kiszolgálót észleli.
Docker – cimprov | 1.0.0 | A következőhöz tartozó Docker-szolgáltató:. Csak akkor van telepítve, ha a Docker észlelve van.

### <a name="agent-installation-details"></a>Ügynök telepítésének részletei

A Linux-csomagok Log Analytics ügynökének telepítése után a rendszer a következő további rendszerszintű konfigurációs módosításokat alkalmazza. Ezek az összetevők el lesznek távolítva a omsagent-csomag eltávolításakor.

* A (z) nevű nem Kiemelt felhasználó `omsagent` létrejött. A démon ezen a hitelesítő adatokon fut. 
* A rendszer létrehoz egy sudo *-fájlt a* alkalmazásban `/etc/sudoers.d/omsagent` . Ez engedélyezi `omsagent` a syslog és a omsagent démonok újraindítását. Ha a sudo *include* utasítások nem támogatottak a sudo telepített verziójában, a rendszer ezeket a bejegyzéseket fogja írni a következőre: `/etc/sudoers` .
* A syslog-konfiguráció úgy módosul, hogy az események egy részhalmazát továbbítsa az ügynöknek. További információ: a [syslog-adatok gyűjtésének konfigurálása](data-sources-syslog.md).

A figyelt Linux rendszerű számítógépeken az ügynök szerepel a következőben: `omsagent` . `omsconfig`a Linux-alapú konfigurációs ügynök Log Analytics ügynöke, amely 5 percenként keresi az új portál oldal konfigurációját. Az új és frissített konfiguráció a (z) helyen található ügynök konfigurációs fájljaira lesz alkalmazva `/etc/opt/microsoft/omsagent/conf/omsagent.conf` .

## <a name="obtain-workspace-id-and-key"></a>A munkaterület-azonosító és -kulcs lekérése

A Linuxhoz készült Log Analytics-ügynök telepítése előtt szüksége lesz a Log Analytics-munkaterület azonosítójára és kulcsára. Ezek az információk szükségesek ahhoz, hogy az ügynök megfelelően konfigurálja azt, és ellenőrizze, hogy sikeresen tud-e kommunikálni Azure Monitorokkal.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. A Azure Portal bal felső sarkában válassza a **minden szolgáltatás**lehetőséget. A keresőmezőbe írja be a **log Analytics**kifejezést. A beíráskor a rendszer a bemenet alapján szűri a listákat. Válassza **log Analytics munkaterületek**lehetőséget.

2. Log Analytics munkaterületek listájában válassza ki a korábban létrehozott munkaterületet. (Lehet, hogy elnevezte a **DefaultLAWorkspace**.)

3. Válassza a **Speciális beállítások**lehetőséget:

    ![A Azure Portal Log Analytics speciális beállítások menüjében](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Válassza ki a **Csatlakoztatott források**, majd a **Linuxos kiszolgálók** elemet.

5. A **Munkaterület-azonosító** és az **Elsődleges kulcs** jobb oldalán lévő érték. Másolja ki és illessze be mindkettőt a kedvenc szerkesztőjébe.

## <a name="install-the-agent-manually"></a>Az ügynök manuális telepítése

A Linux rendszerhez készült Log Analytics-ügynök egy önkicsomagoló és telepíthető rendszerhéj-parancsfájl-csomagban van megadva. Ez a csomag minden ügynök-összetevőhöz tartalmaz Debian és RPM csomagokat, amelyeket közvetlenül vagy kinyerve telepíthet az egyes csomagok lekérésére. Az x64-es és az x86-os architektúrához egy csomag van megadva. 

> [!NOTE]
> Az Azure-beli virtuális gépek esetében javasoljuk, hogy telepítse az ügynököt a Linux rendszerhez készült [azure log Analytics](../../virtual-machines/extensions/oms-linux.md) virtuálisgép-bővítmény használatával. 

1. [Töltse le](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) és vigye át a megfelelő köteget (x64 vagy x86) a Linux rendszerű virtuális gépre vagy fizikai számítógépre, SCP/SFTP használatával.

2. Telepítse a csomagot az argumentum használatával `--install` . Ha a telepítés során Log Analytics munkaterületre szeretne bejelentkezni, adja meg a `-w <WorkspaceID>` `-s <workspaceKey>` korábban átmásolt és paramétereket.

    >[!NOTE]
    >Az argumentumot akkor kell használnia `--upgrade` , ha a függő csomagok, például a omsconfig, az SCX vagy a régebbi verziói telepítve vannak, például ha már telepítve van a System Center Operations Manager-ügynök a Linux rendszerhez. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Ha úgy szeretné konfigurálni a Linux-ügynököt, hogy Log Analytics átjárón keresztül telepítsen és kapcsolódjon egy Log Analytics-munkaterülethez, futtassa a következő parancsot a proxy, a munkaterület-azonosító és a munkaterület-kulcs paramétereinek megadásával. Ez a konfiguráció a parancssorban is megadható a következővel: `-p [protocol://][user:password@]proxyhost[:port]` . A *ProxyHost* tulajdonság a log Analytics átjárókiszolgáló teljes tartománynevét vagy IP-címét fogadja el.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Ha hitelesítésre van szükség, meg kell adnia a felhasználónevet és a jelszót. Például: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Ha úgy szeretné konfigurálni a Linux rendszerű számítógépet, hogy Azure Government felhőben Log Analytics munkaterülethez kapcsolódjon, futtassa a következő parancsot, amely a munkaterület-azonosítót és az elsődleges kulcsot a korábban másolta.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Ha az ügynök csomagjait szeretné telepíteni, és úgy konfigurálja, hogy egy adott Log Analytics munkaterületre egy későbbi időpontban jelentsen, futtassa a következő parancsot:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Ha az ügynök csomagjait az ügynök telepítése nélkül szeretné kibontani a kötegből, futtassa a következő parancsot:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>Az ügynök telepítése burkoló parancsfájl használatával

A következő lépésekkel konfigurálhatja az ügynököt az Azure-ban Log Analyticshoz, és Azure Government a felhőt a Linux rendszerű számítógépek burkoló parancsfájljának használatával, amelyek közvetlenül vagy egy proxykiszolgálón keresztül kommunikálhatnak a GitHubon üzemeltetett ügynök letöltésével és az ügynök telepítésével.  

Ha a Linux rendszerű számítógépnek egy proxykiszolgálón keresztül kell kommunikálnia a Log Analytics, akkor ez a konfiguráció a parancssorban is megadható `-p [protocol://][user:password@]proxyhost[:port]` . A *Protocol* tulajdonság fogadja `http` vagy a `https` , és a *ProxyHost* tulajdonság fogadja a proxykiszolgáló teljes tartománynevét vagy IP-címét. 

Például:`https://proxy01.contoso.com:30443`

Ha mindkét esetben hitelesítésre van szükség, meg kell adnia a felhasználónevet és a jelszót. Például:`https://user01:password@proxy01.contoso.com:30443`

1. A Linux rendszerű számítógép Log Analytics munkaterülethez való kapcsolódásának konfigurálásához futtassa a következő parancsot a munkaterület-azonosító és az elsődleges kulcs megadásával. A következő parancs letölti, majd az ellenőrzőösszeg érvényesítése után telepíti az ügynököt.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    A következő parancs tartalmazza a `-p` proxy paramétert és a példa szintaxisát, ha a proxykiszolgáló hitelesítést igényel:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Ha úgy szeretné konfigurálni a Linux rendszerű számítógépet, hogy Azure Government felhőben Log Analytics munkaterülethez kapcsolódjon, futtassa a következő parancsot, amely a munkaterület-azonosítót és az elsődleges kulcsot a korábban másolta. A következő parancs letölti, majd az ellenőrzőösszeg érvényesítése után telepíti az ügynököt. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    A következő parancs tartalmazza a `-p` proxy paramétert és a példa szintaxisát, ha a proxykiszolgáló hitelesítést igényel:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Indítsa újra az ügynököt a következő parancs futtatásával: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="upgrade-from-a-previous-release"></a>Frissítés korábbi kiadásról

A korábbi verzióról a 1.0.0-47 verziótól kezdődően minden kiadásban támogatott a frissítés. Végezze el a telepítést a `--upgrade` paraméterrel az ügynök összes összetevőjének a legújabb verzióra való frissítéséhez.

## <a name="next-steps"></a>További lépések

- Tekintse át a [Windows és Linux rendszerhez készült log Analytics ügynök felügyeletét és karbantartását](agent-manage.md) , hogy megtudja, hogyan lehet újrakonfigurálni, frissíteni vagy eltávolítani az ügynököt a virtuális gépről.

- Ha problémába ütközik az ügynök telepítésekor vagy felügyeletekor, tekintse át [a Linux-ügynök hibaelhárítását ismertető témakört](agent-linux-troubleshoot.md) .
