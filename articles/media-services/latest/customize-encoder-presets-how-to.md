---
title: Egyéni átalakítás kódolása a Media Services 3.NET v - Azure használatával | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan használhatja az Azure Media Services v3-as egyéni átalakítást a .NET használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/03/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ebe701032e6416b3e007a28db62f5a8235bb1bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068034"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Hogyan kódolj egyéni átalakítással - .NET

Az Azure Media Services használatával történő kódolás során gyorsan elkezdheti az iparági bevált eljárásokon alapuló ajánlott beépített készletek egyikét, ahogy azt a [Streamelési fájlok](stream-files-tutorial-with-api.md) oktatóanyaga is bemutatja. Egyéni készletet is létrehozhat az adott forgatókönyv vagy eszköz követelményeinek megcélzásához.

## <a name="considerations"></a>Megfontolandó szempontok

Egyéni készletek létrehozásakor a következő szempontok érvényesek:

* Az AVC-tartalom magasságának és szélességének minden értékének a 4 többszörösének kell lennie.
* Az Azure Media Services v3-as részében az összes kódolási bitsebesség bitper másodpercben van. Ez eltér a v2 API-kkal rendelkező készletektől, amelyek kilobit/második egységként használták a kilobitokat/ a második at. Ha például a v2-ben a bitráta 128 (kilobit/másodperc), akkor a v3-ban 128000 (bit/másodperc) lesz megadva.

## <a name="prerequisites"></a>Előfeltételek 

[Media Services-fiók létrehozása](create-account-cli-how-to.md)

## <a name="download-the-sample"></a>A minta letöltése

Klónozzon egy GitHub-tárházat, amely a teljes .NET Core mintát tartalmazza a gépre a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Az egyéni készletminta az [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) mappában található.

## <a name="create-a-transform-with-a-custom-preset"></a>Átalakítás létrehozása egyéni készlettel 

Új [átalakítás](https://docs.microsoft.com/rest/api/media/transforms)létrehozásakor meg kell adnia, hogy mit szeretne kimenetként létrehozni. A kötelező paraméter egy [TransformOutput](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#transformoutput) objektum, ahogyan az az alábbi kódban látható. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. A **készlet** a kívánt **TransformOutput**létrehozásához használt video- és/vagy hangfeldolgozási műveletek lépésenkénti utasításait ismerteti. A következő **TransformOutput** egyéni kodek- és rétegkimeneti beállításokat hoz létre.

[Átalakítások](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor ellenőrizze a **Get** metódussal, hogy létezik-e már átalakítás, ahogyan az az alábbi kódban látható. A Media Services v3-as, **Get** metódusok entitások null értéket ad **vissza,** ha az entitás nem létezik (a kis- és nagybetűk nem megkülönböztető ellenőrzése a név).

### <a name="example"></a>Példa

A következő példa azokat a kimeneteket határozza meg, amelyeket az átalakítás során létre szeretnénk hozni. Először a hangkódoláshoz adunk hozzá Egy AacAudio réteget, és két H264Video réteget a videó kódoláshoz. A videorétegeken címkéket rendelünk hozzá, hogy azok használhatók legyenek a kimeneti fájlnevekben. Ezután azt szeretnénk, hogy a kimenet is tartalmazza a miniatűröket. Az alábbi példában png formátumú képeket adunk meg, amelyek a bemeneti videó felbontásának 50%-ában, és a bemeneti videó hosszának {25%, 50%, 75} - létrehozásához. Végül, mi határozza meg a formátumot a kimeneti fájlokat - az egyik a videó + audio, és egy másik a miniatűrök. Mivel több H264Layers-ünk van, olyan makrókat kell használnunk, amelyek rétegenként egyedi neveket hoznak létre. Használhatjuk vagy `{Label}` `{Bitrate}` makrót, a példa az előbbit mutatja.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>További lépések

[Fájlok streamelése](stream-files-tutorial-with-api.md) 
