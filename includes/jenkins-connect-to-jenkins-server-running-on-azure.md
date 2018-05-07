1. A böngészőben navigáljon a Jenkins virtuális gép. Mivel a Jenkins irányítópult nem érhető el, nem titkosított HTTP Protokollon keresztül, egy üzenet jelenik meg, azt jelzi, hogy szükség van alagút be a virtuális gép SSH használatával.

    ![Jenkins információival foglalja össze az SSH használata a Jenkins virtuális géphez történő csatlakozáshoz.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ssh-instructions.png)

1. Nyisson meg egy bash vagy a Terminálszolgáltatások ablakot SSH elérésére.

1. Írja be a következő `ssh` parancs helyett a  **&lt;felhasználónév >** és  **&lt;tartományi >** helyőrzőt a Jenkins létrehozásakor megadott értékek virtuális gép:

    ```bash
    ssh -L 127.0.0.1:8080:localhost:8080 <username>@<domain>.eastus.cloudapp.azure.com
    ```

1. Kövesse a `ssh` kér a Jenkins virtuálisgép jelentkezni.

1. Adja meg a következő parancsot a kezdeti jelszó lekérése a feloldásának Jenkins.

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

1. Másolja a vágólapra a jelszavát, amelyet a bash vagy a Terminálszolgáltatások ablakban jelenik meg.

1. A böngészőjében lépjen a `http://localhost:8080` helyre.

1. Illessze be a jelszót a korábban kapott a **rendszergazdai jelszó** mezőjét, majd válassza **Folytatás**.

    ![Jenkins tárolja egy kezdeti jelszót kell használnia a Jenkins virtuálisgép kapcsolódik hozzá először feloldásához.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-unlock.png)

1. Válassza ki **javasolt beépülő modulok telepítése**.

    ![Jenkins segítségével könnyen telepíthető a kezdeti bevitelét javasolt beépülő modulok gyűjteménye](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-customize.png)

1. Az a **első rendszergazda felhasználó létrehozása** lapon a rendszergazda felhasználó és a jelszót a Jenkins irányítópult hozhat létre, és válassza ki **mentés és Befejezés**.

1. Az a **Jenkins készen áll!** lapon válassza **Jenkins indíthatja**.

    ![Után Jenkins telepítve és konfigurálva a hozzáférés, megkezdheti a feladatok létrehozása és használja azt.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ready.png)