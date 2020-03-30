---
title: Statikus webhely üzemeltetése az Azure Storage-ban
description: Az Azure Storage statikus webhelyüzemeltetése költséghatékony, méretezhető megoldást kínál a modern webalkalmazások üzemeltetéséhez.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 848fd89444281d82d6d0d1bfc4df15d499c09ee0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370491"
---
# <a name="static-website-hosting-in-azure-storage"></a>Statikus webhely üzemeltetése az Azure Storage-ban

Statikus tartalmakat (HTML, CSS, JavaScript és képfájlokat) közvetlenül a *$web*nevű tárolótárolóból szolgálhat ki. A tartalom Azure Storage-ban való tárolása lehetővé teszi, hogy kiszolgáló nélküli architektúrákat használjon, amelyek magukban foglalják [az Azure Functions-t](/azure/azure-functions/functions-overview) és más Platform szolgáltatásként (PaaS) szolgáltatásokat.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE]
> Ha a webhely a kiszolgálóoldali kódtól függ, használja inkább az [Azure App Service-t.](/azure/app-service/overview)

## <a name="setting-up-a-static-website"></a>Statikus webhely beállítása

Statikus webhely üzemeltetése egy olyan funkció, amelyet engedélyeznie kell a tárfiókban.

A statikus webhely-üzemeltetés engedélyezéséhez jelölje ki az alapértelmezett fájl nevét, majd adja meg az egyéni 404-es lap elérési útját. Ha egy blob storage tároló **nevű $web** még nem létezik a fiókban, egy jön létre az Ön számára. Adja hozzá a webhely fájljait a tárolóhoz.

Részletes útmutatásért tekintse meg [egy statikus webhely üzemeltetése az Azure Storage-ban című témakört.](storage-blob-static-website-how-to.md)

![Az Azure Storage statikus webhelyeinek metrikája](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

A **$web** tárolóban lévő fájlok a kis- és nagybetűket megkülönböztetők, névtelen hozzáférési kérelmeken keresztül szolgálják ki őket, és csak olvasási műveleteken keresztül érhetők el.

## <a name="uploading-content"></a>Tartalom feltöltése

Ezen eszközök bármelyikével tartalmakat tölthet fel a **$web** tárolóba:

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Azure PowerShell-modul](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure-folyamatok](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code-bővítmény](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Tartalom megtekintése

A felhasználók a webhely tartalmát a webhely nyilvános URL-címének használatával tekinthetik meg a böngészőből. Az URL-cím az Azure Portalon, az Azure CLI-n vagy a PowerShellen keresztül található. Használja ezt a táblázatot útmutatóként.

|Eszköz| Útmutatás |
|----|----|
|**Azure-portál** | [A webhely URL-címének megkeresése az Azure Portal használatával](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [A webhely URL-címének megkeresése az Azure CLI használatával](storage-blob-static-website-how-to.md#cli-find-url) |
|**Azure PowerShell-modul** | [A webhely URL-címének megkeresése a PowerShell használatával](storage-blob-static-website-how-to.md#powershell-find-url) |

A webhely URL-címe regionális kódot tartalmaz. Az URL-cím `https://contosoblobaccount.z22.web.core.windows.net/` például regionális kódot `z22`tartalmaz.

Bár ennek a kódnak az URL-ben kell maradnia, csak belső használatra van, és nem kell használnia ezt a kódot semmilyen más módon.

A statikus webhely-üzemeltetés engedélyezésekor megadott indexdokumentum akkor jelenik meg, amikor a felhasználók `https://contosoblobaccount.z22.web.core.windows.net`megnyitják a webhelyet, és nem ad meg konkrét fájlt (például: ).  

Ha a kiszolgáló 404-es hibát ad vissza, és a webhely engedélyezésekor nem adott meg hibaüzenetet, akkor a rendszer egy alapértelmezett 404-es lapot ad vissza a felhasználónak.

> [!NOTE]
> [Cors](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) nem támogatott statikus honlapján.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>A webes tároló nyilvános hozzáférési szintjének beállításának hatása

Módosíthatja a **$web** tároló nyilvános hozzáférési szintjét, de ez nincs hatással az elsődleges statikus webhely végpontjára, mivel ezek a fájlok névtelen hozzáférési kérelmeken keresztül kerülnek kiszolgálásra. Ez azt jelenti, nyilvános (csak olvasható) hozzáférést az összes fájlt.

Az alábbi képernyőképen látható a nyilvános hozzáférési szint beállítása az Azure Portalon:

![Képernyőkép a nyilvános hozzáférési szint beállításáról a portálon](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Bár az elsődleges statikus webhely végpontját nem érinti, a nyilvános hozzáférési szint módosítása hatással van az elsődleges blobszolgáltatás végpontjára.

Ha például a **$web** tároló nyilvános hozzáférési szintjét **privát (névtelen hozzáférés nélkül)** **blobra módosítja (csak a blobok névtelen olvasási hozzáférése),** akkor az elsődleges statikus webhely végpontjához való nyilvános hozzáférés szintje `https://contosoblobaccount.z22.web.core.windows.net/index.html` nem változik.

Azonban az elsődleges blob szolgáltatás végpontnyilvános `https://contosoblobaccount.blob.core.windows.net/$web/index.html` hozzáférése privátról nyilvánosra változik. Mostantól a felhasználók megnyithatják a fájlt a két végpont egyikének használatával.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Egyéni tartomány hozzárendelése statikus webhely URL-címéhez

Statikus webhelyét egyéni tartományon keresztül is elérhetővé teheti. 

Egyszerűbb en engedélyezheti a HTTP-hozzáférést az egyéni tartományhoz, mivel az Azure Storage natív módon támogatja azt. A HTTPS engedélyezéséhez az Azure CDN-t kell használnia, mert az Azure Storage még nem támogatja natív módon a HTTPS-t az egyéni tartományokhoz. Lásd: [Egyéni tartomány leképezése egy Azure Blob Storage-végponthoz](storage-custom-domain-name.md) részletes útmutatásért.

Ha a tárfiók úgy van beállítva, hogy [biztonságos átvitelt igényeljen](../common/storage-require-secure-transfer.md) HTTPS-kapcsolaton keresztül, akkor a felhasználóknak a HTTPS-végpontot kell használniuk. 

> [!TIP]
> Fontolja meg a tartomány üzemeltetését az Azure-ban. További információt a [Tartomány üzemeltetése az Azure DNS-ben](../../dns/dns-delegate-domain-azure-dns.md)című témakörben talál.

## <a name="adding-http-headers"></a>HTTP-fejlécek hozzáadása

A fejlécek konfigurálása nem lehet a statikus webhely szolgáltatás részeként. Az Azure CDN használatával azonban fejléceket adhat hozzá, és hozzáfűzheti (vagy felülírhatja) a fejlécértékeket. Lásd: [Az Azure CDN általános szabályokkal való hivatkozása.](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference)

Ha fejléceket szeretne használni a gyorsítótárazás szabályozásához, olvassa el [az Azure CDN-gyorsítótárazási viselkedésének vezérlése gyorsítótárazási szabályokkal című témakört.](https://docs.microsoft.com/azure/cdn/cdn-caching-rules)

## <a name="pricing"></a>Díjszabás

Engedélyezheti statikus website hosting ingyenes. Csak a blob storage, hogy a webhely által használt és a működési költségeket csak a blob storage. Az Azure Blob Storage árairól az [Azure Blob Storage díjszabási lapján](https://azure.microsoft.com/pricing/details/storage/blobs/)talál további információt.

## <a name="metrics"></a>Mérőszámok

A metrikákat statikus webhelyoldalakon engedélyezheti. Miután engedélyezte a metrikákat, a **$web** tárolóban lévő fájlok forgalmi statisztikái a metrikák irányítópultján kerülnek jelentésre.

Ha engedélyezni szeretné a mérőszámokat a statikus webhelyoldalakon, olvassa el [a Metrikák engedélyezése statikus webhelyoldalakon című témakört.](storage-blob-static-website-how-to.md#metrics)

## <a name="next-steps"></a>További lépések

* [Statikus webhely üzemeltetése az Azure Storage-ban](storage-blob-static-website-how-to.md)
* [Egyéni tartomány hozzárendelése Az Azure Blob Storage-végponthoz](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Az első kiszolgáló nélküli webalkalmazás létrehozása](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Oktatóanyag: Saját tartomány üzemeltetése az Azure DNS-ben](../../dns/dns-delegate-domain-azure-dns.md)
