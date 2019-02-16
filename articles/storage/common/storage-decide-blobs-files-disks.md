---
title: Annak eldöntése, mikor érdemes használni az Azure-Blobok, az Azure Files és az Azure-lemezek
description: További információ a tárolhatja és érheti el az Azure-ban segítségével adatokat különböző módjait, melyik technológia használata mellett dönt.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 869d2105ccf635a46a21e9b7f382ddbef713d68b
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327010"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Annak eldöntése, mikor érdemes használni az Azure-Blobok, az Azure Files és az Azure-lemezek
A Microsoft Azure tárolásához és eléréséhez, az adatok a felhőben az Azure Storage számos funkciót biztosít. Ez a cikk ismerteti az Azure-fájlok, Blobok és lemezek, és célja, hogy ezek a szolgáltatások közötti választás érdekében.

## <a name="scenarios"></a>Forgatókönyvek
Az alábbi táblázat összehasonlítja a fájlok, Blobok és lemezek, és minden egyes megfelelő példa eseteit mutatja be.

| Szolgáltatás | Leírás | A következő esetekben használja |
|--------------|-------------|-------------|
| **Az Azure Files** | Biztosít egy SMB-kapcsolatot, ügyfél szalagtárakban és a egy [REST-felület](/rest/api/storageservices/file-service-rest-api) hozzáférés bárhonnan, amely lehetővé teszi a tárolt fájlok. | "Lift and shift" szeretne egy alkalmazást a felhőbe, amely már a natív fájlrendszer API-k segítségével, és más Azure-ban futó alkalmazások közötti adatmegosztást.<br/><br/>Fejlesztés és hibakeresés eszközöket, amelyek számos virtuális gépekről érhető el kell tárolni szeretné. |
| **Azure Blobs** | Ügyfélkódtárak biztosít és a egy [REST-felület](/rest/api/storageservices/blob-service-rest-api) , amely lehetővé teszi a strukturálatlan adatok tárolása és elérése a blokkblobok nagy méretű.<br/><br/>Emellett támogatja [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) vállalati big Data típusú adatok elemzési megoldásokat. | Szeretné, hogy az alkalmazás támogatja a streamelési és a véletlenszerű adathozzáférési forgatókönyvek esetében.<br/><br/>Szeretné tudni alkalmazásadatok bárhonnan elérheti.<br/><br/>Szeretné hozhat létre egy vállalati a data lake az Azure-ban, és hajtsa végre a big data-elemzés. |
| **Azure-lemezek** | Ügyfélkódtárak biztosít és a egy [REST-felület](/rest/api/compute/manageddisks/disks/disks-rest-api) , amely lehetővé teszi az adatok állandó tárolása és elérése a csatlakoztatott virtuális merevlemezről. | Olvasási és írási adatok állandó lemezt natív fájlrendszer API-kat használó alkalmazások átemelése szeretné.<br/><br/>Ez nem szükséges a virtuális gép, amelyhez a lemez csatlakozik kívülről adatokat tárolni szeretné. |

## <a name="comparison-files-and-blobs"></a>Összehasonlítás: Fájl és BLOB
Az alábbi táblázat hasonlítja össze az Azure Files az Azure Blobszolgáltatása révén.  
  
||||  
|-|-|-|  
|**Attribútum**|**Azure Blobs**|**Az Azure Files**|  
|Tartóssági beállításai|LRS, ZRS, GRS, RA-GRS|LRS, ZRS, GRS|  
|Kisegítő lehetőségek|REST API-k|REST API-k<br /><br /> Az SMB 2.1 és az SMB 3.0-s (standard fájlrendszer API-k)|  
|Kapcsolatok|REST API-k – világszerte|REST API-k – világszerte<br /><br /> SMB 2.1--régión belüli<br /><br /> Az SMB 3.0-s – világszerte|  
|Végpontok|`http://myaccount.blob.core.windows.net/mycontainer/myblob`|`\\myaccount.file.core.windows.net\myshare\myfile.txt`<br /><br /> `http://myaccount.file.core.windows.net/myshare/myfile.txt`|  
|Könyvtárak|Névtere strukturálatlan|Igaz címtárobjektumok|  
|Kis-és nagybetűk nevek|Kis- és nagybetűk megkülönböztetése|Kis-és nagybetű nincs megkülönböztetve, de a kis megőrzése|  
|Kapacitás|Legfeljebb 2 PiB fiók korlát |5 TiB fájlmegosztások|  
|Teljesítmény|Akár 60 per blokkblob MiB/s|Akár 60 jutó MiB/s|  
|Objektum mérete|Legfeljebb körülbelül 4,75 TiB blokkblob kiszolgálónként|Legfeljebb 1 Tib-ra / fájl|  
|Számlázott kapacitás|Írt bájtok száma alapján|A fájl mérete alapján|  
|Ügyfélkódtárak|Több nyelv|Több nyelv|  
  
## <a name="comparison-files-and-disks"></a>Összehasonlítás: Fájlok és lemezek
Az Azure Files Azure-lemezek egészítenek ki. Egy lemezt csak lehet csatolni, egy Azure virtuális gép egyszerre. Lemezek rögzített formátumú VHD-k tárolt és az Azure Storage-ban, és a virtuális gép által hosszú élettartamú adatok tárolására szolgálnak. Fájlmegosztások az Azure Files ugyanúgy elérni is, mivel a helyi lemez (fájlrendszer API-k használatával) érhető el, és több virtuális gép között megoszthatók.  
 
Az alábbi táblázat hasonlítja össze az Azure Files Azure-lemezek.  
 
||||  
|-|-|-|  
|**Attribútum**|**Azure-lemezek**|**Az Azure Files**|  
|Hatókör|Csak egyetlen virtuális gép|Több virtuális gép között megosztott hozzáférés|  
|A pillanatképek és másolása|Igen|Igen|  
|Konfiguráció|A virtuális gép indításakor csatlakoztatva|Miután elindult a virtuális gép csatlakoztatva|  
|Authentication|Beépített|Net use beállítása|  
|Adatelérési REST használatával|A virtuális merevlemez található fájl nem érhető el|Olyan megosztáson tárolt fájlok elérhetők|  
|Max. méret|4 TiB-lemez|5 TiB fájlmegosztás és a megosztáson belüli 1 TiB-fájl|  
|Maximális iops-érték|500 IOps|1000 IOps|  
|Teljesítmény|Akár 60 MiB/s lemezenként|Cél értéke 60 MiB/s fájlmegosztásonként (beszerezheti a nagyobb, magasabb i/o-méretek esetében)|  

## <a name="next-steps"></a>További lépések
Vonatkozó döntések meghozatalát hogyan adatait tárolják és érik el, ha akkor is figyelembe kell venni a költségek érint. További információkért lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).
  
Egyes SMB-funkciók nem használhatók a felhőben. További információkért lásd: [az Azure File Storage-szolgáltatás által nem támogatott funkciók](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
  
Lemezekkel kapcsolatos további információkért lásd: a [Bevezetés a managed Disks szolgáltatásba](../../virtual-machines/windows/managed-disks-overview.md) és [bemutatja, hogyan csatlakoztathat adatlemezt egy Windows virtuális gép](../../virtual-machines/windows/attach-managed-disk-portal.md).
