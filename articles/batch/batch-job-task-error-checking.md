---
title: Feladat- és feladathibák ellenőrzése – Azure Batch | Microsoft dokumentumok
description: Feladatok és feladatok keresésének és hibaelhárításának hibái
services: batch
author: mscurrell
ms.service: batch
ms.topic: article
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: 4ace0de6d252680eb64990277b9478adf752f54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087015"
---
# <a name="job-and-task-error-checking"></a>Feladat- és tevékenységhiba-ellenőrzés

Különböző hibák léphetnek fel feladatok és feladatok hozzáadásakor. Ezeka műveletek hibáinak észlelése egyszerű, mert az API, a CLI vagy a felhasználói felület azonnal visszaadja a hibákat.  Vannak azonban olyan hibák, amelyek később fordulhatnak elő, amikor a feladatok és feladatok ütemezése és futtatása történik.

Ez a cikk a feladatok és feladatok elküldése után előforduló hibákat ismerteti. Felsorolja és elmagyarázza azokat a hibákat, amelyeket ellenőrizni és kezelni kell.

## <a name="jobs"></a>Feladatok

A feladat egy vagy több tevékenység csoportosítása, a futtatandó parancssorokat ténylegesen meghatározó feladatok.

Feladat hozzáadásakor a következő paraméterek adhatók meg, amelyek befolyásolhatják a feladat sikertelensítését:

- [Feladatmegkötések](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - A `maxWallClockTime` tulajdonság tetszés szerint megadható, hogy a feladat legfeljebb mennyi ideig lehet aktív vagy futó. Ha túllépi, a feladat megszűnik `terminateReason` a feladat [executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob) tulajdonságában beállított tulajdonsággal.
- [Feladat-előkészítési feladat](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - Ha meg van adva, a feladat-előkészítési feladat futtatása az első alkalommal fut egy feladat egy csomóponton. A feladat-előkészítési feladat sikertelen lehet, ami azt eredményezi, hogy a feladat nem fut, és a feladat nem fut.
- [Feladat kiadási feladata](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - Feladatkiadási feladat csak akkor adható meg, ha van beállítva egy feladat-előkészítési feladat. Egy feladat leállításakor a feladatkiadási feladat azon a készletcsomóponton fut, ahol egy feladat-előkészítési feladatot futtattak. Egy feladatkiadási feladat sikertelen lehet, de a `completed` feladat továbbra is állapotba kerül.

### <a name="job-properties"></a>Feladat tulajdonságai

A következő feladattulajdonságokat kell ellenőrizni a hibákat:

- '[executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)':
  - A `terminateReason` tulajdonság értékei jelezhetik, hogy a `maxWallClockTime`feladatmegkötésekben megadott értékek túllépték a feladatot, ezért a feladat megszakadt. Azt is beállíthatja, hogy jelezze a `onTaskFailure` feladat sikertelen, ha a feladat tulajdonság megfelelően van beállítva.
  - Az [schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) tulajdonság ütemezési hiba esetén van beállítva.
 
### <a name="job-preparation-tasks"></a>Feladat-előkészítési feladatok

Ha egy feladat-előkészítési feladat meg van adva egy feladathoz, akkor a feladat egy példánya akkor fog futni, amikor a feladat egy csomóponton fut. A feladatra konfigurált feladat-előkészítési feladat feladatsablonnak tekinthető, több feladat-előkészítési feladatpéldány futtatásával, a készletben lévő csomópontok számáig.

A feladat-előkészítési feladat példányait ellenőrizni kell annak megállapításához, hogy voltak-e hibák:
- A feladat-előkészítési feladat futtatásakor a feladat-előkészítési feladatot `preparing`kiváltó feladat a ( feladat-előkészítési feladat) [állapotba](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) kerül. ha a feladat-előkészítési feladat ezután sikertelen `active` lesz, az indító feladat visszaáll az állapotra, és nem fog futni.  
- A feladat-előkészítési feladat összes futtatott példánya beszerezhető a feladatból a [Lista előkészítése és a Feladat feloldása állapota](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) API használatával. Mint minden feladatnál, a [végrehajtási információk](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) `failureInfo`is `exitCode`rendelkezésre állnak a tulajdonságaival, például a , és `result`a.
- Ha a feladat-előkészítési feladatok sikertelenek, akkor az kiváltó feladatfeladatok nem futnak, a feladat nem fejeződik be, és beragad. A készlet nem használható, ha nincs más feladatok feladatokat lehet ütemezni.

### <a name="job-release-tasks"></a>Feladatkiadási feladatok

Ha egy feladatkiadási feladat meg van adva egy feladathoz, akkor egy feladat leállításakor a feladatkiadási feladat egy példánya fut minden olyan készletcsomóponton, ahol a feladat-előkészítési feladatot futtatták.  A feladatkiadási feladatpéldányokat ellenőrizni kell annak megállapításához, hogy voltak-e hibák:
- A futtatott feladatkiadási feladat összes példánya beszerezhető a feladatból az [API-lista előkészítése és](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus)a feladat feloldása állapota használatával. Mint minden feladatnál, a [végrehajtási információk](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) `failureInfo`is `exitCode`rendelkezésre állnak a tulajdonságaival, például a , és `result`a.
- Ha egy vagy több feladatkiadási feladat sikertelen, akkor a feladat `completed` továbbra is leáll, és állapotba kerül.

## <a name="tasks"></a>Feladatok

A feladatfeladatok több okból is sikertelenek lehetnek:

- A feladat parancssora sikertelen, és nem nulla kilépési kóddal tér vissza.
- Egy `resourceFiles` feladathoz meg vannak adva, de hiba történt, ami azt jelentette, hogy egy vagy több fájl nem lett letöltve.
- Egy `outputFiles` feladathoz meg vannak adva, de hiba történt, ami azt jelentette, hogy egy vagy több fájl nem lett feltöltve.
- Túllépte a tevékenység eltelt idejét, amelyet a tulajdonság a `maxWallClockTime` [tevékenységkorlátokban](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)megadott.

Minden esetben ellenőrizni kell a következő tulajdonságokat a hibák és a hibákkal kapcsolatos információk szempontjából:
- A feladatok [executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) tulajdonsága több olyan tulajdonságot tartalmaz, amelyek információt nyújtanak egy hibáról. [az eredmény](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) azt jelzi, hogy a `exitCode` feladat `failureInfo` bármilyen okból sikertelen volt-e, és további információt nyújt a hibáról.
- A feladat mindig az `completed` [állapotba](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate)kerül, függetlenül attól, hogy sikeres vagy sikertelen volt.The task will always move to the state , függetlenül attól, hogy sikerült vagy sikertelen.

Figyelembe kell venni a tevékenységhibák nak a feladatra és a tevékenységfüggőségre gyakorolt hatását.  Az [exitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) tulajdonság megadható egy feladathoz, amely konfigurálegy műveletet a függőségek és a feladat számára.
- Függőségek esetén a [DependencyAction](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) azt szabályozza, hogy a sikertelen tevékenységtől függő tevékenységek le vannak-e tiltva vagy futnak-e.
- A feladatnál a JobAction azt [szabályozza,](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) hogy a sikertelen feladat a feladat letiltásához, leállításához vagy változatlanul maradó állapotához vezet-e.

### <a name="task-command-line-failures"></a>Feladat parancssori hibái

A feladat parancssorának futtatásakor `stderr.txt` a `stdout.txt`kimenet a és a ba kerül. Ezenkívül az alkalmazás írhat alkalmazásspecifikus naplófájlokba is.

Ha a készletcsomópont, amelyen a feladat futott, még mindig létezik, akkor a naplófájlok beszerezhetők és megtekinthetők. Az Azure Portal például felsorolja és megtekintheti egy feladat vagy egy készletcsomópont naplófájljait. Több API-k is lehetővé teszik a feladatfájlok felsorolását és beszerzését, például a [Get From Task](https://docs.microsoft.com/rest/api/batchservice/file/getfromtask).

Mivel a készletek és a készletcsomópontok gyakran mulandóak, a csomópontok folyamatos hozzáadása és törlése miatt ajánlott a naplófájlok megőrzése. [A feladatkimeneti fájlok](https://docs.microsoft.com/azure/batch/batch-task-output-files) kényelmesen menthetik a naplófájlokat az Azure Storage-ba.

### <a name="output-file-failures"></a>Kimeneti fájlhibák
A Batch minden fájlfeltöltéskor két naplófájlt ír `fileuploadout.txt` a `fileuploaderr.txt`számítási csomópontra, és . Ezeket a naplófájlokat megvizsgálhatja, ha többet szeretne megtudni egy adott hibáról. Azokban az esetekben, amikor a fájl feltöltését soha nem kísérelték meg, például azért, mert maga a feladat nem futtatva nem tudott futni, akkor ezek a naplófájlok nem fognak létezni.  

## <a name="next-steps"></a>További lépések

Ellenőrizze, hogy az alkalmazás átfogó hibaellenőrzést hajt-e végre; kritikus fontosságú lehet a problémák azonnali észlelése és diagnosztizálása.
