---
title: Feladat és feladatkimenet megőrzése egy adattárban - Azure Batch
description: Ismerje meg a batch feladatok ból és feladatokból származó kimeneti adatok megőrzésének különböző lehetőségeit. Az azure storage-ba vagy egy másik adattárba adatokat tarthat meg.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0809a838f1d34491eb4e276ce356eded9b98756e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022834"
---
# <a name="persist-job-and-task-output"></a>Feladatok és tevékenységek kimenetének megőrzése

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Néhány gyakori példa a feladatkimenetre:

- A bemeneti adatok feldolgozásakor létrehozott fájlok.
- A feladat végrehajtásához társított naplófájlok.

Ez a cikk a feladatkimenet megőrzésének különböző lehetőségeit ismerteti.

## <a name="options-for-persisting-output"></a>A tartós kimenet beállításai

A forgatókönyvtől függően néhány különböző módszert is megtehetsz a feladatkimenet megőrzéséhez:

- [Használja a Batch szolgáltatás API-ját.](batch-task-output-files.md)  
- [Használja a batch file konvenciók könyvtárat a .NET](batch-task-output-file-conventions.md).  
- Valósítsa meg a Batch File Conventions szabványt az alkalmazásban.
- Egyéni fájlmozgatási megoldás megvalósítása.

A következő szakaszok röviden ismertetik az egyes megközelítéseket, valamint a teljesítmény megőrzésével kapcsolatos általános tervezési szempontokat.

### <a name="use-the-batch-service-api"></a>A Batch szolgáltatás API-jának használata

A Batch szolgáltatás támogatja a kimeneti fájlok megadását az Azure Storage-ban a feladatadatokhoz, amikor [feladatot ad hozzá egy feladathoz,](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) vagy [feladatok gyűjteményét adja hozzá egy feladathoz.](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job)

A Batch szolgáltatás API-jával a feladatkimenet megőrzéséről további információt a [Feladatadatok megőrzése az Azure Storage-ba a Batch service API-val](batch-task-output-files.md)című témakörben talál.

### <a name="use-the-batch-file-conventions-library-for-net"></a>A kötegfájl-konvenciók könyvtár használata a .NET fájlhoz

Batch határozza meg a választható konvenciók elnevezési feladat kimeneti fájlokat az Azure Storage-ban. A [batch file konvenciók szabvány](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) ismerteti ezeket a konvenciókat. A fájlkonvenciók szabvány határozza meg a céltároló és a blob elérési útja az Azure Storage-ban egy adott kimeneti fájl neve alapján a feladat és a feladat.

Ön dönti el, hogy a fájlkonvenciók szabványt használja-e a kimeneti adatfájlok elnevezésére. A céltárolót és a blobot tetszés szerint is elnevezheti. Ha a fájlkonvenciók szabványt használja a kimeneti fájlok elnevezéséhez, akkor a kimeneti fájlok megtekinthetők az [Azure Portalon.][portal]

A C# és .NET fájlkonvenciók könyvtárat fejlesztő fejlesztők a [.NET fájlkonvenciók könyvtárával][nuget_package] a Batch File File Data-fiókban a [Batch File Conventions szabványnak](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions)megfelelően megőrizhetik a feladatadatokat egy Azure Storage-fiókban. A Fájlkonvenciók könyvtár kezeli a kimeneti fájlok áthelyezése az Azure Storage és elnevezési céltárolók és blobok egy jól ismert módon.

A .NET fájlkonvenciók könyvtárával a [feladat- és feladatadatok megőrzése az Azure Storage szolgáltatásba a Batch File Conventions könyvtárral](batch-task-output-file-conventions.md)című témakörben talál további információt.

### <a name="implement-the-batch-file-conventions-standard"></a>A batch file egyezmények szabványának végrehajtása

Ha nem .NET nyelvet használ, a [batch file konvenciók szabványt](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) saját alkalmazásában is megvalósíthatja.

Előfordulhat, hogy szeretné végrehajtani a fájlkonvenciók elnevezési szabvány magát, ha azt szeretné, hogy egy bevált elnevezési séma, vagy ha meg szeretné tekinteni a feladatkimenetet az Azure Portalon.

### <a name="implement-a-custom-file-movement-solution"></a>Egyéni fájlmozgatási megoldás megvalósítása

Saját teljes fájlmozgatási megoldást is megvalósíthat. Ezt a módszert akkor használja, ha:

- A feladatadatokat az Azure Storage-tól eltérő adattárban szeretné megtartani. Fájlok feltöltéséhez egy adattárba, például az Azure SQL vagy az Azure DataLake, létrehozhat egy egyéni parancsfájlt vagy végrehajtható feltölteni az adott helyre. Ezután az elsődleges végrehajtható fájl futtatása után hívhatja meg a parancssorban. Egy Windows-csomóponton például a következő két parancsot hívhatja meg:`doMyWork.exe && uploadMyFilesToSql.exe`
- A kezdeti eredmények ellenőrzése vagy korai feltöltése.
- A hibakezelés részletes vezérlését szeretné fenntartani. Előfordulhat például, hogy saját megoldást szeretne megvalósítani, ha a tevékenységfüggőségi műveleteket bizonyos feltöltési műveletek végrehajtására szeretné használni adott feladatkilépési kódok alapján. A tevékenységfüggőségi műveletekről további információt a [Tevékenységfüggőségek létrehozása más tevékenységektől függő feladatok futtatásához](batch-task-dependencies.md)című témakörben talál.

## <a name="design-considerations-for-persisting-output"></a>Tervezési szempontok a tartós kimenethez

A Batch-megoldás tervezésekor vegye figyelembe a feladat- és feladatkimenetekkel kapcsolatos alábbi tényezőket.

- **Számítási csomópontok élettartama:** A számítási csomópontok gyakran átmenetiek, különösen az automatikus skálázást támogató készletekben. A csomóponton futó feladat kimenete csak akkor érhető el, ha a csomópont létezik, és csak a feladathoz beállított fájlmegőrzési időszakon belül. Ha egy feladat olyan kimenetet hoz létre, amelyre a feladat befejezése után szükség lehet, akkor a feladatnak fel kell töltenie a kimeneti fájljait egy tartós tárolóba, például az Azure Storage-ba.

- **Kimeneti tárhely**: Az Azure Storage adattárként ajánlott a feladatkimenethez, de bármilyen tartós tárolót használhat. A feladatkimenet írása az Azure Storage-ba integrálva van a Batch szolgáltatás API-ba. Ha egy másik formája a tartós tároló, akkor meg kell írnia az alkalmazás logikáját a feladat kimeneti saját kezűleg.

- **Kimenetlekérések:** A feladatkimenetet közvetlenül a készlet ben lévő számítási csomópontokból, vagy az Azure Storage-ból vagy más adattárból is lekérheti, ha megőrzött feladatkimenetet. A feladat kimenetének lekéréséhez közvetlenül egy számítási csomópontról, szükség van a fájl nevét és kimeneti helyét a csomóponton. Ha megósza a feladatkimenetet az Azure Storage-ba, akkor az Azure Storage-ban a fájl teljes elérési útjának kell, hogy letölthesse a kimeneti fájlokat az Azure Storage SDK-val.

- **Kimenet megtekintése:** Amikor az Azure Portalon egy Batch-feladatra navigál, és a Fájlok a csomóponton lehetőséget **választja,** a feladathoz társított összes fájl megjelenik, nem csak az önt érdeklő kimeneti fájlok. A számítási csomópontokon lévő fájlok is csak akkor érhetők el, ha a csomópont létezik, és csak a feladathoz beállított fájlmegőrzési időn belül. Az Azure Storage-ban megőrzött feladatkimenet megtekintéséhez használhatja az Azure Portalt vagy egy Azure Storage-ügyfélalkalmazást, például az [Azure Storage Explorert.][storage_explorer] A kimeneti adatok megtekintéséhez az Azure Storage-ban a portálon vagy egy másik eszközzel, ismernie kell a fájl helyét, és keresse meg közvetlenül.

## <a name="next-steps"></a>További lépések

- Fedezze fel a Batch szolgáltatás API új funkcióinak használatával a feladatadatok megőrzéséhez az [Azure Storage feladatadatainak megőrzéséhez az Azure Storage-ban a Batch service API-val.](batch-task-output-files.md)
- Megtudhatja, hogy miként használja a Batch File Conventions függvénytár -for .NET in [Persist feladat és feladatadatok az Azure Storage-ba a Batch File Conventions könyvtár .NET](batch-task-output-file-conventions.md).
- Tekintse meg a [PersistOutputs][github_persistoutputs] mintaprojektet a GitHubon, amely bemutatja, hogyan használhatja a Batch ügyfélkönyvtárat a .NET-hez, és a .NET fájlkonvenciók könyvtárát a feladatkimenet tartós tárolóvá való megőrzéséhez.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
