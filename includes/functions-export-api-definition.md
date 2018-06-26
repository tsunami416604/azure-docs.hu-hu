## <a name="export-an-api-definition"></a>Az API-definíció exportálása
Egy OpenAPI definíciója a függvény a rendelkezik [létrehozása a következő függvényt egy OpenAPI definíciója](../articles/azure-functions/functions-openapi-definition.md). Ez a folyamat következő lépése a rendszer az API-definíció, hogy a PowerApps és Microsoft Flow használható egy egyéni API-t.

> [!IMPORTANT]
> Ne feledje, hogy be kell jelentkeznie Azure-ba, használja a PowerApps és Microsoft Flow bérlők azonos hitelesítő adatokkal. Ez lehetővé teszi az egyéni API-t létrehozni, és tegye elérhetővé a PowerApps és a Microsoft Flow Azure.

1. Az a [Azure-portálon](https://portal.azure.com), kattintson a függvény alkalmazás neve (például **függvény-bemutató-energia**) > **Platform funkciói** > **API-definíció** .

    ![API-definíció](media/functions-export-api-definition/api-definition.png)

1. Kattintson a **exportálja a PowerApps és Flow**.

    ![API-definíció forrása](media/functions-export-api-definition/export-api-1.png)

1. A jobb oldali ablaktáblában a táblázatban megadott beállítást használja.

    |Beállítás|Leírás|
    |--------|------------|
    |**Exportálás módja**|Válassza ki **Express** automatikusan létrehozni az egyéni API-t. Kiválasztása **manuális** exportálja az API-definíció, de majd meg kell importálnia kell azt PowerApps és Microsoft Flow manuálisan. További információkért lásd: [PowerApps és Microsoft Flow exportálása](../articles/azure-functions/app-service-export-api-to-powerapps-and-flow.md).|
    |**Környezet**|Válassza ki a környezetben, ami az egyéni API-t menteni kell. További információkért lásd: [környezetek – áttekintés (PowerApps)](https://powerapps.microsoft.com/tutorials/environments-overview/) vagy [környezetek overview (a Microsoft-Flow)](https://us.flow.microsoft.com/documentation/environments-overview-admin/).|
    |**Egyéni API neve**|Adjon meg egy nevet, például `Turbine Repair`.|
    |**Az API-kulcs nevét**|Adja meg, hogy az alkalmazás és a folyamat szerkesztők kell megjelennie az egyéni API felhasználói felületén. Vegye figyelembe, hogy a példában tartalmaz hasznos információkat.|
 
    ![Exportálás a PowerAppsbe és a Microsoft Flow-ba](media/functions-export-api-definition/export-api-2.png)

1. Kattintson az **OK** gombra. Az egyéni API-t most már a beépített, és felveszi a környezetbe a megadott.