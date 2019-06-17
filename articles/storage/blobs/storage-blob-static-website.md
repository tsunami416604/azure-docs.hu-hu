---
title: Statikus webhely üzemeltetése az Azure Storage-ban
description: Az Azure Storage statikus webhely üzemeltetése, a modern webalkalmazásokat szeretne üzemeltetni egy költséghatékony, méretezhető megoldás.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.reviewer: seguler
ms.date: 05/29/2019
ms.subservice: blobs
ms.openlocfilehash: 36cc8cebdb567cb9650ad1ad3baf72a0b5478247
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427985"
---
# <a name="static-website-hosting-in-azure-storage"></a>Statikus webhely üzemeltetése az Azure Storage-ban

Statikus tartalom (HTML, CSS, JavaScript és képfájlok) nevű tárolót közvetlenül a hálózati tárából *$web*. A tartalom az Azure Storage-tároló lehetővé teszi, hogy tartalmazó kiszolgáló nélküli architektúrák [Azure Functions](/azure/azure-functions/functions-overview) és más Platform sem platformszolgáltatási (PaaS) szolgáltatásokra.

> [!NOTE]
> Ha a hely függ a szerveroldali kódot, [Azure App Service](/azure/app-service/overview) helyette.

## <a name="setting-up-a-static-website"></a>Statikus webhely beállítása

Statikus webhely üzemeltetése funkciója, amely a tárfiókban engedélyeznie kell.

Statikus webhely-üzemeltetésre engedélyezéséhez válassza ki az alapértelmezett fájl nevét, és igény szerint adjon meg egy egyéni 404-es oldal elérési útját. Ha egy blob storage-tároló nevű **$web** még nem létezik a fiókban lévő egyik jön létre az Ön számára. A hely a fájlok hozzá ezt a tárolót.

Lépésenkénti útmutatásért lásd: [működtethető az Azure Storage-ban statikus webhely](storage-blob-static-website-how-to.md).

![Az Azure Storage statikus webhelyek kiszolgálására metrikák metrika](./media/storage-blob-static-website/storage-blob-static-website-blob-container.png)

A fájlok a **$web** tároló nagybetűket, kiszolgált kérések névtelen hozzáférés és a csak olvasási műveletek elérhető.

## <a name="uploading-content"></a>Tartalom feltöltése

Felsorolt eszközök bármelyikével tartalmat feltölteni használhatja a **$web** tároló:

> [!div class="checklist"]
> * [Azure CLI](storage-blob-static-website-how-to.md#cli)
> * [Azure PowerShell-modul](storage-blob-static-website-how-to.md#powershell)
> * [AzCopy](../common/storage-use-azcopy-v10.md)
> * [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
> * [Az Azure-folyamatok](https://azure.microsoft.com/services/devops/pipelines/)
> * [Visual Studio Code extension](https://code.visualstudio.com/tutorials/static-website/getting-started)

## <a name="viewing-content"></a>Tartalom megtekintése

Felhasználók megtekinthetik a böngésző tartalmát nyilvános URL-címét a webhely használatával. Az URL-cím az Azure portal, az Azure CLI vagy a PowerShell használatával is megtalálhatja. Ez a táblázat használata alapján.

|Eszköz| Útmutatás |
|----|----|
|**Azure Portal** | [A webhely URL-Címének megkeresése az Azure portal használatával](storage-blob-static-website-how-to.md#portal-find-url) |
|**Azure CLI** | [A webhely URL-Címének megkeresése az Azure CLI-vel](storage-blob-static-website-how-to.md#cli-find-url) |
|**Azure PowerShell-modul** | [A webhely URL-cím keresése a PowerShell használatával](storage-blob-static-website-how-to.md#powershell-find-url) |

A webhely URL-címét a területi kódot tartalmaz. Például az URL-cím `https://contosoblobaccount.z22.web.core.windows.net/` területi kódot tartalmaz `z22`.

Bár ezt a kódot az URL-címet kell maradnia, csak belső használatra van, és nem kell használni, hogy a kód bármilyen egyéb módon.

Az index dokumentumot, adja meg, ha engedélyezi a statikus webhely-üzemeltetés, akkor jelenik meg, amikor a felhasználók nyissa meg a webhelyet, és nem ad meg egy adott fájlra (például: `https://contosoblobaccount.z22.web.core.windows.net`).  

Ha a kiszolgáló a 404-es hibát ad vissza, és nem adott meg-hiba történt a dokumentum a webhely engedélyezésekor, majd egy alapértelmezett 404-es oldal küld vissza a felhasználó.

## <a name="impact-of-the-setting-the-public-access-level-of-the-web-container"></a>A beállítás hatását a webes tárolót nyilvános hozzáférés szintje

Nyilvános hozzáférés szintje módosíthatja a **$web** tároló, de ez nem befolyásolja a statikus webhely elsődleges végpontja, mert ezek a fájlok fájlnévkiterjesztései névtelen hozzáférés kérelmeket. Ez azt jelenti, nyilvános (írásvédett) hozzáférés az összes olyan fájlra.

Az alábbi képernyőfelvételen a nyilvános hozzáférési szint beállítását az Azure Portalon:

![Képernyőfelvétel: hogyan állíthatja be a nyilvános hozzáférés szintje a portálon](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

A statikus webhely elsődleges végpontja nem érinti, míg a nyilvános hozzáférés szintje módosítás az elsődleges blob service-végpont nincs hatással.

Például, ha a nyilvános hozzáférés szintje módosítható a **$web** tárolóba való **privát (nincs névtelen hozzáférés)** való **Blob (névtelen olvasási hozzáférés csak blobokhoz)** , majd a a statikus webhely elsődleges végpontja való nyilvános hozzáférés szintje `https://contosoblobaccount.z22.web.core.windows.net/index.html` nem változik.

Azonban a nyilvános hozzáférést az elsődleges blob-szolgáltatásvégpont `https://contosoblobaccount.blob.core.windows.net/$web/index.html` saját nyilvános módosítja. Most már felhasználó meg tudja nyitni a fájlt a fenti két végpontok.

## <a name="content-delivery-network-cdn-and-secure-socket-layer-ssl-support"></a>Content Delivery Network (CDN) és a Secure Socket Layer (SSL)-támogatás

A statikus webhely fájlok elérhetővé tenni az egyéni tartomány és a HTTPS, lásd: [az Azure CDN használatával HTTPS-kapcsolaton keresztül egyéni tartománnyal rendelkező blobok elérése](storage-https-custom-domain-cdn.md). Ez a folyamat részeként, akkor át kell irányítania a CDN az elsődleges *statikus webhely* ellentétben az elsődleges végpont *blob szolgáltatás* végpont. Előfordulhat, hogy kell néhány percet, mielőtt a tartalmak jelenik meg a CDN-konfiguráció végrehajtása nem azonnal, várjon.

Amikor frissíti a statikus webhelye, mindenképpen törölje a gyorsítótárazott tartalom a CDN peremhálózati kiszolgálókon a CDN-végpont végleges törlése a. További információkért lásd az [Azure CDN-végpontok végleges törléséről](../../cdn/cdn-purge-endpoint.md) szóló cikket.

> [!NOTE]
> HTTPS támogatott natív módon a fiók webes végponton keresztül, a webes végpont esetében a HTTP és HTTPS-en keresztül elérhető-e. Azonban ha a tárfiók biztonságos átvitel megkövetelése a HTTPS-kapcsolaton keresztül van konfigurálva, majd felhasználókat kell használnia a HTTPS-végpont. További információkért lásd: [az Azure Storage-ban biztonságos átvitel megkövetelése](../common/storage-require-secure-transfer.md).
>
> Az egyéni tartományok HTTPS-kapcsolaton keresztül használatához jelenleg az Azure CDN használata.

## <a name="custom-domain-names"></a>Egyéni tartománynevek

Elérhetővé teheti a statikus webhelye egy egyéni tartomány használatával. További tudnivalókért lásd: [az Azure Storage-fiókhoz tartozó egyéni tartománynév beállítása](storage-custom-domain-name.md).

Szűrőtípusok a tartományt az Azure-ban üzemeltető, lásd: [üzemeltessen saját tartományt az Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Díjszabás

Engedélyezheti a statikus webhely üzemeltetése díjmentes. A számlázás a blob storage, amely a webhely már használja és az üzemeltetés költségeit. Az Azure Blob Storage-díjak a további részletekért tekintse meg a [Azure Blob Storage díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="metrics"></a>Mérőszámok

Statikus webhely lapjain metrikákat is engedélyezheti. Miután engedélyezte a metrikák, forgalom statisztikai értéket is a fájlok a **$web** tároló jelenti a metrikák irányítópultján.

Engedélyezheti a statikus webhely lapjain a metrikák [engedélyezze a mérőszámok a statikus webhely lapokon](storage-blob-static-website-how-to.md#metrics).

## <a name="next-steps"></a>További lépések

* [Az Azure Storage-ban statikus webhely üzemeltetése](storage-blob-static-website-how-to.md)
* [Az Azure CDN használatával HTTPS-kapcsolaton keresztül egyéni tartománnyal rendelkező blobok elérése](storage-https-custom-domain-cdn.md)
* [A blob vagy a webszolgáltatás-végpont egyéni tartománynév konfigurálása](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Az első kiszolgáló nélküli webalkalmazás létrehozása](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Oktatóanyag: Üzemeltessen saját tartományt az Azure DNS-ben](../../dns/dns-delegate-domain-azure-dns.md)
