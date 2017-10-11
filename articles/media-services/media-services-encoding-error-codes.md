---
title: "Az Azure Media Services kódolási hibakódok |} Microsoft Docs"
description: "Ez a témakör felsorolja a abban az esetben, ha hiba történt a kódolási feladat a végrehajtás során visszatérő hibakódok..."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: f4fd2212d19f89148dde08c75c5a48cdd322d029
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="encoding-error-codes"></a>Kódolási hibakódok

A következő táblázat sorolja fel, ha hiba történt a kódolási feladat a végrehajtás során visszatérő hibakódok.  Hiba legutolsó részletes adatai a .NET-kódban, amelyet a [hiba részletei](http://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) osztály. Hiba legutolsó részletes adatai a REST-kódban, amelyet a [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) REST API-t.

| ErrorDetail.Code | Hiba lehetséges okai |
| --- | --- |
| Ismeretlen |Ismeretlen hiba történt a feladat végrehajtása |
| ErrorDownloadingInputAssetMalformedContent |Kategória hibák, amely lefedi a hibák például rossz fájlneveket, nulla hosszúságú fájlok, a hibás bemeneti eszköz letöltése a formázza és így tovább. |
| ErrorDownloadingInputAssetServiceFailure |Kategória hibák szolgáltatás oldalán - letöltése közben hálózati vagy tárolási hibák példa problémákat ismerteti. |
| ErrorParsingConfiguration |A hibák kategória ahol feladat <see cref="MediaTask.PrivateData"/> (konfiguráció) érvénytelen, például a konfiguráció nem érvényes rendszerrel előre, vagy érvénytelen XML-kódot tartalmaz. |
| ErrorExecutingTaskMalformedContent |Hol található a bemeneti médiafájljait okozza hiba a tevékenység végrehajtása közben hibákat kategóriáját. |
| ErrorExecutingTaskUnsupportedFormat |Kategória hibák, ahol a media feldolgozója nem képes feldolgozni a megadott fájlok - adathordozó formázása nem támogatott, vagy nem felel meg a konfigurációt. Egy eszköz, amelynek csak videó csak kimenete létrehozásához például közben |
| ErrorProcessingTask |Más hibák, amelyek a tevékenység feldolgozása során találkozik az adathordozó processzor tartalom nem kapcsolt kategóriáját. |
| ErrorUploadingOutputAsset |A hibák feltöltésekor a rendszer a kimeneti adategységen kategória |
| ErrorCancelingTask |Kategória fedik le a hibák, a feladat megszakítására tett próbálkozás során hibák |
| TransientError |Kategória hibák, amelyek átmeneti probléma (például) ideiglenes hálózati probléma az Azure Storage szolgáltatással) |

A Súgó a **Media Services** team, nyissa meg a [támogatja a jegy](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Speciális feladatokra kódolási Media Encoder Standard készletek testreszabásával.](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóták és korlátozások](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
