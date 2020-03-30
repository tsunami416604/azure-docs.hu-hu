---
title: DNS-elemzési megoldás az Azure Monitorban | Microsoft dokumentumok
description: Állítsa be és használja a DNS Analytics-megoldást az Azure Monitorban, hogy betekintést nyerjen a DNS-infrastruktúrába a biztonság, a teljesítmény és a műveletek terén.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2018
ms.openlocfilehash: 2471c29f559df5c347c62ceb4c7fd9b4ae1e5eec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657333"
---
# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>A DNS-infrastruktúra elemzéseinek összegyűjtése a DNS Analytics előzetes verziójának megoldásával

![DNS Analytics szimbólum](./media/dns-analytics/dns-analytics-symbol.png)

Ez a cikk bemutatja, hogyan állíthatja be és használhatja az Azure DNS-elemzési megoldást az Azure Monitorban a biztonsággal, a teljesítménnyel és a műveletekkel kapcsolatos DNS-infrastruktúrák betekintéséhez.

A DNS Analytics a következőkben nyújt segítséget:

- Azonosítsa azokat az ügyfeleket, amelyek megpróbálják feloldani a rosszindulatú tartományneveket.
- Azonosítsa az elavult erőforrásrekordokat.
- Azonosítsa a gyakran lekérdezett tartományneveket és a beszédes DNS-ügyfeleket.
- A DNS-kiszolgálók kérelemterhelésének megtekintése.
- Dinamikus DNS-regisztrációs hibák megtekintése.

A megoldás összegyűjti, elemzi és korrelálja a Windows DNS-elemzési és naplózási naplókat és más kapcsolódó adatokat a DNS-kiszolgálókról.

## <a name="connected-sources"></a>Összekapcsolt források

Az alábbi táblázat a megoldás által támogatott csatlakoztatott forrásokat ismerteti:

| **Csatlakoztatott forrás** | **Támogatás** | **Leírás** |
| --- | --- | --- |
| [Windows-ügynökök](../platform/agent-windows.md) | Igen | A megoldás DNS-adatokat gyűjt a Windows-ügynököktől. |
| [Linux-ügynökök](../learn/quick-collect-linux-computer.md) | Nem | A megoldás nem gyűjt DNS-adatokat a közvetlen Linux-ügynököktől. |
| [System Center Operations Manage felügyeleti csoport](../platform/om-agents.md) | Igen | A megoldás dns-adatokat gyűjt egy csatlakoztatott Operations Manager felügyeleti csoport ügynökeitől. Nincs szükség közvetlen kapcsolatra az Operations Manager-ügynök és az Azure Monitor között. Az adatok továbbítása a felügyeleti csoportból a Log Analytics munkaterületre. |
| [Azure tárfiók](../platform/collect-azure-metrics-logs.md) | Nem | Az Azure storage-t a megoldás nem használja. |

### <a name="data-collection-details"></a>Adatgyűjtésrészletei

A megoldás dns-leltárt és DNS-eseményekkel kapcsolatos adatokat gyűjt azoktól a DNS-kiszolgálóktól, amelyeken a Log Analytics-ügynök telepítve van. Ezeket az adatokat ezután feltölti az Azure Monitorba, és megjelenik a megoldás irányítópultján. A készlettel kapcsolatos adatokat, például a DNS-kiszolgálók, zónák és erőforrásrekordok számát a DNS PowerShell-parancsmagok futtatásával gyűjti a rendszer. Az adatok kétnaponta egyszer frissülnek. Az eseményekkel kapcsolatos adatokat a rendszer közel valós időben gyűjti a Windows Server 2012 R2 továbbfejlesztett DNS-naplózása és diagnosztikája által biztosított [elemzési és naplóból.](https://technet.microsoft.com/library/dn800669.aspx#enhanc)

## <a name="configuration"></a>Konfiguráció

A megoldás konfigurálásához használja az alábbi információkat:

- Minden figyelni kívánt DNS-kiszolgálón rendelkeznie kell egy [Windows](../platform/agent-windows.md) vagy [Operations](../platform/om-agents.md) Manager-ügynökkel.
- A DNS Analytics-megoldást hozzáadhatja a Log Analytics-munkaterülethez az [Azure Piactérről.](https://aka.ms/dnsanalyticsazuremarketplace) Az [Azure Monitor-megoldások hozzáadása a Megoldások tárból](solutions.md)című albumban ismertetett folyamatot is használhatja.

A megoldás további konfiguráció nélkül kezdi meg az adatgyűjtést. A következő konfigurációval azonban testre szabhatja az adatgyűjtést.

### <a name="configure-the-solution"></a>A megoldás konfigurálása

A megoldás irányítópultján kattintson a **Konfiguráció** gombra a DNS Analytics konfigurációja lap megnyitásához. Kétféle konfigurációs módosítást tehet ünk meg:

- **Engedélyezési tartománynevek**. A megoldás nem dolgozza fel az összes lekérdezést. A tartománynév-utótagok engedélyezési listáját tartja fenn. Az engedélyezési listán szereplő tartománynév-utótagoknak megfelelő tartományneveket feloldó lekérdezéseket a megoldás nem dolgozza fel. Az engedélyezési tartománynevek feldolgozásának lenem küldése segít az Azure Monitornak küldött adatok optimalizálásában. Az alapértelmezett engedélyezési lista népszerű nyilvános tartományneveket tartalmaz, például www.google.com és www.facebook.com. A teljes alapértelmezett listát görgetéssel tekintheti meg.

  A lista módosításával hozzáadhat ja a tartománynév-utótagot, amelyhez meg szeretné tekinteni a keresett elemzéseket. Eltávolíthatja azokat a tartománynév-utótagokat is, amelyek szempontjából nem szeretné megtekinteni a keresett elemzéseket.

- **Beszédes ügyfél küszöb**. A keresési kérelmek száma küszöbértékét túllépő DNS-ügyfelek kiemelve jelennek meg a **DNS-ügyfelek** panelen. Az alapértelmezett küszöbérték 1000. A küszöbértéket módosíthatja.

    ![Engedélyezési listás tartománynevek](./media/dns-analytics/dns-config.png)

## <a name="management-packs"></a>Felügyeleti csomagok

Ha a Microsoft Monitoring Agent segítségével csatlakozik a Log Analytics-munkaterülethez, a következő felügyeleti csomag lesz telepítve:

- Microsoft DNS-adatgyűjtő–intelligenciacsomag (Microsoft.IntelligencePacks.Dns)

Ha az Operations Manager felügyeleti csoport csatlakozik a Log Analytics-munkaterülethez, a következő felügyeleti csomagok települnek az Operations Managerbe, amikor hozzáadja ezt a megoldást. A felügyeleti csomagok nem igényelnek szükséges konfigurációt vagy karbantartást:

- Microsoft DNS-adatgyűjtő–intelligenciacsomag (Microsoft.IntelligencePacks.Dns)
- Microsoft System Center Advisor DNS-elemzés konfigurációja (Microsoft.IntelligencePack.Dns.Configuration)

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](../platform/om-agents.md).

## <a name="use-the-dns-analytics-solution"></a>A DNS Analytics megoldás használata

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]


A DNS csempe tartalmazza azoknak a DNS-kiszolgálóknak a számát, amelyeken az adatok gyűjtése történik. Azt is tartalmazza, hogy az ügyfelek hány kérelmet nyújtottak be a rosszindulatú tartományok feloldására az elmúlt 24 órában. Amikor a csempére kattint, megnyílik a megoldás irányítópultja.

![DNS Analytics csempe](./media/dns-analytics/dns-tile.png)

### <a name="solution-dashboard"></a>A megoldás irányítópultja

A megoldás irányítópultja a megoldás különböző funkcióinak összesített adatait jeleníti meg. Ez is tartalmaz linkeket a részletes nézet törvényszéki elemzés és diagnózis. Alapértelmezés szerint az adatok az elmúlt hét napban jelennek meg. A dátum- és időtartományt a **dátum-idő választó vezérlővel**módosíthatja, ahogy az az alábbi képen látható:

![Időválasztó vezérlő](./media/dns-analytics/dns-time.png)

A megoldás irányítópultján a következő panelek láthatók:

**DNS-biztonság**. Jelenti a rosszindulatú tartományokkal kommunikálni próbáló DNS-ügyfeleket. A Microsoft fenyegetésfelderítési hírcsatornáinak használatával a DNS Analytics képes észlelni a rosszindulatú tartományokelérését próbáló ügyfélIP-eket. Sok esetben a rosszindulatú programokkal fertőzött eszközök "tárcsázza" a rosszindulatú tartomány "parancs és vezérlés" központjába a rosszindulatú tartomány név feloldásával.

![A DNS biztonsága panel](./media/dns-analytics/dns-security-blade.png)

Amikor egy ügyfél IP-címére kattint a listában, megnyílik a Naplókeresés, és megjeleníti az adott lekérdezés keresési adatait. A következő példában a DNS Analytics észlelte, hogy a kommunikáció egy [IRCbot-kal](https://www.microsoft.com/en-us/wdsi/threats/malware-encyclopedia-description?Name=Backdoor:Win32/IRCbot&threatId=2621)történt:

![Az ircbotot megjelenítő keresési eredmények naplózása](./media/dns-analytics/ircbot.png)

Az információk segítségével azonosíthatja a következőket:

- A kommunikációt kezdeményező ügyfél IP-cím.
- A rosszindulatú IP-címre feloldó tartománynév.
- Azok az IP-címek, amelyekhez a tartománynév feloldódik.
- Rosszindulatú IP-cím.
- A probléma súlyossága.
- A rosszindulatú IP feketelistára vételének oka.
- Az észlelés ideje.

**Lekérdezett tartományok**. A környezetben lévő DNS-ügyfelek által lekérdezett leggyakoribb tartományneveket tartalmazza. Megtekintheti az összes lekérdezett tartománynév listáját. A naplókeresésben egy adott tartománynév keresési kérelmének részleteit is leáshatja.

![Lekérdezett tartományok panel](./media/dns-analytics/domains-queried-blade.png)

**DNS-ügyfelek**. Jelenti, hogy az ügyfelek a kiválasztott időszakban a lekérdezések száma *küszöbértéket.* Megtekintheti az összes DNS-ügyfél listáját és az általuk végzett lekérdezések részleteit a Naplókeresés ben.

![DNS-ügyfelek panel](./media/dns-analytics/dns-clients-blade.png)

**Dinamikus DNS-regisztrációk**. Névregisztrációs hibákat jelent. A [címerőforrás-rekordok](https://en.wikipedia.org/wiki/List_of_DNS_record_types) (A és AAAA típus) összes regisztrációs hibája ki van emelve a regisztrációs kérelmeket elíró ügyfél IP-címekkel együtt. Ezt az információt az alábbi lépések végrehajtásával használhatja a regisztrációs hiba kiváltó okának megkereséséhez:

1. Keresse meg azt a zónát, amely mérvadó az ügyfél által frissíteni kívánt névhez.

1. A megoldás segítségével ellenőrizze a zóna készletadatait.

1. Ellenőrizze, hogy engedélyezve van-e a zóna dinamikus frissítése.

1. Ellenőrizze, hogy a zóna biztonságos dinamikus frissítésre van-e konfigurálva.

    ![Dinamikus DNS-regisztrációk panel](./media/dns-analytics/dynamic-dns-reg-blade.png)

**Névregisztrációs kérelmek**. A felső csempén a sikeres és sikertelen DNS dinamikus frissítési kérelmek trendvonala látható. Az alsó csempe felsorolja a sikertelen DNS-frissítési kérelmeket küldő 10 legfontosabb ügyfelet a DNS-kiszolgálóknak, a hibák száma szerint rendezve.

![Névregisztrációs kérelmek panel](./media/dns-analytics/name-reg-req-blade.png)

**Minta DDI-elemzési lekérdezések**. A leggyakoribb keresési lekérdezések listáját tartalmazza, amelyek közvetlenül lekérik a nyers elemzési adatokat.


![Mintalekérdezések](./media/dns-analytics/queries.png)

Ezeket a lekérdezéseket kiindulási pontként használhatja a testreszabott jelentésekhez saját lekérdezések létrehozásához. A lekérdezések a DNS Analytics naplókeresési lapra mutatnak, ahol az eredmények megjelennek:

- **A DNS-kiszolgálók listája**. Az összes DNS-kiszolgáló listáját jeleníti meg a hozzájuk tartozó teljes tartománynévvel, tartománynévvel, erdőnévvel és kiszolgálóIP-kiszolgálókkal.
- **A DNS-zónák listája**. A társított zónanévvel, dinamikus frissítési állapottal, névkiszolgálókkal és DNSSEC-aláírási állapottal rendelkező DNS-zónák listáját jeleníti meg.
- **Nem használt erőforrásrekordok**. Az összes fel nem használt/elavult erőforrásrekord listáját jeleníti meg. Ez a lista tartalmazza az erőforrásrekord nevét, az erőforrásrekord típusát, a kapcsolódó DNS-kiszolgálót, a rekordgenerálási időt és a zónanevét. A lista segítségével azonosíthatja azokat a DNS-erőforrásrekordokat, amelyek már nincsenek használatban. Ezen információk alapján ezután eltávolíthatja ezeket a bejegyzéseket a DNS-kiszolgálókról.
- **A DNS-kiszolgálók lekérdezése betöltése**. Információkat jelenít meg, hogy megkaphassa a DNS-kiszolgálók DNS-terhelésének perspektíváját. Ez az információ segíthet a kiszolgálók kapacitásának megtervezésében. A **Metrikák** lapon grafikus vizualizációra módosíthatja a nézetet. Ez a nézet segít megérteni, hogyan oszlik el a DNS-terhelés a DNS-kiszolgálók között. Az egyes kiszolgálók DNS-lekérdezési sebességének tendenciáit jeleníti meg.

    ![DNS-kiszolgálók lekérdezési naplókeresési eredményei](./media/dns-analytics/dns-servers-query-load.png)

- **DNS-zónák lekérdezésbetöltése**. A megoldás által kezelt DNS-kiszolgálók összes zónájának másodpercenkénti statisztikáját jeleníti meg. A **Metrikák** fülre kattintva módosíthatja a nézetet a részletes rekordokról az eredmények grafikus megjelenítésére.
- **Konfigurációs események**. Megjeleníti az összes DNS-konfigurációmódosítási eseményt és a kapcsolódó üzeneteket. Ezután szűrheti ezeket az eseményeket az esemény, az eseményazonosító, a DNS-kiszolgáló vagy a feladatkategória alapján. Az adatok segítségével naplózhatja az adott DNS-kiszolgálókon adott időpontokban végrehajtott módosításokat.
- **DNS analitikai napló**. A megoldás által kezelt összes DNS-kiszolgáló összes analitikus eseményének megjelenítése. Ezután szűrheti ezeket az eseményeket az esemény, az eseményazonosító, a DNS-kiszolgáló, a keresési lekérdezést készítő ügyfél IP-cím és a lekérdezéstípusú feladatkategória alapján. A DNS-kiszolgáló analitikus eseményei lehetővé teszik a tevékenységkövetést a DNS-kiszolgálón. A kiszolgáló minden alkalommal naplóz egy analitikus eseményt, amikor DNS-adatokat küld vagy fogad.

### <a name="search-by-using-dns-analytics-log-search"></a>Keresés a DNS Analytics naplókeresés használatával

A Naplókeresés lapon lekérdezést hozhat létre. A keresési eredményeket a mezőelemek használatával szűrheti. Speciális lekérdezéseket is létrehozhat az eredmények átalakításához, szűréséhez és jelentéséhez. Kezdje a következő lekérdezésekkel:

1. A **keresési lekérdezés**mezőbe `DnsEvents` írja be a megoldás által kezelt DNS-kiszolgálók által létrehozott összes DNS-esemény megtekintéséhez. Az eredmények a keresési lekérdezésekhez, a dinamikus regisztrációkhoz és a konfigurációs módosításokhoz kapcsolódó összes esemény naplóadatait sorolják fel.

    ![DnsEvents naplókeresés](./media/dns-analytics/log-search-dnsevents.png)  

    a. A keresőlekérdezések naplóadatainak megtekintéséhez válassza a **LookUpQuery** **altípus** szűrőt a bal oldali lapadat-vezérlőből. Megjelenik egy tábla, amely a kijelölt időszak összes lekérdezési eseményét felsorolja.

    b. A dinamikus regisztrációk naplóadatainak megtekintéséhez válassza a **DynamicRegistration** **altípus** szűrőt a bal oldali lapszámvezérlőből. Megjelenik egy táblázat, amely a kiválasztott időszak összes dinamikus regisztrációs eseményét felsorolja.

    c. A konfigurációs módosítások naplóadatainak megtekintéséhez válassza a **ConfigurationChange** as the **Subtype** szűrő t a bal oldali lapadat-vezérlőből. Megjelenik egy táblázat, amely a kiválasztott időszak összes konfigurációváltozási eseményét sorolja fel.

1. A **keresési lekérdezés**mezőbe `DnsInventory` írja be a megoldás által kezelt DNS-kiszolgálók összes DNS-leltárral kapcsolatos adatának megtekintését. Az eredmények a DNS-kiszolgálók, DNS-zónák és erőforrásrekordok naplóadatait sorolják fel.

    ![DnsInventory naplókeresés](./media/dns-analytics/log-search-dnsinventory.png)
    
## <a name="troubleshooting"></a>Hibaelhárítás

Gyakori hibaelhárítási lépések:

1. Hiányzó DNS-keresések adatai – A probléma elhárításához próbálja meg alaphelyzetbe alapozni a konfigurációt, vagy csak egyszer töltse be a konfigurációs lapot a portálon. Alaphelyzetbe állításhoz egyszerűen módosítsa a beállítást egy másik értékre, majd módosítsa vissza az eredeti értékre, és mentse a konfigurációt.

## <a name="feedback"></a>Visszajelzés

Visszajelzéshez látogasson el a [Log Analytics UserVoice oldalra,](https://aka.ms/dnsanalyticsuservoice) ahol ötleteket tesz közzé a DNS Analytics-funkciókkal kapcsolatban. 

## <a name="next-steps"></a>További lépések

[Lekérdezési naplók a](../log-query/log-query-overview.md) részletes DNS-naplórekordok megtekintéséhez.
