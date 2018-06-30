---
title: Az Azure Naplóelemzés DNS elemzési megoldások |} Microsoft Docs
description: Állítsa be, és Naplóelemzési a DNS elemzési megoldások segítségével gyűjtse össze a DNS-infrastruktúra a biztonsággal, a teljesítmény és a műveletek betekintést.
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
ms.openlocfilehash: a6f63fac85008425f473f431ae85d04f62eed667
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127591"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>A DNS-infrastruktúra DNS Analytics Preview megoldással észrevételeket összegyűjtése

![DNS Analytics szimbólum](./media/log-analytics-dns/dns-analytics-symbol.png)

Ez a cikk ismerteti, hogyan beállítását és az Azure Log Analytics az Azure DNS elemzési megoldások segítségével gyűjtse össze a DNS-infrastruktúra a biztonsággal, a teljesítmény és a műveletek betekintést.

DNS Analytics nyújt segítséget:

- Ügyfelek, amelyek kártékony tartománynevek azonosítása.
- Elavult erőforrásrekordok azonosításához.
- Azonosítsa, gyakran lekérdezett tartománynevek és talkative DNS-ügyfeleket.
- DNS-kiszolgálók nézet kérelem terhelése.
- Tekintse meg a dinamikus DNS-regisztráció sikertelen.

A megoldás gyűjti, elemzi és a Windows DNS elemzési és a vizsgálati naplók és egyéb kapcsolódó adatokat a DNS-kiszolgálók ad eredményül.

## <a name="connected-sources"></a>Összekapcsolt források

A következő táblázat a csatlakoztatott adatforrások, ez a megoldás által támogatott:

| **Csatlakoztatott forrás** | **Támogatás** | **Leírás** |
| --- | --- | --- |
| [Windows-ügynökök](log-analytics-windows-agent.md) | Igen | A megoldás DNS információt gyűjt a Windows-ügynökök. |
| [Linux-ügynökök](log-analytics-linux-agents.md) | Nem | A megoldás nem DNS-információkat gyűjtsön a közvetlen Linux-ügynököt. |
| [System Center Operations Manager felügyeleti csoport](log-analytics-om-agents.md) | Igen | A megoldás DNS adatokat gyűjt az ügynökök a csatlakoztatott Operations Manager felügyeleti csoportban. Az Operations Management Suite közvetlen kapcsolat az Operations Manager-ügynök nincs szükség. Az Operations Management Suite tárházba adat továbbítódik a felügyeleti csoportból. |
| [Azure Storage-fiók](log-analytics-azure-storage.md) | Nem | Az Azure storage a megoldás nem használja. |

### <a name="data-collection-details"></a>Az gyűjtemény adatait

A megoldás gyűjti DNS-készlet és a DNS-esemény kapcsolatos adatok a DNS-kiszolgálókról ahol Log Analytics-ügynök telepítve van. Az adatok majd Naplóelemzési feltöltött és a megoldás irányítópultja jelenik meg. A DNS PowerShell-parancsmagok futtatását leltárral kapcsolatos adatok, például a DNS-kiszolgálók, a zóna és a erőforrásrekordokat, számát gyűjti. Az adatokat két naponta egyszer frissül. Az esemény kapcsolatos adatok gyűjtése közel valós időben a a [elemzési és a naplók](https://technet.microsoft.com/library/dn800669.aspx#enhanc) továbbfejlesztett DNS-naplózás és diagnosztika a Windows Server 2012 R2 által biztosított.

## <a name="configuration"></a>Konfiguráció

Az alábbi információk segítségével konfigurálja a megoldást:

- Rendelkeznie kell egy [Windows](log-analytics-windows-agent.md) vagy [Operations Manager](log-analytics-om-agents.md) ügynök figyelni kívánt minden DNS-kiszolgálón.
- Az Operations Management Suite munkaterületen, a DNS elemzési megoldások is hozzáadhat a [Azure piactér](https://aka.ms/dnsanalyticsazuremarketplace). Használhatja a ismertetett folyamatot [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md).

A megoldás további konfiguráció nélkül adatgyűjtés megkezdése. A következő konfigurációs használatával azonban adatgyűjtés testreszabása.

### <a name="configure-the-solution"></a>A megoldás konfigurálása

A megoldás irányítópultján kattintson **konfigurációs** a DNS-Analytics konfigurációs oldal megnyitásához. Konfigurációs változások, hogy két típusa van:

- **Tartománynevek szerepel az engedélyezési listán**. A megoldás nem dolgozza fel a keresési lekérdezések. A tartománynév-utótagokat az engedélyezési lista tart fenn. A tartománynév-utótagokat az engedélyezési lista a megfelelő tartománynév feloldható keresési lekérdezések nem dolgozza fel a megoldás. Nem dolgozza fel az engedélyezési listán szereplő tartománynevek segít Naplóelemzési küldött adatok optimalizálása érdekében. Az alapértelmezett engedélyezési lista népszerű nyilvános tartomány nevét, például www.google.com és www.facebook.com tartalmazza. Az alapértelmezett teljes lista megtekintéséhez görgetéshez használható.

 Módosíthatja a tartománynév utótagja, hogy meg szeretné tekinteni a keresési insights hozzáadása a listában. Is eltávolíthatja a tartománynév utótagja nem kívánt keresési elemzések a megtekintéséhez.

- **Talkative ügyfél küszöbérték**. DNS-ügyfelek, amelyek mérete meghaladja a küszöbértéket a címkeresési kérelmek száma emel ki a **DNS-ügyfelek** panelen. Az alapértelmezett küszöbértéke 1000. Szerkesztheti a küszöbértéket.

    ![Szerepel az engedélyezési listán tartománynevek](./media/log-analytics-dns/dns-config.png)

## <a name="management-packs"></a>Felügyeleti csomagok

Ha az Operations Management Suite-munkaterülettel való csatlakozáshoz használ a Microsoft Monitoring Agent, a következő felügyeleti csomag telepítve van:

- Microsoft DNS Data Collector Intelligence Pack (Microsft.IntelligencePacks.Dns)

Ha az Operations Manager felügyeleti csoport az Operations Management Suite-munkaterületre van csatlakoztatva, a következő felügyeleti csomagokat az Operations Manager települnek, amikor ez a megoldás. Nincs szükség konfigurációs vagy karbantartási a felügyeleti csomagok:

- Microsoft DNS Data Collector Intelligence Pack (Microsft.IntelligencePacks.Dns)
- Microsoft System Center Advisor DNS Analytics Configuration (Microsoft.IntelligencePack.Dns.Configuration)

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](log-analytics-om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>A DNS elemzési megoldás használja

Ez a szakasz ismerteti az irányítópult funkciók és a használatukat.

A megoldás felvételét a munkaterületet, a megoldás csempe az Operations Management Suite – Áttekintés lapon a DNS-infrastruktúra gyors összegzését tartalmazza. Ez magában foglalja a DNS-kiszolgálók, ahol a folyik adatgyűjtés a száma. Ezenkívül tartalmazza a rosszindulatú tartományok feloldani az elmúlt 24 órában az ügyfelek által intézett kérések száma. A csempére kattintva megnyílik a megoldás irányítópultja.

![DNS Analytics tile](./media/log-analytics-dns/dns-tile.png)

### <a name="solution-dashboard"></a>A megoldás irányítópultja

A megoldás irányítópultja a megoldás a különféle szolgáltatások összefoglaló információit jeleníti meg. A részletes nézet bírósági elemzéshez és diagnosztikai mutató hivatkozásokat is tartalmaz. Alapértelmezés szerint az adatok jelennek meg az elmúlt hét napban. A dátum és idő tartomány használatával módosíthatja a **dátum és idő kiválasztása vezérlő**, a következő ábrán látható módon:

![Kijelölés vezérlő](./media/log-analytics-dns/dns-time.png)

A megoldás irányítópult a következő paneleken jeleníti meg:

**DNS-biztonság**. A jelentés a DNS-ügyfelek próbáló rosszindulatú tartományok kommunikálni. Microsoft fenyegetés eszközintelligencia hírcsatornák használatával DNS Analytics ügyfél IP-címek, amelyek az elérni kívánt rosszindulatú tartományok képes észlelni. Sok esetben kártevők fertőzött eszközök "tárcsázásával" a "parancs és vezérlő" center a rosszindulatú tartomány által a kártevő szoftverek tartománynév feloldása.

![DNS-biztonság panel](./media/log-analytics-dns/dns-security-blade.png)

Kattintson egy ügyfél IP-cím a listában, ha a naplófájl-keresési megnyílik, és a megfelelő lekérdezés keresési részleteit jeleníti meg. A következő példában a DNS-elemzés azt észlelte, hogy a kommunikáció végezhető el a egy [IRCbot](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=Win32/IRCbot):

![Napló keresési eredményeket megjelenítő ircbot](./media/log-analytics-dns/ircbot.png)

Az információk alapján azonosíthatja a:

- Ügyfél IP-cím által kezdeményezett kommunikáció.
- Az tartománynév, amely az kártékony IP-cím.
- IP-címek, amely a tartomány nevét.
- Kártékony IP-címet.
- A probléma súlyossága.
- A rosszindulatú IP letiltott okát.
- Észlelés ideje.

**Tartományok lekérdezett**. A DNS-ügyfelek számára az adott környezetben lekérdezett leggyakoribb tartománynevek biztosít. A lekérdezett tartománynevek listáját tekintheti meg. Is tovább részletezhető le egy adott tartomány nevét, a keresési napló keresési kérelem részleteit.

![Tartományok lekérdezett panel](./media/log-analytics-dns/domains-queried-blade.png)

**DNS-ügyfelek**. Az ügyfelek jelentések *a küszöbérték megsértése* a választott időszakban lekérdezések száma. A lista összes DNS-ügyfelek és a lekérdezéseiről azokat a keresési napló részletes adatait tekintheti meg.

![DNS-ügyfelek panel](./media/log-analytics-dns/dns-clients-blade.png)

**Dinamikus DNS-regisztráció**. Jelentések eszközregisztrációs hibák nevet. Minden eszközregisztrációs hibák cím [erőforrásrekordok](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (típus A és AAAA) együtt az ügyfél IP-címet, amely a regisztrációs kérelmet vannak kiemelve. Ez az információ segítségével majd az alapvető ok, a regisztrációs hiba található a következő lépések végrehajtásával:

1. A zóna mérvadó a neve, amely az ügyfél megpróbálja frissíteni található.

2. A megoldás használatával ellenőrizze, hogy a zóna a leltáradatokat.

3. Győződjön meg arról, hogy engedélyezve van-e a dinamikus frissítést, a zóna.

4. Ellenőrizze, hogy a zóna konfigurálták biztonságos dinamikus frissítés vagy sem.

    ![Dinamikus DNS-regisztráció panel](./media/log-analytics-dns/dynamic-dns-reg-blade.png)

**Regisztrációs kérelem neve**. A felső csempe sikeres és sikertelen DNS dinamikus frissítési kérelmek trendvonal jeleníti meg. Az alsó csempe felsorolja a felső 10 ügyfelek sikertelen DNS-frissítési kéréseket a DNS-kiszolgálók, a hibák száma alapján rendezve üzenetet küld.

![Név regisztrációs kérelmek panel ](./media/log-analytics-dns/name-reg-req-blade.png)

**Eszközillesztő elemzési lekérdezések minta**. A leggyakoribb keresési lekérdezések raw analytics adatlehívás közvetlenül listáját tartalmazza.


![Mintalekérdezések](./media/log-analytics-dns/queries.png)

Ezeket a lekérdezéseket kiindulási pontként használható a saját testreszabott jelentéskészítéshez lekérdezések létrehozásáról. A lekérdezések hivatkozásra kattintva a DNS Analytics napló keresése oldal, ahol eredmény jelenik meg:

- **DNS-kiszolgálók listája**. Az összes DNS-kiszolgálók, a kapcsolódó teljes Tartománynevét, tartománynév, erdő neve, és a kiszolgáló IP-címek listáját jeleníti meg.
- **DNS-zónák listájának**. A társított zóna nevét, a dinamikus frissítési állapot, a kiszolgálókat és a DNSSEC-aláírási állapot az összes DNS-zóna listáját tartalmazza.
- **Nem használt erőforrásrekordok**. A nem használt vagy elavult erőforrás-rekordok listáját jeleníti meg. Ez a lista azokat az erőforrásrekord-névhez, erőforrásrekord típusa, a társított DNS-kiszolgáló, rekord eseménygenerálás és zóna neve. Ez a lista segítségével azonosíthatja a DNS-erőforrásrekordok, amelyeket már nem használnak. Ezen információ alapján, majd eltávolíthat tételekhez a DNS-kiszolgálók.
- **DNS-kiszolgálók lekérdezése terhelés**. Információit jeleníti meg, hogy a DNS-kiszolgálókon is ki egy perspektívát, a DNS-terhelés. Az információ segítségével tervezheti meg a kapacitás, a kiszolgálókat. Nyissa meg a **metrikák** fülre, és módosítsa a nézet grafikus képi megjelenítés. Ez a nézet segít megérteni, hogyan oszlik meg a DNS-terhelés a DNS-kiszolgálók között. DNS-lekérdezések aránya trendek kiszolgálónként mutatja.

    ![DNS-kiszolgálók lekérdezések napló keresési eredményei](./media/log-analytics-dns/dns-servers-query-load.png)

- **DNS-zóna lekérdezése a terhelés**. A DNS-zóna lekérdezés-/ másodperc statisztikát a zónák a megoldás által kezelt DNS-kiszolgálókon jeleníti meg. Kattintson a **metrikák** fülre, és módosítsa a nézet a részletes rekordok az eredmények grafikus képi megjelenítés.
- **Konfigurációs események**. A DNS Konfigurációmódosítási események és a kapcsolódó üzeneteket jeleníti meg. Ezek az események az esemény, eseményazonosító, a DNS-kiszolgáló ideje alapján majd végezhet vagy Feladatkategória. Az adatok segíthetnek naplózni a megadott DNS-kiszolgálók egy adott időpontban végzett módosítások.
- **DNS-analitikai naplója**. Az elemzési eseményeket jeleníti meg a megoldás által kezelt DNS-kiszolgálók. Ezek az események az esemény, eseményazonosító, a DNS-kiszolgáló ideje alapján majd végezhet ügyfél IP-cím a keresési lekérdezés és a lekérdezés típus Feladatkategória. DNS-kiszolgáló elemzési események nyomon követése a DNS-kiszolgálón tevékenység engedélyezése. Elemzési eseményt naplózott, minden alkalommal, amikor a kiszolgáló küld vagy fogad a DNS-adatok.

### <a name="search-by-using-dns-analytics-log-search"></a>DNS-Analytics napló keresés a keresés

A napló lapon létrehozhat egy lekérdezést. A keresési eredményeket szűrheti értékkorlátozás vezérlők használatával. Az eredmények átalakító, a szűrő és a jelentés speciális lekérdezéseket is létrehozhat. Indítsa el az alábbi lekérdezéseket:

1. Az a **lekérdezés keresőmezőbe**, típus `DnsEvents` a megoldás által kezelt DNS-kiszolgálók által létrehozott DNS-események megtekintése. Az eredmények a naplóadatok keresési lekérdezések, a dinamikus regisztráció és a konfigurációs módosítások kapcsolódó összes esemény felsorolása.

    ![DnsEvents napló keresése](./media/log-analytics-dns/log-search-dnsevents.png)  

    a. Válassza ki, ha a keresési lekérdezések adatainak naplózása **LookUpQuery** , a **altípus** szűrő a bal oldali értékkorlátozás vezérlőből. A keresési lekérdezés események a kiválasztott időszak tartalmazó táblázat jelenik meg.

    b. Válassza ki, ha a dinamikus regisztrációk adatainak naplózása **DynamicRegistration** , a **altípus** szűrő a bal oldali értékkorlátozás vezérlőből. A dinamikus regisztrációs események a kiválasztott időszak tartalmazó táblázat jelenik meg.

    c. Válassza ki, ha a konfigurációs módosítások naplóadatok **konfigurációváltozás** , a **altípus** szűrő a bal oldali értékkorlátozás vezérlőből. Minden a konfigurációmódosítási események a kiválasztott időszak tartalmazó táblázat jelenik meg.

2. Az a **lekérdezés keresőmezőbe**, típus `DnsInventory` minden DNS leltárral kapcsolatos adatok megtekintéséhez a megoldás által kezelt DNS-kiszolgálók. Az eredmények a naplóadatok a DNS-kiszolgálók, DNS-zónák és erőforrásrekordok listában.

    ![DnsInventory napló keresése](./media/log-analytics-dns/log-search-dnsinventory.png)

## <a name="feedback"></a>Visszajelzés

Két módon visszajelzést is:

- **UserVoice**. DNS elemzési szolgáltatások működését az ötletek utáni. Látogasson el a [Operations Management Suite UserVoice lap](https://aka.ms/dnsanalyticsuservoice).
- **Csatlakozás a kohorszok**. Azt mindig kíváncsiak vagyunk abban, csatlakoztassa az új szolgáltatások korai eléréséhez és DNS elemzési javítása érdekében cohorts új ügyfelek. Ha érdekli a cohorts csatlakoztatása, töltse ki [a gyors felmérés](https://aka.ms/dnsanalyticssurvey).

## <a name="next-steps"></a>További lépések

[Naplók keresése](log-analytics-log-searches.md) DNS-napló részletes rekordok megtekintéséhez.
