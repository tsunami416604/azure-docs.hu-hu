---
title: Az Active Directory replikációs állapot figyelése az Azure Monitor szolgáltatással |} A Microsoft Docs
description: Az Active Directory replikációs állapota megoldáscsomag rendszeresen figyeli az Active Directory-környezetet a replikációs hibákat.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 1b988972-8e01-4f83-a7f4-87f62778f91d
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/24/2018
ms.author: magoedte
ms.openlocfilehash: f7bbde98c6ef35021cc03b2646193d3601ca1cff
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59425848"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Az Active Directory replikációs állapot figyelése az Azure Monitor szolgáltatással

![AD-replikáció állapotát szimbólum](./media/ad-replication-status/ad-replication-status-symbol.png)

Active Directory a vállalati IT-környezet legfontosabb összetevője. Annak érdekében, magas rendelkezésre állású és nagy teljesítményű, minden egyes tartományvezérlő rendelkezik az Active Directory-adatbázis a saját példányát. Annak érdekében, hogy a módosítások propagálása a vállalaton belül a tartományvezérlő replikálja egymással. Hibák a replikációs folyamat különböző problémákat okozhat a vállalaton belül.

Az AD Replication Status megoldáscsomag rendszeresen figyeli az Active Directory-környezetet a replikációs hibákat.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>A megoldás telepítése és konfigurálása
A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat.

### <a name="install-agents-on-domain-controllers"></a>Ügynökök telepítése tartományvezérlőn
Ügynököket kell telepítenie a tartományvezérlőkön, amelyek ki kell értékelni a tartomány tagjai. Vagy tag kiszolgálón telepíti az ügynökök és az ügynökök AD replikációs adatokat küldeni az Azure Monitor konfigurálnia kell. Windows-számítógépek csatlakoztatása az Azure monitornak ismertetése: [csatlakozás Windows számítógépek számára az Azure Monitor](../../azure-monitor/platform/agent-windows.md). Ha a tartományvezérlő már része egy meglévő System Center Operations Manager-környezettel, amelyet szeretne csatlakozni az Azure Monitor, lásd: [Operations Manager csatlakoztatása az Azure monitornak](../../azure-monitor/platform/om-agents.md).

### <a name="enable-non-domain-controller"></a>Nem tartományvezérlő engedélyezése
Ha nem szeretné a tartományvezérlők bármelyikét közvetlenül csatlakozhat az Azure Monitor, segítségével többi számítógép csatlakozik az Azure Monitor a tartományban az AD Replication Status megoldás Pack adatokat gyűjthet, és hogy az adatok küldése.

1. Ellenőrizze, hogy a számítógép a tartományhoz, akinél szeretné figyelni az AD Replication Status megoldás használatával.
2. [A Windows-számítógép csatlakozhat az Azure Monitor](../../azure-monitor/platform/om-agents.md) vagy [csatlakoztathatja azt a meglévő Operations Manager-környezetbe az Azure Monitor használatával](../../azure-monitor/platform/om-agents.md), ha nem kapcsolódik.
3. Az adott számítógépen állítsa be a következő beállításkulcsot:<br>Kulcs: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<ManagementGroupName>\Solutions\ADReplication**<br>Érték: **IsTarget**<br>Érték: **igaz**

   > [!NOTE]
   > Ezek a változások nem lépnek érvénybe, amíg újra nem indítja a Microsoft Monitoring Agent szolgáltatása (HealthService.exe).
   > ### <a name="install-solution"></a>Megoldás telepítése
   > Kövesse az ismertetett folyamatot [figyelési megoldás telepítése](solutions.md#install-a-monitoring-solution) hozzáadása a **Active Directory replikációs állapota** megoldás a Log Analytics-munkaterületre. Nincs szükség további konfigurációra.


## <a name="ad-replication-status-data-collection-details"></a>AD replikációs állapot adatok gyűjtemény részletei
Az alábbi táblázatban látható adatok gyűjtési módszerek és egyéb hogyan adatgyűjtés az AD Replication Status részleteit.

| Platform | Közvetlen ügynök | SCOM-ügynök | Azure Storage | Az SCOM szükséges? | Az SCOM agent adatküldés felügyeleticsoport-n keresztül | Gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |öt naponta |



## <a name="understanding-replication-errors"></a>Replikációs hibák ismertetése

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Az AD Replication Status csempét jeleníti meg, hogy jelenleg hány replikációs hibák. **Kritikus fontosságú replikációs hibák** hibák, amelyek legalább 75 %-a [szemétgyűjtési](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) számára az Active Directory-erdőben.

![AD-replikáció állapota csempe](./media/ad-replication-status/oms-ad-replication-tile.png)

Ha a csempére kattint, megtekintheti a hibákkal kapcsolatos további információk.
![AD-replikáció állapota irányítópult](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Célkiszolgáló állapota és a forráskiszolgáló állapota
Ezek az oszlopok megjelenítése célkiszolgálókon és a replikációs hibákat tapasztaló adatforrás-kiszolgálók állapotát. Minden egyes tartományvezérlő neve után a szám azt jelzi, hogy a tartományvezérlő replikációs hibák száma.

A célkiszolgáló és a forrás kiszolgálók hibák jelennek meg, mert hibák is megkönnyíti a cél kiszolgáló szempontjából a kiszolgáló forrás szempontjából és mások hibaelhárítását.

Ebben a példában láthatja, hogy számos célkiszolgálókon rendelkezik nagyjából azonos számú hibák, de van egy forráskiszolgálóhoz (ADDC35), mint minden más számos további hibákat tartalmaz. Valószínű, hogy van-e probléma a ADDC35 okozza, hogy nem küld adatokat a replikációs partnerek számára. A problémák megoldása a ADDC35 megoldhatja a hibákat, amely a cél kiszolgáló területen megjelenik számos.

### <a name="replication-error-types"></a>Replikációs hibatípusok
Ez a terület a típusú hibákat észlelt az egész vállalat adatait jeleníti meg. Minden egyes hibához egyedi numerikus kódot és a egy üzenet, amely segít meghatározni a hiba okának rendelkezik.

A fánkdiagram felső biztosít ötlet, mely hibák jelennek meg több és kevésbé gyakran a környezetben.

Bemutatja azt, ha a több tartományvezérlő élmény ugyanaz a replikációs hiba. Ebben az esetben, előfordulhat, hogy képesek felderíteni azonosíthatja a megoldás egy tartományvezérlőn, majd ismételje meg a többi tartományvezérlőn, ugyanaz a hiba érinti.

### <a name="tombstone-lifetime"></a>Törlésjelző életciklusa
A szemétgyűjtési határozza meg, mennyi ideig törölt objektum, a törlésre kijelölt néven, az Active Directory-adatbázis addig őrzi meg. Ha egy törölt objektumot adja át a szemétgyűjtési, egy szemétgyűjtési adatgyűjtési folyamat automatikusan eltávolítja az Active Directory-adatbázis.

Az alapértelmezett szemétgyűjtési legújabb verziója, Windows, 180 nap, de a régebbi verziók 60 napig volt, és explicit módon lehet módosítani egy Active Directory-rendszergazda.

Fontos tudni, hogy ha hamarosan eléri vagy elmúlt a szemétgyűjtési replikációs hibák merültek fel. Ha két tartományvezérlő replikációs ütköztek a szemétgyűjtési korábbi továbbra is fennáll, replikáció le van tiltva ezen két tartományvezérlők között akkor is, ha az alapul szolgáló replikációs hiba kijavítása.

A szemétgyűjtési terület segítségével azonosíthatja a helyek, annak a veszélye, azonban letiltott replikáció esetén. A hibát a **több mint 100 %-os TSL** kategória olyan partíció, amely legalább a szemétgyűjtési az erdő nem replikálta az a forrás- és kiszolgáló közötti jelöli.

Ebben a helyzetben egyszerűen javítása a replikációs hiba: nem elegendő. Minimális manuálisan ellenőrizze, hogy azonosítsa és a fennmaradó objektumok replikációs újraindításához először törölni kell. Még akkor is szükség lehet egy olyan tartományvezérlő leszerelése.

Azonosítása, amely rendelkezik a szemétgyűjtési korábbi megőrzött replikálási hibákat, mellett szeretné is nagy figyelmet fordítani az esetleges hibákat tartozó a **50 – 75 %-os TSL** vagy **75 – 100 %-os TSL** kategóriák.

Ezek a hibák, amelyek egyértelműen fennmaradó, nem átmeneti, így valószínűleg a megoldásához beavatkozás szükséges. A jó hír az, hogy azok nem ért a szemétgyűjtési. Ha azonnal hárítsa el ezeket a problémákat és *előtt* elérnék a szemétgyűjtési, replikációs minimális manuális beavatkozás újraindíthatja.

Feljegyzett korábban, az irányítópulton megjelenő csempén a az AD Replication Status megoldás számát jeleníti meg *kritikus* a környezetben, amelyet a hibákat, amelyek több mint 75 %-a szemétgyűjtési (beleértve a hibákat, a replikációs hibák amelyek több mint 100 %-a TSL). Arra törekszik, hogy ez a szám 0.

> [!NOTE]
> Minden a törlésre kijelölt élettartama százalékos számítások esetében az Active Directory-erdőben, a tényleges szemétgyűjtési alapulnak, így, hogy ezeket a százalékok pontosak, ha már rendelkezik egy egyéni élettartamértékénél beállítása.
>
>

### <a name="ad-replication-status-details"></a>AD replikációs állapot részletei
Amikor egyik lista tetszőleges elemre kattint, láthatja, a napló lekérdezésekkel kapcsolatos további részletek. Az eredmények csak az adott elem kapcsolódó hibák vannak szűrve. Például, ha kattint az első tartományvezérlő alatt felsorolt **célkiszolgáló állapota (ADDC02)**, láthatja a szűrt lekérdezési eredmények megjelenítése hibák az adott tartományvezérlő-listán a célkiszolgálón:

![AD replikációs állapothibákat lekérdezés eredményei](./media/ad-replication-status/oms-ad-replication-search-details.png)

Itt további szűréséhez, módosítsa a napló lekérdezést, és így tovább. Az Azure monitorban log lekérdezésekkel kapcsolatos további információkért lásd: [naplóadatok elemzése az Azure monitorban](../../azure-monitor/log-query/log-query-overview.md).

A **HelpLink** mezőben látható URL-címét a TechNet-oldal, további részletekkel a meghatározott hiba. Másolja, és ez a hivatkozás illessze be a böngészőablakot, hibaelhárítási és a hiba kijavítása információinak megtekintéséhez.

Is **exportálása** exportálhatja az eredményeket az Excelbe. Az adatok exportálása segítségével megjelenítheti a replikációs hiba adatok milyen módon.

![exportált AD replikációs állapota hibák az Excelben](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>AD-replikáció állapota – gyakori kérdések
**K: Milyen gyakran az AD replikálás állapotadatainak frissítése?**
V: Az adatokat öt naponta frissül.

**K: Van mód konfigurálása, hogy milyen gyakran frissül, ezeket az adatokat?**
V: Jelenleg nem.

**K: Kell adja hozzá az összes saját tartományvezérlők, a Log Analytics-munkaterület ahhoz, hogy a replikációs állapot megtekintéséhez?**
V: Nem, csak egyetlen tartományvezérlő hozzá kell adni. Ha rendelkezik több tartományvezérlő a Log Analytics-munkaterülethez, ezek mindegyike adatait az Azure monitornak küld.

**K: Nem szeretnék a tartományvezérlők hozzáadni a Log Analytics-munkaterületet. Továbbra is használható az AD Replication Status megoldás?**

V: Igen. Beállíthat egy beállításkulcs megadásával engedélyezze azt értékét. Lásd: [engedélyezése nem tartományvezérlő](#enable-non-domain-controller).

**K: Mi a neve, a folyamat, amely az adatgyűjtés?**
V: AdvisorAssessment.exe

**K: Mennyi ideig tart a gyűjtendő adatokat?**
V: Adatok gyűjtése idő az Active Directory-környezet méretétől függ, de általában kisebb, mint 15 perc alatt.

**K: Milyen típusú adatokat gyűjt?**
V: LDAP-n keresztül gyűjt replikációs adatokat.

**K: Van mód a konfigurálását, amikor az adatgyűjtés történik?**
V: Jelenleg nem.

**K: Milyen engedélyekkel van szükségem adatok gyűjtésére?**
V: Az Active Directory normál felhasználói engedélyek elegendőek.

## <a name="troubleshoot-data-collection-problems"></a>Adatok gyűjtése kapcsolatos problémák elhárítása
Annak érdekében, hogy az adatok gyűjtéséhez az AD Replication Status megoldáscsomag szükséges legalább egy tartományvezérlő, a Log Analytics-munkaterületre kell csatlakoztatni. Mindaddig, amíg a tartományvezérlő csatlakozott, megjelenik egy üzenet jelzi, hogy **adatok összegyűjtése még folyamatban van**.

Csatlakozás a tartományvezérlők egyik segítségre van szüksége, ha megtekintheti a dokumentációban, [csatlakozás Windows számítógépek számára az Azure Monitor](../../azure-monitor/platform/om-agents.md). Azt is megteheti, ha a tartományvezérlő már csatlakoztatva van egy meglévő System Center Operations Manager-környezetbe, megtekintheti dokumentációját a [System Center Operations Manager csatlakoztatása az Azure monitornak](../../azure-monitor/platform/om-agents.md).

Ha nem szeretne csatlakozni a tartományvezérlők bármelyikét közvetlenül az Azure Monitor vagy a System Center Operations Manager, [engedélyezése nem tartományvezérlő](#enable-non-domain-controller).

## <a name="next-steps"></a>További lépések
* Használat [lekérdezések jelentkezzen be az Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) Active Directory-replikáció állapota részletes adatainak megtekintéséhez.
