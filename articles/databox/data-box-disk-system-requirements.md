---
title: Microsoft Azure Data Box Disk rendszerkövetelményei | Microsoft Docs
description: Tudnivalók a Azure Data Box Disk szoftveres és hálózati követelményeiről
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 08/29/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 20c27a3e4e9a96a19b347e5ef57ab9fb3c047140
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194426"
---
::: zone target="docs"

# <a name="azure-data-box-disk-system-requirements"></a>Rendszerkövetelmények Azure Data Box Disk

Ez a cikk a Microsoft Azure Data Box Disk megoldás és a Data Box Diskhoz csatlakozó ügyfelek fontos rendszerkövetelményeit ismerteti. Javasoljuk, hogy körültekintően tekintse át az információkat, mielőtt üzembe helyezi a Data Box Disk, majd az üzembe helyezés és az azt követő művelet során szükség szerint visszatekinti azt.

A rendszerkövetelmények tartalmazzák a lemezekhez, a támogatott Storage-fiókokhoz és a tárolási típusokhoz csatlakozó ügyfelek által támogatott platformokat.

::: zone-end

::: zone target="chromeless"

# <a name="review-prerequisites"></a>Előfeltételek áttekintése

1. A Data Box Disk az oktatóanyag használatával kell elrendeznie: Rendeljen Azure Data Box Disk. Lemezeket és egy csatlakoztatott kábelt fogadott el lemezenként.
2. Van olyan ügyfélszámítógép, amelyről másolatot készíthet az adatokról. Az ügyfélszámítógépen:

    - Futtasson egy támogatott operációs rendszert.
    - Telepítenie kell más szükséges szoftvereket.

::: zone-end

## <a name="supported-operating-systems-for-clients"></a>Ügyfelek által támogatott operációs rendszerek

Itt látható a lemez zárolásának feloldásához és az adatmásolási művelethez támogatott operációs rendszerek listája a Data Box Diskhoz csatlakozó ügyfeleken.

| **Operációs rendszer** | **Tesztelt verziók** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows (64 bites) |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04-es, 16.04, 18.04 <br> 8,11, 9 <br> 7.0 <br> 6,5, 6,9, 7,0, 7,5 |  

## <a name="other-required-software-for-windows-clients"></a>Egyéb szükséges szoftverek Windows-ügyfelekhez

Windows-ügyfél esetén a következőket is telepíteni kell.

| **Szoftver**| **Verzió** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET-keretrendszer |4.5.1 |
| Windows Management Framework |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Egyéb szükséges szoftverek Linux-ügyfelekhez

A Linux-ügyfelek esetében a Data Box Disk eszközkészlet a következő szükséges szoftvereket telepíti:

- zárolás feloldása
- OpenSSL

::: zone target="docs"

## <a name="supported-connection"></a>Támogatott kapcsolatok

Az adattárolót tartalmazó ügyfélszámítógépnek USB 3,0 vagy újabb porttal kell rendelkeznie. A lemezek a megadott kábellel csatlakoznak ehhez az ügyfélhez.

## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

Az alábbi lista a Data Box Disk támogatott tárolási típusait sorolja fel.

| **Storage-fiók** | **Megjegyzések** |
| --- | --- |
| Klasszikus | Standard |
| Általános rendeltetés  |Standard a v1 és a v2 is támogatott. A gyakori és a ritka elérésű rétegek is támogatottak. |
| BLOB Storage-fiók | |

>[!NOTE]
> Azure Data Lake Storage 2. generációs fiókok nem támogatottak.


## <a name="supported-storage-types-for-upload"></a>A feltöltéshez támogatott tárolási típusok

Az alábbi lista felsorolja az Azure-ba való feltöltéshez támogatott tárolási típusokat Data Box Disk használatával.

| **Fájl formátuma** | **Megjegyzések** |
| --- | --- |
| Azure Block-blob | |
| Azure-oldal blobja  | |
| Azure Files  | |
| Managed Disks | |


## <a name="next-step"></a>Következő lépés

* [A Azure Data Box Disk üzembe helyezése](data-box-disk-deploy-ordered.md)

::: zone-end

