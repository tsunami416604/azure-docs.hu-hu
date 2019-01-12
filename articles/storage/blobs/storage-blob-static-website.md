---
title: Statikus webhely üzemeltetése az Azure Storage-ban
description: Az Azure Storage statikus webhely üzemeltetése, a modern webalkalmazásokat szeretne üzemeltetni egy költséghatékony, méretezhető megoldás.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/19/18
ms.author: tamram
ms.component: blobs
ms.openlocfilehash: 0663f569edd0dec47949053b6ecf7fe49fa24a17
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54229302"
---
# <a name="static-website-hosting-in-azure-storage"></a>Statikus webhely üzemeltetése az Azure Storage-ban
Az Azure Storage GPv2-fiókok lehetővé teszik statikus tartalom (HTML, CSS, JavaScript és képfájlok) nevű tárolót történő közvetlen *$web*. Kihasználhatja a üzemeltetése az Azure Storage lehetővé teszi, hogy például kiszolgáló nélküli architektúrák [Azure Functions](/azure/azure-functions/functions-overview) és egyéb PaaS-szolgáltatások.

Statikus webhelyüzemeltetésre szakembereket szerveroldali kódot támaszkodó dinamikus webhelyek legjobb üzemeltetik [Azure App Service](/azure/app-service/overview).

## <a name="how-does-it-work"></a>Hogyan működik?
Statikus webhely engedélyezésekor a tárfiókot, a üzemeltetési, válassza ki az alapértelmezett fájl nevét, és megadhat egy egyéni 404-es oldal elérési útját. A szolgáltatás engedélyezve van, mert egy tároló nevű *$web* jön létre, ha még nem létezik.

A fájlok a *$web* tárolóban vannak:

- Névtelen hozzáférés kérelmeket szolgált
- csak olvasási műveletek objektumon keresztül érhető el
- kis-és nagybetűket
- az ezt a mintát a következő nyilvános interneten érhető el:
    - `https://<ACCOUNT_NAME>.<ZONE_NAME>.web.core.windows.net/<FILE_NAME>`
- Ezt a mintát a következő Blob storage-végponton keresztül érhető el:
    - `https://<ACCOUNT_NAME>.blob.core.windows.net/$web/<FILE_NAME>`

Fájlok feltöltése a Blob storage-végpont használatával történik. Ha például ezen a helyen feltölteni a fájlt:

```bash
https://contoso.blob.core.windows.net/$web/image.png
```

a böngészőben, ez például egy helyen érhető el:

```bash
https://contoso.z4.web.core.windows.net/image.png
```

A kijelölt alapértelmezett név szolgál a legfelső szintű és alkönyvtárakat, amikor egy fájl neve nincs megadva. Ha a kiszolgáló a 404-es adja vissza, és nem ad meg egy hibadokumentum elérési útja, majd egy alapértelmezett 404-es oldal küld vissza a felhasználó.

## <a name="cdn-and-ssl-support"></a>CDN-t és az SSL-támogatás

Hogy a statikus webhely fájlok elérhető HTTPS-kapcsolaton keresztül, lásd: [az Azure CDN használatával HTTPS-kapcsolaton keresztül egyéni tartománnyal rendelkező blobok elérése](storage-https-custom-domain-cdn.md). Ez a folyamat részeként kell *mutasson a CDN a webes végpontra* ellentétben a blob végpontja. Szükség lehet néhány percet, mielőtt a tartalmak jelenik meg a CDN-konfiguráció végrehajtása nem azonnal, várjon.


## <a name="custom-domain-names"></a>Egyéni tartománynevek

Is [az Azure Storage-fiókhoz tartozó egyéni tartománynév beállítása](storage-custom-domain-name.md) a statikus webhelye egy egyéni tartományt keresztül elérhetővé. A tartomány üzemeltetésének a szűrőtípusok a [Azure, lásd: üzemeltessen saját tartományt az Azure DNS](../../dns/dns-delegate-domain-azure-dns.md).

## <a name="pricing"></a>Díjszabás
Statikus webhely üzemeltetése további költségek nélkül biztosított. Az Azure Blob Storage-díjak a további részletekért tekintse meg a [Azure Blob Storage díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="quickstart"></a>Első lépések

### <a name="azure-portal"></a>Azure Portal
Első lépésként nyissa meg az Azure Portalra a https://portal.azure.com , és futtassa az alábbi lépéseket a GPv2-tárfiókban:

1. Kattintson a **beállításai**
2. Kattintson a **statikus webhely**
3. Adjon meg egy *indexdokumentum nevének*. (A gyakori értéke *index.html)*
4. Igény szerint adjon meg egy *hibadokumentum elérési útjának* egy egyéni 404-es oldalra. (A gyakori értéke *404.html)*

![](media/storage-blob-static-website/storage-blob-static-website-portal-config.PNG)

Ezután töltse fel az eszközök a *$web* az Azure Portalon vagy a tároló a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) teljes könyvtárak feltölteni. Ügyeljen arra, hogy egy fájlt, amely megfelel a *indexdokumentum nevének* választotta, amikor a funkció engedélyezése.

Végül nyissa meg a webes végpontra teszteli a webhelyét.

### <a name="azure-cli"></a>Azure CLI
A storage előzetes bővítmény telepítéséhez:

```azurecli-interactive
az extension add --name storage-preview
```
Több előfizetés esetén állítsa be az engedélyezni kívánt GPv2-tárfiók előfizetésében a parancssori felület:

```azurecli-interactive
az account set --subscription <SUBSCRIPTION_ID>
```
A funkció engedélyezéséhez. Ügyeljen arra, hogy cserélje le az összes helyőrző értékeket, beleértve a zárójeleket, a saját értékeire:

```azurecli-interactive
az storage blob service-properties update --account-name <ACCOUNT_NAME> --static-website --404-document <ERROR_DOCUMENT_NAME> --index-document <INDEX_DOCUMENT_NAME>
```
A webes végpont URL-lekérdezés:

```azurecli-interactive
az storage account show -n <ACCOUNT_NAME> -g <RESOURCE_GROUP> --query "primaryEndpoints.web" --output tsv
```

Töltse fel az objektumok a *$web* tároló forráskönyvtárból. Ügyeljen arra, hogy megfelelően escape-mutató hivatkozást a *$web* a parancsot a tároló. Például ha a cloud Shell az Azure CLI-vel használ az Azure Portalon, karakterpárt a *$web* tároló látható módon:

```azurecli-interactive
az storage blob upload-batch -s <SOURCE_PATH> -d \$web --account-name <ACCOUNT_NAME>
```

## <a name="deployment"></a>Környezet

Tartalom központi telepítése egy storage-tárolóba elérhető módszerek a következők:

- [AzCopy](../common/storage-use-azcopy.md)
- [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
- [Az Azure-folyamatok](https://code.visualstudio.com/tutorials/static-website/deploy-VSTS)
- [A Visual Studio Code-bővítménnyel](https://code.visualstudio.com/tutorials/static-website/getting-started)

Minden esetben ellenőrizze, hogy a fájlok másolása a *$web* tároló.

## <a name="metrics"></a>Mérőszámok

Ahhoz, hogy a statikus webhely lapjain a metrikák, kattintson a **beállítások** > **figyelés** > **metrikák**.

Metrikai adatok történetének más érdekes mérőszám API-k alapján jönnek létre. A portál csak annak érdekében, hogy csak összpontosíthat tagokat, amelyeket vissza adatokat egy adott időszakon belül használt API-t a tagok jeleníti meg. Annak érdekében, hogy ellenőrizze, hogy tud a válassza a szükséges API-t, az első lépéseként időkeretet kibontásához.

Kattintson az időkeret gombra, és válassza ki **az elmúlt 24 órából** majd **alkalmaz**

![Az Azure Storage-statikus webhelyek kiszolgálására metrikák időtartomány](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

Majd **Blob** származó a *Namespace* legördülő menü.

![Az Azure Storage statikus webhelyek kiszolgálására metrikák névtér](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

Válassza ki a **kimenő** metrikát.

![Az Azure Storage statikus webhelyek kiszolgálására metrikák metrika](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

Válassza ki **Sum** származó a *összesítési* választó.

![Az Azure Storage statikus webhelyek kiszolgálására metrikák összesítése](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

Ezután kattintson a **szűrő hozzáadása** gombra, és válasszon **API neve** származó a *tulajdonság* választó.

![Az Azure Storage statikus webhelyek kiszolgálására metrikák API neve](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

Végül a jelölőnégyzetet a **GetWebContent** a a *értékek* választó feltölti a metrikai jelentést.

![Az Azure Storage statikus webhelyek kiszolgálására metrikák GetWebContent](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

Az engedélyezés után a forgalom statisztika lévő fájlokat a *$web* tároló jelenti a metrikák irányítópultján.

## <a name="faq"></a>GYIK

**A statikus webhelyek kiszolgálására funkció érhető el minden storage fióktípus esetében?**  
Nem, statikus webhelyüzemeltetésre érhető el csak a GPv2-tárfiókok standard.

**Tároló virtuális hálózat és az új webes végpont támogatott tűzfalszabályok vannak?**  
Igen, az új webes végpont obeys a virtuális hálózat és tűzfal szabályokat a tárfiók számára beállított.

**Az a webes végpont kis-és nagybetűket?**  
Igen, a webes végpont kis-és nagybetűket csakúgy, mint a blob végpontja. 

## <a name="next-steps"></a>További lépések
* [Egyéni tartománnyal rendelkező blobok elérése az Azure CDN használatával HTTPS-kapcsolaton keresztül](storage-https-custom-domain-cdn.md)
* [A blob vagy a webszolgáltatás-végpont egyéni tartománynév konfigurálása](storage-custom-domain-name.md)
* [Azure Functions](/azure/azure-functions/functions-overview)
* [Azure App Service](/azure/app-service/overview)
* [Az első kiszolgáló nélküli webalkalmazás létrehozása](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Oktatóanyag: Üzemeltessen saját tartományt az Azure DNS-ben](../../dns/dns-delegate-domain-azure-dns.md)
