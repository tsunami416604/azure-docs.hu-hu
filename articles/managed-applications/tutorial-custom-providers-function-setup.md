---
title: Az Azure Functions beállítása az Azure egyéni szolgáltatók
description: Ez az oktatóanyag ismerteti, hogyan hozhat létre egy Azure-függvényt, és állítsa be az Azure egyéni szolgáltatók a kerül
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7e4de43659db88bfd9aad40cc3b9f1753189bba
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799993"
---
# <a name="setup-azure-functions-for-azure-custom-providers"></a>Az Azure Functions beállítása az Azure egyéni szolgáltatók

Egyéni szolgáltatók lehetővé teszik az Azure-ban a munkafolyamatok testreszabása. Egy egyéni szolgáltató az Azure között létrejött szerződés és a egy `endpoint`. Ebben az oktatóanyagban a folyamat az Azure-függvény beállítása egy egyéni szolgáltató használatára fog áthaladni `endpoint`.

Ebben az oktatóanyagban meg van osztva az alábbi lépéseket:

- Az Azure-függvény létrehozása
- Telepítse az Azure Table-kötések
- Frissítse a RESTful HTTP-metódusok
- Az Azure Resource Manager NuGet-csomagok hozzáadása

Ebben az oktatóanyagban az alábbi oktatóanyagok hoz létre:

- [Az Azure Portalon az első Azure-függvény létrehozása](../azure-functions/functions-create-first-azure-function.md)

## <a name="creating-the-azure-function"></a>Az Azure-függvény létrehozása

> [!NOTE]
> Ebben az oktatóanyagban hozunk létre egy Azure-függvény használatával egy egyszerű service-végpont, azonban egy egyéni szolgáltató használhat bármilyen nyilvánosan elérhető `endpoint`. Az Azure Logic Apps, az Azure API Management és az Azure Web Apps alternatívával néhány nagyszerű.

Ez az oktatóanyag elindításához kövesse az oktatóanyag [az első Azure-függvény létrehozása az Azure Portalon](../azure-functions/functions-create-first-azure-function.md). Az oktatóanyagban létrehoz egy .NET core-webhook függvény módosítható az Azure Portalon.

## <a name="install-azure-table-bindings"></a>Telepítse az Azure Table-kötések

Ebben a szakaszban fog áthaladni gyors lépések az Azure Table storage-kötések telepítéséhez.

1. Keresse meg a `Integrate` a HttpTrigger lapján.
2. Kattintson a `+ New Input`.
3. Válassza a(z) `Azure Table Storage` lehetőséget.
4. Telepítse a `Microsoft.Azure.WebJobs.Extensions.Storage` Ha nem telepítette.
5. Frissítés a `Table parameter name` "tableStorage", és a `Table name` a "myCustomResources".
6. Mentse a frissített bemeneti paraméter.

![Egyéni szolgáltatók áttekintése](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Frissítse a RESTful HTTP-metódusok

Ebben a szakaszban fog áthaladni az egyéni szolgáltató RESTful kérési metódusainak tartalmazza az Azure-függvény beállításának gyors lépéseit.

1. Keresse meg a `Integrate` a HttpTrigger lapján.
2. Frissítés a `Selected HTTP methods` való: GET, PUT, POST és DELETE.

![Egyéni szolgáltatók áttekintése](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="modifying-the-csproj"></a>A csproj módosítása

> [!NOTE]
> A csproj hiányzik a könyvtárból, manuálisan hozzáadni, vagy egyszer meg fog jelenni a `Microsoft.Azure.WebJobs.Extensions.Storage` bővítmény telepítve van a függvényt.

Ezután a Microsoft frissíti a csproj-fájl hasznos NuGet-kódtárak együttese könnyebben egyéni szolgáltatóktól származó bejövő kérelmek elemzése. Kövesse a lépéseket [-kiterjesztések hozzáadása a portálról](../azure-functions/install-update-binding-extensions-manual.md) és frissíti a csproj a következő csomag mutató hivatkozásokat:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Minta csproj-fájl:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>További lépések

Ez a cikk egy Azure-függvényt Azure egyéni szolgáltatóként beállítottuk `endpoint`. Nyissa meg a következő cikkben megtudhatja, hogyan hozzon létre egy egyéni REST-alapú szolgáltató `endpoint`.

- [Oktatóanyag: Jelentésszerkesztő egy REST-alapú egyéni szolgáltatói végpont](./tutorial-custom-providers-function-authoring.md)
