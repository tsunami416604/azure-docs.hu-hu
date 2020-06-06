---
title: Statikus webhely üzemeltetése az Azure Storage-ban
description: Azure Storage – statikus webhely üzemeltetése, költséghatékony, méretezhető megoldás a modern webalkalmazások üzemeltetéséhez.
author: normesta
ms.service: storage
ms.topic: how-to
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/14/2020
ms.subservice: blobs
ms.openlocfilehash: f3dc7a051021c75c7e1ed6904096c43a27c3e05e
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465898"
---
# <a name="static-website-hosting-in-azure-storage"></a>Statikus webhely üzemeltetése az Azure Storage-ban

A statikus tartalom (HTML-, CSS-, JavaScript-és képfájlok) közvetlenül egy *$web*nevű tárolóból is kiszolgálható. A tartalom Azure Storage-ban való üzemeltetése lehetővé teszi, hogy olyan kiszolgáló nélküli architektúrákat használjon, amelyek a [Azure functions](/azure/azure-functions/functions-overview) és más platformként nyújtott szolgáltatásokat is tartalmazzák.

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

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
> * [Azure CLI](storage-blob-static-website-how-to.md?tabs=azure-cli)
> * [Azure PowerShell-modul](storage-blob-static-website-how-to.md?tabs=azure-powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code-bővítmény](/azure/javascript/tutorial-vscode-static-website-node-01)

## <a name="viewing-content"></a>Tartalom megtekintése

A felhasználók a webhely nyilvános URL-címének használatával tekinthetik meg a tartalmak böngészőből való megtekintését. Az URL-címet a Azure Portal, az Azure CLI vagy a PowerShell használatával keresheti meg. Tekintse [meg a webhely URL-címét](storage-blob-static-website-how-to.md#portal-find-url).

Ha a kiszolgáló 404 hibát ad vissza, és nem adott meg hibaüzenetet a webhely engedélyezésekor, akkor a rendszer az alapértelmezett 404 lapot adja vissza a felhasználónak.

> [!NOTE]
> A statikus webhely nem támogatja a [CORS](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) .

### <a name="regional-codes"></a>Regionális kódok

A hely URL-címe tartalmaz egy regionális kódot. Például az URL-cím `https://contosoblobaccount.z22.web.core.windows.net/` regionális kódot tartalmaz `z22` .

Habár a kódnak az URL-címben kell maradnia, csak belső használatra szolgál, és semmilyen más módon nem kell ezt a kódot használnia.

A statikus webhelyek üzemeltetésének engedélyezésekor megadott index-dokumentum akkor jelenik meg, amikor a felhasználók megnyitják a helyet, és nem határoznak meg konkrét fájlt (például: `https://contosoblobaccount.z22.web.core.windows.net` ).  

### <a name="secondary-endpoints"></a>Másodlagos végpontok

Ha [a redundanciát egy másodlagos régióban](../common/storage-redundancy.md#redundancy-in-a-secondary-region)állítja be, akkor a webhely tartalmát másodlagos végpont használatával is elérheti. Mivel az adatreplikációt a másodlagos régiókba aszinkron módon replikálja a rendszer, a másodlagos végponton elérhető fájlok nem mindig szinkronban vannak az elsődleges végponton elérhető fájlokkal. 

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>A webes tároló nyilvános hozzáférési szintjének beállításának hatása

Módosíthatja a **$web** tároló nyilvános hozzáférési szintjét, de ez nem befolyásolja az elsődleges statikus webhely végpontját, mert ezek a fájlok névtelen hozzáférési kérelmeken keresztül vannak kézbesítve. Ez azt jelenti, hogy a nyilvános (csak olvasható) hozzáférés minden fájlhoz.

Az alábbi képernyőfelvételen a Azure Portal nyilvános hozzáférési szintjének beállítása látható:

![A nyilvános hozzáférési szint megadását bemutató képernyőkép a portálon](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

Noha a rendszer nem érinti az elsődleges statikus webhely végpontját, a nyilvános hozzáférési szint módosítása hatással van az elsődleges blob Service-végpontra.

Ha például módosítja a **$web** tároló nyilvános hozzáférési szintjét **(nincs névtelen hozzáférés)** a **blobhoz (névtelen olvasási hozzáférés csak Blobok esetén)**, akkor az elsődleges statikus webhely végpontjának való nyilvános hozzáférés szintje `https://contosoblobaccount.z22.web.core.windows.net/index.html` nem változik.

Az elsődleges blob Service-végponthoz való nyilvános hozzáférés azonban `https://contosoblobaccount.blob.core.windows.net/$web/index.html` magánjellegűről nyilvánosra változik. Most a felhasználók megnyithatja a fájlt a két végpont egyikével.

## <a name="mapping-a-custom-domain-to-a-static-website-url"></a>Egyéni tartomány leképezése statikus webhely URL-címére

A statikus webhelyet egyéni tartományon keresztül is elérhetővé teheti. 

Könnyebben engedélyezhető a HTTP-hozzáférés az egyéni tartományhoz, mivel az Azure Storage natív módon támogatja azt. A HTTPS engedélyezéséhez Azure CDN kell használnia, mivel az Azure Storage még nem támogatja natív módon a HTTPS-t az egyéni tartományokkal. részletes útmutatásért lásd: [egyéni tartomány leképezése Azure Blob Storage végpontra](storage-custom-domain-name.md) .

Ha a Storage-fiók úgy van konfigurálva, hogy [biztonságos átvitelt igényel](../common/storage-require-secure-transfer.md) a HTTPS protokollon keresztül, akkor a felhasználóknak a https-végpontot kell használniuk. 

> [!TIP]
> Érdemes lehet tartományt üzemeltetni az Azure-ban. További információ: [tartomány üzemeltetése Azure DNSban](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="adding-http-headers"></a>HTTP-fejlécek hozzáadása

A statikus webhely funkciójának részeként nincs lehetőség fejlécek konfigurálására. Az Azure CDN segítségével azonban fejléceket adhat hozzá, és hozzáfűzheti (vagy felülírhatja) a fejlécek értékeit. Lásd: [a Standard Rules Engine referenciája Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-standard-rules-engine-reference).

Ha fejléceket kíván használni a gyorsítótárazás vezérléséhez, tekintse meg [a vezérlés Azure CDN gyorsítótárazási viselkedés a gyorsítótárazási szabályokkal](https://docs.microsoft.com/azure/cdn/cdn-caching-rules)című témakört.

## <a name="pricing"></a>Díjszabás

A statikus webhelyek üzemeltetése díjmentesen engedélyezhető. Csak a hely által használt BLOB Storage-hoz és az üzemeltetési költségekhez kell fizetnie. Az Azure Blob Storage áraival kapcsolatos további információkért tekintse meg az [azure blob Storage díjszabási oldalát](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Mérőszámok

A metrikák a statikus webhelyek oldalain is engedélyezhetők. A metrikák engedélyezése után a **$web** tárolóban található fájlokra vonatkozó forgalmi statisztikát a metrikák irányítópultján kell jelenteni.

A mérőszámok statikus webhely oldalain való engedélyezéséhez tekintse [meg a metrikák engedélyezése statikus webhelyeken](storage-blob-static-website-how-to.md#metrics)című témakört.

## <a name="next-steps"></a>Következő lépések

* [Statikus webhely üzemeltetése az Azure Storage-ban](storage-blob-static-website-how-to.md)
* [Egyéni tartomány leképezése egy Azure Blob Storage-végpontra](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Az első kiszolgáló nélküli Webalkalmazás létrehozása](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Oktatóanyag: Saját tartomány üzemeltetése az Azure DNS-ben](../../dns/dns-delegate-domain-azure-dns.md)
