---
title: Az Azure Media Services v3 kibocsátási megjegyzései |} A Microsoft Docs
description: A legújabb fejlemények az naprakész információkat, ez a cikk az Azure Media Services v3 legújabb frissítéseit.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/22/2018
ms.author: juliako
ms.openlocfilehash: 10bd2101839e1b6d1dbdc1e53c31693ab0be98fd
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647765"
---
# <a name="azure-media-services-v3-release-notes"></a>Az Azure Media Services v3 kibocsátási megjegyzései 

Naprakész a legújabb fejlemények, ez a cikk azt ismerteti kapcsolatban:

* A legújabb kiadásaihoz.
* Ismert problémák
* Hibajavítások
* Elavult funkciók
* Módosítások tervek

## <a name="october-2018---ga"></a>Október 2018 – GA

Ez a szakasz ismerteti az Azure Media Services (AMS). októberi frissítések.

### <a name="rest-v3-ga-release"></a>REST v3 GA kiadás

A [REST v3 GA kiadás](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) további API-kat tartalmaz Live, a fiók vagy eszköz szintű dinamikusjegyzék-szűrők és a digitális Jogkezelés támogatása.

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Az Azure CLI 2.0 modul jelenleg tartalmaz Tartalomszabályzat kulcs, a Live Streaming szabályzatokra irányuló műveletek. További információkért lásd: [módosításokat tervez](#plans-for-changes).

#### <a name="azure-resource-management"></a>Azure-erőforrások kezelése 

Azure Resource Management támogatása lehetővé teszi, hogy egységes felügyeleti és műveleti API (most már minden egy helyen).

Ettől a kiadástól kezdve használhatja Resource Manager-sablonok élő eseményeket létrehozásához.

#### <a name="improvement-of-asset-operations"></a>Az Eszközintelligencia műveletek javítása 

A következő fejlesztései jelentek meg:

- Betöltési http (s) URL-címek vagy az Azure Blob Storage SAS URL-címeit.
- Adja meg, az eszközök a saját tároló neve. 
- Egyszerűbb a kimeneti támogatási egyéni munkafolyamatokat hozhat létre az Azure Functions használatával.

#### <a name="new-transform-object"></a>Új átalakítás objektum

Az új **átalakítása** objektum leegyszerűsíti a kódolás modell. Az új objektum megkönnyíti a kódolási Resource Manager-sablonok és a készletek létrehozásához és megosztásához. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Az Azure Active Directory-hitelesítés és az RBAC

Az Azure AD-hitelesítés és a szerepköralapú hozzáférés-vezérlés (RBAC) engedélyezése biztonságos átalakítások, LiveEvents, Tartalomszabályzat kulcs vagy szerepkör vagy a felhasználók eszközeinek az Azure ad-ben.

#### <a name="client-sdks"></a>Ügyfél-SDK-k  

A Media Services v3 által támogatott nyelvek: .NET Core, Java, Node.js, a Ruby, a Typescript, Python, Go.

#### <a name="live-encoding-updates"></a>Live encoding frissítések

Az alábbi élő kódolás frissítések jelennek meg:

- Az új közel valós idejű módban élő (10 másodperc – végpontok).
- Továbbfejlesztett RMTP-támogatása (fokozott stabilitás és további forráskód kódoló).
- Biztonságos RTMPS betöltését.

    Amikor létrehoz egy videókhoz, akkor most get 4 betöltési URL-címeket. A 4 betöltési URL-címek olyan majdnem teljesen megegyezik, rendelkezik a azonos streamelési token (alkalmazásazonosító), csak a port száma rész nem egyezik. Az URL-címek kettő elsődleges és tartalék RTMPS számára. 
- átkódolása 24 órás támogatás. 
- Továbbfejlesztett támogatást ad jelzés RTMP SCTE35 keresztül.

#### <a name="improved-event-grid-support"></a>Továbbfejlesztett Event Grid-támogatás

A következő fejlesztéseket támogatja az Event Grid tekintheti meg:

- Azure EventGrid-integráció a Logic Apps és az Azure Functions egyszerűbben fejlesztési. 
- Fizessen elő a kódolás, élő csatornák és egyéb eseményeket.

### <a name="cmaf-support"></a>CMAF támogatása

CMAF és "cbcs" titkosítás támogatása (iOS 11 +) Apple HLS és MPEG-DASH, amelyek támogatják a CMAF lejátszók.

### <a name="video-indexer"></a>Video Indexer

Video Indexer GA kiadás augusztusban mutattuk be. Új jelenleg támogatott funkciókkal kapcsolatos információkért lásd: [Mi az a Video Indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Módosítások tervek

Az Azure CLI-vel kiadási 11/06/2018 hamarosan elérhető.

### <a name="known-issues"></a>Ismert problémák

Csak az előzetes API használt eszköz vagy AccountFilters ügyfelek a következő probléma által érintett.

Ha a hozott eszközök vagy Fiókszűrők 09/28 és 10 közötti/12, a Media Services v3 parancssori felület vagy az API-k, kell távolítsa el az összes eszköz és AccountFilters, és hozza létre őket ismét verzió ütközés miatt. 

## <a name="may-2018---preview"></a>2018 május – előzetes verzió

### <a name="net-sdk"></a>.NET SDK

A .net SDK-t a következő funkciók találhatók:

* **Átalakítások** és **feladatok** kódolásához, vagy a médiatartalmak elemzése. Példák: [fájlok Stream](stream-files-tutorial-with-api.md) és [elemzés](analyze-videos-tutorial-with-api.md).
* **StreamingLocators** közzétételéhez és a végfelhasználói eszközökön tartalmak online lejátszásához
* **StreamingPolicies** és **ContentKeyPolicies** konfigurálása kulcskézbesítési és a tartalomvédelmi (DRM), amikor tartalmat továbbít.
* **LiveEvents** és **LiveOutputs** betöltésre és élő adatfolyamok tartalmát archiválása konfigurálása.
* **Eszközök** tárolására és médiatartalmak közzététele az Azure Storage-ban. 
* **Streamvégpontok** konfigurálásához és a dinamikus csomagolás, titkosítási és folyamatos élő és igény szerinti médiatartalom méretezéséhez.

### <a name="known-issues"></a>Ismert problémák

* Küldjön el egy feladatot, megadhatja, hogy a forrásvideókat, HTTPS URL-címek, SAS URL-címek vagy elérési utak használata az Azure Blob storage-ban található fájlokat. Az AMS v3 jelenleg nem támogatja a HTTPS URL-címekkel történő darabolásos átviteli kódolást.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Áttekintés](media-services-overview.md)
