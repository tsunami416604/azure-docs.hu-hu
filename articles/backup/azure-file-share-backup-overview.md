---
title: Az Azure fájlmegosztás biztonsági másolata
description: Ismerje meg, hogyan lehet biztonsági másolatot adni az Azure-fájlmegosztásokról a Recovery Services-tárolóban
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 0e17b05a3febaa673fb29d45c2bcef25e2996df8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78386719"
---
# <a name="about-azure-file-share-backup"></a>Az Azure fájlmegosztás biztonsági másolata

Az Azure fájlmegosztási biztonsági mentés egy natív, felhőalapú biztonsági mentési megoldás, amely védi az adatokat a felhőben, és kiküszöböli a helyszíni biztonsági mentési megoldásokban felmerülő további karbantartási költségeket. Az Azure Backup szolgáltatás zökkenőmentesen integrálható az Azure-fájlszinkronizálással, és lehetővé teszi a fájlmegosztási adatok és a biztonsági mentések központosítását. Ez az egyszerű, megbízható és biztonságos megoldás lehetővé teszi, hogy a vállalati fájlmegosztások védelmét néhány egyszerű lépésben konfigurálja, és garantálja, hogy bármilyen katasztrófa esetén helyreállíthatja az adatokat.

## <a name="key-benefits-of-azure-file-share-backup"></a>Az Azure fájlmegosztásbiztonsági biztonsági mentésének legfontosabb előnyei

* Nulla infrastruktúra: Nincs szükség üzembe helyezésre a fájlmegosztások védelmének konfigurálásához.
* Beépített felügyeleti képességek: Ütemezheti a biztonsági mentéseket, és megadhatja a kívánt megőrzési időszakot az adatmetszés további többletterhelése nélkül.
* Azonnali visszaállítás: Az Azure fájlmegosztásbiztonsági mentése fájlmegosztási pillanatképeket használ, így csak azokat a fájlokat választhatja ki, amelyeket azonnal vissza szeretne állítani.
* Riasztás és jelentéskészítés: Beállíthatja a biztonsági mentési és visszaállítási hibák riasztási és visszaállítási riasztási megoldását, és az Azure Backup által biztosított jelentéskészítő megoldás sal betekintést nyerhet a fájlmegosztások biztonsági másolatairól.

## <a name="architecture"></a>Architektúra

![Azure fájlmegosztás biztonsági mentési architektúrája](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>A biztonsági mentési folyamat működése

1. Az Azure File shares biztonsági mentésének konfigurálása első lépése a helyreállítási szolgáltatások tárolójának létrehozása. A tároló a különböző munkaterhelések között konfigurált biztonsági mentések összesített nézetét biztosítja.

2. Miután létrehozott egy trezort, az Azure Backup szolgáltatás felderíti a tárolóban regisztrálható tárfiókokat. Kiválaszthatja a védeni kívánt fájlmegosztásokat tároló tárfiókot.

3. Miután kiválasztotta a tárfiókot, az Azure Backup szolgáltatás felsorolja a tárfiókban található fájlmegosztások készletét, és a nevüket a felügyeleti réteg katalógusában tárolja.

4. Ezután konfigurálja a biztonsági mentési házirendet (ütemezésés és megőrzés) a követelményeknek megfelelően, és válassza ki a fájlmegosztásokat a biztonsági mentéshez. Az Azure Backup szolgáltatás regisztrálja az ütemezések a vezérlősíkon az ütemezett biztonsági mentések.

5. A megadott szabályzat alapján az Azure Backup ütemező elindítja a biztonsági mentést az ütemezett időpontban. Ennek a feladatnak a részeként a fájlmegosztás pillanatképe a Fájlmegosztás API használatával jön létre. Csak a pillanatkép URL-címe tárolódik a metaadat-tárolóban.

    >[!NOTE]
    >A fájlmegosztási adatok nem kerülnek át a Biztonsági mentés szolgáltatásba, mivel a Biztonsági másolat szolgáltatás létrehozza és kezeli a tárfiók részét tartalmazó pillanatképeket.

6. Az Azure-fájlmegosztás tartalmát (az egyes fájlokat vagy a teljes megosztást) a forrásfájl-megosztáson elérhető pillanatképekből állíthatja vissza. A művelet aktiválása után a pillanatkép URL-címe beolvasásra kerül a metaadat-tárolóból, és az adatok megjelennek, és a forráspillanatképből a választott célfájl-megosztásra kerülnek.

7. A biztonsági mentési és visszaállítási feladat figyelési adatok leküldéses az Azure Backup monitoring szolgáltatás. Ez lehetővé teszi a fájlmegosztások felhőbeli biztonsági másolatainak egyetlen irányítópulton való figyelését. Emellett riasztásokat vagy e-mail értesítéseket is konfigurálhat, ha a biztonsági mentés állapota érintett. Az e-maileket az Azure e-mail szolgáltatásán keresztül küldik.

## <a name="next-steps"></a>További lépések

* További információ az [Azure-fájlmegosztások biztonsági és biztonsági rendszerről](backup-afs.md)
* Válaszok az [Azure Files biztonsági mentésével kapcsolatos kérdésekre](backup-azure-files-faq.md)
