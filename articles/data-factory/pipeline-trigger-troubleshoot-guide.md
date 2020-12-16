---
title: A folyamat-előkészítés és-eseményindítók hibáinak megoldása az ADF-ben
description: Az ADF-ben a folyamat-triggerekkel kapcsolatos hibák elhárításához különböző módszereket használhat
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: ed3728513820da9f4ef85d44cac983dc09c3fc7d
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97521832"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-adf"></a>A folyamat-előkészítés és-eseményindítók hibáinak megoldása az ADF-ben

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A folyamatfuttatás egy folyamat-végrehajtás egy példányát határozza meg az Azure Data Factoryben. Tegyük fel, hogy van egy olyan folyamata, amely a 8:00-es, 9:00-as és 10:00-es időpontban fut. Ebben az esetben a folyamat vagy a folyamat futtatásának három különálló futtatása van. Minden folyamathoz tartozik egy egyedi folyamatfuttatási azonosító. A futtatási azonosító egy GUID (globálisan egyedi azonosító), amely meghatározza, hogy az adott folyamat fusson.

A folyamatfuttatások példányosítása jellemzően az argumentumoknak a folyamatban meghatározott paraméterekhez történő továbbításával történik. A folyamatok manuálisan vagy egy eseményindító használatával hajthatók végre. A részletekért tekintse meg a [folyamat-végrehajtást és a Azure Data Factory eseményindítókat](concepts-pipeline-execution-triggers.md) .

## <a name="common-issues-causes-and-solutions"></a>Gyakori problémák, okok és megoldások

### <a name="pipeline-with-azure-function-throws-error-with-private-end-point-connectivity"></a>A folyamat és az Azure Function dob hibát jelez a privát végponti kapcsolattal
 
#### <a name="issue"></a>Probléma
Bizonyos kontextusokban az ADF és az Azure függvényalkalmazás fut egy privát végponton. Olyan folyamatot próbál beolvasni, amely együttműködik az Azure függvényalkalmazásával. Három különböző metódust próbált meg, de egy hibát ad vissza `Bad Request` , a másik két metódus visszatér `103 Error Forbidden` .

#### <a name="cause"></a>Ok 
Az ADF jelenleg nem támogatja az Azure függvényalkalmazáshez készült magánhálózati végpont-összekötőt. Ennek az az oka, hogy az Azure függvényalkalmazás elutasítja a hívásokat, mivel úgy lett konfigurálva, hogy csak privát kapcsolaton keresztül engedélyezze a kapcsolatokat.

#### <a name="resolution"></a>Feloldás
Létrehozhat egy **PrivateLinkService** típusú privát végpontot, és megadhatja a függvény alkalmazásának DNS-t, és a hálózatnak működnie kell.

### <a name="pipeline-run-is-killed-but-the-monitor-still-shows-progress-status"></a>A folyamat futása megszakadt, de a figyelő továbbra is megjeleníti a folyamat állapotát.

#### <a name="issue"></a>Probléma
Amikor megöl egy folyamat futását, a folyamat figyelése továbbra is a folyamat állapotát mutatja. Ez azért fordulhat elő, mert a böngésző gyorsítótárazza a problémát, és nem megfelelő szűrőket használ a figyeléshez.

#### <a name="resolution"></a>Feloldás
Frissítse a böngészőt, és alkalmazza a megfelelő szűrőket a figyeléshez.
 
### <a name="copy-pipeline-failure--found-more-columns-than-expected-column-count-delimitedtextmorecolumnsthandefined"></a>Másolási folyamat hibája – a várt oszlopoknál több oszlop található (DelimitedTextMoreColumnsThanDefined)

#### <a name="issue"></a>Probléma  
Ha a másolt mappában lévő fájlok különböző sémákkal (például változó számú oszlopokkal, különböző elválasztó karakterekkel, idézőjelekkel vagy valamilyen adatproblémával) rendelkeznek, akkor az ADF-folyamat véget ért a következő hibánál:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

#### <a name="resolution"></a>Feloldás
A Adatok másolása tevékenység létrehozásakor válassza a "bináris másolás" lehetőséget. Így az adatok tömeges másolása vagy áttelepítése az egyik Data Lakeról a másikra, **bináris** lehetőség használatával az ADF nem nyitja meg a fájlokat az olvasási sémának, de csak az összes fájlt bináris fájlként kezeli, és átmásolja azokat a másik helyre.

### <a name="pipeline-run-fails-when-capacity-limit-of-integration-runtime-is-reached"></a>A folyamat futtatása meghiúsul, ha elérte az integrációs modul kapacitásának korlátját

#### <a name="issue"></a>Probléma
Hibaüzenet:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

A hiba a jelenleg 50-os integrációs futtatókörnyezet korlátozását jelzi. További részletekért tekintse meg a [határértékeket](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#version-2) .

Ha egy időben nagy mennyiségű adatfolyamatot hajt végre ugyanazzal az integrációs futtatókörnyezettel, akkor az ilyen típusú hibákhoz vezethet.

#### <a name="resolution"></a>Feloldás 
- Elkülönítheti ezeket a folyamatokat a végrehajtás különböző elindítási idejére.
- Hozzon létre egy új integrációs modult, és ossza szét ezeket a folyamatokat több integrációs modul között.

### <a name="how-to-monitor-pipeline-failures-on-regular-interval"></a>Folyamat-meghibásodások figyelése rendszeres időközönként

#### <a name="issue"></a>Probléma
Gyakran van szükség az ADF-folyamatok időközönkénti figyelésére, azaz 5 percre. A folyamat a végpont használatával kérdezheti le és szűrheti a folyamat futtatását egy adatok előállítóján. 

#### <a name="recommendation"></a>Ajánlás
1. Állítson be egy Azure logikai alkalmazást, amely 5 percenként lekérdezi az összes sikertelen folyamatot.
2. Ezt követően az incidenseket bejelenthetjük a [QueryByFactory](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory).

#### <a name="reference"></a>Referencia
- [Külső – értesítések küldése az ADF-ből](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/)

### <a name="how-to-handle-activity-level-errors-and-failures-in-pipelines"></a>Tevékenység-szintű hibák és hibák kezelése a folyamatokban

#### <a name="issue"></a>Probléma
Azure Data Factory a koordinálás lehetővé teszi a feltételes logikát, és lehetővé teszi, hogy a felhasználó a korábbi tevékenységek eredményei alapján különböző útvonalakat tegyen. Négy feltételes elérési utat tesz lehetővé: "a sikeres (alapértelmezett pass)", "sikertelen", "Befejezés után" és "a kihagyás után". A különböző elérési utak használata engedélyezett.

Azure Data Factory a folyamat sikeres és sikertelen működésének meghatározása a következőképpen történik:

- Kiértékeli az összes levél szintű tevékenység eredményét. Ha a levél tevékenység ki lett hagyva, a fölérendelt tevékenységet kiértékeljük.
- A folyamat eredménye sikeres, és csak akkor, ha az összes művelet sikeres.

#### <a name="recommendation"></a>Ajánlás
- A tevékenységek szintjének ellenőrzésének végrehajtása [a folyamat hibáinak és hibáinak kezelése](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459)után.
- Az Azure Logic App használatával rendszeres időközönként figyelheti a folyamatokat a [DataFactory által végzett lekérdezés]( https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory)után.

## <a name="next-steps"></a>További lépések

További hibaelhárítási segítségért próbálja ki ezeket az erőforrásokat:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&egy kérdés oldal](/answers/topics/azure-data-factory.html)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)