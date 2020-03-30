---
title: Adatok gyűjtése hibrid Windows-számítógépről az Azure Monitor segítségével
description: Ebben a rövid útmutatóban megtudhatja, hogyan telepítheti a Log Analytics-ügynököt az Azure-on kívül futó Windows-számítógépekre, és hogyan engedélyezheti az adatgyűjtést az Azure Monitor naplókkal.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/22/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: 11b61612a261b32e2d15b5dc70005b18aa112ed4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240344"
---
# <a name="collect-data-from-a-windows-computer-in-a-hybrid-environment-with-azure-monitor"></a>Adatok gyűjtése Windows-számítógépről hibrid környezetben az Azure Monitor segítségével

[Az Azure Monitor](../overview.md) a környezetében lévő fizikai vagy virtuális Windows-számítógépekről közvetlenül adatokat gyűjthet a Log Analytics-munkaterületre a részletes elemzés és korreláció szempontjából. A [Log Analytics-ügynök](../platform/log-analytics-agent.md) telepítése lehetővé teszi, hogy az Azure Monitor adatokat gyűjtsön egy adatközpontból vagy más felhőkörnyezetből. Ez a rövid útmutató bemutatja, hogyan konfigurálhatja a windowsos számítógépekről történő adatgyűjtést néhány egyszerű lépésben. Az Azure Windows virtuális gépeiről az [Azure virtuális gépekre vonatkozó adatok gyűjtése című](../../azure-monitor/learn/quick-collect-azurevm.md)témakörben talál további információt.  

A támogatott konfigurációt a [Támogatott Windows operációs rendszerek](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) és a Hálózati tűzfal [konfigurációja](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements)című témakörben ismerteti.
 
Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="sign-in-to-azure-portal"></a>Jelentkezzen be az Azure Portalon

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a . 

## <a name="create-a-workspace"></a>Munkaterület létrehozása

1. Az Azure Portalon válassza a **Minden szolgáltatás**lehetőséget. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **a Log Analytics-munkaterületek lehetőséget.**

    ![Azure portál](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Válassza **a Létrehozás**lehetőséget, majd adja meg a következő elemek hez szükséges beállításokat:

   * Adja meg az új **Log Analytics-munkaterület** nevét, például: *DefaultLAWorkspace*.  
   * A legördülő listából válassza ki azt az **előfizetést**, amelyikhez kapcsolódni szeretne, ha az alapértelmezett kiválasztás nem megfelelő.
   * Az **Erőforráscsoport** beállításnál válasszon ki egy meglévő erőforráscsoportot, amely egy vagy több Azure-beli virtuális gépet tartalmaz.  
   * Válassza ki a **Helyet** a virtuális gépek üzembehelyezési céljaként.  További információkért tekintse meg [a Log Analytics által támogatott régiókat](https://azure.microsoft.com/regions/services/).
   * Ha 2018. április 2. után létrehozott új előfizetésben hoz létre munkaterületet, az automatikusan a *GB-alapú* díjcsomagot használja, és a tarifacsomag kiválasztásának lehetősége nem érhető el.  Ha az április 2. előtt létrehozott meglévő előfizetéshez hoz létre munkaterületet, vagy pedig egy meglévő EA-regisztrációhoz kötött előfizetéshez, válassza ki a kívánt tarifacsomagot.  További információt az elérhető csomagokról [a Log Analytics részletes díjszabásában](https://azure.microsoft.com/pricing/details/log-analytics/) találhat.
  
        ![Log Analytics-erőforrás panel létrehozása](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. Miután megadta a szükséges információkat a **Log Analytics munkaterület** ido pane, válassza az OK **gombot.**  

Az **Értesítések** menüpontot kiválasztva nyomon követheti, hogyan ellenőrzi a rendszer az adatokat, és hogyan hozza létre a munkaterületet. 


## <a name="get-the-workspace-id-and-key"></a>A munkaterület-azonosító és a kulcs beszerezése

A Windows Log Analytics-ügynök (más néven a Microsoft Monitoring Agent (MMA) telepítése előtt szüksége van a munkaterület-azonosítóra és a kulcsra a Log Analytics-munkaterülethez. A telepítővarázslónak szüksége van ezekre az adatokra az ügynök megfelelő konfigurálásához és az Azure Monitorlal való kommunikáció biztosításához.  

1. Az Azure Portal bal felső sarkában válassza a **Minden szolgáltatás**lehetőséget. A keresőmezőbe írja be a **Log Analytics**kifejezést. Gépelés közben a lista a bevitt adatok alapján szűr. Válassza **a Log Analytics-munkaterületek lehetőséget.**

2. A Log Analytics-munkaterületek listájában jelölje ki a korábban létrehozott munkaterületet. (Lehet, hogy **alapértelmezettLAWorkspace-nek**nevezte el.)

3. Válassza **a Speciális beállítások lehetőséget:**

    ![A Log Analytics speciális beállításai](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
  
4. Válassza ki a **Csatlakoztatott források**, majd a **Windowsos kiszolgálók** elemet.

5. Másolja az értékeket a **munkaterület-azonosító** és az elsődleges kulcs jobb **oldalán.** Illessze be őket a kedvenc szerkesztőjéhez.

## <a name="install-the-agent-for-windows"></a>A Windowshoz készült ügynök telepítése

A következő lépések telepítik és konfigurálják az ügynököt az Azure-ban és az Azure Governmentben a Log Analytics számára. A Microsoft Monitoring Agent telepítőprogramjával telepítheti az ügynököt a számítógépre.

1. Folytatva az előző lépésektől folytatva, a **Windows-kiszolgálók** lapon válassza a Letölteni kívánt **Windows-ügynök letöltése** verziót. Válassza ki a Windows operációs rendszer processzorarchitektúrájának megfelelő verziót.

2. Futtassa a telepítőt, és telepítse az ügynököt a számítógépre.

3. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.

4. A **Licencfeltételek** oldalon olvassa el és fogadja el a licencet, majd kattintson az **Elfogadom** gombra.

5. A **Célmappa** lapon fogadja el az alapértelmezett telepítési mappát, vagy adjon meg egy másikat, majd kattintson a **Tovább** gombra.

6. Az **Ügynök beállítási beállításai** lapon csatlakoztassa az ügynököt az Azure Log Analytics szolgáltatáshoz, és válassza a **Tovább**gombot.

7. Az **Azure Log Analytics** lapon hajtsa végre az alábbi lépéseket:

   1. Illessze be a korábban másolt **munkaterület-azonosítóba** és **munkaterület-kulcsba (elsődleges kulcs).** Ha a számítógépnek jelentenie kell egy Log Analytics-munkaterületnek az Azure Government ben, válassza az **Azure US Government** lehetőséget az Azure **Cloud** listában.  
   2. Ha a számítógépnek egy proxykiszolgálón keresztül kell kommunikálnia a Log Analytics szolgáltatással, kattintson a **Speciális** gombra, majd adja meg a proxykiszolgáló URL-címét és portszámát. Ha a proxykiszolgáló hitelesítést igényel, adja meg a proxykiszolgálóval való hitelesítéshez szükséges felhasználónevet és jelszót, majd válassza a **Tovább**gombot.  

8. A konfigurációs beállítások megadása után válassza a **Tovább** gombot:

    ![Microsoft monitoring ügynök beállítása](media/quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)

9. A **Telepítésre kész** oldalon ellenőrizze a beállításokat, majd kattintson a **Telepítés** elemre.

10. A **Konfiguráció sikeresen befejeződött** lapon válassza a **Befejezés**lehetőséget.

A telepítés és a telepítés befejezése után a Microsoft Monitoring Agent megjelenik a Vezérlőpulton. Tekintse át a konfigurációt, és ellenőrizze, hogy az ügynök csatlakozik-e a Log Analytics munkaterülethez. Csatlakoztatva, az **Azure Log Analytics** lapon az ügynök ezt az üzenetet jeleníti meg: A Microsoft Monitoring Agent **sikeresen csatlakozott a Microsoft Log Analytics szolgáltatáshoz.**<br><br> ![MMA-kapcsolat állapota](media/quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Esemény- és teljesítményadatok gyűjtése

Az Azure Monitor a Windows eseménynaplójából és teljesítményszámlálóiból gyűjtheti a megadott eseményeket a hosszabb távú elemzés hez és jelentéskészítéshez. Azt is lépéseket, ha azt észleli, egy adott feltétel. A következő lépésekkel konfigurálhatja az események gyűjtését a Windows eseménynaplóból, illetve (kezdetnek) egyes gyakran használt teljesítményszámlálókból.  

1. Az Azure Portal bal alsó sarkában válassza a **További szolgáltatások**lehetőséget. A keresőmezőbe írja be a **Log Analytics**kifejezést. Gépelés közben a lista a bevitt adatok alapján szűr. Válassza **a Log Analytics-munkaterületek lehetőséget.**

2. Válassza **a Speciális beállítások lehetőséget:**

    ![A Log Analytics speciális beállításai](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
 
3. Válassza az **Adatok**, majd a **Windows Eseménynaplók** lehetőséget.  

4. A napló nevének megadásával eseménynaplót adhat hozzá. Írja be **a Rendszer**,**+** majd válassza ki a pluszjelet ( ).  

5. A táblázatban válassza a **Hiba** és **figyelmeztetés** severities lehetőséget.

6. Válassza a **Mentés** gombot a lap tetején.

7. Válassza a **Windows teljesítményszámlálók lehetőséget** a teljesítményszámlálók Windows rendszerű számítógépen való gyűjtésének engedélyezéséhez.

8. Amikor először konfigurálja a Windows teljesítményszámlálóit egy új Log Analytics-munkaterülethez, lehetősége van arra, hogy gyorsan hozzon létre több gyakori számlálót. Minden beállítás megjelenik a listában, mellette egy jelölőnégyzettel:

    ![Windows-teljesítményszámlálók](media/quick-collect-windows-computer/windows-perfcounters-default.png).
    
    Válassza **a Kijelölt teljesítményszámlálók hozzáadása**lehetőséget. A számlálók hozzáadása és beállítása tíz másodperces mintavételi időközzel történik.

9. Válassza a **Mentés** gombot a lap tetején.

## <a name="view-collected-data"></a>Összegyűjtött adatok megtekintése

Most, hogy engedélyezte az adatgyűjtést, futtasson egy egyszerű naplókeresést a célszámítógép bizonyos adatainak megtekintéséhez.  

1. A kijelölt munkaterületen a bal oldali ablaktáblában válassza a **Naplók**lehetőséget.

2. A Naplók lekérdezés lapon `Perf` írja be a lekérdezésszerkesztőt, és válassza a **Futtatás lehetőséget.**
 
    ![Log Analytics-napló keresése](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    A képen lévő lekérdezés például 10 000 teljesítményrekordot adott vissza. Ön ennél sokkal kevesebb találatot fog kapni.

    ![Log Analytics naplóbeli keresés eredménye](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Eltávolíthatja az ügynököt a számítógépről, és törölheti a Log Analytics munkaterületet, ha már nincs rájuk szüksége.  

Az ügynök eltávolításához hajtsa végre az alábbi lépéseket:

1. Nyissa meg a Vezérlőpultot.

2. Nyissa meg a **Programok és szolgáltatások** részt.

3. A **Programok és szolgáltatások területen**válassza a Microsoft Monitoring Agent ( Microsoft Monitoring **Agent** ) lehetőséget, majd az **Eltávolítás**lehetőséget.

A korábban létrehozott Log Analytics-munkaterület törléséhez jelölje ki azt, és az erőforráslapon válassza a **Törlés**lehetőséget:

![A Log Analytics munkaterületének törlése](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>További lépések

Most, hogy működési és teljesítményadatokat gyűjt a Windows rendszerű számítógépről, könnyedén elkezdheti az összegyűjtött adatok feltárását, elemzését és az alapján való *cselekvést.*  

Az adatok megtekintésének és elemzésének megismeréséhez folytassa az oktatóanyaggal:

> [!div class="nextstepaction"]
> [Adatok megtekintése és elemzése a Log Analyticsben](tutorial-viewdata.md)
