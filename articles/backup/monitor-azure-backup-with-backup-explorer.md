---
title: A biztonsági mentések figyelése a Biztonsági másolat kezelővel
description: Ez a cikk azt ismerteti, hogy a Biztonsági másolat intézővel valós idejű biztonsági mentéseket végezhet a tárolók, előfizetések, régiók és bérlők közötti biztonsági mentések végrehajtásához.
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: fa30a061dfe0d9f7721bd2405280f8a01bea87fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131806"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>A biztonsági mentések figyelése a Biztonsági másolat kezelővel

Ahogy a szervezetek egyre több gépről készít biztonsági mentést a felhőbe, egyre fontosabbá válik a biztonsági mentések hatékony figyelése. A legjobb módja annak, hogy kezdődik, hogy egy központi helyen megtekintésére működési információkat egy nagy birtokon.

A Backup Explorer egy beépített Azure Monitor-munkafüzet, amely az Azure Backup-ügyfelek számára egyetlen központi helyet biztosít. A Biztonságimásolat-kezelő segítségével figyelheti a működési tevékenységeket az Azure teljes biztonsági mentési területén, a bérlők, a helyek, az előfizetések, az erőforráscsoportok és a tárolók között. A Biztonsági másolat kezelője általánosságban a következő lehetőségeket nyújtja:

* **Méretarányos perspektíva:** Összesített nézetet kaphat a biztonsági mentési elemekről, feladatokról, riasztásokról, szabályzatokról és erőforrásokról, amelyek még nincsenek konfigurálva biztonsági mentésre a teljes területen. 
* **Részletezési elemzés:** Részletes információkat jeleníthet meg az egyes feladatokról, riasztásokról, házirendekről és biztonsági mentési elemekről, mindezt egy helyen.
* **Végrehajtható felületek:** Miután azonosította a problémát, megoldhatja azt a megfelelő biztonsági mentési elemhez vagy Azure-erőforráshoz való zökkenőmentes megtérésével.

Ezeket a képességeket az Azure Resource Graph és az Azure Monitor munkafüzetekkel való natív integráció biztosítja.

> [!NOTE]
> * A Biztonsági másolat kezelője jelenleg csak az Azure virtuális gépek (VM-ek) adataihoz érhető el.
> * A Biztonsági másolat kezelője az elmúlt 7 nap biztonsági másolatainak megtekintésére szolgáló operatív irányítópult (legfeljebb).
> * A Biztonsági másolat kezelője jelenleg nem támogatott a nemzeti felhőkben.
> * A Biztonsági másolat készítő sablon testreszabása jelenleg nem támogatott. 
> * Nem javasoljuk, hogy egyéni automatizálásokat írjon az Azure Resource Graph-adatokra.

## <a name="get-started"></a>Bevezetés

A Biztonsági másolat kezelőjét úgy érheti el, hogy bármelyik Recovery Services-tárolóba kerül, és az **Áttekintő** ablaktáblában kiválasztja a **Biztonsági másolat készítőhivatkozást.**

![A trezor gyorskapcsolata](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

A hivatkozás kiválasztása megnyitja a Biztonsági másolat kezelőjét, amely összesített nézetet biztosít az összes olyan tárolóban és előfizetésben, amelyhez hozzáféréssel rendelkezik. Ha Egy Azure Lighthouse-fiókot használ, megtekintheti az adatokat az összes bérlő, amely rendelkezik hozzáféréssel. További információkért tekintse meg a "Bérlőközi nézetek" című szakaszban a cikk végén.

![A Biztonsági másolat kezelőjének kezdőlapja](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>A Biztonságimásolat-kezelő használati esetei

A Biztonságimásolat-kezelő több lapot jelenít meg, amelyek mindegyike részletes információkat tartalmaz egy adott biztonsági másolat összetevőről (például egy biztonsági másolatról, feladatról vagy házirendről). Ez a rész rövid áttekintést nyújt az egyes lapokról. A videók minden egyes biztonsági másolat összetevőhöz mintahasználati eseteket biztosítanak, valamint a rendelkezésre álló vezérlők leírását.

### <a name="the-summary-tab"></a>Az Összegzés lap

Az **Összegzés** lap gyors áttekintést nyújt a biztonsági mentési állapot általános állapotáról. Megtekintheti például a védett elemek számát, azon elemek számát, amelyeknél a védelem nem volt engedélyezve, vagy hogy hány feladat volt sikeres az elmúlt 24 órában.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="the-backup-items-tab"></a>A Biztonsági másolat elemei lap

Szűrheti és megtekintheti az egyes biztonsági mentési elemeket előfizetés, tároló és egyéb jellemzők szerint. Egy biztonsági másolat elem nevének kiválasztásával megnyithatja az adott elem Azure-ablaktábláját. A táblából például megfigyelheti, hogy az utolsó biztonsági mentés nem sikerült az *X*elemhez. Az *X*lehetőség kiválasztásával megnyithatja az elem **Biztonsági másolat** ablaktábláját, ahol igény szerinti biztonsági mentési műveletet indíthat el.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="the-jobs-tab"></a>A Feladatok lap

Válassza a **Feladatok** lapot az elmúlt 7 napban indított összes feladat részleteinek megtekintéséhez. Itt a Feladat *művelet*, *A feladat állapota*és a *Hibakód* (sikertelen feladatok esetén) szerint szűrhet.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="the-alerts-tab"></a>A Riasztások lap

Válassza a **Riasztások** lapot az elmúlt 7 nap ban a tárolókon létrehozott összes riasztás részleteinek megtekintéséhez. A riasztásokat típus *(Biztonsági mentési hiba* vagy *visszaállítási hiba),* aktuális állapot (*aktív* vagy *megoldott*) és súlyosság *(Kritikus*, *Figyelmeztetés*vagy *Információ*) szerint szűrheti. Kiválaszthat egy hivatkozást az Azure virtuális géphez, és meghajthatja a szükséges lépéseket.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="the-policies-tab"></a>A Házirendek lap

A **Házirendek** lapon megtekintheti a biztonsági mentési állapotban létrehozott biztonsági mentési szabályzatok legfontosabb adatait. Megtekintheti az egyes házirendekhez társított elemek számát, valamint a házirend által megadott megőrzési tartományt és biztonsági mentési gyakoriságot.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="the-backup-not-enabled-tab"></a>A Biztonsági mentés nincs engedélyezve lap

A biztonsági mentést minden védelemre szoruló géphez engedélyezni kell. A Biztonsági másolat kezelőjével a biztonsági mentés tanfelügyelői gyorsan azonosíthatják, hogy a szervezet mely gépeit nem védi még biztonsági másolat. Az információk megtekintéséhez válassza a **Biztonsági mentés nincs engedélyezve** lapot.

A **Biztonsági mentés nincs engedélyezve** ablaktábla a nem védett gépek listáját tartalmazó táblázatot jeleníti meg. A szervezet különböző címkéket rendelhet éles gépekhez és tesztgépekhez, illetve különböző funkciókat kiszolgáló gépekhez. Mivel a gépek minden osztályának külön biztonsági mentési házirendre van szüksége, a címkék szerinti szűrés segít az egyes adatok megtekintésében. Bármely számítógép nevének kiválasztása átirányítja a számítógép **Biztonsági másolat konfigurálása** ablaktáblára, ahol kiválaszthatja a megfelelő biztonsági mentési házirend alkalmazását.


> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="export-to-excel"></a>Exportálás Excelbe

Bármely táblázat vagy diagram tartalmát Excel-számolótáblaként exportálhatja. A tartalom exportálása a jelenlegi állapotában történik, a meglévő szűrők alkalmazásával. További táblázatsorok exportálásához növelheti az oldalon megjelenítendő sorok számát az egyes lapok tetején található **Lap/lap** legördülő lista segítségével.

## <a name="pin-to-the-dashboard"></a>Rögzítés az irányítópulton

Az egyes táblázatok vagy diagramok tetején a "pin" ikont választhatja, hogy rögzítse az Azure Portal irányítópultjára. Ezen információk rögzítésével személyre szabott irányítópultot hozhat létre, amely az Ön számára legfontosabb információk megjelenítésére lett kialakítva.

## <a name="cross-tenant-views"></a>Több-bérlős nézetek

Ha Ön Azure Lighthouse-felhasználó, aki delegált hozzáféréssel rendelkezik az előfizetésekhez több bérlői környezetben, használhatja az alapértelmezett előfizetési szűrőt. Az Azure Portal jobb felső részén található "szűrő" ikonkiválasztásával megjeleníti azokat az előfizetéseket, amelyek adatait meg szeretné tekinteni. Ha ezt a funkciót használja, a Biztonsági másolat készítő összesíti a kiválasztott előfizetések összes tárolójának adatait. További információ: [Mi az Azure világítótorony?](https://docs.microsoft.com/azure/lighthouse/overview)

## <a name="next-steps"></a>További lépések

[Megtudhatja, hogyan használhatja az Azure Monitort a biztonsági mentési adatokkal kapcsolatos elemzések megszerzéséhez](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)
