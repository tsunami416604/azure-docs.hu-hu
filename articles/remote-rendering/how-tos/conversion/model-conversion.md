---
title: Modell átalakítása
description: A modell renderelésre történő átalakításának folyamata
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 135c58a665779ffaad8750ffe618bdbe38639b66
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681492"
---
# <a name="convert-models"></a>Modellek konvertálása

Az Azure remote rendering lehetővé teszi, hogy nagyon összetett modellek et jelenítsen meg. A maximális teljesítmény elérése érdekében az adatokat előkell dolgozni, hogy optimális formátumban legyenek. Az adatok mennyiségétől függően ez a lépés eltarthat egy ideig. Nem lenne praktikus, ha ezt az időt töltött során modell betöltése. Is, lenne pazarló megismételni ezt a folyamatot több ülés. Ezen okok miatt az ARR szolgáltatás dedikált *konverziós szolgáltatást*biztosít, amelyet előre futtathat.
Konvertálás után egy modell betölthető egy Azure Storage-fiókból.

## <a name="supported-source-formats"></a>Támogatott forrásformátumok

A konverziós szolgáltatás a következő formátumokat támogatja:

- **FBX** (2011-es és újabb verzió)
- **GLTF** (2.x verzió)
- **GLB** (2.x verzió)

Az anyagtulajdon-átalakítás formátumai között kisebb különbségek vannak, amint az a [modellformátumok fejezetanyag-feltérképezésében](../../reference/material-mapping.md)szerepel.

## <a name="the-conversion-process"></a>Az átalakítási folyamat

1. [Készítsen elő két Azure Blob Storage-tárolót:](blob-storage.md)egyet a bemenethez, egyet a kimenethez
1. Töltse fel a modellt a bemeneti tárolóba (opcionálisan egy részútvonal alatt)
1. Az átalakítási folyamat aktiválása [a modellkonverziós REST API-n](conversion-rest-api.md) keresztül
1. A szolgáltatás lekérdezése a konverziós folyamathoz
1. Miután elkészült, töltsön be egy modellt
    - egy összekapcsolt tárfiókból (lásd a "Tárfiókok összekapcsolása" lépéseket a [Fiók létrehozása](../create-an-account.md#link-storage-accounts) a tárfiók összekapcsolására című témakörben)
    - vagy *megosztott hozzáférésű aláírás (SAS)* megadásával.

Minden modelladatok (bemeneti és kimeneti) a felhasználó által biztosított Azure blob storage tárolja. Az Azure remote rendering teljes körű felügyeletet biztosít az eszközkezelés felett.

## <a name="conversion-parameters"></a>Átalakítási paraméterek

A különböző konverziós lehetőségeket ebben a [fejezetben](configure-model-conversion.md)talál.

## <a name="examples"></a>Példák

- [Rövid útmutató: A modell konvertálása renderelésre](../../quickstarts/convert-model.md) egy lépésről-lépésre bevezető, hogyan kell átalakítani a modellt.
- [Példa PowerShell-parancsfájlok](../../samples/powershell-example-scripts.md), amelyek bemutatják a konverziós szolgáltatás használatát, megtalálható az [ARR minttárházban](https://github.com/Azure/azure-remote-rendering) a *Scripts* mappában.

## <a name="next-steps"></a>További lépések

- [Az Azure Blob Storage használata modellátalakításhoz](blob-storage.md)
- [A modellkonverziós REST API](conversion-rest-api.md)
- [A modellkonvertálás konfigurálása](configure-model-conversion.md)
- [Anyagleképezés modellformátumokhoz](../../reference/material-mapping.md)
