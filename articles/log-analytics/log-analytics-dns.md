---
title: DNS Analytics megoldás az Azure Log Analyticsben |} A Microsoft Docs
description: Beállítása, és a Log Analytics a DNS Analytics megoldás segítségével gyűjtse össze a DNS-infrastruktúra a biztonság, teljesítmény és műveleteket.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f44a40c4-820a-406e-8c40-70bd8dc67ae7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 56ad633807a59eaa7ae75dc334aa630fb3bf94a3
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39436726"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Gyűjtsön információt a DNS-infrastruktúra, a DNS Analytics előzetes verziójának megoldással kapcsolatos

![DNS Analytics szimbólum](./media/log-analytics-dns/dns-analytics-symbol.png)

Ez a cikk bemutatja, hogyan beállítását és az Azure Log Analytics az Azure DNS Analytics megoldás segítségével gyűjtse össze a DNS-infrastruktúra a biztonsági, teljesítmény és a műveletek.

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
| [Windows-ügynökök](log-analytics-windows-agent.md) | Igen | A megoldás a DNS-adatok Windows-ügynököktől gyűjti. |
| [Linux-ügynökök](log-analytics-linux-agents.md) | Nem | A megoldás nem DNS-információkat gyűjtsön a közvetlen Linux-ügynökök. |
| [System Center Operations Manager felügyeleti csoport](log-analytics-om-agents.md) | Igen | A megoldás a DNS-adatok egy csatlakoztatott az Operations Manager felügyeleti csoportban lévő ügynököktől gyűjti. Közvetlen kapcsolat legyen az Operations Manager-ügynököt az Operations Management Suite nem kötelező. Adatok a felügyeleti csoportból az Operations Management Suite-tárházba lesznek továbbítva. |
| [Azure Storage-fiók](log-analytics-azure-storage.md) | Nem | A megoldás az Azure storage nem használja. |

### <a name="data-collection-details"></a>Adatok gyűjtése részletei

A megoldás gyűjti DNS-leltár- és DNS-esemény kapcsolatos adatokat a DNS-kiszolgálók ahol egy Log Analytics-ügynök telepítve van. Ezek az adatok ezután a Log Analytics szolgáltatásba feltöltött és a megoldás irányítópultján jelenik meg. A DNS PowerShell-parancsmagok futtatása leltárhoz kapcsolódó adatok, például a DNS-kiszolgálók, zónák és rekordok, számát gyűjti. Az adatok két naponta egyszer frissül. Az esemény-kapcsolódó adatok gyűjtése történik, közel valós időben a [elemzési és auditnaplók](https://technet.microsoft.com/library/dn800669.aspx#enhanc) továbbfejlesztett DNS-naplózás és diagnosztika a Windows Server 2012 R2 által biztosított.

## <a name="configuration"></a>Konfiguráció

A megoldás konfigurálásához kövesse az alábbi adatokat:

- Rendelkeznie kell egy [Windows](log-analytics-windows-agent.md) vagy [az Operations Manager](log-analytics-om-agents.md) ügynököt mindegyik figyelni kívánt DNS-kiszolgálón.
- Az Operations Management Suite-munkaterületet a DNS Analytics megoldás is hozzáadhat a [Azure Marketplace-en](https://aka.ms/dnsanalyticsazuremarketplace). Is használhatja a leírt folyamatot [adja hozzá a Log Analytics solutions kövesse a megoldástárban](log-analytics-add-solutions.md).

A megoldás elindult, nincs szükség további konfigurációs adatok gyűjtése. A következő konfiguráció használatával azonban adatgyűjtés testreszabása.

### <a name="configure-the-solution"></a>A megoldás konfigurálása

A megoldás irányítópultján kattintson **konfigurációs** a DNS Analytics konfigurációs lap megnyitásához. Konfigurációs módosításokat végezhet két típusa van:

- **Engedélyezett tartománynevek**. A megoldás nem dolgozza fel azokat a keresési lekérdezéseket. A tartománynév-utótagokat tartalmazó engedélyezési listát tárol. A keresési lekérdezéseket, hogy a megfelelő tartománynév-utótagokat az engedélyezési listán szereplő tartománynevek nem dolgozza fel a megoldás. Nem dolgozza fel az engedélyezési listához hozzáadni kívánt tartománynevek segít a Log Analytics felé küldött adatok optimalizálása érdekében. Az alapértelmezett engedélyezési lista népszerű tartományneveket, mint például www.google.com, www.facebook.com és magában foglalja. Az alapértelmezett teljes lista megtekintéséhez görgetés.

 Módosíthatja bármely tartománynév utótagja, hogy meg szeretné tekinteni a keresési insights hozzáadása a listában. Valamint eltávolíthatja bármely tartománynév utótagja nem szeretnénk a keresési elemzések megtekintése.

- **"Beszédes ügyfél" küszöbérték**. DNS-ügyfelek, amelyek ki vannak emelve a keresési kérelmek száma meghaladja a küszöbértéket a **DNS-ügyfelek** panelen. Az alapértelmezett küszöbérték 1000. Szerkesztheti a küszöbértéket.

    ![Engedélyezett tartománynevek](./media/log-analytics-dns/dns-config.png)

## <a name="management-packs"></a>Felügyeleti csomagok

Ha a Microsoft Monitoring Agentet az Operations Management Suite-munkaterülethez való kapcsolódáshoz használ, a következő felügyeleti csomag telepítve van:

- Microsoft DNS Data Collector Intelligence Pack (Microsft.IntelligencePacks.Dns)

Ha az Operations Manager felügyeleti csoportban a Operations Management Suite-munkaterülethez van csatlakoztatva, a következő felügyeleti csomagokat az Operations Manager települnek, ha ez a megoldás hozzáadásakor. Ne legyen kötelező konfigurációs vagy karbantartási a felügyeleti csomagok:

- Microsoft DNS Data Collector Intelligence Pack (Microsft.IntelligencePacks.Dns)
- Microsoft System Center Advisor DNS Analytics Configuration (Microsoft.IntelligencePack.Dns.Configuration)

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](log-analytics-om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>A DNS Analytics megoldás használata

Ez a szakasz ismerteti az összes irányítópult funkciók és azok használatát.

Miután a megoldást a munkaterülethez hozzáadott, a megoldás csempe az Operations Management Suite – Áttekintés oldalon a DNS-infrastruktúra gyors összegzését tartalmazza. DNS-kiszolgálók, ahol az adatokat gyűjtenek számát tartalmazza. Rosszindulatú tartományok feloldani az elmúlt 24 órában az ügyfelek által végrehajtott kérelmek száma is tartalmaz. Ha a csempére kattint, megnyílik a megoldás irányítópultján.

![DNS Analytics tile](./media/log-analytics-dns/dns-tile.png)

### <a name="solution-dashboard"></a>A megoldás irányítópultja

A megoldás irányítópultján különböző funkcióhoz a megoldás összefoglaló információit jeleníti meg. A részletes nézet a törvényszéki elemzés és diagnosztika mutató hivatkozásokat is tartalmaz. Alapértelmezés szerint az elmúlt hét napban megjelennek az adatok. A dátum és idő tartomány használatával módosíthatja a **dátum és idő kiválasztása vezérlőelem**, ahogy az alábbi képen látható:

![Kijelölt vezérlő](./media/log-analytics-dns/dns-time.png)

A megoldás irányítópultján a következő paneleken látható:

**DNS-biztonság**. A jelentés a DNS-ügyfelek, amelyek próbál meg kommunikálni a rosszindulatú tartományokat. Használja a Microsoft fenyegetésészlelési intelligenciával foglalkozó hírcsatornákra, DNS-elemzés észlelni tudja az ügyfél IP-címek, amelyhez hozzá szeretne férni a rosszindulatú tartományokat. Sok esetben kártevők által fertőzött eszközök "tárcsázásával" a "parancs és vezérlés" center kártékony tartomány által a kártevő szoftver-tartománynév feloldására.

![DNS-biztonsági panel](./media/log-analytics-dns/dns-security-blade.png)

Ha rákattint egy ügyfél IP-címet a listában, a naplóbeli keresés megnyitja, és a megfelelő lekérdezés keresési részleteit jeleníti meg. A következő példában a DNS Analytics azt észlelte, hogy a kommunikáció az egy [IRCbot](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=Win32/IRCbot):

![A naplókeresések ircbot megjelenítése](./media/log-analytics-dns/ircbot.png)

Az információ segít azonosítani a:

- Ügyfél által kezdeményezett kommunikáció IP-Címmel.
- Tartomány neve, amelyet a rosszindulatú IP-cím.
- A tartománynév feloldása egy olyan IP-címeket.
- Rosszindulatú IP-cím.
- A probléma súlyosságát.
- A kártékony IP-cím áruházat okát.
- Észlelés ideje.

**Lekérdezett tartományok**. A leggyakoribb, a környezetben a DNS-ügyfelek által éppen lekérdezett tartománynevek biztosít. Megtekintheti a lekérdezett tartománynevek listája. Emellett részletezhet keresési kérelem adatait, a naplóbeli keresés egy adott tartománynév.

![Tartományok lekérdezett panel](./media/log-analytics-dns/domains-queried-blade.png)

**A DNS-ügyfelek**. Az ügyfelek jelentések *a küszöbérték megszegéséhez* a választott időszakban lekérdezések száma. Megtekintheti a DNS-ügyfelek és a részletek a Naplókeresésben őket által végrehajtott lekérdezések listáját.

![A DNS-ügyfelek panel](./media/log-analytics-dns/dns-clients-blade.png)

**Dinamikus DNS-regisztráció**. Jelentések adjon nevet a regisztrációs hibák. Az összes regisztrációs hibák cím [erőforrásrekordok](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (írja be A és AAAA) az ügyfél IP-címek, a regisztrációs kérések fényében, amelyek együtt vannak kiemelve. Ezután használhatja ezeket az információkat az alapvető ok a regisztrációs hiba található a következő lépésekkel:

1. Keresse meg a zóna mérvadó a neve, amely az ügyfél megpróbálja frissíteni.

1. A megoldás segítségével ellenőrizheti, hogy a zóna a leltáradatokat.

1. Győződjön meg arról, hogy engedélyezve van-e a zóna a dinamikus frissítés.

1. Ellenőrizze, hogy e vagy sem a zóna van konfigurálva a biztonságos a dinamikus frissítés.

    ![Dinamikus DNS-regisztráció panel](./media/log-analytics-dns/dynamic-dns-reg-blade.png)

**Nevezze el regisztrációs kérelmeket**. A felső csempe sikeres és sikertelen DNS dinamikus frissítési kérelmek trendvonal jeleníti meg. Az alacsonyabb csempe felsorolja a felső 10 ügyfél küldő sikertelen DNS-frissítési kérelmek a DNS-kiszolgálók, a hibák száma szerint rendezve.

![Név regisztrációs kérelmek panel ](./media/log-analytics-dns/name-reg-req-blade.png)

**Eszközillesztő elemzési lekérdezések minta**. A leggyakoribb keresési lekérdezések raw elemzési adatok közvetlenül listáját tartalmazza.


![Mintalekérdezések](./media/log-analytics-dns/queries.png)

Ezek a lekérdezések kiindulási pontként használható a saját testre szabott jelentéskészítési lekérdezések létrehozásáról. A lekérdezéseket a DNS Analytics naplóbeli keresés-oldalának hivatkozása, ahol eredmények jelennek meg:

- **DNS-kiszolgálók listájának**. Minden DNS-kiszolgálók, és azok kapcsolódó FQDN, tartománynév, erdő neve és a kiszolgáló IP-címek listája látható.
- **DNS-zónák listája**. A társított zóna neve, a dinamikus frissítési állapot, a Névkiszolgálók és a DNSSEC-aláírási állapot az összes DNS-zónák listája látható.
- **Nem használt erőforrásrekordok**. A fel nem használt és elavult erőforrás-rekordok listáját jeleníti meg. Ez a lista tartalmazza az erőforrás-rekord neve, erőforrásrekord típusa, a társított DNS-kiszolgáló, rekord létrehozási idő és zóna nevét. Ez a lista segítségével azonosíthatja a DNS-erőforrásrekordok már használatban van. Ezen információ alapján, majd eltávolíthatja ezeket a bejegyzéseket a DNS-kiszolgálókról.
- **DNS-kiszolgálók lekérdezési terhelése**. Információkat jelenít meg, hogy kap egy-a DNS-terhelés szempontjából a DNS-kiszolgálókon. Ezek az információk alapján megtervezheti a kapacitást, a kiszolgálók. Megnyithatja a **metrikák** fülre, és módosítsa a nézet egy grafikus vizualizációt. Ez a nézet segítségével megismerheti, hogyan oszlik meg a DNS-betöltés a DNS-kiszolgálók között. DNS-lekérdezés arány trendek minden olyan kiszolgáló látható.

    ![DNS-kiszolgálók lekérdezések napló keresési eredményei](./media/log-analytics-dns/dns-servers-query-load.png)

- **DNS-zónák lekérdezési terhelése**. Minden zóna DNS zóna lekérdezési – másodpercenkénti statisztikáit mutatja a megoldás által felügyelt DNS-kiszolgálókon. Kattintson a **metrikák** fülre, és módosítsa a nézet a részletes rekordok egy grafikus vizualizációt, az eredmények.
- **Konfigurációs események**. A DNS a konfigurációmódosítási események és a kapcsolódó üzeneteket jeleníti meg. Ezután ezek az események az esemény, eseményazonosító, DNS-kiszolgáló ideje alapján szűrheti vagy Feladatkategória. Az adatok segítséget nyújtanak az adott DNS-kiszolgálók egy adott időpontban végzett módosítások naplózása.
- **DNS-elemzési napló**. A megoldás által felügyelt DNS-kiszolgálók az elemzési eseményeket jeleníti meg. Ezek az események az esemény, eseményazonosító, DNS-kiszolgáló ideje alapján szűrheti majd a keresési lekérdezés és a lekérdezés típusa és a feladat kategóriája ügyfél IP-Címét. DNS-kiszolgáló elemzési eseményeket engedélyezze a műveletek nyomon követése a DNS-kiszolgálón. Egy elemző naplóz minden alkalommal, amikor a kiszolgáló DNS-információkat fogad vagy küld.

### <a name="search-by-using-dns-analytics-log-search"></a>DNS Analytics naplóbeli keresés használatával keresése

A naplók keresése oldalát létrehozhat egy lekérdezést. A keresési eredményeket szűrheti értékkorlátozás vezérlők használatával. Átalakítás, szűrheti és jelentés speciális lekérdezéseket is létrehozhat az eredményeket. Indítsa el az alábbi lekérdezéseket:

1. Az a **lekérdezés keresőmezőbe**, típus `DnsEvents` a megoldás által felügyelt DNS-kiszolgáló által létrehozott összes DNS-események megtekintése. Az eredményeket a keresési lekérdezéseket, a dinamikus regisztrációk és a konfigurációs módosítások kapcsolódó összes eseményt adatainak naplózása listája.

    ![DnsEvents naplók keresése](./media/log-analytics-dns/log-search-dnsevents.png)  

    a. A keresési lekérdezések adatainak megtekintéséhez jelölje ki **LookUpQuery** , a **altípus** szűrő a dimenzió vezérlőből, a bal oldalon. A keresési lekérdezés események a kiválasztott időszakra vonatkozó tartalmazó táblázat jelenik meg.

    b. A dinamikus regisztrációk adatainak megtekintéséhez jelölje ki **DynamicRegistration** , a **altípus** szűrő a dimenzió vezérlőből, a bal oldalon. Egy táblázat listázza az összes dinamikus regisztrációs események a kiválasztott időszakra vonatkozó jelenik meg.

    c. A naplóadatok konfigurációs módosítások megtekintéséhez jelölje ki **ConfigurationChange** , a **altípus** szűrő a dimenzió vezérlőből, a bal oldalon. Egy táblát, amely a kijelölt időszakra vonatkozó összes a konfigurációmódosítási események listája jelenik meg.

1. Az a **lekérdezés keresőmezőbe**, típus `DnsInventory` összes DNS leltárhoz kapcsolódó adatok megtekintéséhez a megoldás által felügyelt DNS-kiszolgálók esetén. Az eredményeket a naplóadatok a DNS-kiszolgálók, DNS-zónák és rekordok listája.

    ![DnsInventory naplók keresése](./media/log-analytics-dns/log-search-dnsinventory.png)

## <a name="feedback"></a>Visszajelzés

Kétféleképpen visszajelzést is:

- **UserVoice**. Közzététele a DNS-elemzési funkciók működését az ötleteit. Látogasson el a [Operations Management Suite UserVoice-lapunkon](https://aka.ms/dnsanalyticsuservoice).
- **Csatlakozzon a kohorsz**. Mindig érdekel rendelkező új ügyfeleket az új funkciók korai hozzáférést kaphat, és segítsen még jobbá tenni a DNS Analytics kohorszok csatlakozzon. Ha érdekli a kohorszok való csatlakozás, töltse ki [ez rövid felmérés](https://aka.ms/dnsanalyticssurvey).

## <a name="next-steps"></a>További lépések

[Naplók keresése](log-analytics-log-searches.md) részletes DNS-rekordok naplózása megtekintéséhez.
