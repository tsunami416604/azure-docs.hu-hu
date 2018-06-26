---
title: Az Azure Media Services v3 áttekintése | Microsoft Docs
description: Ez a cikk általános áttekintését nyújt a Media Services szolgáltatásról, és további információkat biztosító cikkekre mutató hivatkozásokat tartalmaz.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: azure media services, stream, szórás, élő, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 06/14/2018
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 489801852202163ef40d57da0082e39793196d85
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264094"
---
# <a name="what-is-azure-media-services-v3"></a>Az Azure Media Services v3 ismertetése

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [2. verzió – Általánosan elérhető](../previous/media-services-overview.md)
> * [3. verzió – Előzetes verzió](media-services-overview.md)

> [!NOTE]
> Az Azure Media Services legújabb verziója előzetes verzió, és v3 néven is ismert.

Az Azure Media Services egy felhőalapú platform, amellyel olyan megoldások hozhatók létre, amelyek lehetővé teszik többek között a médiaszínvonalú videostreamelést, a hozzáférhetőség és terjesztés javítását vagy tartalmak elemzését. Függetlenül attól, hogy Ön alkalmazásfejlesztő, vagy egy ügyfélszolgálat, kormányzati intézmény vagy szórakoztató vállalat munkatársa, a Media Services olyan alkalmazások létrehozásához nyújt segítséget, amelyek kiváló minőségű multimédiás élményt nyújtanak nagy célközönség számára napjaink legnépszerűbb mobileszközein és böngészőiben. 

## <a name="what-can-i-do-with-media-services"></a>Mire használhatom a Media Services szolgáltatást?

A Media Services lehetővé teszi különböző média-munkafolyamatok létrehozását a felhőben. Az alábbiakban néhány példa látható arra, hogy mit tehet a Media Services használatával.  

* Különböző formátumú videók biztosítása, hogy számos különféle böngészőben és eszközön le lehessen játszani azokat. A különböző ügyfeleknek (mobileszközökre, tévékészülékekre, számítógépekre stb.) biztosított igény szerinti és élő streamek esetén megfelelően kell kódolni és csomagolni a video- és hangtartalmakat. Az ilyen tartalmak továbbításával és streamelésével kapcsolatban tekintse meg a [fájlok kódolásával és streamelésével foglalkozó rövid útmutatót](stream-files-dotnet-quickstart.md).
* Élő sportesemények, például focimeccsek, baseballmeccsek, felsőoktatási és középiskolai sportesemények és egyebek streamelése nagy online közönség számára. 
* Nyilvános értekezletek és események, például polgármesteri, képviselőtestületi és törvényhozói testületi ülések közvetítése.
* Rögzített video- vagy hangtartalmak elemzése. A vásárlói elégedettség növelése érdekében például a vállalatok szöveggé alakíthatják a beszédet, és keresési indexeket és irányítópultokat hozhatnak létre. Ezután kinyerhetik a gyakori panaszokkal kapcsolatos adatokat, a panaszok forrását és más kapcsolódó adatokat. 
* Előfizetéses videoszolgáltatás létrehozása, és DRM-védelemmel ellátott tartalmak streamelése, ha egy ügyfélnek (például egy filmstúdiónak) korlátoznia kell a szerzői joggal védett munkákhoz való hozzáférést és azok használatát.
* Offline tartalmak biztosítása repülőn, vonaton és autóban való lejátszáshoz. Előfordulhat, hogy egy ügyfélnek le kell töltenie a tartalmat a telefonjára vagy táblagépére a lejátszáshoz, amikor várhatóan nem fognak hálózathoz csatlakozni.
* Feliratok hozzáadása a videókhoz a szélesebb közönség kiszolgálása érdekében (például hallássérültek vagy olyan személyek esetén, akik más nyelven szeretnének feliratot olvasni). 
* Oktatási célú e-tanulási videoplatform megvalósítása az Azure Media Services és az [Azure Cognitive Services API-jai](https://docs.microsoft.com/en-us/azure/#pivot=products&panel=ai) segítségével a beszéd szövegként történő feliratozása, a több nyelvre való fordítás stb. érdekében.
* Az Azure CDN engedélyezése a nagyléptékű méretezés megvalósításához az azonnali nagy terhelések (például a termékbevezetési események kezdetének) jobb kezelése érdekében. 

## <a name="v3-capabilities"></a>A v3 képességei

A v3 egy egységes API felületen alapul, amely az Azure Resource Manager szolgáltatásra épülő felügyeleti és műveleti funkciókat is biztosít. 

Ez a verzió az alábbi képességeket biztosítja:  

* **Átalakítás**, amellyel a médiafeldolgozási vagy az -elemzési feladatok egyszerű munkafolyamatait határozhatja meg. Az átalakítás a video- és hangfájlok feldolgozásának rögzített folyamata. A későbbiekben ismételten alkalmazhatja a tartalomtárban levő összes fájl feldolgozásához a feladatoknak az átalakítási szolgáltatás számára történő elküldésével.
* **Feladatok** a videók feldolgozásához (kódolás vagy elemzés). A bemenő tartalmak HTTP- URL-, SAS URL-címekkel vagy az Azure Blob Storage-ban található fájlok elérési útjával határozhatók meg a feladatokban. 
* **Értesítések**, amelyek monitorozzák a feladat előrehaladását vagy állapotát, az élő csatornák elindítását/leállítását és a hibaeseményeket. Az értesítések az Azure Event Grid értesítési rendszerével vannak integrálva. Könnyedén előfizethet az Azure Media Services több erőforrásának eseményeire. 
* Az **Azure Resource Manager-sablonokkal** átalakítások, streamelési végpontok, csatornák és egyebek hozhatók létre és helyezhetők üzembe.
* **Szerepköralapú hozzáférés-vezérlés** állítható be az erőforrásszinten, amely lehetővé teszi az adott erőforrásokhoz, például az átalakításokhoz, csatornákhoz és egyebekhez való hozzáférés zárolását.
* **Ügyfél SDK-k** több nyelven: .NET, .NET core, Python, Go, Java és Node.js.

## <a name="naming-conventions"></a>Elnevezési konvenciók

Az Azure Media Services v3 erőforrásneveire is (pl. Adategység, Feladatok, Átalakítások) az Azure Resource Manager elnevezési korlátozásai vonatkoznak. Az Azure Resource Manager szolgáltatásnak megfelelően az erőforrásnevek mindig egyediek. Így bármilyen egyedi azonosító sztringet (pl. GUID-ok) használhat erőforrásnévként. 

A Media Services-erőforrás neve nem tartalmazhatja a következőket: "<", ">", "%", "&", ': ','&#92;','?', '/', "*", "+",".", szimpla idézőjel vagy bármely egyéb vezérlőkarakter. Minden egyéb karakter engedélyezett. Az erőforrásnév maximális hossza 260 karakter. 

Az Azure Resource Manager elnevezéseire vonatkozó további információért tekintse meg az [Elnevezési követelmények](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) és az [Elnevezési konvenciók](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) című részeket.

## <a name="how-can-i-get-started-with-v3"></a>Hogyan kezdhetem meg a v3 használatát?

Fejlesztőként a Media Services [REST API-jával](https://go.microsoft.com/fwlink/p/?linkid=873030), illetve a REST API-val való kommunikációt lehetővé tévő ügyfélkódtárakkal könnyedén hozhat létre, felügyelhet és kezelhet egyéni média-munkafolyamatokat. A REST Postman-példát [itt](https://github.com/Azure-Samples/media-services-v3-rest-postman) találja. [Azure Resource Manager-alapú REST API-t](https://github.com/Azure-Samples/media-services-v3-arm-templates) is használhat.

A Microsoft az alábbi ügyfélkódtárakat hozza létre és támogatja: 

|Ügyfélkódtár|Példák|
|---|---|
|[Azure CLI SDK](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)|[Azure CLI-minták](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)|
|[.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0)|[.NET-minták](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials)|
|[.NET Core SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0) (válassza a **.NET CLI** fület)|[.NET Core-minta](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials)|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/mediaservices)||
|[Node.js SDK](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/index?view=azure-node-latest)|[Node.js-minták](https://github.com/Azure-Samples/media-services-v3-node-tutorials)|
|[Python SDK](https://pypi.org/project/azure-mgmt-media/1.0.0rc1/)||
|[Go SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/mediaservices/mgmt/2018-03-30-preview/media)||

A Media Services [Swagger-fájlokat](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media) biztosít, amelyekkel SDK-kat hozhat létre az előnyben részesített nyelvhez/technológiához.  

## <a name="next-steps"></a>További lépések

A [fájlok streamelésével](stream-files-dotnet-quickstart.md) foglalkozó témakörben tekintheti meg, hogy milyen egyszerűen kezdheti meg a videofájlok kódolását és streamelését. 

