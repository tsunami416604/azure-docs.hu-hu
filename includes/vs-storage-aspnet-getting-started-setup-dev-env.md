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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179020"
---
## <a name="set-up-the-development-environment"></a>A fejlesztési környezet kialakítása

Ez a szakasz végigvezeti a fejlesztői környezet beállítását. Ez magában foglalja egy ASP.NET MVC alkalmazás létrehozását, csatlakoztatott szolgáltatások kapcsolatának hozzáadását, vezérlő hozzáadását és a szükséges névtér-irányelvek megadását.

### <a name="create-an-aspnet-mvc-app-project"></a>ASP.NET MVC alkalmazásprojekt létrehozása

1. Nyissa meg a Visual Studiót.

1. A főmenüben válassza az**Új** > projekt **fájlja** > **parancsot.**

1. Az **Új projekt** párbeszédpanelen válassza a **Web** > **ASP.NET Web Application (.NET Framework)** lehetőséget. A **Név** mezőben adja meg a **StorageAspNet**értéket. Válassza **az OK gombot.**

    ![Képernyőkép az Új projekt párbeszédpanelről](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Az **Új ASP.NET webalkalmazás** párbeszédpanelen válassza az **MVC**elemet, majd az **OK**gombot.

    ![Képernyőkép: Új ASP.NET webalkalmazás párbeszédpanel](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Kapcsolatban álló szolgáltatások használata azure-tárfiókhoz való csatlakozáshoz

1. A **Megoldáskezelőben**kattintson a jobb gombbal a projektre.

1. A helyi menüben válassza a Csatlakoztatott szolgáltatás **hozzáadása** > **parancsot.**

1. A **Csatlakoztatott szolgáltatások** párbeszédpanelen válassza a **Cloud Storage with Azure Storage lehetőséget.**

    ![Képernyőkép: Csatlakoztatott szolgáltatások párbeszédpanel](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. Az **Azure Storage** párbeszédpanelen válassza ki az ehhez az oktatóanyaghoz használandó Azure storage-fiókot. Új Azure-tárfiók létrehozásához válassza **az Új tárfiók létrehozása**lehetőséget, és töltse ki az űrlapot. Miután kiválasztottegy meglévő tárfiókot, vagy létrehozott egy újat, válassza **a Hozzáadás**lehetőséget. A Visual Studio telepíti az Azure Storage NuGet csomagját és a **Web.config**tárolókapcsolati karakterláncát.

1. A **Solution Explorer**ben kattintson a jobb gombbal a **Függőségek**elemre, válassza **a NuGet-csomagok kezelése parancsot,** és adjon hozzá egy NuGet-csomaghivatkozást a Microsoft.Azure.ConfigurationManager legújabb verziójához.

> [!TIP]
> Ha tudni szeretné, hogyan hozhat létre tárfiókot az [Azure Portalon,](https://portal.azure.com)olvassa el a Tárfiók létrehozása című [témakört.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
>
> [Az Azure PowerShell,](../articles/storage/common/storage-powershell-guide-full.md) [az Azure CLI](../articles/storage/common/storage-azure-cli.md)vagy az [Azure Cloud Shell](../articles/cloud-shell/overview.md)használatával is létrehozhat tárfiókot.
