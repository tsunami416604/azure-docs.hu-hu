---
title: 'Rövid útmutató: Adatok gyűjtése hibrid Linux-számítógépről az Azure Monitor segítségével'
description: Ebben a rövid útmutatóban megtudhatja, hogyan telepítheti a Log Analytics-ügynököt az Azure-on kívül futó Linux-számítógépekre, és hogyan engedélyezheti az adatgyűjtést az Azure Monitor naplókkal.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/24/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 1da862a7994d7db5ee8c9f090119e14f19c6b13a
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637894"
---
# <a name="quickstart-collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>Rövid útmutató: Adatok gyűjtése linuxos számítógépről hibrid környezetben az Azure Monitor segítségével

[Az Azure Monitor](../overview.md) adatokat gyűjthet közvetlenül a fizikai vagy virtuális Linux-számítógépek a környezetben egy Log Analytics munkaterület részletes elemzés és korreláció. A [Log Analytics-ügynök](../platform/log-analytics-agent.md) telepítése lehetővé teszi, hogy az Azure Monitor adatokat gyűjtsön egy adatközpontból vagy más felhőkörnyezetből. Ez a rövid útmutató bemutatja, hogyan konfigurálhatja és gyűjthet adatokat a Linux-kiszolgálóról néhány egyszerű lépéssel. Az Azure Linux virtuális gépeiről az [Azure virtuális gépekre vonatkozó adatok gyűjtése című](../../azure-monitor/learn/quick-collect-azurevm.md)témakörben talál.  

A támogatott konfigurációt a [Támogatott Windows operációs rendszerek](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) és a Hálózati tűzfal [konfigurációja](../../azure-monitor/platform/log-analytics-agent.md#network-requirements)című témakörben ismerteti.
 
Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a . 

## <a name="create-a-workspace"></a>Munkaterület létrehozása

1. Az Azure Portalon válassza a **Minden szolgáltatás**lehetőséget. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **a Log Analytics-munkaterületek lehetőséget.**

    ![Log Analytics-munkaterület keresése az Azure Portalon](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Válassza **a Létrehozás**lehetőséget, majd adja meg a következő elemek hez szükséges beállításokat:

   * Adja meg az új **Log Analytics-munkaterület** nevét, például: *DefaultLAWorkspace*.  
   * A legördülő listából válassza ki azt az **előfizetést**, amelyikhez kapcsolódni szeretne, ha az alapértelmezett kiválasztás nem megfelelő.
   * Az **Erőforráscsoport** beállításnál válasszon ki egy meglévő erőforráscsoportot, amely egy vagy több Azure-beli virtuális gépet tartalmaz.  
   * Válassza ki a **Helyet** a virtuális gépek üzembehelyezési céljaként.  További információkért tekintse meg [a Log Analytics által támogatott régiókat](https://azure.microsoft.com/regions/services/).
   * Ha 2018. április 2. után létrehozott új előfizetésben hoz létre munkaterületet, az automatikusan a *GB-alapú* díjcsomagot használja, és a tarifacsomag kiválasztásának lehetősége nem érhető el.  Ha az április 2. előtt létrehozott meglévő előfizetéshez hoz létre munkaterületet, vagy pedig egy meglévő EA-regisztrációhoz kötött előfizetéshez, válassza ki a kívánt tarifacsomagot.  További információt az elérhető csomagokról [a Log Analytics részletes díjszabásában](https://azure.microsoft.com/pricing/details/log-analytics/) találhat.
  
        ![Log Analytics-munkaterület létrehozása az Azure Portalon](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Miután megadta a szükséges információkat a **Log Analytics munkaterület** ido pane, válassza az OK **gombot.**  

Az **Értesítések** menüpontot kiválasztva nyomon követheti, hogyan ellenőrzi a rendszer az adatokat, és hogyan hozza létre a munkaterületet. 

## <a name="obtain-workspace-id-and-key"></a>A munkaterület-azonosító és -kulcs lekérése

A Linuxhoz készült Log Analytics-ügynök telepítése előtt szüksége lesz a Log Analytics-munkaterület azonosítójára és kulcsára. Ezt az információt az ügynök burkoló parancsfájl megfelelő konfigurálásához az ügynök, és győződjön meg arról, hogy sikeresen kommunikálni az Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. Az Azure Portal bal felső sarkában válassza a **Minden szolgáltatás**lehetőséget. A keresőmezőbe írja be a **Log Analytics**kifejezést. Gépelés közben a lista a bevitt adatok alapján szűr. Válassza **a Log Analytics-munkaterületek lehetőséget.**

2. A Log Analytics-munkaterületek listájában jelölje ki a korábban létrehozott munkaterületet. (Lehet, hogy **alapértelmezettLAWorkspace-nek**nevezte el.)

3. Válassza **a Speciális beállítások lehetőséget:**

    ![A Log Analytics speciális beállítások menüje az Azure Portalon](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Válassza ki a **Csatlakoztatott források**, majd a **Linuxos kiszolgálók** elemet.

5. A **Munkaterület-azonosító** és az **Elsődleges kulcs** jobb oldalán lévő érték. Másolja ki és illessze be mindkettőt a kedvenc szerkesztőjébe.

## <a name="install-the-agent-for-linux"></a>A Linuxhoz készült ügynök telepítése

Az alábbi lépésekkel konfigurálhatóak az ügynök beállításai a Log Analytics használatához az Azure és az Azure Government felhőben.  

>[!NOTE]
>A Linuxhoz készült Log Analytics-ügynököt nem lehet úgy konfigurálni, hogy egynél több Log Analytics-munkaterületre is jelentsen.  

Ha a Linux rendszerű számítógépnek proxykiszolgálón keresztül kell kommunikálnia a Log Analytics szolgáltatással, a proxykonfiguráció a parancssorban a `-p [protocol://][user:password@]proxyhost[:port]` záradékkal adható meg.  A *proxyhost* tulajdonság a proxykiszolgáló teljes tartománynevét vagy IP-címét fogadja el. 

Például:`https://user01:password@proxy01.contoso.com:30443`

1. Ha úgy szeretné konfigurálni a Linux-számítógépet, hogy egy Log Analytics-munkaterülethez csatlakozzon, futtassa a következő parancsot, amely korábban másolja a munkaterület-azonosítót és az elsődleges kulcsot. A következő parancs letölti, majd az ellenőrzőösszeg érvényesítése után telepíti az ügynököt. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    A következő parancs `-p` tartalmazza a proxyparamétert és a példaszintaxist, ha a proxykiszolgálóhitelesítésre van szükség:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Ha úgy szeretné konfigurálni a Linux-számítógépet, hogy az Azure Government-felhőben csatlakozzon a Log Analytics-munkaterülethez, futtassa a következő parancsot, amely korábban másolja a munkaterület-azonosítót és az elsődleges kulcsot. A következő parancs letölti, majd az ellenőrzőösszeg érvényesítése után telepíti az ügynököt. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    A következő parancs `-p` tartalmazza a proxyparamétert és a példaszintaxist, ha a proxykiszolgálóhitelesítésre van szükség:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```

3. Indítsa újra az ügynököt a következő parancs futtatásával: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Esemény- és teljesítményadatok gyűjtése

Az Azure Monitor gyűjthet eseményeket a Linux Syslog és teljesítményszámlálók, amelyek hosszabb távú elemzés és jelentéskészítés. Azt is lépéseket, ha azt észleli, egy adott feltétel. A következő lépésekkel konfigurálhatja az események gyűjtését a Linux rendszernaplójából, illetve egyes gyakran használt teljesítményszámlálókból is.  

1. Az Azure Portal bal alsó sarkában válassza a **További szolgáltatások**lehetőséget. A keresőmezőbe írja be a **Log Analytics**kifejezést. Gépelés közben a lista a bevitt adatok alapján szűr. Válassza **a Log Analytics-munkaterületek lehetőséget.**

2. Válassza **az Adatok**lehetőséget, majd a **Syslog**lehetőséget.  

3. A syslog hozzáadásához írja be a napló nevét. Írja be **a Syslog értéket,** majd válassza ki a pluszjelet **+**.  

4. A táblában törölje a **Tájékoztatás**, az **Értesítés** és a **Hibakeresés** súlyossági szint jelölését. 

5. A konfiguráció mentéséhez válassza a lap tetején található **Mentés** lehetőséget.

6. Válassza a **Linuxos teljesítményadatok** elemet a teljesítményszámláló-adatok gyűjtésének Linux rendszerű gépen történő engedélyezéséhez. 

7. Amikor először konfigurálja egy új Log Analytics-munkaterület Linux-teljesítményszámlálóit, akkor több gyakran használt számlálót is gyorsan létrehozhat. Ezek mindegyike mellett egy jelölőnégyzet található.

    ![Az Azure Monitorban kiválasztott alapértelmezett Linux-teljesítményszámlálók](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Válassza **az Alkalmaz alatt a konfigurációt a gépeimre,** majd a Kiválasztott **teljesítményszámlálók hozzáadása**lehetőséget. A rendszer hozzáadja a kiválasztott számlálókat, és mindegyikhez beállít egy tíz másodperces mintagyűjtési időszakot.  

8. A konfiguráció mentéséhez válassza a lap tetején található **Mentés** lehetőséget.

## <a name="view-data-collected"></a>Összegyűjtött adatok megtekintése

Most, hogy engedélyezte az adatgyűjtést, futtasson le egy egyszerű naplóbeli keresést, hogy megtekinthessen néhány, a célszámtógépekről származó adatot.  

1. A kijelölt munkaterületen a bal oldali ablaktáblában válassza a **Naplók**lehetőséget.

2. A Naplók lekérdezés lapon `Perf` írja be a lekérdezésszerkesztőt, és válassza a **Futtatás lehetőséget.**
 
    ![Log Analytics-napló keresése](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Az alábbi képen lévő lekérdezés például 10 000 teljesítményrekordot adott vissza. Ön ennél sokkal kevesebb találatot fog kapni.

    ![Log Analytics naplóbeli keresés eredménye](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, eltávolíthatja az ügynököt a linuxos számítógépről, és törölheti a Log Analytics-munkaterületet.  

Az ügynök eltávolításához futtassa az alábbi parancsot a Linux rendszerű számítógépen. A *--purge* argumentum teljesen eltávolítja az ügynököt és annak konfigurációját.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

A munkaterület törléséhez jelölje ki a korábban létrehozott Log Analytics munkaterületet, és az erőforráslapon válassza a **Törlés**lehetőséget.

![Log Analytics-erőforrás törlése](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>További lépések

Most, hogy már gyűjti a működési és teljesítményadatokat a linuxos számítógépéről, könnyen nekiláthat az *ingyenesen* gyűjtött adatok felfedezésének és elemzésének, és reagálhat a kapott eredményekre.  

Az adatok megtekintésének és elemzésének ismertetéséhez lépjen tovább az útmutatóhoz.

> [!div class="nextstepaction"]
> [Adatok megtekintése és elemzése a Log Analyticsben](../../azure-monitor/learn/tutorial-viewdata.md)
