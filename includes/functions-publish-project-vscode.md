

## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

A Visual Studio Code lehetővé teszi a függvényprojekt közzétételét közvetlenül az Azure-ba. A folyamat során létrehoz egy függvényalkalmazást és az azokhoz tartozó erőforrásokat az Azure-előfizetésében. A függvényalkalmazás végrehajtási környezetet biztosít a függvények számára. A projektet a rendszer becsomagolja, és az Azure-előfizetésben lévő új függvényalkalmazásban helyezi üzembe. 

A cikk azt feltételezi, hogy Ön új függvényalkalmazást hoz létre. Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban.

1. Az **Azure: Functions** területen válassza a Deploy to Function App (Üzembe helyezés függvényalkalmazásban) ikont.

    ![A függvényalkalmazás beállításai](./media/functions-publish-project-vscode/function-app-publish-project.png)

1. Válassza ki a projekt mappáját, amely a jelenlegi munkaterület.

1. Ha egynél több előfizetéssel rendelkezik, akkor válassza ki azt, amelyben üzemeltetni kívánja a függvényalkalmazását. Ezután válassza a **+ Create New Function App** (Új függvényalkalmazás létrehozása) lehetőséget.

1. Írjon be egy globálisan egyedi nevet a függvényalkalmazás azonosításához, majd nyomja le az Enter billentyűt. A függvényalkalmazás nevéhez használható érvényes karakterek a következők: `a-z`, `0-9` és `-`.

1. Válassza a **+ Create New Resource Group** (Új erőforráscsoport létrehozása) lehetőséget, adjon meg egy erőforráscsoport-nevet (pl. `myResourceGroup`), majd nyomja le az Enter billentyűt. Használhat meglévő erőforráscsoportot is.

1. Válasszon **+ létrehozás új Tárfiók**, egy globálisan egyedi nevet az új tárfiók a függvényalkalmazást, és nyomja le az Enter által használt típus. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat. Meglévő fiókot is használhat.

1. Válasszon ki egy helyet egy, a közelben, vagy a függvények által elért más szolgáltatások közelében található [régióban](https://azure.microsoft.com/regions/).

    A hely kiválasztása után megkezdődik a függvényalkalmazás létrehozása. A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés.

1. Az értesítések között válassza a **View Output** (Kimenet megtekintése) lehetőséget a létrehozási és üzembehelyezési műveletek eredményeinek megtekintéséhez – ezek közé tartoznak az Ön által létrehozott Azure-erőforrások is.

    ![A függvényalkalmazás létrehozásának kimenete](./media/functions-publish-project-vscode/function-create-notifications.png)

1. Jegyezze fel az új függvényalkalmazás URL-címét az Azure-ban. Ezzel tesztelheti a függvényt, miután a projekt közzététele megtörtént az Azure-ban.

    ![A függvényalkalmazás létrehozásának kimenete](./media/functions-publish-project-vscode/function-create-output.png)

1. Az **Azure: Functions** területre visszatérve láthatja az új függvényalkalmazást, amely az előfizetése alatt jelenik meg. Amikor kibontja ezt a csomópontot, láthatja a függvényeket a függvényalkalmazásban, valamint az alkalmazásbeállításokat és a függvényproxykat is.

    ![A függvényalkalmazás beállításai](./media/functions-publish-project-vscode/function-app-project-settings.png)

    A függvényalkalmazás csomópontján állva a Ctrl billentyűt lenyomva és a jobb egérgombbal kattintva választhat különböző felügyeleti és konfigurációs műveletek közül, amelyeket elvégezhet a függvényalkalmazáson az Azure-ban. A függvényalkalmazás az Azure Portalon való megtekintését is választhatja.
