---
title: Migrálás a StorSimple-ből a Azure File Syncba
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: fauhse
ms.subservice: files
description: A StorSimple egy életcikluson kívüli termék, és Azure File Sync az a megoldás, amelybe migrálni kell. Ismerje meg az áttelepítési koncepciót, és ismerkedjen meg AzureFiles@microsoft.com a testreszabott áttelepítési súgóval.
ms.openlocfilehash: 1cc88080522a62085d9a515223512ef25c20a9e4
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895080"
---
# <a name="storsimple-migration-to-azure-file-sync"></a>StorSimple Migrálás Azure File Syncre

A StorSimple egy megszűnt Microsoft-termék. A termék és a felhőalapú szolgáltatás kiterjesztett támogatása a 2022 végéig tart.
Fontos, hogy azonnal megkezdje a StorSimple áttelepítésének megtervezését.

Az alapértelmezett és a stratégiai hosszú távú Azure Service StorSimple-berendezések áttelepíthetők a Azure File Sync. Ez egy általánosan elérhető Azure-szolgáltatás, amely egy StorSimple-alapú funkciókkal rendelkezik.

## <a name="full-cloud-side-migration-with-limited-downtime"></a>Teljes felhőalapú áttelepítés korlátozott állásidővel
Ez a cikk az áttelepítés megkezdésének koncepcióját ismerteti.
Fontos megjegyezni, hogy a StorSimple-ra és a Azure File Syncre való áttelepítést igénylő ügyfeleknek nem kell saját magukra lépniük.

> [!IMPORTANT]
> A Microsoft elkötelezte magát, hogy segítse az ügyfeleket az áttelepítés során. E-mail-AzureFiles@microsoft. com a testreszabott áttelepítési tervhez, valamint az áttelepítés során nyújtandó támogatáshoz.

## <a name="migration-approach"></a>Áttelepítési módszer
A Azure File Syncre való Migrálás megkezdi a felhőben való áttelepítést, és minimális hatással van a helyszíni és a korlátozott állásidőre.
Az alábbi koncepció a StorSimple 8000 Series készülékekre irányul.
Ha a StorSimple 7000 sorozatból szeretne áttelepítést végezni, az első lépés egy, a Microsofttól származó megfelelő 8000 sorozatú cseredarab-eszközre való ingyenes frissítést eredményez.
Érje el AzureFiles@microsoft.com, és segítünk a megfelelő számú cseredarab-eszköz megszervezésében.

### <a name="general-approach"></a>Általános megközelítés
![ALT](media/storage-sync-files-storsimple-migration/storsimple-docs-overview-concept.png "A felhőalapú áttelepítés egy ideiglenes virtuális készüléken és a Windows Serveren keresztül egy új helyszíni Windows Serverre mutat, amely a helyszíni StorSimple berendezést váltja fel.")

1. Készítsen kötet-klónt a helyszíni StorSimple készülékről, és csatlakoztassa egy ideiglenes StorSimple virtuális készülékhez.
2. A virtuális berendezés iSCSI-kapcsolaton keresztül egy ideiglenes Azure-beli virtuális gépre csatlakoztatható.
3. Telepítse a Azure File Synct az ideiglenes Windows Server rendszerű virtuális gépre – ehhez az áttelepítéshez egy adott beállításkulcsot is be kell állítani ahhoz, hogy a szinkronizálás be legyen állítva a kiszolgálón.
    * A StorSimple-köteten található megosztások számától függően telepítsen annyi Azure-fájlmegosztást. (Ajánlott egy Azure-fájlmegosztás üzembe helyezése egy Storage-fiókban.)
    * Konfigurálja a szinkronizálást a Windows Server Felhőbeli virtuális gép és az Azure-fájlmegosztás egyedi megosztása között. (1:1 leképezés)
    * Opcionálisan hozzáadhat egy helyi kiszolgálót helyszíni teljesítmény-gyorsítótárként, amelyen engedélyezve van a felhőalapú réteg. Erre a lépésre akkor van szükség, ha a helyszíni StorSimple egy szélesebb körű, helyi gyorsítótárral szeretné lecserélni, amelyet a Windows Server és a Azure File Sync felhőalapú rétege működtet. A helyszíni Windows Server lehet egy fizikai gép vagy fürt, vagy egy virtuális gép. Nem kell annyi tárterületet telepítenie, mint az adatkészlet mérete. Csak annyi tárterületre van szükség, hogy helyileg gyorsítótárazza a leggyakrabban használt fájlokat.

## <a name="minimizing-downtime"></a>Leállások minimalizálása
A fenti 3. lépés után a felhasználók és az alkalmazások továbbra is aktívan használják a StorSimple helyszíni berendezését. Így a kezdeti kötet klónozásával szinkronizált fájlok készlete némileg elavult, amikor a szinkronizálás befejeződik.
Az állásidő minimalizálásának módszere, ha megismétli a szinkronizálást a kötetek klónozási folyamatával, így a szinkronizálás gyorsabban és gyorsabban fejeződik be az egyes iterációk esetében, ami viszont a kötetek klónozása közötti változásoknál kevesebb és kevesebb lesz.
Megismételheti ezt a folyamatot, amíg a kötetek klónozása nem fejezhető be az állásidő számára elfogadható időtartam alatt.
Ha ez a helyzet, tiltsa le a felhasználókat és az alkalmazásokat, hogy bármilyen változást StorSimple készülékén. A leállás megkezdődik.
Vegyen fel egy másik kötet-klónt, és szinkronizálja a csatlakoztatott kiszolgáló (ka) t.
Hozzon létre hozzáférést a felhasználókhoz és az alkalmazásokhoz az új, Azure File Sync támogatott Windows Server-kiszolgálóhoz.
Érdemes lehet egy elosztott fájlrendszerbeli névtér üzembe helyezése/módosítása, hogy a régi StorSimple készülékről az új Windows Serverre való kivágást az alkalmazások és felhasználók számára transzparensvé tegye.
A Migrálás befejeződött.

## <a name="migration-goal"></a>Áttelepítési cél
Az áttelepítés befejezése után az ideiglenes StorSimple virtuális készülék és az Azure virtuális gép kiépíthető.

Továbbá a helyszíni StorSimple-készülék kiépíthető, mivel a felhasználók és az alkalmazások már hozzáférnek a Windows Serverhez.
Az alábbi képen látható, hogy mi marad. A standard Azure File Sync üzemelő példány számos Azure-fájlmegosztást és Windows-kiszolgálót tartalmaz, amelyek Azure File Sync használatával csatlakoznak hozzájuk. Ne feledje, hogy egy adott kiszolgáló különböző helyi mappákat tud csatlakozni egymáshoz egy időben.
Emellett egy Azure-fájlmegosztás számos különböző kiszolgálóra is szinkronizálható, ha a fiókirodákban tárolt adatmennyiségre van szüksége. Azt is ellenőrizze, hogy optimalizálhatja-e a felhőalapú rétegbeli szabályzatokat a helyszíni tárolóhely hatékonyabb kihasználásához.

![ALT](media/storage-sync-files-storsimple-migration/storsimple-docs-goal.PNG "Az áttelepítés befejezése utáni célt bemutató ábra. Több fájlmegosztást szinkronizál egy helyszíni Windows Server-kiszolgálóval a felhőben vagy a Windows Serveren lévő fájlokat elérő felhasználókkal és alkalmazásokkal.")

## <a name="next-steps"></a>Következő lépések
Ismerkedjen meg Azure Filesával és Azure File Syncával. Fontos, hogy megértse a sikeres áttelepítéshez szükséges Azure File Sync terminológiai és telepítési mintát. Az áttekintő cikkben található minden egyes lépéshez részletesebb információ érhető el. A Migrálás megtervezése és végrehajtása során győződjön meg arról, hogy a Microsofttól a testreszabott súgóhoz jut.

> [!IMPORTANT]
> A Microsoft elkötelezte magát, hogy segítse az ügyfeleket az áttelepítés során. E-mail-AzureFiles@microsoft. com a testreszabott áttelepítési tervhez, valamint az áttelepítés során nyújtandó támogatáshoz.

## <a name="additional-resources"></a>További források
Azure File Sync a célként megadott szolgáltatásnak két alapvető dokumentuma van, amelyeket érdemes elolvasnia, ha új a Azure File Sync.
* [Azure File Sync – áttekintés](storage-sync-files-planning.md)
* [Azure File Sync – üzembe helyezési útmutató](storage-sync-files-deployment-guide.md)

Azure Files egy Azure-beli tárolási szolgáltatás, amely a fájlmegosztást szolgáltatásként kínálja. Nincs szükség a virtuális gépek vagy a kapcsolódó virtuálisgép-tárolók megfizetésére és karbantartására.
* [Azure Files – áttekintés](storage-files-introduction.md)
* [Azure Files – Azure-fájlmegosztás üzembe helyezése](storage-how-to-create-file-share.md)