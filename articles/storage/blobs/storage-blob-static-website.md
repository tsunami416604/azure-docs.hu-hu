---
title: Statikus webhely üzemeltetése az Azure Storage (előzetes verzió) |} A Microsoft Docs
description: Az Azure Storage most a statikus webhely üzemeltetése (előzetes verzió), a modern webalkalmazásokat szeretne üzemeltetni egy költséghatékony, méretezhető megoldást kínál.
services: storage
author: MichaelHauss
ms.service: storage
ms.topic: article
ms.date: 08/17/18
ms.author: mihauss
ms.component: blobs
ms.openlocfilehash: 65a1cd85baf18ac1f0d193e7e6d6c3139919fb59
ms.sourcegitcommit: a62cbb539c056fe9fcd5108d0b63487bd149d5c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2018
ms.locfileid: "42617397"
---
# <a name="static-website-hosting-in-azure-storage-preview"></a>Statikus webhely üzemeltetése az Azure Storage (előzetes verzió)
Az Azure Storage most kínál a statikus webhely üzemeltetése (előzetes verzió), lehetővé téve az Azure-ban költséghatékony és méretezhető modern webes alkalmazások üzembe helyezése. A statikus webhely a weblapok statikus tartalmat és a JavaScript- vagy egyéb ügyféloldali kódot tartalmazzák. Ezzel szemben az dinamikus webhelyeket szerveroldali kódot, és függ használatával lehet üzemeltetni [Azure Web Apps](/azure/app-service/app-service-web-overview).

Központi telepítések shift rugalmas, költséghatékony modellek felé, mert arra, hogy webes tartalmak felügyelet nélkül. Statikus webhely üzemeltetése az Azure Storage bevezetésével Ez lehetővé teszi, a kiszolgáló nélküli architektúrák kihasználva gazdag háttérfunkcióinak engedélyezése [Azure Functions](/azure/azure-functions/functions-overview) és egyéb PaaS-szolgáltatások.

## <a name="how-does-it-work"></a>Hogyan működik?
Ha statikus webhelyek kiszolgálására engedélyezi a storage-fiókjában, egy új webszolgáltatási végpontot jön létre az űrlap `<account-name>.<zone-name>.web.core.windows.net`.

A webszolgáltatási végpontot mindig lehetővé teszi a névtelen olvasási hozzáférés, formázott HTML-lapok szolgáltatás hibáinak választ adja vissza, és lehetővé teszi, hogy a csak olvasási műveletek objektumot. A web service-végpont az index a dokumentum a legfelső szintű és az összes alkönyvtár kért könyvtárában adja vissza. A storage szolgáltatás 404-es hibát ad vissza, ha a webes végpont a ha konfigurált ilyet egy egyéni hiba dokumentumot ad vissza.

A statikus webhely tartalmának "$web" nevű speciális tárolóban üzemel. Az engedélyezés folyamat részeként "$web" jön létre, ha azt nem létezik. A webes végpont használatával fiók gyökerénél "$web" tartalom elérhető lesz. Például `https://contoso.z4.web.core.windows.net/` a webhely konfigurált index dokumentumot ad vissza, ha már létezik ilyen nevű dokumentum $web gyökérkönyvtárában.

Amikor tartalmat töltenek fel a webhelyet, használja a blob storage-végponthoz. Nevű blobba feltölteni "image.jpg", amely a fiók gyökérkönyvtárában érhető el a következő URL-cím használata `https://contoso.blob.core.windows.net/$web/image.jpg`. A feltöltött kép tekinthet meg a megfelelő végponton webes böngészőben `https://contoso.z4.web.core.windows.net/image.jpg`.


## <a name="custom-domain-names"></a>Egyéni tartománynevek
Egyéni tartomány használatával a webes tartalom üzemeltetéséhez. Ehhez kövesse az útmutató [az Azure Storage-fiókhoz tartozó egyéni tartománynév beállítása](storage-custom-domain-name.md). Tekintse meg a HTTPS-kapcsolaton keresztül, egy egyéni tartománynevet üzemeltetett webhely eléréséhez [az Azure CDN használatával HTTPS-kapcsolaton keresztül egyéni tartománnyal rendelkező blobok elérése](storage-https-custom-domain-cdn.md). A CDN a webes végpontra ellentétben a blob végpontja és a korábbi, hogy a CDN-konfiguráció nem fordulhat elő, azonnal, várjon néhány percet, mielőtt a tartalmak látható-e szükség lehet.

## <a name="pricing-and-billing"></a>Árak és számlázás
Statikus webhely üzemeltetése további költségek nélkül biztosított. Az Azure Blob Storage-díjak a további részletekért tekintse meg a [Azure Blob Storage díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Első lépések
### <a name="azure-portal"></a>Azure Portal
Ha még nem tette, [GPv2-tárfiók létrehozása](../common/storage-quickstart-create-account.md) indítása a webalkalmazás üzemeltetéséhez, konfigurálhatja a funkció az Azure Portal használatával, és a "Beállítások" a bal oldali navigációs sávon kattintson a "Statikus webhely (előzetes verzió)". Kattintson az "Engedélyezve", és adja meg az index dokumentum, és (opcionálisan) az egyéni hibadokumentum elérési útjának neve.

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

A webes adategységek feltöltése "$web" tároló, amely a statikus webhely lehetővé tétele részeként jött létre. Ezt megteheti közvetlenül az Azure Portalon is, és igénybe veheti a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) teljes könyvtárstruktúrák feltölteni. Ellenőrizze, hogy egy konfigurált nevű index dokumentum tartalmazza. Ebben a példában a dokumentum neve az "index.html".

> [!NOTE]
> A dokumentum neve megkülönbözteti a kis-és nagybetűket, és ezért a fájlt a tároló neve pontosan egyeznie kell.

Végül nyissa meg a webes végpontra teszteli a webhelyét.

### <a name="azure-cli"></a>Azure CLI
A storage előzetes bővítmény telepítéséhez:

```azurecli-interactive
az extension add --name storage-preview
```
A funkció engedélyezéséhez:

```azurecli-interactive
az storage blob service-properties update --account-name <account-name> --static-website --404-document <error-doc-name> --index-document <index-doc-name>
```
A webes végpont URL-lekérdezés:

```azurecli-interactive
az storage account show -n <account-name> -g <resource-group> --query "primaryEndpoints.web" --output tsv
```

Feltöltés a $web tároló objektumok:

```azurecli-interactive
az storage blob upload-batch -s deploy -d $web --account-name <account-name>
```

## <a name="faq"></a>GYIK
**Statikus webhelyek kiszolgálására érhető el minden storage fióktípus esetében?**  
Nem, statikus webhelyüzemeltetésre érhető el csak a GPv2-tárfiókok standard.

**Tároló virtuális hálózat és az új webes végpont támogatott tűzfalszabályok vannak?**  
Igen, az új webes végpont obeys a virtuális hálózat és tűzfal szabályokat a tárfiók számára beállított.

**Az a webes végpont kis-és nagybetűket?**  
Igen, a webes végpont kis-és nagybetűket csakúgy, mint a blob végpontja. 

## <a name="next-steps"></a>További lépések
* [Az Azure CDN használatával HTTPS-kapcsolaton keresztül egyéni tartománnyal rendelkező blobok elérése](storage-https-custom-domain-cdn.md)
* [A blob vagy a webszolgáltatás-végpont egyéni tartománynév konfigurálása](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure Web Apps](/azure/app-service/app-service-web-overview)
* [Az első kiszolgáló nélküli webalkalmazás létrehozása](https://aka.ms/static-serverless-webapp)
