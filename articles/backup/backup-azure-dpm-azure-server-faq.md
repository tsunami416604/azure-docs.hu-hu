---
title: Azure Backup Server és DPM – gyakori kérdések
description: Ebben a cikkben válaszokat kaphat a Microsoft Azure Backup Server (MABS) és a DPM (Adatvédelmi kezelő) szolgáltatással kapcsolatos gyakori kérdésekre.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/05/2019
ms.openlocfilehash: 35957a1e8a3d6c3d9be06d9d44dbcd47efa0e6ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173159"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Azure Backup Server és DPM – gyakori kérdések

## <a name="general-questions"></a>Általános kérdések

Ez a cikk választ ad az Azure Backup Server és a DPM szolgáltatással kapcsolatos gyakori kérdésekre.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server"></a>Használhatom az Azure Backup kiszolgálót egy operációs rendszer nélküli helyreállítás (BMR) biztonsági másolatának létrehozásához egy fizikai kiszolgálón?

Igen.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Regisztrálhatom a kiszolgálót több tárolóba?

Nem. A DPM vagy az Azure Backup kiszolgáló csak egy tárolóregisztrálható.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Használhatom a DPM-et az Azure Stackben lévő alkalmazások biztonsági csinálására?

Nem. Az Azure Backup segítségével megvédheti az Azure Stacket, az Azure Backup nem támogatja a DPM használatát az Azure Stackben lévő alkalmazások biztonsági mentéséhez.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Ha telepítettem az Azure Backup-ügynököt a fájlok és mappák védelmére, telepíthetem a System Center DPM-et a helyszíni számítási feladatok Azure-ba való biztonsági mentéséhez?

Igen. De először állítsa be a DPM-et, majd telepítse az Azure Backup-ügynököt.  Az összetevők telepítése ebben a sorrendben biztosítja, hogy az Azure Backup ügynök együttműködik a DPM. A DPM telepítése előtt az ügynök telepítése nem ajánlott és nem támogatott.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Miért nem tudok külső DPM-kiszolgálót hozzáadni az UR7 és a legújabb Azure Backup-ügynök telepítése után?

A felhővel védett adatforrásokkal rendelkező DPM-kiszolgálók esetében (a 7. összegző frissítőcsomagnál korábbi összegző frissítőcsomag használatával) legalább egy napot várnia kell az UR7 és a legújabb Azure Backup-ügynök telepítése után a **Külső DPM-kiszolgáló hozzáadása**elindításához. Az egynapos időszak a DPM-védelmi csoportok metaadatainak az Azure-ba való feltöltéséhez szükséges. A védelmi csoport metaadatai az első alkalommal egy éjszakai feladaton keresztül töltődnek fel.

## <a name="vmware-and-hyper-v-backup"></a>VMware és Hyper-V biztonsági mentés

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Tudok biztonsági mentést készíteni VMware vCenter-kiszolgálókról az Azure-ban?

Igen. Az Azure Backup Server segítségével biztonsági másolatot készíthet a VMware vCenter Server és ESXi-állomásokról az Azure-ba.

- [További információ](backup-mabs-protection-matrix.md) a támogatott verziókról.
- A VMware-kiszolgáló biztonsági és biztonsági biztonsági rendszerének biztonsági leéséhez [kövesse az alábbi lépéseket.](backup-azure-backup-server-vmware.md)

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Külön licencre van szükségem a teljes helyszíni VMware/Hyper-V fürt helyreállításához?

A VMware/Hyper-V védelemhez nincs szükség külön licencelésre.

- Ha Ön System Center-ügyfél, használja a System Center Data Protection Manager (DPM) szolgáltatást a VMware virtuális gépek védelméhez.
- Ha ön nem system center-ügyfél, használhatja az Azure Backup Server (használatalapú fizetés) segítségével a VMware virtuális gépek védelmére.

## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Helyreállítható kontinamányos SharePoint-elem az eredeti helyre, ha a SharePoint az SQL AlwaysOn (lemezen védelemmel) használatával van konfigurálva?

Igen, az elem visszavezethető az eredeti SharePoint-webhelyre.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Helyreállítható kontinamányos SharePoint-adatbázis az eredeti helyre, ha a SharePoint az SQL AlwaysOn használatával van konfigurálva?

Mivel a SharePoint-adatbázisok SQL AlwaysOn nyelven vannak konfigurálva, csak akkor módosíthatók, ha a rendelkezésre állási csoportot eltávolították. Ennek eredményeképpen a DPM nem tudja visszaállítani az adatbázist az eredeti helyre. Az SQL Server-adatbázis egy másik SQL Server-példányba állítható helyre.

## <a name="next-steps"></a>További lépések

További gyakori kérdéseket is áttekinthet:

- [További információ](backup-support-matrix-mabs-dpm.md) az Azure Backup Server és a DPM támogatási mátrixáról.
- [További információ](backup-azure-mabs-troubleshoot.md) az Azure Backup Server és a DPM hibaelhárítási irányelveiről.
