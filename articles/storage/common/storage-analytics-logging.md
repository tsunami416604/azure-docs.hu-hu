---
title: Azure Storage Analytics-naplózás
description: Ismerje meg, hogyan naplózhat részleteket az Azure Storage-on végrehajtott kérelmekről.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.openlocfilehash: 5b94a97f1286e1273300014e4eef140be412436b
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637182"
---
# <a name="azure-storage-analytics-logging"></a>Azure Storage Analytics-naplózás

A Storage Analytics naplózza a tárolási szolgáltatásoknak elküldött sikeres és sikertelen kérelmek részletes információit. Ezekkel az információkkal monitorozhatók az egyes kérelmek és diagnosztizálhatók a tárolási szolgáltatások problémái. A kérelmek naplózása a legjobb erőfeszítés alapján történik.

 A Storage Analytics-naplózás alapértelmezés szerint nincs engedélyezve a Storage-fiókjához. Az [Azure Portalon](https://portal.azure.com/)engedélyezheti; További információt a [Tárfiók figyelése az Azure Portalon című témakörben talál.](/azure/storage/storage-monitor-storage-account) A Storage Analytics programozott módon is engedélyezhető a REST API-n vagy az ügyfélkódtáron keresztül. Használja a [Blob szolgáltatás tulajdonságainak begete](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API), [A várólista-szolgáltatás tulajdonságainak beszereznie](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties)és [a Table Service Properties műveleteket](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) az egyes szolgáltatások storage-elemzések engedélyezéséhez.

 A naplóbejegyzések csak akkor jönnek létre, ha a szolgáltatás végpontjának kérései vannak. Ha például egy tárfiók rendelkezik tevékenységa a Blob végpont, de nem a tábla vagy a várólista végpontok, csak a Blob szolgáltatáshoz tartozó naplók jönnek létre.

> [!NOTE]
>  A Storage Analytics-naplózás jelenleg csak a Blob, a Queue és a Table szolgáltatás esetében érhető el. A prémium szintű tárfiókok azonban nem támogatottak.

## <a name="requests-logged-in-logging"></a>Naplózásba naplózott kérelmek
### <a name="logging-authenticated-requests"></a>Hitelesített kérelmek naplózása

 A hitelesített kérések alábbi típusai vannak rögzítve:

- Sikeres kérelmek
- Sikertelen kérések, köztük az időtúllépések, torlódások, valamint a hálózati, hitelesítési és egyéb hibák
- Közös hozzáférésű jogosultságkódot vagy OAuth-hitelesítést használó sikertelen és sikeres kérések
- Elemzési adatokhoz kapcsolódó kérelmek

  A Storage Analytics által tett kérelmek, például a napló létrehozása vagy törlése, nem kerülnek naplózásra. A naplózott adatok teljes listáját a [Storage Analytics naplózott műveletek és állapotüzenetek és](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a Storage Analytics log [format](/rest/api/storageservices/storage-analytics-log-format) témakörök dokumentálják.

### <a name="logging-anonymous-requests"></a>Névtelen kérelmek naplózása

 A rendszer a következő típusú névtelen kérelmeket naplózza:

- Sikeres kérelmek
- Kiszolgálóhibák
- Időbeli megtapadási hibák mind az ügyfél, mind a kiszolgáló esetében
- 304-es (Nincs módosítva) hibakóddal ellátott sikertelen GET-kérések

  Az összes többi sikertelen névtelen kérelem nem kerül naplózásra. A naplózott adatok teljes listáját a [Storage Analytics naplózott műveletek és állapotüzenetek és](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) a Storage Analytics log [format](/rest/api/storageservices/storage-analytics-log-format) témakörök dokumentálják.

## <a name="how-logs-are-stored"></a>A naplók tárolásának megóra

Az összes napló tanéven ként egy `$logs`tárolóban tárolóban tárolja a naplókat, amely automatikusan létrejön, ha a Storage Analytics engedélyezve van egy tárfiókhoz. A `$logs` tároló a tárfiók blobnévterében található, `http://<accountname>.blob.core.windows.net/$logs`például: . Ez a tároló nem törölhető, ha a Storage Analytics engedélyezve van, bár annak tartalma törölhető. Ha a tároló-böngészési eszköz segítségével közvetlenül a tárolóra navigál, látni fogja az összes blobot, amely tartalmazza a naplózási adatokat.

> [!NOTE]
>  A `$logs` tároló nem jelenik meg, ha egy tároló listaelemezési műveletet hajt végre, például a List Containers műveletet. Közvetlenül hozzá kell férni. Például a Lista blobok művelet segítségével elérheti a `$logs` blobok a tárolóban.

A kérelmek naplózása után a Storage Analytics köztes eredményeket tölt fel blokkokként. Rendszeres időközönként a Storage Analytics véglegesíti ezeket a blokkokat, és elérhetővé teszi őket blobként. Akár egy órát is igénybe vehet, amíg a naplóadatok megjelennek a **blobokban** a $logs tárolóban, mert a tárolási szolgáltatás kiüríti a naplóírók gyakoriságát. Az ugyanabban az órában létrehozott naplókhoz ismétlődő rekordok is létezhetnek. A **RequestId** és a **Művelet** szám ának ellenőrzésével meghatározhatja, hogy egy rekord ismétlődő-e.

Ha óránként több fájllal rendelkező naplóadatok nagy mennyisége van, akkor a blob metaadatai segítségével meghatározhatja, hogy a napló milyen adatokat tartalmaz a blob metaadat-mezőinek vizsgálatával. Ez azért is hasznos, mert néha késleltethető, amíg az adatok a naplófájlokba írásakor: a blob metaadatai pontosabban jelzik a blob tartalmát, mint a blob neve.

A legtöbb tárolóböngészési eszköz lehetővé teszi a blobok metaadatainak megtekintését; ezt az információt a PowerShell vagy programozott módon is elolvashatja. A következő PowerShell-kódrészlet egy példa a naplóblobok listájának név szerinti szűrésére egy időpont megadásához, valamint metaadatok alapján, hogy csak azokat a naplókat azonosítsa, amelyek **írási** műveleteket tartalmaznak.  

 ```powershell
 Get-AzureStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

A blobok programozott listázásáról a [Blob-erőforrások felsorolása,](https://msdn.microsoft.com/library/azure/hh452233.aspx) valamint [a Tulajdonságok és metaadatok beállítása a Blob-erőforrásokhoz](https://msdn.microsoft.com/library/azure/dd179404.aspx)című témakörben talál további információt.  

### <a name="log-naming-conventions"></a>Naplóelnevezési konvenciók

 Minden napló a következő formátumban lesz megírva:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 Az alábbi táblázat a naplónév ben szereplő egyes attribútumokat ismerteti:

|Attribútum|Leírás|
|---------------|-----------------|
|`<service-name>`|A tárolószolgáltatás neve. Például: `blob` `table`, , vagy`queue`|
|`YYYY`|A napló négyjegyű éve. Például:`2011`|
|`MM`|A napló kétjegyű hónapja. Például:`07`|
|`DD`|A napló kétjegyű napja. Például:`31`|
|`hh`|A naplók kezdési óráját jelző kétjegyű óra, 24 órás UTC formátumban. Például:`18`|
|`mm`|A naplók kezdőpercét jelző kétjegyű szám. **Megjegyzés:**  Ez az érték nem támogatott a Storage Analytics jelenlegi verziójában, és értéke mindig a. `00`|
|`<counter>`|Egy nulla-alapú számláló hat számjegy, amely azt jelzi, hogy a tárolási szolgáltatás hoz létre egy óra alatt létrehozott naplóblobok száma. Ez a `000000`számláló a kezdő helyen kezdődik. Például:`000001`|

 A következő egy teljes mintanapló név, amely egyesíti a fenti példákat:

 `blob/2011/07/31/1800/000001.log`

 A következő egy minta URI, amely a fenti napló eléréséhez használható:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 A tárolási kérelem naplózásakor az eredményül kapott naplónév korrelál a kért művelet befejezésének órájával. Ha például egy GetBlob-kérés 2011.07.31-én 6:30-kor befejeződött, a napló a következő előtaggal lesz megírva:`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Napló metaadatai

 Minden naplóblob metaadatokkal van tárolva, amelyek segítségével azonosíthatja, hogy a blob milyen naplózási adatokat tartalmaz. Az alábbi táblázat az egyes metaadat-attribútumokat ismerteti:

|Attribútum|Leírás|
|---------------|-----------------|
|`LogType`|Ez a témakör azt ismerteti, hogy a napló tartalmaz-e olvasási, írási vagy törlési műveletekre vonatkozó információkat. Ez az érték tartalmazhat egy típust vagy mindhárom kombinációját, vesszővel elválasztva.<br /><br /> 1. példa:`write`<br /><br /> 2. példa:`read,write`<br /><br /> 3. példa:`read,write,delete`|
|`StartTime`|A naplóban lévő bejegyzés legkorábbi időpontja `YYYY-MM-DDThh:mm:ssZ`. Például:`2011-07-31T18:21:46Z`|
|`EndTime`|A naplóban lévő bejegyzés legkésőbbi időpontja `YYYY-MM-DDThh:mm:ssZ`. Például:`2011-07-31T18:22:09Z`|
|`LogVersion`|A naplóformátum verziója.|

 Az alábbi lista a teljes mintametaadatokat jeleníti meg a fenti példák használatával:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Tárolónaplózás engedélyezése

Az Azure Portal, a PowerShell és a Storage SDK-k használatával engedélyezheti a storage-naplózást.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Storage-naplózás engedélyezése az Azure Portalon  

Az Azure Portalon a **Diagnosztikai beállítások (klasszikus)** panel segítségével szabályozhatja a storage-naplózást, amely a storage-fiók **Menü panelének** **figyelése (klasszikus)** szakaszából érhető el.

Megadhatja a naplózni kívánt tárolási szolgáltatásokat, valamint a naplózott adatok megőrzési időszakát (napokban).  

### <a name="enable-storage-logging-using-powershell"></a>Storage-naplózás engedélyezése a PowerShell használatával  

 A PowerShell a helyi gépen konfigurálhatja a storage-naplózás a tárfiókban az Azure PowerShell-parancsmag **get-AzureStorageServiceLoggingProperty** az aktuális beállítások lekéréséhez, és a **parancsmag Set-AzureStorageServiceLoggingProperty** az aktuális beállítások módosításához.  

 A tárolónaplózást vezérlő parancsmagok olyan **LoggingOperations** paramétert használnak, amely a naplózandó kérelemtípusok vesszővel tagolt listáját tartalmazó karakterlánc. A három lehetséges kérelemtípus az **olvasás**, **írás**és **törlés.** A naplózás kikapcsolásához használja a **LoggingOperations** paraméter **none** értékét.  

 A következő parancs bekapcsolja az olvasási, írási és törlési kérelmek naplózását az alapértelmezett tárfiók várólista szolgáltatásában, és a megőrzésöt öt napra állítja be:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 A következő parancs kikapcsolja a táblaszolgáltatás naplózását az alapértelmezett tárfiókban:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Az Azure PowerShell-parancsmagok Azure-előfizetéssel való együttműködésre történő konfigurálásáról, valamint a használandó alapértelmezett tárfiók kiválasztásáról a következő témakörben olvashat: [Az Azure PowerShell telepítése és konfigurálása.](https://azure.microsoft.com/documentation/articles/install-configure-powershell/)  

### <a name="enable-storage-logging-programmatically"></a>Tárolónaplózás engedélyezése programozott módon  

 Az Azure Portalon vagy az Azure PowerShell-parancsmagok használata mellett a storage-naplózás vezérléséhez is használhatja az Azure Storage API-k egyikét. Ha például .NET nyelvet használ, használhatja a Storage Client Library könyvtárat.  

 A **CloudBlobClient**, **CloudQueueClient**és **CloudTableClient** osztályok mindegyike rendelkezik olyan metódusokkal, mint például **a SetServiceProperties** és **a SetServicePropertiesAsync,** amelyek paraméterként a **ServiceProperties** objektumot veszik. A **ServiceProperties** objektum segítségével konfigurálhatja a tárolónaplózást. A következő C# kódrészlet például bemutatja, hogyan módosíthatja a naplózott adatokat, és hogyan lehet a várólista naplózásának megőrzési időszakát:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 A .NET nyelv tárolási naplózásának konfigurálásáról a [Storage Client Library Reference című témakörben talál](https://msdn.microsoft.com/library/azure/dn261237.aspx)további információt.  

 A tárolónaplózás REST API-val történő konfigurálásáról a [Storage Analytics engedélyezése és konfigurálása](https://msdn.microsoft.com/library/azure/hh360996.aspx)című témakörben talál általános tudnivalókat.  

## <a name="download-storage-logging-log-data"></a>Storage naplózási naplóadatainak letöltése

 A naplóadatok megtekintéséhez és elemzéséhez töltse le azokat a blobokat, amelyek az önt érdeklő naplóadatokat tartalmazzák egy helyi számítógépre. Számos tárolóböngészési eszköz lehetővé teszi blobok letöltését a tárfiókból; az Azure Storage által biztosított parancssori Azure Copy Tool [AzCopy](storage-use-azcopy-v10.md) parancssori használatával is letöltheti a naplóadatokat.  
 
>[!NOTE]
> A `$logs` tároló nincs integrálva az Event Griddel, így nem kap értesítést a naplófájlok írásakor. 

 Annak érdekében, hogy biztosan letöltsd az Önt érdeklő naplóadatokat, és ne töltsön le ugyanazokat a naplóadatokat egynél többször:  

-   Használja a dátum- és időelnevezési konvenciót a naplóadatokat tartalmazó blobok esetében, hogy nyomon követhesse, mely blobokat töltötte le elemzésre, hogy elkerülje ugyanazon adatok többszöri újraletöltését.  

-   A naplóadatokat tartalmazó blobok metaadatai segítségével azonosítsa azt az időszakot, amelyre vonatkozóan a blob naplóadatokat tartalmaz a letöltandó blob pontos azonosításához.  

Az AzCopy ismerkedése az [AzCopy első lépései: Az AzCopy ismerkedés](storage-use-azcopy-v10.md) 

A következő példa bemutatja, hogyan töltheti le a várólista-szolgáltatás naplóadatait a 2014 május 20-án 09,10 és 11:00 órától kezdődő órákra.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Ha többet szeretne tudni arról, hogyan tölthet le bizonyos fájlokat, olvassa [el A fájlok letöltése (Fájl letöltése) (Fájlok letöltése)](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-blobs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#download-specific-files)témakört.

A naplóadatok letöltése után megtekintheti a fájlok naplóbejegyzéseit. Ezek a naplófájlok olyan tagolt szövegformátumot használnak, amelyet számos naplóolvasó eszköz képes elemezni, beleértve a Microsoft Message Analyzer-t is (további információkért lásd a [Figyelés, diagnosztizálás és hibaelhárítás című témakört).](storage-monitoring-diagnosing-troubleshooting.md) A különböző eszközök különböző lehetőségeket tartalmaznak a naplófájlok formázásához, szűréséhez, rendezéséhez, hirdetéskereséséhez. A Storage Logging log fájlformátumáról és tartalmáról a [Storage Analytics naplóformátuma](/rest/api/storageservices/storage-analytics-log-format) és [a Tárolási elemzés naplózott műveletei és állapotüzenetei című témakörben](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)talál további információt.

## <a name="next-steps"></a>További lépések

* [Storage Analytics-napló formátuma](/rest/api/storageservices/storage-analytics-log-format)
* [Storage Analytics naplózott műveletek és állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Storage Analytics metrikák (klasszikus)](storage-analytics-metrics.md)
