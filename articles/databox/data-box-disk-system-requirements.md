---
title: A Microsoft Azure Data Box-lemezek követelményei |} A Microsoft Docs
description: Ismerje meg a szoftver- és hálózati követelményei az Azure Data Box-lemezek
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/05/2019
ms.author: alkohli
ms.openlocfilehash: 0effc6af1ddc3273f0c6e2bf9cbfd0f2ecadf0a2
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747322"
---
# <a name="azure-data-box-disk-system-requirements"></a>Az Azure Data Box-lemezek rendszerkövetelményei

Ez a cikk a Microsoft Azure Data Box-lemezek megoldás és a kapcsolódó ügyfelek számára a Data Box-lemezek fontos rendszerkövetelményeit ismerteti. Azt javasoljuk, hogy tekintse át az információkat gondosan előtt a Data Box-lemezek üzembe helyezése, majd tekintse át biztonsági, szükség szerint a telepítési és az ezt követő művelet során.

A rendszerkövetelmények lemezek, a támogatott tárfiókok és a tárolási típusok csatlakozó ügyfelek által támogatott platformok közé tartoznak.


## <a name="supported-operating-systems-for-clients"></a>Ügyfelek támogatott operációs rendszerek

A támogatott operációs rendszerek listáját itt van a lemez zárolásának feloldásához és adatmásolási műveletet az ügyfelek keresztül csatlakozik a Data Box-lemezek.

| **Operációs rendszer** | **A tesztelt verziók** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2 <br> 2016 |
| Windows |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04-es, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0, 7.5 |  

## <a name="other-required-software-for-windows-clients"></a>Windows-ügyfelek számára szükséges szoftverek

Windows-ügyfél következő is telepítenie kell.

| **Szoftver**| **Verzió** |
| --- | --- |
| Windows PowerShell |5.0 |
| .NET-keretrendszer |4.5.1 |
| Windows Management Framework |5.0|
| BitLocker| - |

## <a name="other-required-software-for-linux-clients"></a>Linux-ügyfelek számára szükséges szoftverek

Linux-ügyfél a Data Box-lemezek eszközkészlet telepíti a következő szükséges szoftverek:

- dislocker
- OpenSSL

## <a name="supported-connection"></a>Támogatott kapcsolatok

Az ügyfélszámítógépen, az adatokat tartalmazó rendelkeznie kell egy USB 3.0-s vagy újabb port. Ez az ügyfél a megadott kábelen csatlakozzon a lemezek. 

## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

Íme a támogatott tárolási típusokat a Data Box-lemezek listája.

| **Storage-fiók** | **Megjegyzések** |
| --- | --- |
| Klasszikus | Standard |
| Általános rendeltetés  |Standard; a V1 és V2 is támogatja. A gyakori és ritka elérésű szint használata támogatott. |

>[!NOTE]
> Az Azure Data Lake Storage 2. generációs gyűjtések fiókok nem támogatottak.


## <a name="supported-storage-types"></a>Támogatott tárolási típus

Íme a támogatott tárolási típusokat a Data Box-lemezek listája.

| **Fájlformátum** | **Megjegyzések** |
| --- | --- |
| Az Azure blokkblob | |
| Oldala az Azure blob  | |


## <a name="next-step"></a>Következő lépés

* [Az Azure Data Box-lemezek üzembe helyezése](data-box-disk-deploy-ordered.md)

