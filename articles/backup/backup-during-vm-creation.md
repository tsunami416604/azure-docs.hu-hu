---
title: Biztonsági mentés engedélyezése Azure-beli virtuális gép létrehozásakor
description: Bemutatja, hogyan engedélyezheti a biztonsági mentést, amikor azure-beli virtuális gép létrehozása az Azure Backup használatával.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 0cfea6579791c4fd23c1b7acdfe722d57b5ec2fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247851"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Biztonsági mentés engedélyezése Azure-beli virtuális gép létrehozásakor

Az Azure Backup szolgáltatás használatával készítsen biztonsági másolatot az Azure virtuális gépekről.Use the Azure Backup service to back up Azure virtual machines (VMs). A virtuális gépekbiztonsági mentéséről a biztonsági mentési házirendben megadott ütemezés szerint készülnek, és helyreállítási pontok jönnek létre biztonsági mentések. A helyreállítási pontok a Helyreállítási szolgáltatások tárolóiban tárolódnak.

Ez a cikk részletezi, hogyan engedélyezheti a biztonsági mentést, amikor virtuális gépet (VM) hoz létre az Azure Portalon.  

## <a name="before-you-start"></a>Előkészületek

- [Ellenőrizze,](backup-support-matrix-iaas.md#supported-backup-actions) hogy mely operációs rendszerek támogatottak, ha engedélyezi a biztonsági mentést, amikor virtuális gép létrehozásakor engedélyezi.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha még nem jelentkezett be a fiókjába, jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="create-a-vm-with-backup-configured"></a>Virtuális gép létrehozása konfigurált biztonsági másolattal

1. Az Azure Portalon kattintson **az Erőforrás létrehozása**elemre.

2. Az Azure Piactéren kattintson a **Számítási gombra,** majd válasszon ki egy virtuális géplemezképet.

3. Állítsa be a virtuális gép a [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) [vagy](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) linuxos utasításoknak megfelelően.

4. A **Kezelés** lap **Biztonsági mentés engedélyezése**területén kattintson **a Be gombra.**
5. Az Azure Backup biztonsági mentések egy Recovery Services-tárolóba. Kattintson **az Új létrehozása gombra,** ha nem rendelkezik meglévő tárolóval.
6. Fogadja el a javasolt tároló nevét, vagy adja meg a saját.
7. Adja meg vagy hozzon létre egy erőforráscsoportot, amelyben a tároló található lesz. Az erőforráscsoport-tároló eltérhet a virtuális gép erőforráscsoporttól.

    ![Biztonsági mentés engedélyezése virtuális géphez](./media/backup-during-vm-creation/enable-backup.png)

8. Fogadja el az alapértelmezett biztonsági mentési házirendet, vagy módosítsa a beállításokat.
    - A biztonsági mentési szabályzat határozza meg, hogy milyen gyakran készítsen biztonsági mentési pillanatképeket a virtuális gépről, és mennyi ideig őrizze meg ezeket a biztonsági másolatokat.
    - Az alapértelmezett házirend naponta egyszer biztonsági másolatot ad a virtuális gépről.
    - Testreszabhatja a saját biztonsági mentési szabályzategy Azure virtuális gép, hogy a biztonsági mentések naponta vagy hetente.
    - [További információ](backup-azure-vms-introduction.md#backup-and-restore-considerations) az Azure-beli virtuális gépek biztonsági mentési szempontjairól.
    - [További információ](backup-instant-restore-capability.md) az azonnali visszaállítás funkcióról.

      ![Alapértelmezett biztonsági mentési házirend](./media/backup-during-vm-creation/daily-policy.png)

## <a name="azure-backup-resource-group-for-virtual-machines"></a>Azure Biztonsági másolat erőforráscsoport virtuális gépekhez

A biztonsági mentési szolgáltatás létrehoz egy külön erőforráscsoportot (RG), amely eltér a virtuális gép erőforráscsoportjától a visszaállítási pontgyűjtemény (RPC) tárolásához. Az RPC a felügyelt virtuális gépek azonnali helyreállítási pontjait is eltalizittének ad otthont. A Biztonsági másolat szolgáltatás által létrehozott erőforráscsoport `AzureBackupRG_<Geo>_<number>`alapértelmezett elnevezési formátuma: . Például: *AzureBackupRG_northeurope_1*. Most már testreszabhatja az Azure Backup által létrehozott erőforráscsoport nevét.

Megjegyzésre mutat:

1. Használhatja az RG alapértelmezett nevét, vagy szerkesztheti a vállalati követelményeknek megfelelően.
2. Az RG névmintát adja meg a virtuális gép biztonsági mentési házirendjének létrehozása során. Az RG névformátumnak a `<alpha-numeric string>* n <alpha-numeric string>`következő formátumúnak kell lennie: . Az 'n' helyére egy egész szám kerül (1-től kezdődően), és a horizontális felskálázásra szolgál, ha az első RG megtelt. Egy RG-nek ma legfeljebb 600 RFC-je lehet.
              ![Név kiválasztása házirend létrehozásakor](./media/backup-during-vm-creation/create-policy.png)
3. A mintának az alábbi RG elnevezési szabályokat kell követnie, és a teljes hossz nem haladhatja meg a maximálisan megengedett RG névhosszt.
    1. Az erőforráscsoportok nevei csak alfanumerikus karaktereket, pontokat, aláhúzásjeleket, kötőjeleket és zárójeleket engedélyezik. Nem végződhetnek egy időszakban.
    2. Az erőforráscsoport nevek legfeljebb 74 karaktert tartalmazhatnak, beleértve az RG nevét és az utótagot.
4. Az `<alpha-numeric-string>` első kötelező, míg a második az "n" után nem kötelező. Ez csak akkor érvényes, ha személyre szabott nevet ad meg. Ha egyik szövegdobozba sem ír be semmit, a program az alapértelmezett nevet használja.
5. Az RG nevét szükség esetén módosíthatja a házirend módosításával. Ha a névminta megváltozik, új rp-k jönnek létre az új RG-ben. Azonban a régi r-k továbbra is a régi RG-ben maradnak, és nem kerülnek áthelyezésre, mivel az RP-gyűjtemény nem támogatja az erőforrás-áthelyezést. Végül a RPs kap szemetet gyűjtött, mint a pontok lejár.
![Név módosítása házirend módosításakor](./media/backup-during-vm-creation/modify-policy.png)
6. Azt tanácsoljuk, hogy ne zárolja a biztonsági mentési szolgáltatás által használatra létrehozott erőforráscsoportot.

## <a name="start-a-backup-after-creating-the-vm"></a>Biztonsági mentés indítása a virtuális gép létrehozása után

A virtuális gép biztonsági mentése a biztonsági mentési szabályzatnak megfelelően fog futni. Azonban azt javasoljuk, hogy futtasson egy kezdeti biztonsági mentést.

A virtuális gép létrehozása után tegye a következőket:

1. A virtuális gép tulajdonságai között kattintson a **Biztonsági másolat gombra.** A virtuális gép állapota kezdeti biztonsági mentés függőben, amíg a kezdeti biztonsági mentés nem fut
2. Kattintson **a Biztonsági másolat most** gombra az igény szerinti biztonsági mentés futtatásához.

    ![Igény szerinti biztonsági mentés futtatása](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Védett virtuális gép üzembe helyezése Erőforrás-kezelő sablon használatával

Az előző lépések bemutatják, hogyan használhatja az Azure Portalon egy virtuális gép létrehozásához és a Recovery Services-tárolóban való védelméhez. Ha gyorsan szeretne telepíteni egy vagy több virtuális gépet, és védeni szeretné őket a Recovery Services-tárolóban, olvassa el a [Windows virtuális gép telepítése és a biztonsági mentés engedélyezése sablont.](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)

## <a name="next-steps"></a>További lépések

Most, hogy megvédte a virtuális gép, megtanulják, hogyan kezelheti és állítsa vissza őket.

- [Virtuális gépek kezelése és monitorozása](backup-azure-manage-vms.md)
- [Virtuális gép visszaállítása](backup-azure-arm-restore-vms.md)

Ha bármilyen problémát tapasztal, [tekintse át](backup-azure-vms-troubleshoot.md) a hibaelhárítási útmutatót.
