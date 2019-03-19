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
ms.date: 03/11/2019
ms.author: juliako
ms.openlocfilehash: dee7f831562dc1f4b2478d13b204aab1d8455e1e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57840630"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Az Azure Media Services az Azure CLI-példák

A következő táblázat az Azure Media Services az Azure CLI-példákra mutató hivatkozásokat tartalmaz.

## <a name="examples"></a>Példák

|  |  |
|---|---|
|**Méretezés**||
| [Scale a Media szolgáltatás számára fenntartott egységek](media-reserved-units-cli-how-to.md)|A hangelemzés és videó elemzési feladatokat a Media Services v3 által aktivált vagy a Video Indexer azt javasoljuk 10 S3 szintű helyet a fiók kiépítése. <br/>A szkript bemutatja, hogyan skálázhatja a Media szolgáltatás számára fenntartott egységek (helyet) a parancssori felület használatával.|
|**Fiók**||
| [A Media Services-fiók létrehozása](create-account-cli-how-to.md) | A szkript létrehoz az Azure Media Services-fiók. |
| [Fiók hitelesítő adatainak alaphelyzetbe állítása](./scripts/cli-reset-account-credentials.md)|Alaphelyzetbe állítja a fiók hitelesítő adatait, és megkapja az app.config beállításokat.|
|**Eszközök**||
| [Adategységek létrehozása](./scripts/cli-create-asset.md)|Létrehoz egy Media Services eszköz tartalmat feltölteni.|
| [Fájl feltöltése](./scripts/cli-upload-file-asset.md)|Feltölt egy helyi fájlt egy storage-tárolóba.|
| **Átalakítások** és **feladatok**||
| [Átalakítások létrehozása](./scripts/cli-create-transform.md)|Bemutatja, hogyan hozhat létre átalakításokat. Az átalakítások feladatokból álló egyszerű munkafolyamatot definiálnak a video- vagy hangfájlok feldolgozásához (ezek „recept” néven is ismertek).<br/> Mindig ellenőrizni kell, ha egy-egy átalakítási kívánt nevére, és a "recept" már létezik. Ha igen, újra felhasználhatja. |
| [Az egyéni átalakítási kódolása](custom-preset-cli-howto.md) | Bemutatja, hogyan hozhat létre egy egyéni, amelyekre az adott forgatókönyv vagy eszközkövetelmények előbeállítást.|
| [Feladatok létrehozása](./scripts/cli-create-jobs.md)|Elküld egy feladatot, amely egy egyszerű kódolási átalakító HTTPs URL-cím használatával.|
| [EventGrid létrehozása](./scripts/cli-create-event-grid.md)|Egy fiók szintű Event Grid-előfizetést hoz létre feladat állapotváltozások.|
| **Deliver**||
| [Az objektum közzététele](./scripts/cli-publish-asset.md)| A Streamelési lokátor hoz létre, és megkapja Streamelési URL-címeket. |
| [Szűrő](filters-dynamic-manifest-cli-howto.md)| Konfigurálja az igény szerinti videó eszköz szűrőt, és bemutatja, hogyan hozhat létre a parancssori felület használatával [Fiókszűrők](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) és [eszköz szűrők](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="see-also"></a>Lásd még

- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
- [Rövid útmutató: Stream-videó fájlok – CLI](stream-files-cli-quickstart.md)
