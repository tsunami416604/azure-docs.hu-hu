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
ms.date: 08/28/2018
ms.author: alkohli
ms.openlocfilehash: 2fdd574adf3587f11984bee2a2549d9bcd0c4c0d
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43126005"
---
# <a name="azure-data-box-disk-system-requirements-preview"></a>Az Azure Data Box-lemezek rendszerkövetelmények (előzetes verzió)

Ez a cikk a Microsoft Azure Data Box-lemezek megoldás és a kapcsolódó ügyfelek számára a Data Box-lemezek fontos rendszerkövetelményeit ismerteti. Azt javasoljuk, hogy tekintse át az információkat gondosan előtt a Data Box-lemezek üzembe helyezése, majd tekintse át biztonsági, szükség szerint a telepítési és az ezt követő művelet során.

> [!IMPORTANT]
> Data Box-lemezek az előzetes verzióban. Tekintse át a [az előzetes verzió használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Ez a megoldás üzembe helyezése előtt. 

A rendszerkövetelmények lemezek, a támogatott tárfiókok és a tárolási típusok csatlakozó ügyfelek által támogatott platformok közé tartoznak.


## <a name="supported-operating-systems-for-clients"></a>Ügyfelek támogatott operációs rendszerek

A támogatott operációs rendszerek listáját itt van a lemez zárolásának feloldásához és adatmásolási műveletet az ügyfelek keresztül csatlakozik a Data Box-lemezek.

| **Operációs rendszer és platformok** | **Verziók** |
| --- | --- |
| Windows Server |2008 R2 SP1 <br> 2012 <br> 2012 R2-BEN <br> 2016 |
| Windows |7, 8, 10 |
| Windows PowerShell |4.0 |
| .NET-keretrendszer |4.5.1 |
| Windows Management Framework |4.0|

> [!NOTE] 
> A lemez futtató ügyfeleken engedélyezni kell a BitLocker eszköz feloldásához, és segítségével az adatok másolása.


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

