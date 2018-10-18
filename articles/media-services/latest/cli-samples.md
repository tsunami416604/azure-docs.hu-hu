---
title: Az Azure CLI-példák – az Azure Media Services |} A Microsoft Docs
description: Azure CLI-példák az Azure Media Services szolgáltatáshoz
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 5559f9055da3a2a852427c0f27d367159cdc7655
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49388510"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Az Azure Media Services az Azure CLI-példák

A következő táblázat az Azure Media Services az Azure CLI-példákra mutató hivatkozásokat tartalmaz.

|  |  |
|---|---|
|**Fiók**||
| [A Media Services-fiók létrehozása](./scripts/cli-create-account.md) | Létrehoz egy Azure Media Services-fiókot. Emellett létrehoz egy egyszerű szolgáltatást, amely API-k programozott módon kezelheti a fiók elérésére használható. |
| [Fiók hitelesítő adatainak alaphelyzetbe állítása](./scripts/cli-reset-account-credentials.md)|Alaphelyzetbe állítja a fiók hitelesítő adatait, és megkapja az app.config beállításokat.|
|**Eszközök**||
| [Adategységek létrehozása](./scripts/cli-create-asset.md)|Létrehoz egy Media Services eszköz tartalmat feltölteni.|
| [Fájl feltöltése](./scripts/cli-upload-file-asset.md)|Feltölt egy helyi fájlt egy storage-tárolóba.|
| [Az objektum közzététele](./scripts/cli-publish-asset.md)| A Streamelési lokátor hoz létre, és megkapja Streamelési URL-címeket. |
| **Átalakítások** és **feladatok**||
| [Átalakítások létrehozása](./scripts/cli-create-transform.md)|Bemutatja, hogyan hozhat létre átalakításokat. Az átalakítások feladatokból álló egyszerű munkafolyamatot definiálnak a video- vagy hangfájlok feldolgozásához (ezek „recept” néven is ismertek).<br/> Mindig ellenőrizze, hogy létezik-e már adott nevű és „receptű” Átalakítás. Ha igen, újra felhasználhatja. |
| [Feladatok létrehozása](./scripts/cli-create-jobs.md)|Elküld egy feladatot, amely egy egyszerű kódolási átalakító HTTPs URL-cím használatával.|
| [EventGrid létrehozása](./scripts/cli-create-event-grid.md)|Egy fiók szintű Event Grid-előfizetést hoz létre feladat állapotváltozások.|

## <a name="see-also"></a>Lásd még

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
