---
title: A streamelési Lokátorok az Azure Media Services |} A Microsoft Docs
description: Ez a cikk lehetővé teszi a Streamelési Lokátorok vannak, és hogyan használják az Azure Media Services ismertetése.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 51aa33e4ff387a1030dac42bce8d12cf72343b35
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317476"
---
# <a name="streaming-locators"></a>Streamelési lokátor

Győződjön meg arról, videók, a kimenetben az eszköz érhető el az ügyfelek számára a lejátszás, létre kell hoznia egy [Streamelési lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators) majd kialakítható a streamelési URL-címek. .NET-minta: [beolvasása egy Streamelési lokátor](stream-files-tutorial-with-api.md#get-a-streaming-locator).

A folyamat létrehozásának egy **Streamelési lokátor** közzététel nevezzük. Alapértelmezés szerint a **Streamelési lokátor** érvényes az API-hívások végrehajtása után azonnal, és tart, amíg nem törli, ha nem konfigurál a választható kezdő és befejező időpontok. 

Létrehozásakor egy **Streamelési lokátor**, meg kell adnia a [eszköz](https://docs.microsoft.com/rest/api/media/assets) nevét és a [Streamelési házirend](https://docs.microsoft.com/rest/api/media/streamingpolicies) nevét. Használja az előre definiált adatfolyam-szabályzatokra, vagy egy egyéni szabályzatot hozott létre. A jelenleg elérhető előre meghatározott házirendek a következők: 'Predefined_DownloadOnly', 'Predefined_ClearStreamingOnly', 'Predefined_DownloadAndClearStreaming', 'Predefined_ClearKey', 'Predefined_MultiDrmCencStreaming' and 'Predefined_MultiDrmStreaming'. Egyéni használatakor adatfolyam-szabályzatot, tervezzen egy részéhez az ilyen szabályzatok a Media Services-fiók, és újból felhasználja őket a Streamelési Lokátorok, amikor a beállítások és a protokollok van szükség. 

Ha meg szeretné határozni a titkosítási beállítások a streamben, hozza létre a [tartalom kulcs házirend](https://docs.microsoft.com/rest/api/media/contentkeypolicies) , amely beállítja a tartalomkulcsot a rendszer hogyan továbbítja a Media Services kulcs kézbesítési összetevője keresztül végfelhasználók. A Streamelési lokátor való társítása a **tartalom kulcs házirend** és a tartalomkulcsot. A Media Services engedélyezheti a automatikus létrehozása a kulcsot. A következő .NET-példa bemutatja, hogyan AES-titkosítás konfigurálása a Media Services v3 token korlátozás: [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted). **Tartalom-kulcs házirendjei** frissíthető, érdemes lehet frissíteni a szabályzatot, ha kell tennie egy kulcsrotálás azok. A kulcs kézbesítési gyorsítótárak frissítése, és vegye fel a frissített szabályzatot esetében akár 15 percet is igénybe vehet. Javasoljuk, hogy nem az új tartalom kulcs szabályzat létrehozása az egyes Streamelési lokátor. Próbálkozzon újra felhasználhatja a meglévő szabályzatokat, amikor szükség van a ugyanazokkal a beállításokkal.

> [!IMPORTANT]
> * Tulajdonságainak **Streamelési Lokátorok** a DateTime típusú állandóan UTC formátumban vannak.
> * Korlátozott számú házirendeket tervezzen a Media Services-fiók és újból felhasználja őket a Streamelési Lokátorok, amikor szükség van a beállítások. 

## <a name="associate-filters-with-streaming-locators"></a>Szűrők társítani a Streamelési Lokátorok

Egy listában megadhatja [eszköz vagy a fiók szűrők](filters-concept.md), amely a alkalmazni szeretné a [Streamelési lokátor](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body). A [dinamikus packager](dynamic-packaging-overview.md) vonatkozik ez a lista azokat az URL-címet adja meg az ügyfél és-szűrők. Állít elő, ez a kombináció egy [dyanamic jegyzékfájl](filters-dynamic-manifest-overview.md), amely alapján az URL-címben szűrők + szűrők megad a Streamelési lokátor. Azt javasoljuk, hogy a szolgáltatás használata, ha alkalmazza a szűrőket, de nem szeretné elérhetővé tenni az URL-szűrő nevét.

## <a name="filter-order-page-streaming-locator-entities"></a>Szűrő, a sorrendben, a Streamelési lokátor entitások lap

Lásd: [szűrése, rendezése, a Media Services entitások lapozás](entities-overview.md).

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: Feltöltése, kódolása és streamelése a .NET használatával kíván videókat](stream-files-tutorial-with-api.md)
* [DRM a dinamikus titkosítás és a licenc kézbesítési szolgáltatás használata](protect-with-drm.md)
