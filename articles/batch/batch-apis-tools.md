---
title: API-k és eszközök fejlesztők számára – Azure Batch |} A Microsoft Docs
description: Megismerheti az Azure Batch szolgáltatással történő megoldásfejlesztéshez elérhető API-kat és eszközöket.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: get-started-article
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: e1df3fd721461d2a7ca2dd9f4bdd9c783c55b35c
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594389"
---
# <a name="overview-of-batch-apis-and-tools"></a>A Batch API-k és eszközök áttekintése

Az Azure Batch segítségével párhuzamos számítási feladatok feldolgozására általában történik programozott módon a Batch API-k használatával. Az Ön által készített ügyfélalkalmazások vagy szolgáltatások a Batch API-k használatával kommunikálhatnak a Batch szolgáltatással. A Batch API-kkal számítási csomópontok készletét, virtuális gépeket vagy felhőszolgáltatásokat hozhat létre és felügyelhet. Ezt követően pedig a feladatok és tevékenységek ütemezésével futtathatja őket ezeken a csomópontokon. 

Hatékonyan dolgozhat fel nagyméretű számítási feladatokat a szervezet számára, vagy szolgáltatási előtérrendszert nyújthat az ügyfeleknek, hogy feladatokat és tevékenységeket futtathassanak – igény szerint vagy ütemterv alapján – egyetlen, több száz vagy akár több ezer csomóponton. Az Azure Batch szolgáltatást a nagyobb munkafolyamatok részeként is felügyelheti olyan eszközökkel, mint például az [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Amikor készen áll a Batch API megismerésére, az általa nyújtott szolgáltatások alaposabb elsajátítása érdekében tekintse meg [Az Azure Batch funkcióinak áttekintése](batch-api-basics.md) című témakört.
> 
> 

## <a name="azure-accounts-for-batch-development"></a>A Batch-fejlesztéshez szükséges Azure-fiókok
A Batch-megoldások fejlesztésekor a következő fiókokat fogja használni az Azure-előfizetésében:

* **Batch-fiók** – Az Azure Batch-erőforrások, például a készletek, számítási csomópontok, feladatok és tevékenységek egy Azure [Batch-fiókkal](batch-api-basics.md#account) vannak társítva. Amikor az alkalmazás egy kérelmet továbbít a Batch szolgáltatás felé, a hitelesítést az Azure Batch-fiók, a fiók URL-címe és egy hozzáférési kulcs vagy Azure Active Directory-jogkivonat használatával hajtja végre a szolgáltatás. Az Azure Portalon vagy programozott módon [hozhat létre Batch-fiókot](batch-account-create-portal.md).
* **Storage-fiók** – A Batch beépített támogatást kínál az [Azure Storage][azure_storage] fájljainak használatához. Szinte mindegyik Batch-forgatókönyv az Azure Blob Storage-ot használja a tevékenységek által futtatott programok és feldolgozott adatok átmeneti tárolásához, valamint a tevékenységek által létrehozott kimeneti adatok tárolásához. A Batch szolgáltatásban elérhető tárfiók-lehetőségekről további információt [a Batch funkcióinak áttekintésében](batch-api-basics.md#azure-storage-account) talál.

## <a name="batch-service-apis"></a>A Batch szolgáltatás API-jai

Az alkalmazások és szolgáltatások közvetlen REST API-hívásokat hajthatnak végre, illetve a következő ügyfélkódtárak legalább egyikének használatával futtathatják és kezelhetik az Azure Batch számítási feladatait.

| API | API-referencia | Letöltés | Oktatóanyag | Kódminták | További információ |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[docs.microsoft.com][batch_rest] |– |- |- | [Támogatott verziók](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet ][api_net_nuget] |[Oktatóanyag](tutorial-parallel-dotnet.md) |[GitHub][api_sample_net] | [Kibocsátási megjegyzések](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[docs.microsoft.com][api_python] |[PyPI][api_python_pypi] |[Oktatóanyag](tutorial-parallel-python.md)|[GitHub][api_sample_python] | [Olvass el](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Batch Node.js** |[docs.microsoft.com][api_nodejs] |[npm][api_nodejs_npm] |[Oktatóanyag](batch-nodejs-get-started.md) |- | [Olvass el](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[docs.microsoft.com][api_java] |[Maven][api_java_jar] |- |[Olvass el][api_sample_java] | [Olvass el](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>Batch Management API-k

A Batch Azure Resource Manager API-jai programozott hozzáférést biztosítanak a Batch-fiókokhoz. Ezen API-k használatával programozott módon kezelheti a Batch-fiókokat, a kvótákat, az alkalmazáscsomagokat és az erőforrásokat a Microsoft.Batch szolgáltató segítségével.  

| API | API-referencia | Letöltés | Oktatóanyag | Kódminták |
| --- | --- | --- | --- | --- |
| **Batch Management REST** |[docs.microsoft.com][api_rest_mgmt] |– |- |[GitHubon](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Management .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet ][api_net_mgmt_nuget] | [Oktatóanyag](batch-management-dotnet.md) |[GitHub][api_sample_net] |
| **Batch Management Python** |[docs.microsoft.com][api_python_mgmt] |[PyPI][api_python_mgmt_pypi] |- |- |
| **Batch Management Node.js** |[docs.microsoft.com][api_nodejs_mgmt] |[npm][api_nodejs_mgmt_npm] |- |- | 
| **Batch Management Java** |- |[Maven][api_java_mgmt_jar] |- |- |
## <a name="batch-command-line-tools"></a>A Batch parancssori eszközei

Ezek a parancssori eszközök ugyanazt a funkcionalitást biztosítják, mint a Batch szolgáltatás API-jai és a Batch Management API-k: 

* [Batch PowerShell-parancsmagok][batch_ps]: Az Azure Batch-parancsmagokkal a [Azure PowerShell-lel](/powershell/azure/overview) modul lehetővé teszi a Batch-erőforrások kezelése a PowerShell.
* [Az Azure CLI](/cli/azure): Az Azure CLI-vel egy többplatformos eszközkészlet, amely rendszerhéjparancsokat biztosít sok Azure-szolgáltatások, például a Batch szolgáltatás és a Batch Management szolgáltatással való interakcióhoz. Az Azure CLI a Batch szolgáltatással való használatával kapcsolatos további információkért lásd: [Batch-erőforrások kezelése az Azure CLI-vel](batch-cli-get-started.md).

## <a name="other-tools-for-application-development"></a>Egyéb alkalmazásfejlesztési eszközök

Íme néhány további eszköz, amelyek hasznosak lehetnek a Batch-alkalmazások és -szolgáltatások létrehozása és hibakeresése során.

* [Az Azure portal][portal]: Létrehozása, figyelése, és törölje a Batch készletek, feladatok és tevékenységek az Azure Portalon. Megtekintheti ezen és más erőforrások állapotinformációit a feladatok futtatásakor, és fájlokat is letölthet a készletekben található számítási csomópontokról. Letöltheti például egy sikertelen feladat `stderr.txt` fájlját a hibaelhárítás során. Távoli asztali (RDP-) fájlokat is letölthet, amelyekkel bejelentkezhet a számítási csomópontokba.
* [Az Azure Batch Explorer][batch_labs]: A Batch Explorer (korábbi nevén BatchLabs) egy ingyenes, számos funkcióval ellátott, különálló ügyféleszköz létrehozásához, hibakereséséhez és monitorozásához az Azure Batch-alkalmazások. Töltse le a [telepítőcsomagot](https://azure.github.io/BatchExplorer/) Mac, Linux vagy Windows rendszerre.
* [Az Azure Batch hajógyárnak](https://github.com/Azure/batch-shipyard): A Batch hajógyárnak egy olyan eszköz kiépítése, hajtsa végre, és tároló-alapú kötegelt feldolgozások és a HPC számítási feladatok Azure Batch figyelésére.
* [Az Azure Storage Explorer][storage_explorer]: Bár nem kimondottan Azure Batch-eszköz a Storage Explorer egy másik értékes eszköz kell számolnia, amíg a fejlesztés és hibakeresés a Batch-megoldások.

## <a name="additional-resources"></a>További források

- A Batch-alkalmazás eseményeinek naplózásával kapcsolatos információkért lásd: [Események naplózása Batck-alkalmazások diagnosztikai kiértékeléséhez és figyeléséhez](batch-diagnostics.md). A Batch-szolgáltatás által létrehozott események referenciájáért lásd:[Batch-elemzés](batch-analytics.md).
- A számítási csomópontok környezeti változóival kapcsolatos információért lásd: [Azure Batch számítási csomópont környezeti változói](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>További lépések

* Olvassa el [Az Azure Batch funkcióinak áttekintése](batch-api-basics.md) című témakört, amely hasznos információkkal szolgál a Batch használatára készülőknek. A cikk a Batch szolgáltatás erőforrásainak, például a készleteknek, csomópontoknak, feladatoknak, tevékenységeknek és sok olyan API funkciónak a részletesebb információit tartalmazza, amelyeket a Batch-alkalmazás kiépítésekor használhat.
* [Ismerkedjen meg az Azure Batch .NET-es kódtárával](tutorial-parallel-dotnet.md), hogy megtudja, hogyan használhatja a C# nyelvet és a Batch .NET-es kódtárat egy egyszerű számítási feladat végrehajtásához egy általános Batch-munkafolyamattal. Egy [Python-verzió](tutorial-parallel-python.md) és egy [Node.js-oktatóanyag](batch-nodejs-get-started.md) is elérhető.
* Töltse le a [GitHubon található kódmintákat][github_samples], hogy lássa, hogyan használható a C# és a Python a Batch eszközzel a mintául szolgáló számítási feladatok ütemezése és feldolgozása során.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: /java/api/overview/azure/batch
[api_java_mgmt]: /java/api/overview/azure/batch/managementapi
[api_java_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_java_mgmt_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: /dotnet/api/overview/azure/batch/
[api_net_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[api_rest_mgmt]: /rest/api/batchmanagement/
[api_net_mgmt]: /dotnet/api/overview/azure/batch/management
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: /javascript/api/overview/azure/batch/client
[api_nodejs_mgmt]: /javascript/api/overview/azure/batch/management
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_nodejs_mgmt_npm]: https://www.npmjs.com/package/azure-arm-batch
[api_python]: /python/api/overview/azure/batch/client
[api_python_mgmt]: /python/api/overview/azure/batch/management
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_python_mgmt_pypi]: https://pypi.python.org/pypi/azure-mgmt-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/module/az.batch/
[batch_rest]: /rest/api/batchservice/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_labs]: https://azure.github.io/BatchExplorer/
[storage_explorer]: https://storageexplorer.com/
[portal]: https://portal.azure.com
