---
title: Azure Functions beállítása egyéni Azure-szolgáltatók számára
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Azure Function-alkalmazást, és hogyan állíthatja be az egyéni Azure-szolgáltatókkal való együttműködésre
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 6b5ab6948d382a9925c9ced91e04f360ecf51a0e
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173026"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Azure Functions beállítása egyéni Azure-szolgáltatók számára

Az egyéni szolgáltató az Azure és a végpont közötti szerződés. Az egyéni szolgáltatók használatával megváltoztathatja a munkafolyamatokat az Azure-ban. Ebből az oktatóanyagból megtudhatja, hogyan állíthat be egy Azure Function-alkalmazást egyéni szolgáltatói végpontként való működéshez.

## <a name="create-the-azure-function-app"></a>Az Azure Function-alkalmazás létrehozása

> [!NOTE]
> Ebben az oktatóanyagban egy egyszerű szolgáltatási végpontot hoz létre, amely egy Azure Function alkalmazást használ. Az egyéni szolgáltatók azonban bármilyen nyilvánosan elérhető végpontot használhatnak. Az alternatívák közé tartozik az Azure Logic Apps, az Azure API Management és a Azure App Service Web Apps funkciója.

Az oktatóanyag elindításához először kövesse az [első Azure Function-alkalmazás létrehozása a Azure Portalon](../azure-functions/functions-create-first-azure-function.md)című oktatóanyagot. Ez az oktatóanyag létrehoz egy .NET Core webhook-függvényt, amely módosítható a Azure Portal. Emellett az aktuális oktatóanyag alapja is.

## <a name="install-azure-table-storage-bindings"></a>Azure Table Storage-kötések telepítése

Az Azure Table Storage-kötések telepítése:

1. Nyissa meg a HttpTrigger **integráció** lapját.
1. Válassza az **+ új bevitel**lehetőséget.
1. Válassza az **Azure Table Storage**lehetőséget.
1. Telepítse a Microsoft. Azure. webjobs. Extensions. Storage bővítményt, ha még nincs telepítve.
1. A **Table paraméter neve** mezőbe írja be a **tableStorage**nevet.
1. A **tábla neve** mezőbe írja be a **myCustomResources**nevet.
1. A frissített bemeneti paraméter mentéséhez válassza a **Mentés** lehetőséget.

![Az egyéni szolgáltatók áttekintése táblázatos kötéseket mutat](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>REST-alapú HTTP-metódusok frissítése

Az Azure-függvény beállításával adja meg az egyéni szolgáltató REST-kérelmi módszereit:

1. Nyissa meg a HttpTrigger **integráció** lapját.
1. A **kiválasztott http-metódusok**területen válassza a **beolvasás**, **Közzététel**, **Törlés**és **put**lehetőséget.

![Az egyéni szolgáltató áttekintése HTTP-metódusok megjelenítésével](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Azure Resource Manager NuGet-csomagok hozzáadása

> [!NOTE]
> Ha a C# projektfájl hiányzik a projekt könyvtárából, manuálisan is hozzáadhatja. Vagy ekkor megjelenik a Microsoft. Azure. webjobs. Extensions. Storage bővítmény a Function alkalmazásban.

Ezután frissítse a C# projektfájlt, hogy tartalmazza a hasznos NuGet-kódtárakat. Ezek a kódtárak megkönnyítik az egyéni szolgáltatóktól érkező kérések elemzését. Kövesse a [bővítmények a portálról való hozzáadásának](../azure-functions/install-update-binding-extensions-manual.md) lépéseit C# , és frissítse a projektfájlt, hogy tartalmazza a következő csomagok hivatkozásait:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

A következő XML-elem egy példa C# nevű projektfájl:

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

Ebben az oktatóanyagban egy Azure Function-alkalmazást állít be, amely Azure-beli egyéni szolgáltatói végpontként működik.

Ha szeretné megtudni, hogyan hozhat létre egy Rest-beli egyéni szolgáltatói végpontot, tekintse [meg az oktatóanyagot: REST-beli egyéni szolgáltatói végpont](./tutorial-custom-providers-function-authoring.md)létrehozása.

