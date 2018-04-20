---
title: Továbbra is fennáll, eredményeit, illetve a befejezett feladatokhoz és a feladatok egy adattár - Azure Batch-naplókat |} Microsoft Docs
description: Ismerje meg a kötegelt feladatok és munkák kimeneti adatait különböző lehetőségek közül. Az adatok Azure Storage, vagy hogy egy másik adattárhoz is megmaradnak.
services: batch
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cb8b1ca3514e27221e95cb2def823c8f89d151e5
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
---
# <a name="persist-job-and-task-output"></a>Feladatok és tevékenységek kimenetének megőrzése

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Néhány gyakori példán feladat kimenetéből a következők:

- A fájlok jön létre, amikor a tevékenység végpontja bemeneti adatok.
- A feladat a végrehajtás társított naplófájlokat. 

Ez a cikk ismerteti, különféle beállításai tárolásakor feladat kimenetének és a forgatókönyvekben, amelynek minden egyes beállítás leginkább megfelelő.   

## <a name="about-the-batch-file-conventions-standard"></a>A kötegelt fájl egyezmények standard kapcsolatos

Kötegelt meghatároz egy választható feladat kimeneti fájlok az Azure Storage elnevezési konvenciókat. A [kötegelt fájl egyezmények standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) ezek konvenciókat ismerteti. A fájl egyezmények szabvány meghatározza, hogy a tároló és a blob célútvonalat az Azure Storage a feladat- és a neve alapján adott kimeneti fájl nevét.

Már Önnek e úgy dönt, hogy a fájl egyezmények standard használja a kimeneti adatok fájlok elnevezéséhez. Is a céltárolója name és blob azonban kívánja. Ha a fájl egyezmények standard kimeneti fájlok elnevezési, akkor a kimeneti fájlok megtekintését a rendszer a [Azure-portálon][portal].

Használható a fájl egyezmények standard néhány különböző módja van:

- A Batch szolgáltatás API-JÁNAK használatakor megőrizni a kimeneti fájlok esetén dönthet úgy neve cél tárolók és blobok a fájl egyezmények szabvány szerint. A Batch szolgáltatás API lehetővé teszi a feladat alkalmazás módosítása nélkül megőrizni a kimeneti fájlok ügyfél kódból.
- Ha a .NET fejleszt, használhatja a [Azure Batch fájl egyezmények .NET-keretrendszerhez készült][nuget_package]. Ezt a szalagtárat használó előnye, hogy az támogatja a kimeneti fájlok azonosító vagy célú szerint lekérdezése. A beépített lekérdező funkció megkönnyíti a kimeneti fájlok eléréséhez, egy ügyfélalkalmazást, illetve más feladatok. A feladat alkalmazás módosítani kell hívni fájl egyezmények könyvtár. További információkért lásd: mutató hivatkozás a [fájl egyezmények .NET-keretrendszerhez készült](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.aspx).
- .NET eltérő nyelvű fejleszt, ha a fájl egyezmények standard valósíthatja meg az alkalmazás.

## <a name="design-considerations-for-persisting-output"></a>Persisting kimeneti kialakítási szempontjai 

A Batch-megoldás tervezésekor vegye figyelembe az alábbi tényezők feladat- és kimeneti kapcsolódik.

* **Számítási csomópont élettartama**: számítási csomópontok gyakran átmeneti jellegűek, különösen a készletek automatikus skálázás engedélyezve van. Egy csomóponton futó feladat kimenete csak, amíg a csomópont létezik-e, és csak a fájl megőrzési időtartamon belül a feladathoz beállított érhető el. Ha a feladat kimenete, szükséges lehet a feladat végrehajtása után, majd a feladat fel kell tölteni a kimeneti fájlok egy tartós például az Azure Storage-tároló.

* **A kimeneti tárolási**: a feladat kimenete adattárként Azure Storage ajánlott, de a tartós tárolót is használhatja. A feladat kimenetének Azure Storage írása a Batch szolgáltatás API integrálva van. Egy másik formája tartós tárolási használatakor szüksége megőrizni a feladatot, kimeneti saját kezűleg alkalmazáslogikák.   

* **Lekérés kimeneti**: kérheti le a feladat kimenetének közvetlenül a számítási csomópontok a készlethez, vagy az Azure Storage vagy más adattárolóbeli tárolására, ha azt teszi lehetővé a megőrzött feladat kimenete. A számítási csomópont-ről egy feladat kimenetének lekéréséhez szüksége van a fájl nevét és a kimeneti helyére a csomóponton. Ha a feladat kimenetének Azure Storage is fennáll, akkor szüksége az Azure Storage Azure Storage SDK-t a kimeneti fájlok letöltéséhez a fájl teljes elérési útja.

* **Kimeneti megtekintése**: az Azure portál, és válassza a kötegelt feladat kiválasztásakor **csomóponton fájlok**, lehetősége lesz a feladathoz hozzárendelt összes fájl, nem csak a kimeneti fájlok kíváncsiak vagyunk. Ebben az esetben a számítási csomópontok fájl áll rendelkezésre, csak a csomópont létezik-e, és csak a fájl megőrzési időn belül beállította a feladat során. A feladat kimenetének Azure Storage fennállásának megtekintéséhez használhatja az Azure-portálon vagy egy Azure Storage ügyfélalkalmazást, mint a [Azure Tártallózó][storage_explorer]. Azure Storage a portálon vagy egy másik eszköz a kimeneti adatok megtekintéséhez ismeri a fájl helyét, és keresse meg a fájlt közvetlenül.

## <a name="options-for-persisting-output"></a>Persisting kimenet beállításai

A forgatókönyvtől függően a rendszer továbbra is fennáll feladatkimenet is igénybe vehet néhány különböző szempontok:

- A Batch szolgáltatás API Felületet használják.  
- A kötegelt fájl egyezmények könyvtár használata a .NET-hez.  
- A kötegelt fájl egyezmények standard megvalósítása az alkalmazásban.
- Egy egyéni fájl adatátviteli szolgáltatás megvalósítása.

A következő szakaszok ismertetik részletesebben mindkét megközelítés.

### <a name="use-the-batch-service-api"></a>A Batch szolgáltatás API-val

2017-05-01 verziójával, a Batch szolgáltatás támogatást biztosít az Azure Storage kimeneti fájlok feladat adatainak megadása során meg [ad hozzá tevékenységet egy feladathoz](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) vagy [feladatok egy gyűjteményének hozzáadása egy feladat](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

A Batch szolgáltatás API feladat adatait támogatja az Azure Storage-fiók a virtuálisgép-konfiguráció létre készleteket. A Batch szolgáltatás API-t az alkalmazás, amely a feladat módosítása nélkül megőrizni a feladat adatait. Is opcionálisan igazodnia kell a [kötegelt fájl egyezmények standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) Azure Storage továbbra is fennáll, a fájlok elnevezési. 

A Batch szolgáltatás API-t használ a tartós feladatot, kimeneti ha:

- Szeretné megőrizni a kötegelt és feladat manager feladatok készletek létrehozni a virtuális gép konfigurációs adatait.
- Szeretné megőrizni az adatokat az Azure Storage-tárolóban egy tetszőleges nevet.
- Szeretné megőrizni az adatokat a következők szerint az Azure Storage tárolót a [kötegelt fájl egyezmények standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). 

> [!NOTE]
> A Batch szolgáltatás API nem támogatja a készletek a felhőalapú szolgáltatás konfigurációja létre futó feladatok adatait. A felhő konfigurálása futtató készletek kimenetét tárolásakor feladattal kapcsolatos információkért lásd: [a kötegelt fájl egyezmények könyvtárhoz megőrizni a .NET-keretrendszerhez készült Azure Storage feladat- és adatok megőrzése ](batch-task-output-file-conventions.md)
> 
> 

A Batch szolgáltatás API-val tárolásakor feladat kimenetének további információkért lásd: [megőrző feladat adatok Azure Storage a kötegelt API szolgáltatás](batch-task-output-files.md). Is tekintse meg a [PersistOutputs] [github_persistoutputs] mintaprojektet a Githubon, amely bemutatja, hogyan kell a kötegelt ügyféloldali kódtára a .NET használatával továbbra is fennáll feladatkimenet tartós tárhelyre.

### <a name="use-the-batch-file-conventions-library-for-net"></a>A kötegelt fájl egyezmények könyvtár használata a .NET-hez

A fejlesztők kötegelt megoldások a C# és .NET felépítése a [fájl egyezmények .NET-keretrendszerhez készült] [ nuget_package] és megőrizni a feladat adatokat az Azure Storage-fiók, függően, hogy a [kötegelt fájl egyezmények standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). A fájl egyezmények könyvtár áthelyezése kimeneti fájlok Azure Storage és a cél-tárolók és blobok elnevezésével egy jól ismert módon kezeli.

A fájl egyezmények könyvtárban vagy az azonosító, vagy a célból, így könnyen helyezni őket anélkül, hogy a teljes fájl URI-k által lekérdező kimeneti-fájlokat támogatja. 

A kötegelt fájl egyezmények .NET-keretrendszerhez készült használ a tartós feladatot, kimeneti ha:

- A feladat futása közben szeretné adatfolyam adatok Azure Storage.
- Szeretné megőrizni a készletek a felhőalapú szolgáltatás konfigurációja vagy a virtuálisgép-konfiguráció adatait.
- Keresse meg és töltse le a tevékenység kimeneti fájlok azonosító vagy célú kell az ügyfélalkalmazás vagy egyéb feladatok, a feladat. 
- Szeretne végezni az eredmények ellenőrzés mutató vagy korai feltöltése.
- A feladat kimenetének megtekintése az Azure portálon szeretné.

A .NET-hez lévő objektumán feladat kimenete a fájl egyezmények könyvtárhoz további információkért lásd: [megőrizni a feladat- és adatok Azure Storage a kötegelt fájl egyezmények .NET-keretrendszerhez készült megőrizni ](batch-task-output-file-conventions.md). Is tekintse meg a [PersistOutputs] [github_persistoutputs] mintaprojektet a Githubon, amely bemutatja, hogyan használható a fájl egyezmények könyvtár a .NET-hez továbbra is fennáll feladatkimenet tartós tárhelyre.

A [PersistOutputs] [github_persistoutputs] mintaprojektet a Githubon bemutatja, hogyan kell a kötegelt ügyféloldali kódtára a .NET használatával továbbra is fennáll feladatkimenet tartós tárhelyre.

### <a name="implement-the-batch-file-conventions-standard"></a>A kötegelt fájl egyezmények standard megvalósítása

.NET helyett más nyelvre használatakor is létrehozható a [kötegelt fájl egyezmények standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) a saját alkalmazásban. 

Előfordulhat, hogy szeretné végrehajtani a fájl egyezmények elnevezési szabványnak magát, ha azt szeretné, hogy egy megbízható elnevezési sémát, vagy ha meg szeretné tekinteni a feladat kimenetének Azure-portálon.

### <a name="implement-a-custom-file-movement-solution"></a>Egy egyéni fájl adatátviteli megoldás megvalósítása

A saját teljes fájl adatátviteli megoldás is megvalósíthatja. Ez készíthető elő, amikor használja:

- Szeretné megőrizni a feladat adatok Azure Storage eltérő adattárat. Fájlok feltöltése az Azure SQL-vagy Azure DataLake adattárat, létrehozhat egy egyéni parancsfájl vagy végrehajtható fájlt tölthet fel erre a helyre. Majd hívása, a parancssorban az elsődleges végrehajtható fájl futtatása után. Például egy Windows-csomóponton, előfordulhat, hogy meghívja a két parancsokkal: `doMyWork.exe && uploadMyFilesToSql.exe`
- Szeretne végezni az eredmények ellenőrzés mutató vagy korai feltöltése.
- Meg szeretné tartani a hibakezelés részletes szabályozását. Érdemes lehet például a saját megoldás megvalósításának, ha használandó függőségi Feladatműveletek bizonyos feltöltés műveletekre, a megadott kilépési kód alapján. A feladatütemezés-függőség műveletek további információkért lásd: [feladat függőségek más feladatok függő feladatok futtatásához létrehozása](batch-task-dependencies.md). 

## <a name="next-steps"></a>További lépések

- Fedezze fel az új funkciók használatához a Batch szolgáltatás API-ban megőrizni a tevékenységek adatai [megőrző feladat adatok Azure Storage a kötegelt API szolgáltatás](batch-task-output-files.md).
- További tudnivalók a kötegelt fájl egyezmények könyvtár használata a .NET-keretrendszerhez készült [megőrizni a feladat- és adatok Azure Storage a kötegelt fájl egyezmények .NET-keretrendszerhez készült megőrizni ](batch-task-output-file-conventions.md).
- Tekintse meg a [PersistOutputs] [github_persistoutputs] mintaprojektet a Githubon, amely bemutatja, hogyan kell használni a kötegelt ügyféloldali kódtár .NET és a fájl egyezmények .NET-keretrendszerhez készült megőrizni a feladat kimenete a tartós tárhelyre.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/
