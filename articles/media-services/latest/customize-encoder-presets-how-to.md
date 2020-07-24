---
title: Egyéni átalakítás kódolása a Media Services v3 .NET-Azure használatával | Microsoft Docs
description: Ez a témakör bemutatja, hogyan kódolhat egyéni átalakításokat a .NET használatával a Azure Media Services v3 használatával.
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
ms.openlocfilehash: 28525c198bb6863e942f5ff33d17226769efdc21
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87001145"
---
# <a name="how-to-encode-with-a-custom-transform---net"></a>Kódolás egyéni átalakítással – .NET

Ha Azure Media Services kódolást használ, gyorsan megkezdheti az egyik javasolt beépített beállításkészletet az iparág ajánlott eljárásai alapján, ahogyan azt a [streaming Files](stream-files-tutorial-with-api.md) oktatóanyag mutatja. Létrehozhat egy egyéni beállításkészletet is, amely az adott forgatókönyv vagy eszköz követelményeit célozza meg.

## <a name="considerations"></a>Megfontolandó szempontok

Egyéni beállításkészletek létrehozásakor a következő szempontokat kell figyelembe venni:

* Az AVC-tartalom magasságának és szélességének összes értékének a 4-es többszörösének kell lennie.
* A Azure Media Services V3 esetében az összes kódolási bitrátát bit/másodpercben kell kiszámítani. Ez különbözik az előre beállított v2 API-kkal, amelyek az egységhez kilobit/másodpercet használnak. Ha például a v2-es bitrátát 128 (kilobit/másodperc) értékre adták, a v3-as verzióban a 128000 (BITS/Second) értéket adja meg.

## <a name="prerequisites"></a>Előfeltételek 

[Media Services-fiók létrehozása](./create-account-howto.md)

## <a name="download-the-sample"></a>A minta letöltése

Egy GitHub-tárház klónozása, amely a teljes .NET Core mintát tartalmazza a gépen a következő parancs használatával:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Az egyéni előre definiált minta a [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) mappában található.

## <a name="create-a-transform-with-a-custom-preset"></a>Átalakítás létrehozása egyéni beállításkészlettel 

Új [átalakítás](/rest/api/media/transforms)létrehozásakor meg kell adnia, hogy mit szeretne kimenetként létrehozni. A kötelező paraméter egy [TransformOutput](/rest/api/media/transforms/createorupdate#transformoutput) objektum, ahogyan az az alábbi kódban látható. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. A **beállításkészlet** a kívánt **TransformOutput**létrehozásához használt videó-és/vagy hangfeldolgozási műveletek részletes utasításait írja le. Az alábbi **TransformOutput** egyéni kodekeket és rétegbeli kimeneti beállításokat hoz létre.

[Átalakítások](/rest/api/media/transforms) létrehozásakor ellenőrizze a **Get** metódussal, hogy létezik-e már átalakítás, ahogyan az az alábbi kódban látható. A (z) Media Services v3-as verziójában az entitások metódusának **beolvasása** **Null értéket** ad vissza, ha az entitás nem létezik (kis-és nagybetű nem megkülönbözteti a nevet)

### <a name="example"></a>Példa

Az alábbi példa azokat a kimeneteket határozza meg, amelyeket az átalakító használatakor szeretnénk generálni. Először hozzáadunk egy AacAudio réteget a hangkódoláshoz, és két H264Video réteget a videó kódolásához. A videó rétegekben címkéket rendelünk hozzá, hogy használhatók legyenek a kimeneti fájlnevekben. Ezután azt szeretnénk, hogy a kimenet bélyegképeket is tartalmazzon. Az alábbi példában a bemeneti videó felbontásának 50%-ában, valamint három időbélyeggel ({25%, 50%, 75%}) adjuk meg a képeket PNG formátumban. Végül megadjuk a kimeneti fájlok formátumát – az egyiket a videó + hang, a másik pedig a miniatűrökhöz. Mivel több H264Layers is rendelkezünk, olyan makrókat kell használnia, amelyek egyedi neveket hoznak létre rétegként. Használhatunk egy vagy egy `{Label}` `{Bitrate}` makrót is, a példa az előzőt mutatja.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>További lépések

[Fájlok streamelése](stream-files-tutorial-with-api.md) 
