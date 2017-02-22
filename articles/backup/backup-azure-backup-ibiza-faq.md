---
title: "Recovery Services-tároló – gyakori kérdések | Microsoft Docs"
description: "A GYIK ezen verziója támogatja az Azure Backup szolgáltatás Nyilvános előzetes kiadását. Megválaszolja a biztonsági mentési ügynökre, a biztonsági mentésre és a megtartásra, helyreállításra, biztonságra és egyéb, a biztonsági mentéssel és az Azure Backup megoldással kapcsolatos általánosságokra vonatkozó gyakran ismételt kérdéseket."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "biztonsági mentési megoldás; biztonsági mentési szolgáltatás"
ms.assetid: 5f55b500-1ee9-4f64-9306-02d6f7a8eded
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/21/2016
ms.author: markgal;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: e12d533ac5befe020c0aad4aa64ca9ed50349c3d
ms.openlocfilehash: 271f447a36f42330ed7b8dea78b32c06eebdeda6


---
# <a name="recovery-services-vault---faq"></a>Recovery Services-tároló – gyakori kérdések
Ez a cikk a Recovery Services-tárolóra vonatkozó információkat tartalmaz, és az [Azure Backup GYIK](backup-azure-backup-faq.md) kiegészítéséül szolgál. Az Azure Backup GYIK tartalmazza az Azure Backup szolgáltatással kapcsolatos kérdéseket és válaszokat.  

Az Azure Backup szolgáltatással kapcsolatban ezen vagy egy kapcsolódó cikk Disqus szakaszában tehet fel kérdéseket. Emellett egy fórumbejegyzésben is feltehet kérdéseket az Azure Backup szolgáltatással kapcsolatban a [vitafórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>A helyreállítási tárak a Resource Manageren alapulnak. A biztonsági mentési tárak (klasszikus módban) továbbra is támogatottak? <br/>
Igen, a Backup tárolók még támogatottak. Backup tárolókat a [klasszikus portálon](https://manage.windowsazure.com) lehet létrehozni. Recovery Services-tárolókat az [Azure Portalon](https://portal.azure.com) lehet létrehozni. Határozottan javasolt azonban Recovery Services-tárolót létrehozni, mivel a jövőbeli fejlesztések csak Recovery Services-tárolókban lesznek elérhetők.

## <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Áttelepíthetek egy Backup tárolót egy Recovery Services-tárolóra? <br/>
Sajnos nem, jelenleg nem telepíthető át egy Backup tároló tartalma egy Recovery Services-tárolóra. Jelenleg is dolgozunk ezen funkción, de a Nyilvános előzetes kiadásnak nem része.

## <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>A Recovery Services-tárolók támogatják a klasszikus vagy a Resource Manager alapú virtuális gépeket? <br/>
A Recovery Services-tárolók mindkét modellt támogatják.  Mind a klasszikus portálon létrehozott (vagyis klasszikus módú), mind az Azure Portalon létrehozott (vagyis Resource Manager alapú) virtuális gépekről készíthet biztonsági mentést egy Recovery Services-tárolóba.

## <a name="i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode--how-can-i-backup-them-in-recovery-services-vault"></a>Biztonsági másolatot készítettem a klasszikus virtuális gépemről a Backup-tárolóban. Most át szeretném telepíteni a virtuális gépeimet a klasszikus módból Resource Manager módba.  Hogyan készíthetek róluk biztonsági másolatot a Recovery Services-tárolóban?
A klasszikus virtuális gépek biztonsági másolatai a Backup tárolóból nem telepíthetők át automatikusan a Recovery Services-tárolóba, amikor a virtuális gépeket a klasszikusból Resource Manager módba telepíti át. Kövesse a virtuális gép biztonsági mentéseinek áttelepítésére vonatkozó lépéseket:

1. A Backup-tárolóban lépjen a **Protected Items** (Védett elemek) lapra, és válassza ki a virtuális gépet. Kattintson a [Stop Protection](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines) (Védelem leállítása) gombra. Hagyja a *Delete associated backup data* (Társított biztonsági mentési adatok törlése) beállítást **bejelöletlenül**.
2. Telepítse át a virtuális gépet a klasszikus módból a Resource Manager módba. A virtuális gépnek megfelelő tárolót és hálózatot is mindenképpen telepítse át Resource Manager módba.
3. Hozzon létre egy Recovery Services-tárolót, és konfigurálja a biztonsági mentést az áttelepített virtuális gépen a tároló irányítópultjának tetejénél található **Biztonsági mentés** művelettel. További információk a [biztonsági mentés engedélyezéséről a Recovery Services-tárolóban](backup-azure-vms-first-look-arm.md).



<!--HONumber=Feb17_HO3-->


