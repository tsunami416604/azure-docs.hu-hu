---
title: Az Azure Backup Server és a DPM – gyakori kérdések
description: 'Gyakori kérdésekre adott válaszok: Az Azure Backup Server és a DPM.'
services: backup
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: srinathv
ms.openlocfilehash: 7a598038ee435b67b9ad8f06bdec2490bc1c53c3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705102"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Az Azure Backup Server és a DPM - – gyakori kérdések
Ez a cikk az Azure Backup Server és a DPM kapcsolatos gyakori kérdésekre ad választ.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>Használhatom az Azure Backup kiszolgálót egy operációs rendszer nélküli helyreállítás (BMR) biztonsági másolatának létrehozásához egy fizikai kiszolgálón? <br/>
Igen.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>Regisztrálhatom a kiszolgálómat több tárolóhoz?
Nem. A DPM vagy az Azure Backup server csak egy tárolóhoz regisztrálhatók.


### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>A DPM használatával biztonsági másolatot készíteni az alkalmazásokat az Azure Stack?
Nem. Az Azure Backup segítségével megvédheti az Azure Stack, az Azure Backup nem támogatja a DPM biztonsági mentése az Azure Stackben alkalmazások használatával.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>Telepítettem az Azure Backup ügynököt a fájljaim és Mappáim védelme, ha telepíthető System Center DPM biztonsági mentése a helyszíni számítási feladatokat az Azure-bA?
Igen. De kell először állítsa be a DPM, és ezután telepítheti az Azure Backup ügynököt.  Ebben a sorrendben összetevők telepítése biztosítja, hogy az Azure Backup ügynök működik a dpm-mel. Az ügynök telepítése a DPM telepítése előtt nem javasolt vagy támogatott.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>Miért nem lehet külső DPM-kiszolgáló felvétele UR7 és a legújabb Azure Backup-ügynök telepítése után?
A DPM-kiszolgálók a felhőben (használatával a kumulatív frissítések kumulatív frissítés 7-nél régebbi) védett adatforrások esetében kellene, indítsa el a UR7 és a legújabb Azure Backup-ügynök telepítése után legalább egy napot várnia kell **külső DPM hozzáadása server**. A DPM védelmi csoportok a metaadatok feltöltése az Azure-bA az egynapos időszakban van szükség. Védelmi csoport metaadatai feltöltődtek először egy éjszakai feladattal keresztül.

## <a name="vmware-and-hyper-v-backup"></a>A VMware és Hyper-V biztonsági mentés

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>Tudok biztonsági mentést készíteni VMware vCenter-kiszolgálókról az Azure-ban?
Igen. Az Azure Backup Server használatával biztonsági másolatot készíteni VMware vCenter-kiszolgáló és az ESXi-gazdagépek Azure-bA.

- [További](backup-mabs-protection-matrix.md) kapcsolatos támogatott verziója.
- [Az alábbi lépésekkel](backup-azure-backup-server-vmware.md) egy VMware-kiszolgálók biztonsági mentéséhez.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>Kell helyreállítani egy teljes helyi VMware vagy Hyper-V fürt különálló licenc?
Nem különálló van szüksége a licencelés VMware vagy Hyper-V-védelem.

- Ha egy System Center-előfizető, VMware virtuális gépek védelme a System Center Data Protection Manager (DPM) használatával.
- Ha egy System Center-ügyfél nem, az Azure Backup Server (használatalapú fizetés) használhatja a VMware virtuális gépek védelméhez.


## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>Tudok helyreállítani egy SharePoint-elem az eredeti helyre, ha a SharePoint SQL AlwaysOn (a védelem a lemezen) használatával van konfigurálva?
Igen, az elem visszaállíthatja az eredeti SharePoint-webhelyre.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>Tudok helyreállítani egy SharePoint-adatbázis az eredeti helyre, ha a SharePoint SQL AlwaysOn használatára van konfigurálva?
A SharePoint-adatbázisok az SQL AlwaysOn vannak konfigurálva, mivel azok nem lehet módosítani, ha a rendszer eltávolítja a rendelkezésre állási csoport. Ennek eredményeképpen az a DPM nem tudja visszaállítani az adatbázis az eredeti helyre. SQL Server-adatbázis egy másik SQL Server-példányra helyreállítható.

## <a name="next-steps"></a>További lépések

Olvassa el a gyakori kérdések:

- [További](backup-support-matrix-mabs-dpm.md) hamarosan az Azure Backup Server és a DPM támogatási mátrixa.
- [További](backup-azure-mabs-troubleshoot.md) az Azure Backup Server és a DPM hibaelhárítási irányelvek.
