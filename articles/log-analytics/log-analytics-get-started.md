---
title: "Ismerkedés az Azure Log Analytics-munkaterülettel | Microsoft Docs"
description: "A Log Analyticsban a munkaterület percek alatt üzembe helyezhető."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 508716de-72d3-4c06-9218-1ede631f23a6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/08/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 7bdbdf8654ae22ba143d5f87384e04fe4a11d10e
ms.contentlocale: hu-hu
ms.lasthandoff: 08/08/2017

---
# <a name="get-started-with-a-log-analytics-workspace"></a>Ismerkedés a Log Analytics-munkaterülettel
Az Azure Log Analytics percek alatt üzembe helyezhető, és segít kiértékelni az informatikai infrastruktúrából gyűjtött operatív adatelemzési információkat. Ennek a cikknek a segítségével könnyedén nekiláthat az *ingyenesen* gyűjtött adatok felfedezésének és elemzésének, valamint reagálhat azokra.

Ez a cikk bevezetőként szolgál a Log Analytics használatába, és egy rövid oktatóanyagon keresztül végigvezeti egy minimális Azure-alapú üzemi példány létrehozásán, így rögtön el is kezdheti használni a szolgáltatást. A logikai tárolót, amely az Azure-ban a felügyeleti adatok tárolására szolgál, munkaterületnek nevezzük. Miután áttekintette ezeket az információkat, és elvégezte az értékelésüket, eltávolíthatja a kiértékelési munkaterületet. Lévén ez a cikk egy oktatóanyag, az üzleti követelményekkel, a tervezéssel és az architektúrával kapcsolatos iránymutatásokkal nem foglalkozik.

>[!NOTE]
>A Microsoft Azure Government Cloud használata esetén inkább az [Azure Government felügyeletének és kezelésének dokumentációját](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#log-analytics) érdemes áttekintenie.

Elsőként is érdemes röviden áttekinteni az Ön előtt álló folyamatot:

![folyamatábra](./media/log-analytics-get-started/onboard-oms.png)

## <a name="1-create-an-azure-account-and-sign-in"></a>1 Azure-fiók létrehozása és bejelentkezés

Ha még nem rendelkezik Azure-fiókkal, először létre kell egyet hoznia a Log Analytics használatához. Létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/), amely 30 napon keresztül működik, és ezalatt bármely Azure-szolgáltatáshoz hozzáférést biztosít.

### <a name="to-create-a-free-account-and-sign-in"></a>Ingyenes fiók létrehozása és bejelentkezés
1. Kövesse az [ingyenes Azure-fiók létrehozását](https://azure.microsoft.com/free/) ismertető utasításokat.
2. Nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be.

## <a name="2-create-a-workspace"></a>2 Munkaterület létrehozása

A következő lépés egy munkaterület létrehozása.

1. Az Azure Portalon, a Piactéren keressen a *Log Analytics* kifezezésre a szolgáltatások listájában, majd válassza ki a **Log Analytics** elemet.  
    ![Azure Portal](./media/log-analytics-get-started/log-analytics-portal.png)
2. Kattintson a **Létrehozás** gombra, majd válassza ki az egyik lehetőséget a következő elemeknél:
   * **OMS-munkaterület** – Adja meg a munkaterület nevét.
   * **Előfizetés** – Ha több előfizetéssel rendelkezik, válassza ki azt, amelyiket társítani szeretné az új munkaterülethez.
   * **Erőforráscsoport**
   * **Hely**
   * **Tarifacsomag**  
       ![gyors létrehozás](./media/log-analytics-get-started/oms-onboard-quick-create.png)
3. Kattintson az **OK** gombra a munkaterületek listájának megtekintéséhez.
4. Válasszon ki egy munkaterületet, hogy megtekinthesse a részleteit az Azure Portalon.       
    ![munkaterület részletei](./media/log-analytics-get-started/oms-onboard-workspace-details.png)         

## <a name="3-upgrade-workspace-to-new-log-search"></a>3 A munkaterület frissítése új naplókeresésre
Új Log Analytics lekérdezési nyelv érhető el. A nyelv által kínált előnyök kihasználásához át kell alakítania a munkaterületét.  Ha a régió, amelyben a munkaterület üzemeltetve van, frissül, egy lila szalagcím jelenik meg a munkaterület tetején, amely felkéri Önt az átalakításra. A frissítés teljesen önkéntes, és nincs hatással a Log Analytics és a hozzáadott megoldások által biztosított felhasználói élményre.  

Az előnyöket, a szempontokat és a frissítés folyamatát ismertető további információkért tekintse meg [az Azure Log Analytics új naplókeresésre való frissítését](log-analytics-log-search-upgrade.md) bemutató cikket.  

## <a name="4-add-solutions-and-solution-offerings"></a>4 Megoldások és megoldásajánlatok hozzáadása

A következő lépésben adjon hozzá megoldásokat és megoldásajánlatokat. A felügyeleti megoldások egy adott problématerületet érintő mérőszámokat szolgáltató logikai, adatábrázolási és adatgyűjtési szabályok gyűjteményei. A megoldásajánlat alatt csomagba foglalt felügyeleti megoldásokat értünk.

A megoldások a munkaterülethez való hozzáadásával a Log Analytics különféle adatokat lesz képes gyűjteni az ügynökök révén hozzácsatolt számítógépekről. A felvételi ügynökökről a későbbiekben lesz szó.

### <a name="to-add-solutions-and-solution-offerings"></a>Megoldások és megoldásajánlatok hozzáadása

1. Az Azure portálon kattintson az **Új** gombra, majd a **Keresés a piactéren** mezőbe írja be az **Activity Log Analytics** kifejezést, és nyomja le az ENTER billentyűt.
2. A Minden erőforrás panelen válassza ki az **Activity Log Analytics** lehetőséget, majd kattintson a **Létrehozás** gombra.  
    ![Activity Log Analytics](./media/log-analytics-get-started/activity-log-analytics.png)  
3. A *Felügyeleti megoldás neve* panelen válassza ki azt a munkaterületet, amelyet a felügyeleti megoldáshoz társítani kíván.
4. Kattintson a **Létrehozás** gombra.  
    ![megoldás munkaterület](./media/log-analytics-get-started/solution-workspace.png)  
5. Az 1–4. lépés megismétlésével adja hozzá a következőket:
    - A **Biztonság és megfelelőség** szolgáltatásajánlatot a Kártevőirtók felmérése és a Biztonság és naplózás megoldással.
    - Az **Automatizálás és vezérlés** szolgáltatásajánlatot az Automation hibrid feldolgozó, a Változáskövetés és a Rendszerfrissítési felmérés (más néven Frissítéskezelés) megoldással. A szolgáltatásajánlat hozzáadásakor létre kell hoznia egy Automation-fiókot.  
        ![Automation-fiók](./media/log-analytics-get-started/automation-account.png)  
6. A munkaterülethez hozzáadott felügyeleti megoldások áttekintéséhez lépjen a **Log Analytics** > **Előfizetések** > ***a munkaterület neve*** > **Áttekintés** felületre. Itt láthatóak a hozzáadott felügyeleti megoldások csempéi.  
    >[!NOTE]
    >Mivel még nem csatlakoztattunk ügynököket a munkaterülethez, így nem jelennek meg a hozzáadott megoldások adatai.  

    ![megoldások csempéi adatok nélkül](./media/log-analytics-get-started/solutions-no-data.png)

## <a name="4-create-a-vm-and-onboard-an-agent"></a>4 Virtuális gép létrehozása és ügynök felvétele

A következő lépésben hozzon létre egy egyszerű virtuális gépet az Azure-ban. Miután létrehozta a virtuális gépet, vegye fel, és ezáltal engedélyezze az OMS-ügynököt. Az ügynök engedélyezésével megkezdődik az adatok gyűjtése a virtuális gépen, és a továbbításuk a Log Analyticsbe.

### <a name="to-create-a-virtual-machine"></a>Virtuális gép létrehozása

- Kövesse [az első Windows rendszerű virtuális gép az Azure Portalon történő létrehozását](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ismertető témakör útmutatásait, és indítsa el az új virtuális gépet.

### <a name="connect-the-virtual-machine-to-log-analytics"></a>A virtuális gép csatlakoztatása a Log Analyticshez

- Kövesse az [Azure-beli virtuális gépek a Log Analyticshez történő csatlakoztatását](log-analytics-azure-vm-extension.md) ismertető témakör útmutatásait a virtuális gép és a Log Analytics az Azure Portal segítségével történő csatlakoztatásához.

## <a name="6-view-and-act-on-data"></a>6 Adatok megtekintése és reagálás

Előzőleg már engedélyezte az Activity Log Analytics megoldást, valamint a Biztonság és megfelelőség és az Automatizálás és vezérlés szolgáltatásajánlatot. A következő lépésben elkezdjük áttekinteni a megoldások által gyűjtött adatokat és a naplókeresések során kapott eredményeket.

Kezdésképp nézzük meg a megoldásokon belül megjelenített adatokat. Ezután nézzünk meg néhány, a naplókeresésekben elérhető keresési eredményt. A naplókeresések segítségével a környezetben lévő különféle forrásokból származó adatokat ötvözhet, és feltárhatja az összefüggéseket. További információkért tekintse meg [a Log Analyticsben végzett naplókeresésekkel kapcsolatos](log-analytics-log-searches.md) cikket, vagy ha már átalakította a munkaterületet az új lekérdezési nyelvre, tekintse meg [a Log Analytics naplókereséseit ismertető](log-analytics-log-search-new.md) cikket. 

### <a name="to-view-antimalware-data"></a>A kártevőirtó adatainak megtekintése

1. Az Azure Portalon lépjen a **Log Analytics** > ***saját munkaterület*** felületre.
2. A munkaterület panelén, az **Általános** területen kattintson az **Áttekintés** elemre.  
    ![Áttekintés](./media/log-analytics-get-started/overview.png)
3. Kattintson a **Kártevőirtók felmérése** csempére. Ebben a példában láthatja, hogy a Windows Defender telepítve van az egyik számítógépen, azonban az aláírása elavult.  
    ![Kártevőirtó](./media/log-analytics-get-started/solution-antimalware.png)
4. Példánk esetében a **Védelem állapota** területen kattintson az **Aláírás lejárt** elemre a Naplókeresés megnyitásához, ahol megtekintheti a lejárt aláírású számítógépeket. Példánkban a számítógép neve *getstarted*. Ha több olyan számítógép is van, amelyiknek lejárt az aláírása, mindegyik megjelenik a Naplókeresésben.  
    ![A kártevőirtó elavult](./media/log-analytics-get-started/antimalware-search.png)

### <a name="to-view-security-and-audit-data"></a>A biztonsági és naplózási adatok megtekintése

1. A munkaterület panelén, az **Általános** területen kattintson az **Áttekintés** elemre.  
2. Kattintson a **Biztonság és naplózás** csempére. Példánkban két jelentős problémát láthat: az egyik számítógépről kritikus frissítések hiányoznak, egy másikon pedig nincs elégséges védelem.  
    ![Biztonság és naplózás](./media/log-analytics-get-started/security-audit.png)
3. Példánk esetében a **Jelentős problémák** területen kattintson a **Kritikus frissítéseket igénylő számítógépek** elemre a Naplókeresés megnyitásához, ahol megtekintheti a kritikus frissítéseket nem tartalmazó számítógépek részleteit. Példánkban egy kritikus frissítés és 63 egyéb frissítés hiányzik.  
    ![Biztonság és naplózás naplókeresése](./media/log-analytics-get-started/security-audit-log-search.png)

### <a name="to-view-and-act-on-system-update-data"></a>Rendszerfrissítési adatok megtekintése és reagálás

1. A munkaterület panelén, az **Általános** területen kattintson az **Áttekintés** elemre.  
2. Kattintson a **Rendszerfrissítési felmérés** csempére. Példánkban azt láthatja, hogy van egy *getstarted* nevű Windows-számítógép, amelyről kritikus frissítések hiányoznak, egy másik számítógép pedig definíciófrissítésekre szorul.  
    ![Rendszerfrissítések](./media/log-analytics-get-started/system-updates.png)
3. Példánk esetében a **Hiányzó frissítések** területen kattintson a **Kritikus frissítések** elemre a Naplókeresés megnyitásához, ahol megtekintheti a kritikus frissítéseket nem tartalmazó számítógépek részleteit. Példánkban egy frissítés hiányzik és egy szükséges.  
    ![Rendszerfrissítések naplókeresése](./media/log-analytics-get-started/system-updates-log-search.png)
4. Nyissa meg az [Operations Management Suite](http://microsoft.com/oms) webhelyet, és jelentkezzen be Azure-fiókjával. Miután bejelentkezett, láthatja, hogy a megoldás adatai hasonlóak az Azure Portalon látható adatokhoz.  
    ![OMS-portál](./media/log-analytics-get-started/oms-portal.png)
5. Kattintson a **Frissítéskezelés** csempére.
6. A Frissítéskezelés irányítópultján láthatja, hogy a rendszerfrissítési adatok hasonlóak az Azure Portalon látható Rendszerfrissítés adataihoz. A **Frissítéstelepítések kezelése** csempe azonban egy új elem. Kattintson a **Frissítéstelepítések kezelése** csempére.  
    ![Frissítéskezelés csempe](./media/log-analytics-get-started/update-management.png)
7. A **Frissítéstelepítések** lapon kattintson a **Hozzáadás** gombra egy *frissítés futtatásának* létrehozásához.  
    ![Frissítéstelepítések](./media/log-analytics-get-started/update-management-update-deployments.png)
8.  Az **Új frissítéstelepítés** lapon adja meg a frissítéstelepítés nevét, válassza ki a frissíteni kívánt számítógépeket (példánkban ez a *getstarted*), válassza ki az ütemezést, majd kattintson a **Mentés** gombra.  
    ![Új telepítés](./media/log-analytics-get-started/new-deployment.png)  
    A frissítéstelepítés mentése után megjelenik az ütemezett frissítés.  
    ![ütemezett frissítés](./media/log-analytics-get-started/scheduled-update.png)  
    A frissítés futtatása után az állapot **Befejeződött** értékre vált.
    ![befejezett frissítés](./media/log-analytics-get-started/completed-update.png)
9. A frissítés futtatása után megtekintheti, hogy a futtatás sikeres volt-e vagy sem, és megtekintheti az alkalmazott frissítések részleteit.

## <a name="after-evaluation"></a>A kiértékelést követően

Ebben az oktatóanyagban egy ügynököt telepített egy virtuális gépre, és azonnal munkához is látott. A végrehajtott lépések rövidek és egyszerűek voltak. A legtöbb nagyobb szervezet és vállalat azonban összetett helyszíni informatikai infrastruktúrával rendelkezik. És az adatgyűjtés az ilyen komplex környezetekből több tervezést és erőfeszítést igényel, mint amit az oktatóanyag elvégzése jelentett. Tekintse át az alábbi, Következő lépések elnevezésű szakaszban lévő információkat a további hasznos cikkekre mutató hivatkozásokért.

Az oktatóanyagban létrehozott munkaterületet igény szerint el is távolíthatja.

## <a name="next-steps"></a>Következő lépések
* Tudnivalók a [Windows-ügynökök](log-analytics-windows-agents.md) a Log Analyticshez való csatlakoztatásáról.
* Tudnivalók az [Operations Manager-ügynökök](log-analytics-om-agents.md) a Log Analyticshez való csatlakoztatásáról.
* A funkciók hozzáadásával és az adatgyűjtéssel kapcsolatban lásd: [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Log Analytics-megoldások hozzáadása a megoldástárból).
* A [naplókeresések](log-analytics-log-searches.md) megismerése a megoldások által összegyűjtött részletes információk megtekintéséhez.

