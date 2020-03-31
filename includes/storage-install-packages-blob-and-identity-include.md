---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806582"
---
## <a name="install-client-library-packages"></a>Ügyféltárcsomagok telepítése

> [!NOTE]
> Az itt látható példák az Azure Storage-ügyfélkódtár 12-es verzióját használják. A 12-es verziójú ügyfélkódtár az Azure SDK része. Az Azure SDK-ról további információt az Azure SDK-tárház [a GitHubon](https://github.com/Azure/azure-sdk)című témakörben talál.

A Blob storage csomag telepítéséhez futtassa a következő parancsot a NuGet csomagkezelő konzoljáról:

```powershell
Install-Package Azure.Storage.Blobs
```

Az itt látható példák is használja a legújabb verzióját az [Azure Identity ügyfélkódtár .NET](https://www.nuget.org/packages/Azure.Identity/) az Azure AD hitelesítő adatokkal való hitelesítéshez. A csomag telepítéséhez futtassa a következő parancsot a NuGet csomagkezelő konzoljáról:

```powershell
Install-Package Azure.Identity
```
