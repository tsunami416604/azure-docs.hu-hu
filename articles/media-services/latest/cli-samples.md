---
title: Az Azure CLI példák - Azure Media Services |} Microsoft Docs
description: Az Azure CLI példák Azure Media Services szolgáltatáshoz
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 04/15/2018
ms.author: juliako
ms.openlocfilehash: acc92662aa5b727656a8eda368ba6d78a87d9ecd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640889"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Azure Media Services Azure CLI-példák

A következő táblázat az Azure Media Services Azure CLI példák hivatkozásokat tartalmaz.

|  |  |
|---|---|
|**Fiók**||
| [Media Services-fiók létrehozása](./scripts/cli-create-account.md) | Létrehoz egy Azure Media Services-fiókot. Ezenkívül létrehoz egy szolgáltatás egyszerű, amely programozott módon kezelheti a fiók API-k elérésére használható. |
| [Fiók hitelesítő adatok alaphelyzetbe állítása](./scripts/cli-reset-account-credentials.md)|Alaphelyzetbe állítja a fiók hitelesítő adatait, és megkapja az app.config beállításokat.|
|**Eszközök**||
| [Eszközök létrehozása](./scripts/cli-create-asset.md)|Tartalom feltöltése a Media Services eszköz hoz létre.|
| [Fájl feltöltése](./scripts/cli-upload-file-asset.md)|Egy tároló feltölt egy helyi fájlt.|
| [Az objektum közzététele](./scripts/cli-publish-asset.md)| A Streamelési lokátor hoz létre, és megkapja Streaming URL-címeket. |
| **Átalakítja az** és **feladatok**||
| [Átalakítások létrehozása](./scripts/cli-create-transform.md)|Bemutatja, hogyan átalakítások létrehozásához. Az átalakítások feladatokból álló egyszerű munkafolyamatot definiálnak a video- vagy hangfájlok feldolgozásához (ezek „recept” néven is ismertek).<br/> Mindig ellenőrizze, hogy létezik-e már adott nevű és „receptű” Átalakítás. Ha igen, használja fel újra. |
| [Feladatok létrehozása](./scripts/cli-create-jobs.md)|Egy feladatot, amely egy egyszerű kódolási átalakítási HTTPs URL-Címének használatával küldi el.|
| [EventGrid létrehozása](./scripts/cli-create-event-grid.md)|Egy fiók szintű esemény rács előfizetést hoz létre a feladat állapotának változását.|

## <a name="see-also"></a>Lásd még

[CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/ams?view=azure-cli-latest)
