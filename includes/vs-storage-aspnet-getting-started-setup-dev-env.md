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
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610135"
---
## <a name="set-up-the-development-environment"></a>A fejlesztési környezet kialakítása

Ez a szakasz végigvezeti a fejlesztési környezet beállítása. Ez magában foglalja az ASP.NET MVC alkalmazás létrehozása, csatlakoztatott szolgáltatások-kapcsolat hozzáadása, olyan vezérlő hozzáadását és a szükséges névtér irányelvek megadása.

### <a name="create-an-aspnet-mvc-app-project"></a>Egy ASP.NET MVC-alkalmazás projekt létrehozása

1. Nyissa meg a Visual Studiót.

1. Válassza ki a főmenü **fájl** > **új** > **projekt**.

1. Az a **új projekt** párbeszédpanelen jelölje ki **webes** > **ASP.NET Web Application (.NET Framework)**. Az a **neve** mezőben adja meg **StorageAspNet**. Kattintson az **OK** gombra.

    ![Képernyőkép az Új projekt párbeszédpanelről](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Az a **új ASP.NET-webalkalmazás** párbeszédpanelen jelölje ki **MVC**, majd válassza ki **OK**.

    ![Képernyőfelvétel az új ASP.NET-webalkalmazás párbeszédpanel](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Csatlakoztatott szolgáltatások használatával csatlakozni egy Azure storage-fiók

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a projektre.

1. A helyi menüből válassza ki a **Hozzáadás** > **csatlakoztatott szolgáltatás**.

1. Az a **csatlakoztatott szolgáltatás** párbeszédpanelen jelölje ki **felhőalapú tárolás az Azure Storage**.

    ![A csatlakoztatott szolgáltatások képernyőfelvételen párbeszédpanel](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. Az a **Azure Storage** párbeszédpanelen jelölje be az Azure storage-fiók ebben az oktatóanyagban használható. Egy új Azure-tárfiók létrehozásához válassza **hozzon létre egy új Tárfiókot**, és töltse ki az űrlapot. Vagy egy meglévő tárfiókot, vagy új létrehozása után válassza ki a **Hozzáadás**. A Visual Studio telepíti a NuGet-csomagot az Azure Storage és a egy tárolási kapcsolati karakterlánccal, **Web.config**.

1. A **Megoldáskezelőben**, kattintson a jobb gombbal a **függőségek**, válassza a **NuGet-csomagok kezelése**, és a legújabb verzióra a NuGet csomag hivatkozás hozzáadása Microsoft.Azure.ConfigurationManager.

> [!TIP]
> Megtudhatja, hogyan hozhat létre egy tárfiókot a [az Azure portal](https://portal.azure.com), lásd: [hozzon létre egy tárfiókot](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
>
> Storage-fiók használatával is létrehozhat [Azure PowerShell-lel](../articles/storage/common/storage-powershell-guide-full.md), [Azure CLI-vel](../articles/storage/common/storage-azure-cli.md), vagy [Azure Cloud Shell](../articles/cloud-shell/overview.md).
