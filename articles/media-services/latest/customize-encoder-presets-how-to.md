---
title: Kódolás a Media Services v3 – az Azure használatával egyéni átalakító |} A Microsoft Docs
description: Ez a témakör bemutatja az Azure Media Services v3 használatával egyéni átalakító kódolása.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 12/08/2018
ms.author: juliako
ms.openlocfilehash: c62d9132cdd7eb2ebcbecc3c417ad30d368a278a
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138704"
---
# <a name="how-to-encode-with-a-custom-transform"></a>Az egyéni átalakítási kódolása

Ha az Azure Media Services encoding, ismerkedhet meg gyorsan az, ahogyan az ágazatban kialakult bevált gyakorlaton alapuló ajánlott a beépített beállítások egyikét a [fájlok Streamelési](stream-files-tutorial-with-api.md) oktatóanyagot, vagy dönthet úgy, hogy egyéni előre definiált, amelyekre a saját forgatókönyvéhez vagy eszköz követelményeinek. 

> [!Note]
> Az Azure Media Services v3-as a kódolási átviteli sebességek összes bit / másodperc. Ez eltér a többi v2 Media Encoder Standard készletek. Ha például az átviteli sebesség a v2-ben ezt kell megadni, mint 128, de a v3-as 128000 lenne.

## <a name="download-the-sample"></a>A minta letöltése

Klónozza a GitHub-adattár, amely tartalmazza a teljes .NET Core-minta a gépre a következő paranccsal:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials.git
 ```
 
Az egyéni előre definiált mintát található a [EncodeCustomTransform](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/EncodeCustomTransform/) mappát.

## <a name="create-a-transform-with-a-custom-preset"></a>Hozzon létre egy-egy átalakítási egyéni előbeállítás 

Egy új létrehozásakor [átalakítása](https://docs.microsoft.com/rest/api/media/transforms), meg kell adnia a kívánt műveleteket, mint kimenet előállításához. A kötelező paraméter egy **TransformOutput** objektum, ahogyan az az alábbi kódban látható. Minden **TransformOutput** objektum tartalmaz **előzetes beállításokat**. Az **előzetes beállítások** részletesen leírják azokat a video- és audiofeldolgozási műveleteket, amelyek a kívánt **TransformOutput** objektum előállításához szükségesek. A következő **TransformOutput** hoz létre egyéni kodeket, és a réteg kimeneti beállításait.

[Átalakítások](https://docs.microsoft.com/rest/api/media/transforms) létrehozásakor ellenőrizze a **Get** metódussal, hogy létezik-e már átalakítás, ahogyan az az alábbi kódban látható.  A Media Services v3 **első** módszerek is szolgálnak az entitásokon **null** Ha az entitás nem létezik (a kis-és ellenőrizze a nevét).

[!code-csharp[Main](../../../media-services-v3-dotnet-core-tutorials/NETCore/EncodeCustomTransform/MediaV3ConsoleApp/Program.cs#EnsureTransformExists)]

## <a name="next-steps"></a>További lépések

[Adatfolyam-fájlok](stream-files-tutorial-with-api.md) 
