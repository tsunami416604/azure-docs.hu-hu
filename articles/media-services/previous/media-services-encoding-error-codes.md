---
title: Azure Media Services kódolási hibakódok | Microsoft Docs
description: Ez a témakör azokat a hibakódokat sorolja fel, amelyek akkor adhatók vissza, ha hiba történt a kódolási feladat végrehajtása során.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 5c038f0be31acea52c2ef07d43f0dbaf3434a371
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "64709522"
---
# <a name="encoding-error-codes"></a>Kódolási hibakódok

A következő táblázat felsorolja azokat a hibakódokat, amelyeket a rendszer a kódolási feladat végrehajtása során felmerülő hiba esetén visszaadhat.  Ha a .NET-kódban hibát szeretne kapni, használja a [ErrorDetails](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) osztályt. Ha részletes adatokat szeretne kapni a REST-kódban, használja a [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) REST API.

| ErrorDetail. code | Hiba lehetséges okai |
| --- | --- |
| Ismeretlen |Ismeretlen hiba történt a feladat végrehajtása közben |
| ErrorDownloadingInputAssetMalformedContent |Olyan hibák kategóriája, amelyek a bemeneti eszköz (például hibás fájlnevek, nulla hosszúságú fájlok, helytelen formátumok stb.) letöltésével kapcsolatos hibákat fedik le. |
| ErrorDownloadingInputAssetServiceFailure |A szolgáltatással kapcsolatos problémákat fedező hibák kategóriája – például hálózati vagy tárolási hibák a letöltés során. |
| ErrorParsingConfiguration |A hibák kategóriája, \<ahol a feladat a cref = "MediaTask. PrivateData"/> (konfiguráció) nem érvényes, például a konfiguráció nem érvényes rendszer-beállításkészlet, vagy érvénytelen XML-t tartalmaz. |
| ErrorExecutingTaskMalformedContent |A hibák kategóriája annak a feladatnak a végrehajtása során, hogy a bemeneti médiafájlokon belüli problémák hibát okoznak. |
| ErrorExecutingTaskUnsupportedFormat |Azon hibák kategóriája, amelyekben a média processzora nem tudja feldolgozni a megadott fájlokat – a média formátuma nem támogatott, vagy nem felel meg a konfigurációnak. Például egy csak hang kimenetet szeretne létrehozni egy olyan eszközről, amely csak videóval rendelkezik |
| ErrorProcessingTask |Az adathordozó-feldolgozó által a tartalomhoz nem kapcsolódó feladatok feldolgozásakor észlelt egyéb hibák kategóriája. |
| ErrorUploadingOutputAsset |A kimeneti eszköz feltöltésekor előforduló hibák kategóriája |
| ErrorCancelingTask |A feladat megszakítását megkísérlő hibák kategóriája |
| TransientError |Az átmeneti problémákra vonatkozó hibák kategóriája (például átmeneti Hálózatkezelési problémák az Azure Storage-ban |

Ha segítséget szeretne kérni a **Media Services** csapattól, nyisson meg egy [támogatási jegyet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Speciális kódolási feladatok végrehajtása a Media Encoder Standard-készletek testreszabásával](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóták és korlátozások](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
