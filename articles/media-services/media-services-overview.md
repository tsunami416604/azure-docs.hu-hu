---
title: "Az Azure Media Services áttekintése | Microsoft Docs"
description: "Ezen témakör áttekintést nyújt az Azure Media Services szolgáltatásairól"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 7a5e9723-c379-446b-b4d6-d0e41bd7d31f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 07/04/2017
ms.author: juliako;anilmur
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 38c3637cc477e24ac8d056611e7fda9a4d693cdb
ms.contentlocale: hu-hu
ms.lasthandoff: 07/06/2017

---
# <a name="azure-media-services-overview"></a>Az Azure Media Services áttekintése 

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

## <a name="concepts-and-overview"></a>Alapfogalmak és áttekintés
Az Azure Media Services alapfogalmaiért lásd: [Fogalmak](media-services-concepts.md)

Az Azure Media Services összes fő összetevőjét bemutató útmutató-sorozat: [Az Azure Media Services részletes oktatóprogramjai](https://docs.com/fukushima-shigeyuki/3439/english-azure-media-services-step-by-step-series) Ez a sorozat átfogó áttekintést nyújt a fogalmakról, és az AMSE eszköz használatával mutatja be az AMS-feladatokat. Az AMSE eszköz egy Windows-eszköz. Az eszköz támogatja a legtöbb olyan műveletet, amelyek a [.NET-keretrendszerhez készült AMS SDK](https://github.com/Azure/azure-sdk-for-media-services), a [Javához készült Azure SDK](https://github.com/Azure/azure-sdk-for-java) vagy az [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php) használatával programozás útján megvalósíthatók.

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

[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

