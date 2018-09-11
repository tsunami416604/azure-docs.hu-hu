---
title: A Microsoft Azure Data Box-lemezek követelményei |} A Microsoft Docs
description: Ismerje meg a szoftver- és hálózati követelményei az Azure Data Box-lemezek
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2018
ms.author: alkohli
ms.openlocfilehash: aaa4e4bb24ca42adb9d283e6286dbef879bcb1ea
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299848"
---
# <a name="azure-data-box-disk-system-requirements-preview"></a>Az Azure Data Box-lemezek rendszerkövetelmények (előzetes verzió)

Ez a cikk a Microsoft Azure Data Box-lemezek megoldás és a kapcsolódó ügyfelek számára a Data Box-lemezek fontos rendszerkövetelményeit ismerteti. Azt javasoljuk, hogy tekintse át az információkat gondosan előtt a Data Box-lemezek üzembe helyezése, majd tekintse át biztonsági, szükség szerint a telepítési és az ezt követő művelet során.

> [!IMPORTANT]
> Data Box-lemezek az előzetes verzióban. Tekintse át a [az előzetes verzió használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Ez a megoldás üzembe helyezése előtt. 

A rendszerkövetelmények lemezek, a támogatott tárfiókok és a tárolási típusok csatlakozó ügyfelek által támogatott platformok közé tartoznak.


## <a name="supported-operating-systems-for-clients"></a>Ügyfelek támogatott operációs rendszerek

A támogatott operációs rendszerek listáját itt van a lemez zárolásának feloldásához és adatmásolási műveletet az ügyfelek keresztül csatlakozik a Data Box-lemezek.

| **Operációs rendszer** | **A tesztelt verziók** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2-BEN <br> 2016 |
| Windows |7, 8, 10 |
|Linux <br> <li> Ubuntu </li><li> Debian </li><li> Red Hat Enterprise Linux (RHEL) </li><li> CentOS| <br>14.04-es, 16.04, 18.04 <br> 8.11, 9 <br> 7.0 <br> 6.5, 6.9, 7.0 és 7.5 |  

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

## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

Íme a támogatott tárolási típusokat a Data Box-lemezek listája.

| **Storage-fiók** | **Megjegyzések** |
| --- | --- |
| Klasszikus | Standard |
| Általános rendeltetés  |Standard; a V1 és V2 is támogatja. A gyakori és ritka elérésű szint használata támogatott. |


## <a name="supported-storage-types"></a>Támogatott tárolási típus

Íme a támogatott tárolási típusokat a Data Box-lemezek listája.

| **Fájlformátum** | **Megjegyzések** |
| --- | --- |
| Az Azure blokkblob | |
| Oldala az Azure blob  | |


## <a name="next-step"></a>Következő lépés

* [Az Azure Data Box-lemezek üzembe helyezése](data-box-disk-deploy-ordered.md)

