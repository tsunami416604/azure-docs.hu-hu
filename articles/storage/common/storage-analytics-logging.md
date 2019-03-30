---
title: Az Azure Storage Analytics naplózása
description: Megtudhatja, hogyan bejelentkezni az Azure Storage kérelmekre részleteit.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: 09a5a6d823240b724e6ec88de38df068a58982d9
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652059"
---
# <a name="azure-storage-analytics-logging"></a>Az Azure Storage analytics naplózása

A Storage Analytics naplók sikeres és sikertelen kérések kapcsolatos részletes információk egy tárolási szolgáltatásba. Ezt az információt a figyelheti az egyes kérések és a egy storage szolgáltatással kapcsolatos problémák diagnosztizálásához használható. Kérelmek van bejelentkezve a képességeinkhez mérten biztosítjuk.

 A Storage Analytics naplózási tárfiók alapértelmezés szerint nincs engedélyezve. Engedélyezheti a a [az Azure portal](https://portal.azure.com/); további információkért lásd: [monitorozása az Azure Portal tárfiók](/azure/storage/storage-monitor-storage-account). A Storage Analytics automatizáltan a REST API-t vagy az ügyféloldali kódtár is engedélyezheti. Használja a [beolvasása a Blob szolgáltatás tulajdonságai](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API), [lekérése a Queue szolgáltatás tulajdonságai](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties), és [tábla szolgáltatástulajdonságok](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) minden egyes szolgáltatás a Storage Analytics engedélyezésének műveletei.

 Naplóbejegyzések jönnek létre, csak ha a szolgáltatásvégpont kérelmekre. Például ha a tárfiók a Blob végpontja, de nem a tábla vagy üzenetsor végpontjait, csak a Blob szolgáltatáshoz tartozó naplók létrejön.

> [!NOTE]
>  A Storage Analytics naplózási jelenleg csak a Blob, Queue és Table szolgáltatások érhető el. Premium storage-fiók azonban nem támogatott.

## <a name="requests-logged-in-logging"></a>Naplózott kérések naplózása
### <a name="logging-authenticated-requests"></a>Hitelesített naplózást kérelmek

 A következő típusú hitelesített kérelmeket naplózza:

- Sikeres kérelmek
- Sikertelen kérelmek, beleértve az időkorlát, a szabályozás, hálózati, engedélyezési és egyéb hibák
- A közös hozzáférésű Jogosultságkód (SAS) vagy az OAuth, beleértve a sikeres és sikertelen kérések-kérések
- Elemzési adatok kérelmeket

  Kérést hoz létre a Storage Analytics, például a napló a létrehozás vagy törlés, a program nem naplózza. A naplózott adatok teljes listáját leírása itt található a [Storage Analytics naplózott műveletek és az állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) és [Storage Analytics naplóformátum](/rest/api/storageservices/storage-analytics-log-format) témaköröket.

### <a name="logging-anonymous-requests"></a>Névtelen kérések naplózása

 A következő típusú névtelen kérésekkel jelentkezett:

- Sikeres kérelmek
- Kiszolgálóhibák
- Az ügyfél és kiszolgáló egyaránt időtúllépési hibákat
- Sikertelen GET kérelmek 304 (nem módosított). Hibakód:

  Az összes többi sikertelen névtelen kérelmek nem jelentkezett. A naplózott adatok teljes listáját leírása itt található a [Storage Analytics naplózott műveletek és az állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) és [Storage Analytics naplóformátum](/rest/api/storageservices/storage-analytics-log-format) témaköröket.

## <a name="how-logs-are-stored"></a>Naplók tárolási módját

Az összes napló blokkblobok nevű tárolóban vannak tárolva `$logs`, amely automatikusan létrejön, amikor a Storage Analytics engedélyezve van a storage-fiók. A `$logs` tárolóban található a tárfiók blob névtér például: `http://<accountname>.blob.core.windows.net/$logs`. Ez a tároló nem lehet törölni a Storage Analytics engedélyezése után tartalmának törlése, ha. A storage-böngészés eszköz segítségével keresse meg a tároló közvetlenül, ha látni fogja, hogy a naplózási adatokat tartalmazó összes BLOB.

> [!NOTE]
>  A `$logs` tároló nem jelenik meg, amikor egy tároló listázása művelet hajtja végre, például a lista tárolók műveletet. Azt kell elérni közvetlenül. Például használhatja a Blobok listázása művelet eléréséhez a blobok a `$logs` tároló.

Kérés naplózásra kerül, mint a Storage Analytics köztes eredményeket fogja feltöltése blokkolja. Rendszeres időközönként a Storage Analytics ezeket adatblokkokat véglegesítése és elérhetővé teheti őket blobként. Is igénybe vehet egy naplózási adatai megjelennek a blobok az órát a **$logs** tároló mert a gyakoriságot, a storage szolgáltatás a log írók kiürítéseinek száma. Ismétlődő rekordok léteznek előfordulhat, hogy az adott órán létrehozott naplók. Segítségével meghatározhatja, hogy ha egy rekord ellenőrzésével többször szerepel a **kérelemazonosító** és **művelet** számát.

Ha nagy mennyiségű, több fájlt a naplóadatok esetében minden egyes óráért, majd használhatja a blob metaadatait a napló tartalmazza a blob metaadatainak mezők megvizsgálásával adatok meghatározásához. Ez akkor is hasznos, mert egyes esetekben lehet késleltetés közben a naplófájlok írt adatok: a blob metaadatai, lehetővé teszi egy pontosabb arra utalhat, hogy a blob tartalma, mint a blob nevét.

A legtöbb tárolási böngészési eszközei lehetővé teszik a blobok; metaadatainak megtekintése Ez az információ PowerShell-lel is olvashatja vagy programozott módon. Az alábbi PowerShell-kódrészlettel példaként szolgál a szűrés a naplózási blobok listáját, az időpontnak a megadása név szerint, és csak ezeket a naplókat tartalmazó azonosításához metaadatok **írási** műveleteket.  

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

Blobok listázása programozott módon kapcsolatos információkért lásd: [Blob erőforrások számbavétele](http://msdn.microsoft.com/library/azure/hh452233.aspx) és [beállítás és a Tulajdonságok beolvasásakor és a Blob-erőforrások metaadatokat](http://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Napló elnevezési konvenciók

 Minden napló lesz írva a következő formátumban:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 Az alábbi táblázat ismerteti az egyes tulajdonságokat a napló neve:

|Attribútum|Leírás|
|---------------|-----------------|
|`<service-name>`|A storage szolgáltatás neve. Például: `blob`, `table`, vagy `queue`|
|`YYYY`|A napló a négyjegyű év. Például:`2011`|
|`MM`|A napló hónap kétjegyű. Például:`07`|
|`DD`|A napló kétjegyű napja. Például:`31`|
|`hh`|A két számjegyű óra, amely azt jelzi, hogy a naplók a kezdő óra 24 órás UTC formátumban. Például:`18`|
|`mm`|A két jegyű számot, amely azt jelzi, hogy a kiindulási perc, a naplókhoz. **Megjegyzés:**  A Storage Analytics jelenlegi verziója nem támogatja ezt az értéket, és az érték mindig lesz `00`.|
|`<counter>`|Egy nulla-alapú számláló 6 számjegyű, amely azt jelzi, hogy a storage szolgáltatás egy órás időszakban létrehozott naplóblobok száma. Ez a számláló kezdődik, `000000`. Például:`000001`|

 Az alábbiakban látható egy teljes példa a neve, amely egyesíti a fenti példák:

 `blob/2011/07/31/1800/000001.log`

 Az alább látható egy minta URI-t a fenti napló elérésére használható:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Egy tárolási kérés naplózásakor az óra, ha a kért művelet utal. az eredményül kapott napló nevét. Például ha GetBlob kérelmet, 6:30 -kor 7/31/2011 befejeződött, a napló tartalmazná a következő előtaggal: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Napló metaadatai

 Összes naplózási blobok metaadatait, mely tartalmazza a blob naplózási adatok azonosítására szolgáló együtt vannak tárolva. Az alábbi táblázat ismerteti az egyes metaadatok tulajdonságokat:

|Attribútum|Leírás|
|---------------|-----------------|
|`LogType`|Ismerteti, hogy a napló tartalmazza-e olvasási, írási vagy törlési műveletek kapcsolatos információk. Ez az érték tartalmazhat egy vagy három, vesszővel elválasztva kombinációja.<br /><br /> 1. példa: `write`<br /><br /> 2. példa: `read,write`<br /><br /> 3. példa: `read,write,delete`|
|`StartTime`|A legkorábbi időpontot, amikor egy bejegyzést a naplóban formájában `YYYY-MM-DDThh:mm:ssZ`. Például:`2011-07-31T18:21:46Z`|
|`EndTime`|A napló formájában bejegyzés legújabb idején `YYYY-MM-DDThh:mm:ssZ`. Például:`2011-07-31T18:22:09Z`|
|`LogVersion`|A napló formátuma verziója.|

 A következő lista azoknak a teljes minta metaadatait a fenti példák:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Storage-naplózás engedélyezése

Az Azure portal, PowerShell és a Storage SDK-kkal tárolási naplózás engedélyezésével.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Tárolási naplózásának engedélyezése az Azure portal használatával  

Használja az Azure Portalon a **diagnosztikai beállítások (klasszikus)** tárolási naplózás, elérhető-e a panel a **figyelés (klasszikus)** egy storage-fiók szakaszban **menü panel** .

A naplózni kívánt tárolási szolgáltatások és a megőrzési időszak (nap) a naplózott adatok is megadhat.  

### <a name="enable-storage-logging-using-powershell"></a>PowerShell-lel tárolási naplózás engedélyezése  

 Ön PowerShell a helyi gépen való konfigurálásához használható tárolási naplózás a tárfiókban lévő Azure PowerShell-parancsmag használatával **Get-AzureStorageServiceLoggingProperty** lekérni az aktuális beállítások és a parancsmag  **Set-AzureStorageServiceLoggingProperty** az aktuális beállítások módosításához.  

 A parancsmagok, amelyek vezérlik a Storage-naplózás használata a **LoggingOperations** paraméter, amely kéréstípusok bejelentkezni vesszővel tagolt listáját tartalmazó karakterlánc. A három lehetséges kérések típusa **olvasási**, **írási**, és **törlése**. Váltás kikapcsolhatják a naplózást, használja az értéket **nincs** számára a **LoggingOperations** paraméter.  

 A következő parancsot a naplózás olvasási vált, írási és törlési kérelmek, a Queue szolgáltatás az alapértelmezett tárfiókot az öt nap megőrzési:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 A következő parancs vált kikapcsolhatják a naplózást az a table service alapértelmezett tárfiókban található:  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Működik az Azure-előfizetésében az Azure PowerShell-parancsmagjainak konfigurálása és használata az alapértelmezett tárfiók kiválasztása kapcsolatos információkért lásd: [Azure PowerShell telepítése és konfigurálása annak](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Tárolási programozott módon naplózás engedélyezése  

 Mellett az Azure Portalon vagy a Storage-naplózás az Azure PowerShell-parancsmagok használata esetén is használhatja az Azure Storage API-k közül. Ha például egy .NET-nyelv használata a Storage ügyféloldali kódtára használhatja.  

 Az osztályok **CloudBlobClient**, **CloudQueueClient**, és **CloudTableClient** összes rendelkezik metódusokkal például **SetServiceProperties** és **SetServicePropertiesAsync** hosszabb időt igénylő egy **ServiceProperties** paraméterként objektum. Használhatja a **ServiceProperties** objektum tárolási naplózás konfigurálása. Ha például a következő C# kódrészlet bemutatja, hogyan módosíthatja a Mi a naplózására akkor kerül sor, és a várakozási sor naplózása megőrzési időtartama:  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 Storage-naplózás konfigurálása egy .NET-nyelv használatával kapcsolatos további információkért lásd: [Storage ügyféloldali kódtár – referencia](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 Storage-naplózás a REST API-val konfigurálásával kapcsolatos általános információkért lásd: [engedélyezése és konfigurálása a Storage Analytics](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Töltse le a Storage-naplózás adatainak naplózása

 Megtekintése és elemzése a naplózási adatokat, le kell töltenie a blobok, amelyek tartalmazzák a naplóadatok is érdeklik a helyi számítógépre. Számos tárolási böngészési eszközei lehetővé teszik a blobok letöltéséhez a tárfiókból; az Azure-példány parancssori eszköz a megadott Azure Storage csapata is használhatja (**AzCopy**) letöltéséhez a naplózási adatokat.  

 Ellenőrizze, hogy letöltötte az Önt érdeklő naplóadatokat, valamint a tölt le az azonos naplóadatok egynél többször:  

-   Használja a dátum és idő elnevezési szabályait a blobokat tartalmazó naplóadatok nyomon követésére, blobok, amely már letöltötte elkerülése érdekében újból letöltheti a ugyanazokat az adatokat többször elemzés céljából.  

-   Naplóadatokat tartalmazó a blobokat a metaadatok segítségével azonosíthatja az adott időszakban, amelyhez a blob tartalmazza a naplózási adatokat, le kell töltenie a pontos blob azonosításához.  

> [!NOTE]
>  Az AzCopy az Azure SDK részét képezi, de mindig letöltheti a legújabb verziót [ https://aka.ms/AzCopy ](https://aka.ms/AzCopy). Alapértelmezés szerint a AzCopy telepítve van a mappában **C:\Program Files (x86) \Microsoft SDKs\Windows Azure\AzCopy**, és Ön hozzá kell adnia ezt a mappát az elérési úthoz előtt próbál meg egy parancssort vagy a PowerShell-ablakban futtassa az eszközt.  

 Az alábbi példa bemutatja, hogyan tölthet le a napló adatokat a queue szolgáltatás az díjtól 09 AM, 10 AM és 20, 2014. május 11: 00 óra. A **/S** paraméter hatására hozhat létre egy helyi mappastruktúra alapján a dátumok és időpontok a naplófájlnevekkel; az AzCopy a **/V** paramétert az AzCopy a részletes kimenet; létrehozására hatására a **/Y** paraméter AzCopy felülírja a helyi fájlok okoz. Cserélje le **< yourstorageaccount\>**  a storage-fiókot, és cserélje le a nevű **< yourstoragekey\>**  az a tárfiók kulcsára.  

```
AzCopy 'http://<yourstorageaccount>.blob.core.windows.net/$logs/queue'  'C:\Logs\Storage' '2014/05/20/09' '2014/05/20/10' '2014/05/20/11' /sourceKey:<yourstoragekey> /S /V /Y  
```  

 AzCopy is rendelkezik néhány hasznos paraméter hogyan beállítja az utolsó módosítás időpontja a letöltött fájlokkal vezérlőhöz, és hogy megpróbálja letölteni a fájlokat, amelyek régebbi vagy újabb, mint a helyi gépen már létező fájlokat. Is futtathatja, újraindítható üzemmódban. További részletek megtekintéséhez a Súgó futtatásával a **AzCopy /?** a parancs.  

 Példa bemutatja, hogyan töltse le a naplózási adatokat programozott módon, a következő blogbejegyzésben:: [Windows Azure Storage-naplózás: Tárolási kérelmek nyomon követése a naplók segítségével](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) , és keresse meg az oldalon a "DumpLogs" szó.  

 Ha letöltötte a naplózási adatokat, megtekintheti a naplóbejegyzéseket a fájlokat. Ezek a naplófájlok elválasztójellel tagolt szöveges formátum használata számú naplófájl olvasása eszközök képesek elemezni, többek között a Microsoft Message Analyzer (további információkért lásd: Útmutató a [figyelés felismerése és hibáinak elhárítása a Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)). A különböző eszközök eltérő berendezések a formázást, szűrése, rendezése, a Keresés a naplófájlok tartalmát ad rendelkezik. A Storage naplózási naplófájlformátum és a tartalom kapcsolatos további információkért lásd: [Storage Analytics naplóformátum](/rest/api/storageservices/storage-analytics-log-format) és [Storage Analytics naplózott műveletek és az állapotüzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>További lépések

* [Storage Analytics naplóformátum](/rest/api/storageservices/storage-analytics-log-format)
* [A Storage Analytics naplózott műveletek és a hibaállapot-üzenetek](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Storage Analytics Metrics (klasszikus)](storage-analytics-metrics.md)