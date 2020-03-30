---
title: Az Azure Functions beállítása
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Azure-függvényalkalmazást, és hogyan állíthatja be az Azure egyéni szolgáltatókkal való együttműködésre.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: b6984bfdf7ec078d02999bccf77f3592cfbcab27
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649987"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Azure-függvények beállítása egyéni Azure-szolgáltatókhoz

Az egyéni szolgáltató az Azure és a végpont közötti szerződés. Az egyéni szolgáltatókkal módosíthatja a munkafolyamatokat az Azure-ban. Ez az oktatóanyag bemutatja, hogyan állíthat be egy Azure-függvényalkalmazást, amely egyéni szolgáltatói végpontként működik.

## <a name="create-the-azure-function-app"></a>Az Azure függvényalkalmazás létrehozása

> [!NOTE]
> Ebben az oktatóanyagban egy egyszerű szolgáltatásvégpontot hoz létre, amely egy Azure-függvényalkalmazást használ. Az egyéni szolgáltatók azonban bármely nyilvánosan elérhető végpontot használhatnak. Az alternatívák közé tartozik az Azure Logic Apps, az Azure API Management és az Azure App Service Web Apps szolgáltatása.

Az oktatóanyag elindításához először kövesse az oktatóanyag [létrehozása az első Azure-függvény alkalmazást az Azure Portalon.](../../azure-functions/functions-create-first-azure-function.md) Ez az oktatóanyag létrehoz egy .NET core webhook függvényt, amely az Azure Portalon módosítható. Ez is az alapja a jelenlegi tutorial.

## <a name="install-azure-table-storage-bindings"></a>Az Azure Table storage-kötések telepítése

Az Azure Table storage-kötések telepítése:

1. Nyissa meg a HttpTrigger **Integrálás** lapját.
1. Válassza a **+ Új bevitel lehetőséget.**
1. Válassza az **Azure Table Storage lehetőséget.**
1. Telepítse a Microsoft.Azure.WebJobs.Extensions.Storage bővítményt, ha még nincs telepítve.
1. A **Tábla paraméter neve** mezőbe írja be a tableStorage ( **táblatároló**) mezőbe.
1. A **Táblanév** mezőbe írja be a **myCustomResources (SajátEgyéni erőforrások**) mezőbe.
1. A frissített bemeneti paraméter mentéséhez válassza a **Mentés** lehetőséget.

![Egyéni szolgáltató – áttekintés táblakötések megjelenítése](./media/create-custom-provider/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>RESTful HTTP-módszerek frissítése

Az Azure függvény beállítása az egyéni szolgáltatóRE VONATKOZÓ RESTful kérési módszerek restful módszereinek felvételéhez:

1. Nyissa meg a HttpTrigger **Integrálás** lapját.
1. A **Kijelölt HTTP-módszerek**csoportban válassza a **GET**, **POST**, **DELETE**és **PUT**lehetőséget.

![Egyéni szolgáltató – áttekintés a HTTP-módszerekkel](./media/create-custom-provider/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Azure Resource Manager NuGet csomagok hozzáadása

> [!NOTE]
> Ha a C# projektfájl hiányzik a projektkönyvtárból, manuálisan is hozzáadhatja. Vagy akkor jelenik meg, miután a Microsoft.Azure.WebJobs.Extensions.Storage bővítmény telepítve van a függvényalkalmazásban.

Ezután frissítse a C# projektfájlt, hogy hasznos NuGet könyvtárakat tartalmazzon. Ezek a kódtárak megkönnyítik az egyéni szolgáltatóktól érkező kérelmek elemzését. A lépéseket követve [bővítményeket adhat hozzá a portálról,](../../azure-functions/install-update-binding-extensions-manual.md) és frissítse a C# projektfájlt, hogy az tartalmazza a következő csomaghivatkozásokat:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

A következő XML-elem egy C# projektfájl példa:

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

Ebben az oktatóanyagban egy Azure-függvényalkalmazást állít be, amely egyéni Azure-szolgáltatói végpontként működik.

A RESTful egyéni szolgáltatóvégpont okozásáról az [Oktatóanyag: ReSTful egyéni szolgáltatóvégpont készítése című](./tutorial-custom-providers-function-authoring.md)témakörben olvashat.

