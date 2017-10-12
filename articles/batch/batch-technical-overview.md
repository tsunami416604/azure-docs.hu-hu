---
title: "Nagyméretű párhuzamos számítási feladatok futtatása a felhőben az Azure Batch használatával | Microsoft Docs"
description: "Megismerheti, hogyan használhatja az Azure Batch szolgáltatást nagyméretű párhuzamos és HPC számítási feladatokhoz."
services: batch
documentationcenter: 
author: tamram
manager: timlt
editor: 
ms.assetid: 93e37d44-7585-495e-8491-312ed584ab79
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/05/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a99f96db0c1e8bcd0cf29c564e5badf0eb728e56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="run-intrinsically-parallel-workloads-with-batch"></a>Belsőleg párhuzamos számítási feladatok futtatása a Batch használatával

Az Azure Batch platformszolgáltatás lehetővé teszi, hogy hatékonyan futtasson nagyméretű párhuzamos és nagy teljesítményű feldolgozási (HPC) alkalmazásokat a felhőben. Az Azure Batch számításigényes munkák futtatását ütemezi virtuális gépek felügyelt gyűjteményében, és automatikusan képes méretezni a számítási erőforrásokat a feladatok igényeinek megfelelően.

Az Azure Batch-szolgáltatással könnyűszerrel határozhat meg Azure számítási erőforrásokat az alkalmazások párhuzamos és méretezhető futtatásához. Nem kell manuálisan létrehoznia, konfigurálnia és felügyelnie a HPC-fürtöket, az egyes virtuális gépeket, virtuális hálózatokat vagy az összetett feladat- és tevékenységütemezési infrastruktúrát. Az Azure Batch automatizálja vagy egyszerűbbé teszi ezeket a feladatokat az Ön számára.

## <a name="use-cases-for-batch"></a>A Batch használati esetei
A Batch felügyelt Azure szolgáltatás, amely *kötegelt feldolgozásra* vagy *kötegelt számításra* szolgál – vagyis a kívánt eredmények elérése érdekében a hasonló feladatok nagy mennyiségének a futtatására. A kötegelt feldolgozás olyan szervezetek használják a leggyakrabban, amelyek rendszeresen dolgoznak fel, alakítanak át és elemeznek nagy mennyiségű adatot.

A Batch nagyszerűen működik a belsőleg párhuzamos (más néven „zavaróan párhuzamos”) alkalmazásokkal és számítási feladatokkal. A belsőleg párhuzamos számítási feladatok könnyen oszthatók több olyan feladatra, amelyek egy időben sok számítógépen hajtanak végre munkát.

![Párhuzamos feladatok][1]<br/>

Néhány példa az ezzel a technikával gyakran feldolgozott számítási feladatokra:

* pénzügyi kockázat modellezése,
* időjárási és hidrológiai adatok elemzése,
* képek renderelése, elemzése és feldolgozása,
* adathordozók kódolása és átkódolása,
* génszekvenciák elemzése,
* műszaki feszültségelemzés,
* szoftvertesztelés.

A Batch végső soron kevesebb lépéssel hajthat végre párhuzamos számításokat, valamint összetettebb HPC számítási feladatokat végezhet, például [Message Passing Interface (MPI)](batch-mpi.md) alkalmazásokat futtathat.

Az Azure által biztosított Batch szolgáltatás és az egyéb HPC-megoldások összehasonlítását lásd: [HPC, Batch és Big Compute-megoldások](../virtual-machines/linux/high-performance-computing.md).

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="scenario-scale-out-a-parallel-workload"></a>Forgatókönyv: Párhuzamos számítási feladat horizontális felskálázása
A Batch szolgáltatással való interakcióhoz Batch API-kat használó általános megoldás a belsőleg párhuzamos munkák horizontálisan felskálázásával jár – például a 3D jelenetek képeit rendereli – a számítási csomópontok készletén. A számítási csomópontok ezen készlete a „renderelési farm” lehet, amely több tíz, több száz vagy akár több ezer magot biztosít például a renderelési feladathoz.

Az alábbi diagram egy általános Batch-munkafolyamatot mutat be, ahol az ügyfélalkalmazás vagy az üzemeltetett szolgáltatás Batch szolgáltatással futtat párhuzamos számítási feladatot.

![Batch-megoldás munkafolyamata][2]

Ebben az általános forgatókönyvben az alkalmazás vagy a szolgáltatás számítási feladatot dolgoz fel az Azure Batch szolgáltatásban a következő lépések végrehajtásával:

1. Töltse fel a **bemeneti fájlokat** és az **alkalmazást**, amely fel fogja dolgozni ezeket a fájlokat az Azure Storage-fiókban. A bemeneti fájlok bármely olyan adatok lehetnek, amelyet az alkalmazás fel fog dolgozni, például pénzügyi modellezési adatok vagy átkódolni kívánt videofájlok. Az alkalmazásfájlok az adatok feldolgozásához használt bármilyen alkalmazások lehetnek, például 3D renderelési alkalmazások vagy adathordozó-átkódolók.
2. Hozza létre a számítási csomópontok Batch-**készletét** a Batch-fiókban. Ezek a csomópontok a feladatokat végrehajtó virtuális gépek. Olyan tulajdonságokat adhat meg, mint a [csomópont mérete](../cloud-services/cloud-services-sizes-specs.md), operációs rendszere és helye azon alkalmazás Azure Storage tárolójában, amelyet akkor telepít, amikor a csomópontok a készlethez csatlakoznak (az 1. lépésben feltöltött alkalmazás). A készletet [automatikus méretezésre](batch-automatic-scaling.md) is konfigurálhatja a tevékenységek által létrehozott számítási feladatokra válaszul. Az automatikus méretezés dinamikusan állítja be a készletben a számítási csomópontok számát.
3. Hozzon létre egy Batch-**feladatot** a számítási feladat futtatásához a számítási csomópontok készletén. Feladat létrehozásakor társítsa azt a Batch-készlettel.
4. Adjon hozzá **tevékenységeket** a feladathoz. Amikor tevékenységeket ad hozzá egy munkához, a Batch szolgáltatás automatikusan ütemezi a tevékenységeket a készletben lévő számítási csomópontokon. Mindegyik tevékenység a bemeneti fájlok feldolgozásához feltöltött alkalmazást használja.
   
   * 4a. A tevékenység végrehajtása előtt a tevékenység letöltheti azon adatokat (a bemeneti fájlokat), amelyeket fel kell dolgoznia a hozzárendelt számítási csomóponton. Ha az alkalmazás még nem lett telepítve a csomóponton (lásd a 2. lépést), helyette ide tölthető le. Amikor a letöltések elkészültek, a tevékenységek lefutnak a hozzájuk rendelt csomópontokon.
5. A tevékenységek futtatásakor lekérdezheti a Batch szolgáltatást a feladat és a tevékenységei állapotának megfigyeléséhez. Az Ön által készített ügyfélalkalmazások vagy szolgáltatások HTTPS használatával kommunikálhatnak a Batch szolgáltatással. Mivel előfordulhat, hogy számítási csomópontok ezrein futó több ezer tevékenységet kell figyelnie, gondoskodjon arról, hogy [hatékonyan kérdezi le a Batch szolgáltatást](batch-efficient-list-queries.md).
6. A tevékenységek befejeződésekor a tevékenységek feltöltik eredményadataikat az Azure Storage-ba. Közvetlenül a számítási csomóponton lévő fájlrendszerből is lekérhet fájlokat.
7. Amikor a megfigyelés észleli, hogy a feladat tevékenységei befejeződtek, az ügyfélalkalmazás vagy szolgáltatás letöltheti a kimeneti adatokat további feldolgozás vagy kiértékelés céljából.

Vegye figyelembe, hogy ez a Batch használatának csak egyik módja, és ez a forgatókönyv a szolgáltatás mindössze néhány elérhető funkcióját ismerteti. Futtathat például [párhuzamosan több tevékenységet](batch-parallel-node-tasks.md) mindegyik számítási csomóponton, a [feladat-előkészítési és befejezési tevékenységekkel](batch-job-prep-release.md) pedig előkészítheti a csomópontokat a feladatokhoz, majd tisztítást végezhet.

## <a name="next-steps"></a>Következő lépések
Most, hogy nagy vonalakban áttekintette a Batch szolgáltatás jellemzőit, ideje mélyebbre ásni, hogy megtudja, hogyan használhatja ezt a szolgáltatást a számításigényes párhuzamos számítási feladatok feldolgozásához.

* Olvassa el [Az Azure Batch funkcióinak áttekintése](batch-api-basics.md) című témakört, amely hasznos információkkal szolgál a Batch használatára készülőknek. A cikk a Batch szolgáltatás erőforrásainak, például a készleteknek, csomópontoknak, feladatoknak, tevékenységeknek és sok olyan API funkciónak a részletesebb információit tartalmazza, amelyeket a Batch-alkalmazás kiépítésekor használhat.
* Megismerheti a Batch-megoldások fejlesztéséhez rendelkezésre álló [Batch API-kat és eszközöket](batch-apis-tools.md).
* [Ismerkedjen meg az Azure Batch .NET-es kódtárával](batch-dotnet-get-started.md), hogy megtudja, hogyan használhatja a C# nyelvet és a Batch .NET-es kódtárat egy egyszerű számítási feladat végrehajtásához egy általános Batch-munkafolyamattal. Ennek a cikknek az áttekintése legyen az egyik első lépés, amikor igyekszik elsajátítani a Batch használatát. Az oktatóanyagnak [Python verziója](batch-python-tutorial.md) is van.
* Töltse le a [GitHubon található kódmintákat][github_samples], hogy lássa, hogyan használható a C# és a Python a Batch eszközzel a mintául szolgáló számítási feladatok ütemezése és feldolgozása során.
* Tekintse meg a [Batch képzési tervet][learning_path], amelyben megismerheti a Batch használatának elsajátítása során igénybe vehető erőforrásokat.


[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png
