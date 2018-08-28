---
title: Nagy mennyiségű adat áthelyezését és- tárolókról az Azure-beli felhőalapú tárolás |} A Microsoft Docs
description: Adatok áthelyezése az Azure Storage szolgáltatásba vagy onnan a különböző módszerek áttekintése.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/26/2018
ms.author: tamram
ms.component: common
ms.openlocfilehash: 1df237a65a8b5312b20de19a99399b3a3dd075ff
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43049610"
---
# <a name="moving-data-to-and-from-azure-storage"></a>Adatok áthelyezése az Azure Storage szolgáltatásba vagy onnan máshová
Ha meg szeretné áthelyezni a helyszíni adatok Azure Storage (vagy fordítva), nincsenek ehhez többféleképpen. Az Önnek leginkább megfelelő megoldást, amelyen a forgatókönyvtől függ. Ez a cikk gyors áttekintést és különféle forgatókönyvekre és minden egyes megfelelő ajánlatokat nyújtanak.

## <a name="building-applications"></a>Alkalmazások létrehozása
Ha egy alkalmazást fejleszt, a REST API vagy egy a sok klienskódtárak fejlesztését kiválóan alkalmas adatok áthelyezése az Azure Storage szolgáltatásba vagy onnan.

Az Azure Storage klienskódtárakat biztosít számos népszerű nyelvhez, beleértve a .NET, Java, Android, Go, a Xamarin, C++, Node.JS, PHP, Ruby, Python és IOS-es. Az ügyfélkódtárak olyan fejlett képességeket biztosítanak, mint az újrapróbálkozási logika, a naplózás vagy a párhuzamos feltöltések. Fejleszthet közvetlenül a REST API-n is, amely minden, HTTP-/HTTPS-kérelmek létrehozására alkalmas nyelven meghívható.

Lásd: [Azure Blob Storage használatának első lépései](../blobs/storage-dotnet-how-to-use-blobs.md) további.

Emellett azt is kínál a [Azure Storage adatátviteli könyvtár](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) Ez a tár készült nagy teljesítményű másolása az adatok és az Azure-ból. Tekintse meg az adatátviteli könyvtár [dokumentáció](https://github.com/Azure/azure-storage-net-data-movement) további. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Adatai gyors megtekintése, és interakció adataival
Ha azt szeretné, hogy egy legegyszerűbb módja az Azure Storage-adatok feltöltése és az adatok letöltése mellett is, majd fontolja meg egy Azure Storage Explorer használatával.

Tekintse meg a kiválasztott [Azure Storage-tallózók](../storage-explorers.md) további.

## <a name="system-administration"></a>Rendszerfelügyelet
Ha szükséges, vagy egy parancssori eszköz (például a rendszergazdák) felkínálhatja, Íme néhány lehetőség, hogy fontolja meg:

### <a name="azcopy"></a>AzCopy
Az AzCopy parancssori segédprogram az készült nagy teljesítményű másolása az adatok és az Azure Storage-ból. Adatok a tárfiókon belül vagy között különböző tárfiókokban is másolhatja. Az AzCopy érhető el az [Windows](storage-use-azcopy.md) és a [Linux](storage-use-azcopy-linux.md).

Lásd: [adatátvitel az AzCopy parancssori segédprogrammal](storage-use-azcopy.md) vagy [adatok áthelyezése az Azcopyval Linux rendszeren](storage-use-azcopy-linux.md) további.

### <a name="azure-powershell"></a>Azure PowerShell
Az Azure PowerShell modul parancsmagokat biztosít az Azure-szolgáltatások kezeléséhez. Ez egy feladatalapú parancshéj és parancsnyelv, amely kifejezetten rendszer-felügyeleti célra készült.

Lásd: [az Azure PowerShell az Azure Storage](storage-powershell-guide-full.md) további.

### <a name="azure-cli"></a>Azure CLI
Az Azure CLI felülete nyílt forráskódú, platformfüggetlen parancsokat biztosít Azure-szolgáltatások használatához. Az Azure CLI Windows, os x és Linux rendszereken érhető el.

Lásd: [az Azure CLI használatával az Azure Storage](../storage-azure-cli.md) további.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Nagy mennyiségű adat és a lassú hálózat áthelyezése
A legnagyobb kihívás, nagy mennyiségű adat áthelyezését társított egyik átvitelének az idejét. Ha azt szeretné, nem kell bajlódnunk hálózatok költségeit és kód írása nélkül az adatok és-tárolókról az Azure Storage a Azure Import/Export egy megfelelő megoldással.

Lásd: [Azure Import/Export](../storage-import-export-service.md) további.

## <a name="backing-up-your-data"></a>Az adatok biztonsági mentése
Egyszerűen szeretne az Azure Storage-adatok biztonsági mentése, Azure Backup-e a mód. Ez a helyszíni adatok és Azure virtuális gépek biztonsági másolatának egy hatékony megoldás.

Lásd: [Azure Backup](../../backup/backup-introduction-to-azure-backup.md) további.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Az adatok helyszíni hozzáférés és a felhőből
Ha a megoldás való hozzáféréshez szükséges adatokat a helyi és a felhőben, majd érdemes az Azure hibrid felhőalapú tárolási megoldás, a StorSimple használatával. Ez a megoldás áll, hogy intelligensen üzletek gyakran használt adatokat az SSD-k, időnként használt adatokat a HDD-k és az inaktív és biztonsági mentési és archiválási adatok az Azure Storage egy fizikai StorSimple eszközön.

Lásd: [StorSimple](../../storsimple/storsimple-overview.md) további.

## <a name="recovering-your-data"></a>Az adatok helyreállítása
Ha a helyszíni számítási feladatok és alkalmazások, olyan megoldás, amely lehetővé teszi, hogy egy esetleges vészhelyzet esetén is működőképes marad az üzleti lesz szüksége. Az Azure Site Recovery kezeli a replikáció, feladatátvétel és helyreállítás virtuális gépek és fizikai kiszolgálók. A replikált adatokat az Azure Storage, amely lehetővé teszi egy másodlagos helyszíni adatközpontba szükségtelenné teszi tárolódik.

Lásd: [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) további.
### <a name="moving-data-faq"></a>Gyakori kérdések az adatok mozgatása:
## <a name="can-i-migrate-vhds-from-one-region-to-another-without-copying"></a>Áttelepíthetek VHD-k egyik régióból a másikba másolása nélkül?
Másolja a VHD-k régió között csak úgy, hogy az egyes régiókban a storage-fiókok közötti adatokat másolja. Ehhez az AZCopy is használhatja. Tekintse meg az AzCopy parancssori segédprogrammal további információ az adatok átvitele. Nagyon nagy mennyiségű adat is Azure Import/Export. Lásd: [Azure Import/Export](https://docs.microsoft.com/azure/storage/storage-import-export-service) további.
