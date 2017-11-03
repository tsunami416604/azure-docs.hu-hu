## <a name="set-up-the-development-environment"></a>A fejlesztési környezet kialakítása

Ez a szakasz bemutatja, hogyan állítja be a fejlesztési környezetet, például egy ASP.NET MVC alkalmazás létrehozása, kapcsolódó szolgáltatások-kapcsolat hozzáadása, a vezérlő hozzáadása és adja meg a szükséges névtér irányelvek.

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

1. Az a **kapcsolódó szolgáltatás hozzáadása** párbeszédablakban válassza **Azure Storage**, majd válassza ki **konfigurálása**.

    ![Csatlakoztatott Service párbeszédpanelen](./media/vs-storage-aspnet-getting-started-setup-dev-env/vs-storage-aspnet-getting-started-setup-dev-env-3.png)

1. Az a **Azure Storage** párbeszédpanelen válassza ki a kívánt Azure-tárfiókot, és válassza ki kívánja **Hozzáadás**.
