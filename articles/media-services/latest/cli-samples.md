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
ms.openlocfilehash: bbf69bdcc92316642f6b37d267cdea2aad920316
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
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
| [Átalakítások létrehozása](./scripts/cli-create-transform.md)|Bemutatja, hogyan átalakítások létrehozásához. Átalakítások egyszerű munkafolyamat a video- vagy -fájlok (más néven "módszereivel") feldolgozása kapcsolatos feladatokat írják le.<br/> Ha a kívánt nevű átalakító mindig ellenőrizze, és "módszereivel" már létezik. Ha igen, használja fel újra. |
| [Feladatok létrehozása](./scripts/cli-create-jobs.md)|Egy feladatot, amely egy egyszerű kódolási átalakítási HTTPs URL-Címének használatával küldi el.|
| [EventGrid létrehozása](./scripts/cli-create-event-grid.md)|Egy fiók szintű esemény rács előfizetést hoz létre a feladat állapotának változását.|

