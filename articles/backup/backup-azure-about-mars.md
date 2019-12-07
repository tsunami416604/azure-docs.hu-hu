---
title: Tudnivalók a MARS-ügynökről
description: Ismerje meg, hogyan támogatja a MARS-ügynök a biztonsági mentési forgatókönyveket
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: c036d93c09195c0c330cfe86f307d28866131d9f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74897324"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Tudnivalók a Microsoft Azure Recovery Services (MARS) ügynökről

Ez a cikk azt ismerteti, hogyan használja a Azure Backup szolgáltatás a Microsoft Azure Recovery Services (MARS) ügynököt a fájlok/mappák, a kötet vagy a rendszerállapot biztonsági mentéséhez és visszaállításához a helyszíni számítógépről az Azure-ba.

A MARS-ügynök a következő biztonsági mentési forgatókönyveket támogatja:

![Recovery Services-tároló irányítópultja](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Fájlok és mappák: a**Windows-fájlok és-mappák szelektív védelemmel való ellátása.
- **Kötet szintje**: a számítógép teljes Windows-kötetének a megóvása.
- **Rendszerszintű**: a teljes Windows rendszerállapotának biztosítása.

A MARS-ügynök a következő visszaállítási forgatókönyveket támogatja:

![Recovery Services-tároló irányítópultja](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

-   **Ugyanazon kiszolgáló**: ugyanaz a kiszolgáló, amelyen a biztonsági mentés eredetileg létrejött.
    -    **Fájlok és mappák**: megkeresheti és kiválaszthatja azokat az egyes fájlokat és mappákat, amelyeket vissza szeretne állítani.
    -    **Kötet szintje**: kiválaszthatja azt a kötetet és helyreállítási pontot, amelyet vissza szeretne állítani, majd visszaállíthatja ugyanarra a helyre vagy másik helyre ugyanazon a gépen.  Létrehozhat egy másolatot a meglévő fájlokról, felülírhatja a meglévő fájlokat, vagy kihagyhatja a meglévő fájlok helyreállítását.
    -    **Rendszerszintű**: kiválaszthatja a rendszerállapotot és a helyreállítási pontot, ha ugyanarra a gépre szeretne visszaállítani egy adott helyen.


-   **Másodlagos kiszolgáló**: egy másik kiszolgáló, azaz nem ugyanaz a kiszolgáló, ahol a biztonsági mentés készült.
    -    **Fájlok és mappák**: megkeresheti és kiválaszthatja azokat a fájlokat és mappákat, amelyeket vissza szeretne állítani a helyreállítási pontról a célszámítógépre.
    -    **Kötet szintje**: kiválaszthatja azt a kötetet és helyreállítási pontot, amelyet egy másik helyre szeretne visszaállítani, ha a meglévő fájlok másolatát hozza létre, felülírja a meglévő fájlokat, vagy kihagyja a meglévő fájlok helyreállítását.
    -    **Rendszerszintű**: kiválaszthatja a rendszerállapotot és a helyreállítási pontot, ha rendszerállapot-fájlként szeretné visszaállítani egy másik gépre.

## <a name="backup-process"></a>A biztonsági mentés folyamata

1.  A Azure Portal hozzon létre egy [helyreállítási](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-recovery-services-vault) tárat, és válassza a fájlok és mappák és/vagy a rendszerállapot lehetőséget a biztonsági mentési célokból.
2.  [Töltse le](https://docs.microsoft.com/azure/backup/backup-configure-vault#download-the-mars-agent) a Recovery Service Vault hitelesítő adatait és az ügynök telepítőjét egy helyszíni gépre. A helyszíni gép védeleméhez válassza a fájlok és mappák, valamint a rendszerállapot és a MARS-ügynök letöltése lehetőséget a biztonsági mentés lehetőség kiválasztásával.
3.  Az infrastruktúra előkészítése:

    a.    Futtassa a telepítőt az ügynök [telepítéséhez](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) .

    b.  A letöltött tároló hitelesítő adataival regisztrálja a gépet Recovery Services-tárolóba.
4.  A [biztonsági mentés konfigurálásához használja az](https://docs.microsoft.com/azure/backup/backup-configure-vault#create-a-backup-policy) ügynök konzolját az ügyfélen. Adja meg a biztonsági mentési adatok megőrzési szabályát, és indítsa el a védelmet.

![Recovery Services-tároló irányítópultja](./media/backup-try-azure-backup-in-10-mins/backup-process.png)


### <a name="additional-scenarios"></a>További helyzetek
-   **Meghatározott fájlok és mappák biztonsági mentése az Azure** -beli virtuális gépen – az Azure Virtual Machines (VM) biztonsági mentésének elsődleges módszere egy Azure Backup-bővítmény használata a virtuális gépen. Ezzel biztonsági másolatot készít a teljes virtuális gépről. Ha egy virtuális gépen belül szeretne biztonsági másolatot készíteni bizonyos fájlokról és mappákról, telepítheti a MARS-ügynököt az Azure-beli virtuális gépeken. [További információk](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

-   **Offline** kivezetés – az adatok kezdeti teljes biztonsági mentése az Azure-ba, általában nagy mennyiségű adat átvitele és nagyobb hálózati sávszélesség szükséges a csak a különbözeti/növekményes biztonsági mentéshez képest. Azure Backup tömöríti a kezdeti biztonsági mentéseket. Az offline kivetés folyamatán keresztül a Azure Backup lemezek használatával feltölthetik a tömörített kezdeti biztonsági mentési adatok az Azure-ba. [További információk](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-).


## <a name="next-steps"></a>Következő lépések
[A MARS-ügynök támogatja a mátrixot](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[Gyakori kérdések – a MARS-ügynök](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
