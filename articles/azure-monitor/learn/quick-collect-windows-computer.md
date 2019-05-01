---
title: Az Azure Log Analytics-ügynök Windows-számítógépeken a hibrid konfigurálása |} A Microsoft Docs
description: Ebben a rövid útmutatóban fog megtudhatja, hogyan helyezheti üzembe a Log Analytics-ügynököt Windows rendszerű Azure-on kívül, és engedélyezze az adatgyűjtést a Log Analytics használatával.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 99be0cee9c939ed200bd74c94e88c3fcd989e25b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64722894"
---
# <a name="configure-the-log-analytics-agent-for-windows-computers-in-a-hybrid-environment"></a>Hibrid környezetben a Log Analytics-ügynököket Windows-számítógépek konfigurálása
[Az Azure Log Analytics](../../azure-monitor/platform/agent-windows.md) tud adatokat gyűjteni közvetlenül a fizikai vagy virtuális Windows-számítógépek egy adattárba, részletes elemzés és összehasonlítás céljából. A log Analytics tud adatokat gyűjteni egy adatközpontban vagy egyéb felhőalapú környezetben. Ez a rövid útmutató bemutatja, hogyan konfigurálhatja a windowsos számítógépekről történő adatgyűjtést néhány egyszerű lépésben.  Azure Windows virtuális gépek kapcsolatos információkért lásd: [Azure-beli virtuális gépek adatainak gyűjtését](../../azure-monitor/learn/quick-collect-azurevm.md).  

A támogatott konfiguráció megismeréséhez tekintse meg [támogatott Windows operációs rendszerek](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) és [hálózati tűzfal-konfiguráció](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).
 
Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-workspace"></a>Munkaterület létrehozása
1. Az Azure Portalon válassza a **Minden szolgáltatás** elemet. A Keresés mezőbe írja be a **Log Analytics**. Írja be, mert a szűrők megjelenítése a bemenet alapján. Válassza ki **Log Analytics**:

    ![Azure Portal](media/quick-collect-windows-computer/azure-portal-01.png)
  
2. Válassza ki **létrehozás**, majd adja meg ezeket az adatokat:

   * Adjon meg egy nevet az új **Log Analytics-munkaterület**. Hasonló **DefaultLAWorkspace**.
   * Válassza ki a **előfizetés** összekapcsolása. Ha az alapértelmezett nem az, amelyiket Ön szeretne használni, válassza ki a listából egy másik.
   * Az a **erőforráscsoport**, válasszon ki egy meglévő erőforráscsoportot, amely tartalmaz egy vagy több Azure-beli virtuális gépek.  
   * Válassza ki a **Helyet** a virtuális gépek üzembehelyezési céljaként. Ez egy lista, [régióban, amelyben a Log Analytics elérhető](https://azure.microsoft.com/regions/services/).  
   * A munkaterület 2018. április 2. után létrehozott előfizetés létrehozásánál a munkaterületet automatikusan használja a **GB** tarifacsomagot. Ön nem fog tudni tarifacsomag kiválasztása. Ha egy előfizetést, amely 2018. április 2. előtt létrehozott vagy egy meglévő EA-regisztrációhoz kötött előfizetéshez hoz létre egy munkaterületet, válassza ki a használni kívánt tarifacsomagot. Tekintse meg a [Log Analytics szolgáltatás díjszabását](https://azure.microsoft.com/pricing/details/log-analytics/) információ szintekről.

        ![Log Analytics-erőforrás létrehozása](media/quick-collect-windows-computer/create-loganalytics-workspace-02.png)<br>  

3. Miután megadta a szükséges adatokat a **Log Analytics-munkaterület** ablaktáblán válassza előbb **OK**.  

Bár az adatokat a rendszer ellenőrzi, és a munkaterület létrehozását, nyomon követheti a folyamat állapotát **értesítések** menüjében.

## <a name="get-the-workspace-id-and-key"></a>A munkaterület Azonosítóját és kulcsát
Mielőtt telepítené a Microsoft Monitoring Agent a Windows, kell-e a munkaterület Azonosítóját és kulcsát a Log Analytics-munkaterület. A telepítő varázslónak megfelelően konfigurálni az ügynököt, és ellenőrizze a Log Analytics képes kommunikálni, ezt az információt.  

1. Az Azure portal bal felső sarkában válassza **minden szolgáltatás**. A Keresés mezőbe írja be a **Log Analytics**. Írja be, mert a szűrők megjelenítése a bemenet alapján. Válassza a **Log Analytics** elemet.
2. A Log Analytics-munkaterületek listájában válassza ki a korábban létrehozott munkaterületet. (Előfordulhat, hogy elnevezett azt **DefaultLAWorkspace**.)
3. Válassza ki **speciális beállítások**:

    ![A log Analytics speciális beállításai](media/quick-collect-windows-computer/log-analytics-advanced-settings-01.png)
  
4. Válassza ki a **Csatlakoztatott források**, majd a **Windowsos kiszolgálók** elemet.
5. Az értékek másolásához jobbra **munkaterület-Azonosítót** és **elsődleges kulcs**. Illessze be őket a kedvenc szerkesztőjébe.

## <a name="install-the-agent-for-windows"></a>A Windowshoz készült ügynök telepítése
Az alábbi lépéseket telepítése és konfigurálása az ügynök a Log Analytics az Azure és az Azure Government. Telepítse az ügynököt a számítógépre a Microsoft Monitoring Agent beállítása program fogja használni.

1. A korábbi lépésekben-készletből a Folytatás a **Windows kiszolgálók** lapon válassza ki a **Windows-ügynök letöltése** letölteni kívánt verzió. Válassza ki a megfelelő verzióját a processzor architektúrájától, a Windows operációs rendszer számára.
2. Futtassa a telepítőt, és telepítse az ügynököt a számítógépre.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.
3. A **Licencfeltételek** oldalon olvassa el és fogadja el a licencet, majd kattintson az **Elfogadom** gombra.
4. A **Célmappa** lapon fogadja el az alapértelmezett telepítési mappát, vagy adjon meg egy másikat, majd kattintson a **Tovább** gombra.
5. Az a **ügynök telepítésének beállításai** lapon, az ügynök csatlakoztatása az Azure Log Analyticshez, majd **tovább**.
6. Az a **Azure Log Analytics** lapon, a lépések végrehajtása:
   1. Illessze be a **munkaterület-Azonosítót** és **Munkaterületkulcsot (elsődleges kulcs)** , amely korábban vágólapra másolt. Ha a Log Analytics-munkaterületet az Azure Government szolgáltatásban a kell jelentenie, válassza ki a **Azure US Government** a a **Azure Cloud** listája.  
   2. Ha a számítógépnek egy proxykiszolgálón keresztül kell kommunikálnia a Log Analytics szolgáltatással, kattintson a **Speciális** gombra, majd adja meg a proxykiszolgáló URL-címét és portszámát. Ha a proxykiszolgáló hitelesítést igényel, adja meg a felhasználónevet és jelszót a proxykiszolgáló hitelesítést, majd **tovább**.  
7. Válassza ki **tovább** a konfigurációs beállítások hozzáadása után:

    ![A Microsoft Monitoring Agent beállítása](media/quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)

8. A **Telepítésre kész** oldalon ellenőrizze a beállításokat, majd kattintson a **Telepítés** elemre.
9. Az a **konfigurálása sikeresen befejeződött** lapon jelölje be **Befejezés**.

Ha a telepítés és a telepítés befejeződött, a Vezérlőpult Microsoft Monitoring Agent jelenik meg. Áttekintheti a konfigurációt, és ellenőrizheti, hogy az ügynök csatlakozik-e a Log Analyticshez. Ha csatlakozik, az a **Azure Log Analytics** fülön az ügynök ezt az üzenetet jeleníti meg: **A Microsoft Monitoring Agent sikeresen csatlakozott a Microsoft Log Analytics szolgáltatásba.**<br><br> ![MMA kapcsolati állapota](media/quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Esemény- és teljesítményadatok gyűjtése
A log Analytics megadott események tud gyűjteni a Windows Eseménynapló és a teljesítményszámlálók a hosszabb távú elemzésekhez és jelentéskészítéshez. A művelet is, ha észleli egy adott feltétel is eltarthat. A következő lépésekkel konfigurálhatja az események gyűjtését a Windows eseménynaplóból, illetve (kezdetnek) egyes gyakran használt teljesítményszámlálókból.  

1. Az Azure portal bal alsó sarkában válassza **további szolgáltatások**. A Keresés mezőbe írja be a **Log Analytics**. Írja be, mert a szűrők megjelenítése a bemenet alapján. Válassza a **Log Analytics** elemet.
2. Válassza ki **speciális beállítások**:

    ![A log Analytics speciális beállításai](media/quick-collect-windows-computer/log-analytics-advanced-settings-01.png)
 
3. Válassza az **Adatok**, majd a **Windows Eseménynaplók** lehetőséget.  
4. Hozzáadhat egy eseménynapló írja be a napló nevét. Adja meg **rendszer**, majd válassza a plusz jelre (**+**).  
5. A táblázatban jelölje be a **hiba** és **figyelmeztetés** súlyossági.
6. Válassza ki **mentése** az oldal tetején.
7. Válassza ki **Windows-teljesítményszámlálók** Windows számítógép-teljesítményszámlálók gyűjtésének engedélyezéséhez.
8. Amikor először konfigurálja egy új Log Analytics-munkaterület Windows-teljesítményszámlálók, felhőszolgáltatására, gyorsan létrehozhat több gyakran használt számlálót. Minden beállítás mellett egy jelölőnégyzet bejelölésével szerepel:

    ![Windows-teljesítményszámlálók](media/quick-collect-windows-computer/windows-perfcounters-default.png).
    
    Válassza ki **a kijelölt teljesítményszámlálók felvétele**. A számlálók a hozzáadott és a egy gyűjtemény tíz másodperces mintavételi időköz az adott néven beállítás.

9. Válassza ki **mentése** az oldal tetején.

## <a name="view-collected-data"></a>Gyűjtött adatok megtekintése
Most, hogy engedélyezte az adatgyűjtést, futtassa egy egyszerű naplóbeli keresés, a célszámtógépekről származó bizonyos adatok megtekintéséhez.  

1. Az Azure Portalon, a kiválasztott munkaterületen válassza ki a **naplóbeli keresés** csempére.  
2. Az a **naplóbeli keresés** ablaktáblán, a lekérdezés mezőben adja meg **Teljesítményoptimalizált** majd nyomja le az Enter vagy válassza ki a lekérdezési mezőtől jobbra található Keresés gombra:
 
    ![Log Analytics naplóbeli keresés](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    A lekérdezés ezen a képen például 735 teljesítményrekordot adott vissza:

    ![Log Analytics naplóbeli keresés eredménye](media/quick-collect-windows-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az ügynök eltávolítása a számítógépről, és a Log Analytics-munkaterület törlése, ha már nincs szüksége rájuk.  

Az ügynök eltávolításához hajtsa végre ezeket a lépéseket:

1. Open Control Panel.
2. Nyissa meg a **Programok és szolgáltatások** részt.
3. A **programok és szolgáltatások**válassza **Microsoft Monitoring Agent** majd **Eltávolítás**.

A korábban létrehozott Log Analytics-munkaterület törléséhez válassza ki, és, az erőforrás oldalán válassza **törlése**:

![Naplóelemzési munkaterület törlése](media/quick-collect-windows-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>További lépések
Most, hogy működési gyűjt és a teljesítményadatokat a Windows-számítógépről, könnyen nekiláthat feltárása, elemzése, és a gyűjtött adatok alapján, a *ingyenes*.  

Ismerje meg, hogyan tekintheti meg és elemezheti az adatokat, folytassa az oktatóanyagot:

> [!div class="nextstepaction"]
> [Adatok megtekintése és elemzése a Log Analyticsben](tutorial-viewdata.md)
