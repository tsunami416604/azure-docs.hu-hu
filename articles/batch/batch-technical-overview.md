---
title: "Az Azure Batch szolgáltatás áttekintése | Microsoft Docs"
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
ms.date: 01/23/2017
ms.author: tamram
translationtype: Human Translation
ms.sourcegitcommit: bf22cd3426e936c8d74377f59443e5e1a6834286
ms.openlocfilehash: 3a01764b6a0e777e947cf8648606cdd568d118ff


---
# <a name="basics-of-azure-batch"></a>Az Azure Batch alapjai
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

Az Azure által biztosított Batch szolgáltatás és az egyéb HPC-megoldások összehasonlítását lásd: [Batch és HPC-megoldások](batch-hpc-solutions.md).

[!INCLUDE [batch-pricing-include](../../includes/batch-pricing-include.md)]

## <a name="developing-with-batch"></a>Fejlesztés a Batch használatával
A párhuzamos számítási feladatok Azure Batch használatával végzett feldolgozása általában programozott módon történik az egyik [Batch API-val](#batch-development-apis). Az Ön által készített ügyfélalkalmazások vagy szolgáltatások a Batch API-k használatával kommunikálhatnak a Batch szolgáltatással. A Batch API-kkal számítási csomópontok készletét, virtuális gépeket vagy felhőszolgáltatásokat hozhat létre és felügyelhet. Ezt követően pedig a feladatok és tevékenységek ütemezésével futtathatja őket ezeken a csomópontokon. 

Hatékonyan dolgozhat fel nagyméretű számítási feladatokat a szervezet számára, vagy szolgáltatási előtérrendszert nyújthat az ügyfeleknek, hogy feladatokat és tevékenységeket futtathassanak – igény szerint vagy ütemterv alapján – egyetlen, több száz vagy akár több ezer csomóponton. Az Azure Batch szolgáltatást a nagyobb munkafolyamatok részeként is felügyelheti olyan eszközökkel, mint például az [Azure Data Factory](../data-factory/data-factory-data-processing-using-batch.md).

> [!TIP]
> Amikor készen áll a Batch API megismerésére, az általa nyújtott szolgáltatások alaposabb elsajátítása érdekében tekintse meg [Az Azure Batch funkcióinak áttekintése](batch-api-basics.md) című témakört.
> 
> 

### <a name="azure-accounts-youll-need"></a>Szükséges Azure-fiókok
Batch-megoldások fejlesztésekor a következő fiókokat fogja használni a Microsoft Azure-ban.

* **Azure-fiók és -előfizetés** – Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit][msdn_benefits], vagy regisztrálhat egy [ingyenes Azure-fiókot][free_account]. Fiók létrehozásakor a rendszer egy alapértelmezett előfizetést hoz létre.
* **Batch-fiók** – Az Azure Batch-erőforrások, például a készletek, számítási csomópontok, feladatok és tevékenységek, egy Batch-fiókkal vannak társítva. Amikor az alkalmazás egy kérelmet továbbít a Batch szolgáltatás felé, a hitelesítést az Azure Batch-fiók, a fiók URL-címe és egy hozzáférési kulcs használatával hajtja végre a szolgáltatás. Az Azure Portalon [hozhat létre Batch-fiókot](batch-account-create-portal.md).
* **Storage-fiók** – A Batch beépített támogatást kínál az [Azure Storage][azure_storage] fájljainak használatához. Szinte mindegyik Batch-forgatókönyv az Azure Blob Storage-ot használja a tevékenységek által futtatott programok és feldolgozott adatok átmeneti tárolásához, valamint a tevékenységek által létrehozott kimeneti adatok tárolásához. A Storage-fiók létrehozásával kapcsolatban tekintse meg a [Tudnivalók az Azure Storage-fiókokról](../storage/storage-create-storage-account.md) című témakört.

### <a name="batch-development-apis"></a>Batch fejlesztési API-k
Az alkalmazások és szolgáltatások közvetlen REST API-hívásokat hajthatnak végre, illetve a következő ügyfélkódtárak legalább egyikének használatával futtathatják és kezelhetik az Azure Batch számítási feladatait.

| API | API-referencia | Letöltés | Oktatóanyag | Kódminták |
| --- | --- | --- | --- | --- |
| **Batch REST** |[MSDN][batch_rest] |N/A |- |- |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[Oktatóanyag](batch-dotnet-get-started.md) |[GitHub][api_sample_net] |
| **Batch Python** |[readthedocs.io][api_python] |[PyPI][api_python_pypi] |[Oktatóanyag](batch-python-tutorial.md)|[GitHub][api_sample_python] |
| **Batch Node.js** |[github.io][api_nodejs] |[npm][api_nodejs_npm] |- |- |
| **Batch Java** (előzetes verzió) |[github.io][api_java] |[Maven][api_java_jar] |- |[GitHub][api_sample_java] |

### <a name="batch-command-line-tools"></a>A Batch parancssori eszközei

A fejlesztői API-k által biztosított funkciók szintén elérhetők a parancssori eszközök használatával: 

* [Batch PowerShell-parancsmagok][batch_ps]: Az [Azure PowerShell](/powershell/azureps-cmdlets-docs) modulban található Azure Batch-parancsmagokkal felügyelheti a Batch-erőforrásokat a PowerShell használatával.
* [Azure CLI](../xplat-cli-install.md): Az Azure parancssori felület (Azure CLI) egy többplatformos eszközkészlet, amely rendszerhéjparancsokat biztosít sok Azure-szolgáltatással, például a Batch szolgáltatással való interakcióhoz.

### <a name="batch-resource-management"></a>Batch-erőforráskezelés

A Batch Azure Resource Manager API-jai programozott hozzáférést biztosítanak a Batch-fiókokhoz. Ezen API-k használatával programozott módon kezelheti a Batch-fiókokat, a kvótákat és az alkalmazáscsomagokat.  

| API | API-referencia | Letöltés | Oktatóanyag | Kódminták |
| --- | --- | --- | --- | --- |
| **Batch Resource Manager REST** |[docs.microsoft.com][api_rest_mgmt] |N/A |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Resource Manager .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [Oktatóanyag](batch-management-dotnet.md) |[GitHub][api_sample_net] |


### <a name="batch-tools"></a>Batch-eszközök
Noha nem kötelező a Batch szolgáltatással létrehozni a megoldásokat, itt megtalálható néhány hasznos eszköz a Batch-alkalmazások és -szolgáltatások létrehozásához és hibakereséséhez.

* [Azure Portal][portal]: Batch-készleteket, -feladatokat és -tevékenységeket hozhat létre, figyelhet meg és törölhet az Azure Portal Batch-paneljein. Megtekintheti ezen és más erőforrások állapotinformációit a feladatok futtatásakor, és fájlokat is letölthet a készletekben található számítási csomópontokról (letöltheti például egy sikertelen feladat `stderr.txt` fájlját hibaelhárításkor). Távoli asztali (RDP-) fájlokat is letölthet, amelyekkel bejelentkezhet a számítási csomópontokba.
* [Azure Batch Explorer][batch_explorer]: A Batch Explorer hasonló Batch-erőforráskezelő funkciókat nyújt, mint az Azure Portal, de egy különálló Windows megjelenítési alaprendszer (WPF) ügyfélalkalmazás részeként. A [GitHubon][github_samples] elérhető egyik Batch .NET mintaalkalmazás, amelyet a Visual Studio 2015-ös vagy újabb verziójával építhet ki, majd a használatával megkeresheti és felügyelheti az erőforrásokat a Batch-fiókban a Batch-megoldások fejlesztésekor és hibakeresésekor. Megtekintheti a feladatok, készletek és tevékenységek részleteit, fájlokat tölthet le a számítási csomópontokról, és távolról csatlakozhat csomópontokhoz a Batch Explorerrel letölthető távoli asztali (RDP-) fájlokkal.
* [Microsoft Azure Storage Explorer][storage_explorer]: Bár nem kimondottan Azure Batch-eszköz, a Storage Explorer egy másik értékes eszköz a Batch-megoldások fejlesztésekor és hibakeresésekor.

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
* [Ismerkedjen meg az Azure Batch .NET-es kódtárával](batch-dotnet-get-started.md), hogy megtudja, hogyan használhatja a C# nyelvet és a Batch .NET-es kódtárat egy egyszerű számítási feladat végrehajtásához egy általános Batch-munkafolyamattal. Ennek a cikknek az áttekintése legyen az egyik első lépés, amikor igyekszik elsajátítani a Batch használatát. Az oktatóanyagnak [Python verziója](batch-python-tutorial.md) is van.
* Töltse le a [GitHubon található kódmintákat][github_samples], hogy lássa, hogyan használható a C# és a Python a Batch eszközzel a mintául szolgáló számítási feladatok ütemezése és feldolgozása során.
* Tekintse meg a [Batch képzési tervet][learning_path], amelyben megismerheti a Batch használatának elsajátítása során igénybe vehető erőforrásokat.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_rest_mgmt]: https://docs.microsoft.com/en-us/rest/api/batchmanagement/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[storage_explorer]: http://storageexplorer.com/
[portal]: https://portal.azure.com

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png



<!--HONumber=Jan17_HO4-->


