---
title: "A VS Code használata a Resource Manager-sablonokkal | Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan állítható be a Visual Studio Code az Azure Resource Manager-sablonok létrehozására."
services: azure-resource-manager
documentationcenter: na
author: cmatskas
manager: timlt
editor: tysonn
ms.assetid: 78f2aa22-df1d-41bd-92ec-dabd1175db88
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: chmatsk;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 10c7051c9b1218081d95cb10403006bfd95126ba
ms.openlocfilehash: 2ac1c2cce7a9e045990894b0bbaa045df3d48954


---
# <a name="working-with-azure-resource-manager-templates-in-visual-studio-code"></a>Az Azure Resource Manager-sablonok használata a Visual Studio Code kódszerkesztőben
Az Azure Resource Manager-sablonok az erőforrásokat és a kapcsolódó függőségeket leíró JSON-fájlok. Ezek a fájlok néha nagyméretűek és összetettek lehetnek, ezért fontos a megfelelő eszközök biztosítása a kezelésükhöz. A Visual Studio Code egy új, könnyen használható, nyílt forráskódú, platformfüggetlen kódszerkesztő. Egy [új bővítményével](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) lehetővé teszi a Resource Manager-sablonok létrehozását és szerkesztését. A VS Code mindenhol fut, és csak internet-hozzáférésre van szüksége, ha üzembe szeretné helyezni a Resource Manager-sablonokat az Azure-előfizetésén.

Ha még nem telepítette a VS Code szerkesztőt, a [https://code.visualstudio.com/](https://code.visualstudio.com/) webhelyről telepítheti.

## <a name="install-the-resource-manager-extension"></a>A Resource Manager bővítményének telepítése
Ha JSON-sablonokkal szeretne dolgozni a VS Code szerkesztőben, telepítenie kell egy bővítményt. A következő lépések végrehajtásával töltheti le és telepítheti a Resource Manager JSON-sablonjainak nyelvi támogatását:

1. Indítsa el a VS Code szerkesztőt. 
2. Jelenítse meg a gyorsmegnyitási listát (Ctrl+P). 
3. Futtassa az alábbi parancsot: 
   
        ext install msazurermtools.azurerm-vscode-tools
4. A bővítmény engedélyezéséhez indítsa újra a VS Code alkalmazást, amikor a rendszer kéri. 
   
   Ezzel el is készült.

## <a name="set-up-resource-manager-snippets"></a>A Resource Manager-kódrészletek beállítása
Az előző lépésekkel már telepítette az eszközös támogatást, most pedig konfigurálnia kell a VS Code szerkesztőt a JSON-sablonok kódrészleteinek használatára.

1. Másolja a vágólapra az [azure-xplat-arm-tooling](https://raw.githubusercontent.com/Azure/azure-xplat-arm-tooling/master/VSCode/armsnippets.json) tárházban található fájl tartalmát.
2. Indítsa el a VS Code szerkesztőt. 
3. VS Code-ban megnyithatja a JSON-kódrészletfájlt a **Fájl** -> **Beállítások** -> **Felhasználói kódrészletek** -> **JSON** helyről. Másik lehetőségként nyomja le az **F1** billentyűt, és kezdje el begépelni a **beállítások** kifejezést, amíg a rendszer nem kínálja fel a **Beállítások: Kódrészletek** elemet.
   
    ![Beállítások, kódrészletek](./media/resource-manager-vs-code/preferences-snippets.png)
   
    A listáról válassza ki a **JSON** lehetőséget.
   
    ![A JSON lehetőség kiválasztása](./media/resource-manager-vs-code/select-json.png)
4. Illessze be az 1. lépésben kiválasztott fájl tartalmát a felhasználói kódrészletek fájljába az utolsó „}” karakter elé. 
5. Ellenőrizze, hogy a JSON rendben van-e, és nincsenek-e valahol hibák benne. 
6. Mentse és zárja be a felhasználói kódrészletek fájlját.

Ez minden, ami szükséges a Resource Manager-kódrészletek használatának elkezdéséhez. Ezután teszteljük ezt a beállítást.

## <a name="work-with-template-in-vs-code"></a>A sablon használata a VS Code szerkesztőben
A sablonok használatát legegyszerűbben úgy kezdheti el, hogy választ egy gyorsan üzembe helyezhető sablont a [Github](https://github.com/Azure/azure-quickstart-templates) kínálatából, vagy a sajátját használja. A portálon keresztül könnyen [exportálhat sablonokat](resource-manager-export-template.md) bármelyik erőforráscsoportja esetében. 

1. Ha exportált egy sablont egy erőforráscsoportból, nyissa meg a kibontott fájlokat VS Code szerkesztőben.
   
    ![Fájlok megjelenítése](./media/resource-manager-vs-code/show-files.png)
2. Nyissa meg a template.json fájlt, hogy szerkeszthesse, és további erőforrásokat adhasson hozzá. A(z) `"resources": [` után nyomja le az Enter billentyűt új sor kezdéséhez. Ha beírja az **arm** kifejezést, megjelenik a lehetőségek listája. Ezek az elemek a telepített sablonkódrészletek. 
   
    ![A kódrészletek megjelenítése](./media/resource-manager-vs-code/type-snippets.png)
3. Válassza ki a kívánt kódrészletet. Ebben a cikkben az **arm-ip** kódrészletet választjuk egy új nyilvános IP-cím hozzáadásához. Tegyen egy vesszőt az újonnan létrehozott erőforrás záró szögletes zárójele `}` után, hogy a sablon szintaxisa érvényes legyen.
   
     ![A vessző hozzáadása](./media/resource-manager-vs-code/add-comma.png)
4. A VS Code beépítetten tartalmazza az IntelliSense funkciót. A sablonok szerkesztésekor a VS Code felkínálja az elérhető értékeket. Ha például hozzá szeretne adni egy változószakaszt a sablonhoz, adja hozzá a `""` (két idézőjel) karaktereket, és nyomja le a **Ctrl+Szóköz** billentyűkombinációt az idézőjelek között. Megjelennek lehetőségek, például a **változók**.
   
    ![Változók hozzáadása](./media/resource-manager-vs-code/add-variables.png)
5. Az IntelliSense a rendelkezésre álló értékek és a függvények közül is tud javaslatot tenni. Ha egy tulajdonságot be szeretne állítani egy paraméterértékre, hozzon létre egy kifejezést a következő segítségével: `"[]"` és **Ctrl+Szóköz**. Kezdje el beírni a függvény nevét. Amikor megtalálta a megfelelő függvényt, nyomja meg a **Tab** billentyűt.
   
    ![Paraméter hozzáadása](./media/resource-manager-vs-code/select-parameters.png)
6. Ha meg szeretné tekinteni az elérhető paraméterek listáját a sablonon belül, nyomja le újra a **Ctrl+Szóköz** billentyűkombinációt a függvényen belül.
   
    ![Paraméter hozzáadása](./media/resource-manager-vs-code/select-avail-parameters.png)
7. Ha sémaellenőrzési problémái adódnak a sablonban, megjelennek az ismerős hibák a szerkesztőben. A hibák és a figyelmeztetések listájának megtekintéséhez nyomja meg a **Ctrl+Shift+M** billentyűt, vagy kattintson az állapotsor bal oldalán található ikonokra.
   
    ![Hibák](./media/resource-manager-vs-code/errors.png)
   
    A sablon érvényesítése segítheti a szintaktikai problémák felismerését, azonban olyan hibák is megjelenhetnek, amelyeket figyelmen kívül hagyhat. Bizonyos esetekben a szerkesztő a sablont egy nem naprakész sémával hasonlítja össze, és hibát jelez, holott Ön tudja, hogy a sablon helyes. Tegyük fel például, hogy nemrég egy függvény lett hozzáadva a Resource Managerhez, de a séma még nem lett frissítve. A szerkesztő ekkor hibát jelez annak ellenére, hogy a függvény megfelelően működik az üzembe helyezés során.
   
    ![Hibaüzenet](./media/resource-manager-vs-code/unrecognized-function.png)

## <a name="deploy-your-new-resources"></a>Az új erőforrások üzembe helyezése
Ha kész van a sablon, üzembe helyezheti az új erőforrásokat az alábbi utasításokkal: 

### <a name="windows"></a>Windows
1. Nyissa meg a PowerShell-parancssort. 
2. A bejelentkezéshez írja be a következőt: 
   
  ```powershell
  Login-AzureRmAccount
  ```

3. Ha több előfizetéssel rendelkezik, jelenítse meg az előfizetések listáját a következő paranccsal:

  ```powershell 
  Get-AzureRmSubscription
  ```
   
    Válassza ki a használandó előfizetést.

  ```powershell
  Select-AzureRmSubscription -SubscriptionId <Subscription Id>
  ```

4. Frissítse a paramétereket a parameters.json fájlban.
5. Futtassa a Deploy.ps1 fájlt a sablon Azure-beli üzembe helyezéséhez.

### <a name="osxlinux"></a>OSX/Linux
1. Nyisson meg egy terminálablakot. 
2. A bejelentkezéshez írja be a következőt:

  ```azurecli
  azure login
  ```

3. Ha több előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést a következő paranccsal:

  ```azurecli
  azure account set <subscriptionNameOrId> 
  ```

4. Frissítse a paramétereket a parameters.json fájlban.
5. A sablon üzembe helyezéséhez futtassa a következő parancsot:

  ```azurecli 
  azure group deployment create -f <PathToTemplate>
  ``` 

## <a name="next-steps"></a>Következő lépések
* A sablonokkal kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* A sablonokban használható függvények részletes ismertetését lásd: [Az Azure Resource Manager-sablonok függvényei](resource-group-template-functions.md).
* További példák a Visual Studio Code használatára: [Build cloud apps with Visual Studio Code](https://github.com/Microsoft/HealthClinic.biz/wiki/Build-cloud-apps-with-Visual-Studio-Code) (Felhőalkalmazások létrehozása a Visual Studio Code használatával) a [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect [bemutatóban](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). A HealthClinic.biz bemutatóban található további gyorsindítási útmutatókat lásd: [Azure Developer Tools Quickstarts](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts) (Gyorsindítási útmutatók az Azure fejlesztői eszközeihez).




<!--HONumber=Jan17_HO1-->


