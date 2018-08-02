---
title: Statikus webhely üzemeltetése az Azure Storage (előzetes verzió) |} A Microsoft Docs
description: Az Azure Storage most a statikus webhely üzemeltetése (előzetes verzió), a modern webalkalmazásokat szeretne üzemeltetni egy költséghatékony, méretezhető megoldást kínál.
services: storage
author: MichaelHauss
ms.service: storage
ms.topic: article
ms.date: 06/26/18
ms.author: mihauss
ms.component: blobs
ms.openlocfilehash: e53b573a27f0b1462ccf1170bbde2f8af01d0d3a
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397475"
---
# <a name="static-website-hosting-in-azure-storage-preview"></a>Statikus webhely üzemeltetése az Azure Storage (előzetes verzió)
Az Azure Storage most kínál a statikus webhely üzemeltetése (előzetes verzió), lehetővé téve az Azure-ban költséghatékony és méretezhető modern webes alkalmazások üzembe helyezése. A statikus webhely a weblapok statikus tartalmat és a JavaScript- vagy egyéb ügyféloldali kódot tartalmazzák. Ezzel szemben az dinamikus webhelyeket szerveroldali kódot, és függ használatával lehet üzemeltetni [Azure Web Apps](/app-service/app-service-web-overview.md).

Központi telepítések shift rugalmas, költséghatékony modellek felé, mert arra, hogy webes tartalmak felügyelet nélkül. Statikus webhely üzemeltetése az Azure Storage bevezetésével Ez lehetővé teszi, a kiszolgáló nélküli architektúrák kihasználva gazdag háttérfunkcióinak engedélyezése [Azure Functions](/azure-functions/functions-overview.md) és egyéb PaaS-szolgáltatások.

## <a name="how-does-it-work"></a>Hogyan működik?
Ha statikus webhelyek kiszolgálására engedélyezi a storage-fiókjában, egy új webszolgáltatási végpontot jön létre az űrlap `<account-name>.<zone-name>.web.core.windows.net`.

A webszolgáltatási végpontot mindig lehetővé teszi a névtelen olvasási hozzáférés, formázott HTML-lapok szolgáltatás hibáinak választ adja vissza, és lehetővé teszi, hogy a csak olvasási műveletek objektumot. A web service-végpont az index a dokumentum a legfelső szintű és az összes alkönyvtár kért könyvtárában adja vissza. A storage szolgáltatás 404-es hibát ad vissza, ha a webes végpont a ha konfigurált ilyet egy egyéni hiba dokumentumot ad vissza.

A statikus webhely tartalmának "$web" nevű speciális tárolóban üzemel. Az engedélyezés folyamat részeként "$web" jön létre, ha azt nem létezik. A webes végpont használatával fiók gyökerénél "$web" tartalom elérhető lesz. Például `https://contoso.z4.web.core.windows.net/` a webhely konfigurált index dokumentumot ad vissza, ha már létezik ilyen nevű dokumentum $web gyökérkönyvtárában.

Amikor tartalmat töltenek fel a webhelyet, használja a blob storage-végponthoz. Nevű blobba feltölteni "image.jpg", amely a fiók gyökérkönyvtárában érhető el a következő URL-cím használata `https://contoso.blob.core.windows.net/$web/image.jpg`. A feltöltött kép tekinthet meg a megfelelő végponton webes böngészőben `https://contoso.z4.web.core.windows.net/image.jpg`.


## <a name="custom-domain-names"></a>Egyéni tartománynevek
Egyéni tartomány használatával a webes tartalom üzemeltetéséhez. Ehhez kövesse az útmutató [az Azure Storage-fiókhoz tartozó egyéni tartománynév beállítása](storage-custom-domain-name.md). Tekintse meg a HTTPS-kapcsolaton keresztül, egy egyéni tartománynevet üzemeltetett webhely eléréséhez [az Azure CDN használatával HTTPS-kapcsolaton keresztül egyéni tartománnyal rendelkező blobok elérése](storage-https-custom-domain-cdn.md).

## <a name="pricing-and-billing"></a>Árak és számlázás
Statikus webhely üzemeltetése további költségek nélkül biztosított. Az Azure Blob Storage-díjak a további részletekért tekintse meg a [Azure Blob Storage díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Első lépések
### <a name="azure-portal"></a>Azure Portal
A webalkalmazást az Azure Storage üzemeltetésének megkezdéséhez, konfigurálja a szolgáltatást az Azure Portal használatával, és kattintson a "Statikus webhely (előzetes verzió)" a "Beállítások" a bal oldali navigációs sávon. Kattintson az "Engedélyezve", és adja meg az index dokumentum, és (opcionálisan) az egyéni hibadokumentum elérési útjának neve.

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

A webes adategységek feltöltése "$web" tároló, amely a statikus webhely lehetővé tétele részeként jött létre. Ezt megteheti közvetlenül az Azure Portalon is, és igénybe veheti a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) teljes könyvtárstruktúrák feltölteni. Ellenőrizze, hogy egy konfigurált nevű index dokumentum tartalmazza. Ebben a példában a dokumentum neve az "index.html".

> [!NOTE]
> A dokumentum neve megkülönbözteti a kis-és nagybetűket, és ezért a fájlt a tároló neve pontosan egyeznie kell.

Végül nyissa meg a webes végpontra teszteli a webhelyét.

## <a name="faq"></a>GYIK
**Statikus webhelyek kiszolgálására érhető el minden storage fióktípus esetében?**  
Nem, statikus webhelyüzemeltetésre érhető el csak a GPv2-tárfiókok standard.

**Tároló virtuális hálózat és az új webes végpont támogatott tűzfalszabályok vannak?**  
Igen, az új webes végpont obeys a virtuális hálózat és tűzfal szabályokat a tárfiók számára beállított.

## <a name="next-steps"></a>További lépések
* [Az Azure CDN használatával HTTPS-kapcsolaton keresztül egyéni tartománnyal rendelkező blobok elérése](storage-https-custom-domain-cdn.md)
* [A blob vagy a webszolgáltatás-végpont egyéni tartománynév konfigurálása](storage-custom-domain-name.md)
* [Azure Functions](/azure-functions/functions-overview.md)
* [Azure Web Apps](/app-service/app-service-web-overview.md)
* [Az első kiszolgáló nélküli webalkalmazás létrehozása](https://aka.ms/static-serverless-webapp)
