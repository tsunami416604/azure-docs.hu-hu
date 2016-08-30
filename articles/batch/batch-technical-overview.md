<properties
    pageTitle="Az Azure Batch alapjai | Microsoft Azure"
    description="Megismerheti, hogyan használhatja az Azure Batch szolgáltatást nagyméretű párhuzamos és HPC számítási feladatokhoz."
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/02/2016"
    ms.author="marsma"/>

# Az Azure Batch alapjai

Az Azure Batch lehetővé teszi, hogy hatékonyan futtasson nagyméretű párhuzamos és nagy teljesítményű feldolgozási (HPC) alkalmazásokat a felhőben. A platformszolgáltatás számításigényes munkák futtatását ütemezi virtuális gépek felügyelt gyűjteményében, és automatikusan képes méretezni a számítási erőforrásokat a feladatok igényeinek megfelelően.

A Batch szolgáltatással Azure számítási erőforrásokat határoz meg az alkalmazások párhuzamos és méretezhető futtatásához. Igény szerinti vagy ütemezett feladatokat futtathat, és nem kell manuálisan létrehoznia, konfigurálnia és felügyelnie a HPC-fürtöket, az egyes virtuális gépeket, virtuális hálózatokat vagy az összetett feladat- és tevékenységütemezési infrastruktúrát.

## A Batch használati esetei

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

## Fejlesztés a Batch használatával

Az Azure Batch szolgáltatást párhuzamos számítási feladatok feldolgozásához használó megoldásokat a Batch API-k használatával, szoftveresen hozhatja létre. A Batch API-kkal számítási csomópontok (virtuális gépek) készleteit hozhatja létre és felügyelheti, valamint feladatokat és tevékenységeket ütemezhet a csomópontokon való futtatáshoz. Az Ön által készített ügyfélalkalmazások vagy szolgáltatások a Batch API-k használatával kommunikálnak a Batch szolgáltatással. Hatékonyan dolgozhat fel nagyméretű számítási feladatokat a szervezet számára, vagy szolgáltatási előtérrendszert nyújthat az ügyfeleknek, hogy feladatokat és tevékenységeket futtathassanak – igény szerint vagy ütemterv alapján – egy, több száz vagy több ezer csomóponton. A Batch szolgáltatást a nagyobb munkafolyamatok részeként is felügyelheti olyan eszközökkel, mint az [Azure Data Factory][data_factory].

> [AZURE.TIP] Amikor készen áll a Batch API megismerésére az általa nyújtott szolgáltatások alaposabb elsajátítása érdekében, tekintse meg az [Azure Batch-funkciók áttekintésével](batch-api-basics.md) foglalkozó témakört.

### Szükséges Azure-fiókok

Batch-megoldások fejlesztésekor a következő fiókokat fogja használni a Microsoft Azure-ban.

- **Azure-fiók és -előfizetés** – Ha még nincs Azure-előfizetése, aktiválhatja az [MSDN-előfizetői előnyeit][msdn_benefits], vagy regisztrálhat egy [ingyenes Azure-fiókot][free_account]. Fiók létrehozásakor a rendszer egy alapértelmezett előfizetést hoz létre.

- **Batch-fiók** – Amikor az alkalmazások kommunikálnak a Batch szolgáltatással, a fióknév, fiók URL-je és egy elérési kulcs szolgál hitelesítő adatként. Az összes Batch-erőforrás, például a készletek, számítási csomópontok, feladatok és tevékenységek, egy Batch-fiókkal van társítva. Az Azure Portalon [hozhat létre és felügyelhet Batch-fiókot](batch-account-create-portal.md).

- **Storage-fiók** – A Batch beépített támogatást tartalmaz az [Azure Storage][azure_storage] fájljainak használatához. Szinte mindegyik Batch-forgatókönyv az Azure Storage-ot használja – a tevékenységek által futtatott programok és feldolgozott adatok átmeneti tárolásához, valamint a tevékenységek által létrehozott kimeneti adatok tárolásához. A Storage-fiók létrehozásával kapcsolatban tekintse meg a [Tudnivalók az Azure Storage-fiókokról](./../storage/storage-create-storage-account.md) című témakört.

### Batch fejlesztési API-k

A számítási erőforrások felügyeletéhez és a párhuzamos számítási feladatok méretezett futtatásához az alkalmazások és a szolgáltatások közvetlen REST API-hívásokat indíthatnak, egy vagy több ügyfélkönyvtárat használhatnak a következők közül, vagy a kettő kombinációját alkalmazhatják a Batch szolgáltatás használatával.

| API    | API-referencia | Letöltés | Kódminták |
| ----------------- | ------------- | -------- | ------------ |
| **Batch REST** | [MSDN][batch_rest] | N/A | [MSDN][batch_rest] |
| **Batch .NET**    | [MSDN][api_net] | [NuGet ][api_net_nuget] | [GitHub][api_sample_net] |
| **Batch Python**  | [readthedocs.io][api_python] | [PyPI][api_python_pypi] |[GitHub][api_sample_python] |
| **Batch Node.js** | [github.io][api_nodejs] | [npm][api_nodejs_npm] | - |
| **Batch Java** (előzetes verzió) | [github.io][api_java] | [Maven pillanatfelvétel-tár][api_java_jar] | - |

### Batch-erőforráskezelés

Az ügyfél API-k mellett az alábiakkal is felügyelheti az erőforrásokat a Batch-fiókban.

- [Batch PowerShell-parancsmagok][batch_ps]: Az [Azure PowerShell](../powershell-install-configure.md) modulban található Azure Batch-parancsmagokkal felügyelheti a Batch-erőforrásokat a PowerShell használatával.

- [Azure CLI](../xplat-cli-install.md): Az Azure parancssori felület (Azure CLI) egy többplatformos eszközkészlet, amely rendszerhéjparancsokat biztosít sok Azure-szolgáltatással, például a Batch szolgáltatással való interakcióhoz.

- [Batch-kezelési .NET](batch-management-dotnet.md) ügyfélkönyvtár: A [NuGet][api_net_mgmt_nuget] segítségével is elérhető Batch-kezelési .NET ügyfélkönyvtárral szoftveresen felügyelheti a Batch-fiókokat, -kvótákat és -alkalmazáscsomagokat. A felügyeleti könyvtárral kapcsolatos tudnivalók az [MSDN][api_net_mgmt] webhelyen érhetők el.

### Batch-eszközök

Jóllehet nem kötelező a Batch szolgáltatással létrehozni a megoldásokat, ezek az eszközök elengedhetetlennek tekinthetők a Batch-alkalmazások és -szolgáltatások létrehozásakor és hibakeresésekor.

- [Azure Batch Explorer][batch_explorer]: A Batch Explorer a [GitHub][github_samples] webhelyen elérhető Batch .NET-mintaalkalmazások egyike. A Windows megjelenítési alaprendszeren (WPF) alapuló alkalmazást létrehozhatja a Visual Studio 2013-mal vagy 2015-tel, majd a használatával megkeresheti és felügyelheti az erőforrásokat a Batch-fiókban a Batch megoldások fejlesztésekor és hibakeresésekor. Megtekintheti a feladatok, készletek és tevékenységek részleteit, fájlokat tölthet le a számítási csomópontokról, vagy akár távolról is csatlakozhat csomópontokhoz a Batch Explorer felületéről mindössze néhány kattintással beszerezhető távoli asztali (RDP-) fájlokkal.

- [Microsoft Azure Storage Explorer][storage_explorer]: Bár nem kimondottan Azure Batch-eszköz, a Storage Explorer egy másik értékes eszköz a Batch-megoldások fejlesztésekor és hibakeresésekor.

## Forgatókönyv: Párhuzamos számítási feladat horizontális felskálázása

A Batch szolgáltatással való interakcióhoz Batch API-kat használó általános megoldás a belsőleg párhuzamos munkák horizontálisan felskálázásával jár – például a 3D jelenetek képeit rendereli – a számítási csomópontok készletén. A számítási csomópontok ezen készlete a „renderelési farm” lehet, amely több tíz, több száz vagy akár több ezer magot biztosít például a renderelési feladathoz.

Az alábbi diagram egy általános Batch-munkafolyamatot mutat be, ahol az ügyfélalkalmazás vagy az üzemeltetett szolgáltatás Batch szolgáltatással futtat párhuzamos számítási feladatot.

![Batch-megoldás munkafolyamata][2]

Ebben az általános forgatókönyvben az alkalmazás vagy a szolgáltatás számítási feladatot dolgoz fel az Azure Batch szolgáltatásban a következő lépések végrehajtásával:

1. Töltse fel a **bemeneti fájlokat** és az **alkalmazást**, amely fel fogja dolgozni ezeket a fájlokat az Azure Storage-fiókban. A bemeneti fájlok bármely olyan adatok lehetnek, amelyet az alkalmazás fel fog dolgozni, például pénzügyi modellezési adatok vagy átkódolni kívánt videofájlok. Az alkalmazásfájlok az adatok feldolgozásához használt bármilyen alkalmazások lehetnek, például 3D renderelési alkalmazások vagy adathordozó-átkódolók.

2. Hozza létre a számítási csomópontok Batch-**készletét** a Batch-fiókban – ezek a feladatokat végrehajtó virtuális gépek. Olyan tulajdonságokat adhat meg, mint a [csomópont mérete](./../cloud-services/cloud-services-sizes-specs.md), operációs rendszere és helye azon alkalmazás Azure Storage tárolójában, amelyet akkor telepít, amikor a csomópontok a készlethez csatlakoznak (az 1. lépésben feltöltött alkalmazás). A készletet [automatikus méretezésre](batch-automatic-scaling.md) is konfigurálhatja – dinamikusan állítsa be a számítási csomópontok számát a készletben – a tevékenységek által létrehozott számítási feladatokra válaszul.

3. Hozzon létre egy Batch-**feladatot** a számítási feladat futtatásához a számítási csomópontok készletén. Feladat létrehozásakor társítsa azt a Batch-készlettel.

4. Adjon hozzá **tevékenységeket** a feladathoz. Amikor tevékenységeket ad hozzá egy munkához, a Batch szolgáltatás automatikusan ütemezi a tevékenységeket a készletben lévő számítási csomópontokon. Mindegyik tevékenység a bemeneti fájlok feldolgozásához feltöltött alkalmazást használja.

    - 4a. A tevékenység végrehajtása előtt a tevékenység letöltheti azon adatokat (a bemeneti fájlokat), amelyeket fel kell dolgoznia a hozzárendelt számítási csomóponton. Ha az alkalmazás még nem lett telepítve a csomóponton (lásd a 2. lépést), helyette ide tölthető le. Amikor a letöltések elkészültek, a tevékenységek lefutnak a hozzájuk rendelt csomópontokon.

5. A tevékenységek futtatásakor lekérdezheti a Batch szolgáltatást a feladat és a tevékenységei állapotának megfigyeléséhez. Az ügyfélalkalmazás vagy szolgáltatás a HTTPS protokollon keresztül kommunikál a Batch szolgáltatással, és mivel esetleg több ezer számítási csomóponton futó több ezer tevékenységek figyel meg, [hatékonyan kérdezze le a Batch szolgáltatást](batch-efficient-list-queries.md).

6. A tevékenységek befejeződésekor a tevékenységek feltöltik eredményadataikat az Azure Storage-ba. Közvetlenül a számítási csomópontokról is lekérhet fájlokat.

7. Amikor a megfigyelés észleli, hogy a feladat tevékenységei befejeződtek, az ügyfélalkalmazás vagy szolgáltatás letöltheti a kimeneti adatokat további feldolgozás vagy kiértékelés céljából.

Vegye figyelembe, hogy ez a Batch használatának csak egyik módja, és ez a forgatókönyv a szolgáltatás mindössze néhány elérhető funkcióját ismerteti. Futtathat például [párhuzamosan több tevékenységet](batch-parallel-node-tasks.md) mindegyik számítási csomóponton, a[feladat-előkészítési és befejezési tevékenységekkel](batch-job-prep-release.md) pedig előkészítheti a csomópontokat a feladatokhoz, majd tisztítást végezhet.

## Következő lépések

Most, hogy látott egy példa Batch-forgatókönyvet, ideje, hogy mélyebben megismerjük a szolgáltatást, hogy megtudja, hogyan használhatja a számításigényes párhuzamos számítási feladatok feldolgozásához.

- [Ismerkedjen meg az Azure Batch .NET-es kódtárával](batch-dotnet-get-started.md), hogy megtudja, hogyan használhatja a C# nyelvet és a Batch .NET-es kódtárat a fent leírt módszerek végrehajtásához. Ennek kell az egyik első megállónak lennie a Batch szolgáltatás használatának elsajátításakor.

- Tekintse meg a [Batch-funkció áttekintésével](batch-api-basics.md) foglalkozó témakört, amelyben részletes információkat olvashat a Batch által a nagy számításigényű számítási feladatok feldolgozásához nyújtott API-funkciókkal kapcsolatban.

- A Batch Explorer mellett a [GitHub többi kódmintája][github_samples] mutatja be, hogyan használhatja a Batch számos funkcióját a Batch .NET-es kódtárával.

- Tekintse meg a [Batch képzési tervet][learning_path], amelyben megismerheti az elérhető erőforrásokat a Batch használatának elsajátításakor.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: http://azure.github.io/azure-sdk-for-java/
[api_java_jar]: http://adxsnapshots.azurewebsites.net/?dir=com%5cmicrosoft%5cazure%5cazure-batch
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_nuget]: https://www.nuget.org/packages/Azure.Batch/
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: http://azure.github.io/azure-sdk-for-node/azure-batch/latest/
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_python]: http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.batch.html
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[batch_explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch_ps]: https://msdn.microsoft.com/library/azure/mt125957.aspx
[batch_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[data_factory]: https://azure.microsoft.com/documentation/services/data-factory/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-technical-overview/tech_overview_01.png
[2]: ./media/batch-technical-overview/tech_overview_02.png



<!--HONumber=jun16_HO2-->


