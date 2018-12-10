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
ms.custom: seodec18
ms.date: 12/08/2018
ms.author: juliako
ms.openlocfilehash: 9df9279a3922fa639385657660d6d0f4a55b5a4d
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53132772"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Az Azure Media Services az Azure CLI-példák

A következő táblázat az Azure Media Services az Azure CLI-példákra mutató hivatkozásokat tartalmaz.

## <a name="examples"></a>Példák

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
