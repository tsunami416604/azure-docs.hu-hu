---
title: Biztonsági másolatok figyelése a Backup Explorerrel
description: Egy cikk leírja, hogyan használható a Backup Explorer a biztonsági másolatok valós idejű figyelésére a tárolók, előfizetések, régiók és bérlők között
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 331d8aeeb828dedb6700a94fafa074c179bef7ab
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992182"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Biztonsági másolatok figyelése a Backup Explorerrel

Mivel a szervezetek egyre több gépet készítenek a felhőbe, fontos, hogy egy központi helyen tekintsék meg a biztonsági másolatok működésével kapcsolatos információkat a nagy mennyiségű, a biztonsági mentések hatékony figyelése érdekében.

A Backup Explorer munkafüzet egy beépített Azure Monitor-munkafüzet, amely lehetővé teszi, hogy a biztonsági mentést végző ügyfelek egyetlen ablaktáblával rendelkezzenek az Azure-ban a teljes biztonsági mentéshez kapcsolódó operatív figyelési tevékenységek végrehajtásához (a bérlők, a helyszínek és a előfizetések, erőforráscsoportok és tárolók), mindezt egy központi helyen. Tágabb értelemben a következő képességeket biztosítja:

* **Méretarányos perspektíva** – a biztonsági mentési elemek, a feladatok, a riasztások, a házirendek és az erőforrások összesített nézete, amelyeket a rendszer a teljes biztonsági mentéshez nem konfigurált a biztonsági mentéshez. 
* Részletes **elemzés** – az egyes entitások – a feladatok, a riasztások, a szabályzatok és a biztonsági másolati elemek – részletes információit is megtekintheti egyetlen helyről.
* Végrehajtható **felületek** – a probléma azonosítása után elvégezheti a műveletek végrehajtását úgy, hogy zökkenőmentesen navigál a biztonsági mentési elemhez vagy az Azure-erőforráshoz.

A fenti funkciókat az Azure Resource Graph és a Azure Monitor munkafüzetek natív integrációja biztosítja.

> [!NOTE]
> * A Backup Explorer jelenleg csak az Azure-beli virtuálisgép-szolgáltatásokhoz érhető el.
> * A Backup Explorer olyan operatív irányítópult, amely az elmúlt 7 napban (maximum) megtekintheti a biztonsági másolatokkal kapcsolatos információkat.
> * Jelenleg a Backup Explorer sablonjának testreszabása nem támogatott. Emellett jelenleg nem ajánlott egyéni automatizálásokat írni az Azure Resource Graph-adatszolgáltatásban.

## <a name="getting-started"></a>Első lépések

A Backup Explorer eléréséhez navigáljon bármelyik Recovery Services-tárolóhoz, és kattintson a **Backup Explorer** hivatkozásra az **Áttekintés** oldalon.

![Tároló gyors hivatkozása](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

A hivatkozásra kattintva megnyílik a Backup Explorer, amely összesített nézetet biztosít az összes olyan tárolóhoz és előfizetéshez, amelyhez hozzáféréssel rendelkezik. Ha Azure Lighthouse-fiókot használ, megtekintheti az összes olyan bérlőre vonatkozó információt, amelyhez hozzáfér (lásd a továbbiak című szakaszt a több-bérlős nézetekben).

![Explorer kezdőlapja](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>A Backup Explorer használati esetei

A Backup Explorer több lapból áll, amelyek mindegyike részletes információkat biztosít a biztonsági mentési összetevőkről, például a biztonsági másolati elemekről, a feladatokról, a szabályzatokról stb. Ez a szakasz egy rövid áttekintést nyújt az egyes lapokról. A videók minta használati eseteket biztosítanak az egyes lapokhoz, valamint a felhasználóhoz elérhető különböző vezérlőelemek leírását.

### <a name="summary"></a>Összefoglalás

Az összefoglalás lapon gyors áttekintést készíthet a Backup-hagyaték általános feltételéről. Megtekintheti az olyan információkat, mint a védett elemek száma, az elemek száma, amelyekhez nincs engedélyezve a védelem, hány feladat sikeres volt az elmúlt 24 órában, és így tovább. 

A többi lap külön entitást képvisel – például: biztonsági másolati elemek, biztonsági mentési feladatok, biztonsági mentési riasztások és biztonsági mentési szabályzatok. Megtekintheti azon gépek adatait is, amelyekhez nincs konfigurálva a biztonsági mentés. Ezen fülek bármelyikére kattintva az adott entitásra jellemző információk jelennek meg.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="backup-items"></a>Biztonsági másolati elemek

Megtekintheti az előfizetéssel, a tárolóval és egyéb paraméterekkel szűrt biztonsági mentési elemeket. A biztonsági másolati elem nevére kattintva megnyithatja az adott biztonsági másolathoz tartozó Azure-panelt. A táblából például megfigyelheti, hogy az utolsó biztonsági mentés sikertelen volt a (z) X elemnél. Ha az X gombra kattint, megnyílik az elemhez tartozó biztonsági mentési panel, amelyen igény szerinti biztonsági mentési művelet aktiválható.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="jobs"></a>Feladatok

Az elmúlt hét napban indított összes feladat részleteit a feladatok lapra lépve tekintheti meg. Itt szűrheti a feladat művelet, a feladat állapota és a hibakód alapján (sikertelen feladatok esetén).

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="alerts"></a>Értesítések

Az elmúlt hét napban elindított összes riasztás részleteit megtekintheti a riasztások lapra kattintva. Itt szűrheti a rekordokat a riasztás típusa szerint (például: biztonsági mentési hiba, visszaállítási hiba), a riasztás aktuális állapota (például aktív vagy megoldott) és a riasztás súlyossága (például kritikus, figyelmeztetés, információ). Az Azure-beli virtuális géphez a virtuális gépre mutató hivatkozásra kattintva is megnyithatja a szükséges lépéseket.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="policies"></a>Irányelvek

A szabályzatok lapra kattintva megtekintheti az összes biztonsági mentési szabályzatot, amelyet a Backup-hagyatékban hozott létre. Megtekintheti, hogy hány elem van társítva az egyes szabályzatokhoz, valamint a megőrzési időtartamot és a biztonsági mentés gyakoriságát, amelyet az egyes szabályzatok határoznak meg.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="backup-not-enabled"></a>A biztonsági mentés nincs engedélyezve

Fontos, hogy a szervezet biztonsági mentési rendszergazdája gyorsan azonosítsa, hogy a szervezet mely gépei még nem rendelkeznek biztonsági mentéssel, hogy a biztonsági mentés engedélyezve legyen minden olyan gépen, amely védelmet igényel. Ha a **biztonsági mentés nem engedélyezett** lapra kattint, a feladat segít ebben a feladatban.

Ezen a lapon egy tábla jelenik meg, amely a nem védett elemek listáját tartalmazza. Ha a szervezete azt a gyakorlatot követi, hogy különböző címkéket rendel a termelési gépekhez és a tesztelési gépekhez, illetve a különböző funkciókat kiszolgáló gépekhez, amelyek mindegyike külön biztonsági mentési szabályzatra van szüksége, a címkék alapján történő szűrés segítségével megtekintheti az adott bizonyos kategóriájú gépek. Ha bármelyik elem nevére kattint, a rendszer átirányítja az elem **biztonsági mentésének konfigurálása** panelre, ahol lehetősége van arra, hogy a megfelelő biztonsági mentési szabályzattal biztonsági másolatot készítsen az adott elemről.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="exporting-to-excel"></a>Exportálás Excelbe

Bármely widget (táblázat/diagram) jobb felső sarkában található lefelé mutató nyílra kattintva exportálhatja a widget tartalmát egy Excel-táblázatként, amely a meglévő szűrőket alkalmazza. Ha egy táblázat több sorát szeretné exportálni az Excelbe, a lapon megjelenő sorok számát az egyes lapok tetején lévő **sorok/oldal** legördülő lista használatával növelheti.

## <a name="pinning-to-dashboard"></a>Rögzítés az irányítópulton

Az egyes widgetek tetején található rögzítés ikonra kattintva rögzítheti a widgetet a Azure Portal irányítópulton. Ez segít a szükséges legfontosabb információk megjelenítéséhez igazított testreszabott irányítópultok létrehozásában.

## <a name="cross-tenant-views"></a>Több-bérlős nézetek

Ha Ön egy Azure Lighthouse-felhasználó, aki több bérlői környezetben delegált hozzáféréssel rendelkezik az előfizetésekhez, az alapértelmezett előfizetés-szűrőt használhatja (a Azure Portal jobb felső részén található szűrő ikonra kattintva kiválaszthatja az összes előfizetést, amelyet szeretne Lásd:. Ez lehetővé teszi a Backup Explorer számára, hogy összesítse az összes tár adatait Ezen előfizetések között. További információ az [Azure Lighthouse-](https://docs.microsoft.com/azure/lighthouse/overview)ról.

## <a name="next-steps"></a>Következő lépések

[Megtudhatja, hogyan használhatja a Azure Monitort a biztonsági mentési adataival kapcsolatos elemzések lekéréséhez](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)