---
title: Tudnivalók a MARS-ügynökről
description: Ismerje meg, hogyan támogatja a MARS-ügynök a biztonsági mentési forgatókönyveket
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d2cc8e32152f6930c9c250e2811668cc2c924616
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78673296"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Tudnivalók a Microsoft Azure Recovery Services (MARS) ügynökről

Ez a cikk azt ismerteti, hogyan használja a Azure Backup szolgáltatás a Microsoft Azure Recovery Services (MARS) ügynököt a fájlok, mappák, valamint a kötet vagy a rendszerállapot biztonsági mentésére és visszaállítására egy helyszíni számítógépről az Azure-ba.

A MARS-ügynök a következő biztonsági mentési forgatókönyveket támogatja:

![MARS biztonsági mentési forgatókönyvek](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Fájlok és mappák: a**Windows-fájlok és-mappák szelektív védelemmel való ellátása.
- **Kötet szintje**: a számítógép teljes Windows-kötetének a megóvása.
- **Rendszerszintű**: a teljes Windows rendszerállapotának biztosítása.

A MARS-ügynök a következő visszaállítási forgatókönyveket támogatja:

![MARS helyreállítási forgatókönyvek](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Ugyanazon kiszolgáló**: az a kiszolgáló, amelyen a biztonsági másolatot eredetileg létrehozták.
  - **Fájlok és mappák**: válassza ki a visszaállítani kívánt fájlokat és mappákat.
  - **Kötet szintje**: válassza ki azt a kötetet és helyreállítási pontot, amelyet vissza szeretne állítani, majd állítsa vissza ugyanarra a helyre, vagy egy másik helyre ugyanazon a gépen.  Meglévő fájlok másolatának létrehozása, meglévő fájlok felülírása vagy a meglévő fájlok helyreállításának kihagyása.
  - **Rendszerszintű**: válassza ki a rendszerállapotot és a helyreállítási pontot, hogy a visszaállítás ugyanarra a gépre történjen egy adott helyen.

- **Másodlagos kiszolgáló**: a biztonsági mentést végző kiszolgálótól eltérő kiszolgáló.
  - **Fájlok és mappák**: válassza ki azokat az egyes fájlokat és mappákat, amelyeknek a helyreállítási pontját szeretné visszaállítani a célszámítógépen.
  - **Kötet szintje**: válassza ki azt a kötetet és helyreállítási pontot, amelyet vissza szeretne állítani egy másik helyre. Meglévő fájlok másolatának létrehozása, meglévő fájlok felülírása vagy a meglévő fájlok helyreállításának kihagyása.
  - **Rendszerszintű**: válassza ki a rendszerállapotot és a helyreállítási pontot, ha rendszerállapot-fájlként szeretné visszaállítani egy másik számítógépre.

## <a name="backup-process"></a>A biztonsági mentés folyamata

1. A Azure Portal hozzon létre egy [Recovery Services](install-mars-agent.md#create-a-recovery-services-vault)-tárolót, majd a biztonsági mentési célokból válassza a fájlok, mappák és a rendszerállapot lehetőséget.
2. [Töltse le a Recovery Services-tároló hitelesítő adatait és az ügynök telepítőjét](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) egy helyszíni gépre.

    Ha a biztonsági mentés lehetőség kiválasztásával szeretné megóvni a helyszíni gépet, válassza a fájlok, mappák és a rendszerállapot lehetőséget, majd töltse le a MARS-ügynököt.

3. Az infrastruktúra előkészítése:

    a. Futtassa a telepítőt az [ügynök telepítéséhez](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent).

    b. A letöltött tároló hitelesítő adataival regisztrálja a gépet a Recovery Services-tárolóban.
4. [Konfigurálja a biztonsági mentést](https://docs.microsoft.com/azure/backup/backup-windows-with-mars-agent#create-a-backup-policy)az ügyfél ügynök-konzolján. A biztonsági mentési adatok megőrzési szabályának megadásával megkezdheti a védelem megkezdését.

![Azure Backup-ügynök diagramja](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-scenarios"></a>További helyzetek

- **Meghatározott fájlok és mappák biztonsági mentése az Azure Virtual Machines**szolgáltatásban: az Azure-beli virtuális gépek (VM-EK) biztonsági mentésének elsődleges módszere egy Azure Backup-bővítmény használata a virtuális gépen. A bővítmény biztonsági másolatot készít a teljes virtuális gépről. Ha meghatározott fájlokról és mappákról szeretne biztonsági mentést készíteni egy virtuális gépen belül, telepítheti a MARS-ügynököt az Azure-beli virtuális gépekre. További információ: [architektúra: beépített Azure virtuális gépek biztonsági mentése](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

- **Offline előkészítés**: az Azure-ba irányuló adatok kezdeti teljes biztonsági mentése általában nagy mennyiségű adat átvitelét és nagyobb hálózati sávszélességet igényel. A következő biztonsági másolatok csak a különbözetet, vagy növekményes adatmennyiséget továbbítanak. Azure Backup tömöríti a kezdeti biztonsági mentéseket. Az *Offline kivetés*folyamatán keresztül a Azure Backup lemezek használatával feltölthetik a tömörített kezdeti biztonsági mentési adatok az Azure-ba. További információ: [Azure Backup offline biztonsági mentés Azure Data Box használatával](offline-backup-azure-data-box.md).

## <a name="next-steps"></a>További lépések

[A MARS-ügynök támogatási mátrixa](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[MARS-ügynök – gyakori kérdések](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
