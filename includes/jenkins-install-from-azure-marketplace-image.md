1. A böngészőben nyissa meg a [Jenkins Azure piactér kép](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview).

1. Válassza ki **LEKÉRÉSE most informatikai**.

    ![Válassza ki a GIT informatikai most a Jenkins Piactéri lemezképhez a telepítési folyamat elindításához.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. Árképzési részleteit és a használati információk áttekintése után válassza ki a **Folytatás**.

    ![Jenkins Piactéri lemezkép árak és használati adatokat.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Válassza ki **létrehozása** a Jenkins kiszolgáló konfigurálása az Azure portálon. 

    ![Telepítse a Jenkins Piactéri lemezképhez.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. Az a **alapjai** lapra, adja meg a következő értékeket:

    - **Név** -meg `Jenkins`.
    - **Felhasználói** -adja meg a felhasználói nevét használja, ha a virtuális gép, amelyen Jenkins fut. bejelentkezni.
    - **Hitelesítés típusa** – Itt adhatja meg **jelszó**.
    - **Jelszó** -írja be a jelszót használja, ha a virtuális gép, amelyen Jenkins fut. bejelentkezni.
    - **Jelszó megerősítése** -írja be újra a jelszót használja, ha a virtuális gép, amelyen Jenkins fut. bejelentkezni.
    - **Jenkins kiadás típusa** – Itt adhatja meg **LTS**.
    - **Előfizetés** -válassza ki az Azure-előfizetés amelybe Jenkins telepíteni szeretné.
    - **Erőforráscsoport** – Itt adhatja meg **hozzon létre új**, és adja meg a gyűjtemény-erőforrások Jenkins telepítésének alkotó logikai tárolója látja, hogy az erőforráscsoport nevét.
    - **Hely** – Itt adhatja meg **USA keleti régiója**.

    ![Adja meg a hitelesítés és az erőforrás csoport információkat Jenkins alapvető lapján.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Válassza ki **OK** folytassa a **beállítások** fülre. 

1. Az a **beállítások** lapra, adja meg a következő értékeket:

    - **Méret** -válassza ki a Jenkins virtuális gép megfelelő méretezési beállítását.
    - **Virtuális gép lemeztípus** : Adja meg vagy HDD (merevlemez-meghajtóra), vagy a Jenkins virtuális gép számára engedélyezve van az SSD (SSD-meghajtóra), milyen típusú jelzi.
    - **Nyilvános IP-cím** -az IP-cím neve alapértelmezés szerint az IP - utótaggal az előző lapon megadott Jenkins neve. Kiválaszthatja, hogy az alapértelmezett módosításának lehetőségét.
    - **Tartománynév-címke** -értéket adja meg a teljes URL-címének a Jenkins virtuális gép.

    ![Adja meg a virtuális gép beállításait Jenkins beállítások lapján.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Válassza ki **OK** folytassa a **összegzés** fülre.

1. Ha a **összegzés** lapon megjelenik, a megadott adatok ellenőrzését. Miután látja a **ellenőrzésen** üzenet, jelölje be **OK**. 

    ![Az összefoglaló lapon jeleníti meg, és érvényesíti a kiválasztott beállításokat.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. Ha a **létrehozása** lapon megjelenik, válassza ki **létrehozása** a Jenkins virtuális gép létrehozásához. A kiszolgáló készen áll, amikor egy értesítés jeleníti meg az Azure-portálon.

    ![Jenkins az készen értesítést.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)