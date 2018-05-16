---
title: Az Azure Media Services áttekintése | Microsoft Docs
description: Ezen témakör áttekintést nyújt az Azure Media Services szolgáltatásairól
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 7a5e9723-c379-446b-b4d6-d0e41bd7d31f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 10/24/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 77ac0bb3ea941d35faa31f7f8a5d7f3f04971316
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="azure-media-services-overview"></a>Az Azure Media Services áttekintése 

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [2. verzió – Általánosan elérhető](media-services-overview.md)
> * [3. verzió – Előzetes verzió](../latest/media-services-overview.md)

Microsoft Azure Media Services egy bővíthető, felhőalapú platform, amely lehetővé teszi a fejlesztők számára méretezhető médiafelügyeleti és -továbbítási alkalmazások létrehozását. A Media Services alapjai a REST API-k, amelyek lehetővé teszik különböző videó- és audiotartalmak feltöltését, tárolását, kódolását és becsomagolását, majd igény szerinti és élő adatfolyamként történő továbbítását különböző ügyfelek részére (például tévékészülékekre, számítógépekre és mobileszközökre).

A Media Services használatával teljes, átfogó munkafolyamatokat hozhat létre. A munkafolyamatok bizonyos részeihez harmadik féltől származó összetevőket is szabadon felhasználhat. Például egy harmadik féltől származó kódolóval végezhet kódolást. Ezután a Media Services használatával intézheti a feltöltést, a védelmet, a csomagolást és a továbbítást.

A tartalmakat továbbíthatja streamként, illetve az ügyfelek igénye szerint is. Emellett a témakör hivatkozásokat is tartalmaz egyéb, kapcsolódó témakörökre.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
Az AMS képzési terveket itt tekintheti meg:

* [AMS élő adatfolyam-továbbítási munkafolyamat](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
* [AMS igényalapú streaming-munkafolyamat](https://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

## <a name="prerequisites"></a>Előfeltételek

Az Azure Media Services használatának megkezdéséhez rendelkeznie kell a következőkkel:

* Egy Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com).
* Egy Azure Media Services-fiók. További információ: [Fiók létrehozása](media-services-portal-create-account.md)
* (Választható:) A fejlesztési környezet beállítása Válassza ki, hogy a .NET vagy a REST API fejlesztési környezetet kívánja-e használni. További információ: [Környezet beállítása](media-services-dotnet-how-to-use.md)

    Emellett megismerheti az [AMS API-hoz való programozott csatlakozást](media-services-use-aad-auth-to-access-ams-api.md) is.
* Elindított állapotú standard vagy prémium szintű streamvégpont.  További információ: [Streamvégpontok felügyelete](media-services-portal-manage-streaming-endpoints.md)

## <a name="sdks-and-tools"></a>SDK-k és eszközök

A Media Services-megoldások létrehozásához a következőket használhatja:

* [Media Services REST API](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* Az elérhető ügyféloldali SDK-k valamelyike:
    * [.NET-keretrendszerhez készült Azure Media Services SDK](https://github.com/Azure/azure-sdk-for-media-services),
    * [Javához készült Azure SDK](https://github.com/Azure/azure-sdk-for-java),
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php),
    * [Node.js-hez készült Azure Media Services](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Ez a Node.js SDK nem Microsoft által készített verziója. Ennek a karbantartását egy közösség végzi, és jelenleg nem fedi le 100%-osan az AMS API-k tartalmát.)
* Meglévő eszközök:
    * [Azure Portal](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Az Azure Media Services Explorer (AMSE) egy Winforms/C#-alkalmazás Windows rendszerre)

> [!NOTE]
> A Java SDK legújabb verziójának beszerzéséhez és a Java-fejlesztés megkezdéséhez tekintse meg [Az Azure Media Services Java ügyfél-SDK használatának megkezdése](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use) című cikket. <br/>
> A legfrissebb Media Services PHP SDK letöltéséhez keresse meg a Microsoft Azure-csomag 0.5.7-es verzióját a [Packagist-adattárban](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="code-samples"></a>Kódminták

Az **Azure-kódminták** katalógusban számos kódmintát talál: [Azure Media Services-kódminták](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0).

## <a name="concepts"></a>Alapelvek

Az Azure Media Services alapfogalmaiért lásd: [Fogalmak](media-services-concepts.md)

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Támogatott helyzetek és a Media Services rendelkezésre állása az egyes adatközpontokban

Részletes információkért lásd az [AMS-forgatókönyvek és a funkciók és szolgáltatások az egyes adatközpontokban történő rendelkezésre állását](scenarios-and-availability.md) ismertető cikket.

## <a name="service-level-agreement-sla"></a>Szolgáltatói szerződés (SLA)

* A Media Services kódolási funkciójához szükséges REST API-tranzakciókhoz 99,9%-os rendelkezésre állást garantálunk.
* A streamelési funkciók szolgáltatási kéréseinek sikeres kiszolgálására 99,9%-os rendelkezésre állást garantálunk a létező médiatartalmak esetében egy standard vagy prémium szintű streamvégpont megvásárlásakor.
* Az élő csatornák esetében garantáljuk, hogy a futó csatornák az idő legalább 99,9%-ában elérhetők lesznek kívülről.
* A Content Protection esetében garantáljuk, hogy a kulcskéréseket az idő 99,9%-ában sikeresen teljesítjük.
* Az indexelő esetében sikeresen, 99,9%-os rendelkezésre állási garanciával kiszolgáljuk az egy kódolási egységgel feldolgozott indexelési kéréseket.

További információ: [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/)

Az adatközpontokban lévő rendelkezésre állásról információért lásd a [Rendelkezésre állás](scenarios-and-availability.md#availability) című szakaszt.

## <a name="support"></a>Támogatás

[Az Azure-támogatási szolgáltatások](https://azure.microsoft.com/support/options/) támogatási lehetőségeket biztosítanak az Azure szolgáltatásaival, köztük a Media Services szolgáltatással kapcsolatban.

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
