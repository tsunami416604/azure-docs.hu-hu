---
title: "Nagy mennyiségű adat áthelyezését, onnan felhőalapú tárolás az Azure-ban |} Microsoft Docs"
description: "A különböző módszereket a megköveteli az adatok és az Azure Storage áttekintése."
services: storage
documentationcenter: 
author: JarrettRenshaw
manager: msmets
editor: tysonn
ms.assetid: 5e3947a9-d99b-4108-9d57-3eb67c03e7ba
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: jarrettr
ms.openlocfilehash: ba390a5973ad33405f1d4217d60d7989f04db3b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="moving-data-to-and-from-azure-storage"></a>Adatok áthelyezése az Azure Storage szolgáltatásba vagy onnan máshová
Ha azt szeretné, hogy helyezze át a helyszíni adatok Azure Storage (vagy fordítva), nincsenek többféleképpen ehhez. A megközelítés, amely a legjobban a forgatókönyvtől függ. Ez a cikk különböző alkalmazási helyzetek és minden egyes megfelelő ajánlatok gyors áttekintést biztosít.

## <a name="building-applications"></a>Alkalmazások létrehozása
Ha egy alkalmazás, a REST API fejlesztésről most felépítése, vagy a sok ügyfél függvénytárainak egyik kiváló módja annak, hogy helyezze át az adatokat, és az Azure Storage.

Az Azure Storage gazdag ügyfélkódtárakat biztosít a .NET, iOS, Java, Android, az univerzális Windows Platform (UWP), Xamarin, C++, Node.JS, PHP, Ruby és Python. Az ügyfélkódtárak olyan fejlett képességeket biztosítanak, mint az újrapróbálkozási logika, a naplózás vagy a párhuzamos feltöltések. Fejleszthet közvetlenül a REST API-n is, amely minden, HTTP-/HTTPS-kérelmek létrehozására alkalmas nyelven meghívható.

Lásd: [Ismerkedés az Azure Blob Storage](../blobs/storage-dotnet-how-to-use-blobs.md) további.

Ezenkívül azt is kínál a [Azure Storage adatátviteli könyvtár](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) Ez az egy könyvtárban, nagy teljesítményű az adatok másolása, és az Azure-ból készült. Tekintse meg az adatátviteli könyvtár [dokumentáció](https://github.com/Azure/azure-storage-net-data-movement) további. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Adatai gyors megtekintése, és interakció adataival
Ha azt szeretné, hogy egyszerűen megtekintheti az Azure Storage adatokat feltölteni, és az adatok letöltése mellett is, fontolja meg egy Azure Storage Intéző segítségével.

Tekintse meg a kiválasztott [Azure Tártallózók](../storage-explorers.md) további.

## <a name="system-administration"></a>Rendszer-felügyeleti
Ha szükséges, vagy több részeként (pl. a rendszergazdák) parancssori segédprogram, itt figyelembe kell venni néhány lehetőség áll:

### <a name="azcopy"></a>AzCopy
Az AzCopy egy Windows parancssori segédprogram, amely az adatok az Azure Storage szolgáltatásba vagy onnan máshová való nagyteljesítményű másolására lett kifejlesztve. Adatok egy tárfiókon belül, vagy eltérő tárfiókokból között is másolhatja.

Lásd: [adatátvitel az AzCopy parancssori segédprogram a](storage-use-azcopy.md) további.

### <a name="azure-powershell"></a>Azure PowerShell
Az Azure PowerShell modul parancsmagokat biztosít az Azure-szolgáltatások kezeléséhez. Ez egy feladatalapú parancshéj és parancsnyelv, amely kifejezetten rendszer-felügyeleti célra készült.

Lásd: [Azure PowerShell használata az Azure Storage](storage-powershell-guide-full.md) további.

### <a name="azure-cli"></a>Azure CLI
Az Azure parancssori felület számos nyílt forráskódú, platformok közötti parancsokon keresztül működő Azure-szolgáltatásokkal. Az Azure CLI Windows, OSX és Linux érhető el.

Lásd: [az Azure parancssori felület használatával az Azure Storage](../storage-azure-cli.md) további.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Nagy mennyiségű adat egy lassú hálózati áthelyezése
A legnagyobb kihívást nagy mennyiségű adat áthelyezését társított egyik átvitelének az idejét. Ha szeretne adatok Azure Storage és a hálózatok költségek aggódni és programozás nélkül, Azure Import/Export egy megfelelő megoldást.

Lásd: [Azure Import/Export](../storage-import-export-service.md) további.

## <a name="backing-up-your-data"></a>Az adatok biztonsági mentése
Ha egyszerűen kell biztonsági másolatot a Azure Storage, Azure biztonsági mentés módja a nyissa meg. Ez egy hatékony megoldás a helyszíni adatokat és az Azure virtuális gépek biztonsági mentése.

Lásd: [Azure biztonsági mentés](../../backup/backup-introduction-to-azure-backup.md) további.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Fér hozzá a adatok a helyszínen vagy a felhőből
Ha megoldásra van szüksége egy való hozzáféréshez szükséges adatokat a helyszíni és a felhőből, majd érdemes Azure hibrid felhőalapú tárolómegoldás, StorSimple használatával. Ez a megoldás, hogy intelligens módon tárolja a gyakran használt adatokat az SSD-k, alkalmanként használja az adatok a HDD-k, inaktív vagy biztonsági mentés/archiválási adatok Azure Storage a fizikai StorSimple eszköz áll.

Lásd: [StorSimple](../../storsimple/storsimple-overview.md) további.

## <a name="recovering-your-data"></a>Az adatok helyreállítása
Ha a helyszíni munkaterhelések és alkalmazások, olyan megoldás, amely lehetővé teszi, hogy az üzleti katasztrófa esetén futtatása lesz szüksége. Az Azure Site Recovery kezeli a replikáció, feladatátvétel és helyreállítási virtuális gépek és fizikai kiszolgálók. A replikált adatok Azure Storage, így nincs szükség egy másodlagos helyszíni adatközpontba tárolja.

Lásd: [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) további.
### <a name="moving-data-faq"></a>Gyakori kérdések az adatok mozgatása:
## <a name="can-i-migrate-vhds-from-one-region-to-another-without-copying"></a>Telepíthetek át virtuális merevlemezek egy régióban másik másolása nélkül?
Csak másolja a VHD-k közötti terület, az adatok minden egyes régió a storage-fiókok közötti másolásához. Az AZCopy is használhatja. Tekintse meg az AzCopy parancssori segédprogram további átviteli adatok. Nagyon nagy mennyiségű adat is Azure Import/Export. Lásd: [Azure Import/Export](https://docs.microsoft.com/en-us/azure/storage/storage-import-export-service) további.
