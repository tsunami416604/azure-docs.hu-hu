---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/15/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a7c696870e22e1692ca5ed778e47f8e4cc00615a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67179020"
---
## <a name="set-up-the-development-environment"></a>A fejlesztési környezet kialakítása

Ez a szakasz végigvezeti a fejlesztési környezet beállításán. Ez magában foglalja a ASP.NET MVC-alkalmazás létrehozását, a csatlakoztatott szolgáltatások kapcsolatának hozzáadását, a vezérlő hozzáadását és a szükséges névtér-irányelvek megadását.

### <a name="create-an-aspnet-mvc-app-project"></a>ASP.NET MVC-alkalmazás projekt létrehozása

1. Nyissa meg a Visual Studiót.

1. A főmenüben válassza a **fájl** > **új** > **projekt**lehetőséget.

1. Az **új projekt** párbeszédpanelen válassza a **web** > **ASP.net Web Application (.NET-keretrendszer)** lehetőséget. A **név** mezőben adja meg a **StorageAspNet**. Kattintson az **OK** gombra.

    ![Képernyőkép az Új projekt párbeszédpanelről](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Az **új ASP.net-webalkalmazás** párbeszédpanelen válassza az **MVC**lehetőséget, majd kattintson **az OK gombra**.

    ![Képernyőkép az új ASP.NET-webalkalmazás párbeszédpanelről](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Csatlakoztatott szolgáltatások használata Azure Storage-fiókhoz való csatlakozáshoz

1. **Megoldáskezelő**kattintson a jobb gombbal a projektre.

1. A helyi menüben válassza a**csatlakoztatott szolgáltatás** **hozzáadása** > elemet.

1. A **csatlakoztatott szolgáltatások** párbeszédpanelen válassza a **felhőalapú tárolás az Azure Storage**-ban lehetőséget.

    ![A csatlakoztatott szolgáltatások párbeszédpanel képernyőképe](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. Az **Azure Storage** párbeszédpanelen válassza ki az oktatóanyaghoz használni kívánt Azure Storage-fiókot. Új Azure Storage-fiók létrehozásához válassza az **új Storage-fiók létrehozása**lehetőséget, és fejezze be az űrlapot. Miután kiválasztotta a meglévő Storage-fiókot, vagy újat hoz létre, válassza a **Hozzáadás**lehetőséget. A Visual Studio telepíti az Azure Storage-hoz készült NuGet-csomagot és egy Storage kapcsolati karakterláncot a **web. config fájlba**.

1. **Megoldáskezelő**kattintson a jobb gombbal a **függőségek**elemre, válassza a **NuGet-csomagok kezelése**lehetőséget, és adjon hozzá egy NuGet-csomagot a Microsoft. Azure. ConfigurationManager legújabb verziójához.

> [!TIP]
> Ha meg szeretné tudni, hogyan hozhat létre egy Storage-fiókot a [Azure Portal](https://portal.azure.com), tekintse meg a [Storage-fiók létrehozása](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)című témakört.
>
> [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Azure CLI](../articles/storage/common/storage-azure-cli.md)vagy [Azure Cloud Shell](../articles/cloud-shell/overview.md)használatával is létrehozhat Storage-fiókot.
