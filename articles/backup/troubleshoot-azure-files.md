---
title: Az Azure-fájlmegosztások biztonsági mentésének hibaelhárítása
description: A cikk olyan hibákkal kapcsolatos hibaelhárítási információkat tartalmaz, amelyek az Azure fájlmegosztások védelmekor következnek be.
ms.service: backup
author: rayne-wiselman
ms.author: raynew
ms.date: 07/22/2019
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: c44819bfda2f48195c25ad6be7608383162f3c61
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467117"
---
# <a name="troubleshoot-problems-backing-up-azure-file-shares"></a>Az Azure-fájlmegosztások biztonsági mentésével kapcsolatos problémák elhárítása
Az alábbi táblázatokban szereplő információk segítségével elháríthatja az Azure-fájlmegosztások biztonsági mentése közben fellépő problémákat és hibákat.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Az Azure-fájlmegosztás biztonsági mentésének korlátozásai az előzetes verzióban
Az Azure-fájlmegosztások biztonsági mentése jelenleg előzetes verzióban érhető el. Az Azure-fájlmegosztás az általános célú v1 és az általános célú v2 Storage-fiókok esetében is támogatott. Az Azure-fájlmegosztások nem támogatják az alábbi biztonsági mentési forgatókönyveket:
- Nem biztosítható az Azure-fájlmegosztások védelme olyan tárfiókokban, amelyeken engedélyezve vannak a virtuális hálózatok vagy a tűzfal.
- Azure Backup használatával nem érhető el a CLI a Azure Files védelméhez.
- Az ütemezett biztonsági mentések maximális száma naponta egy.
- Az igény szerinti biztonsági mentések maximális száma naponta négy.
- Használjon [erőforrászárat](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) a tárfiókon, hogy megelőzze a helyreállítási tárban lévő biztonsági másolatok véletlen törlését.
- Ne törölje az Azure Backuppal létrehozott pillanatképeket. A pillanatképek törlése helyreállítási pontok elvesztését és/vagy visszaállítási hibákat eredményezhet.
- Ne törölje a Azure Backup által védett fájlmegosztást. A jelenlegi megoldás törli az Azure Backup által készített összes pillanatképet, miután a fájlmegosztás törölve lett, és így elveszíti az összes visszaállítási pontot

A Storage-fiókokban tárolt Azure-fájlmegosztás [(ZRS](../storage/common/storage-redundancy-zrs.md) ) replikációja jelenleg csak az USA középső régiójában (ke), az USA keleti régiójában (EUs), az USA 2. keleti régiójában (EUS2), Észak-Európában (ne), Délkelet-Ázsiában (tenger), Nyugat-Európában (mi) és az USA 2. nyugati régiójában (WUS2) érhető el.

## <a name="configuring-backup"></a>Biztonsági mentés konfigurálása
Az alábbi táblázat a biztonsági mentés minél pontosabb konfigurálásához használható:

| Hibaüzenetek | Megkerülő vagy megoldási tippek |
| ------------------ | ----------------------------- |
| Nem találom a tárfiókom az Azure-fájlmegosztás biztonsági mentésének konfigurálásához | <ul><li>Várjon, amíg a felderítés befejeződik. <li>Ellenőrizze, hogy egy másik helyreállítási tár védi-e már bármelyik fájlmegosztást a tárfiókból. **Megjegyzés**: A Storage-fiókokban lévő összes fájlmegosztást csak egy Recovery Services tárolóban lehet védeni. <li>Győződjön meg arról, hogy a fájlmegosztás nincs jelen egyik nem támogatott tárfiókban sem.|
| A portál hibája azt jelenti, hogy a tárfiókok felderítése sikertelen volt. | Ha az előfizetése partneri (CSP-engedélyezett), hagyja figyelmen kívül a hibát. Ha az előfizetés nem engedélyezi a CSP-t, a tárfiókok pedig nem deríthetők fel, forduljon az ügyfélszolgálathoz.|
| A kiválasztott tárfiók érvényesítése vagy regisztrációja sikertelen.| Próbálkozzon újra a művelettel. Ha a probléma továbbra is fennáll, forduljon az ügyfélszolgálathoz.|
| A kiválasztott tárfiókban nem listázható vagy nem található fájlmegosztás. | <ul><li> Győződjön meg arról, hogy a tárfiók létezik az erőforráscsoportban (és nem törölték vagy helyezték át a tárolóban az utolsó ellenőrzés/regisztráció után).<li>Ellenőrizze, hogy a védeni kívánt fájlmegosztás nem lett-e törölve. <li>Ellenőrizze, hogy a tárfiók támogatott-e fájlmegosztásokról való biztonsági másolat készítéséhez.<li>Ellenőrizze, hogy a fájlmegosztás már védve van-e ugyanabban a helyreállítási tárban.|
| A fájlmegosztás biztonsági mentésének konfigurációja (vagy a védelmi szabályzat konfigurálása) sikertelen. | <ul><li>Próbálja megismételni a műveletet annak ellenőrzéséhez, hogy a probléma továbbra is fennáll-e. <li> Győződjön meg arról, hogy a védeni kívánt fájlmegosztás nem lett törölve. <li> Ha több fájlmegosztást kíván egyszerre védeni, és a fájlmegosztások némelyike nem működik, ismételje meg a sikertelen fájlmegosztások biztonsági mentésének konfigurálását. |
| Nem sikerült törölni a helyreállítási tárat a fájlmegosztás védelmének feloldása után. | Nyissa meg az Azure Portalon a saját Tár > **Biztonsági mentési infrastruktúra** > **Tárfiókok** elemet, és kattintson a **Regisztráció törlése** lehetőségre a tárfiók helyreállítási tárból történő eltávolításához.|


## <a name="error-messages-for-backup-or-restore-job-failures"></a>A feladatok biztonsági mentése vagy helyreállítása során jelentkező hibák üzenetei

| Hibaüzenetek | Megkerülő vagy megoldási tippek |
| -------------- | ----------------------------- |
| A művelet nem sikerült, mert a fájlmegosztás nem található. | Ellenőrizze, hogy a védeni kívánt fájlmegosztás nem lett-e törölve.|
| A tárfiók nem található vagy nem támogatott. | <ul><li>Győződjön meg arról, hogy az erőforráscsoport tartalmazza a tárfiókot, és nem törölték vagy távolították el az erőforráscsoportból az utolsó ellenőrzés alkalmával. <li> Ellenőrizze, hogy a tárfiók támogatott-e fájlmegosztásokról való biztonsági másolat készítéséhez.|
| Elérte a fájlmegosztás pillanatképekre vonatkozó maximális korlátját. Akkor fog tudni továbbiakat készíteni, ha a régebbiek érvényessége lejár. | <ul><li> Ez a hiba akkor fordulhat elő, ha több igény szerinti biztonsági mentést hoz létre egy fájlhoz. <li> Egy fájlmegosztásról legfeljebb 200 pillanatkép készíthető, beleértve az Azure Backup által készítetteket is. A régebbi ütemezett biztonsági mentések (vagy pillanatképek) automatikusan törlődnek. Törölni kell az igény szerinti biztonsági mentést (vagy pillanatképeket), ha elérik a maximális korlátot.<li> Törölje az igény szerinti biztonsági mentéseket (az Azure fájlmegosztási pillanatképeket) az Azure Files portálról. **Megjegyzés**: A helyreállítási pontok elvesznek, ha törli a Azure Backup által létrehozott pillanatképeket. |
| A fájlmegosztás biztonsági mentése vagy visszaállítása a társzolgáltatás szabályozása miatt meghiúsult. Ez azért történhet, mert a társzolgáltatás a megadott tárfiók más kérelmeinek feldolgozásával van elfoglalva.| Próbálja meg újra a műveletet később. |
| A visszaállítás sikertelen, mert a célként megadott fájlmegosztás nem található. | <ul><li>Győződjön meg arról, hogy a kiválasztott tárfiók létezik, a célfájlmegosztás pedig nincs törölve. <li> Ellenőrizze, hogy a tárfiók támogatott-e fájlmegosztásokról való biztonsági másolat készítéséhez. |
| Az Azure Backup jelenleg nem támogatott olyan tárfiókokban lévő Azure-fájlmegosztásokhoz, amelyeknél a virtuális hálózatok engedélyezve vannak. | Tiltsa le a virtuális hálózatokat a tárfiókon a sikeres biztonsági mentések vagy visszaállítási műveletek biztosításához. |
| A biztonsági mentési vagy visszaállítási feladat sikertelen volt a tárfiók zárolt állapota miatt. | Távolítsa el a zárolást a tárfiókról, vagy használjon törlési zárolást az olvasási zárolás helyett, majd ismételje meg a műveletet. |
| A helyreállítás sikertelen volt, mert a hibás fájlok száma meghaladja a küszöbértéket. | <ul><li> A helyreállítási hibák okainak listája egy fájlban található (a feladat részletei tartalmazzák az elérési utat). Kérjük, oldja meg a hibákat, és ismételje meg visszaállítási műveletet csak a hibás fájlokra vonatkozóan. <li> A fájlvisszaállítási hibák gyakori okai: <br/> – győződjön meg arról, hogy a hibás fájlok jelenleg nincsenek használatban, <br/> – a hibás fájllal megegyező nevű könyvtár található a szülőkönyvtárban. |
| A helyreállítás sikertelen volt, mert egyetlen fájl sem állítható helyre. | <ul><li> A helyreállítási hibák okainak listája egy fájlban található (a feladat részletei tartalmazzák az elérési utat). Oldja meg a hibákat, majd ismételje meg a visszaállítási műveleteket csak a hibás fájlokra vonatkozóan. <li> A sikertelen fájlvisszaállítás gyakori okai: <br/> – Győződjön meg arról, hogy a hibás fájlok jelenleg nincsenek használatban. <br/> – A hibás fájllal megegyező nevű könyvtár található a szülőkönyvtárban. |
| A visszaállítás sikertelen, mert a forrás egyik fájlja nem létezik. | <ul><li> A kijelölt elemek nem szerepelnek a helyreállítási pont adataiban. A fájlok helyreállításához adja meg a helyes fájllistát. <li> A helyreállítási ponthoz tartozó megosztás fájlpillanatképet manuálisan kell törölni. Válasszon egy másik helyreállítási pontot, és ismételje meg a visszaállítási műveletet. |
| Már folyamatban van egy ugyanerre a célhelyre irányuló helyreállítási feladat. | <ul><li>A fájlmegosztás biztonsági mentése nem támogatja az ugyanazon célfájlmegosztásra végzett párhuzamos helyreállítást. <li>Várja meg a meglévő helyreállítási feladat befejeződését, majd próbálja meg újból. Ha nem talál helyreállítási feladatot a helyreállítási tárban, ellenőrizze az adott előfizetéshez tartozó többi helyreállítási tárat. |
| A visszaállítási művelet sikertelen volt, mert a célfájlmegosztás megtelt. | Növelje meg a célfájlmegosztás méretkvótáját, hogy az képes legyen fogadni a visszaállítási adatokat, majd ismételje meg a műveletet. |
| A visszaállítási művelet sikertelen volt, mivel hiba történt a File Sync szolgáltatás a célfájlmegosztással társított erőforrásain végrehajtott visszaállítás előtti műveletek során. | Később próbálkozzon újra, és ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához. |
| Egy vagy több fájlt nem sikerült helyreállítani. További információkért ellenőrizze a hibás fájlok listáját a fent megadott elérési úton. | <ul> <li> A helyreállítási hibák okai megtalálhatók a fájlban (az elérési utat a feladat részletei tartalmazzák). Vizsgálja ki az okokat, majd ismételje meg a visszaállítási műveletet csak a hibás fájlokra vonatkozóan. <li> A fájlvisszaállítási hibák leggyakoribb okai a következők: <br/> – Győződjön meg arról, hogy a hibás fájlok jelenleg nincsenek használatban. <br/> – Egy, a hibás fájlokkal megegyező nevű könyvtár található a szülőkönyvtárban. |


## <a name="modify-policy"></a>Házirend módosítása
| Hibaüzenetek | Megkerülő vagy megoldási tippek |
| ------------------ | ----------------------------- |
| Egy másik védelemkonfigurálási művelet van folyamatban az elemhez kapcsolódóan. | Várjon, amíg befejeződik az előző házirend-módosítási művelet, és némi várakozás után próbálkozzon újra.|
| Egy másik művelet van folyamatban a kijelölt elemen. | Várjon, amíg a másik folyamatban lévő művelet befejeződik, és próbálkozzon újra egy kis idő elteltével |


## <a name="see-also"></a>Lásd még:
Az Azure-fájlmegosztások biztonsági mentésével kapcsolatos további információk:
- [Azure-fájlmegosztások biztonsági mentése](backup-azure-files.md)
- [Azure-fájlmegosztások biztonsági mentése – GYIK](backup-azure-files-faq.md)
