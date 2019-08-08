---
title: Statikus webhely üzemeltetése az Azure Storage-ban
description: Azure Storage – statikus webhely üzemeltetése, költséghatékony, méretezhető megoldás a modern webalkalmazások üzemeltetéséhez.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 9a751956f73ca4a88545e034a32d699c0766dd1d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855379"
---
# <a name="static-website-hosting-in-azure-storage"></a>Statikus webhely üzemeltetése az Azure Storage-ban

A statikus tartalom (HTML-, CSS-, JavaScript-és képfájlok) közvetlenül egy *$web*nevű tárolóból is kiszolgálható. A tartalom Azure Storage-ban való üzemeltetése lehetővé teszi, hogy olyan kiszolgáló nélküli architektúrákat használjon, amelyek a [Azure functions](/azure/azure-functions/functions-overview) és más platformként nyújtott szolgáltatásokat is tartalmazzák.

> [!NOTE]
> Ha a hely kiszolgálóoldali kódból függ, használja a [Azure app Service](/azure/app-service/overview) helyet.

## <a name="setting-up-a-static-website"></a>Statikus webhely beállítása

A statikus webhely üzemeltetése olyan szolgáltatás, amelyet engedélyeznie kell a Storage-fiókban.

A statikus webhely üzemeltetésének engedélyezéséhez válassza ki az alapértelmezett fájl nevét, majd opcionálisan adja meg az Egyéni 404-oldal elérési útját. Ha egy **$web** nevű blob Storage-tároló még nem létezik a fiókban, a rendszer létrehoz egyet. Adja hozzá a hely fájljait a tárolóhoz.

Részletes útmutatásért lásd: [statikus webhely üzemeltetése az Azure Storage-ban](storage-blob-static-website-how-to.md).

![Azure Storage – statikus webhelyek mérőszámai metrika](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

A **$web** tárolóban lévő fájlok megkülönböztetik a kis-és nagybetűket, és névtelen hozzáférési kérelmeken keresztül érhetők el, és csak olvasási műveleteken keresztül érhetőek el.

## <a name="uploading-content"></a>Tartalom feltöltése

A következő eszközök bármelyikével feltöltheti a tartalmakat a **$web** tárolóba:

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Azure PowerShell modul](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure-folyamatok](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code-bővítmény](https://code.visualstudio.com/tutorials/static-website/getting-started)

## <a name="viewing-content"></a>Tartalom megtekintése

A felhasználók a webhely nyilvános URL-címének használatával tekinthetik meg a tartalmak böngészőből való megtekintését. Az URL-címet a Azure Portal, az Azure CLI vagy a PowerShell használatával keresheti meg. Ezt a táblázatot útmutatóként használhatja.

|Eszköz| Útmutatás |
|----|----|
|**Azure Portal** | [A webhely URL-címének megkeresése a Azure Portal használatával](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [A webhely URL-címének megkeresése az Azure CLI használatával](storage-blob-static-website-how-to.md#cli-find-url) |
|**Azure PowerShell modul** | [A webhely URL-címének megkeresése a PowerShell használatával](storage-blob-static-website-how-to.md#powershell-find-url) |

A hely URL-címe tartalmaz egy regionális kódot. Például az URL- `https://contosoblobaccount.z22.web.core.windows.net/` cím regionális kódot `z22`tartalmaz.

Amíg a kódnak meg kell felelnie az URL-címnek, csak belső használatra van szüksége, és semmilyen más módon nem kell ezt a kódot használnia.

A statikus webhelyek üzemeltetésének engedélyezésekor megadott index-dokumentum akkor jelenik meg, amikor a felhasználók megnyitják a helyet, és nem határoznak meg `https://contosoblobaccount.z22.web.core.windows.net`konkrét fájlt (például:).  

Ha a kiszolgáló 404 hibát ad vissza, és nem adott meg hibaüzenetet a webhely engedélyezésekor, akkor a rendszer az alapértelmezett 404 lapot adja vissza a felhasználónak.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>A webes tároló nyilvános hozzáférési szintjének beállításának hatása

Módosíthatja a **$web** tároló nyilvános hozzáférési szintjét, de ez nem befolyásolja az elsődleges statikus webhely végpontját, mert ezek a fájlok névtelen hozzáférési kérelmeken keresztül vannak kézbesítve. Ez azt jelenti, hogy a nyilvános (csak olvasható) hozzáférés minden fájlhoz.

Az alábbi képernyőfelvételen a Azure Portal nyilvános hozzáférési szintjének beállítása látható:

![A nyilvános hozzáférési szint megadását bemutató képernyőkép a portálon](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Noha a rendszer nem érinti az elsődleges statikus webhely végpontját, a nyilvános hozzáférési szint módosítása hatással van az elsődleges blob Service-végpontra.

Ha például módosítja a **$web** tároló nyilvános hozzáférési szintjét **(nincs névtelen hozzáférés)** a **blobhoz (névtelen olvasási hozzáférés csak Blobok esetén)** , akkor a nyilvános hozzáférés szintje az elsődleges statikus webhely végpontjának. `https://contosoblobaccount.z22.web.core.windows.net/index.html`nem változik.

Az elsődleges blob Service-végponthoz `https://contosoblobaccount.blob.core.windows.net/$web/index.html` való nyilvános hozzáférés azonban magánjellegűről nyilvánosra változik. Most a felhasználók megnyithatja a fájlt a két végpont egyikével.

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>Content Delivery Network (CDN) és Secure Socket Layer (SSL) támogatása

Annak érdekében, hogy a statikus webhelyek fájljai elérhetők legyenek az egyéni tartományon és a HTTPS-en keresztül, tekintse meg a következőt: [a Azure CDN használata a Blobok egyéni tartományokkal](storage-https-custom-domain-cdn.md) Ennek a folyamatnak a részeként be kell mutatnia a CDN-t az elsődleges *statikus webhely* -végpontra, szemben az elsődleges *blob Service* -végponttal. Előfordulhat, hogy néhány percet várnia kell, amíg a tartalom látható lesz, mivel a CDN-konfiguráció nem kerül azonnal végrehajtásra.

A statikus webhely frissítésekor ügyeljen arra, hogy a CDN peremhálózati kiszolgálókon törölje a gyorsítótárazott tartalmat a CDN-végpont törlésével. További információkért lásd az [Azure CDN-végpontok végleges törléséről](../../cdn/cdn-purge-endpoint.md) szóló cikket.

> [!NOTE]
> A HTTPS a fiók webes végpontján keresztül natív módon támogatott, így a webes végpont a HTTP és a HTTPS protokollon keresztül érhető el. Ha azonban a Storage-fiók úgy van konfigurálva, hogy biztonságos átvitelt igényel a HTTPS protokollon keresztül, akkor a felhasználóknak a HTTPS-végpontot kell használniuk. További információ: [biztonságos átvitel megkövetelése az Azure Storage-ban](../common/storage-require-secure-transfer.md).
>
> Az egyéni tartományok HTTPS-kapcsolaton keresztüli használata a Azure CDN jelenleg való használatát igényli.

## <a name="custom-domain-names"></a>Egyéni tartománynevek

A statikus webhelyet egyéni tartományon keresztül is elérhetővé teheti. További információ: [Egyéni tartománynév beállítása az Azure Storage](storage-custom-domain-name.md)-fiókhoz.

A tartomány Azure-beli üzemeltetésének részletes ismertetését a [tartomány üzemeltetése Azure DNSban](../../dns/dns-delegate-domain-azure-dns.md)című témakörben tekintheti meg.

## <a name="pricing"></a>Díjszabás

A statikus webhelyek üzemeltetése díjmentesen engedélyezhető. Csak a hely által használt BLOB Storage-hoz és az üzemeltetési költségekhez kell fizetnie. Az Azure Blob Storage áraival kapcsolatos további információkért tekintse meg az [azure blob Storage díjszabási oldalát](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Mérőszámok

A metrikák a statikus webhelyek oldalain is engedélyezhetők. A metrikák engedélyezése után a **$web** tárolóban található fájlokra vonatkozó forgalmi statisztikát a metrikák irányítópultján kell jelenteni.

A mérőszámok statikus webhely oldalain való engedélyezéséhez tekintse [meg a metrikák engedélyezése statikus](storage-blob-static-website-how-to.md#metrics)webhelyeken című témakört.

## <a name="next-steps"></a>További lépések

* [Statikus webhely üzemeltetése az Azure Storage-ban](storage-blob-static-website-how-to.md)
* [A Blobok egyéni tartományokkal való elérésének Azure CDN használata HTTPS-kapcsolaton keresztül](storage-https-custom-domain-cdn.md)
* [Egyéni tartománynév beállítása a blob vagy a webes végpont számára](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Az első kiszolgáló nélküli Webalkalmazás létrehozása](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Oktatóanyag: A tartomány üzemeltetése Azure DNS](../../dns/dns-delegate-domain-azure-dns.md)
