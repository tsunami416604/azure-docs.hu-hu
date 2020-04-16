---
title: Az Azure Synapse Studio hibáinak elhárítása (előzetes verzió)
description: Az Azure Synapse Studio hibáinak elhárítása
author: julieMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ede38317762e5aac5562c9bb3494713c3edacc69
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431318"
---
# <a name="azure-synapse-studio-preview-troubleshooting"></a>Az Azure Synapse Studio (előzetes verzió) hibaelhárítása

Ez a hibaelhárítási útmutató arról nyújt tájékoztatást, hogy milyen információkat kell megadni a hálózati kapcsolódási problémákra vonatkozó támogatási jegy megnyitásakor. A megfelelő információkkal gyorsabban megoldhatjuk a problémát.

## <a name="sql-on-demand-preview-service-connectivity-issue"></a>SQL igény szerinti (előzetes verziójú) szolgáltatás kapcsolódási problémája

### <a name="symptom-1"></a>1. tünet

Az "SQL on-demand" beállítás szürkén jelenik meg a "Csatlakozás" legördülő menüben.

![tünet1](media/troubleshooting-synapse-studio/symptom1v2.png)

### <a name="symptom-2"></a>2. tünet

Ha a lekérdezést "SQL on-demand" állapotban futtatja, a "Nem sikerült kapcsolatot létesíteni a kiszolgálóval" hibaüzenet jelenik meg.

![tünet2](media/troubleshooting-synapse-studio/symptom2.png)

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

> [!NOTE] 
>    A következő hibaelhárítási lépések a Chromium Edge és a Chrome esetében találhatók. Használhat más böngészőket (például a FireFox-ot) ugyanazzal a hibaelhárítási lépésekkel, de a "Fejlesztői eszköz" ablak elrendezése eltérhet a TSG képernyőképeitől. Ha lehetséges, NE használja a klasszikus Edge-et a hibaelhárításhoz, mert bizonyos helyzetekben pontatlan információkat mutathat.

Nyissa meg a "Diagnosztikai információ" panelt, és válassza a "Diagnosztika letöltése" gombot. A hibajelentéshez letöltött információk megtartása. Ehelyett másolja a "Session ID"-t, és csatolja a támogatási jegy megnyitásakor.

![diagnosztikai-info](media/troubleshooting-synapse-studio/diagnostic-info-download.png)

A hibaelhárítás megkezdéséhez próbálkozzon újra az Azure Synapse Studio-ban végrehajtott művelettel.

- Az 1- es tünet nél válassza az "Adatbázis használata" legördülő menü "Adatbázis használata" legördülő menüjének jobb oldalán található "Frissítés" gombot az "SQL script" lapon, és ellenőrizze, hogy látható-e az "SQL on-demand".
- A 2-es tünet esetén próbálja meg újra futtatni a lekérdezést, és nézze meg, hogy sikeresen végrehajtja-e.

Ha a probléma továbbra is fennáll, nyomja le az F12 billentyűt a böngészőben a "Fejlesztői eszközök" (Fejlesztői eszközök) megnyitásához.

A "Fejlesztői eszközök" ablakban váltson a "Hálózat" panelre. Szükség esetén válassza a "Hálózat" panel eszköztárának "Törlés" gombját.
Győződjön meg arról, hogy a "Hálózat" panelen a "Gyorsítótár letiltása" jelölőnégyzet be van jelölve.

Próbálkozzon újra az Azure Synapse Studio-ban végrehajtott művelettel. Az új elemek a "Fejlesztői eszközök" "Hálózat" listájában jelenhetnek meg. Jegyezze fel a jelenlegi rendszeridőt, hogy a támogatási jegyben megadhassa.

![hálózati panel](media/troubleshooting-synapse-studio/network-panel.png)

Keresse meg azt az elemet, amelynek URL-oszlopa megegyezik a következő mintával:

`https://[*A*]-ondemand.database.windows.net:1443/databases/[*B*]/query?api-version=2018-08-01-preview&application=ArcadiaSqlOnDemandExplorer`

Ahol *[A]* a munkaterület neve, és a "-ondemand" lehet "-sqlod", és ahol *[B]* lehet adatbázisnév, például "master". Legtöbb esetben legtöbb elem nek kell lennie, amelyek URL-értéke azonos, de eltérő metódusértékek; OPCIÓK és postai úton. Ellenőrizze, hogy ez a két elem "200" vagy "20x" betűvel rendelkezik-e az állapotoszlop alatt, ahol az "x" lehet egy számjegy.

Ha bármelyikük nek van valami más, mint a "20x", és:

- az állapot a "(failed)" szöveggel kezdődik, vagy szélesíti az "Állapot" oszlopot, vagy vigye az egérmutatót az állapotszöveg fölé a teljes szöveg megtekintéséhez. Adja meg a szöveget és/vagy a képernyőképet a támogatási jegy megnyitásakor.

    ![állapot-szöveg](media/troubleshooting-synapse-studio/status-text.png)

    - Ha látja ERR_NAME_NOT_RESOLVED, és 10 percen belül létrehozta a munkaterületet, várjon 10 percet, majd próbálja meg újra látni, hogy a probléma továbbra is fennáll-e.
    - Ha ERR_INTERNET_DISCONNECTED vagy ERR_NETWORK_CHANGED lát, az azt jelezheti, hogy a számítógép hálózati kapcsolata problémákat jelez. Ellenőrizze a hálózati kapcsolatot, majd próbálkozzon újra a művelettel.
    - Ha ERR_CONNECTION_RESET, ERR_SSL_PROTOCOL_ERROR vagy más " SSL"-t tartalmazó hibakódot lát, az azt jelezheti, hogy a helyi SSL-konfiguráció problémákat okoz, vagy a hálózati rendszergazda letiltotta az SQL igény szerinti kiszolgálóhoz való hozzáférést. Nyisson meg egy támogatási jegyet, és csatolja a hibakódot a leíráshoz.
    - Ha ERR_NETWORK_ACCESS_DENIED lát, előfordulhat, hogy a rendszergazdával kell megbeszélnie, hogy a helyi tűzfalházirend letiltotta-e a hozzáférést a *.database.windows.net tartományhoz vagy az 1443-as távoli porthoz.
    - Szükség esetén próbálja meg azonnal ugyanazt a műveletet egy másik gépen és/vagy hálózati környezetben, hogy kizárja a hálózati konfigurációs problémát a számítógépen.

- állapot "40x", "50x", vagy más számok, válassza ki a tétel (ek) a részletek megtekintéséhez. Az elem részleteinek jobbra kell látnia. Keresse meg a "Válaszfejléc" szakaszt; ezután ellenőrizze, hogy létezik-e "hozzáférés-vezérlés-engedélyező-eredete" nevű elem. Ha igen, ellenőrizze, hogy rendelkezik-e az alábbi értékek egyikével:

    - `*`(egycsillag)
    - https://web.azuresynapse.net/(vagy más érték, amellyel a böngésző címsorában lévő szöveg kezdődik)

Ha a válaszfejléc a fenti értékek egyikét tartalmazza, az azt jelenti, hogy már össze kellett volna gyűjtenünk a hibaadatokat. Szükség esetén megnyithat egy támogatási jegyet, és szükség esetén csatolhatja az elem részleteiről készült képernyőképet.

Ha nem látja a fejlécet, vagy a fejlécnem rendelkezik a fent felsorolt értékek egyikével, csatoljon egy képernyőképet az elem részleteiről a jegy megnyitásakor.

![cikk részletei](media/troubleshooting-synapse-studio/item-details.png)

Ha a fenti lépések nem oldják meg a problémát, előfordulhat, hogy meg kell nyitnia egy támogatási jegyet. A támogatási jegy elküldésekor adja meg az útmutató elején letöltött "Munkamenet-azonosító" vagy "Diagnosztikai információ" kifejezést.

A probléma bejelentésekor tetszés szerint képernyőképet készíthet a "Fejlesztői eszközök" lapon található "Konzol" fülről, és csatolhatja a támogatási jegyhez. Görgessen a tartalommal, és szükség esetén több képernyőképet is készítsen a teljes üzenet rögzítéséhez.

![fejlesztő-eszközkonzol](media/troubleshooting-synapse-studio/developer-tool-console.png)

Ha screenshotokat csatol, adja meg a képernyőképek elkészítésének idejét (vagy becsült időtartományát). Segíteni fog nekünk, ha megvizsgáljuk a problémát.

Egyes böngészők támogatják az időbélyegek megjelenítését a "Konzol" lapon. Krómszegély/Chrome esetén nyissa meg a "Beállítások" párbeszédpanelt a "Fejlesztői eszközök" párbeszédpanelen, és jelölje be az "Időbélyegek megjelenítése" jelölőnégyzetet a "Beállítások" lapon.

![fejlesztő-eszköz-konzol-beállítások](media/troubleshooting-synapse-studio/developer-tool-console-settings.png)

![show-idő-bélyegző](media/troubleshooting-synapse-studio/show-time-stamp.png)

## <a name="next-steps"></a>További lépések
Ha az előző lépések nem segítenek megoldani a támogatási [jegy létrehozása](../../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
