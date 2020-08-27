---
title: Tudnivalók az Azure-fájlmegosztás biztonsági mentéséről
description: Ismerje meg, hogyan készíthet biztonsági mentést az Azure-fájlmegosztás számára a Recovery Services-tárolóban
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 7960040df4208ad021bca3406f6472d8c3206d81
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892252"
---
# <a name="about-azure-file-share-backup"></a>Tudnivalók az Azure-fájlmegosztás biztonsági mentéséről

Az Azure fájlmegosztás biztonsági mentése natív, felhőalapú biztonsági mentési megoldás, amely megvédi a felhőben tárolt adatait, és kiküszöböli a helyszíni biztonsági mentési megoldásokban érintett További karbantartási költségeket. A Azure Backup szolgáltatás zökkenőmentesen integrálható Azure File Syncekkel, és lehetővé teszi a fájlmegosztás-és a biztonsági mentések központosítását. Ez az egyszerű, megbízható és biztonságos megoldás lehetővé teszi, hogy néhány egyszerű lépéssel konfigurálja a vállalati fájlmegosztás védelmét, és garantálni tudja, hogy bármely katasztrófa esetén helyreállíthatja az adatokat.

## <a name="key-benefits-of-azure-file-share-backup"></a>Az Azure-fájlmegosztás biztonsági mentésének főbb előnyei

* **Nulla infrastruktúra**: nincs szükség központi telepítésre a fájlmegosztás védelmének konfigurálásához.
* **Testreszabott megőrzés**: a biztonsági mentéseket a követelményeinek megfelelően napi/heti/havi/éves megőrzéssel állíthatja be.
* **Beépített felügyeleti képességek**: ütemezheti a biztonsági mentéseket, és megadhatja a kívánt megőrzési időtartamot az adatok további terhelése nélkül.
* **Azonnali visszaállítás**: az Azure fájlmegosztás biztonsági mentése fájlmegosztási pillanatképeket használ, így egyszerűen kiválaszthatja azokat a fájlokat, amelyeket azonnal vissza szeretne állítani.
* **Riasztások és jelentéskészítés**: beállíthatja a riasztásokat a biztonsági mentési és visszaállítási hibákhoz, és a Azure Backup által biztosított jelentéskészítési megoldás használatával bepillantást nyerhet a fájlok megosztásainak biztonsági másolatából.
* **Fájlmegosztás véletlen törlésével szembeni védelem**: Azure Backup engedélyezi a helyreállítható [törlési funkciót](../storage/files/storage-files-prevent-file-share-deletion.md) a Storage-fiók szintjén egy 14 napos megőrzési időtartammal. Még ha rosszindulatú színész is törli a fájlmegosztást, a fájlmegosztás tartalmát és a helyreállítási pontokat (pillanatképeket) a rendszer megőrzi egy konfigurálható megőrzési időszakra, amely lehetővé teszi a forrás tartalmának és pillanatképének sikeres és teljes helyreállítását adatvesztés nélkül.

## <a name="architecture"></a>Architektúra

![Azure-fájlmegosztás biztonsági mentési architektúrája](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>A biztonsági mentési folyamat működése

1. Az Azure-fájlmegosztás biztonsági mentésének konfigurálásának első lépése Recovery Services-tároló létrehozása. A tároló a különböző munkaterhelések között konfigurált biztonsági másolatok összevont nézetét jeleníti meg.

2. A tár létrehozása után a Azure Backup szolgáltatás felfedi a tárolóban regisztrálható Storage-fiókokat. Kiválaszthatja azt a Storage-fiókot, amely a védelemmel ellátni kívánt fájlmegosztást üzemelteti.

3. A Storage-fiók kiválasztása után a Azure Backup szolgáltatás felsorolja a Storage-fiókban lévő fájlmegosztás készletét, és a nevüket a felügyeleti réteg katalógusában tárolja.

4. Ezután konfigurálja a biztonsági mentési szabályzatot (az ütemtervet és a megőrzést) a követelmények szerint, és válassza ki a biztonsági mentésre kijelölt fájlmegosztást. A Azure Backup szolgáltatás regisztrálja az ütemezéseket a vezérlő síkon az ütemezett biztonsági mentések elvégzéséhez.

5. A megadott házirend alapján az Azure Backup ütemező a biztonsági mentéseket az ütemezett időpontban indítja el. Ennek a feladatoknak a részeként a fájlmegosztás-pillanatkép a fájlmegosztás API használatával jön létre. A rendszer csak a pillanatkép URL-címét tárolja a metaadat-tárolóban.

    >[!NOTE]
    >A fájlmegosztási adatokat a rendszer nem továbbítja a Backup szolgáltatásnak, mivel a biztonsági mentési szolgáltatás létrehozza és kezeli a Storage-fiókhoz tartozó pillanatképeket, és a biztonsági mentések nem kerülnek át a tárba.

6. Az Azure-fájlmegosztás tartalmát (az egyes fájlokat vagy a teljes megosztást) visszaállíthatja a forrásfájl-megosztáson elérhető pillanatképekről. A művelet elindítása után a rendszer beolvassa a pillanatkép URL-címét a metaadat-tárolóból, és az adatok szerepelnek a forrás-pillanatképből a választott célfájl-megosztásra.

7. Ha Azure File Sync használ, a Backup szolgáltatás jelzi, hogy a Azure File Sync szolgáltatás a visszaállítani kívánt fájlok elérési útját jeleníti meg, amely ezután elindítja a háttérben futó változások észlelésének folyamatát ezeken a fájlokon. A módosult fájlok a kiszolgálói végpontra vannak szinkronizálva. Ez a folyamat párhuzamosan történik az Azure-fájlmegosztás eredeti visszaállításával.

8. A biztonsági mentési és visszaállítási feladatok figyelési adatai a Azure Backup figyelési szolgáltatásba vannak leküldve. Ez lehetővé teszi, hogy egyetlen irányítópulton figyelje a fájlmegosztás Felhőbeli biztonsági másolatait. Emellett riasztásokat vagy e-mail-értesítéseket is konfigurálhat a biztonsági mentés állapotának befolyásolása esetén. Az e-maileket az Azure e-mail szolgáltatáson keresztül küldi el a rendszer.

## <a name="backup-costs"></a>Biztonsági mentési költségek

Jelenleg csak Pillanatképek után kell fizetnie, mivel az Azure-fájlmegosztás biztonsági másolata egy pillanatkép-alapú megoldás. A pillanatképekkel kapcsolatban felmerülő tárolási költségek számlázása Azure Files használattal együtt történik, az [itt](https://azure.microsoft.com/pricing/details/storage/files/)említett díjszabási adatok alapján.

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [készíthet biztonsági mentést az Azure file shares](backup-afs.md) -ről
* Válaszokat talál a [Azure Files biztonsági mentésével kapcsolatos kérdésekre](backup-azure-files-faq.md) .
