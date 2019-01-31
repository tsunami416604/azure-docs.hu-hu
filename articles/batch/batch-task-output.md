---
title: Eredmények és az adattár – Azure Batch a befejezett feladatok és tevékenységek naplók megőrzése |} A Microsoft Docs
description: A Batch-feladatok és a feladatok megőrzése kimeneti adatokat a különböző lehetőségek ismertetése. Megőrizheti az adatokat az Azure Storage vagy a másikba.
services: batch
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ff7224b342aa421c576c170f3c23ac64cad9f161
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474344"
---
# <a name="persist-job-and-task-output"></a>Feladatok és tevékenységek kimenetének megőrzése

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Néhány gyakori példa a feladat kimenete a következők:

- A fájl jön létre, amikor a feladat-folyamatok bemeneti adatokat.
- A feladat a végrehajtás társított a rendszernapló fájljaiban.

Ez a cikk ismerteti a különböző beállítások megőrzése tevékenység kimenetének.

## <a name="options-for-persisting-output"></a>Kimenet megőrzése lehetőségei

A forgatókönyvtől függően többféle módon is néhány feladat kimenetének megőrzése érdekében:

- [A Batch szolgáltatás API-val](batch-task-output-files.md).  
- [A Batch File Conventions-könyvtárral használata a .NET-hez](batch-task-output-file-conventions.md).  
- A Batch File Conventions standard valósítja meg az alkalmazásban.
- Egy egyéni fájl áthelyezése megoldást valósíthat meg.

A következő szakaszok röviden ismertetik egyes megközelítés, valamint a hadoopban kimeneti kapcsolatos általános kialakítási szempontok.

### <a name="use-the-batch-service-api"></a>A Batch szolgáltatás API-val

A Batch szolgáltatás támogat megadásával kimeneti fájlok az Azure Storage-ban a feladat adatai amikor Ön [feladat hozzáadása egy feladathoz](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) vagy [feladatok tevékenységek gyűjteményei hozzáadása egy feladathoz](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Persisting feladat kimenete a Batch szolgáltatás API-val további információkért lásd: [megőrzése feladat adatokat az Azure Storage a Batch szolgáltatás API](batch-task-output-files.md).

### <a name="use-the-batch-file-conventions-library-for-net"></a>A Batch File Conventions-könyvtárral használata a .NET-hez

A Batch nem kötelezők, a feladat kimeneti fájlok az Azure Storage elnevezési konvenciók határozza meg. A [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) ezek konvenciókat ismerteti. A File Conventions szabvány határozza meg, hogy a tároló és blobnév elérési utat az Azure Storage-ban a feladatok és tevékenységek nevei alapján adott kimeneti fájl nevét.

Arra, hogy kívánja-e a kimeneti adatok fájlok elnevezéséhez a File Conventions standard használja. Nevezze el a céltároló is, és szeretné azonban blob. Ha a File Conventions standard kimeneti fájlok elnevezési használja, akkor a kimeneti fájlok megtekintését a a [az Azure portal][portal].

A fejlesztők a Batch megoldások fejlesztése a C#- és .NET a a [File Conventions-könyvtárral a .NET-hez] [ nuget_package] és megőrizni a feladat adatai egy Azure Storage-fiók, függően, hogy a [kötegfájlt Standard konvenciók](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). A File Conventions-könyvtárral mozgó kimeneti fájlok az Azure Storage és a cél-tárolók és blobok elnevezésével egy jól ismert módon kezeli.

A .NET-hez a File Conventions-könyvtárral megőrzése feladat kimenete a további információkért lásd: [megőrizheti a feladatok és tevékenységek adatokat, a Batch File Conventions-könyvtárral az Azure Storage .NET-keretrendszerhez készült](batch-task-output-file-conventions.md).

### <a name="implement-the-batch-file-conventions-standard"></a>A Batch File Conventions standard megvalósítása

.NET nem nyelvű használatakor valósítható meg a [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) a saját alkalmazásában.

Előfordulhat, hogy szeretné végrehajtani a File Conventions elnevezési szabványt saját magának, ha azt szeretné, hogy egy jól bevált elnevezési sémát, vagy ha a feladat kimenetének megtekintéséhez az Azure Portalon.

### <a name="implement-a-custom-file-movement-solution"></a>Egy egyéni fájl adatátviteli megoldás megvalósítása

A saját teljes fájl adatátviteli megoldás is alkalmazhat. Ez a megközelítés mikor használja:

- Szeretné megőrizni a feladat adatai egy adattárba, eltérő Azure Storage. Fájlok feltöltése egy adattár, például az Azure SQL- vagy Azure-DataLake, létrehozhat egy egyéni parancsfájl vagy végrehajtható fájlt tölthet fel az adott helyen. Ezt követően meghívhatja azt a parancssorból az elsődleges végrehajtható fájl futtatása után. Egy Windows-csomóponton, például ez a két parancs meghívása előfordulhat, hogy: `doMyWork.exe && uploadMyFilesToSql.exe`
- Az eredmények ellenőrzőpontos vagy korai feltöltését végrehajtására vonatkozó szándékát.
- Meg szeretné tartani a szabályozható a hibakezelés. Például érdemes a saját megoldást valósíthat meg, ha azt szeretné, bizonyos műveleteket feltöltési alapján meghatározott tevékenységek kilépési kódjai függőségi Feladatműveletek használatával. A függőségi Feladatműveletek további információkért lásd: [létrehozása, amely más tevékenységektől függő feladatok tevékenységfüggőségek](batch-task-dependencies.md).

## <a name="design-considerations-for-persisting-output"></a>Kimenet megőrzése kapcsolatos kialakítási szempontok

A Batch-megoldás tervezésekor vegye figyelembe a következő tényezőket, feladatok és tevékenységek kimeneteinek kapcsolatos.

- **Számítási csomópont élettartama**: Számítási csomópontok általában átmeneti, különösen az automatikus méretezés felkészített készletekben. A csomóponton futó feladat kimenete csak, amíg a csomópont létezik, és csak a fájl megőrzési időn belül beállította a feladat érhető el. Ha a feladat kimeneti, szükséges lehet a feladat befejezése után, majd a feladat fel kell tölteni a kimeneti fájlok – például az Azure Storage olyan tartós tárban.

- **Tároló kimeneti**: Az Azure Storage ajánlott tevékenység kimenetének adattárként, de bármilyen tartós tárolási is használhatja. Az Azure Storage-feladat kimeneti írása integrálva van az a Batch szolgáltatás API-ja. Ha egy másik képernyő tartós tárhelyet használja, szüksége az alkalmazáslogikák teljes feladat kimeneti saját maga is tartalmaz.

- **Lekérés kimeneti**: Kérheti le a feladat kimeneti közvetlenül a készletben lévő számítási csomópontok, vagy az Azure Storage vagy egy másik adattárral, ha a feladat kimenetének megőrizte rendelkezik. Közvetlenül a számítási csomóponton a feladat kimenetének lekéréséhez szüksége van a fájl nevét és a kimeneti helyet a csomóponton. Ha az Azure Storage-feladat kimeneti szűnik meg, akkor szüksége az Azure Storage-töltse le a kimeneti fájlokat az Azure Storage SDK-val a fájl teljes elérési útja.

- **Megtekintés a kimeneti**: Ha megnyitja egy Batch-feladat az Azure Portalon, és válassza **csomóponton lévő fájlok**, lehetősége lesz a feladathoz hozzárendelt összes fájl, nem csak a kimeneti fájlokat is érdekli. Újra a számítási csomópontokon fájl áll rendelkezésre, csak, amíg a csomópont létezik, és csak a fájlmegőrzési időt belül beállította a feladat. Feladat kimenete, amely az Azure Storage már megőrizte megtekintéséhez használhatja az Azure portal vagy egy Azure Storage ügyféloldali alkalmazás például a [Azure Storage Explorer][storage_explorer]. Kimeneti adatok megtekintéséhez az Azure Storage-ban a portál vagy egy másik eszközzel kell ismeri a fájl helyét, és keresse meg a fájlt közvetlenül.

## <a name="next-steps"></a>További lépések

- Ismerje meg az új funkciók használatához a Batch szolgáltatás API-ban a feladat adatmegőrzésre [megőrzése feladat adatokat az Azure Storage a Batch szolgáltatás API](batch-task-output-files.md).
- Ismerje meg a Batch File Conventions-könyvtárral használata a .NET-keretrendszerhez készült [megőrizheti a feladatok és tevékenységek adatokat, a Batch File Conventions-könyvtárral az Azure Storage .NET-keretrendszerhez készült](batch-task-output-file-conventions.md).
- Tekintse meg a [PersistOutputs] [ github_persistoutputs] mintaprojektet a Githubon, amely azt ismerteti, hogyan használhatja a kötegelt ügyféloldali kódtár a .NET és .NET-keretrendszerhez készült File Conventions-könyvtárral tartós tárolási, a feladat kimenetének megőrzése .

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
