<properties
    pageTitle="Ismerkedés a Log Analytics szolgáltatással | Microsoft Azure"
    description="A Log Analytics a Microsoft Operations Management Suite-ben (OMS) percek alatt üzembe helyezhető."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/09/2016"
    ms.author="banders"/>



# Ismerkedés a Log Analytics szolgáltatással

A Log Analytics a Microsoft Operations Management Suite-ben (OMS) percek alatt üzembe helyezhető. OMS-munkaterületek létrehozásakor a fiókokhoz hasonlóan két lehetősége van:

- A Microsoft Operations Management Suite webhelye
- Microsoft Azure-előfizetés

Az OMS webhelyen ingyenes OMS-munkaterületet hozhat létre. Vagy létrehozhat OMS-munkaterületet egy Microsoft Azure-előfizetéssel is. A két munkaterület funkcionális szempontból egyenértékű, azzal a különbséggel, hogy az ingyenes OMS-munkaterület naponta csak 500 MB mennyiségű adatot képes továbbítani az OMS szolgáltatásnak. Ha Azure-előfizetést használ, ugyanazzal az előfizetéssel más Azure-szolgáltatásokhoz is hozzáférhet. A munkaterület létrehozási módszerétől függetlenül a munkaterületet egy Microsoft-fiókkal vagy egy szervezeti fiókkal fogja létrehozni.

A folyamat itt látható:

![regisztrációs diagram](./media/log-analytics-get-started/oms-onboard-diagram.png)

## A Log Analytics előfeltételei és üzembe helyezési szempontjai

- A Log Analytics teljes körű használatához fizetős Microsoft Azure-előfizetés szükséges. Ha még nincs Azure-előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/), amellyel bármely Azure-szolgáltatást elérhet. Azt is megteheti, hogy létrehoz egy ingyenes OMS-fiókot az [Operations Management Suite](http://microsoft.com/oms) webhelyén, és a **Try for free** (Ingyenes kipróbálás) lehetőségre kattint.
- Egy OMS-munkaterület
- Minden Windows rendszerű számítógépnek, amelyről adatokat szeretne gyűjteni, a Windows Server 2008 SP1 vagy újabb verzióját kell futtatnia
- [Tűzfalhozzáférés](log-analytics-proxy-firewall.md) az OMS-webszolgáltatás címeihez
- Egy [OMS Log Analytics továbbító-](https://blogs.technet.microsoft.com/msoms/2016/03/17/oms-log-analytics-forwarder) (átjáró-) kiszolgáló a forgalom kiszolgálókról az OMS-re történő továbbításához arra az esetre, ha a számítógépekről nem érhető el az internet
- Az Operations Manager használata esetén a Log Analytics az Operations Manager 2012 SP1 UR 6-os vagy újabb, illetve az Operations Manager 2012 R2 UR 2-es vagy újabb verzióját támogatja. A proxytámogatás az Operations Manager 2012 SP1 UR 7-es és az Operations Manager 2012 R2 UR 3-as verziójában jelent meg. Határozza meg, hogyan legyen integrálva az OMS-sel.
- Határozza meg, hogy a számítógépek rendelkeznek-e közvetlen internet-hozzáféréssel. Ha nem, szükségük lesz egy átjárókiszolgálóra az OMS webszolgáltatás webhelyeinek eléréséhez. Minden hozzáférés HTTPS-kapcsolaton keresztül történik.
- Határozza meg, hogy mely technológiák és kiszolgálók küldjenek adatokat az OMS-nek. Például tartományvezérlők, SQL Server stb.
- Biztosítson engedélyt a felhasználóknak az OMS-ben és az Azure-ban.
- Ha aggódik az adathasználat miatt, telepítse egyenként a megoldásokat, és tesztelje a teljesítményre gyakorolt hatásukat, mielőtt további megoldásokat adna hozzá.
- Figyelje az adathasználatot és a teljesítményt, miközben megoldásokat és szolgáltatásokat ad a Log Analyticshez. Ezek közé tartozik az eseménygyűjtés, a naplógyűjtés, a teljesítményadatok gyűjtése stb. Érdemes csak a minimális mennyiségű adatot gyűjteni az adathasználat vagy a teljesítményre gyakorolt hatás megállapításáig.
- Ügyeljen rá, hogy a Windows-ügynökök kezelése ne az Operations Managerrel történjen, különben duplikált adatok fognak keletkezni. Ez azokra az Azure-alapú ügynökökre is vonatkozik, amelyek esetében engedélyezve van az Azure Diagnostics.
- Az ügynökök telepítése után ellenőrizze, hogy azok megfelelően működne-e. Ha nem, ellenőrizze, hogy a csoportházirend nem tiltotta-e le a Kriptográfiai API következő generációja (CNG) kulcsának elkülönítését.
- Egyes Log Analytics-megoldások további követelményeket támaszthatnak



## Regisztráljon három lépésben az Operations Management Suite használatával

1. Nyissa meg az [Operations Management Suite](http://microsoft.com/oms) webhelyet, majd kattintson a **Try for free** (Ingyenes kipróbálás) lehetőségre. Jelentkezzen be a Microsoft-fiókjával, például az Outlook.com szolgáltatással, illetve a vállalata vagy oktatási intézménye által az Office 365 vagy más Microsoft-szolgáltatások használatához biztosított szervezeti fiókkal.
2. Adjon meg egy egyedi nevet a munkaterület számára. A munkaterület egy logikai tároló, amely a felügyeleti adatok tárolására szolgál. A segítségével feloszthatja az adatokat a szervezet csapatai között, mivel az adatok kizárólag azon a munkaterületen érhetők el, amelyhez tartoznak. Adjon meg egy e-mail-címet, valamint a régiót, ahol tárolni szeretné az adatokat.  
    ![munkaterület létrehozása és előfizetés társítása](./media/log-analytics-get-started/oms-onboard-create-workspace-link01.png)
3. Ezután létrehozhat egy új Azure-előfizetést, vagy kapcsolódhat egy meglévő Azure-előfizetéshez. Ha továbbra is az ingyenes próbaverziót szeretné használni, kattintson a **Most nem** lehetőségre.  
  ![munkaterület létrehozása és előfizetés társítása](./media/log-analytics-get-started/oms-onboard-create-workspace-link02.png)

Most már készen áll arra, hogy megkezdje az Operations Management Suite használatát.

A munkaterület beállításával és a meglévő Azure-fókok az Operations Management Suite-tal létrehozott munkaterületekhez való társításával kapcsolatban lásd: [Manage access to Log Analytics](log-analytics-manage-access.md) (A Log Analyticshez való hozzáférés kezelése).

## Gyors regisztráció a Microsoft Azure-ral

1. Nyissa meg az [Azure Portalt](https://portal.azure.com) és jelentkezzen be, majd a szolgáltatások listájából válassza ki a **Log Analytics (OMS)** lehetőséget.  
    ![Azure Portal](./media/log-analytics-get-started/oms-onboard-azure-portal.png)
2. Kattintson a **Hozzáadás** gombra, majd válassza ki az egyik lehetőséget a következő elemeknél:
    - **OMS-munkaterület** neve
    - **Előfizetés** – Ha több előfizetéssel rendelkezik, válassza ki azt, amelyiket társítani szeretné az új munkaterülethez.
    - **Erőforráscsoport**
    - **Hely**
    - **Tarifacsomag**  
        ![gyors létrehozás](./media/log-analytics-get-started/oms-onboard-quick-create.png)
3. Kattintson a **Létrehozás** gombra, és az Azure Portalon megjelennek a munkaterület részletei.       
    ![munkaterület részletei](./media/log-analytics-get-started/oms-onboard-workspace-details.png)         
4. Kattintson az **OMS portál** hivatkozására az új munkaterületet tartalmazó Operations Management Suite webhely megnyitásához.

Most már készen áll arra, hogy megkezdje az Operations Management Suite portál használatát.

A munkaterület beállításával és az Operations Management Suite-tal létrehozott meglévő munkaterületek Azure-előfizetésekhez társításával kapcsolatban lásd: [Manage access to Log Analytics](log-analytics-manage-access.md) (A Log Analyticshez való hozzáférés kezelése).

## Ismerkedés az Operations Management Suite portál használatával
A megoldások kiválasztásához és a felügyelni kívánt kiszolgálók csatlakoztatásához kattintson a **Beállítások** csempére, és kövesse a jelen szakaszban található lépéseket.  

![első lépések](./media/log-analytics-get-started/oms-onboard-get-started.png)  

- **Megoldások hozzáadása** – Válassza ki a használni kívánt megoldásokat, majd kattintson a **Kiválasztott megoldások hozzáadása** lehetőségre.  
    ![megoldások](./media/log-analytics-get-started/oms-onboard-solutions.png)
- **Adatforrás csatlakoztatása** – Válassza ki, hogyan szeretne csatlakozni a kiszolgálói környezethez adatgyűjtés céljából:
    - Windows Server vagy ügyfél közvetlen csatlakoztatása egy ügynök telepítésével.
    - A System Center Operations Manager használata a felügyeleti csoportok vagy a teljes Operations Manager-telepítés csatolására.
    - A Windows vagy a Linux Azure diagnosztikai virtuálisgép-bővítményével konfigurált Azure Storage-fiók használata.
        ![adatforrások](./media/log-analytics-get-started/oms-onboard-data-sources.png)    
- **Naplók hozzáadása** Konfiguráljon legalább egy adatforrást az adatai feltöltésére, majd válassza a **Mentés** lehetőséget. Az eseménynaplóknál megadhatja a megfigyelni kívánt üzenettípust, amely lehet például hiba-, figyelmeztető vagy információs üzenet.    

    ![naplók](./media/log-analytics-get-started/oms-onboard-logs.png)    


## Egy ügynök telepítésével közvetlenül is csatlakoztathat kiszolgálókat az Operations Management Suite-hez
1. Kattintson a **Beállítások** csempére, a **Csatlakoztatott források** lapra, majd a célszámítógép architektúrájának megfelelő **Windows-ügynök letöltése** lehetőségre. Az ügynök csak Windows Server 2008 SP 1 vagy újabb, illetve Windows 7 SP1 vagy újabb rendszeren telepíthető.
2. Telepítse az ügynököt egy vagy több kiszolgálón. Az ügynököket telepítheti egyenként, vagy egy automatizáltabb módszerrel, [egyéni parancsfájl](log-analytics-windows-agents.md) segítségével, illetve használhat meglévő szoftvermegoldást is.
3. A licencszerződés feltételeinek elfogadása és a telepítési mappa kiválasztása után jelölje be a **Connect the agent to Microsoft Azure Operational Insights** (Ügynök csatlakoztatása a Microsoft Azure Operational Insightshoz) lehetőséget. (Az OMS korábbi neve Operational Insights).  
    ![ügynök telepítése](./media/log-analytics-get-started/oms-onboard-agent.png)

4. A következő oldalon adja meg a munkaterület-azonosítót és a munkaterületkulcsot. A munkaterület-azonosítót és a -kulcsot azon a képernyőn találja meg, ahonnan letöltötte az ügynökfájlt.  
    ![ügynökkulcsok](./media/log-analytics-get-started/oms-onboard-mma-keys.png)
    ![kiszolgálók csatolása](./media/log-analytics-get-started/oms-onboard-key.png)
5. Telepítés közben az **Advanced** (Speciális) elemre kattintva beállíthatja a proxykiszolgálót, és megadhatja a hitelesítő adatokat. Kattintson a **Next** (Tovább) gombra a munkaterület adatait tartalmazó képernyőre való visszatéréshez.
6. Kattintson a **Next** (Tovább) gombra a munkaterület-azonosító és -kulcs érvényesítéséhez. Ha a rendszer bármilyen hibát észlel, a **Back** (Vissza) gombra kattintva javíthatja a megadottakat. A munkaterület-azonosító és -kulcs érvényesítése után kattintson az **Install** (Telepítés) gombra az ügynök telepítésének befejezéséhez.
7. Jelentkezzen be újra az Operations Management Suite portálra, és az Áttekintés lapon kattintson a **Beállítások** csempére. Amikor az ügynökök kommunikálnak az Operations Management Suite szolgáltatással, egy zöld pipa ikon jelenik meg. Ez első alkalommal körülbelül 5–10 percet vesz igénybe.

>[AZURE.NOTE] Az Operations Management Suite-hez közvetlenül csatlakozó kiszolgálók jelenleg nem támogatják a kapacitáskezelési és a konfigurációértékelési megoldásokat.


Az ügynököt a System Center Operations Manager 2012 SP1 vagy újabb verziójához is csatlakoztathatja. Ehhez válassza ki a **Connect the agent to System Center Operations Manager** (Ügynök csatlakoztatása a System Center Operations Managerhez) elemet. Ha ezt a lehetőséget választja, anélkül küld adatokat a szolgáltatásnak, hogy további hardverekre, vagy a felügyeleti csoportok terhelésének növelésére lenne szükség.

Az ügynökök az Operations Management Suite-hez való csatlakoztatásával kapcsolatos további információkért lásd: [Connect Windows computers to Log Analytics](log-analytics-windows-agents.md) (Windows rendszerű számítógépek csatlakoztatása a Log Analyticshez).

## A System Center Operations Managerrel is csatlakoztathat kiszolgálókat

1. Az Operations Manager konzolján válassza az **Adminisztráció** lehetőséget.
2. Bontsa ki az **Operational Insights** csomópontot, és válassza ki az **Operational Insights-kapcsolat** elemet.

  >[AZURE.NOTE] Attól függően, hogy az SCOM melyik kumulatív frissítését használja, megjelenhet egy *System Center Advisor*, *Operational Insights* vagy *Operations Management Suite* nevű csomópont.

3. Kattintson a jobb felső részen található **Regisztráció az Operational Insights szolgáltatásba** hivatkozásra, és kövesse az utasításokat.
4. A regisztrációs varázsló befejezését követően kattintson a **Számítógép/csoport hozzáadása** hivatkozásra.
5. A **Számítógép keresése** párbeszédpanelen az Operations Manager által megfigyelt számítógépekre vagy csoportokra kereshet. Válassza ki azokat a számítógépeket vagy csoportokat, amelyeket be szeretne vezetni a Log Analyticsbe, és kattintson a **Hozzáadás**, majd az **OK** gombra. Úgy ellenőrizheti, hogy az OMS szolgáltatás fogad-e adatokat, hogy az Operations Management Suite portálon megnyitja a **Használat** csempét. Az adatok körülbelül 5–10 perc múlva jelennek meg.

Az Operations Manager az Operations Management Suite-hez való csatlakoztatásával kapcsolatos információkért lásd: [Connect Operations Manager to Log Analytics](log-analytics-om-agents.md) (Az Operations Manager csatlakoztatása a Log Analyticshez).

## Felhőszolgáltatásokból származó adatokat is elemezhet a Microsoft Azure-ban

Ha engedélyezi a diagnosztikát az Azure Cloud Services számára, az Operations Management Suite-tel gyorsan kereshet a felhőszolgáltatások és a virtuális gépek esemény- és IIS-naplóiban. A Microsoft Monitoring Agent telepítésével további elemzésekhez juthat az Azure virtuális gépekhez. Az Azure-környezet az Operations Management Suite használatára való konfigurálásával kapcsolatos további információkért lásd: [Connect Azure storage to Log Analytics](log-analytics-azure-storage.md) (Azure Storage csatlakoztatása a Log Analyticshez).


## Következő lépések

- A funkciók hozzáadásával és az adatgyűjtéssel kapcsolatban lásd: [Add Log Analytics solutions from the Solutions Gallery](log-analytics-add-solutions.md) (Log Analytics-megoldások hozzáadása a megoldástárból).
- Ismerkedjen meg a [naplókeresésekkel](log-analytics-log-searches.md), hogy megtekinthesse a megoldások által összegyűjtött részletes információkat.
- Használjon [irányítópultokat](log-analytics-dashboards.md) a saját egyéni kereséseinek mentéséhez és megjelenítéséhez.



<!--HONumber=Sep16_HO4-->


