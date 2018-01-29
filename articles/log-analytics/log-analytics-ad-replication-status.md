---
title: "Az Active Directory replikációs állapotát az Azure Naplóelemzés figyelése |} Microsoft Docs"
description: "Az Active Directory replikációs állapotát megoldáscsomag rendszeresen figyeli az Active Directory-környezet minden replikációs hibák."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 1b988972-8e01-4f83-a7f4-87f62778f91d
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7ca3b87ea14589aa2c45c8fe49b01d3b10a75aa1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2018
---
# <a name="monitor-active-directory-replication-status-with-log-analytics"></a>A Naplóelemzési Active Directory replikációs állapot figyelése

![AD-replikáció állapotát szimbólum](./media/log-analytics-ad-replication-status/ad-replication-status-symbol.png)

Az Active Directory az informatikai környezetben vállalati nyilvános kulcsokra épülő. Magas rendelkezésre állás és a magas teljesítmény biztosítása érdekében minden egyes tartományvezérlő rendelkezik saját az Active Directory-adatbázis másolatát. Ahhoz, hogy a módosítások átvitele a vállalaton belül a tartományvezérlő replikálja egymás mellett. A replikálási folyamat-hibák hatására problémák számos a vállalaton belül.

Az AD-replikáció állapotát megoldáscsomag rendszeresen figyeli az Active Directory-környezet a replikációs hibákat.

## <a name="installing-and-configuring-the-solution"></a>Telepítése és a megoldás konfigurálása
Az alábbi információk segítségével telepítse és konfigurálja a megoldást.

* Telepítenie kell ügynökök olyan tartományvezérlőn, amely a tartomány tagjaként kell kiértékelni. Vagy telepíthet ügynököket a kiszolgálókon és az ügynökök AD replikációs adatokat küldeni a Naplóelemzési konfigurálnia kell. Windows rendszerű számítógépek csatlakoztatása a Log Analyticshez ismertetése: [Log Analyticshez való csatlakozás Windows számítógépek](log-analytics-windows-agent.md). Ha a tartományvezérlő már tartozik egy meglévő System Center Operations Manager-környezet, amelyet szeretne csatlakozni a szolgáltatáshoz, lásd: [csatlakozás az Operations Manager szolgáltatáshoz](log-analytics-om-agents.md).
* Az Active Directory replikációs állapotát megoldás hozzáadni a Naplóelemzési munkaterület ismertetett eljárással [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md).  Nincs szükség további konfigurációra.

## <a name="ad-replication-status-data-collection-details"></a>AD replikációs állapot adatok gyűjtemény részletei
A következő táblázat adatgyűjtési módszerek és egyéb adatok gyűjtése hogyan AD replikációs állapot részleteit.

| Platform | Közvetlen ügynök | SCOM-ügynököt | Azure Storage | SCOM szükséges? | Felügyeleti csoport keresztül küldött SCOM ügynök adatok | Gyűjtemény gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |öt naponta |

## <a name="optionally-enable-a-non-domain-controller-to-send-ad-data-to-log-analytics"></a>Másik lehetőségként a nem-tartományvezérlő AD adatokat küldeni a Naplóelemzési engedélyezése
Ha nem szeretné, a tartományvezérlőket bármelyikét közvetlen elérésére Naplóelemzési, használhatja lévő többi számítógép abban a tartományban, Log Analyticshez kapcsolódik az AD-replikáció állapotát megoldáscsomag adatokat gyűjthessen és küldheti az adatokat.

### <a name="to-enable-a-non-domain-controller-to-send-ad-data-to-log-analytics"></a>Egy nem tartományvezérlő AD adatokat küldeni a Naplóelemzési engedélyezése
1. Győződjön meg arról, hogy a számítógép a figyelheti a replikációs állapot AD megoldást használni kívánt tartomány tagja.
2. [A Windows számítógép csatlakoztatása a Log Analyticshez](log-analytics-windows-agent.md) vagy [csatlakoztassa a meglévő Operations Manager-környezetet a Naplóelemzési használatával](log-analytics-om-agents.md), ha az nincs csatlakoztatva.
3. Az adott számítógépen állítsa be a következő beállításkulcsot:

   * Kulcs: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management csoportok\<ManagementGroupName > \Solutions\ADReplication**
   * Érték: **IsTarget**
   * Érték: **igaz**

   > [!NOTE]
   > Ezek a változások életbe csak az újraindítás a Microsoft Monitoring Agent szolgáltatása (HealthService.exe).
   >
   >

## <a name="understanding-replication-errors"></a>Replikációs hibák ismertetése
Miután szolgáltatáshoz küldött AD állapot adatokat, egy hasonló, a következő kép jelenleg hány replikálási hibákat jelző Naplóelemzési a csempe láthatja.  
![AD-replikáció állapota csempe](./media/log-analytics-ad-replication-status/oms-ad-replication-tile.png)

**Kritikus fontosságú replikációs hibák** , amelyek vagy annál újabb 75 %-a hibák a [szemétgyűjtési](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) az Active Directory-erdőben.

A csempére kattintva megtekintheti a hibákkal kapcsolatos további információkat.
![AD-replikáció állapotát irányítópult](./media/log-analytics-ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Cél kiszolgálójának állapotát és a forrás-kiszolgáló állapota
Ezekben az oszlopokban a célkiszolgáló és adatforrás-kiszolgálók, amelyeken a replikációs hibák állapotának megjelenítése. Az a szám, miután minden tartományvezérlő neve jelzi az adott tartományvezérlőn replikációs hibák számát.

A célkiszolgáló és a forráskiszolgálókra hibák jelennek meg, mert hibák is megkönnyíti a célként megadott kiszolgáló szempontjából az a forrás kiszolgálón szempontjából, illetve más hibaelhárítását.

Ebben a példában láthatja, hogy sok célkiszolgálókon rendelkezik nagyjából hibák ugyanannyi, de egy forráskiszolgálóhoz (ADDC35), mint a többi sok további hiba van. Valószínű, hogy van-e probléma a ADDC35 okozza, hogy nem sikerült adatokat küldeni a replikációs partnerek számára. ADDC35 kijavította a problémák megoldására jelentős része, amely a célként megadott kiszolgáló területen megjelenik a hibákat.

### <a name="replication-error-types"></a>Replikációs hiba típusa
Ez a terület a hibák észlelhetők a vállalaton belüli típusú tájékoztatást nyújt. Minden egyes hibához rendelkezik egy numerikus kód és egy üzenet, amelyik segíthet a hiba okának meghatározásához.

A felső fánk lehetővé teszi egy ötletet, mely hibák jelennek meg több és ritkábban a környezetben.

Azt mutatja, ha több tartományvezérlő élmény ugyanaz a replikációs hiba. Ebben az esetben akkor előfordulhat, hogy képes azonosítani a megoldás egyik tartományvezérlőn kell, majd ismételje meg azt a többi tartományvezérlőn ugyanaz a hiba érinti.

### <a name="tombstone-lifetime"></a>Szemétgyűjtési
A szemétgyűjtési határozza meg, mennyi ideig törölt objektum, a törlésre kijelölt nevezik, addig maradnak az Active Directory-adatbázisában. Ha egy törölt objektumot adja át a szemétgyűjtési, a szemétgyűjtő folyamatának automatikusan eltávolítja az Active Directory-adatbázis.

Az alapértelmezett szemétgyűjtési 180 nappal a Windows, a legújabb verzióját, de régebbi verzióin 60 nap volt, és egy Active Directory-rendszergazda explicit módon módosítható.

Fontos tudni, hogy ha tapasztal-e replikációs hibák vannak eléri vagy a szemétgyűjtési részhez. Ha két tartományvezérlő egy replikációs hiba, amely a szemétgyűjtési túli továbbra is fennáll, replikáció le van tiltva a két tartományvezérlők közötti akkor is, ha az alapul szolgáló replikációs hiba rögzített.

A szemétgyűjtési terület könnyebb legyen azonosítani annak a veszélye, azonban letiltott replikáció esetén helyek. A hibákat a **több mint 100 %-os TSL** kategória jelöl, amely nem replikálása a forrás és cél kiszolgáló közötti legalább a szemétgyűjtési az erdőhöz.

Ebben a helyzetben a replikációs hiba egyszerűen kijavítása nem lesz elég. Legalább manuálisan ellenőrizze, hogy azonosítása és a fennmaradó objektumok replikációs újraindításához először törölni kell. Akkor is szükség lehet egy tartományvezérlő leszerelése.

Azonosító a múltbeli a szemétgyűjtési fennállásának replikációs hibákat, mellett szeretné nagy figyelmet fordítani az esetleges hibákat tartozó a **50-75 % TSL** vagy **75-100 %-os TSL** kategóriák.

Ezek a hibák, amelyek egyértelműen fennmaradó, nem átmeneti, ezért valószínűleg kell feloldani a beavatkozást. Jó hírünk, hogy azok nem ért a szemétgyűjtési. Ha azonnal a problémák kijavításához és *előtt* érik a szemétgyűjtési, replikációs minimális kézi beavatkozás indíthatja újra.

Amint korábban, az irányítópult-csempe a az AD-replikáció állapotát a megoldás bemutatja száma *kritikus* replikációs hibák, a környezetben, amelyet hibaként, amelyek több mint 75 %-a szemétgyűjtési (beleértve a hibák a rendszer több mint 100 %-a TSL). Ez a szám 0 megőrizni szükség.

> [!NOTE]
> Minden a törlésre kijelölt élettartama százalékos számítás úgy, hogy ezeket a szolgáltatásajánlatokat pontosak, még akkor is, ha egy egyéni törlésre való kijelölés élettartamértékénél, állítsa be még megbízik az Active Directory erdő, a tényleges szemétgyűjtési alapulnak.
>
>

### <a name="ad-replication-status-details"></a>AD replikációs állapotának részletei
Ha egyik listán valamely elemére kattint, megjelenik az naplófájl-keresési kapcsolatos további részletek. Az eredmények csak az, hogy az elem kapcsolatos hibák vannak szűrve. Például, ha rákattint az első tartományvezérlő alatt megjelenik **cél kiszolgáló állapota (ADDC02)**, a szűrt keresési eredmények között megjelenik a célkiszolgálónak felsorolt tartományvezérlő hibák megjelenítése:

![Replikációs állapothibákat AD a találatok között](./media/log-analytics-ad-replication-status/oms-ad-replication-search-details.png)

Itt további szűréséhez, módosítsa a keresési lekérdezést, és így tovább. A naplófájl-keresési kapcsolatos további információkért lásd: [keresések jelentkezzen](log-analytics-log-searches.md).

A **HelpLink** mezőben látható, hogy adott hibával kapcsolatban további részletekkel a TechNet-oldal URL-CÍMÉT. Másolja, és ez a hivatkozás illessze be a böngészőablakot, hibaelhárítási és a hiba kijavítása kapcsolatos információk megjelenítéséhez.

Is **exportálása** az eredmények exportálása az Excelbe. Az adatok exportálása segítségével megjelenítheti a replikációs hiba adatok milyen módon.

![exportált AD replikációs állapothibákat az Excel programban](./media/log-analytics-ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>AD-replikáció állapotát – gyakori kérdések
**K: milyen gyakran az AD replikációs állapot adatok frissítése?**
V: az adatokat öt naponta frissül.

**K: van mód konfigurálása, hogy milyen gyakran frissül, ezeket az adatokat?**
Jelenleg nem A:.

**K: kell ahhoz, hogy a replikációs állapot a Naplóelemzési munkaterület hozzáadása összes a tartományvezérlő?**
V: nem, csak egy tartományvezérlő hozzá kell adni. Ha több tartományvezérlő van a Naplóelemzési munkaterület, ezek az adatok Naplóelemzési érkezik.

**K: nem szeretnék minden olyan tartományvezérlőn hozzáadni a Naplóelemzési munkaterület. Továbbra is használható az AD-replikáció állapotát megoldás?**
V: Igen. Engedélyezheti a beállításkulcs értékét állíthatja be. Lásd: [ahhoz, hogy egy nem tartományvezérlő AD adatokat küldeni a Naplóelemzési](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

**K: Mi az a folyamat, amelyet az adatgyűjtést a neve?**
A: AdvisorAssessment.exe

**K: mennyi ideig tart a gyűjtött adatok?**
V: gyűjtemény ideje az Active Directory-környezet méretétől függ, de általában a kisebb, mint 15 percet vesz igénybe.

**K: milyen típusú adatokat gyűjt a rendszer?**
V: replikációs adatok összegyűjtése LDAP keresztül történik.

**K: van konfigurálni az adatgyűjtés mód?**
Jelenleg nem A:.

**K: milyen engedélyekkel kell adatok gyűjtéséért felelős ügyfélfeladatot?**
A: az Active Directory normál felhasználói engedélyek megfelelőek.

## <a name="troubleshoot-data-collection-problems"></a>Adatok gyűjtése kapcsolatos problémák elhárítása
Ahhoz, hogy az adatok gyűjtéséhez az AD-replikáció állapotát megoldáscsomag kell csatlakoztatni a Naplóelemzési munkaterület legalább egy tartományvezérlő van szükség. Csatlakozzon egy olyan tartományvezérlőre, amíg megjelenik egy üzenet azt jelzi, hogy **továbbra is gyűjtenek adatokat**.

Csatlakozás egy tartományvezérlő segítségre van szüksége, ha címen tekintheti [Log Analyticshez való csatlakozás Windows számítógépek](log-analytics-windows-agent.md). Azt is megteheti, ha a tartományvezérlő már csatlakoztatva van egy meglévő System Center Operations Manager-környezetbe, megtekintheti címen [csatlakozás a System Center Operations Manager szolgáltatáshoz](log-analytics-om-agents.md).

Ha nem szeretne összekapcsolni, a tartományvezérlőket bármelyikét közvetlenül Naplóelemzési vagy System Center Operations Manager, lásd: [ahhoz, hogy egy nem tartományvezérlő AD adatokat küldeni a Naplóelemzési](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

## <a name="next-steps"></a>További lépések
* Használjon [Log Analytics-e jelentkezni a keresések](log-analytics-log-searches.md) Active Directory replikációs állapot részletes adatainak megtekintéséhez.
