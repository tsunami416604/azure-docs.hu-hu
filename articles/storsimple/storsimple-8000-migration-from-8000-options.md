---
title: Adatáttelepítési lehetőségek a StorSimple 8000 sorozatú eszközökről
description: Áttekintést nyújt a StorSimple 8000 sorozatból származó adatok áttelepítésének lehetőségeiről.
services: storsimple
author: priestlg
ms.service: storsimple
ms.topic: article
ms.date: 03/25/2020
ms.author: v-grpr
ms.openlocfilehash: f712486c8035bbc913fbd229759f3415a1005449
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80438319"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>A StorSimple 8000 sorozatból származó adatok áttelepítésének lehetőségei

> [!IMPORTANT]
> 2022. december 31-én a StorSimple 8000 sorozat eléri a támogatási (EOS) státuszt. Azt javasoljuk, hogy a StorSimple 8000 sorozatú ügyfelek a dokumentumban ismertetett alternatívák egyikére térjenek át.

A StorSimple 8000 sorozat 2022 decemberében [ér véget a támogatásnak.](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) A StorSimple 8000 sorozatot futtató ügyfelek más Azure-beli hibrid szolgáltatásokra is frissíthetnek. Ez a cikk ismerteti az Azure hibrid lehetőségek az adatok áttelepítéséhez elérhető.

## <a name="migration-options"></a>Migrálási lehetőségek

A StorSimple 8000 sorozatot használó ügyfelek Azure-beli vagy külső gyártótól származó lehetőségekkel rendelkeznek.

### <a name="azure-options"></a>Azure-lehetőségek

#### <a name="migrate-to-azure-file-sync"></a>Migrálás az Azure File Syncre

Ez a vadonatúj áttelepítési beállítás lehetővé teszi az ügyfelek számára, hogy a szervezet fájlmegosztásait az Azure Files-ban tárolják. Ezek a fájlmegosztások ezután központosított a helyszíni hozzáférés az Azure File Sync (AFS) használatával. Az AFS telepíthető Windows Server-állomásra. A tényleges adatáttelepítés ezután gazdagépmásolatként vagy az áttelepítési eszköz használatával történik.

Az adatok Azure File Sync szolgáltatásba való áttelepítéséről a [StorSimple 8100 és 8600 áttelepítése az Azure File Sync szolgáltatásba](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)című webhelyen talál további információt.

<!-- 03/25/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
### Third-party options

#### Migrate to Panzura Freedom NAS

StorSimple 5000-7000 customers can choose to migrate to Panzura Freedom NAS to keep their data in Azure. Panzura Freedom solution provides a NAS solution that spans datacenters, offices, public and private clouds. The solution enables local, hybrid, and in-cloud data workflows for NFS, SMB, and mobile clients. 

This migration is supported by Panzura and customers can get started by requesting migration support from the [Panzura website](https://panzura.com/storsimple-migration/).

#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>Migráció - Gyakori kérdések

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>K. Mikor érik el a StorSimple 8000 sorozatú eszközök a szolgáltatás végét?

A. A StorSimple 8000 sorozat 2022 decemberében [ér véget a támogatásnak.](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series) A támogatás megszűnése azt jelenti, hogy a Microsoft 2022 decembere után már nem lesz képes támogatást nyújtani az eszközök hardveréhez és szoftveréhez. Javasoljuk, hogy most kezdje el az adatok áttelepítésének tervét az eszközökről.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>K. Mi történik az Azure-ban tárolt adatokkal?  

A. Az adatok továbbra is használhatók az Azure-ban, miután áttelepítette őket egy újabb szolgáltatásba.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>K. Mi történik a StorSimple-eszközön helyileg tárolt adatokkal?

A. A helyi eszközön lévő adatok az áttelepítési dokumentumokban leírtak szerint átmásolhatók az újabb szolgáltatásba.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>K. Mi történik, ha meg szeretném tartani a StorSimple 8000 sorozatú készülékemet?

A. Bár a szolgáltatások továbbra is működhetnek, a Microsoft a továbbiakban nem tud hardver- és szoftvertámogatást nyújtani. Az áttelepítés erősen ajánlott az üzletmenet folytonossága érdekében.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>K. Milyen lehetőségek állnak rendelkezésre az adatok StorSimple 8000 sorozatú eszközökről történő áttelepítésére?

A. A forgatókönyvtől függően a StorSimple 8000 sorozat felhasználói a következő áttelepítési lehetőségekkel rendelkeznek:

* **Migrálás az Azure File Sync:** Használja ezt a beállítást, ha át szeretne váltani az Azure natív formátumban. Az Azure File Sync segítségével a fájlmegosztások központi felügyeletéhez.

* **Egyéb lehetőségek**: A Microsoft támogatási szolgálatához fordulhat, hogy megbeszéljék az itt nem felsorolt áttelepítési lehetőségeket.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>K. Támogatott a más tárolási megoldásokra való áttérés?

A. Igen. Az adatok gazdapéldányát használó más tárolási megoldásokra való áttelepítés támogatott.

### <a name="q-is-migration-supported-by-microsoft"></a>K. Támogatja az áttelepítést a Microsoft?

A. A 8000-es sorozatból való áttelepítés teljes mértékben támogatott művelet. A Microsoft azt javasolja, hogy az áttelepítés megkezdése előtt a Microsoft elérje a támogatási felet. Az áttelepítés jelenleg egy támogatott művelet. Ha adatokat kíván áttelepíteni a StorSimple 8000 sorozatú eszközről, forduljon a [StorSimple támogatási szolgálatához.](mailto:storsimp@microsoft.com)

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>K. Mi az Azure File Sync-be való migrálás díjszabási modellje?

A. Az Azure File Sync használata esetén a szolgáltatás előfizetési díjai is kimerülhetnek. Az ügyfeleknek folyamatos tárolási költségeket is kell fizetniük. Becslésért tekintse meg az [AFS-díjszabást.]( https://azure.microsoft.com/pricing/details/storage/files/)

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>K. Mennyi ideig tart az áttelepítés befejezése?

A. Az adatok áttelepítésének ideje az adatok mennyiségétől és a kiválasztott frissítési beállítástól függ.

## <a name="next-steps"></a>További lépések

* [Adatok áttelepítése StorSimple 8000 sorozatból az Azure File Sync szolgáltatásba](https://docs.microsoft.com/azure/storage/files/storage-files-migration-storsimple-8000)
