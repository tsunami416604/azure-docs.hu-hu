## <a name="set-up-the-development-environment"></a>A fejlesztési környezet kialakítása

Ez a szakasz végigvezeti a fejlesztési környezet létrehozása. Ez magában foglalja egy ASP.NET MVC alkalmazás létrehozása, kapcsolódó szolgáltatások-kapcsolat hozzáadása, a vezérlő hozzáadása, és adja meg a szükséges névtér irányelvek.

### <a name="create-an-aspnet-mvc-app-project"></a>ASP.NET MVC alkalmazás projekt létrehozása

1. Nyissa meg a Visual Studiót.

1. Válassza ki a főmenü **fájl** > **új** > **projekt**.

1. Az a **új projekt** párbeszédpanelen jelölje ki **webes** > **ASP.NET Web Application (.NET-keretrendszer)**. Az a **neve** mezőben adja meg **StorageAspNet**. Kattintson az **OK** gombra.

    ![Képernyőfelvétel az új projekt párbeszédpanel](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Az a **új ASP.NET-webalkalmazás** párbeszédpanelen jelölje ki **MVC**, majd válassza ki **OK**.

    ![Képernyőfelvétel az új ASP.NET Webalkalmazásként való kezelése párbeszédpanel](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Kapcsolódó szolgáltatások segítségével csatlakozzon az Azure storage-fiók

1. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre.

2. Válassza ki a helyi menüből **Hozzáadás** > **kapcsolódó szolgáltatás**.

1. Az a **kapcsolódó szolgáltatások** párbeszédpanelen jelölje ki **felhőalapú tárolás az Azure Storage**.

    ![Szolgáltatások csatlakoztatott képernyőkép párbeszédpanel](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. Az a **Azure Storage** párbeszédpanelen jelölje ki az Azure storage-fiók ebben az oktatóanyagban használt. Egy új Azure storage-fiók létrehozásához válassza **hozzon létre egy új Tárfiókot**, töltse ki az űrlapot. Válasszon egy meglévő tárfiókot használ, vagy új létrehozása után **Hozzáadás**. A Visual Studio telepíti a NuGet-csomagot az Azure Storage és a tároló kapcsolati karakterláncának **Web.config**.

> [!TIP]
> További információt a storage-fiók létrehozása a [Azure-portálon](https://portal.azure.com), lásd: [hozzon létre egy tárfiókot](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> A storage-fiók használatával is létrehozhat [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Azure CLI](../articles/storage/common/storage-azure-cli.md), vagy [Azure Cloud rendszerhéj](../articles/cloud-shell/overview.md).

