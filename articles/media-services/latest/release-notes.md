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
ms.date: 11/09/2018
ms.author: juliako
ms.openlocfilehash: 70a3de35f6fd942bca5355db3a7c6b57aec6adbc
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51613936"
---
# <a name="azure-media-services-v3-release-notes"></a>Az Azure Media Services v3 kibocsátási megjegyzései 

Naprakész a legújabb fejlemények, ez a cikk azt ismerteti kapcsolatban:

* A legújabb kiadásaihoz.
* Ismert problémák
* Hibajavítások
* Elavult funkciók
* Módosítások tervek

## <a name="november-2018"></a>A 2018. november

A CLI 2.0 modul már elérhető a [Azure Media Services v3 GA](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50.

### <a name="new-commands"></a>Új parancsok

- [az ams-fiók](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az ams-fiók-szűrő](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams-adategység](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams az eszközintelligencia-szűrő](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams tartalom-key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams-feladat](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [élő az ams-esemény](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams élő kimenet](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams-streamvégpont](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [streamelési az ams-lokátor](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams-fiók legutóbbi](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest) – lehetővé teszi a Media szolgáltatás számára fenntartott egységek kezelése

### <a name="new-features-and-breaking-changes"></a>Új funkciókat és jelentős módosításai

#### <a name="asset-commands"></a>Az eszközintelligencia-parancsok

- ```--storage-account``` és ```--container``` argumentumok hozzáadva.
- A lejárati idő (Now + 23 óra) és az engedélyek (olvasás), az alapértelmezett értékeket ```az ams asset get-sas-url``` parancs hozzáadva.

#### <a name="job-commands"></a>Feladat-parancsok

- ```--correlation-data``` és ```--label``` argumentumok hozzáadva
- ```--output-asset-names``` átnevezett ```--output-assets```. Most fogad el egy szóközzel elválasztott lista lévő eszközök "assetName = label" formátumban. Egy címke nélküli eszköz elküldött ehhez hasonló: "assetName ='.

#### <a name="streaming-locator-commands"></a>Streamelési lokátor parancsok

- ```az ams streaming locator``` Alap parancs helyére ```az ams streaming-locator```.
- ```--streaming-locator-id``` és ```--alternative-media-id support``` argumentumok hozzáadva.
- ```--content-keys argument``` az argumentum frissítve.
- ```--content-policy-name``` átnevezett ```--content-key-policy-name```.

#### <a name="streaming-policy-commands"></a>Adatfolyam-továbbítási szabályzat parancsok

- ```az ams streaming policy``` Alap parancs helyére ```az ams streaming-policy```.
- Támogatja a titkosítási paraméterek ```az ams streaming-policy create``` hozzáadva.

#### <a name="transform-commands"></a>Parancsok átalakítása

- ```--preset-names``` Argument helyére ```--preset```. Most csak akkor állíthat 1 kimeneti/készlet egyszerre (Továbbiak futtatásával hozzáadásához ```az ams transform output add```). Beállíthatja a egyéni StandardEncoderPreset is, az egyéni JSON elérési átadásával.
- ```az ams transform output remove``` a kimeneti index eltávolítása átadásával hajtható végre.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` a hozzáadott argumentumok ```az ams transform create``` és ```az ams transform output add``` parancsokat.

## <a name="october-2018---ga"></a>Október 2018 – GA

Ez a szakasz ismerteti az Azure Media Services (AMS). októberi frissítések.

### <a name="rest-v3-ga-release"></a>REST v3 GA kiadás

A [REST v3 GA kiadás](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) további API-kat tartalmaz Live, a fiók vagy eszköz szintű dinamikusjegyzék-szűrők és a digitális Jogkezelés támogatása.

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

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Az Azure CLI 2.0 modul, amely tartalmazza az összes funkciót (beleértve az élő, Tartalomszabályzat kulcs, fiók vagy eszköz szűrők, szabályzatokat. adatfolyam-) műveleteket hamarosan elérhető lesz. 

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

[Áttekintés](media-services-overview.md)
