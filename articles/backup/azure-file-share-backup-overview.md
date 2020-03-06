---
title: Tudnivalók az Azure-fájlmegosztás biztonsági mentéséről
description: Ismerje meg, hogyan készíthet biztonsági mentést az Azure-fájlmegosztás számára a Recovery Services-tárolóban
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 0e17b05a3febaa673fb29d45c2bcef25e2996df8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386719"
---
# <a name="about-azure-file-share-backup"></a>Tudnivalók az Azure-fájlmegosztás biztonsági mentéséről

Az Azure fájlmegosztás biztonsági mentése natív, felhőalapú biztonsági mentési megoldás, amely megvédi a felhőben tárolt adatait, és kiküszöböli a helyszíni biztonsági mentési megoldásokban érintett További karbantartási költségeket. A Azure Backup szolgáltatás zökkenőmentesen integrálható az Azure file Sync szolgáltatással, és lehetővé teszi a fájlmegosztás-és a biztonsági másolatok központi kezelését. Ez az egyszerű, megbízható és biztonságos megoldás lehetővé teszi, hogy néhány egyszerű lépéssel konfigurálja a vállalati fájlmegosztás védelmét, és gondoskodjon arról, hogy vészhelyzet esetén helyreállítsa az adatokat.

## <a name="key-benefits-of-azure-file-share-backup"></a>Az Azure-fájlmegosztás biztonsági mentésének főbb előnyei

* Nulla infrastruktúra: nincs szükség központi telepítésre a fájlmegosztás védelmének konfigurálásához.
* Beépített felügyeleti képességek: ütemezheti a biztonsági mentéseket, és megadhatja a kívánt megőrzési időtartamot az adatok további terhelése nélkül.
* Azonnali visszaállítás: az Azure fájlmegosztás biztonsági mentése fájlmegosztási pillanatképeket használ, így egyszerűen kiválaszthatja azokat a fájlokat, amelyeket azonnal vissza szeretne állítani.
* Riasztások és jelentéskészítés: beállíthatja a riasztásokat a biztonsági mentési és visszaállítási hibákhoz, és a Azure Backup által biztosított jelentéskészítési megoldás használatával bepillantást nyerhet a fájlok megosztásainak biztonsági másolatából.

## <a name="architecture"></a>Architektúra

![Azure-fájlmegosztás biztonsági mentési architektúrája](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>A biztonsági mentési folyamat működése

1. Az Azure-fájlmegosztás biztonsági mentésének konfigurálásának első lépése a Recovery Services-tároló létrehozása. A tároló a különböző munkaterhelések között konfigurált biztonsági másolatok összevont nézetét jeleníti meg.

2. A tár létrehozása után a Azure Backup szolgáltatás felfedi a tárolóban regisztrálható Storage-fiókokat. Kiválaszthatja azt a Storage-fiókot, amely a védelemmel ellátni kívánt fájlmegosztást üzemelteti.

3. A Storage-fiók kiválasztása után a Azure Backup szolgáltatás felsorolja a Storage-fiókban lévő fájlmegosztás készletét, és a nevüket a felügyeleti réteg katalógusában tárolja.

4. Ezután konfigurálja a biztonsági mentési szabályzatot (az ütemtervet és a megőrzést) a követelmények szerint, és válassza ki a biztonsági mentésre kijelölt fájlmegosztást. A Azure Backup szolgáltatás regisztrálja az ütemezéseket a vezérlő síkon az ütemezett biztonsági mentések elvégzéséhez.

5. A megadott házirend alapján az Azure Backup ütemező a biztonsági mentéseket az ütemezett időpontban indítja el. Ennek a feladatoknak a részeként a fájlmegosztás-pillanatkép a fájlmegosztás API használatával jön létre. A rendszer csak a pillanatkép URL-címét tárolja a metaadat-tárolóban.

    >[!NOTE]
    >A fájlmegosztás adatai nem kerülnek át a Backup szolgáltatásba, mivel a Backup szolgáltatás létrehozza és kezeli a Storage-fiókhoz tartozó pillanatképeket.

6. Az Azure-fájlmegosztás tartalmát (az egyes fájlokat vagy a teljes megosztást) visszaállíthatja a forrásfájl-megosztáson elérhető pillanatképekről. A művelet elindítása után a rendszer beolvassa a pillanatkép URL-címét a metaadat-tárolóból, és az adatok szerepelnek a forrás-pillanatképből a választott célfájl-megosztásra.

7. A biztonsági mentési és visszaállítási feladatok figyelési adatai a Azure Backup figyelési szolgáltatásba vannak leküldve. Ez lehetővé teszi, hogy egyetlen irányítópulton figyelje a fájlmegosztás Felhőbeli biztonsági másolatait. Emellett riasztásokat vagy e-mail-értesítéseket is konfigurálhat a biztonsági mentés állapotának befolyásolása esetén. Az e-maileket az Azure e-mail szolgáltatáson keresztül küldi el a rendszer.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [készíthet biztonsági mentést az Azure file shares](backup-afs.md) -ről
* Válaszokat talál a [Azure Files biztonsági mentésével kapcsolatos kérdésekre](backup-azure-files-faq.md) .
