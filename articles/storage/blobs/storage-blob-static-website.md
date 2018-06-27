---
title: Statikus webhely üzemeltetéséhez az Azure Storage (előzetes verzió) |} Microsoft Docs
description: Az Azure Storage most kínál statikus webhely üzemeltetéséhez (előzetes verzió), egy költséghatékony, méretezhető megoldást biztosít a modern webalkalmazások üzemeltetéséhez.
services: storage
author: MichaelHauss
manager: vamshik
ms.service: storage
ms.topic: article
ms.date: 06/26/18
ms.author: mihauss
ms.openlocfilehash: 7021a0499547818d702d14aecb9d8e451a820181
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37025808"
---
# <a name="static-website-hosting-in-azure-storage-preview"></a>Statikus webhely üzemeltetéséhez az Azure Storage (előzetes verzió)
Az Azure Storage most kínál statikus webhely (előzetes verzió) tároló, telepíthet költséghatékony, méretezhető modern webalkalmazások Azure engedélyezése. Statikus webhely, a weblap tartalmazza a statikus tartalom és a JavaScript vagy más ügyféloldali kódot. Ezzel szemben, dinamikus webhelyek függ a kiszolgálóoldali kódot, és segítségével működtethető [Azure Web Apps](/app-service/app-service-web-overview.md).

Központi telepítések költséghatékony, rugalmas modellek felé shift, mert a webes tartalom Kiszolgálókezelés szükségessége nélkül teszi fontos. Statikus webhely tároló Azure Storage bemutatása Ez lehetővé teszi, mivel lehetővé teszi gazdag háttér képességei kihasználva kiszolgáló nélküli architektúrák [Azure Functions](/azure-functions/functions-overview.md) és egyéb PaaS szolgáltatások.

## <a name="how-does-it-work"></a>Hogyan működik?
A tárfiók engedélyezésekor statikus webhelyek kiszolgálására egy új webszolgáltatási végpontot az űrlap létrehozása `<account-name>.<zone-name>.web.core.windows.net`.

A webszolgáltatás végpontja mindig lehetővé teszi a névtelen olvasási hozzáférés, formázott HTML-lapok hibákat válasz adja vissza, és lehetővé teszi, hogy a csak olvasási műveletek objektum. A webszolgáltatási végpontot az index dokumentumot ad vissza, a legfelső szintű és alkönyvtáraiban a kért címtárban. Ha a társzolgáltatás 404 hibaüzenetet ad vissza, a webes végpontjának dokumentumot ad vissza, egy egyéni hiba Ha konfigurált ilyet.

A speciális tároló "$web" nevű webhelyének statikus tartalmát tárolja. Az engedélyezési folyamat részeként "$web" jön létre, ha még nem létezik. "$Web" tartalma a webszolgáltatás végpont használatával fiók gyökérkönyvtárában érhető el. Például `https://contoso.z4.web.core.windows.net/` a webhely konfigurált index dokumentumát adja vissza, ha van ilyen nevű egy dokumentum $web gyökérkönyvtárában.

Amikor tartalmat tölt fel a webhelyéhez, használja a blob storage endpoint. Blobok feltöltése nevű "image.jpg" fiók gyökérkönyvtárában elérhető használja a következő URL-cím `https://contoso.blob.core.windows.net/$web/image.jpg`. A feltöltött lemezkép tekintheti meg a megfelelő webes végpont webböngésző `https://contoso.z4.web.core.windows.net/image.jpg`.


## <a name="custom-domain-names"></a>Egyéni tartománynevek
Az egyéni tartománynév használatával a webalkalmazás tartalmát. Ehhez kövesse az ismertetés [egy egyéni tartománynév beállítása az Azure Storage-fiók](storage-custom-domain-name.md). Tekintse meg a HTTPS-KAPCSOLATON keresztül egy egyéni tartománynevet, üzemeltetett webhely eléréséhez [blobokhoz való hozzáférést az egyéni tartomány HTTPS-KAPCSOLATON keresztül az Azure CDN használatával](storage-https-custom-domain-cdn.md).

## <a name="pricing-and-billing"></a>Árak és számlázás
Statikus webhely üzemeltetéséhez biztosított további költségek nélkül. Az Azure Blob-tároló árának további részletekért tekintse meg a [Azure Blob Storage árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Első lépések
### <a name="azure-portal"></a>Azure Portal
Indíthatja el a webalkalmazást az Azure Storage, konfigurálja a funkciót, az Azure portál használatával, és a "Beállítások" a bal oldali navigációs sávon kattintson a "Webhely statikus (előzetes verzió)". Kattintson az "Engedélyezve", és adja meg az index a dokumentum, és (opcionálisan) a dokumentum egyéni hiba elérési útjának nevét.

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

Töltse fel a webkiszolgáló eszközöket a "$web" tároló, amely a statikus webhely engedélyezése részeként jött létre. Ezt megteheti közvetlenül az Azure portálon is, vagy kihasználhatja az [Azure Tártallózó](https://azure.microsoft.com/features/storage-explorer/) teljes könyvtárstruktúrák feltöltéséhez. Ügyeljen arra, hogy egy konfigurált nevű index dokumentum tartalmazza. Ebben a példában a dokumentum neve: "index.html".

Végül nyissa meg a webes végponthoz tesztelje a webhelyet.

## <a name="faq"></a>GYIK
**Érhető el statikus webhelyek kiszolgálására összes tárfióktípusokat?**  
Nem, statikus webhely üzemeltetéséhez csak érhető el GPv2 szabványos tárfiókokban.

**Azok a tárolási virtuális hálózat és az új webes végpont támogatott tűzfalszabályok?**  
Igen, az új webes végpontjának obeys a tárfiók beállított virtuális hálózat és a tűzfalon szabályokat.

## <a name="next-steps"></a>További lépések
* [Az Azure CDN használatával blobokhoz való hozzáférést az egyéni tartomány HTTPS-KAPCSOLATON keresztül](storage-https-custom-domain-cdn.md)
* [A blob vagy webszolgáltatás-végpontot az egyéni tartománynév konfigurálása](storage-custom-domain-name.md)
* [Azure Functions](/azure-functions/functions-overview.md)
* [Azure Web Apps](/app-service/app-service-web-overview.md)
* [Az első kiszolgáló nélküli webalkalmazás létrehozása](https://aka.ms/static-serverless-webapp)
