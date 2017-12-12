## <a name="set-up-the-development-environment"></a>A fejlesztési környezet kialakítása

Ez a szakasz végigvezeti a fejlesztési környezet, például egy ASP.NET MVC alkalmazás létrehozása, kapcsolódó szolgáltatások-kapcsolat hozzáadása, a vezérlő hozzáadása és adja meg a szükséges névtér irányelvek létrehozása.

### <a name="create-an-aspnet-mvc-app-project"></a>ASP.NET MVC alkalmazás projekt létrehozása

1. Nyissa meg a Visual Studiót.

1. Válassza ki **fájl -> Új -> projekt** a főmenüből

1. Az a **új projekt** párbeszédpanelen adja meg a beállításokat az alábbi ábrán kiemelt:

    ![ASP.NET-projekt létrehozása](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-1.png)

1. Kattintson az **OK** gombra.

1. Az a **új ASP.NET projekt** párbeszédpanelen adja meg a beállításokat az alábbi ábrán kiemelt:

    ![Adja meg az MVC](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-2.png)

1. Kattintson az **OK** gombra.

### <a name="use-connected-services-to-connect-to-an-azure-storage-account"></a>Kapcsolódó szolgáltatások segítségével csatlakozzon az Azure storage-fiók

1. Az a **Megoldáskezelőben**, kattintson jobb gombbal a projektre, és válassza ki a helyi menüből **Hozzáadás -> kapcsolódó szolgáltatási**.

1. Az a **kapcsolódó szolgáltatás hozzáadása** párbeszédablakban válassza **felhőalapú tárolás az Azure Storage**, majd válassza ki **konfigurálása**.

    ![Csatlakoztatott Service párbeszédpanelen](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. Az a **Azure Storage** párbeszédpanelen jelölje be az Azure Storage-fiók ebben az oktatóanyagban használt.  Egy új Azure Storage-fiók létrehozásához kattintson a **hozzon létre egy új Tárfiókot** töltse ki az űrlapot.  Egy meglévő tárfiókot használ, vagy új létrehozása után kattintson **Hozzáadás**.  A Visual Studio NuGet-csomag telepítése az Azure Storage és a tároló kapcsolati karakterláncának **Web.config**.

> [!TIP]
> További információt a storage-fiók létrehozása a [Azure-portálon](https://portal.azure.com), lásd: [hozzon létre egy tárfiókot](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account).
>
> Egy Azure storage-fiók használatával is létrehozhatók [Azure PowerShell](../articles/storage/common/storage-powershell-guide-full.md), [Azure CLI](../articles/storage/common/storage-azure-cli.md), vagy a [Azure Cloud rendszerhéj](../articles/cloud-shell/overview.md).

