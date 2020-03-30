---
title: Átalakítások és feladatok a Médiaszolgáltatásokban
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan hozhat létre átalakításokat a videók Azure Media Servicesben történő feldolgozásának szabályainak leírására.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: ab99b974aed6f8cd5e1da2ee9b427f593b405889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73571233"
---
# <a name="transforms-and-jobs-in-media-services"></a>Átalakítások és feladatok a Médiaszolgáltatásokban

Ez a témakör az [átalakításokról](https://docs.microsoft.com/rest/api/media/transforms) és a feladatokról ad részletes [információt,](https://docs.microsoft.com/rest/api/media/jobs) és ismerteti az entitások közötti kapcsolatot.

## <a name="overview"></a>Áttekintés

### <a name="transformsjobs-workflow"></a>Átalakítások/feladatok munkafolyamata

Az alábbi ábra az átalakítások/feladatok munkafolyamatát mutatja be:

![Átalakítások és feladatok munkafolyamata az Azure Media Servicesben](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Jellemző munkafolyamat

1. Hozzon létre egy átalakítást.
2. Feladatok küldése az átalakítás alatt.
3. Lista átalakítások.
4. Törölje az átalakítást, ha nem tervezi a jövőben isztikálandó használatát.

#### <a name="example"></a>Példa

Tegyük fel, hogy az összes videó első képkockát miniatűr képként szeretné kibontani – a következő lépéseket tenné:

1. Határozza meg a receptet, vagy a videó feldolgozásának szabályát: "használja a videó első képkockát miniatűrként".
2. Minden egyes videóhoz a következőket kell közölnia a szolgáltatásnak:
    1. Hol található a videó.
    2. Hol kell írni a kimeneti miniatűr képet.

A **Transform** segít létrehozni a recept et egyszer (1. lépés), és küldje el jobs segítségével, hogy a recept (2. lépés).

> [!NOTE]
> Az **Átalakítás** és a Datetime típusú **feladat** tulajdonságai mindig UTC formátumúak.

## <a name="transforms"></a>Átalakítások

**Az Átalakítások** segítségével konfigurálhatja a videók kódolására vagy elemzésére vonatkozó gyakori feladatokat. Minden **átalakítás** a video- vagy hangfájlok feldolgozására szolgáló feladatok receptjét vagy munkafolyamatát írja le. Egyetlen átalakítás több szabályt is alkalmazhat. Az átalakítás megadhatja például, hogy minden videó egy mp4-fájlba legyen kódolva egy adott bitrátával, és hogy a videó első képkockából miniatűr kép jön létre. Minden olyan szabályhoz, amelyet be szeretne vonni az átalakításba, minden egyes szabályhoz hozzá kell adnia egy TransformOutput bejegyzést. A készletek segítségével megállapíthatja, hogy a bemeneti médiafájlok hogyan dolgozhatók fel.

### <a name="viewing-schema"></a>Séma megtekintése

A Media Services v3-ban a készletek erősen gépelt entitások az API-ban. Ezeknek az objektumoknak a "séma" definícióját az [Open API Specification (vagy Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)tartalmazza. Az előre beállított definíciók (például **a StandardEncoderPreset)** a [REST API,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)vagy más Media Services v3 SDK referenciadokumentációban is megtekinthetők.

### <a name="creating-transforms"></a>Átalakítások létrehozása

Átalakításokat hozhat létre rest, CLI vagy bármely közzétett SDK használatával. A Media Services v3 API-t az Azure Resource Manager vezérli, így az Erőforrás-kezelő-sablonok használatával is létrehozhat és telepíthet átalakításokat a Media Services-fiókban. A szerepköralapú hozzáférés-vezérlés sel zárolhatja az átalakításokhoz való hozzáférést.

### <a name="updating-transforms"></a>Átalakítások frissítése

Ha frissítenie kell az [átalakítást,](https://docs.microsoft.com/rest/api/media/transforms)használja a **Frissítés** műveletet. Az alapul szolgáló TransformOutputok leírásának vagy prioritásainak módosítására szolgál. Javasoljuk, hogy az ilyen frissítéseket akkor végezze el, amikor az összes folyamatban lévő feladat befejeződött. Ha át kívánja írni a receptet, új átalakítást kell létrehoznia.

### <a name="transform-object-diagram"></a>Objektumdiagram átalakítása

Az alábbi ábrán az **Átalakítás** objektum és az általa hivatkozott objektumok láthatók, beleértve a származtatási kapcsolatokat is. A szürke nyilak olyan típust mutatnak, amelyet a Feladat hivatkozások és a zöld nyilak az osztályszármazékos kapcsolatokat mutatnak.

Válassza ki a képet a teljes méret megtekintéséhez.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a>

## <a name="jobs"></a>Feladatok

A **feladat** az a tényleges kérés a Media Services számára, hogy az **Átalakítás** t egy adott bemeneti videóra vagy hangtartalomra alkalmazza. Az átalakítás létrehozása után a feladatokat a Media Services API-k vagy a közzétett SDK-k használatával küldheti el. A **feladat** olyan információkat ad meg, mint például a bemeneti videó helye és a kimenet helye. A bemeneti videó helyét a következő használatával adhatja meg: HTTPS URL-címek, SAS URL-ek vagy [Eszközök](https://docs.microsoft.com/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Feladat bemenete https-ből

Használja [a HTTPS-ből származó feladatbevitelt,](job-input-from-http-how-to.md) ha a tartalom már elérhető egy URL-címen keresztül, és nem kell tárolnia a forrásfájlt az Azure-ban (például import az S3-ból). Ez a módszer akkor is alkalmas, ha rendelkezik a tartalom az Azure Blob storage, de nincs szükség a fájl egy eszköz. Ez a módszer jelenleg csak egyetlen fájlt támogat a bevitelhez.

### <a name="asset-as-job-input"></a>Eszköz feladatbemenetként

Használja [az eszközt feladatbemenetként,](job-input-from-local-file-how-to.md) ha a bemeneti tartalom már egy eszközben van, vagy ha a tartalom a helyi fájlban van tárolva. Ez is egy jó lehetőség, ha azt tervezi, hogy közzéteszi a bemeneti eszköz streaming vagy letöltés (mondjuk szeretné közzétenni az mp4 letölthető, hanem azt is szeretné, hogy beszéd a szöveg vagy arcfelismerés). Ez a módszer támogatja a többfájlos eszközöket (például a helyileg kódolt MBR streamelési készleteket).

### <a name="checking-job-progress"></a>A feladat előrehaladásának ellenőrzése

A feladatok állapota és állapota az Event Grid események figyelésével érhető el. További információ: [Események figyelése az EventGrid használatával.](job-state-events-cli-how-to.md)

### <a name="updating-jobs"></a>Feladatok frissítése

A [Projekt](https://docs.microsoft.com/rest/api/media/jobs) entitás frissítési művelete a feladat elküldése után módosíthatja a *leírást* és a *prioritási* tulajdonságokat. A *prioritási* tulajdonság módosítása csak akkor érvényes, ha a feladat még mindig várólistás állapotban van. Ha a feladat feldolgozása megkezdődött vagy befejeződött, a prioritás módosítása nincs hatással.

### <a name="job-object-diagram"></a>Feladatobjektum-diagram

Az alábbi ábrán a **Feladat** objektum és az általa hivatkozott objektumok láthatók, beleértve a származtatási kapcsolatokat is.

Kattintson a képre a teljes méretű megjelenítéshez.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a>

## <a name="configure-media-reserved-units"></a>Media számára fenntartott egységek konfigurálása

A Media Services v3 vagy Video Indexer által indított hangelemzési és videoelemzési feladatok esetében erősen ajánlott a fiók kiépítése 10 S3 Media szolgáltatásszámára fenntartott egységgel (MRUs). Ha 10-nél több S3 MRUs-ra van szüksége, nyisson meg egy támogatási jegyet az [Azure Portalon.](https://portal.azure.com/)

További információt az [Adathordozó-feldolgozás méretezése CLI-vel](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdéseket tehet fel, visszajelzést adhat, frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="see-also"></a>Lásd még

* [Hibakódok](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Media Services-entitások szűrése, rendezése, lapozása](entities-overview.md)

## <a name="next-steps"></a>További lépések

- A fejlesztés megkezdése előtt tekintse át [a Fejlesztés a Media Services v3 API-jával](media-services-apis-overview.md) című irányelveket (beleértve az API-k elérésére, elnevezési konvenciókra stb.)
- Nézze meg ezeket az útmutatókat:

    - [Oktatóanyag: Távoli fájl kódolása URL alapján, és streamelése](stream-files-tutorial-with-rest.md)
    - [Oktatóanyag: Videók feltöltése, kódolása és streamelése](stream-files-tutorial-with-api.md)
    - [Oktatóanyag: Videók elemzése a Media Services v3-as készülékkel](analyze-videos-tutorial-with-api.md)
