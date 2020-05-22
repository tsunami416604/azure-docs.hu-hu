---
title: API-k és eszközök fejlesztők számára
description: Megismerheti az Azure Batch szolgáltatással történő megoldásfejlesztéshez elérhető API-kat és eszközöket.
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7f4138398e28797d8acb6517f33f7f97f2054a93
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780257"
---
# <a name="overview-of-batch-apis-and-tools"></a>A Batch API-k és eszközök áttekintése

A párhuzamos számítási feladatok Azure Batch használatával végzett feldolgozása általában programozott módon történik az egyik Batch API-val. Az Ön által készített ügyfélalkalmazások vagy szolgáltatások a Batch API-k használatával kommunikálhatnak a Batch szolgáltatással. A Batch API-kkal számítási csomópontok készletét, virtuális gépeket vagy felhőszolgáltatásokat hozhat létre és felügyelhet. Ezt követően pedig a feladatok és tevékenységek ütemezésével futtathatja őket ezeken a csomópontokon. 

Hatékonyan dolgozhat fel nagyméretű számítási feladatokat a szervezet számára, vagy szolgáltatási előtérrendszert nyújthat az ügyfeleknek, hogy feladatokat és tevékenységeket futtathassanak – igény szerint vagy ütemterv alapján – egyetlen, több száz vagy akár több ezer csomóponton. Az Azure Batch szolgáltatást a nagyobb munkafolyamatok részeként is felügyelheti olyan eszközökkel, mint például az [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Ha többet szeretne megtudni a Azure Batchban használt szolgáltatásokról és munkafolyamatokról, tekintse meg a [Batch szolgáltatás munkafolyamatait és funkcióit](batch-service-workflow-features.md).
> 
> 

## <a name="azure-accounts-for-batch-development"></a>A Batch-fejlesztéshez szükséges Azure-fiókok
A Batch-megoldások fejlesztésekor a következő fiókokat fogja használni az Azure-előfizetésében:

* **Batch-fiók** – Azure batch erőforrások, beleértve a készleteket, a számítási csomópontokat, a feladatokat és a feladatokat, egy Azure [Batch-fiókkal](accounts.md)vannak társítva. Amikor az alkalmazás egy kérelmet továbbít a Batch szolgáltatás felé, a hitelesítést az Azure Batch-fiók, a fiók URL-címe és egy hozzáférési kulcs vagy Azure Active Directory-jogkivonat használatával hajtja végre a szolgáltatás. Az Azure Portalon vagy programozott módon [hozhat létre Batch-fiókot](batch-account-create-portal.md).
* **Storage-fiók** – A Batch beépített támogatást kínál az [Azure Storage][azure_storage] fájljainak használatához. Szinte mindegyik Batch-forgatókönyv az Azure Blob Storage-ot használja a tevékenységek által futtatott programok és feldolgozott adatok átmeneti tárolásához, valamint a tevékenységek által létrehozott kimeneti adatok tárolásához. Minden batch-fiók általában egy megfelelő Storage-fiókhoz van társítva.

## <a name="batch-service-apis"></a>A Batch szolgáltatás API-jai

Az alkalmazások és szolgáltatások közvetlen REST API-hívásokat hajthatnak végre, illetve a következő ügyfélkódtárak legalább egyikének használatával futtathatják és kezelhetik az Azure Batch számítási feladatait.

| API | API-referencia | Letöltés | Oktatóanyag | Kódminták | További információ |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[docs.microsoft.com][batch_rest] |N/A |- |- | [Támogatott verziók](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[docs.microsoft.com][api_net] |[NuGet][api_net_nuget] |[Oktatóanyag](tutorial-parallel-dotnet.md) |[GitHub][api_sample_net] | [Release Notes (Kibocsátási megjegyzések)](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[docs.microsoft.com][api_python] |[PyPI][api_python_pypi] |[Oktatóanyag](tutorial-parallel-python.md)|[GitHub][api_sample_python] | [Olvass el](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/batch/azure-batch/README.md) |
| **Batch Node.js** |[docs.microsoft.com][api_nodejs] |[NPM][api_nodejs_npm] |[Oktatóanyag](batch-nodejs-get-started.md) |- | [Olvass el](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[docs.microsoft.com][api_java] |[Maven][api_java_jar] |- |[Olvass el][api_sample_java] | [Olvass el](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>Batch Management API-k

A Batch Azure Resource Manager API-jai programozott hozzáférést biztosítanak a Batch-fiókokhoz. Ezen API-k használatával programozott módon kezelheti a Batch-fiókokat, a kvótákat, az alkalmazáscsomagokat és az erőforrásokat a Microsoft.Batch szolgáltató segítségével.  

| API | API-referencia | Letöltés | Oktatóanyag | Kódminták |
| --- | --- | --- | --- | --- |
| **Batch Management REST** |[docs.microsoft.com][api_rest_mgmt] |N/A |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Management .NET** |[docs.microsoft.com][api_net_mgmt] |[NuGet][api_net_mgmt_nuget] | [Oktatóanyag](batch-management-dotnet.md) |[GitHub][api_sample_net] |
| **Batch Management Python** |[docs.microsoft.com][api_python_mgmt] |[PyPI][api_python_mgmt_pypi] |- |- |
| **Batch Management Node.js** |[docs.microsoft.com][api_nodejs_mgmt] |[NPM][api_nodejs_mgmt_npm] |- |- | 
| **Batch Management Java** |- |[Maven][api_java_mgmt_jar] |- |- |
## <a name="batch-command-line-tools"></a>A Batch parancssori eszközei

Ezek a parancssori eszközök ugyanazt a funkcionalitást biztosítják, mint a Batch szolgáltatás API-jai és a Batch Management API-k: 

* [Batch PowerShell-parancsmagok][batch_ps]: Az [Azure PowerShell](/powershell/azure/overview) modulban található Azure Batch-parancsmagokkal felügyelheti a Batch-erőforrásokat a PowerShell használatával.
* [Azure CLI](/cli/azure): Az Azure parancssori felület (Azure CLI) egy többplatformos eszközkészlet, amely rendszerhéjparancsokat biztosít sok Azure-szolgáltatásokkal, például a Batch szolgáltatással és a Batch Management szolgáltatással való interakcióhoz. Az Azure CLI a Batch szolgáltatással való használatával kapcsolatos további információkért lásd: [Batch-erőforrások kezelése az Azure CLI-vel](batch-cli-get-started.md).

## <a name="other-tools-for-application-development"></a>Egyéb alkalmazásfejlesztési eszközök

Íme néhány további eszköz, amelyek hasznosak lehetnek a Batch-alkalmazások és -szolgáltatások létrehozása és hibakeresése során.

* [Azure Portal][portal]: Batch-készleteket, -feladatokat és -tevékenységeket hozhat létre, figyelhet meg és törölhet az Azure Portalon. Megtekintheti ezen és más erőforrások állapotinformációit a feladatok futtatásakor, és fájlokat is letölthet a készletekben található számítási csomópontokról. Letöltheti például egy sikertelen feladat `stderr.txt` fájlját a hibaelhárítás során. Távoli asztali (RDP-) fájlokat is letölthet, amelyekkel bejelentkezhet a számítási csomópontokba.
* [Azure Batch Explorer][batch_labs]: A Batch Explorer (korábbi nevén BatchLabs) egy ingyenes, számos funkcióval ellátott, önálló ügyféleszköz Azure Batch-alkalmazások létrehozásához, hibakereséséhez és monitorozásához. Töltse le a [telepítőcsomagot](https://azure.github.io/BatchExplorer/) Mac, Linux vagy Windows rendszerre.
* [Azure batch hajógyár](https://github.com/Azure/batch-shipyard): a Batch hajógyár egy olyan eszköz, amellyel a tároló-alapú batch-feldolgozási és HPC-munkaterhelések kiépítése, végrehajtása és monitorozása Azure Batch.
* [Azure Storage Explorer][storage_explorer]: Habár nem szigorúan Azure batch eszköz, a Storage Explorer egy másik értékes eszköz a Batch-megoldások fejlesztése és hibakeresése során.

## <a name="additional-resources"></a>További források

- A Batch-alkalmazás eseményeinek naplózásával kapcsolatos információkért lásd: [Események naplózása Batck-alkalmazások diagnosztikai kiértékeléséhez és figyeléséhez](batch-diagnostics.md). A Batch-szolgáltatás által létrehozott események referenciájáért lásd:[Batch-elemzés](batch-analytics.md).
- A számítási csomópontok környezeti változóival kapcsolatos információért lásd: [Azure Batch számítási csomópont környezeti változói](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>További lépések

* Ismerje meg a [Batch szolgáltatás munkafolyamatát és az elsődleges erőforrásokat](batch-service-workflow-features.md) , például a készleteket, a csomópontokat, a feladatokat és a feladatokat.
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
