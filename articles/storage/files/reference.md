---
title: Azure Files referenciája
description: Az Azure Storage API-referenciák, a readme-fájlok és az ügyféloldali kódtár csomagjainak megkeresése.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/14/2020
ms.service: storage
ms.topic: conceptual
ms.reviewer: ripohane
ms.openlocfilehash: c5799b7ec9f6ecce1b9203345446b2d3240beb80
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527846"
---
# <a name="azure-files-reference"></a>Azure Files referenciája

A Azure Files API-referenciák, a függvénytár-csomagok, a readme-fájlok és az első lépések cikkeinek megkeresése.

## <a name="net-client-libraries"></a>.NET-ügyféloldali kódtárak

Az alábbi táblázat a Azure Files .NET API-k hivatkozásait és mintáit sorolja fel.

|  Verzió  | Dokumentáció | Csomag | Gyorsútmutató |
| :-------: | ----------------------- | ------- | ---------- |
| 12. x | [A .NET-hez készült ügyféloldali kódtár Azure Files](/dotnet/api/overview/azure/storage.files.shares-readme) | [Csomag (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files/) | &nbsp; |
| 11. x | [Microsoft. Azure. Storage. file névtér](/dotnet/api/microsoft.azure.storage.file) | [Csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/) | [Fejlesztés az Azure Files szolgáltatáshoz a .NET-keretrendszerrel](/azure/storage/files/storage-dotnet-how-to-use-files) |

### <a name="storage-management"></a>Tárolók kezelése

Az alábbi táblázat az Azure Storage Management .NET API-k dokumentációját ismerteti.

|  Verzió  | Dokumentáció | Csomag |
| :-------: | ----------------------- | ------- |
| 16. x | [Microsoft.Azure.Management.Storage](/dotnet/api/microsoft.azure.management.storage) | [Csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) |

### <a name="data-movement"></a>Adatáthelyezés

Az alábbi táblázat az Azure Storage adatátviteli .NET API-jai dokumentációját tartalmazza.

|  Verzió  | Dokumentáció | Csomag |
| :-------: | ----------------------- | ------- |
| 1. x | [Adatáthelyezés](/dotnet/api/microsoft.azure.storage.datamovement) | [Csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement/) |

## <a name="java-client-libraries"></a>Java-ügyféloldali kódtárak

Az alábbi táblázat a Azure Files Java API-k hivatkozásait és mintáit tartalmazza.

|  Verzió  | Dokumentáció | Csomag | Gyorsútmutató |
| :-------: | ----------------------- | ------- | ---------- |
| 12. x | [A Javához készült ügyféloldali kódtár Azure Files](/java/api/overview/azure/storage-file-share-readme) | [Csomag (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-file-share) | &nbsp; |
| 8. x | [com. microsoft. Azure. Storage. file](/java/api/com.microsoft.azure.storage.file) | [Csomag (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) | [Fejlesztés az Azure Files szolgáltatáshoz Javával](/azure/storage/files/storage-java-how-to-use-file-storage) |

### <a name="storage-management"></a>Tárolók kezelése

Az alábbi táblázat az Azure Storage Management Java API-jai dokumentációját tartalmazza.

|  Verzió  | Dokumentáció | Csomag |
| :-------: | ----------------------- | ------- |
| 0.9. x | [com. microsoft. Azure. Management. Storage](/java/api/overview/azure/storage/management) | [Csomag (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-svc-mgmt-storage) |

## <a name="python-client-libraries"></a>Python-ügyféloldali kódtárak

Az alábbi táblázat a Python API-k Azure Files dokumentációját és mintáit sorolja fel.

|  Verzió  | Dokumentáció | Csomag | Gyorsútmutató |
| :-------: | ----------------------- | ------- | ---------- |
| 12. x | [Az Azure Storage ügyféloldali kódtárai a Pythonhoz](/azure/developer/python/sdk/storage/overview?view=storage-py-v12) | [Csomag (PyPI)](https://pypi.org/project/azure-storage-file/12.0.0b4/) | [Példák](/python/api/overview/azure/storage-file-share-readme#examples) |
| 2. x | [Azure Storage ügyféloldali kódtárak v2 a Pythonhoz](/azure/developer/python/sdk/storage/overview?view=storage-py-v2) | [Csomag (PyPI)](https://pypi.org/project/azure-storage-file/2.1.0/) | [Fejlesztés az Azure Files szolgáltatáshoz Pythonnal](/azure/storage/files/storage-python-how-to-use-file-storage) |

## <a name="javascript-client-libraries"></a>JavaScript-ügyféloldali kódtárak

Az alábbi táblázat a Azure Files JavaScript API-k hivatkozásait és mintáit tartalmazza.

|  Verzió  | Dokumentáció | Csomag | Gyorsútmutató |
| :-------: | ----------------------- | ------- | ---------- |
| 12. x | [Az ügyféloldali kódtár Azure Files a JavaScripthez](/javascript/api/overview/azure/storage-file-share-readme) | [Csomag (NPM)](https://www.npmjs.com/package/@azure/storage-file-share) | [Példák](/javascript/api/overview/azure/storage-file-share-readme#examples) |
| 10. x | [@azure/storage-file](/javascript/api/@azure/storage-file) | [Csomag (NPM)](https://www.npmjs.com/package/@azure/storage-file) | &nbsp; |

## <a name="rest-apis"></a>REST API-k

Az alábbi táblázat a Azure Files REST API-k hivatkozásait és mintáit tartalmazza.

| Dokumentáció | Áttekintés |
| ----------------------- | -------- |
| [Fájlszolgáltatások REST API](/rest/api/storageservices/file-service-rest-api) | [A file Service-fogalmak](/rest/api/storageservices/file-service-concepts) |

### <a name="other-rest-reference"></a>További REST-hivatkozás

- Az [Azure Storage import-export REST API](/rest/api/storageimportexport/) segítségével kezelheti az importálási/exportálási feladatokat az adatok blob Storage-ba vagy onnan történő átviteléhez.

## <a name="other-languages-and-platforms"></a>Egyéb nyelvek és platformok

Az alábbi lista a könyvtárakra mutató hivatkozásokat tartalmaz más programozási nyelvekhez és platformokhoz.

- [C++](https://azure.github.io/azure-storage-cpp)
- [Ruby](https://azure.github.io/azure-storage-ruby)
- [PHP](https://azure.github.io/azure-storage-php/)
- [iOS](https://azure.github.io/azure-storage-ios/)
- [Android](https://azure.github.io/azure-storage-android)

## <a name="powershell"></a>PowerShell

A következő táblázat a hivatkozási tartalom legújabb verziójára mutató hivatkozásokat tartalmaz.

| Verzió | Platform |
| ------- | -------- |
|  3. x  | [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/?view=azps-3.8.0) |
|  2. x  | [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/?view=azps-2.8.0) |

## <a name="azure-cli"></a>Azure CLI

- [Azure CLI](/cli/azure/storage)
