---
title: VMware virtuális gépek Linux rendszerű áttelepítés az Azure-bA az Azure Migrate kiszolgáló áttelepítésének előkészítése |} A Microsoft Docs
description: Ismerteti, hogyan lehet Linux rendszerű virtuális gép előkészítése az áttelepítésre az Azure-bA az Azure Migrate-kiszolgáló áttelepítése
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/14/2018
ms.author: raynew
ms.openlocfilehash: efc410699ef6f4722857c812b38473c8f54b911b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811790"
---
# <a name="prepare-linux-vmware-vms-for-migration-to-azure"></a>Linux VMware virtuális gépek előkészítése migráláshoz 

Ez a cikk azt ismerteti, hogyan készítse elő a VMware virtuális gépeken Linux rendszert futtató, amikor azok Azure-bA migrálni kívánt [Azure Migrate](migrate-overview.md). 

> [!NOTE]
> Azure Migrate Server Migrálási jelenleg nyilvános előzetes verzióban érhető el. A meglévő általánosan elérhető verzióját az Azure Migrate használatával felderítése és értékelése a virtuális gépek migrálásra, de a tényleges áttelepítése a meglévő GA verzióban nem támogatott.

Linux rendszerű gépek készítse elő a következőképpen:

1. Telepítse a Hyper-V Linux Integration Services. Linux-disztribúciók újabb verzióiban előfordulhat ez alapértelmezés szerint telepítve van).
2. A Linux init rendszerkép újraépítése, így tartalmazza a szükséges Hyper-V illesztőprogramok, és úgy, hogy a virtuális gép az Azure-ban (szükséges az egyes disztribúciók) fog elindulni.
3. Soros konzol naplózás engedélyezése a hibaelhárítás. [További információk](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console).
4. Frissítse a térkép eszközfájl társítást, állandó készülékazonosítók használata az eszköz neve.
5. Frissítse az fstab bejegyzések tartós kötet azonosítókat használjon.
6. Foglaljon le a MAC-cím stb alapján illesztőneveket udev a szabály eltávolításához.
7. Frissítés fogadó hálózati adapterek DHCP IP-címeket.
8. Győződjön meg, hogy ssh engedélyezve van. Ellenőrizze, hogy sshd service újraindításkor automatikus indításra van állítva.
9. Győződjön meg arról, hogy a bejövő ssh csatlakozási kérelmek nem blokkolja az operációs rendszer tűzfal- vagy IP-tábla szabályokat.

[További](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) kapcsolatos módosítások elvégzése a népszerű Linux-disztribúciókon.

## <a name="sample-script"></a>Példaszkript

Ez a szkript (előkészítése-az-azure.sh) minta biztosít a Linux rendszerű gépek előkészítése. A parancsfájl nem működik az összes disztribúciók és környezeteket, de hasznos kiindulópontot jelenthet.

A szkript bemutatja, hogyan: 

- Generálja újra a szükséges illesztőprogramok, szükség esetén a Linux init rendszerképet.
- Frissítse az fstab bejegyzések tartós kötet azonosítókat használjon.
- Konzol naplók átirányítása a soros port.
- Konzol Azure soros hozzáférés engedélyezése
- Udev nettó szabályok törlése
- Az indítási szkript, amely akkor fut, amikor a virtuális gép generációazonosítójának futtató helyezhet el. Azt ellenőrzi, hogy a gép fut-e az Azure-ban. Ha igen, a hálózati konfigurációt a virtuális gép a frissítések, és beállítja az első ethernet-adapter IP-címet DHCP segítségével.

### <a name="before-you-start"></a>Előkészületek

- A minta parancsfájl tartalmazza a mintául szolgáló lépéseket. Az éles rendszerekre nem futtatható. Sikerült károkat vagy sérült a virtuális gép, amelyen futtatja.
- Azt javasoljuk, hogy a tesztelési virtuális gép futtatásához. Mielőtt elkezdené, igénybe vehet egy virtuális gép biztonsági mentése vagy egy pillanatképet úgy, hogy szükség esetén visszaállíthatja a virtuális gép. 
- A szkript akkor működik, ha a virtuális gép fut, a Linux-disztribúciók egyikét:
    - Red Hat Enterprise Linux 6.5-ös + 7.1 +
    - Centos 6.5-ös + 7.1 +
    - SUSE Linux Enterprise Server 12 SP1, SP2 SP3
    - Ubuntu 14.04, 16.04, 18.04
    - Debian 7, 8

### <a name="run-the-script"></a>A szkript futtatása

1. Másolás a szkriptet a Linux virtuális gép sftp vagy egy scp-ügyfél, például Filezillát vagy a WinScp segítségével tesztelje.
2. SSH-t a Linux-gép, egy rendszergazdai fiók használatával.
3. Keresse meg a parancsfájl könyvtárát.
4. Ahhoz, hogy a parancsfájl egy végrehajtható fájlba, futtassa **sudo chmod 777 előkészítése-az-azure.sh**.
5. Futtassa a szkriptet a **./prepare-for-azure.sh**.

Itt látható, hogy a parancsfájl futtatása:

![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script1.png)
![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script2.png)
![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script3.png)
![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script4.png)
![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script5.png)
![Linux parancsfájl ](./media/how-to-prepare-linux-for-migration/script6.png)
 ![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script7.png)
![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script8.png)
![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script9.png)
![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script10.png)
![Linux parancsfájl ](./media/how-to-prepare-linux-for-migration/script11.png)
 ![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script12.png)
![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script13.png)
![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script14.png)
![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script15.png)
![Linux parancsfájl ](./media/how-to-prepare-linux-for-migration/script16.png)
 ![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script17.png)
![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script18.png)
![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script19.png)
![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script20.png)
![Linux parancsfájl ](./media/how-to-prepare-linux-for-migration/script21.png)
 ![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script22.png)
![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script23.png)
![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script24.png)
![Linux parancsfájl](./media/how-to-prepare-linux-for-migration/script25.png)



## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan használható [gépfüggőségi leképezések](how-to-create-group-machine-dependencies.md) nagy megbízhatóságú csoportokat létrehozni.
- [További információk](concepts-assessment-calculation.md) az értékelések számításával kapcsolatban.
