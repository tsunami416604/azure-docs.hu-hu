---
title: Az Azure Media Services v3 SDK-k – Azure
description: Ez a cikk áttekintést elindítása a fejlesztés a Media Services v3 API SDK-k és eszközök használatával.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/20/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: cda6c9cd1f9c8b9305349f0904aeb744ba373711
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58350249"
---
# <a name="start-developing-with-media-services-v3-api-using-sdkstools"></a>A Media Services v3 API SDK-k és eszközök használata a fejlesztés megkezdése

A fejlesztők is használhatja a Media Services [REST API-val](https://aka.ms/ams-v3-rest-ref) vagy klienskódtárak, amelyek lehetővé teszik a REST API használatával hozhat létre egyszerűen, kezelésére és karbantartására használható egyéni multimédiás munkafolyamatokat. A [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API az OpenAPI-specifikáció (korábbi nevén Swagger) alapul.

Ez a témakör hivatkozásokat tartalmaz az SDK-k, eszközök, dokumentáció. A különböző fejlesztési env hasznos információkat is tartalmaz.

## <a name="prerequisites"></a>Előfeltételek

Indítsa el a fejlesztését a Media Services, az alábbiak szükségesek:

- Aktív Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.
- [Alapvető fogalmak ismertetése](concepts-overview.md)
- [A Media Services-fiók létrehozása](create-account-cli-how-to.md)

## <a name="start-developing"></a>A fejlesztés megkezdése

Az Azure Media Services támogatja a következő SDK-k és eszközök 

- [Azure CLI](https://aka.ms/ams-v3-cli) 
- [.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk)
- [Python SDK](https://aka.ms/ams-v3-python-sdk)
- [Go SDK](https://aka.ms/ams-v3-go-sdk)
- [Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)

### <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Az Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) egy olyan eszköz, amely számára szeretne további tudnivalók a Media Services Windows-ügyfelek számára érhető el. AMSE egy Winforms /C# feltöltése, letöltése, kódolás, VOD-adatfolyam és tartalmat a Media Services élő alkalmazás. Az AMSE eszköz olyan ügyfelek, akik a Media Services teszteléséhez kód írása nélkül. Az AMSE kódot szeretne kifejleszteni a Media Services használatával ügyfeleink biztosítunk erőforrásként.

AMSE, egy nyílt forráskódú projektje, a Közösség által biztosított támogatás (problémák jelenteni lehet https://github.com/Azure/Azure-Media-Services-Explorer/issues). A projekt a Microsoft nyílt forráskódú projekteket szabályozó etikai kódexe, a [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) hatálya alá esik. További információ: a [Gyakori](https://opensource.microsoft.com/codeofconduct/faq/) , vagy forduljon opencode@microsoft.com a további kérdéseit és észrevételeit.

## <a name="rest"></a>REST

A fejlesztés megkezdése az a Media Services REST API-k, telepítse a REST-ügyféllel. Például **Postman**, **Visual Studio Code** az REST beépülő modullal, vagy **Telerik Fiddler**. Használjuk [Postman](media-rest-apis-with-postman.md) Media Services v3 minták.

A Media Services felfedezése [REST API-val ref](https://aka.ms/ams-v3-rest-ref) dokumentáció, és tekintse meg a következő példákban:

- [Oktatóanyag: URL-cím alapján egy távoli fájl kódolása és streamelése a videó – REST](stream-files-tutorial-with-rest.md)
- [Fájlok feltöltése Media Services-fiók – REST](upload-files-rest-how-to.md)
- [Szűrők létrehozása a Media Services – REST](filters-dynamic-manifest-rest-howto.md)
- [Azure Resource Manager-alapú REST API](https://github.com/Azure-Samples/media-services-v3-arm-templates)

<!-- ## CLI -->
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

A Media Services felfedezése [Azure CLI-vel ref](https://aka.ms/ams-v3-cli-ref) dokumentáció, és tekintse meg a következő példákban:

- [Scale a Media szolgáltatás számára fenntartott egység – CLI](media-reserved-units-cli-how-to.md)
- [Egy Media Services-fiók létrehozása – CLI](./scripts/cli-create-account.md) 
- [Alaphelyzetbe fiókhitelesítő adatai – CLI](./scripts/cli-reset-account-credentials.md)
- [Eszközök – parancssori felület létrehozása](./scripts/cli-create-asset.md)
- [CLI - fájl feltöltése](./scripts/cli-upload-file-asset.md)
- [Átalakítások – parancssori felület létrehozása](./scripts/cli-create-transform.md)
- [Hozzon létre feladatokat – CLI](./scripts/cli-create-jobs.md)
- [EventGrid - létrehozása a parancssori felület](./scripts/cli-create-event-grid.md)
- [Teszi közzé az objektumot – CLI](./scripts/cli-publish-asset.md)
- [Szűrő – CLI](filters-dynamic-manifest-cli-howto.md)

## <a name="net"></a>.NET

A Media Services felfedezése [.NET ref](https://aka.ms/ams-v3-dotnet-ref) dokumentáció, és tekintse meg a következő példákban:

- [Oktatóanyag: Feltöltése, kódolása és streamelése videók – .NET](stream-files-tutorial-with-api.md) 
- [Oktatóanyag: A Media Services v3 - .NET élő Stream](stream-live-tutorial-with-api.md)
- [Oktatóanyag: Elemezhet videókat a Media Services v3 – .NET](analyze-videos-tutorial-with-api.md)
- [Hozzon létre egy feladat bemenete egy helyi fájlból – .NET](job-input-from-local-file-how-to.md)
- [Hozzon létre egy feladat bemenete HTTPS URL-címet – .NET](job-input-from-http-how-to.md)
- [Az egyéni átalakítási – .NET kódolása](customize-encoder-presets-how-to.md)
- [AES-128, a dinamikus titkosítás és a kulcstovábbítást – .NET](protect-with-aes128.md)
- [DRM a dinamikus titkosítás és a licenc kézbesítési szolgáltatás használata – .NET](protect-with-drm.md)
- [A meglévő házirend - .NET aláíró kulcs lekérése](get-content-key-policy-dotnet-howto.md)
- [Szűrők létrehozása a Media Services – .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Az Azure Functions v2 a Media Services v3 igény szerinti videó példák speciális](https://aka.ms/ams3functions)

## <a name="java"></a>Java

Tekintse át a Media Services [Java ref](https://aka.ms/ams-v3-java-ref) dokumentációját.

## <a name="nodejs"></a>Node.js

A Media Services felfedezése [Node.js ref](https://aka.ms/ams-v3-nodejs-ref) dokumentáció, és tekintse meg [minták](https://github.com/Azure-Samples/media-services-v3-node-tutorials) , amelyek bemutatják, hogyan használható a Media Services API a node.js használatával.

## <a name="python"></a>Python

Tekintse át a Media Services [Python ref](https://aka.ms/ams-v3-python-ref) dokumentációját.

## <a name="go"></a>Indítás

Tekintse át a Media Services [Go ref](https://aka.ms/ams-v3-go-ref) dokumentációját.

## <a name="next-steps"></a>További lépések

- [Fiók létrehozása – CLI](create-account-cli-how-to.md)
- [Hozzáférés API-k – parancssori felület](access-api-cli-how-to.md)

