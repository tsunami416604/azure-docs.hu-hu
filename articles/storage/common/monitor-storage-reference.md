---
title: Az Azure Storage figyelési adatreferenciája | Microsoft Docs
description: Naplózási és metrikai referenciák az Azure Storage szolgáltatásban tárolt adatok figyeléséhez.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/01/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 28a127b4debeacd2562867008bc594897558d50d
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446833"
---
# <a name="azure-storage-monitoring-data-reference"></a>Az Azure Storage figyelési adatreferenciája

Az Azure Storage figyelési adatainak gyűjtésével és elemzésével kapcsolatos részletekért lásd: az [Azure Storage figyelése](monitor-storage.md) .

## <a name="metrics"></a>Mérőszámok

Az alábbi táblázatok felsorolják az Azure Storage-hoz összegyűjtött platform metrikákat. 

### <a name="capacity-metrics"></a>Kapacitásmetrikák

A kapacitás metrikáinak értékeit minden órában Azure Monitor elküldjük. Az értékek naponta frissülnek. Az időkeret határozza meg azt az időintervallumot, ameddig a metrikák értékei bemutatva lesznek. Az összes kapacitás metrikájának támogatott időszelete egy óra (PT1H).

Az Azure Storage a következő kapacitási mérőszámokat biztosítja Azure Monitorban.

#### <a name="account-level"></a>Fiók szintje

Ez a táblázat a [fiók szintű metrikákat](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccounts)jeleníti meg.

| Metrika | Leírás |
| ------------------- | ----------------- |
| UsedCapacity | A Storage-fiók által használt tárterület mennyisége. Standard szintű tárfiókok esetében ez a blob, a tábla, a fájl és a várólista által használt kapacitás összege. Prémium szintű és Blob Storage-fiókok esetében a BlobCapacity értékével azonos. <br/><br/> Egység: bájtok <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 |

#### <a name="blob-storage"></a>Blob Storage

Ez a táblázat a [blob Storage metrikáit](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsblobservices)mutatja be.

| Metrika | Leírás |
| ------------------- | ----------------- |
| BlobCapacity | A Storage-fiókban használt BLOB Storage teljes száma. <br/><br/> Egység: bájtok <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 <br/> Méretek: **BlobType**és **BlobTier** ([definíció](#metrics-dimensions)) |
| BlobCount    | A Storage-fiókban tárolt blob-objektumok száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 <br/> Méretek: **BlobType**és **BlobTier** ([definíció](#metrics-dimensions)) |
| BlobProvisionedSize | A Storage-fiókban kiépített tárterület mennyisége. Ez a mérőszám csak a Premium Storage-fiókokra vonatkozik. <br/><br/> Egység: bájtok <br/> Összesítés típusa: átlag |
| ContainerCount    | A Storage-fiókban lévő tárolók száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 |
| IndexCapacity     | A ADLS Gen2 hierarchikus index által használt tárterület mennyisége <br/><br/> Egység: bájtok <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 |

#### <a name="table-storage"></a>Table Storage

Ez a táblázat a [táblázatos tároló metrikáit](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountstableservices)jeleníti meg.

| Metrika | Leírás |
| ------------------- | ----------------- |
| TableCapacity | A Storage-fiók által használt Table Storage mennyisége. <br/><br/> Egység: bájtok <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 |
| TableCount   | A Storage-fiókban lévő táblák száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 |
| TableEntityCount | A Storage-fiókban lévő tábla entitások száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 |

#### <a name="queue-storage"></a>Queue Storage

Ez a táblázat a [várólista-tároló metrikáit](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsfileservices)jeleníti meg.

| Metrika | Leírás |
| ------------------- | ----------------- |
| QueueCapacity | A Storage-fiók által használt üzenetsor-tároló mennyisége. <br/><br/> Egység: bájtok <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 |
| QueueCount   | A Storage-fiókban lévő várólisták száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 |
| QueueMessageCount | A tárolási fiók Queue szolgáltatásban lévő üzenetsor-üzenetek hozzávetőleges száma. <br/><br/>Egység: darabszám <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 |

#### <a name="file-storage"></a>File Storage

Ez a táblázat a [file Storage mérőszámait](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsqueueservices)jeleníti meg.

| Metrika | Leírás |
| ------------------- | ----------------- |
| FileCapacity | A Storage-fiók által használt file Storage mennyisége. <br/><br/> Egység: bájtok <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 |
| FileCount   | A Storage-fiókban található fájlok száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 |
| FileShareCount | A Storage-fiókban lévő fájlmegosztás száma. <br/><br/> Egység: darabszám <br/> Összesítés típusa: átlag <br/> Érték példa: 1024 |
| FileShareProvisionedIOPS | A fájlmegosztás kiépített IOPS száma. Ez a mérőszám csak a Premium file Storage esetében alkalmazható. <br/><br/> Egység: bájtok <br/> Összesítés típusa: átlag |

### <a name="transaction-metrics"></a>Tranzakciómetrikák

A tranzakciós metrikák az Azure Storage-ból Azure Monitorba történő minden kérelemre kibocsátva. Abban az esetben, ha nincs tevékenység a Storage-fiókban, az adott időszakban nem lesz adat a tranzakciós metrikákban. Minden tranzakciós metrika a fiók és a szolgáltatás szintjén (blob Storage, Table Storage, Azure Files és üzenetsor-tárolás) is elérhető. Az időgabona meghatározza a metrikai értékek bemutatásának időintervallumát. Az összes tranzakciós metrika támogatott időgabona-PT1H és PT1M.

Az Azure Storage a következő tranzakciós mérőszámokat biztosítja Azure Monitorban.

| Metrika | Leírás |
| ------------------- | ----------------- |
| Tranzakciók | Tárolási szolgáltatás vagy a megadott API-művelet számára elküldött kérések száma. Ez az érték a sikeres és sikertelen kérések, valamint a hibára futott kérések számát tartalmazza. <br/><br/> Egység: darabszám <br/> Összesítés típusa: összesen <br/> Alkalmazandó méretek: ResponseType, GeoType, ApiName és hitelesítés ([definíció](#metrics-dimensions))<br/> Érték példa: 1024 |
| Bejövő forgalom | A bejövő adatok mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló bejövő adatait és az Azure-on belüli bejövő adatokat egyaránt magában foglalja. <br/><br/> Egység: bájtok <br/> Összesítés típusa: összesen <br/> Alkalmazandó méretek: GeoType, ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték példa: 1024 |
| Kimenő forgalom | A kimenő adatok mennyisége. Ez a szám a külső ügyfél Azure Storage-ba irányuló kimenő adatait és az Azure-on belüli kimenő adatokat egyaránt magában foglalja. Az eredményül kapott szám nem tükrözi a számlázható kimenő forgalmat. <br/><br/> Egység: bájtok <br/> Összesítés típusa: összesen <br/> Alkalmazandó méretek: GeoType, ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték példa: 1024 |
| SuccessServerLatency | Az Azure Storage által sikeresen feldolgozott kérések átlagos feldolgozási ideje. Ez az érték nem tartalmazza a SuccessE2ELatency paraméterben megadott hálózati késleltetést. <br/><br/> Egység: ezredmásodperc <br/> Összesítés típusa: átlag <br/> Alkalmazandó méretek: GeoType, ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték példa: 1024 |
| SuccessE2ELatency | A tárolási szolgáltatás vagy a megadott API-művelet számára elküldött sikeres kérések végpontok közötti késésének átlaga. Ez az érték magában foglalja a kérelem elolvasásához, a válasz elküldéséhez és a válasz visszaigazolásának fogadásához az Azure Storage számára szükséges feldolgozási időt. <br/><br/> Egység: ezredmásodperc <br/> Összesítés típusa: átlag <br/> Alkalmazandó méretek: GeoType, ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték példa: 1024 |
| Rendelkezésre állás | A tárolási szolgáltatás vagy a megadott API-művelet rendelkezésre állási hányada. A Rendelkezésre állás az összes számlázható kérelem értékének és a vonatkozó kérelmek számának (a nem várt hibákat eredményező kérelmeket is beleértve) a hányadosa. A nem várt hibák a tárolószolgáltatás vagy a megadott API-művelet rendelkezésre állásának csökkenését eredményezik. <br/><br/> Egység: százalék <br/> Összesítés típusa: átlag <br/> Alkalmazandó méretek: GeoType, ApiName és hitelesítés ([definíció](#metrics-dimensions)) <br/> Érték példa: 99,99 |

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Metrikák méretei

Az Azure Storage a Azure Monitor metrikáinak következő dimenzióit támogatja.

| Dimenzió neve | Leírás |
| ------------------- | ----------------- |
| **BlobType** | A blob típusa csak a blob-metrikák esetében. A támogatott értékek a következők: **BlockBlob**, **PageBlob**és **Azure Data Lake Storage**. A hozzáfűző blobokat a **BlockBlob**tartalmazza. |
| **BlobTier** | Az Azure Storage különböző hozzáférési szinteket kínál, amelyek lehetővé teszik a blob-objektumok-adattárolást a legköltséghatékonyabb módon. További információ az [Azure Storage blob-szintjéről](../blobs/storage-blob-storage-tiers.md). A támogatott értékek a következők: <br/> <li>**Gyors**: gyors elérésű szint</li> <li>**Cool**: lassú szint</li> <li>**Archive**: archiválási szint</li> <li>**Premium**: prémium szint a blob blokkhoz</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: szintű prémium oldal blob</li> <li>**Standard**: a standard oldal blobjának típusa</li> <li>Nem **rétegű**: az általános célú v1-es Storage-fiók típusa</li> |
| **GeoType** | Az elsődleges vagy a másodlagos fürtből származó tranzakció. Az elérhető értékek közé tartozik az **elsődleges** és a **másodlagos**. Az olvasási hozzáférés földrajzi redundáns tárolási (RA-GRS) szolgáltatásra vonatkozik, amikor objektumokat olvas a másodlagos bérlőről. |
| **ResponseType** | Tranzakció válaszának típusa Az elérhető értékek közé a következők tartoznak: <br/><br/> <li>**ServerOtherError**: Minden egyéb kiszolgálóoldali hiba, kivéve az ismertetett hibákat </li> <li>**ServerBusyError**: Hitelesített kérés, amely HTTP 503-as állapotkódot adott vissza. </li> <li>**ServerTimeoutError**: Hitelesített kérés, amely túllépte az időkorlátot, és HTTP 500-as állapotkódot adott vissza. Az időtúllépés egy kiszolgálóhiba miatt lépett fel. </li> <li>**AuthorizationError**: Hitelesített kérés, amely jogosulatlan adathozzáférés vagy egy engedélyezési hiba miatt hiúsult meg. </li> <li>**NetworkError**: Hitelesített kérés, amely hálózati hibák miatt hiúsult meg. Leggyakrabban akkor fordul elő, ha egy ügyfél idő előtt, az időkorlát letelte előtt zár be egy kapcsolatot. </li><li>**ClientAccountBandwidthThrottlingError**: a kérés sávszélességét a rendszer a [tárolási fiók skálázhatósági korlátainak](scalability-targets-standard-account.md)túllépése miatt szabályozza.</li><li>**ClientAccountRequestThrottlingError**: a kérést a rendszer a [tárolási fiók skálázhatósági korlátait](scalability-targets-standard-account.md)meghaladó kérések díjszabása szerint szabályozza.<li>**ClientThrottlingError**: más ügyféloldali sávszélesség-szabályozási hiba. A ClientAccountBandwidthThrottlingError és a ClientAccountRequestThrottlingError ki vannak zárva.</li> <li>**ClientTimeoutError**: Hitelesített kérés, amely túllépte az időkorlátot, és HTTP 500-as állapotkódot adott vissza. Ha az ügyfél hálózati időkorlátja vagy a kérés időkorlátja a tárolási szolgáltatás által várt értéknél alacsonyabbra van állítva, akkor ez egy várt időtúllépés. Máskülönben a rendszer ServerTimeoutError hibát jelent. </li> <li>**ClientOtherError**: Minden egyéb ügyféloldali hiba, kivéve az ismertetett hibákat. </li> <li>**Success**: Sikeres kérés</li> <li> **SuccessWithThrottling**: sikeres kérés, ha az SMB-ügyfél az első kísérlet (ek) során leszabályozza az újrapróbálkozásokat, de az újrapróbálkozások után sikeres lesz.</li> |
| **ApiName** | A művelet neve. Például: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> Az összes művelet neve: [Document](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Hitelesítés** | A tranzakciókban használt hitelesítési típus. Az elérhető értékek közé a következők tartoznak: <br/> <li>**AccountKey**: a tranzakciót a Storage-fiók kulcsa hitelesíti.</li> <li>**Sas**: a tranzakció megosztott hozzáférési aláírásokkal van hitelesítve.</li> <li>**OAuth**: a tranzakció OAuth hozzáférési jogkivonatokkal van hitelesítve.</li> <li>**Névtelen**: a rendszer névtelenül kéri a tranzakciót. Nem tartalmaz elővizsgálati kérelmeket.</li> <li>**AnonymousPreflight**: a tranzakció elővizsgálati kérelem.</li> |

A dimenziókat támogató metrikák esetében meg kell adnia a dimenzió értékét a megfelelő mérőszámok értékeinek megtekintéséhez. Ha például a sikeres válaszok **tranzakció** értékét tekinti át, akkor a **ResponseType** dimenziót a **sikerrel**kell szűrnie. Vagy ha megtekinti a blokk **BlobCount** értékét, akkor a **BlobType** -dimenziót a **BlockBlob**-mel kell szűrnie.

## <a name="resource-logs-preview"></a>Erőforrás-naplók (előzetes verzió)

> [!NOTE]
> Az Azure Storage-naplók Azure Monitor nyilvános előzetes verzióban érhető el, és az előzetes teszteléshez minden nyilvános felhőalapú régióban elérhető. Az előzetes verzióra való regisztráláshoz tekintse meg [ezt a lapot](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Ez az előzetes verzió lehetővé teszi a Blobok (többek között a Azure Data Lake Storage Gen2), a fájlok, a várólisták, a táblák és a Premium Storage-fiókok általános célú v1 és általános célú v2 Storage-fiókokban való naplózását. A klasszikus Storage-fiókok nem támogatottak.

A következő táblázat felsorolja az Azure Storage-beli erőforrás-naplók tulajdonságait Azure Monitor naplókba vagy az Azure Storage-ba való gyűjtéskor. A tulajdonságok leírják a műveletet, a szolgáltatást és a művelet végrehajtásához használt engedélyezési típust.

### <a name="fields-that-describe-the-operation"></a>A műveletet leíró mezők

```json
{
    "time": "2019-02-28T19:10:21.2123117Z",
    "resourceId": "/subscriptions/12345678-2222-3333-4444-555555555555/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/testaccount1/blobServices/default",
    "category": "StorageWrite",
    "operationName": "PutBlob",
    "operationVersion": "2017-04-17",
    "schemaVersion": "1.0",
    "statusCode": 201,
    "statusText": "Success",
    "durationMs": 5,
    "callerIpAddress": "192.168.0.1:11111",
    "correlationId": "ad881411-201e-004e-1c99-cfd67d000000",
    "location": "uswestcentral",
    "uri": "http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10"
}
```

| Tulajdonság | Leírás |
|:--- |:---|
|**idő** | Az az egyezményes világidő (UTC), amikor a kérést a tároló fogadta. Például: `2018/11/08 21:09:36.6900118`.|
|**resourceId** | A Storage-fiók erőforrás-azonosítója. Például: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**Kategória** | A kért művelet kategóriája. Például: `StorageRead` , `StorageWrite` , vagy `StorageDelete` .|
|**operationName** | A végrehajtott REST-művelet típusa. <br> A műveletek teljes listáját lásd: [Storage Analytics naplózott műveletek és állapotüzenetek témakör](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
|**operationVersion** | A tárolási szolgáltatás azon verziója, amely a kérelem végrehajtásakor lett megadva. Ez egyenértékű az **x-MS-Version** fejléc értékével. Például: `2017-04-17`.|
|**sémaverzióval** | A napló séma-verziója. Például: `1.0`.|
|**statusCode** | A kérelem HTTP-állapotkódot. Ha a kérés megszakad, lehet, hogy ez az érték értéke `Unknown` . <br> Például: `206` |
|**statusText** | A kért művelet állapota.  Az állapotüzenetek teljes listáját lásd: [Storage Analytics naplózott műveletek és állapotüzenetek témakör](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). Az 2017-04-17-es és újabb verziókban az állapotjelző üzenet `ClientOtherError` nincs használatban. Ehelyett ez a mező hibakódot tartalmaz. Például: `SASSuccess`  |
|**Átl** | A kért művelet végrehajtásához szükséges teljes idő ezredmásodpercben kifejezve. Ide tartozik a beérkező kérelem olvasásának ideje, valamint a válasz küldése a kérelmezőnek. Például: `12`.|
|**callerIpAddress** | A kérelmező IP-címe, beleértve a portszámot is. Például: `192.100.0.102:4362`. |
|**correlationId** | A naplók erőforrások közötti összekapcsolásához használt azonosító. Például: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**helyen** | A Storage-fiók helye. Például: `North Europe`. |
|**protokoll**|A műveletben használt protokoll. Például: `HTTP` ,, `HTTPS` `SMB` , vagy`NFS`|
| **URI** | A kért egységes erőforrás-azonosító. Például: `http://myaccountname.blob.core.windows.net/cont1/blobname?timeout=10`. |

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>A művelet hitelesítésének módját leíró mezők

```json
{
    "identity": {
        "authorization": [
            {
                "action": "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                "principals": [
                    {
                        "id": "fde5ba15-4355-4223-b811-cccccccccccc",
                        "type": "User"
                    }
                ],
                "roleAssignmentId": "ecf75cb8-491c-4a25-ad6e-aaaaaaaaaaaa",
                "roleDefinitionId": "b7e6dc6d-f1e8-4753-8033-ffffffffffff"
            }
        ],
        "requester": {
            "appId": "691458b9-1327-4635-9f55-bbbbbbbbbbbb",
            "audience": "https://storage.azure.com/",
            "objectId": "fde5ba15-4355-4223-b811-cccccccccccc",
            "tenantId": "72f988bf-86f1-41af-91ab-dddddddddddd",
            "tokenIssuer": "https://sts.windows.net/72f988bf-86f1-41af-91ab-eeeeeeeeeeee/"
           },
        "type": "OAuth"
    },
}

```

| Tulajdonság | Leírás |
|:--- |:---|
|**identitás/típus** | A kérelem elvégzéséhez használt hitelesítés típusa. Például: `OAuth` ,, `SAS Key` `Account Key` , vagy`Anonymous` |
|**identitás/tokenHash**|Ez a mező csak belső használatra van fenntartva. |
|**engedélyezés/művelet** | A kérelemhez rendelt művelet. Például: `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
|**engedélyezés/roleAssignmentId** | A szerepkör-hozzárendelés azonosítója. Például: `4e2521b7-13be-4363-aeda-111111111111`.|
|**engedélyezés/roleDefinitionId** | A szerepkör-definíció azonosítója. Például: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**rendszerbiztonsági tag/azonosító** | A rendszerbiztonsági tag azonosítója. Például: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**rendszerbiztonsági tag/típus** | A rendszerbiztonsági tag típusa. Például: `ServicePrincipal`. |
|**kérelmező/appID** | A kérelmezőként használt Open Authorization-(OAuth-) alkalmazás azonosítója. <br> Például: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**kérelmező/célközönség** | A kérelem OAuth célközönsége. Például: `https://storage.azure.com`. |
|**kérelmező/objectId** | A kérelmező OAuth-objektumának azonosítója. Kerberos-hitelesítés esetén a a Kerberos által hitelesített felhasználó objektumazonosítót jelöli. Például: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**kérelmező/tenantId** | Az identitás OAuth-bérlő azonosítója. Például: `72f988bf-86f1-41af-91ab-222222222222`.|
|**kérelmező/tokenIssuer** | Az OAuth jogkivonat kiállítója. Például: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**kérelmező/UPN** | A kérelmező egyszerű felhasználóneve (UPN). Például: `someone@contoso.com`. |
|**kérelmező/Felhasználónév** | Ez a mező csak belső használatra van fenntartva.|

### <a name="fields-that-describe-the-service"></a>A szolgáltatást leíró mezők

```json
{
    "properties": {
        "accountName": "testaccount1",
        "requestUrl": "https://testaccount1.blob.core.windows.net:443/upload?restype=container&comp=list&prefix=&delimiter=%2F&marker=&maxresults=30&include=metadata&_=1551405598426",
        "userAgentHeader": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
        "referrerHeader": "blob:https://ms.portal.azure.com/6f50025f-3b88-488d-b29e-3c592a31ddc9",
        "clientRequestId": "",
        "etag": "",
        "serverLatencyMs": 63,
        "serviceType": "blob",
        "operationCount": 0,
        "requestHeaderSize": 2658,
        "requestBodySize": 0,
        "responseHeaderSize": 295,
        "responseBodySize": 2018,
        "contentLengthHeader": 0,
        "requestMd5": "",
        "serverMd5": "",
        "lastModifiedTime": "",
        "conditionsUsed": "",
        "smbTreeConnectID" : "0x3",
        "smbPersistentHandleID" : "0x6003f",
        "smbVolatileHandleID" : "0xFFFFFFFF00000065",
        "smbMessageID" : "0x3b165",
        "smbCreditsConsumed" : "0x3",
        "smbCommandDetail" : "0x2000 bytes at offset 0xf2000",
        "smbFileId" : " 0x9223442405598953",
        "smbSessionID" : "0x8530280128000049",
        "smbCommandMajor" : "0x6",
        "smbCommandMinor" : "DirectoryCloseAndDelete"
    }

}
```

| Tulajdonság | Leírás |
|:--- |:---|
|**accountName** | A Storage-fiók neve. Például: `mystorageaccount`.  |
|**requestUrl** | A kért URL-cím. Például: `http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10`.|
|**userAgentHeader** | A **felhasználói ügynök fejlécének** értéke idézőjelek között. Például: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`.|
|**referrerHeader** | A **hivatkozó** fejléc értéke. Például: `http://contoso.com/about.html`.|
|**ügyfélkérelem** | A kérelem **x-MS-Client-Request-ID** fejlécének értéke. Például: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`. |
|**ETAG** | A visszaadott objektum ETag azonosítója az idézőjelek között. Például: `0x8D101F7E4B662C4`.  |
|**serverLatencyMs** | A kért művelet végrehajtásához szükséges ezredmásodpercben kifejezett teljes idő. Ez az érték nem tartalmazza a hálózati késést (a bejövő kérelem beolvasásának idejét és a válasz küldését a kérelmezőnek). Például: `22`. |
|**serviceType** | A kérelemhez társított szolgáltatás. Például: `blob` ,, `table` `files` , vagy `queue` . |
|**operationCount** | A kérelemben érintett összes naplózott művelet száma. Ez a szám a indexével kezdődik `0` . Egyes kérelmeknél több műveletre van szükség, például egy blob másolására irányuló kérelemre. A legtöbb kérelem csak egy műveletet hajt végre. Például: `1`. |
|**requestHeaderSize** | A kérelem fejlécének mérete bájtban kifejezve. Például: `578`. <br>Ha egy kérelem sikertelen, ez az érték üres is lehet. |
|**requestBodySize** | A tárolási szolgáltatás által beolvasott kérési csomagok mérete bájtban kifejezve. <br> Például: `0`. <br>Ha egy kérelem sikertelen, ez az érték üres is lehet.  |
|**responseHeaderSize** | A válasz fejlécének mérete bájtban kifejezve. Például: `216`. <br>Ha egy kérelem sikertelen, ez az érték üres is lehet.  |
|**responseBodySize** | A tárolási szolgáltatás által írt válasz csomagok mérete bájtban megadva. Ha egy kérelem sikertelen, ez az érték üres is lehet. Például: `216`.  |
|**requestMd5** | A kérelemben a **Content-MD5** fejléc vagy az **x-MS-Content-MD5** fejléc értéke. Az ebben a mezőben megadott MD5 kivonatoló érték a kérelemben szereplő tartalmat jelöli. Például: `788815fd0198be0d275ad329cafd1830`. <br>Ez a mező üres lehet.  |
|**serverMd5** | A tárolási szolgáltatás által kiszámított MD5-kivonat értéke. Például: `3228b3cf1069a5489b298446321f8521`. <br>Ez a mező üres lehet.  |
|**lastModifiedTime** | A visszaadott objektum utolsó módosításának időpontja (LMT).  Például: `Tuesday, 09-Aug-11 21:13:26 GMT`. <br>Ez a mező üres olyan műveletekhez, amelyek több objektumot adhatnak vissza. |
|**conditionsUsed** | A feltételt képviselő kulcs-érték párok pontosvesszővel tagolt listája. A feltételek a következők lehetnek: <li> If-Modified-Since <li> Ha – nem módosítva – óta <li> If-Match <li> If-None-Match  <br> Például: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`. |
|**contentLengthHeader** | A tárolási szolgáltatásnak továbbított kérelem Content-Length fejlécének értéke. Ha a kérelem sikeres volt, ez az érték egyenlő a requestBodySize. Ha egy kérelem sikertelen, az érték nem lehet egyenlő a requestBodySize, vagy üres is lehet. |
|**tlsVersion** | A kérelem összekapcsolásakor használt TLS-verzió. Például: `TLS 1.2`. |
|**smbTreeConnectID** | A kiszolgáló-üzenetblokk (SMB) **treeConnectId** a fa csatlakozási idején lett létrehozva. Például: `0x3` |
|**smbPersistentHandleID** | Állandó leíró azonosító egy olyan SMB2-LÉTREHOZÁSi kérelemből, amely túléli a hálózat újrakapcsolását.  Az [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 hivatkozik **SMB2_FILEID. Állandó**. Például: `0x6003f` |
|**smbVolatileHandleID** | A SMB2-LÉTREHOZÁSi kérelem által a hálózat újrakapcsolásakor újrahasznosított ideiglenes leíró azonosítója.  Az [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 hivatkozik **SMB2_FILEID. Változékony**. Például: `0xFFFFFFFF00000065` |
|**smbMessageID** | A kapcsolatok relatív **MessageID**. Például: `0x3b165` |
|**smbCreditsConsumed** | A kérelem által felhasznált bejövő vagy kimenő forgalom 64 kilobájt egységben. Például: `0x3` |
|**smbCommandDetail** | További információ erről a kérésről az általános típus helyett. Például: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | A fájlhoz vagy könyvtárhoz társított **FileId** .  Nagyjából hasonló az NTFS-FileId. Például: `0x9223442405598953` |
|**smbSessionID** | A munkamenet-beállítási időpontban létesített SMB2 **munkamenet** -azonosító. Például: `0x8530280128000049` |
|**smbCommandMajor UInt32** | Érték a **SMB2_HEADER. parancsban**. Jelenleg ez egy 0 és 18 közötti szám. Például: `0x6` |
|**smbCommandMinor** | A **SmbCommandMajor**alosztálya, ahol szükséges. Például: `DirectoryCloseAndDelete` |

## <a name="see-also"></a>További információ

- Az Azure Storage figyelésének leírását lásd: az [Azure Storage figyelése](monitor-storage.md) .
- Az Azure-erőforrások figyelésével kapcsolatos részletekért lásd: az [Azure-erőforrások figyelése Azure monitorokkal](../../azure-monitor/insights/monitor-azure-resource.md) .