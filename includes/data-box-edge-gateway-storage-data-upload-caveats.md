---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179574"
---
A következő kikötések vonatkoznak az adatokra, ahogy az Azure-ba költöznek.

- Azt javasoljuk, hogy egynél több eszköz ne írjon ugyanabba a tárolóba.
- Ha van egy meglévő Azure-objektum (például egy blob vagy egy fájl) a felhőben az azonos nevű, mint az objektum, amely a másolás alatt áll, az eszköz felülírja a fájlt a felhőben.
- A megosztási mappák alatt létrehozott üres könyvtárhierarchia (fájlok nélkül) nem töltődik fel a blobtárolókba.
- Az adatokat a Fájlkezelővel vagy a parancssorból húzással másolhatja. Ha a másolt fájlok összesített mérete meghaladja a 10 GB-ot, javasoljuk, hogy tömeges másolási programot használjon, például robocopy vagy rsync. A tömeges másolási eszközök újra próbálkoznak a másolási művelet időszakos hibák, és további rugalmasságot biztosít.
- Ha az Azure storage-tárolóhoz társított megosztás olyan blobokat tölt fel, amelyek nem egyeznek meg a megosztáshoz a létrehozás időpontjában meghatározott blobok típusával, majd az ilyen blobok nem frissülnek. Például létrehoz egy blokk blob megosztást az eszközön. Társítsa a megosztást egy meglévő felhőalapú tárolóhoz, amely lapblobokat is rendelkezik. Frissítse a megosztást a fájlok letöltéséhez. Módosítsa a felhőben már lapblobként tárolt frissített fájlok némelyikét. Látni fogja, feltöltési hibák.
