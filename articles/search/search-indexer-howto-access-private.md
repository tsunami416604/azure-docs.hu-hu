---
title: Indexelő kapcsolatai egy privát végponton keresztül
titleSuffix: Azure Cognitive Search
description: Az indexelő kapcsolatainak konfigurálásával más Azure-erőforrásoktól származó tartalmakat is elérhet, amelyeket privát végponton keresztül véd.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: bcacd6c2e1353c71d8e4e25c95ee2b563e7b3fba
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150413"
---
# <a name="indexer-connections-through-a-private-endpoint-azure-cognitive-search"></a>Indexelő kapcsolatai egy privát végponton keresztül (Azure Cognitive Search)

Számos Azure-erőforrás (például az Azure Storage-fiókok) konfigurálható úgy, hogy fogadja a virtuális hálózatok egy adott listájáról érkező kapcsolatokat, és elutasítja a nyilvános hálózatról származó kapcsolaton kívüli kapcsolatokat. Ha indexelő használatával indexeli az adatait az Azure Cognitive Searchban, és az adatforrás egy magánhálózaton található, létrehozhat egy (kimenő) [privát végponti kapcsolattal](../private-link/private-endpoint-overview.md) az adatelérést.

Az indexelő kapcsolódási módszer használatához két követelmény van:

+ A tartalmat vagy kódot biztosító Azure-erőforrást előzőleg regisztrálni kell az [Azure Private link Service](https://azure.microsoft.com/services/private-link/)-ben.

+ Az Azure Cognitive Search szolgáltatásnak alapszintű vagy magasabbnak kell lennie (az ingyenes szint nem érhető el). Emellett a készségkészlet rendelkező indexelő esetében a keresési szolgáltatásnak S2 vagy magasabbnak kell lennie. További információ: [szolgáltatási korlátok](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

## <a name="shared-private-link-resources-management-apis"></a>Megosztott privát kapcsolatok erőforrásainak kezelési API-jai

Az Azure Cognitive Search API-kon keresztül létrehozott biztonságos erőforrások privát végpontait *megosztott privát kapcsolati erőforrásoknak* nevezzük, mivel olyan erőforráshoz (például egy Storage-fiókhoz) való hozzáférésre van szüksége, amelyet az [Azure Private link Service](https://azure.microsoft.com/services/private-link/)-be állítottak be.

A felügyeleti REST API az Azure Cognitive Search [CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) műveletet biztosít, amellyel konfigurálhatja az Azure Cognitive Search indexelő szolgáltatáshoz való hozzáférést.

Az egyes erőforrásokhoz tartozó magánhálózati végpontok csak a Search Management API (vagy újabb) előzetes verziójával hozhatók létre, és az `2020-08-01-Preview` alábbi táblázatban az "Előnézet" címke szerepel. Az "Előnézet" címkével nem rendelkező erőforrások az előnézet vagy az általánosan elérhető API-verzió ( `2020-08-01` vagy újabb) használatával hozhatók létre.

Az alábbi lista azokat az Azure-erőforrásokat sorolja fel, amelyekhez a kimenő magánhálózati végpontok létrehozhatók az Azure Cognitive Searchból. Az `groupId` alábbi táblázatban felsorolt értékeket pontosan úgy kell használni, ahogy az API-ban (kis-és nagybetűk megkülönböztetése) az API-ban megosztott privát kapcsolati erőforrás létrehozásához.

| Azure-erőforrás | Csoportazonosító |
| --- | --- |
| Azure Storage – blob (vagy) ADLS Gen 2 | `blob`|
| Azure Storage – táblák | `table`|
| Azure Cosmos DB-SQL API | `Sql`|
| Azure SQL Database | `sqlServer`|
| Azure Database for MySQL (előzetes verzió) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions (előzetes verzió) | `sites` |

A [támogatott API-k listájának](/rest/api/searchmanagement/privatelinkresources/listsupported)használatával lekérdezhető az Azure-erőforrások listája, amelyekhez a kimenő magánhálózati végpontok kapcsolatai támogatottak.

A cikk további részében a [ARMClient](https://github.com/projectkudu/ARMClient) és a [Poster](https://www.postman.com/) együttes használatával mutatjuk be a REST API hívásokat.

> [!NOTE]
> Ez a cikk azt feltételezi, hogy a Search szolgáltatás neve a __contoso-Search__ , amely létezik az előfizetés __00000000-0000-0000-0000-000000000000__-es azonosítójú előfizetésben. __contoso__ A keresési szolgáltatás erőforrás-azonosítója a következő lesz `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search`

A további példák azt mutatják be, hogy a __contoso-Search__ szolgáltatás hogyan konfigurálható úgy, hogy az indexelő hozzáférhessenek az adatokhoz a biztonságos Storage-fiókból. `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage`

## <a name="securing-your-storage-account"></a>A Storage-fiók biztonságossá tétele

Konfigurálja úgy a Storage-fiókot, hogy [csak adott alhálózatokról engedélyezze a hozzáférést](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network). Ha bejelöli ezt a beállítást, és hagyja üresen a készletet, akkor a Azure Portalban nem engedélyezett a virtuális hálózatról érkező forgalom.

   ![Virtual Network hozzáférés](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png "Virtual Network hozzáférés")

> [!NOTE]
> A [megbízható Microsoft-szolgáltatás megközelítés](../storage/common/storage-network-security.md#trusted-microsoft-services) használatával kikerülheti a virtuális hálózat vagy az IP-korlátozásokat egy ilyen Storage-fiókra, és engedélyezheti a keresési szolgáltatás számára a Storage-fiókban tárolt adatelérést, az [Indexelő hozzáférés az Azure Storage-hoz a megbízható szolgáltatás kivételével ](search-indexer-howto-access-trusted-service-exception.md). Ha azonban ezt a módszert használja az Azure Cognitive Search és a Storage-fiók közötti kommunikációra, a Storage-fiók nyilvános IP-címén keresztül történik a biztonságos Microsoft gerinces hálózaton.

## <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>1. lépés: megosztott magánhálózati kapcsolati erőforrás létrehozása a Storage-fiókhoz

Végezze el a következő API-hívást, hogy az Azure Cognitive Searcht hozzon létre egy kimenő magánhálózati végponti kapcsolódást a Storage-fiókhoz

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

A fájl tartalma `create-pe.json` (amely a kérés törzsét jelképezi az API-nak) a következő:

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

`202 Accepted`Sikeres válasz érkezik – a kimenő magánhálózati végpont létrehozásának folyamata hosszú futású (aszinkron) művelet. A következő erőforrások üzembe helyezését foglalja magában:

1. Egy magánhálózati IP-címmel rendelkező magánhálózati végpont `"Pending"` állapotban van. A magánhálózati IP-címet a keresési szolgáltatás adott privát indexelő végrehajtási környezetének virtuális hálózatához lefoglalt címtartomány alapján szerezték be. A privát végpont jóváhagyása után az Azure Cognitive Searchról a Storage-fiókra irányuló minden kommunikáció a magánhálózati IP-címről és egy biztonságos privát kapcsolati csatornából származik.
2. Saját DNS-zóna az erőforrás típusához, a alapján `groupId` . Ezzel biztosíthatja, hogy a privát erőforrásra irányuló DNS-keresés a privát végponthoz társított IP-címet használja.

Ügyeljen arra, hogy megfelelő értéket adjon meg ahhoz `groupId` az erőforráshoz, amelyhez a magánhálózati végpontot létrehozza. Az eltérések nem megfelelő válaszüzenetet eredményeznek.

Az összes aszinkron Azure-művelethez hasonlóan a `PUT` hívás olyan fejléc-értéket ad vissza, `Azure-AsyncOperation` amely a következőképpen fog kinézni:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

Ezt az URI-t rendszeresen le lehet kérdezni a művelet állapotának beszerzéséhez. Javasoljuk, hogy a folytatás előtt várjon, amíg a megosztott magánhálózati kapcsolati erőforrás műveleti állapota elérte a terminál állapotát (azaz `succeeded` ).

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

## <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>2a. lépés: a Storage-fiókhoz tartozó magánhálózati végponti kapcsolatok jóváhagyása

> [!NOTE]
> Ez a szakasz Azure Portal használatával végigvezeti a privát végpontok tárolásához szükséges jóváhagyási folyamaton. Ehelyett a Storage erőforrás-szolgáltatón (RP) keresztül elérhető [REST API](/rest/api/storagerp/privateendpointconnections) is használható.
>
> Más szolgáltatók, például a CosmosDB, az Azure SQL Server stb. is hasonló RP API-kat kínálnak a privát végpontok kapcsolatainak kezeléséhez.

Navigáljon a "**privát végpont kapcsolatai**" lapra a Azure Portal található Storage-fiókhoz. A privát végponti kapcsolatok iránti kérelemnek az előző API-hívással (az aszinkron művelet __sikeres__végrehajtása után) kell lennie.

   ![Privát végpont jóváhagyása](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png "Privát végpont jóváhagyása")

Válassza ki az Azure Cognitive Search által létrehozott privát végpontot (a "privát végpont" oszlop használatával azonosítsa a privát végponti kapcsolatokat az előző API-ban megadott név alapján), és válassza a "jóváhagyás" lehetőséget egy megfelelő üzenettel (az üzenet nem jelentős). Győződjön meg arról, hogy a magánhálózati végponti kapcsolatok az alábbiak szerint jelennek meg (a portálon akár 1-2 percet is igénybe vehet)

![Privát végpont jóváhagyva](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png "Privát végpont jóváhagyva")

A magánhálózati végponti kapcsolatfelvételi kérelem jóváhagyása után az azt jelenti, hogy a forgalom *képes* a privát végponton keresztül átáramlani. Miután jóváhagyta a privát végpontot, az Azure Cognitive Search létrehozza a szükséges DNS-zónák hozzárendeléseit a létrehozott DNS-zónában.

## <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>2b. lépés: a megosztott magánhálózati kapcsolati erőforrás állapotának lekérdezése

 Annak ellenőrzéséhez, hogy a megosztott magánhálózati kapcsolati erőforrás jóváhagyása után frissült-e, szerezze be az állapotát a [Get API](/rest/api/searchmanagement/sharedprivatelinkresources/get)használatával.

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Ha az `properties.provisioningState` erőforrás `Succeeded` és az is, az azt `properties.status` `Approved` jelenti, hogy a megosztott magánhálózati kapcsolati erőforrás működik, és az indexelő úgy konfigurálható, hogy a magánhálózati végponton keresztül kommunikáljanak.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

## <a name="step-3-configure-indexer-to-run-in-the-private-environment"></a>3. lépés: az indexelő konfigurálása a privát környezetben való futtatásra

> [!NOTE]
> Ezt a lépést még a privát végponti kapcsolatok jóváhagyása előtt is el lehet elvégezni. A magánhálózati végponti kapcsolatok jóváhagyása előtt minden olyan indexelő, amely egy biztonságos erőforrással (például a Storage-fiókkal) kommunikálni próbál, egy átmeneti meghibásodási állapotba kerül. Az új indexelő létrehozása sikertelen lesz. A privát végponti kapcsolat jóváhagyása után az indexelő hozzáférhetnek a privát Storage-fiókhoz.

1. [Hozzon létre egy adatforrást](/rest/api/searchservice/create-data-source) , amely a biztonságos Storage-fiókra és a Storage-fiókban található megfelelő tárolóra mutat. A következő üzenet ezt a kérelmet mutatja be Poster-ben.
![Adatforrás létrehozása](media\search-indexer-howto-secure-access\create-ds.png "Adatforrás létrehozása")

1. Hasonlóképpen [hozzon létre egy indexet](/rest/api/searchservice/create-index) , és opcionálisan [hozzon létre egy készségkészlet](/rest/api/searchservice/create-skillset) a REST API használatával.

1. [Hozzon létre egy indexelő](/rest/api/searchservice/create-indexer) , amely a fent létrehozott adatforrásra, indexre és készségkészlet mutat. Továbbá kényszerítse az indexelő futtatását a privát végrehajtási környezetben, az indexelő konfigurációs tulajdonságának beállításával `executionEnvironment` `"Private"` .
![Indexelő létrehozása](media\search-indexer-howto-secure-access\create-idr.png "Indexelő létrehozása")

Az indexelő sikeresen létre kell hozni, és előrehaladást kell biztosítania a Storage-fiókból a privát végponti kapcsolaton keresztül. Az indexelő állapota az [Indexelő status API](/rest/api/searchservice/get-indexer-status)használatával figyelhető.

> [!NOTE]
> Ha már rendelkezik meglévő indexelő szolgáltatásokkal, egyszerűen frissítheti őket a [put API](/rest/api/searchservice/create-indexer) -n keresztül, hogy beállítsa a `executionEnvironment` -t `"Private"` .

## <a name="troubleshooting-issues"></a>Problémák hibaelhárítása

- Indexelő létrehozásakor, ha a létrehozás az "adatforrás hitelesítő adatai érvénytelenek" hibaüzenettel meghiúsul, az azt jelenti, hogy a magánhálózati végponti kapcsolat nem lett *jóváhagyva* , vagy nem működik.
Szerezze be a megosztott privát kapcsolati erőforrás állapotát a [Get API](/rest/api/searchmanagement/sharedprivatelinkresources/get)használatával. Ha *jóváhagyták* , ellenőrizze az `properties.provisioningState` erőforrást. Ha igen `Incomplete` , ez azt jelenti, hogy az erőforráshoz tartozó mögöttes függőségek némelyike nem tudta kiépíteni a `PUT` kérést, hogy a probléma megoldásához szükséges megosztott privát kapcsolati erőforrás "újból létrehozza" a kérelmet. Szükség lehet újbóli jóváhagyásra – ellenőrizze az erőforrás állapotát.
- Ha az indexelő létrehozása a beállítása nélkül történik `executionEnvironment` , az indexelő létrehozása sikeres lehet, de a végrehajtási előzmények azt mutatják, hogy az indexelő futtatása sikertelen. A végrehajtási környezet megadásához [frissítenie kell az indexelő](/rest/api/searchservice/update-indexer) .
- Ha az indexelő létrehozása a és a sikeres futtatása nélkül történik, az azt `executionEnvironment` jelenti, hogy az Azure Cognitive Search úgy döntött, hogy végrehajtási környezete a keresési szolgáltatás specifikus "privát" környezete. Ez azonban a különböző tényezők (az indexelő által felhasznált erőforrások, a keresési szolgáltatás terhelése stb.) alapján változhat, és egy későbbi időpontban meghiúsulhat – javasoljuk, hogy állítsa be úgy, hogy a `executionEnvironment` `"Private"` későbbiekben ne legyen sikertelen.
- A [kvóták és a korlátok](search-limits-quotas-capacity.md) határozzák meg, hogy hány megosztott magánhálózati kapcsolati erőforrás hozható létre, és függ a keresési szolgáltatás SKU-jának.

## <a name="next-steps"></a>Következő lépések

További információ a privát végpontokról:

- [Mik azok a privát végpontok?](../private-link/private-endpoint-overview.md)
- [Magánhálózati végpontokhoz szükséges DNS-konfigurációk](../private-link/private-endpoint-dns.md)