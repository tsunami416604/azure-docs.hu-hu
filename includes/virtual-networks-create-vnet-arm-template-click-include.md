## Az ARM-sablon üzembe helyezése kattintással végrehajtható üzembe helyezéssel

Felhasználhatja a Microsoft által fenntartott és a közösség számára elérhető GitHub-tárakba feltöltött, előre meghatározott ARM-sablonokat. Ezeket a sablonokat közvetlenül a GitHubból is üzembe helyezheti, vagy letöltheti és az igényeihez szabhatja őket. Az alábbi lépések végrehajtásával üzembe helyezhet egy két Vnettel rendelkező sablont.

1. Egy böngészőből keresse fel a [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) címet.
2. Görgessen le a sablonok listáján, és kattintson a **101-vnet-two-subnets** elemre. Ellenőrizze a **README.md** fájlt a lent látható módon.

    ![A README.md fájl a GitHubban](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Kattintson a **Deploy to Azure** (Üzembe helyezés az Azure-ban) elemre. Szükség esetén adja meg az Azure bejelentkezési hitelesítő adatait. 
4. A **Parameters** (Paraméterek) panelen adja meg az értékeket, amelyekkel az új VNetet szeretné létrehozni, majd kattintson az **OK** gombra. Az alábbi ábrán a mi forgatókönyvünk értékei láthatók.

    ![Az ARM-sablon paraméterei](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

4. Kattintson az **Erőforráscsoport** elemre, majd válassza ki az erőforráscsoportot, amelyhez a VNetet hozzá kívánja adni, vagy az **Új létrehozása** elemre kattintva adja hozzá a VNetet egy új erőforráscsoporthoz. Az alábbi ábrán a **TestRG** nevű új erőforráscsoport erőforráscsoport-beállításai láthatóak.

    ![Erőforráscsoport](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

5. Szükség esetén módosítsa a VNethez tartozó **Előfizetés** és **Hely** beállításokat.
6. Ha nem szeretné, hogy a VNet csempe megjelenjen a **Kezdőpulton**, tiltsa le **A kezdőpulton rögzít** lehetőséget.
5. Kattintson a **Legal terms** (Jogi feltételek) elemre, olvassa el a feltételeket, majd az elfogadásukhoz kattintson a **Buy** (Vásárlás) gombra. 
6. A VNet létrehozásához kattintson a **Create** (Létrehozás) gombra.

    ![Üzembe helyezési csempe küldése a betekintő portálban](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

7. Az üzembe helyezés után kattintson a **TestVNet** > **All settings** (Minden beállítás) > **Subnets** (Alhálózatok) elemre a lent látható módon, és tekintse meg az alhálózat tulajdonságait.

    ![VNet létrehozása a betekintő portálon](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.gif)


<!--HONumber=Jun16_HO2-->


