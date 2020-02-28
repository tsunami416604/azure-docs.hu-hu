---
title: DNS Analytics megoldás a Azure Monitorban | Microsoft Docs
description: A Azure Monitor DNS Analytics megoldásának beállítása és használata a DNS-infrastruktúra biztonságával, teljesítményével és műveleteivel kapcsolatos információk gyűjtésére.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2018
ms.openlocfilehash: 2471c29f559df5c347c62ceb4c7fd9b4ae1e5eec
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657333"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Gyűjtsön információt a DNS-infrastruktúra, a DNS Analytics előzetes verziójának megoldással kapcsolatos

![DNS Analytics szimbólum](./media/dns-analytics/dns-analytics-symbol.png)

Ez a cikk azt ismerteti, hogyan állíthatja be és használhatja a Azure Monitor Azure DNS elemzési megoldását, hogy betekintést gyűjtsön a DNS-infrastruktúrába a biztonság, a teljesítmény és a műveletek terén.

DNS-elemzés révén:

- Azok az ügyfelek, kártékony tartománynevek azonosítása.
- Elavult erőforrásrekordok azonosítása.
- Gyakran lekérdezett tartománynevek és beszédes DNS-ügyfelek azonosítása.
- Nézet kérelmek által generált terhelést a DNS-kiszolgálókon.
- Dinamikus DNS-regisztrációs hibák megtekintése.

A megoldás gyűjti, elemzi és korrelációt keres a Windows DNS elemzési és auditnaplók és egyéb kapcsolódó adatait a DNS-kiszolgálók között.

## <a name="connected-sources"></a>Összekapcsolt források

A következő táblázat ismerteti a megoldás által támogatott csatlakoztatott forrásokat:

| **Csatlakoztatott forrás** | **Támogatás** | **Leírás** |
| --- | --- | --- |
| [Windows-ügynökök](../platform/agent-windows.md) | Igen | A megoldás a DNS-adatok Windows-ügynököktől gyűjti. |
| [Linux-ügynökök](../learn/quick-collect-linux-computer.md) | Nem | A megoldás nem DNS-információkat gyűjtsön a közvetlen Linux-ügynökök. |
| [System Center Operations Manager felügyeleti csoport](../platform/om-agents.md) | Igen | A megoldás a DNS-adatok egy csatlakoztatott az Operations Manager felügyeleti csoportban lévő ügynököktől gyűjti. Nem szükséges közvetlen kapcsolódás a Operations Manager ügynöktől a Azure Monitorhoz. Adatok lesznek továbbítva a felügyeleti csoportból a Log Analytics-munkaterületet. |
| [Azure Storage-fiók](../platform/collect-azure-metrics-logs.md) | Nem | A megoldás az Azure storage nem használja. |

### <a name="data-collection-details"></a>Adatok gyűjtése részletei

A megoldás gyűjti DNS-leltár- és DNS-esemény kapcsolatos adatokat a DNS-kiszolgálók ahol egy Log Analytics-ügynök telepítve van. Ezt követően a rendszer feltölti ezeket az adatAzure Monitor, és megjeleníti a megoldás irányítópultján. A DNS PowerShell-parancsmagok futtatása leltárhoz kapcsolódó adatok, például a DNS-kiszolgálók, zónák és rekordok, számát gyűjti. Az adatok két naponta egyszer frissül. Az eseményekkel kapcsolatos adatokat a rendszer valós időben gyűjti a továbbfejlesztett DNS-naplózás és diagnosztika által biztosított [analitikai és naplózási naplókból](https://technet.microsoft.com/library/dn800669.aspx#enhanc) a Windows Server 2012 R2 rendszerben.

## <a name="configuration"></a>Konfiguráció

A megoldás konfigurálásához kövesse az alábbi adatokat:

- A figyelni kívánt összes DNS-kiszolgálón [Windows](../platform/agent-windows.md) vagy [Operations Manager](../platform/om-agents.md) ügynöknek kell lennie.
- Az [Azure Marketplace](https://aka.ms/dnsanalyticsazuremarketplace)-en felveheti a DNS Analytics megoldást az log Analytics-munkaterületre. Használhatja a [Solutions Gallery Azure monitor-megoldások hozzáadása](solutions.md)című részében ismertetett eljárást is.

A megoldás elindult, nincs szükség további konfigurációs adatok gyűjtése. A következő konfiguráció használatával azonban adatgyűjtés testreszabása.

### <a name="configure-the-solution"></a>A megoldás konfigurálása

A megoldás Irányítópultján kattintson a **konfiguráció** elemre a DNS Analytics konfiguráció lap megnyitásához. Konfigurációs módosításokat végezhet két típusa van:

- **Engedélyezési tartomány neve**. A megoldás nem dolgozza fel azokat a keresési lekérdezéseket. A tartománynév-utótagokat tartalmazó engedélyezési listát tárol. A keresési lekérdezéseket, hogy a megfelelő tartománynév-utótagokat az engedélyezési listán szereplő tartománynevek nem dolgozza fel a megoldás. Az engedélyezett tartománynevek feldolgozásának mellőzése segít optimalizálni a Azure Monitor elküldhető adatátvitelt. Az alapértelmezett engedélyezési lista népszerű tartományneveket, mint például www.google.com, www.facebook.com és magában foglalja. Az alapértelmezett teljes lista megtekintéséhez görgetés.

  Módosíthatja bármely tartománynév utótagja, hogy meg szeretné tekinteni a keresési insights hozzáadása a listában. Valamint eltávolíthatja bármely tartománynév utótagja nem szeretnénk a keresési elemzések megtekintése.

- **Beszédes ügyfél küszöbértéke**. Azok a DNS-ügyfelek, amelyek túllépik a keresési kérelmek számának küszöbértékét, a **DNS-ügyfelek** panelen vannak kiemelve. Az alapértelmezett küszöbérték 1000. Szerkesztheti a küszöbértéket.

    ![Engedélyezett tartománynevek](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Felügyeleti csomagok

Ha a Microsoft Monitoring Agent a Log Analytics-munkaterülethez való kapcsolódáshoz használ, a következő felügyeleti csomag telepítve van:

- Microsoft DNS-adatgyűjtő Intelligence Pack (Microsoft. IntelligencePacks. DNS)

Ha az Operations Manager felügyeleti csoportban a Log Analytics-munkaterülethez van csatlakoztatva, a következő felügyeleti csomagokat az Operations Manager települnek, ha ez a megoldás hozzáadásakor. Ne legyen kötelező konfigurációs vagy karbantartási a felügyeleti csomagok:

- Microsoft DNS-adatgyűjtő Intelligence Pack (Microsoft. IntelligencePacks. DNS)
- Microsoft System Center Advisor DNS Analytics Configuration (Microsoft.IntelligencePack.Dns.Configuration)

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](../platform/om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>A DNS Analytics megoldás használata

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


A DNS-csempe tartalmazza azon DNS-kiszolgálók számát, amelyeken az adatok gyűjtése történik. Rosszindulatú tartományok feloldani az elmúlt 24 órában az ügyfelek által végrehajtott kérelmek száma is tartalmaz. Ha a csempére kattint, megnyílik a megoldás irányítópultján.

![DNS Analytics tile](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>A megoldás irányítópultja

A megoldás irányítópultján különböző funkcióhoz a megoldás összefoglaló információit jeleníti meg. A részletes nézet a törvényszéki elemzés és diagnosztika mutató hivatkozásokat is tartalmaz. Alapértelmezés szerint az elmúlt hét napban megjelennek az adatok. A dátum-és időtartomány a **dátum-idő kijelölési vezérlőelem**használatával módosítható, ahogy az alábbi képen is látható:

![Kijelölt vezérlő](./media/dns-analytics/dns-time.png)

A megoldás irányítópultján a következő paneleken látható:

**DNS-biztonság**. A jelentés a DNS-ügyfelek, amelyek próbál meg kommunikálni a rosszindulatú tartományokat. Használja a Microsoft fenyegetésészlelési intelligenciával foglalkozó hírcsatornákra, DNS-elemzés észlelni tudja az ügyfél IP-címek, amelyhez hozzá szeretne férni a rosszindulatú tartományokat. Sok esetben kártevők által fertőzött eszközök "tárcsázásával" a "parancs és vezérlés" center kártékony tartomány által a kártevő szoftver-tartománynév feloldására.

![DNS-biztonsági panel](./media/dns-analytics/dns-security-blade.png)

Ha rákattint egy ügyfél IP-címet a listában, a naplóbeli keresés megnyitja, és a megfelelő lekérdezés keresési részleteit jeleníti meg. A következő példában a DNS Analytics észlelte, hogy a kommunikáció egy [IRCBot](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot&threatId=2621)történt:

![A naplókeresések ircbot megjelenítése](./media/dns-analytics/ircbot.png)

Az információ segít azonosítani a:

- Ügyfél által kezdeményezett kommunikáció IP-Címmel.
- Tartomány neve, amelyet a rosszindulatú IP-cím.
- A tartománynév feloldása egy olyan IP-címeket.
- Rosszindulatú IP-cím.
- A probléma súlyosságát.
- A kártékony IP-cím áruházat okát.
- Észlelés ideje.

**Lekérdezett tartományok**. A leggyakoribb, a környezetben a DNS-ügyfelek által éppen lekérdezett tartománynevek biztosít. Megtekintheti a lekérdezett tartománynevek listája. Emellett részletezhet keresési kérelem adatait, a naplóbeli keresés egy adott tartománynév.

![Tartományok lekérdezett panel](./media/dns-analytics/domains-queried-blade.png)

**DNS-ügyfelek**. Azt jelenti, hogy az ügyfelek *megszegik a* kiválasztott időszakban a lekérdezések számának küszöbértékét. Megtekintheti a DNS-ügyfelek és a részletek a Naplókeresésben őket által végrehajtott lekérdezések listáját.

![A DNS-ügyfelek panel](./media/dns-analytics/dns-clients-blade.png)

**Dinamikus DNS-regisztrációk**. Jelentések adjon nevet a regisztrációs hibák. A cím- [erőforrásrekordok](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (a és AAAA típus) összes regisztrációs hibája ki van emelve a regisztrációs kérelmeket használó ügyfél IP-címeivel együtt. Ezután használhatja ezeket az információkat az alapvető ok a regisztrációs hiba található a következő lépésekkel:

1. Keresse meg a zóna mérvadó a neve, amely az ügyfél megpróbálja frissíteni.

1. A megoldás segítségével ellenőrizheti, hogy a zóna a leltáradatokat.

1. Győződjön meg arról, hogy engedélyezve van-e a zóna a dinamikus frissítés.

1. Ellenőrizze, hogy e vagy sem a zóna van konfigurálva a biztonságos a dinamikus frissítés.

    ![Dinamikus DNS-regisztráció panel](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Név-regisztrációs kérelmek**. A felső csempe sikeres és sikertelen DNS dinamikus frissítési kérelmek trendvonal jeleníti meg. Az alacsonyabb csempe felsorolja a felső 10 ügyfél küldő sikertelen DNS-frissítési kérelmek a DNS-kiszolgálók, a hibák száma szerint rendezve.

![Név regisztrációs kérelmek panel](./media/dns-analytics/name-reg-req-blade.png)

**Példa DDI Analytics-lekérdezésekre**. A leggyakoribb keresési lekérdezések raw elemzési adatok közvetlenül listáját tartalmazza.


![Mintalekérdezések](./media/dns-analytics/queries.png)

Ezek a lekérdezések kiindulási pontként használható a saját testre szabott jelentéskészítési lekérdezések létrehozásáról. A lekérdezéseket a DNS Analytics naplóbeli keresés-oldalának hivatkozása, ahol eredmények jelennek meg:

- **A DNS-kiszolgálók listája**. Minden DNS-kiszolgálók, és azok kapcsolódó FQDN, tartománynév, erdő neve és a kiszolgáló IP-címek listája látható.
- **DNS-zónák listája**. A társított zóna neve, a dinamikus frissítési állapot, a Névkiszolgálók és a DNSSEC-aláírási állapot az összes DNS-zónák listája látható.
- Nem **használt erőforrásrekordok**. A fel nem használt és elavult erőforrás-rekordok listáját jeleníti meg. Ez a lista tartalmazza az erőforrás-rekord neve, erőforrásrekord típusa, a társított DNS-kiszolgáló, rekord létrehozási idő és zóna nevét. Ez a lista segítségével azonosíthatja a DNS-erőforrásrekordok már használatban van. Ezen információ alapján, majd eltávolíthatja ezeket a bejegyzéseket a DNS-kiszolgálókról.
- **DNS-kiszolgálók lekérdezési terhelése**. Információkat jelenít meg, hogy kap egy-a DNS-terhelés szempontjából a DNS-kiszolgálókon. Ezek az információk alapján megtervezheti a kapacitást, a kiszolgálók. A **metrikák** lapon megnyithatja a nézetet grafikus megjelenítésre. Ez a nézet segítségével megismerheti, hogyan oszlik meg a DNS-betöltés a DNS-kiszolgálók között. DNS-lekérdezés arány trendek minden olyan kiszolgáló látható.

    ![DNS-kiszolgálók lekérdezések napló keresési eredményei](./media/dns-analytics/dns-servers-query-load.png)

- **DNS-zónák lekérdezés betöltése**. Minden zóna DNS zóna lekérdezési – másodpercenkénti statisztikáit mutatja a megoldás által felügyelt DNS-kiszolgálókon. Kattintson a **mérőszámok** lapra, hogy a részletes rekordok nézetét az eredmények grafikus megjelenítésére módosítsa.
- **Konfigurációs események**. A DNS a konfigurációmódosítási események és a kapcsolódó üzeneteket jeleníti meg. Ezután ezek az események az esemény, eseményazonosító, DNS-kiszolgáló ideje alapján szűrheti vagy Feladatkategória. Az adatok segítséget nyújtanak az adott DNS-kiszolgálók egy adott időpontban végzett módosítások naplózása.
- **DNS analitikai napló**. A megoldás által felügyelt DNS-kiszolgálók az elemzési eseményeket jeleníti meg. Ezek az események az esemény, eseményazonosító, DNS-kiszolgáló ideje alapján szűrheti majd a keresési lekérdezés és a lekérdezés típusa és a feladat kategóriája ügyfél IP-Címét. DNS-kiszolgáló elemzési eseményeket engedélyezze a műveletek nyomon követése a DNS-kiszolgálón. Egy elemző naplóz minden alkalommal, amikor a kiszolgáló DNS-információkat fogad vagy küld.

### <a name="search-by-using-dns-analytics-log-search"></a>DNS Analytics naplóbeli keresés használatával keresése

A naplók keresése oldalát létrehozhat egy lekérdezést. A keresési eredményeket szűrheti értékkorlátozás vezérlők használatával. Átalakítás, szűrheti és jelentés speciális lekérdezéseket is létrehozhat az eredményeket. Indítsa el az alábbi lekérdezéseket:

1. A **keresési lekérdezés mezőbe**írja be a `DnsEvents` kifejezést a megoldás által kezelt DNS-kiszolgálók által generált összes DNS-esemény megtekintéséhez. Az eredményeket a keresési lekérdezéseket, a dinamikus regisztrációk és a konfigurációs módosítások kapcsolódó összes eseményt adatainak naplózása listája.

    ![DnsEvents naplók keresése](./media/dns-analytics/log-search-dnsevents.png)  

    a. A keresési lekérdezések naplózási adatainak megtekintéséhez válassza a **LookUpQuery** lehetőséget a bal oldali dimenzió vezérlőelemben található **altípus** -szűrőként. A keresési lekérdezés események a kiválasztott időszakra vonatkozó tartalmazó táblázat jelenik meg.

    b. A dinamikus regisztrációk naplózási adatainak megtekintéséhez válassza a **DynamicRegistration** lehetőséget a bal oldali dimenzió vezérlőelemben található **altípus** -szűrőként. Egy táblázat listázza az összes dinamikus regisztrációs események a kiválasztott időszakra vonatkozó jelenik meg.

    c. A konfigurációs módosítások naplózási adatainak megtekintéséhez válassza a **konfigurációváltozás** lehetőséget a bal oldali dimenzió vezérlőelemben található **altípus** -szűrőként. Egy táblát, amely a kijelölt időszakra vonatkozó összes a konfigurációmódosítási események listája jelenik meg.

1. A **keresési lekérdezés mezőbe**írja be a `DnsInventory` kifejezést a megoldás által kezelt DNS-kiszolgálókon található összes DNS-leltárhoz kapcsolódó adat megtekintéséhez. Az eredményeket a naplóadatok a DNS-kiszolgálók, DNS-zónák és rekordok listája.

    ![DnsInventory naplók keresése](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Hibakeresés

Gyakori hibaelhárítási lépések:

1. Hiányzó DNS-keresési információ – a probléma megoldásához próbálja meg alaphelyzetbe állítani a konfigurációt, vagy csak egyszer töltse be a konfigurációs oldalt a portálon. Az alaphelyzetbe állításhoz egyszerűen módosítsa a beállítást egy másik értékre, majd állítsa vissza az eredeti értékre, és mentse a konfigurációt.

## <a name="feedback"></a>Visszajelzés

A visszajelzések megadásához látogasson el a [log Analytics UserVoice oldalára](https://aka.ms/dnsanalyticsuservoice) , és tegye fel ötleteit DNS Analytics funkcióinak működéséhez. 

## <a name="next-steps"></a>Következő lépések

[Lekérdezési naplók](../log-query/log-query-overview.md) a részletes DNS-naplóbejegyzések megtekintéséhez.
