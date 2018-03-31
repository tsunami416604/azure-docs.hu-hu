---
title: Azure BLOB, az Azure fájlok vagy az Azure-lemezek használatára való
description: Ismerje meg a különböző módszereket tárolhatja és érheti el az Azure segítségével adatokat úgy dönt, hogy melyik technológiát használja.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/28/2018
ms.author: tamram
ms.openlocfilehash: ded0884ff83cc214d78f65fed8cefa646f11d952
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Azure BLOB, az Azure fájlok vagy az Azure-lemezek használatára való

A Microsoft Azure számos szolgáltatást biztosít az Azure Storage tárolja, és a felhőben tárolt adatainak eléréséhez. Ez a cikk ismerteti az Azure-fájlok, Blobok és lemezek, és célja, hogy ezek a funkciók választás.

## <a name="scenarios"></a>Alkalmazási helyzetek

Az alábbi táblázat összehasonlítja a fájlok, Blobok és lemezek, és az egyes megfelelő példaforgatókönyvek jeleníti meg.

| Szolgáltatás | Leírás | A következő esetekben használja |
|--------------|-------------|-------------|
| **Az Azure Files** | SMB illesztőfelület, klienskódtárak segítségével, és egy [REST-felület](/rest/api/storageservices/file-service-rest-api) , amely lehetővé teszi, hogy bárhonnan tárolt fájlokhoz. | "A növekedési és az eltolás mértékét megadó" szeretne egy alkalmazást a felhőbe, amely már használja a natív fájlrendszer API-k és más Azure-ban futó alkalmazások közötti adatmegosztásra.<br/><br/>Fejlesztési és hibakeresési eszközök, amelyek több virtuális gép elérhető kell tárolni szeretné. |
| **Azure Blobs** | Ügyfélkódtárakat biztosít, és egy [REST-felület](/rest/api/storageservices/blob-service-rest-api) , amely lehetővé teszi, hogy a strukturálatlan adatok tárolása és egy nagy méretű a blokkblobokhoz érhető el. | Szeretné, hogy az alkalmazás támogassa a streaming és elérésű forgatókönyvek.<br/><br/>Érdemes tudni alkalmazásadatok hozzáférni bárhonnan. |
| **Azure-lemezeket** | Ügyfélkódtárakat biztosít, és egy [REST-felület](/rest/api/compute/manageddisks/disks/disks-rest-api) , amely lehetővé teszi az adatok tartósan tárolja, és egy csatolt virtuális merevlemez érhető el. | Szeretné növekedési és az eltolás mértékét megadó olvasási és írási adatok állandó lemezek natív fájlrendszer API-k használó alkalmazások.<br/><br/>Szeretné tárolni az adatokat, nincs szükség a virtuális gép, amelyen a lemez csatolva van kívül érhető el. |

## <a name="comparison-files-and-blobs"></a>Összehasonlítás: Fájlok és Blobok

Az alábbi táblázat összehasonlítja a Azure BLOB Azure fájlokat.  
  
||||  
|-|-|-|  
|**Attribútum**|**Azure Blobs**|**Az Azure Files**|  
|Tartóssági beállítások|LRS, ZRS, GRS, RA-GRS|LRS, ZRS, GRS|  
|Kisegítő lehetőségek|REST API-k|REST API-k<br /><br /> SMB 2.1 és az SMB 3.0 (szabványos fájlrendszere API-k)|  
|Hálózati kapcsolat|REST API-k – világszerte|REST API-k - világszerte<br /><br /> SMB 2.1--régión belül<br /><br /> Az SMB 3.0--világszerte|  
|Végpontok|`http://myaccount.blob.core.windows.net/mycontainer/myblob`|`\\myaccount.file.core.windows.net\myshare\myfile.txt`<br /><br /> `http://myaccount.file.core.windows.net/myshare/myfile.txt`|  
|Könyvtárak|Egyszerű névtér|Igaz címtárobjektumok|  
|A nevek nagybetűk|Kis- és nagybetűk megkülönböztetése|Kis-és nagybetűk megkülönböztetése nélkül, de megőrzi az eset|  
|Kapacitás|Legfeljebb 500 TiB tárolók|5 TiB fájlmegosztások|  
|Teljesítmény|Legfeljebb 60 MiB/s sebességet blokkblob|Az egy legfeljebb 60 MiB/s|  
|Objektum mérete|Legfeljebb körülbelül 4.75 TiB blokkblob száma|Legfeljebb 1 TiB fájlonként|  
|Számlázott kapacitás|Írt bájtok alapján|A fájl mérete alapján|  
|Ügyfélkódtárak|Több nyelv|Több nyelv|  
  
## <a name="comparison-files-and-disks"></a>Összehasonlítás: Fájlok és lemezek

Az Azure Files kiegészíti az Azure-lemezeket. A lemez csak akkor csatolható számára egy Azure virtuális gép egyszerre. Lemezek rögzített formátumú virtuális merevlemezeket tárolja az Azure Storage lapblobokat, és a virtuális gép által tartós adatok tárolására használt. Azure-fájlokban szereplő fájlmegosztások is elérhetők ugyanúgy, mint a helyi lemez (fájlrendszer API-k használatával) érhető el, és több virtuális gép között megosztható legyen.  
 
Az alábbi táblázat összehasonlítja az Azure-fájlok az Azure-lemezeket.  
 
||||  
|-|-|-|  
|**Attribútum**|**Azure-lemezeket**|**Az Azure Files**|  
|Hatókör|Csak egyetlen virtuális gép|Több virtuális gép között megosztott hozzáférés|  
|A pillanatképek és másolása|Igen|Nem|  
|Konfiguráció|A virtuális gép indításakor csatlakoztatva|A virtuális gép megkezdése után csatlakoztatva|  
|Hitelesítés|Beépített|Net use beállítása|  
|Felesleges tartalmak törlése|Automatikus|Manuális|  
|REST-hozzáférés|A virtuális Merevlemezen található fájl nem érhető el|A megosztáson található fájlok is elérhetők.|  
|Maximális méret|4 TiB lemez|5 TiB fájlmegosztás és a megosztáson belüli 1 TiB fájl|  
|Maximális iops-érték 8 KB-os|500 IOps|1000 IOps|  
|Teljesítmény|Lemezenként legfeljebb 60 MiB/s|Egy fájl akár 60 MiB/s|  

## <a name="next-steps"></a>További lépések

Az adatok tárolásának és elért hogyan kapcsolatos döntések meghozásakor is érdemes a költségek jár. További információkért lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).
  
A felhő nem alkalmazhatók egyes SMB-szolgáltatások. További információkért lásd: [az Azure File service által nem támogatott funkciók](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
  
Lemezek kapcsolatos további információkért lásd: [lemezek és lemezképek kezelése](../../virtual-machines/windows/about-disks-and-vhds.md) és [hogyan lehet adatlemezt csatolni egy Windows rendszerű virtuális gép](../../virtual-machines/windows/attach-managed-disk-portal.md).
