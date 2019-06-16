---
title: A Log Analytics-ügynököt Windows kapcsolatos problémák elhárítása |} A Microsoft Docs
description: A tünetek okok és a leggyakoribb problémák megoldási ismertetik az a Log Analytics az Azure monitorban Windows-ügynököt.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2019
ms.author: magoedte
ms.openlocfilehash: afa4483677336e9a887908a8cccf9590eed27af3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67120109"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>A Log Analytics-ügynököt Windows kapcsolatos problémák elhárítása 

Ez a cikk ismerteti a hibaelhárítást hibákat tapasztalhat az a Log Analytics az Azure monitorban Windows-ügynököt, és a problémák megoldásához lehetséges megoldásokat javasol.

Ha a fentiek egyikével sem működik, a következő támogatási csatornákat is elérhetők:

* Ügyfeleink a Premier szintű támogatási kedvezményekre megnyithat egy támogatási kérést az [Premier](https://premier.microsoft.com/).
* Az Azure támogatási szerződés rendelkező ügyfelek is nyisson egy támogatási kérést [az Azure Portalon](https://manage.windowsazure.com/?getsupport=true).
* A Log Analytics visszajelzési oldalán tekintse át a beküldött ötletekre és hibák [ https://aka.ms/opinsightsfeedback ](https://aka.ms/opinsightsfeedback) vagy egy új fájlt. 

## <a name="important-troubleshooting-sources"></a>Fontos hibaelhárítási források

 A Log Analytics Windows ügyféllel kapcsolatos problémák elhárítása nyújt, az ügynök eseményt egy ügyfélnaplóban a Windows eseménynaplóba, kifejezetten a *alkalmazás- és Services\Operations Manager*.  

## <a name="connectivity-issues"></a>Csatlakozási problémák

Ha az ügynök kommunikál-e egy proxykiszolgálón vagy tűzfalon keresztül, korlátozások megakadályozza a kommunikációt a forrás- és az Azure Monitor szolgáltatásban a helyen lehet. Ha kommunikációja blokkolva van, hibás, a munkaterület regisztrációs sikertelenek lehetnek próbál meg telepíteni az ügynököt, az ügynök utáni telepítése egy további munkaterületnek való konfigurálásához, vagy az ügynök kommunikációja sikertelen lesz, a sikeres regisztráció után. Ez a szakasz bemutatja a problémát a Windows-ügynök az ilyen típusú hibaelhárítása. 

Dupla ellenőrizze, hogy a tűzfal vagy proxy van konfigurálva, hogy a következő portokat és URL-címeket a következő táblázat ismerteti. Is ellenőrizheti a webes forgalmat, nincs engedélyezve a HTTP-ellenőrzési, megakadályozhatja, hogy az ügynök és az Azure Monitor között TLS biztonságos csatornát.  

|Ügynök erőforrása|Portok |Irány |HTTPS-ellenőrzés kihagyása|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |443-as port |Kimenő|Igen |  
|*.oms.opinsights.azure.com |443-as port |Kimenő|Igen |  
|*.blob.core.windows.net |443-as port |Kimenő|Igen |  
|*.azure-automation.net |443-as port |Kimenő|Igen |  

Az Azure Government szükséges tűzfal információkért lásd: [Azure Government felügyeleti](../../azure-government/documentation-government-services-monitoringandmanagement.md#azure-monitor-logs). 

Többféleképpen is ellenőrizheti, ha az ügynök sikeresen kommunikál az Azure Monitor.

- Engedélyezze a [Azure Log Analytics ügynökállapot értékelés](../insights/solution-agenthealth.md) a munkaterületen. Az Agent Health irányítópultján megtekintheti a **nem válaszoló ügynökök száma** oszlop gyorsan megtekintheti, hogy az ügynök szerepel-e.  

- Futtassa a következő lekérdezést, ellenőrizze az ügynök szívverést küld a munkaterülethez való jelentésre van konfigurálva. Cserélje le <ComputerName> a gép tényleges nevét.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Ha a számítógép sikeresen kommunikál a szolgáltatást, a lekérdezés eredményt adja vissza. Ha a lekérdezés nem adott vissza eredményt, először ellenőrizze az ügynök úgy van beállítva, a jelentés a megfelelő munkaterületre. Ha megfelelően van konfigurálva, folytassa a 3. lépés, és keresse meg a Windows Eseménynapló azonosítását, ha az ügynök naplózásának milyen probléma megakadályozhatják, az Azure Monitor szolgáltatással folytatott kommunikációját.

- A futó van egy másik módszer a kapcsolódási probléma azonosítása a **TestCloudConnectivity** eszközt. Az eszköz telepítve van az a mappa az ügynök alapértelmezés szerint *%SystemRoot%\Program Files\Microsoft Monitoring Agent\Agent*. Egy rendszergazda jogú parancssorból lépjen abba a mappába, és futtassa az eszközt. Az eszköz az eredmények és emeli ki, ha a teszt sikertelen volt (például ha egy adott port/URL-címet, amely le lett tiltva volt kapcsolatos) adja vissza. 

    ![TestCloudConnection eszköz végrehajtás eredménye](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Szűrés a *az Operations Manager* Eseménynapló szerint **eseményforrások** - *Állapotfigyelő szolgáltatás modulok*, *HealthService*, és *Service Connector* és szűrés **Eseményszint** *figyelmeztetés* és *hiba* , győződjön meg arról, ha az események írás a a táblázatban. Ha igen, tekintse át a megoldási lépéseket minden lehetséges esemény esetén.

    |Eseményazonosító |source |Leírás |Megoldás: |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Állapotfigyelő szolgáltatás |Nem sikerült csatlakozni a a szolgáltatás az ügynök |Ez a hiba akkor fordulhat elő, amikor az ügynök nem tud kommunikálni, vagy közvetlenül az Azure Monitor szolgáltatásban egy tűzfal vagy proxykiszolgálón keresztül. Ellenőrizze a proxybeállítások ügynök vagy az, hogy a hálózati tűzfal vagy proxy lehetővé teszi, hogy a szolgáltatás a számítógépen TCP-forgalom.|
    |2138 |Állapotfigyelő szolgáltatás modulok |Proxy hitelesítést igényel |Az ügynök Proxybeállítások konfigurálása, és adja meg a felhasználónév/jelszó és a proxykiszolgáló hitelesítéséhez szükséges. |
    |2129 |Állapotfigyelő szolgáltatás modulok |Sikertelen kapcsolat/nem sikerült SSL-egyeztetést |Ellenőrizze a hálózati adapter TCP/IP-beállítások és az ügynök proxybeállításait.|
    |2127 |Állapotfigyelő szolgáltatás modulok |Hiba az adatok küldésének kapott hibakód: |Ha azt csak a nap folyamán rendszeres időközönként történik, ugyanúgy lehet egy véletlenszerű anomáliadetektálási figyelmen kívül hagyható. Ez a figyelő tudni, hogy milyen gyakran történik. Ha ez történik, gyakran a nap folyamán, először ellenőrizze a hálózati konfigurációt és a proxybeállításokat. Ha a leírásban HTTP-hibakóddal 404-es és az ügynök megpróbálja adatokat küldeni a szolgáltatásnak először, ez magában foglalja egy 500-as hiba egy belső 404-es hibakód. 404-es azt jelenti, hogy nem található, ami azt jelenti, hogy a tároló terület, az új munkaterület folyamatban van. A következő újrapróbálkozási adatok sikeresen ír a munkaterülethez elvárt módon. Egy HTTP 403-as hiba egy engedély, vagy a hitelesítő adatait a probléma esetleg jelzik. További információ a 403-as hibát a probléma elhárításához is van.|
    |4000 |Szolgáltatás-összekötő |DNS-névfeloldás nem sikerült |A gép nem sikerült feloldani az internetcím, amikor adatokat küld a szolgáltatásnak. Ez lehet a DNS-feloldási beállítások a gépén, helytelenek a proxykiszolgáló beállításai vagy talán egy ideiglenes DNS-probléma a szolgáltatónál. Rendszeres időközönként történik, ha ezt okozhatja egy átmeneti hálózati probléma.|
    |4001 |Szolgáltatás-összekötő |Nem sikerült csatlakozni a szolgáltatáshoz. |Ez a hiba akkor fordulhat elő, amikor az ügynök nem tud kommunikálni, vagy közvetlenül az Azure Monitor szolgáltatásban egy tűzfal vagy proxykiszolgálón keresztül. Ellenőrizze a proxybeállítások ügynök vagy az, hogy a hálózati tűzfal vagy proxy lehetővé teszi, hogy a szolgáltatás a számítógépen TCP-forgalom.|
    |4002 |Szolgáltatás-összekötő |A szolgáltatás egy lekérdezést a válaszban visszaadott HTTP-állapotkód: 403-as. Ellenőrizze a szolgáltatás állapotát a szolgáltatás adminisztrátorától. A lekérdezés rendszer megpróbálja újból később. |Ez a hiba az ügynököt a kezdeti regisztrációs fázisban van megírva, és láthatja, hogy egy URL-címe a következőhöz hasonló: *https://<workspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest*. Hiba a 403-as azt jelenti, hogy tiltott kódot, és egy helytelenül beírt munkaterület-Azonosítót vagy kulcsot okozhatja, vagy a dátumát és időpontját helytelen azon a számítógépen. Ha az idő +/-aktuális időtől számítva 15 percig, majd előkészítés sikertelen lesz. A probléma megoldása frissítse a dátumot és/vagy a Windows-számítógép időzónáját.|

## <a name="data-collection-issues"></a>Adatgyűjtési hibákat

Miután az ügynök telepítve van, és jelentéseket a konfigurált munkaterületével vagy a munkaterületek, azt leállíthatja konfigurációs, gyűjtése vagy továbbító teljesítmény, naplók vagy más függően engedélyezve van a szolgáltatás adatokat fogad, és a számítógép-e állítva. Azt határozza meg, hogy szükség:

- Ennyi az egész adattípust vagy nem érhető el a munkaterület összes adatot?
- Az adattípus-megoldások által megadott vagy a munkaterület adatgyűjtés konfigurációja részeként megadott?
- Hány számítógép érintett? Egy vagy több olyan számítógép, a-munkaterületre jelentő is ez?
- Volt működik és volt, állítsa le a nap egy adott időpontban, vagy ez soha nem volt összegyűjtött? 
- A naplóbeli keresési lekérdezés, használja a szintaktikailag helyes? 
- Az ügynök minden eddiginél kapott a konfiguráció az Azure Monitor?

Hibaelhárítás első lépéseként az határozza meg, ha a számítógép küld egy szívverést esemény.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Ha a lekérdezés visszaadja az eredményeket, majd szüksége meghatározhatja egy adott adattípus nem gyűjtött és a szolgáltatásnak továbbított. Ezt okozhatja az ügynök frissített konfigurációt nem kap a szolgáltatás, vagy valamilyen egyéb tünet meggátolja, hogy az ügynök megfelelően működik. A következő lépésekkel további hibaelhárításhoz.

1. Nyisson meg egy rendszergazda jogú parancssort azon a számítógépen, és indítsa újra a házirendügynök szolgáltatást beírásával `net stop healthservice && net start healthservice`.
2. Nyissa meg a *az Operations Manager* eseménynaplót, és keresse meg a **eseményazonosítók** *7023, 7024, 7025, 7028* és *1210* a **esemény forrás** *HealthService*.  Ezek az események jelzik, hogy az ügynök konfigurációt sikerült kap az Azure Monitor és azok aktívan figyeli a számítógépet. Esemény azonosítója 1210 is meg kell adnia az utolsó esemény leírása. az összes, amelyek szerepelnek az ügynök figyelési és megoldások sor.  

    ![Az esemény azonosítója 1210 leírása](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Ha néhány perc múlva nem látja a várt adatok, a lekérdezési eredmények vagy a Vizualizáció, attól függően, hogy tekinti meg a megoldás vagy elemzést az adatok a a *az Operations Manager* eseménynaplót, és keressen **esemény források** *HealthService* és *Állapotfigyelő szolgáltatás modulok* és szűrés **Eseményszint** *figyelmeztetés* és *Hiba* , győződjön meg arról, ha a következő táblázatból események írás.

    |Eseményazonosító |source |Leírás |Megoldás: |
    |---------|-------|------------|
    |8000 |HealthService |Ez az esemény fogja megadni, ha egy munkafolyamatot a teljesítménnyel kapcsolatos, esemény vagy más típusú gyűjtött adatokat nem tudta továbbítani a szolgáltatásnak a munkaterülethez támogatunk. | Eseményazonosító 2136 forrás Állapotfigyelő szolgáltatás együtt ez az esemény írása, és adhatja meg, az ügynök nem tud kommunikálni a szolgáltatás a proxy- és hitelesítési beállításait, hálózati kimaradások vagy a hálózati tűzfal helytelen konfigurációja miatt esetleg / proxy nem engedélyezi a TCP-forgalom a szolgáltatást a számítógépről.| 
    |10102 és 10103 |Állapotfigyelő szolgáltatás modulok |A munkafolyamat nem sikerült feloldani az adatforrást. |Ez akkor fordulhat elő, ha a megadott teljesítményszámláló vagy a példány nem létezik a számítógépen, vagy helytelenül van megadva a munkaterület-beállítások. Ha ez a felhasználó által megadott [teljesítményszámláló](data-sources-performance-counters.md#configuring-performance-counters), ellenőrizze a megadott adatokat a helyes formátum a következő, és létezik-e a célszámítógépeken. |
    |26002 |Állapotfigyelő szolgáltatás modulok |A munkafolyamat nem sikerült feloldani az adatforrást. |Ez akkor fordulhat elő, ha a megadott Windows-Eseménynapló nem létezik a számítógépen. Ez a hiba nyugodtan figyelmen kívül hagyható, ha a számítógép nem kellene rendelkeznie, ehhez az eseménynaplóhoz, ellenkező esetben, ha ez a felhasználó által megadott regisztrált [Eseménynapló](data-sources-windows-events.md#configuring-windows-event-logs), ellenőrizze a megadott adatok helyesek. |

