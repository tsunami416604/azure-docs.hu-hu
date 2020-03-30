---
title: Az Active Directory replikációs állapotának figyelése
description: Az Active Directory replikációs állapot megoldáscsomagja rendszeresen figyeli az Active Directory környezetét a replikációs hibák miatt.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/24/2018
ms.openlocfilehash: 30b0c7c87f6d55586b931be1445b175ce58565d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055902"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Az Active Directory replikációs állapotának figyelése az Azure Monitorsegítségével

![AD replikációs állapot szimbóluma](./media/ad-replication-status/ad-replication-status-symbol.png)

Az Active Directory a vállalati informatikai környezet kulcsfontosságú összetevője. A magas rendelkezésre állás és a nagy teljesítmény biztosítása érdekében minden tartományvezérlő saját példányt kapott az Active Directory adatbázisból. A tartományvezérlők replikálnak egymással, hogy a változásokat a vállalaton belül propagálják. A replikációs folyamat hibái számos problémát okozhatnak a vállalaton belül.

Az AD replikációs állapot megoldás rendszeresen figyeli az Active Directory környezetében a replikációs hibákat.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>A megoldás telepítése és konfigurálása
A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat.

### <a name="prerequisites"></a>Előfeltételek

* Az AD replikációs állapot megoldáshoz a .  Az ügynököt a System Center 2016 – Operations Manager, Operations Manager 2012 R2 és az Azure Monitor használja.
* A megoldás támogatja a Windows Server 2008 és 2008 R2, Windows Server 2012 és 2012 R2 és Windows Server 2016 rendszert futtató tartományvezérlőket.
* A Log Analytics munkaterületet, hogy adja hozzá az Active Directory állapot-ellenőrzési megoldás az Azure-piactérről az Azure Portalon. Nincs szükség további konfigurációra.


### <a name="install-agents-on-domain-controllers"></a>Ügynökök telepítése tartományvezérlőkön
A kiértékelendő tartományvezérlőkön ügynököket kell telepítenie. Vagy telepítenie kell az ügynököket a tagkiszolgálókon, és konfigurálnia kell az ügynököket, hogy aD replikációs adatokat küldjenek az Azure Monitornak. Ha tudni szeretné, hogyan csatlakoztathatja a Windows-számítógépeket az Azure Monitorhoz, olvassa el [a Windows-számítógépek csatlakoztatása az Azure Monitorhoz](../../azure-monitor/platform/agent-windows.md). Ha a tartományvezérlő már része egy meglévő System Center Operations Manager-környezetnek, amelyet csatlakozni szeretne az Azure Monitorhoz, olvassa el [az Operations Manager csatlakoztatása az Azure Monitorhoz](../../azure-monitor/platform/om-agents.md)című témakört.

### <a name="enable-non-domain-controller"></a>Nem tartományvezérlő engedélyezése
Ha egyik tartományvezérlőjét sem szeretné közvetlenül csatlakoztatni az Azure Monitorhoz, a tartomány bármely, az Azure Monitorhoz csatlakoztatott számítógépével adatokat gyűjthet az AD replikációs állapotmegoldás-csomagjához, és elküldheti az adatokat.

1. Ellenőrizze, hogy a számítógép tagja-e annak a tartománynak, amelyet az AD replikációs állapot megoldásával figyelni kíván.
2. [Csatlakoztassa a Windows-számítógépet az Azure Monitorhoz,](../../azure-monitor/platform/om-agents.md) vagy [csatlakoztassa a meglévő Operations Manager-környezet használatával az Azure Monitorhoz,](../../azure-monitor/platform/om-agents.md)ha még nincs csatlakoztatva.
3. Ezen a számítógépen állítsa be a következő beállításkulcsot:<br>Kulcs: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management\<Groups ManagementGroupName>\Solutions\ADReplication**<br>Érték: **IsTarget**<br>Érték adatok: **igaz**

   > [!NOTE]
   > Ezek a módosítások csak a Microsoft Monitoring Agent szolgáltatás (HealthService.exe) újraindítása után lépnek érvénybe.
   > ### <a name="install-solution"></a>Megoldás telepítése
   > Kövesse a [Figyelési megoldás telepítése](solutions.md#install-a-monitoring-solution) című témakörben ismertetett eljárást az **Active Directory replikációs állapot** megoldásának a Log Analytics-munkaterülethez való hozzáadásához. Nincs szükség további konfigurációra.


## <a name="ad-replication-status-data-collection-details"></a>Az AD replikációs állapot adatgyűjtésének részletei
Az alábbi táblázat az adatgyűjtési módszereket és az AD replikációs állapothoz szükséges adatok gyűjtésének egyéb részleteit mutatja be.

| 
    platform
   | Közvetlen ügynök | SCOM ügynök | Azure Storage | SCOM szükséges? | A felügyeleti csoporton keresztül küldött SCOM-ügynökadatok | gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |ötnaponta |



## <a name="understanding-replication-errors"></a>A replikációs hibák ismertetése

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Az AD replikációs állapot csempe jelenleg hány replikációs hibával rendelkezik. **A kritikus replikációs hibák** olyan hibák, amelyek az Active Directory-erdő [törlésre kései élettartamának](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) 75%-át meghaladják.

![AD replikáció állapota csempe](./media/ad-replication-status/oms-ad-replication-tile.png)

Amikor a csempére kattint, további információkat tekinthet meg a hibákról.
![AD replikáció állapota irányítópult](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>A célkiszolgáló állapota és a forráskiszolgáló állapota
Ezek az oszlopok a replikációs hibákat tapasztaló cél- és forráskiszolgálók állapotát mutatják. Az egyes tartományvezérlők neve utáni szám az adott tartományvezérlő replikációs hibáinak számát jelzi.

A célkiszolgálók és a forráskiszolgálók hibái azért jelennek meg, mert egyes problémákat könnyebb elhárítani a forráskiszolgáló és mások a célkiszolgáló szempontjából.

Ebben a példában láthatja, hogy sok célkiszolgáló nagyjából ugyanannyi hibával rendelkezik, de van egy forráskiszolgáló (ADDC35), amely sokkal több hibát tartalmaz, mint az összes többi. Valószínű, hogy van valami probléma az ADDC35-ön, ami miatt nem küld adatokat a replikációs partnereinek. Az ADDC35-tel kapcsolatos problémák megoldása számos, a célkiszolgáló területén megjelenő hibát megoldhatja.

### <a name="replication-error-types"></a>Replikációs hibatípusok
Ez a terület tájékoztatást nyújt a vállalaton belül észlelt hibák típusáról. Minden hibának van egy egyedi numerikus kódja és egy üzenete, amely segíthet meghatározni a hiba kiváltó okát.

A fánk tetején ad egy ötletet, hogy mely hibák jelennek meg egyre ritkábban a környezetben.

Megmutatja, ha több tartományvezérlő ugyanazt a replikációs hibát tapasztalja. Ebben az esetben előfordulhat, hogy felderít vagy azonosít egy megoldást az egyik tartományvezérlőn, majd megismételheti azt más tartományvezérlőkön, amelyeket ugyanaz a hiba érint.

### <a name="tombstone-lifetime"></a>Tombstone élettartama
A törlésre kijelölt terület élettartama határozza meg, hogy egy törölt objektum, a továbbiakban: törlésre kerülő objektum mennyi ideig marad meg az Active Directory-adatbázisban. Amikor egy törölt objektum átmegy a törlésre kijelölt terület élettartamán, a szemétgyűjtési folyamat automatikusan eltávolítja azt az Active Directory-adatbázisból.

A törlésre kijelölt alapértelmezett élettartam a Windows legújabb verzióinak 180 napja, a régebbi verziókesetében azonban 60 nap, és az Active Directory rendszergazdája explicit módon módosítható.

Fontos tudni, hogy vannak-e olyan replikációs hibák, amelyek közelednek, vagy a törlésre való letiltás élettartama elmúlt. Ha két tartományvezérlő olyan replikációs hibát tapasztal, amely a törlésre szolgáló kő élettartama után is megmarad, a replikáció le van tiltva a két tartományvezérlő között, még akkor is, ha az alapul szolgáló replikációs hiba ki van javítva.

A Tombstone Lifetime terület segít azonosítani azokat a helyeket, ahol a letiltott replikáció veszélyben van. A **100%-os TSL-kategória minden** hibája olyan partíciót jelöl, amely legalább az erdő törlési kő élettartama során nem replikálódott a forrás- és a célkiszolgáló között.

Ebben az esetben a replikációs hiba egyszerű javítása nem lesz elegendő. Legalább manuálisan kell megvizsgálnia a fennmaradó objektumok azonosítását és karbantartását a replikáció újraindítása előtt. Előfordulhat, hogy le kell szerelnie egy tartományvezérlőt.

A törlésre szolgáló kő élettartama után megőrzött replikációs hibák azonosítása mellett az **50-75% TSL** vagy **75-100% TSL** kategóriákba tartozó hibákra is figyelni kell.

Ezek a hibák, amelyek egyértelműen elhúzódó, nem átmeneti, így valószínűleg szükség van a beavatkozás megoldására. A jó hír az, hogy még nem érték el a sírkő élettartamát. Ha azonnal és a törlésre várókő élettartama *előtt* megoldja ezeket a problémákat, a replikáció minimális manuális beavatkozással újraindulhat.

Amint azt korábban megjegyeztük, az AD replikációs állapot megoldásának irányítópult-csempéje a kritikus *replikációs* hibák számát mutatja a környezetben, amely a törlésre kijelölt terület élettartamának több mint 75%-át teszi ki (beleértve a TSL több mint 100%-át kihasználó hibákat is). Arra törekszünk, hogy ezt a számot 0-on tartsuk.

> [!NOTE]
> A törlésre szolgáló kő élettartamának százalékos értéke az Active Directory-erdő tényleges törlésre készült élettartamán alapul, így biztos lehet benne, hogy ezek a százalékok pontosak, még akkor is, ha egyéni törlésre való élettartamú érték van beállítva.
>
>

### <a name="ad-replication-status-details"></a>Az AD replikációállapotának részletei
Amikor valamelyik lista valamelyik elemére kattint, a naplólekérdezés használatával további részleteket láthat róla. Az eredmények szűrése úgy történt, hogy csak az adott cikkhez kapcsolódó hibák jelenjenek meg. Ha például a **Célkiszolgáló állapota (ADDC02)** csoportban felsorolt első tartományvezérlőre kattint, a lekérdezési eredmények szűrve jelennek meg, hogy az adott tartományvezérlő a célkiszolgálóként jelenjen meg:

![AD replikációs állapothibák a lekérdezési eredményekben](./media/ad-replication-status/oms-ad-replication-search-details.png)

Itt tovább szűrhet, módosíthatja a naplólekérdezést, és így tovább. Az Azure Monitor naplólekérdezéseinek használatáról a [Naplóadatok elemzése az Azure Monitorban](../../azure-monitor/log-query/log-query-overview.md)című témakörben talál további információt.

A **HelpLink** mező ben a TechNet-lap URL-címe látható, amely az adott hibával kapcsolatos további részleteket tartalmazza. Ezt a hivatkozást a böngészőablakba másolhatja és beillesztheti, hogy megtekintse a hibaelhárítással és a hiba kijavításával kapcsolatos információkat.

**Az Exportálás** gombra kattintva is exportálhatja az eredményeket az Excelbe. Az adatok exportálása segíthet a replikációs hibaadatok bármilyen módon történő megjelenítésében.

![exportált AD replikációs állapothibák az Excelben](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>AD replikációs állapot – gyakori kérdések
**K: Milyen gyakran frissülnek az AD replikációs állapotadatai?**
A: Az információ ötnaponta frissül.

**K: Van mód annak konfigurálására, hogy milyen gyakran frissüljenek ezek az adatok?**
A: Jelenleg nem.

**K: Hozzá kell adnom az összes tartományvezérlőmet a Log Analytics-munkaterületemhez a replikáció állapotának megtekintéséhez?**
A: Nem, csak egyetlen tartományvezérlőt kell hozzáadni. Ha több tartományvezérlővel rendelkezik a Log Analytics-munkaterületen, az összes adat küldése az Azure Monitornak.

**K: Nem szeretnék tartományvezérlőket hozzáadni a Log Analytics-munkaterülethez. Továbbra is használhatom az AD replikációs állapot megoldást?**

V: Igen. Az engedélyezéshez beállíthatja a rendszerleíró kulcs értékét. Lásd: [Nem tartományvezérlő engedélyezése](#enable-non-domain-controller).

**K: Mi a neve annak a folyamatnak, amely az adatgyűjtést végzi?**
A: AdvisorAssessment.exe

**K: Mennyi ideig tart az adatok gyűjtése?**
V: Az adatgyűjtési idő az Active Directory-környezet méretétől függ, de általában kevesebb mint 15 percet vesz igénybe.

**K: Milyen típusú adatokat gyűjtenek?**
A: A replikációs adatok gyűjtése LDAP-on keresztül történik.

**K: Van mód az adatgyűjtés kor történő konfigurálására?**
A: Jelenleg nem.

**K: Milyen engedélyekre van szükségem az adatgyűjtéshez?**
A: Az Active Directory normál felhasználói engedélyei elegendőek.

## <a name="troubleshoot-data-collection-problems"></a>Adatgyűjtési problémák elhárítása
Az adatok gyűjtéséhez az AD replikációs állapotmegoldás-csomaghoz legalább egy tartományvezérlőt kell csatlakoztatni a Log Analytics-munkaterülethez. Amíg nem csatlakoztat egy tartományvezérlőt, megjelenik egy üzenet, amely jelzi, hogy **az adatok gyűjtése még folyamatban van.**

Ha segítségre van szüksége az egyik tartományvezérlő csatlakoztatásához, megtekintheti a [Windows-számítógépek csatlakoztatása az Azure Monitorhoz](../../azure-monitor/platform/om-agents.md)című dokumentációt. Másik lehetőségként, ha a tartományvezérlő már csatlakozik egy meglévő System Center Operations Manager környezethez, megtekintheti a connect system center operations manager és [az Azure Monitor dokumentációját.](../../azure-monitor/platform/om-agents.md)

Ha egyik tartományvezérlőjét sem szeretné közvetlenül csatlakoztatni az Azure Monitorhoz vagy a System Center Operations Manager szolgáltatáshoz, olvassa el [a Nem tartományvezérlő engedélyezése](#enable-non-domain-controller)című témakört.

## <a name="next-steps"></a>További lépések
* Az [Azure Monitor naplólekérdezései](../../azure-monitor/log-query/log-query-overview.md) segítségével részletes Active Directory replikációs állapotadatok at tekintheti meg.
