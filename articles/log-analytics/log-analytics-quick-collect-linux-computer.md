---
title: "Adatok gyűjtése a helyszíni Linux számítógépekről az Azure Naplóelemzés |} Microsoft Docs"
description: "Útmutató a Linux Log Analytics-ügynök telepítéséhez és az adott operációs rendszer a Naplóelemzési adatok gyűjtésének engedélyezése."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/13/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: d22fe6456c3bd886f8f8863d362c0084fbe03da3
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="collect-data-from-linux-computers-hosted-in-your-environment"></a>Adatokat gyűjteni a környezetében üzemeltetett Linux rendszerű számítógépek
[Az Azure Naplóelemzés](log-analytics-overview.md) tud adatokat gyűjteni közvetlenül a fizikai vagy virtuális Linux-számítógépek és egyéb erőforrások a környezetben egy részletes elemzését és a korrelációs tárházba.  A gyors üzembe helyezés bemutatja, hogyan konfigurálja, és adatokat gyűjteni a Linux rendszerű számítógép néhány egyszerű lépésben.  Azure Linux virtuális gépek, a következő témakör [Azure virtuális gépek kapcsolatos adatok gyűjtéséhez](log-analytics-quick-collect-azurevm.md).  
 
Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure-portal"></a>Jelentkezzen be Azure-portálon
Jelentkezzen be az Azure portálon, a [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Munkaterületek létrehozása
1. Az Azure portálon kattintson **további szolgáltatások** bal alsó sarokban található. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza ki **Analytics jelentkezzen**.<br><br> ![Azure Portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. Kattintson a **létrehozása**, majd válassza ki a következő elemeket kell:

  * Adjon meg egy nevet az új **OMS-munkaterület**, például a *DefaultLAWorkspace*. 
  * A legördülő listából válassza ki azt az **előfizetést**, amelyikhez kapcsolódni szeretne, ha az alapértelmezett kiválasztás nem megfelelő.
  * A **erőforráscsoport**, válasszon ki egy meglévő erőforráscsoportot, amely tartalmaz egy vagy több Azure virtuális gépeken.  
  * Válassza ki a **hely** a virtuális gépek vannak telepítve.  További információkért lásd: amely [Naplóelemzési is elérhető régiók](https://azure.microsoft.com/regions/services/).
  * Három különböző választhat **tarifacsomagok** Naplóelemzési, de a gyors üzembe helyezés, válassza ki kívánja a **szabad** réteg.  Az adott szintek kapcsolatos további információkért lásd: [napló Analytics díjszabás](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](./media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. Miután megadta a szükséges adatokat a a **OMS-munkaterület** ablaktáblán kattintson a **OK**.  

Az **Értesítések** menüpontot kiválasztva nyomon követheti, hogyan ellenőrzi a rendszer az adatokat, és hogyan hozza létre a munkaterületet. 

## <a name="obtain-workspace-id-and-key"></a>Munkaterületének Azonosítóját és kulcs beszerzése
Linux az OMS-ügynök telepítéséhez szükséges a munkaterület azonosítója és kulcsa a Naplóelemzési munkaterületet.  Ez az információ az ügynök burkoló parancsfájl megfelelően konfigurálni az ügynököt, és sikeresen kommunikálni képes Naplóelemzési szükséges.  

1. Az Azure portálon kattintson **további szolgáltatások** bal alsó sarokban található. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza ki **Analytics jelentkezzen**.
2. A Naplóelemzési munkaterület listában válassza ki a *DefaultLAWorkspace* korábban létrehozott.
3. Válassza ki **speciális beállítások**.<br><br> ![Naplóelemzés speciális beállításai](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Válassza ki **csatlakoztatott források**, majd válassza ki **Linux kiszolgálók**.   
5. Az érték jobb oldalán **munkaterület azonosítója** és **elsődleges kulcs**. Másolással illessze be a kedvenc szerkesztő mindkettő.   

## <a name="install-the-agent-for-linux"></a>A Linux-ügynök telepítése
Az alábbi lépéseket az ügynök a Naplóelemzési telepítő Azure és az Azure Government felhő konfigurálását.  

1. A Log Analyticshez való csatlakozáshoz a Linux-számítógép konfigurálását a következő parancsot a munkaterület azonosítója és kimásolt elsődleges kulcs.  Ez a parancs letölti az ügynök, ellenőrzi az ellenőrzőösszegek és telepíti azt. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Azure Government felhőben Log Analyticshez való csatlakozáshoz a Linux-számítógép konfigurálása, a következő parancsot a munkaterület azonosítója és kimásolt elsődleges kulcs.  Ez a parancs letölti az ügynök, ellenőrzi az ellenőrzőösszegek és telepíti azt. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

## <a name="configure-agent-to-communicate-with-a-proxy-server"></a>Ügynök kommunikáljon a proxykiszolgáló konfigurálása

Hajtsa végre az alábbi lépéseket, ha a Linux rendszerű számítógépek kell szolgáltatáshoz proxykiszolgálón keresztül kommunikálnak.  A proxy konfigurációs érték szintaxisa a következő `[protocol://][user:password@]proxyhost[:port]`.

1. A fájl szerkesztése `/etc/opt/microsoft/omsagent/proxy.conf` futtassa a következő parancsokat, és módosítsa az értékeket a vonatkozó beállítások.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf 
    ```

2. Indítsa újra az ügynököt a következő parancs futtatásával: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Esemény-és teljesítményadatokat gyűjt
A Naplóelemzési eseményeket gyűjteni a Linux rendszernaplójába bejegyzett és a teljesítményszámlálók, amennyit megadott a hosszabb távú elemzési és jelentéskészítési, és hajtsa végre a műveletet egy adott esemény észlelése esetén.  Kövesse az alábbi lépéseket a Linux rendszernaplójába bejegyzett, és néhány általános teljesítményszámlálói eseményeinek gyűjtése kezdődnie konfigurálásához.  

1. Válassza ki **Syslog**.  
2. Az Eseménynapló nevében a napló beírásával vegye fel.  Típus **Syslog** és kattintson a plusz jelre  **+** .  
3. A tábla, törölje a jelet a nyomkövetés alapjául szolgáló **Info**, **értesítés** és **Debug**. 
4. Kattintson a **mentése** a konfiguráció mentéséhez, a lap tetején.
5. Válassza ki **Linux teljesítményadatokat** a Windows rendszerű számítógépeken a teljesítményszámlálók gyűjtésének engedélyezése. 
6. Új Naplóelemzési munkaterület Linux teljesítményszámlálói először konfigurálásakor lehetősége van gyorsan létrehozhat több általános jellegű számlálót. Az egyes jelölőnégyzetét szerepelnek.<br><br> ![Alapértelmezett Windows teljesítményszámlálók kijelölt](media/log-analytics-quick-collect-azurevm/linux-perfcounters-default.png).<br><br> Kattintson a **adja hozzá a kijelölt teljesítményszámlálók**.  Ezek felvétele, illetve egy második tíz gyűjtemény mintavételi időköznek az adott néven beállítás.  
7. Kattintson a **mentése** a konfiguráció mentéséhez, a lap tetején.

## <a name="view-data-collected"></a>Összegyűjtött adatok megtekintése
Most, hogy engedélyezte az adatok gyűjtését, lehetővé teszi, hogy egy egyszerű napló keresési például bizonyos adatai a célszámítógépen futtatni.  

1. Az Azure-portálon Naplóelemzési keresse meg és jelölje ki a korábban létrehozott munkaterületen.
2. Kattintson a **naplófájl-keresési** csempe és a napló Keresés ablaktábla a lekérdezés mezőtípus `Perf` , és adja meg, vagy kattintson a Keresés gombra a lekérdezés mező jobb majd nyomja le.<br><br> ![A Naplóelemzési jelentkezzen keresési lekérdezés – példa](media/log-analytics-quick-collect-linux-computer/log-analytics-portal-queryexample.png)<br><br> A lekérdezés az alábbi ábrán például 735 teljesítmény rögzíti adott vissza.<br><br> ![A Naplóelemzési naplófájl-keresési eredmény](media/log-analytics-quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szükség, az ügynök eltávolítása a Linux rendszerű számítógép, és a Naplóelemzési munkaterület törlése.  

Távolítsa el az ügynököt, hajtsa végre az alábbi lépéseket.

1. Töltse le a a Linux-ügynök [univerzális parancsfájl](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) arra a számítógépre.
2. A köteg .sh fájl w futtassa a *--kiürítése* argumentum a számítógépen, amely teljesen eltávolítja az ügynököt és konfigurációja.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

A munkaterület törléséhez válassza ki a korábban és az erőforrások lapon kattintson a létrehozott Naplóelemzési munkaterület **törlése**.<br><br> ![A Naplóelemzési erőforrás törlése](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Következő lépések
Most, hogy gyűjti a működési és a helyszíni Linux számítógépről teljesítményadatokat, könnyen megkezdheti felfedezése elemzése és a begyűjtött adatok intézkedést *szabad*.  

Megtudhatja, hogyan tekintheti meg és elemezheti az adatokat, hogy továbbra is az oktatóanyag.   

> [!div class="nextstepaction"]
> [Megtekintése vagy Naplóelemzési adatok elemzése](log-analytics-tutorial-viewdata.md)
