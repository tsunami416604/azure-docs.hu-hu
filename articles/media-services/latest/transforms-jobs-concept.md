---
title: Átalakítások és feladatok a Azure Media Servicesban | Microsoft Docs
description: Media Services használatakor létre kell hoznia egy átalakítót, amely leírja a videók feldolgozásához szükséges szabályokat vagy specifikációkat. Ez a cikk áttekintést nyújt a transzformációról és használatáról.
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
ms.openlocfilehash: 466ab0737aa5af40bd1bc137b98ab57a48feafde
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637357"
---
# <a name="transforms-and-jobs"></a>Átalakítások és feladatok

Ez a témakör az [átalakításokkal](https://docs.microsoft.com/rest/api/media/transforms) és a feladatokkal kapcsolatos adatokat ismerteti, és bemutatja az entitások közötti kapcsolatot. [](https://docs.microsoft.com/rest/api/media/jobs) 

## <a name="overview"></a>Áttekintés 

### <a name="transformsjobs-workflow"></a>Átalakítások/feladatok munkafolyamat

A következő ábra a transzformációk/feladatok munkafolyamatot mutatja be.

![Átalakítások](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Jellemző munkafolyamat

1. Átalakítás létrehozása 
2. Feladatok elküldése az átalakítás alatt 
3. Átalakítások listázása 
4. Ha a későbbiekben nem kívánja használni, törölje az átalakítást. 

#### <a name="example"></a>Példa

Tegyük fel, hogy az összes videó miniatűr képként való kinyerését kívánja kinyerni – a lépéseket a következőképpen teheti meg: 

1. Adja meg a receptet vagy a videók feldolgozására szolgáló szabályt – "a videó első képkockáját használja miniatűrként". 
2. A szolgáltatással kapcsolatos minden videóhoz: 
    1. Hol található a videó,  
    2. Hová kell írni a kimenet miniatűr képét. 

Az **átalakítással** egyszerre hozhatja létre a receptet (1. lépés), és elküldheti az adott receptet használó feladatokat (2. lépés).

> [!NOTE]
> Az **átalakítási** és a feladatokhoz tartozó tulajdonságok mindig UTC formátumban jelennek meg.

## <a name="transforms"></a>Átalakítások

Az **átalakítások** használatával konfigurálhatja a videók kódolására vagy elemzésére szolgáló gyakori feladatokat. Mindegyik **transzformáció** egy receptet vagy egy munkafolyamatot ír le, amely a videó-vagy hangfájlok feldolgozására használható. Egyetlen átalakító több szabályt is alkalmazhat. Egy átalakító például megadhatja, hogy minden videó egy adott bitrátán lévő MP4-fájlba legyen kódolva, és hogy a videó első képkockából jöjjön létre egy miniatűr kép. Minden olyan szabályhoz hozzá kell adnia egy TransformOutput-bejegyzést, amelyet fel szeretne venni az átalakításba. Az előállítók használatával adhatja meg, hogyan történjen a bemeneti médiafájlok feldolgozásának átalakítása.

### <a name="viewing-schema"></a>Séma megtekintése

A Media Services v3-as verzióban az előzetes beállítások az API-ban erősen beírt entitások. Ezekhez az objektumokhoz a "séma" definíciója [nyílt API-specifikációban (vagy hencegés)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)található. A [REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), a [.net SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) -ban (vagy más Media Services v3 SDK-dokumentációban) is megtekintheti az előre definiált definíciókat (például **StandardEncoderPreset**).

### <a name="creating-transforms"></a>Átalakítások létrehozása

A REST, a CLI használatával vagy a közzétett SDK-k bármelyikének használatával hozhat létre átalakításokat. Az Media Services V3 API-t a Azure Resource Manager vezérli, így a Resource Manager-sablonokkal is létrehozhatja és telepítheti az átalakításokat a Media Services-fiókban. A szerepköralapú hozzáférés-vezérlés használatával zárolhatja az átalakításokhoz való hozzáférést.

### <a name="updating-transforms"></a>Átalakítások frissítése

Ha frissítenie kell az [átalakítót](https://docs.microsoft.com/rest/api/media/transforms), használja a **frissítési** műveletet. Célja a Leírás módosítása vagy a mögöttes TransformOutputs prioritásainak meghatározása. Javasoljuk, hogy ezeket a frissítéseket akkor hajtsa végre, ha az összes folyamatban lévő feladat befejeződött. Ha újra szeretné írni a receptet, létre kell hoznia egy új átalakítót.

### <a name="transform-object-diagram"></a>Objektum diagramjának átalakítása

A következő ábrán az **átalakító** objektum és az általa hivatkozott objektumok láthatók, beleértve a származtató kapcsolatokat is. A szürke nyíl egy olyan típust mutat be, amelyet a feladatok és a zöld nyilak mutatnak az osztály származtató kapcsolatainak megjelenítéséhez.<br/>Kattintson a képre a teljes méretű megjelenítéshez.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a> 

## <a name="jobs"></a>Feladatok

A feladatnak az a tényleges kérése, hogy Azure Media Services az **átalakítást** egy adott bemeneti videóra vagy hangtartalomra alkalmazza. Az átalakítás létrehozása után Media Services API-kkal vagy a közzétett SDK-kkal is elküldheti a feladatokat. A **feladatok** olyan információkat határoznak meg, mint például a bemeneti videó helye, valamint a kimenet helye. A bemeneti videó helyét a alábbiak alapján adhatja meg: HTTPS URL-címek, SAS URL-címek vagy [eszközök](https://docs.microsoft.com/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Feladathoz tartozó bemenet HTTPS-ről

Ha a tartalom már elérhető egy URL-címen keresztül, és nem szükséges a forrásfájl tárolása az Azure-ban (például az S3-ból való importálás), használja a HTTPS-alapú [feladatokat](job-input-from-http-how-to.md) . Ez a módszer akkor is megfelelő, ha rendelkezik a tartalommal az Azure Blob Storage-ban, de nem szükséges, hogy a fájl egy eszközön legyen. Ez a módszer jelenleg csak egyetlen fájlt támogat a bevitelhez.

### <a name="asset-as-job-input"></a>Az eszköz feladatként való bevitele

Ha a bemeneti tartalom már egy eszközön van, vagy a tartalom helyi fájlban van tárolva, használja az [eszköz](job-input-from-local-file-how-to.md) feladatként való bevitelét. Ez akkor is jó megoldás, ha a bemeneti adategységet szeretné közzétenni a streaminghez vagy a letöltéshez (tegyük fel, hogy közzé kívánja tenni az MP4 letöltését, hanem szöveg-vagy Arcfelismerés-felismerést is szeretne készíteni). Ez a módszer támogatja a többfájlos eszközöket (például a helyileg kódolt MBR-adatfolyam-készleteket).

### <a name="checking-job-progress"></a>A feladatok állapotának ellenőrzése

A feladatok előrehaladását és állapotát a Event Gridával folytatott figyelési események is beszerezhetők. További információ: [események figyelése a EventGrid használatával](job-state-events-cli-how-to.md).

### <a name="updating-jobs"></a>Feladatok frissítése

A [feladat](https://docs.microsoft.com/rest/api/media/jobs) entitás frissítési művelete a *Leírás*és a feladat elküldését követő prioritási tulajdonságok módosítására használható. A *priority* tulajdonság módosítása csak akkor lép érvénybe, ha a feladat továbbra is várólistán lévő állapotban van. Ha a feladat megkezdte a feldolgozást, vagy befejeződött, a módosítás prioritása nincs hatással.

### <a name="job-object-diagram"></a>Feladatütemezés diagramja

A következő ábrán a **feladatütemezés** és az általa hivatkozott objektumok láthatók, beleértve a származtató kapcsolatokat is.<br/>Kattintson a képre a teljes méretű megjelenítéshez.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a> 

## <a name="configure-media-reserved-units"></a>Media szolgáltatás számára fenntartott egységek konfigurálása

A Media Services v3 vagy Video Indexer által aktivált hangelemzési és videó-elemzési feladatokhoz erősen ajánlott, hogy a fiókja 10 S3-as Media szolgáltatás számára fenntartott egységgel (MRUs) legyen kiépítve. Ha több mint 10 S3-helyet, nyisson meg egy támogatási jegyet a a [az Azure portal](https://portal.azure.com/).

Részletekért lásd: [a médiafájlok feldolgozásának skálázása a CLI-vel](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="see-also"></a>Lásd még

* [Hibakódok](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Media Services entitások szűrése, rendezése és lapozása](entities-overview.md)

## <a name="next-steps"></a>További lépések

- A fejlesztés megkezdése előtt tekintse át [az Media Services V3 API](media-services-apis-overview.md) -kkal való fejlesztést (az API-k elérésére vonatkozó információkat, elnevezési konvenciókat stb.).
- Tekintse meg a következő oktatóanyagokat:

    - [Oktatóanyag: Távoli fájl kódolása URL-cím alapján és a videó továbbítása](stream-files-tutorial-with-rest.md)
    - [Oktatóanyag: Videók feltöltése, kódolása és továbbítása](stream-files-tutorial-with-api.md)
    - [Oktatóanyag: Videók elemzése a Media Services v3](analyze-videos-tutorial-with-api.md)
