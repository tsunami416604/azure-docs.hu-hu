---
title: A Windows Log Analytics ügynökével kapcsolatos problémák elhárítása
description: Írja le a tüneteket, okokat és a megoldás a leggyakoribb problémák a Log Analytics ügynök windows az Azure Monitorban.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 4112555347ce1d718375fbab3f166c6f2f5deeaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333507"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>A Windowshoz készült Log Analytics-ügynökkel kapcsolatos hibák elhárítása 

Ez a cikk segítséget nyújt az Azure Monitor Windows-alapú Log Analytics-ügynökével kapcsolatos hibák elhárításához, és további megoldásokat javasol azok megoldására.

Ha a lépések egyike sem működik, a következő támogatási csatornák is elérhetők:

* A Premier támogatási előnyökkel rendelkező ügyfelek támogatási kérelmet nyithatnak a [Premier.](https://premier.microsoft.com/)
* Az Azure-támogatási szerződésekkel rendelkező ügyfelek támogatási kérelmet nyithatnak [az Azure Portalon.](https://manage.windowsazure.com/?getsupport=true)
* Látogasson el a Log Analytics visszajelzési [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) oldalra a beküldött ötletek és hibák áttekintéséhez, vagy nyújtson be egy újat. 

## <a name="important-troubleshooting-sources"></a>Fontos hibaelhárítási források

 A Windows Log Analytics ügynökével kapcsolatos problémák elhárításához az ügynök naplózza az eseményeket a Windows eseménynaplójába, különösen az *Alkalmazás és szolgáltatások\Operations Manager mappában.*  

## <a name="connectivity-issues"></a>Csatlakozási problémák

Ha az ügynök egy proxykiszolgálón vagy tűzfalon keresztül kommunikál, előfordulhat, hogy a forrásszámítógép és az Azure Monitor szolgáltatás kommunikációját megakadályozó korlátozások lehetnek érvényben. Abban az esetben, ha a kommunikáció helytelen konfiguráció miatt blokkolva van, a munkaterülettel történő regisztráció sikertelen lehet, miközben megpróbálja telepíteni az ügynököt, vagy úgy konfigurálja az ügynököt, hogy a telepítés után egy további munkaterületnek jelentsen. Az ügynökkommunikáció sikertelen lehet a sikeres regisztráció után. Ez a szakasz a Windows-ügynökkel kapcsolatos ilyen típusú problémák elhárításának módszereit ismerteti.

Ellenőrizze, hogy a tűzfal vagy a proxy úgy van-e beállítva, hogy engedélyezze az alábbi táblázatban ismertetett portokat és URL-címeket. Azt is ellenőrizze, http-ellenőrzés nincs engedélyezve a webes forgalom, mivel megakadályozhatja a biztonságos TLS-csatorna az ügynök és az Azure Monitor között.  

|Ügynök erőforrása|Portok |Irány |HTTPS-ellenőrzés kihagyása|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |443-as port |Kimenő|Igen |  
|*.oms.opinsights.azure.com |443-as port |Kimenő|Igen |  
|*.blob.core.windows.net |443-as port |Kimenő|Igen |  

Az Azure Government számára szükséges tűzfalinformációkat az [Azure Government felügyeleti témakörben talál.](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs) Ha azt tervezi, hogy az Azure Automation hybrid Runbook Worker használatával csatlakozik az Automation szolgáltatáshoz, és regisztráljon az Automation szolgáltatással a runbookok vagy felügyeleti megoldások használatához a környezetben, akkor hozzá kell férnie a portszámhoz és a [Hálózat konfigurálása a hibrid runbook-feldolgozóhoz](../../automation/automation-hybrid-runbook-worker.md#network-planning)című részben leírt URL-címekhez. 

Számos módon ellenőrizheti, hogy az ügynök sikeresen kommunikál-e az Azure Monitorral.

- Engedélyezze az [Azure Log Analytics Ügynök állapotfelmérését](../insights/solution-agenthealth.md) a munkaterületen. Az Ügynök állapota irányítópulton tekintse meg a **nem válaszoló ügynökök száma** oszlopgyorsan látni, ha az ügynök szerepel.  

- Futtassa a következő lekérdezést annak ellenőrzéséhez, hogy az ügynök szívverést küld-e arra a munkaterületre, amelynek jelentésre van konfigurálva. Cserélje `<ComputerName>` le a gép tényleges nevére.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Ha a számítógép sikeresen kommunikál a szolgáltatással, a lekérdezésnek eredményt kell visszaadnia. Ha a lekérdezés nem adott vissza eredményt, először ellenőrizze, hogy az ügynök úgy van-e beállítva, hogy a megfelelő munkaterületnek jelentsen. Ha megfelelően van konfigurálva, folytassa a 3.

- A kapcsolódási probléma azonosításának másik módja a **TestCloudConnectivity** eszköz futtatása. Az eszköz alapértelmezés szerint a *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent*mappában lévő ügynökkel van telepítve. Egy rendszergazda jogú parancssorból keresse meg a mappát, és futtassa az eszközt. Az eszköz visszaadja az eredményeket, és kiemeli, ahol a teszt sikertelen volt (például, ha egy adott porthoz/URL-hez kapcsolódott, amelyet blokkoltak). 

    ![A TestCloudConnection eszköz végrehajtási eredményei](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Szűrje az *Operations Manager* eseménynaplóját **eseményforrások,** - *állapotszolgáltatás-modulok*, *healthservice*és *szolgáltatásösszekötő* kondiszerint, és szűrje **az eseményszint** *figyelmeztetése* és *hibája* szerint, hogy ellenőrizze, hogy az alábbi táblázatból írt-e eseményeket. Ha így van, tekintse át az egyes lehetséges eseményekhez tartozó megoldási lépéseket.

    |Eseményazonosító |Forrás |Leírás |Megoldás: |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Állapotfigyelő szolgáltatás |Nem sikerült kapcsolatot létesíteni a szolgáltatással az ügynöktől |Ez a hiba akkor fordulhat elő, ha az ügynök nem tud közvetlenül vagy tűzfalon/proxykiszolgálón keresztül kommunikálni az Azure Monitor szolgáltatással. Ellenőrizze az ügynök proxybeállításait, vagy hogy a hálózati tűzfal/proxy engedélyezi-e a TCP-forgalmat a számítógépről a szolgáltatásba.|
    |2138 |Egészségügyi szolgáltatás moduljai |A proxy hitelesítést igényel |Adja meg az ügynök proxybeállításait, és adja meg a proxykiszolgálóval való hitelesítéshez szükséges felhasználónevet/jelszót. |
    |2129 |Egészségügyi szolgáltatás moduljai |Sikertelen kapcsolat/Sikertelen TLS-egyeztetés |Ellenőrizze a hálózati adapter TCP/IP-beállításait és az ügynökproxy-beállításait.|
    |2127 |Egészségügyi szolgáltatás moduljai |Sikertelen küldési fogadott adatok hibakódja |Ha ez csak akkor történik rendszeresen a nap folyamán, lehet, hogy csak egy véletlenszerű anomália, hogy figyelmen kívül lehet hagyni. Monitor, hogy milyen gyakran történik. Ha ez gyakran előfordul a nap folyamán, először ellenőrizze a hálózati konfigurációés a proxy beállításait. Ha a leírás tartalmazza a 404-es HTTP-hibakódot, és ez az első alkalom, hogy az ügynök megpróbál adatokat küldeni a szolgáltatásnak, akkor egy 500-as hibát tartalmaz egy belső 404-es hibakóddal. A 404 azt jelenti, hogy nem található, ami azt jelzi, hogy az új munkaterület tárolási területe még ki van építve. A következő újrapróbálkozáskor az adatok a várt módon sikeresen bekerülnek a munkaterületre. A 403-as HTTP-hiba engedély- vagy hitelesítő adatokkal kapcsolatos problémát jelezhet. A 403-as hiba további információkat tartalmaz a probléma elhárításához.|
    |4000 |Szolgáltatásösszekötő |A DNS-névfeloldás nem sikerült |A számítógép nem tudta feloldani a szolgáltatásnak küldött adatok internetcímét. Ez lehet a DNS-feloldó beállításai a számítógépen, helytelen proxybeállítások, vagy esetleg ideiglenes DNS-probléma a szolgáltatónál. Ha ez rendszeres időközönként megtörténik, azt átmeneti, hálózattal kapcsolatos probléma okozhatja.|
    |4001 |Szolgáltatásösszekötő |A szolgáltatással való kapcsolat nem sikerült. |Ez a hiba akkor fordulhat elő, ha az ügynök nem tud közvetlenül vagy tűzfalon/proxykiszolgálón keresztül kommunikálni az Azure Monitor szolgáltatással. Ellenőrizze az ügynök proxybeállításait, vagy hogy a hálózati tűzfal/proxy engedélyezi-e a TCP-forgalmat a számítógépről a szolgáltatásba.|
    |4002 |Szolgáltatásösszekötő |A szolgáltatás egy lekérdezésre adott válaszként 403-as HTTP-állapotkódot adott vissza. A szolgáltatás állapotáról érdeklődjön a szolgáltatás rendszergazdájától. A lekérdezés később újra meglesz próbálva. |Ez a hiba az ügynök kezdeti regisztrációs fázisában íródott, és a következőhöz hasonló URL jelenik meg: *https://\<munkaterület-azonosító>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest*. A 403-as hibakód tiltott, és hibás munkaterület-azonosító vagy kulcs okozhatja, vagy az adatok és az idő helytelen a számítógépen. Ha az idő +/- 15 percre van az aktuális időtől, akkor a bevezetés sikertelen lesz. A hiba kijavításához frissítse a Windows-számítógép dátumát és/vagy időzónáját.|

## <a name="data-collection-issues"></a>Adatgyűjtési problémák

Miután az ügynök telepítve van, és jelentést tesz a konfigurált munkaterületnek vagy munkaterületnek, leállíthatja a konfiguráció fogadását, a teljesítmény, a naplók vagy más adatok gyűjtését vagy továbbítását a szolgáltatásnak attól függően, hogy mi van engedélyezve és mi a számítógép célzása. Meg kell határozni, hogy:

- Ez egy adott adattípus, vagy az összes olyan adat, amely nem érhető el a munkaterületen?
- Az adattípust egy megoldás adja meg, vagy a munkaterületi adatgyűjtési konfiguráció részeként adja meg?
- Hány számítógép érintett? Egy vagy több számítógép jelent a munkaterületre?
- Működött, és megállt a nap egy adott szakában, vagy soha nem gyűjtötték be? 
- A szintaktikailag használt naplókeresési lekérdezés helyes? 
- Megkapta-e az ügynök a konfigurációját az Azure Monitortól?

A hibaelhárítás első lépése annak megállapítása, hogy a számítógép küld-e szívverési eseményt.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Ha a lekérdezés eredményeket ad vissza, akkor meg kell határoznia, hogy egy adott adattípus nem gyűjti-e és továbbítja-e a szolgáltatásnak. Ennek oka az lehet, hogy az ügynök nem kap frissített konfigurációt a szolgáltatástól, vagy más tünet, amely megakadályozza az ügynök normális működését. A további hibaelhárításhoz hajtsa végre az alábbi lépéseket.

1. Nyisson meg egy rendszergazda jogú parancssort a `net stop healthservice && net start healthservice`számítógépen, és indítsa újra az ügynökszolgáltatást a beírásával.
2. Nyissa meg az *Operations Manager* eseménynaplóját, és keressen *7023, 7024, 7025, 7028* és *1210* **eseményazonosítókat** **az Event source** *HealthService*szolgáltatásból.  Ezek az események azt jelzik, hogy az ügynök sikeresen megkapta a konfigurációt az Azure Monitortól, és aktívan figyelik a számítógépet. Az esemény leírása esemény azonosító 1210 is meghatározza az utolsó sorban az összes megoldást és elemzési, amelyek szerepelnek az ügynök figyelési hatókörében.  

    ![1210-es eseményazonosító leírása](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Ha néhány perc múlva nem látja a várt adatokat a lekérdezés eredményében vagy vizualizációjában, attól függően, hogy egy megoldásból vagy az Insightból tekinti-e meg az adatokat, az *Operations Manager* eseménynaplójában keresse meg az **Állapotszolgáltatás** *HealthService* és *az állapotszolgáltatás moduljait,* és szűrjön **eseményszint-figyelmeztetés** *Warning* és *hiba* szerint annak megerősítéséhez, hogy az a következő táblázatból írt-e eseményeket.

    |Eseményazonosító |Forrás |Leírás |Megoldás: |
    |---------|-------|------------|
    |8000 |HealthService (Egészségügyi Szolgáltatás) |Ez az esemény megadja, hogy a teljesítményhez, eseményhez vagy más adattípushoz kapcsolódó munkafolyamat nem tudja-e továbbítani a szolgáltatásnak a munkaterületre való betöltéshez. | A 2136-os esemény a forrásból a HealthService azonosítója ezzel az eseménnyel együtt íródott, és jelezheti, hogy az ügynök nem tud kommunikálni a szolgáltatással, valószínűleg a proxy- és hitelesítési beállítások helytelen konfigurálása, a hálózati kimaradás miatt, vagy a hálózati tűzfal/proxy nem engedélyezi a TCP-forgalmat a számítógépről a szolgáltatásba.| 
    |10102 és 10103 |Egészségügyi szolgáltatás moduljai |A munkafolyamat nem tudta feloldani az adatforrást. |Ez akkor fordulhat elő, ha a megadott teljesítményszámláló vagy -példány nem létezik a számítógépen, vagy helytelenül van definiálva a munkaterületi adatbeállításokban. Ha ez egy felhasználó által megadott [teljesítményszámláló,](data-sources-performance-counters.md#configuring-performance-counters)ellenőrizze, hogy a megadott adatok a megfelelő formátumot követik-e, és léteznek-e a célszámítógépeken. |
    |26002 |Egészségügyi szolgáltatás moduljai |A munkafolyamat nem tudta feloldani az adatforrást. |Ez akkor fordulhat elő, ha a megadott Windows eseménynapló nem létezik a számítógépen. Ez a hiba biztonságosan figyelmen kívül hagyható, ha a számítógépnek nem várható, hogy ez az eseménynapló regisztrálva legyen, ellenkező esetben, ha ez egy felhasználó által megadott [eseménynapló,](data-sources-windows-events.md#configuring-windows-event-logs)ellenőrizze, hogy a megadott adatok helyesek-e. |

