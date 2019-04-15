---
title: Az Azure Media Services v3 SDK-k – Azure
description: Ez a cikk áttekintést elindítása a fejlesztés a Media Services v3 API SDK-k használatával.
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
ms.date: 04/11/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 9fb4d1561a661387f759aada9e776d43a95aa5c7
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564509"
---
# <a name="develop-against-media-services-v3-api-using-sdks"></a>Fejlesztés a Media Services v3 API, SDK-k használatával

A fejlesztők is használhatja a Media Services [REST API-val](https://aka.ms/ams-v3-rest-ref) vagy klienskódtárak, amelyek lehetővé teszik a REST API használatával hozhat létre egyszerűen, kezelésére és karbantartására használható egyéni multimédiás munkafolyamatokat. A [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API az OpenAPI-specifikáció (korábbi nevén Swagger) alapul.

> [!NOTE]
> Az Azure Media Services v3 SDK-k nem szálbiztos garantáltan. Ha több szálon futó alkalmazás fejlesztése, hozzá kell adnia a saját szál szinkronizálási logika az ügyfél védelméről, vagy használjon egy új AzureMediaServicesClient objektum szálanként. Is kell óvatos, ha az ügyfél (például egy HttpClient-példányt .NET) kódját által biztosított választható objektumok által bevezetett többszálas problémákat.

Ez a témakör hivatkozásokat tartalmaz, az SDK-k, az eszközök, az útmutatókat.

## <a name="prerequisites"></a>Előfeltételek

Indítsa el a fejlesztését a Media Services, az alábbiak szükségesek:

- Aktív Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.
- [Alapvető fogalmak ismertetése](concepts-overview.md)
- Felülvizsgálat [fejlesztés a Media Services v3 API-k](media-services-apis-overview.md)
- [Egy Media Services-fiók létrehozása – CLI](create-account-cli-how-to.md)

## <a name="start-developing-with-sdks"></a>A fejlesztés megkezdése az SDK használatával

### <a name="net"></a>.NET

Használat [.NET SDK-val](https://aka.ms/ams-v3-dotnet-sdk) való [Media Services-csatlakozáshoz](configure-connect-dotnet-howto.md).

A Media Services felfedezése [.NET-referencia](https://aka.ms/ams-v3-dotnet-ref) dokumentációját.

### <a name="java"></a>Java

Használat [Java SDK](https://aka.ms/ams-v3-java-sdk) való [Media Services-csatlakozáshoz](configure-connect-java-howto.md).

Tekintse át a Media Services [Javában – referencia](https://aka.ms/ams-v3-java-ref) dokumentációját.

### <a name="nodejs"></a>Node.js

Használat [Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) való [Media Services-csatlakozáshoz](configure-connect-nodejs-howto.md).

A Media Services felfedezése [Node.js-referencia](https://aka.ms/ams-v3-nodejs-ref) dokumentáció, és tekintse meg [minták](https://github.com/Azure-Samples/media-services-v3-node-tutorials) , amelyek bemutatják, hogyan használható a Media Services API a node.js használatával.

### <a name="python"></a>Python

Használat [Python SDK](https://aka.ms/ams-v3-python-sdk).

Tekintse át a Media Services [Python-referencia](https://aka.ms/ams-v3-python-ref) dokumentációját.

### <a name="go"></a>Indítás

Használat [Go SDK](https://aka.ms/ams-v3-go-sdk).

Tekintse át a Media Services [nyissa meg a hivatkozás](https://aka.ms/ams-v3-go-ref) dokumentációját.

### <a name="ruby"></a>Ruby

Használat [Ruby SDK](https://aka.ms/ams-v3-ruby-sdk).

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Az Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) egy olyan eszköz, amely számára szeretne további tudnivalók a Media Services Windows-ügyfelek számára érhető el. AMSE egy Winforms /C# feltöltése, letöltése, kódolás, VOD-adatfolyam és tartalmat a Media Services élő alkalmazás. Az AMSE eszköz olyan ügyfelek, akik a Media Services teszteléséhez kód írása nélkül. Az AMSE kódot szeretne kifejleszteni a Media Services használatával ügyfeleink biztosítunk erőforrásként.

AMSE, egy nyílt forráskódú projektje, a Közösség által biztosított támogatás (problémák jelenteni lehet https://github.com/Azure/Azure-Media-Services-Explorer/issues). A projekt a Microsoft nyílt forráskódú projekteket szabályozó etikai kódexe, a [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) hatálya alá esik. További információ: a [Gyakori](https://opensource.microsoft.com/codeofconduct/faq/) , vagy forduljon opencode@microsoft.com a további kérdéseit és észrevételeit.

## <a name="next-steps"></a>További lépések

[Áttekintés](media-services-overview.md)
