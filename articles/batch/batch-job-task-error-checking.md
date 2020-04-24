---
title: Hibakeresés feladatokban és tevékenységekben
description: Hibák a feladatok és a feladatok kereséséhez és hibaelhárításához
author: mscurrell
ms.topic: article
ms.date: 03/10/2019
ms.author: markscu
ms.openlocfilehash: 0c58bdf50f3e69b2b7d18f750f94fecdb512af85
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82116026"
---
# <a name="job-and-task-error-checking"></a>Feladat-és feladattal kapcsolatos hibák ellenőrzése

A feladatok és a feladatok hozzáadásakor különböző hibák merülhetnek fel. A műveletekkel kapcsolatos hibák észlelése egyszerű, mert az API, a CLI vagy a felhasználói felület által azonnal visszaadott hibákat.  Vannak azonban olyan hibák, amelyek később megtörténhetnek a feladatok és a feladatok ütemezése és futtatása során.

Ez a cikk azokat a hibákat ismerteti, amelyek a feladatok és a feladatok elküldése után fordulnak elő. Felsorolja és ismerteti azokat a hibákat, amelyeket ellenőrizni és kezelni kell.

## <a name="jobs"></a>Feladatok

A feladat egy vagy több feladat csoportosítása, amelyek ténylegesen meghatározzák a futtatandó parancssorokat.

Feladat hozzáadásakor a következő paraméterek adhatók meg, amelyek befolyásolhatják a feladat sikertelenségét:

- [Feladatok megkötései](https://docs.microsoft.com/rest/api/batchservice/job/add#jobconstraints)
  - A `maxWallClockTime` tulajdonság beállítható úgy, hogy beállítsa azt a maximális időtartamot, ameddig a feladatok aktívak vagy futtathatók. Ha túllépi a műveletet, a rendszer megszakítja a `terminateReason` feladatot a feladatokhoz tartozó [executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#cloudjob) beállított tulajdonsággal.
- [Feladat-előkészítési feladat](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)
  - Ha meg van adva, a feladat-előkészítési feladat az első alkalommal fut, amikor egy feladatot futtatnak egy adott csomóponton. A feladat-előkészítési feladat meghiúsulhat, ami a nem futtatott feladat és a feladat befejezését eredményezi.
- [Feladat kiadási feladata](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)
  - Feladat-felszabadítási feladat csak akkor adható meg, ha a feladat-előkészítési feladat konfigurálva van. Ha egy feladat leáll, a feladat kiadására szolgáló feladat minden olyan készlet-csomóponton fut, amelyen a feladat-előkészítési feladat futott. A feladat kiadási feladatai sikertelenek lehetnek, de a feladat továbbra is `completed` állapotba kerül.

### <a name="job-properties"></a>Feladatok tulajdonságai

A következő feladatok tulajdonságainak ellenőrzése szükséges:

- "[executionInfo](https://docs.microsoft.com/rest/api/batchservice/job/get#jobexecutioninformation)":
  - A `terminateReason` tulajdonság tartalmazhat olyan értékeket, amelyek jelzik `maxWallClockTime`, hogy a, a feladatokra vonatkozó megkötésekben megadott érték túl lett lépve, ezért a rendszer megszakította a feladatot. Azt is beállíthatja, hogy egy feladat nem sikerült, ha a `onTaskFailure` feladat tulajdonság beállítása megfelelő volt.
  - A [schedulingError](https://docs.microsoft.com/rest/api/batchservice/job/get#jobschedulingerror) tulajdonság akkor van beállítva, ha ütemezési hiba történt.
 
### <a name="job-preparation-tasks"></a>Feladat-előkészítési feladatok

Ha egy feladathoz egy feladat-előkészítési feladat van megadva, akkor az adott feladat egy példánya lesz futtatva, amikor a feladathoz tartozó feladat először fut egy csomóponton. A feladathoz konfigurált feladat-előkészítési feladat sablonként is megtekinthető, és több feladat-előkészítési feladattal rendelkező példány fut a készletben lévő csomópontok számának megfelelően.

A feladat-előkészítési feladathoz tartozó példányokat ellenőrizni kell, hogy vannak-e hibák:
- Feladat-előkészítési feladat futtatásakor a feladat-előkészítési feladatot kiváltó feladat a következő [állapotba](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate) kerül: `preparing`; Ha a feladat-előkészítési feladat meghiúsul, az indító feladat visszaállítja az `active` állapotot, és nem fog futni.  
- A feladat-előkészítési feladat összes példánya beszerezhető a feladatból a [lista előkészítése és a kiadási feladat állapota](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus) API használatával. A feladatokhoz hasonlóan olyan [végrehajtási információk](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) is rendelkezésre állnak, mint a `failureInfo`, `exitCode`a és `result`a.
- Ha a feladat-előkészítési feladatok meghiúsulnak, akkor az aktiválási feladatok nem fognak futni, a feladat nem fejeződik be, és a rendszer elakad. Előfordulhat, hogy a készlet nem használható fel, ha nincsenek más feladatok ütemezhető feladatokkal.

### <a name="job-release-tasks"></a>Feladat kiadásával kapcsolatos feladatok

Ha egy feladat kiadási tevékenysége meg van adva egy feladathoz, akkor a feladat leállítása feladat egy példánya fut minden olyan készlet-csomóponton, amelyen a feladat-előkészítési feladat futott.  A feladat kiadási feladatának példányait ellenőrizni kell, hogy vannak-e hibák:
- A feladat-felszabadítási feladat futtatásának összes példánya a feladatból az API- [lista előkészítése és a kiadási feladat állapota](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus)alapján szerezhető be. A feladatokhoz hasonlóan olyan [végrehajtási információk](https://docs.microsoft.com/rest/api/batchservice/job/listpreparationandreleasetaskstatus#jobpreparationandreleasetaskexecutioninformation) is rendelkezésre állnak, mint a `failureInfo`, `exitCode`a és `result`a.
- Ha egy vagy több feladat-kiadási feladat meghiúsul, akkor a feladat továbbra is leáll, és a `completed` rendszer egy állapotba helyezi át.

## <a name="tasks"></a>Feladatok

A feladat feladatai több okból is meghiúsulnak:

- A feladat parancssora meghiúsul, és nem nulla kilépési kóddal tér vissza.
- Egy `resourceFiles` adott feladathoz van megadva, de hiba történt, mert egy vagy több fájl nem lett letöltve.
- Egy feladathoz van `outputFiles` megadva, de hiba történt, mert egy vagy több fájl nem lett feltöltve.
- A feladat `maxWallClockTime` [megkötések](https://docs.microsoft.com/rest/api/batchservice/task/add#taskconstraints)tulajdonságában megadott eltelt idő túllépve.

Minden esetben a következő tulajdonságokat kell ellenőrizni a hibákkal és a hibákkal kapcsolatos információkkal:
- A Tasks [executionInfo](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutioninformation) tulajdonság több olyan tulajdonságot tartalmaz, amelyek információt biztosítanak a hibáról. az [eredmény](https://docs.microsoft.com/rest/api/batchservice/task/get#taskexecutionresult) azt jelzi, hogy a feladat bármilyen okból meghiúsult-e, `exitCode` és `failureInfo` további információt biztosít a hibáról.
- A feladat mindig az `completed` [állapotba](https://docs.microsoft.com/rest/api/batchservice/task/get#taskstate)kerül, függetlenül attól, hogy sikeres vagy sikertelen volt-e.

Figyelembe kell venni a feladat meghibásodásának hatását a feladatra és a feladatok függőségeire.  A [exitConditions](https://docs.microsoft.com/rest/api/batchservice/task/add#exitconditions) tulajdonság megadható egy feladathoz, amely egy műveletet konfigurál a függőségekhez és a feladathoz.
- A függőségek esetében a [DependencyAction](https://docs.microsoft.com/rest/api/batchservice/task/add#dependencyaction) szabályozza, hogy a sikertelen tevékenységtől függő feladatok blokkolva vannak-e vagy futnak-e.
- A [JobAction](https://docs.microsoft.com/rest/api/batchservice/task/add#jobaction) azt szabályozza, hogy a sikertelen feladat a letiltott, leállított vagy nem módosult feladatot eredményezi-e.

### <a name="task-command-line-failures"></a>Feladat parancssori hibái

A Feladat parancssorának futtatásakor a kimenet a `stderr.txt` következőre íródik: `stdout.txt`és. Az alkalmazás Emellett írhat az alkalmazásspecifikus naplófájlokra is.

Ha a készlet olyan csomópontja, amelyen még fut egy feladat, a naplófájlok beszerezhetők és megtekinthetők. Például a Azure Portal listázza és megtekintheti a feladatok vagy a készlet-csomópontok naplófájljait. Több API is lehetővé teszi, hogy a feladatok fájljai megjelenjenek és beszerezhetők legyenek, például [a beolvasás feladatból](https://docs.microsoft.com/rest/api/batchservice/file/getfromtask).

A készletek és a Pool-csomópontok gyakran ideiglenesek, a csomópontok folyamatos hozzáadása és törlése miatt ajánlott a naplófájlok megőrzése. A [feladat kimeneti fájljai](https://docs.microsoft.com/azure/batch/batch-task-output-files) kényelmes módon menthetők a naplófájlok az Azure Storage-ba.

### <a name="output-file-failures"></a>Kimeneti fájlok hibái
Minden fájl feltöltésekor a Batch két naplófájlt ír a számítási csomópontba, `fileuploadout.txt` és. `fileuploaderr.txt` Megvizsgálhatja ezeket a naplófájlokat, ha többet szeretne megtudni egy adott hibáról. Azokban az esetekben, amikor a fájlfeltöltés soha nem történt meg, például azért, mert maga a feladat nem tudott futni, akkor ezek a naplófájlok nem fognak létezni.  

## <a name="next-steps"></a>További lépések

Győződjön meg arról, hogy az alkalmazás átfogó hibaellenőrzés-ellenőrzést végez. kritikus fontosságú lehet a problémák azonnali észlelése és diagnosztizálása.
