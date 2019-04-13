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
ms.date: 04/11/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: d97682de6c8eb7dd36e25cbd4b8d66d328745748
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543630"
---
# <a name="start-developing-with-media-services-v3-api-using-sdkstools"></a>A Media Services v3 API SDK-k és eszközök használata a fejlesztés megkezdése

A fejlesztők is használhatja a Media Services [REST API-val](https://aka.ms/ams-v3-rest-ref) vagy klienskódtárak, amelyek lehetővé teszik a REST API használatával hozhat létre egyszerűen, kezelésére és karbantartására használható egyéni multimédiás munkafolyamatokat. A [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API az OpenAPI-specifikáció (korábbi nevén Swagger) alapul.

Fejlesztés megkezdése előtt tekintse át a [fejlesztés a Media Services v3 API-k](media-services-apis-overview.md).

> [!NOTE]
> Az Azure Media Services v3 SDK-k nem szálbiztos garantáltan. Ha több szálon futó alkalmazás fejlesztése, hozzá kell adnia a saját szál szinkronizálási logika az ügyfél védelméről, vagy használjon egy új AzureMediaServicesClient objektum szálanként. Is kell óvatos, ha az ügyfél (például egy HttpClient-példányt .NET) kódját által biztosított választható objektumok által bevezetett többszálas problémákat.

Ez a témakör hivatkozásokat az SDK-kkal, eszközökkel, más documentaion.

## <a name="prerequisites"></a>Előfeltételek

Indítsa el a fejlesztését a Media Services, az alábbiak szükségesek:

- Aktív Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a virtuális gép létrehozásának megkezdése előtt.
- [Alapvető fogalmak ismertetése](concepts-overview.md)
- [Egy Media Services-fiók létrehozása – CLI](create-account-cli-how-to.md)

## <a name="start-developing-with-sdks"></a>A fejlesztés megkezdése az SDK használatával

### <a name="net"></a>.NET

Használat [.NET SDK-val](https://aka.ms/ams-v3-dotnet-sdk).

A Media Services felfedezése [.NET ref](https://aka.ms/ams-v3-dotnet-ref) dokumentációját.

### <a name="java"></a>Java

Használat [Java SDK](https://aka.ms/ams-v3-java-sdk).

Tekintse át a Media Services [Java ref](https://aka.ms/ams-v3-java-ref) dokumentációját.

### <a name="nodejs"></a>Node.js

Használat [Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk).

A Media Services felfedezése [Node.js ref](https://aka.ms/ams-v3-nodejs-ref) dokumentáció, és tekintse meg [minták](https://github.com/Azure-Samples/media-services-v3-node-tutorials) , amelyek bemutatják, hogyan használható a Media Services API a node.js használatával.

### <a name="python"></a>Python

Használat [Python SDK](https://aka.ms/ams-v3-python-sdk).

Tekintse át a Media Services [Python ref](https://aka.ms/ams-v3-python-ref) dokumentációját.

### <a name="go"></a>Indítás

Használat [Go SDK](https://aka.ms/ams-v3-go-sdk).

Tekintse át a Media Services [Go ref](https://aka.ms/ams-v3-go-ref) dokumentációját.

### <a name="ruby"></a>Ruby

Használat [Ruby SDK](https://aka.ms/ams-v3-ruby-sdk).

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Az Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) egy olyan eszköz, amely számára szeretne további tudnivalók a Media Services Windows-ügyfelek számára érhető el. AMSE egy Winforms /C# feltöltése, letöltése, kódolás, VOD-adatfolyam és tartalmat a Media Services élő alkalmazás. Az AMSE eszköz olyan ügyfelek, akik a Media Services teszteléséhez kód írása nélkül. Az AMSE kódot szeretne kifejleszteni a Media Services használatával ügyfeleink biztosítunk erőforrásként.

AMSE, egy nyílt forráskódú projektje, a Közösség által biztosított támogatás (problémák jelenteni lehet https://github.com/Azure/Azure-Media-Services-Explorer/issues). A projekt a Microsoft nyílt forráskódú projekteket szabályozó etikai kódexe, a [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) hatálya alá esik. További információ: a [Gyakori](https://opensource.microsoft.com/codeofconduct/faq/) , vagy forduljon opencode@microsoft.com a további kérdéseit és észrevételeit.

## <a name="next-steps"></a>További lépések

[Áttekintés](media-services-overview.md)
