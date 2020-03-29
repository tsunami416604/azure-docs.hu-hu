---
title: A MARS-ügynökről
description: Ismerje meg, hogyan támogatja a MARS-ügynök a biztonsági mentési forgatókönyveket
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d2cc8e32152f6930c9c250e2811668cc2c924616
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673296"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>A Microsoft Azure Recovery Services (MARS) ügynök

Ez a cikk azt ismerteti, hogy az Azure Backup szolgáltatás hogyan használja a Microsoft Azure Recovery Services (MARS) ügynök fájlok, mappák és a kötet vagy a rendszer állapotát egy helyszíni számítógépről az Azure-ba.

A MARS-ügynök a következő biztonsági mentési forgatókönyveket támogatja:

![MARS biztonsági mentési forgatókönyvek](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Fájlok és mappák**: A Windows-fájlok és -mappák szelektív védelme.
- **Hangerőszint:** A készülék teljes Windows-kötetének védelme.
- **Rendszerszint:** A Windows teljes rendszerállapotának védelme.

A MARS-ügynök a következő visszaállítási forgatókönyveket támogatja:

![MARS helyreállítási forgatókönyvek](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Ugyanaz a kiszolgáló**: Az a kiszolgáló, amelyen a biztonsági másolat eredetileg készült.
  - **Fájlok és mappák:** Válassza ki a visszaállítani kívánt fájlokat és mappákat.
  - **Hangerőszint:** Válassza ki a visszaállítani kívánt kötetet és helyreállítási pontot, majd állítsa vissza ugyanarra a helyre vagy egy másik helyre ugyanazon a számítógépen.  Hozzon létre egy másolatot a meglévő fájlokról, írja felül a meglévő fájlokat, vagy hagyja ki a meglévő fájlok helyreállítását.
  - **Rendszerszint**: Válassza ki a rendszerállapotot és a helyreállítási pontot, ha egy adott helyen vissza szeretné állítani ugyanarra a gépre.

- **Alternatív kiszolgáló**: Nem az a kiszolgáló, amelyen a biztonsági másolat készült.
  - **Fájlok és mappák:** Válassza ki azokat a fájlokat és mappákat, amelyek helyreállítási pontját vissza szeretné állítani a célgépre.
  - **Hangerő:** Válassza ki azt a kötetet és helyreállítási pontot, amelyet vissza szeretne állítani egy másik helyre. Hozzon létre egy másolatot a meglévő fájlokról, írja felül a meglévő fájlokat, vagy hagyja ki a meglévő fájlok helyreállítását.
  - **Rendszerszint**: Válassza ki a rendszerállapotot és a helyreállítási pontot, ha rendszerállapot-fájlként szeretne visszaállítani egy másik gépre.

## <a name="backup-process"></a>A biztonsági mentés folyamata

1. Az Azure Portalon hozzon létre egy [Recovery Services-tárolót,](install-mars-agent.md#create-a-recovery-services-vault)és válassza ki a fájlokat, mappákat és a rendszerállapotot a biztonsági mentési célok közül.
2. [Töltse le a Recovery Services-tároló hitelesítő adatait és az ügynöktelepítőt](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) egy helyszíni számítógépre.

    A helyszíni gép védelméhez a Biztonsági mentés beállítás kiválasztásával válassza ki a fájlokat, mappákat és a rendszerállapotot, majd töltse le a MARS-ügynököt.

3. Az infrastruktúra előkészítése:

    a. Futtassa a telepítőt [az ügynök telepítéséhez](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent).

    b. A letöltött tároló hitelesítő adataival regisztrálhatja a gépet a Helyreállítási szolgáltatások tárolójában.
4. Az ügyfél ügynökkonzoljáról [konfigurálja a biztonsági mentést.](https://docs.microsoft.com/azure/backup/backup-windows-with-mars-agent#create-a-backup-policy) Adja meg a biztonsági mentési adatok adatmegőrzési házirendjét a védelem megkezdéséhez.

![Azure biztonsági mentési ügynök diagramja](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-scenarios"></a>További helyzetek

- **Készítsen biztonsági másolatot bizonyos fájlokról és mappákról az Azure virtuális gépeken belül:** Az Azure virtuális gépek (VM-ek) biztonsági mentésének elsődleges módja egy Azure Backup-bővítmény használata a virtuális gépen. A bővítmény biztonsági másolatot ad a teljes virtuális gépről. Ha egy virtuális gépen belül bizonyos fájlokról és mappákról szeretne biztonsági másolatot, telepítheti a MARS-ügynököt az Azure virtuális gépeken. További információ: [Architecture: Beépített Azure VM Backup.](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup)

- **Offline vetés:** Az adatok kezdeti teljes biztonsági mentése az Azure-ba általában nagy mennyiségű adatot továbbít, és nagyobb hálózati sávszélességet igényel. Az ezt követő biztonsági mentések csak a különbözeti vagy növekményes adatmennyiséget továbbítják. Az Azure Backup tömöríti a kezdeti biztonsági mentéseket. Az offline *vetés*folyamata során az Azure Backup lemezek használatával töltheti fel a tömörített kezdeti biztonsági mentési adatokat offline az Azure-ba. További információ: [Azure Backup offline-backup using Azure Data Box](offline-backup-azure-data-box.md).

## <a name="next-steps"></a>További lépések

[A MARS-ügynök támogatási mátrixa](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[MARS-ügynök – GYIK](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
