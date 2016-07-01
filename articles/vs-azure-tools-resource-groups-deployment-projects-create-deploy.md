<properties
   pageTitle="Azure erőforráscsoportokkal kapcsolatos Visual Studio-projektek | Microsoft Azure"
   description="A Visual Studio használatával Azure erőforráscsoport-projekteket hozhat létre, és telepítheti az erőforrásokat az Azure rendszerbe."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn" />
<tags
   ms.service="azure-resource-manager"
   ms.devlang="multiple"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/17/2016"
   ms.author="tomfitz" />

# Azure erőforráscsoport-sablonok létrehozása és telepítése a Visual Studio alkalmazással

A Visual Studio és az [Azure SDK](https://azure.microsoft.com/downloads/) alkalmazással olyan projekteket hozhat létre, amelyekkel telepíthető az infrastruktúra és kód az Azure rendszerbe. Meghatározhatja például az alkalmazás webállomását, webhelyét és adatbázisát, továbbá telepítheti az infrastruktúrát a kóddal együtt. Azt is megteheti, hogy meghatározza a virtuális gépet, a virtuális hálózatot és a tárfiókot, majd telepíti az infrastruktúrát a virtuális gépen végrehajtott parancsfájllal együtt. Az **Azure erőforráscsoport** telepítési projektje lehetővé teszi, hogy az összes szükséges erőforrást egyetlen, megismételhető műveletben telepítse. Az erőforrások telepítésével és kezelésével kapcsolatos további információkért lásd: [Az Azure Resource Manager áttekintése](resource-group-overview.md).

Az Azure erőforráscsoport-projektek az Azure Resource Managerből származó JSON-sablonokat tartalmaznak, amelyek meghatározzák az Azure rendszerbe telepítendő erőforrásokat. A Resource Manager-sablon elemeivel kapcsolatos információkért lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md). A Visual Studio lehetővé teszi a sablonok szerkesztését, valamint eszközeivel egyszerűbbé teszi a sablonokkal való munkát.

Ebben a témakörben egy webalkalmazást és az SQL Database szolgáltatás telepíti majd. A lépések azonban majdnem teljesen ugyanazok, mint a különféle típusú erőforrások esetében. Ugyanilyen könnyen telepíthet virtuális gépeket és azok kapcsolódó erőforrásait. A Visual Studio számos különböző kezdősablont kínál a gyakori forgatókönyvek telepítéséhez.

Ez a cikk a Visual Studio 2015 Update 2 és a Microsoft Azure SDK for .NET 2.9 használatával íródott. Amennyiben a Visual Studio 2013 verziót használja Azure SDK 2.9-cel, nagyjából ugyanezt tapasztalja majd. Használhatja az Azure SDK 2.6 vagy újabb verzióit is, azonban ebben az esetben a felhasználó élmény eltérhet a cikkben leírtaktól. Az [Azure SDK](https://azure.microsoft.com/downloads/) legújabb verziójának telepítése erősen ajánlott a lépések megkezdése előtt. 

## Azure erőforráscsoport-projekt létrehozása

Ebben az eljárásban egy Azure erőforráscsoport-projektet hoz majd létre egy **Webes alkalmazás + SQL** sablonból.

1. A Visual Studio programban válassza a **Fájl**, **Új projekt**, majd a **C#** vagy a **Visual Basic** lehetőséget. Ezután válassza a **Felhő** lehetőséget, majd az **Azure erőforráscsoport** projektet.

    ![Felhőtelepítési projekt](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-project.png)

1. Válassza ki az Azure Resource Managerbe telepíteni kívánt sablont. Figyelje meg, hogy a telepíteni kívánt projekt típusától függően számos különböző lehetőség áll rendelkezésre. Ebben a témakörben a **Webes alkalmazás + SQL** sablon használatát mutatjuk be.

    ![Sablon kiválasztása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-project.png)

    A kiválasztott sablon csak egy kiindulási pont. A forgatókönyvnek való megfelelés érdekében hozzáadhat és eltávolíthat erőforrásokat.

    >[AZURE.NOTE] Az elérhető sablonok listájának lekérése az internetről történik, ezért változhat.

    A Visual Studio létrehoz egy erőforráscsoport-telepítési projektet a webalkalmazás és az SQL Database számára.

1. A létrehozott elemek megtekintéséhez bontsa ki a telepítésben található csomópontokat.

    ![csomópontok megjelenítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-items.png)

    Mivel a Webalkalmazás + SQL sablont választottuk ehhez a példához, az alábbi fájlok láthatóak. 

  	|Fájlnév|Leírás|
  	|---|---|
  	|Deploy-AzureResourceGroup.ps1|PowerShell-parancsfájl, amely PowerShell-parancsokat hív meg az Azure Resource Manager telepítéséhez.<br />**Megjegyzés** A Visual Studio ezt a PowerShell-parancsfájlt használja a sablon telepítéséhez. A parancsfájlon végrehajtott módosítások hatással vannak a Visual Studióban üzemelő példányra is, ezért legyen óvatos.|
  	|WebSiteSQLDatabase.json|Az Azure szolgáltatásban telepíteni kívánt infrastruktúrát, valamint a telepítés során megadható paramétereket meghatározó Resource Manager-sablon. Az erőforrások közti függőségeket is meghatározza, hogy azok a megfelelő sorrendben legyenek telepítve.|
  	|WebSiteSQLDatabase.parameters.json|Paraméterfájl, amely a sablonhoz szükséges értékeket tartalmazza. Ezek a megadandó értékek, amelyekkel testre szabhatóak az egyes üzemelő példányok.|

    Mindegyik erőforráscsoport-telepítési projekt tartalmazza ezeket az alapvető fájlokat. Más projektek további fájlokat is tartalmazhatnak, egyéb funkciók támogatásához.

## A Resource Manager-sablon testreszabása

A telepítési projekteket a telepíteni kívánt erőforrásokat leíró JSON sablonok módosításával szabhatja testre. A JSON a JavaScript Object Notation rövidítése, és egy könnyen kezelhető szerializált adatformátum. A JSON-fájlok az egyes fájlok tetején meghivatkozott sémát alkalmazzák. Amennyiben szeretné behatóbban megismerni, töltse le és elemezze a sémát. A séma meghatározza az engedélyezett elemeket, a mezők típusát és formátumát, a felsorolt értékek lehetséges értékeit stb. A Resource Manager-sablon elemeivel kapcsolatos információkért lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).

A munkához nyissa meg a **WebSiteSQLDatabase.json** sablont.

A Visual Studio szerkesztő eszközöket biztosít a Resource Manager-sablon szerkesztéséhez. A **JSON-vázlat** ablak segítségével könnyen áttekinthetőek a sablonban meghatározott elemek.

![JSON-vázlat megjelenítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-json-outline.png)

A vázlatban lévő egyes elemek kiválasztásával a rendszer a sablon adott részére ugrik, és kiemeli a megfelelő JSON-struktúrát.

![navigálás a JSON-fájlban](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/navigate-json.png)

Ha új erőforrást szeretne a sablonhoz adni, kattintson az **Erőforrás hozzáadása** gombra a JSON-vázlat ablak tetején, vagy kattintson a jobb gombbal az **erőforrások** elemre, és válassza az **Új erőforrás hozzáadása** lehetőséget.

![erőforrás hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-resource.png)

Ebben az oktatóanyagban válassza a **Tárfiók** lehetőséget, és adjon meg egy nevet. A tárfiók neve csak számokat és kisbetűket tartalmazhat, és nem haladhatja meg a 24 karakter hosszúságot. A projekt hozzáad egy 13 karakterből álló egyedi karakterláncot a névhez, ezért az Ön által választott név hossza ne haladja meg a 11 karaktert.

![tároló hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-storage.png)

Figyelje meg, hogy nem csupán az erőforrás lett hozzáadva, hanem a tárfiók típusának paramétere is, valamint egy változó a tárfiók nevével.

![vázlat megjelenítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-new-items.png)

A **storageType** paraméter előre meg van határozva az engedélyezett típusokkal és az alapértelmezett típussal egyetemben. Megtarthatja ezeket az értékeket, vagy módosíthatja őket az adott forgatókönyvnek megfelelően. Ha nem szeretné senki számára engedélyezni, hogy **Premium_LRS** tárfiókot hozzanak létre a sablonon keresztül, egyszerűen törölje azt az engedélyezett típusok közül az alábbiak szerint. 

    "storageType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS"
      ]
    }

A Visual Studio intellisense szolgáltatása segítségével megtudhatja, milyen tulajdonságok érhetőek el a sablon szerkesztése során. Például App Service-csomagja tulajdonságainak szerkesztéséhez lépjen a **HostingPlan** erőforrásra, és adjon meg egy új értéket a **properties** számára. Figyelje meg, hogy az intellisense megjeleníti az elérhető értékeket, valamint az adott értékek leírását.

![intellisense megjelenítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-intellisense.png)

Állítsa a **numberOfWorkers** paraméter értékét 1-re.

    "properties": {
      "name": "[parameters('hostingPlanName')]",
      "numberOfWorkers": 1
    }

## Az Erőforráscsoport-projekt telepítése az Azure szolgáltatásban

Készen áll a projekt telepítésére. Az Azure Erőforráscsoport-projekt telepítésekor egy Azure erőforráscsoporton helyezi üzembe azt, amely lényegében nem egyéb, mint az Azure-ban lévő erőforrások, például webalkalmazások, adatbázisok és hasonlók logikai csoportosítása.

1. A telepítési projekt csomópontjának helyi menüjén válassza a **Telepítés** > **Új üzemelő példány** lehetőséget.

    ![Telepítés, Új üzemelő példány menüelem](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/deploy.png)

    Megjelenik a **Telepítés erőforráscsoportra** párbeszédpanel.

    ![Telepítés erőforráscsoportra párbeszédpanel](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployment.png)

1. Az **Erőforráscsoport** legördülő listából válasszon ki egy létező erőforráscsoportot, vagy hozzon létre egy újat. Erőforráscsoport létrehozásához nyissa le az **Erőforráscsoport** legördülő listát, és válassza az **Új létrehozása...** elemet.

    ![Telepítés erőforráscsoportra párbeszédpanel](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-new-group.png)

    Megjelenik az **Erőforráscsoport létrehozása** párbeszédpanel. Adjon meg egy nevet és egy helyet a csoport számára, és kattintson a **Létrehoz** gombra.

    ![Erőforráscsoport létrehozása párbeszédpanel](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/create-resource-group.png)
   
1. Az üzemelő példány paramétereit a **Paraméterek szerkesztése** gombra kattintva adhatja meg. Adja meg a paraméterek értékeit, majd kattintson a **Mentés** gombot.

    ![Paraméterek szerkesztése párbeszédpanel](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/provide-parameters.png)
    
    A **Jelszavak mentése egyszerű szövegként a paraméterfájlban** beállítás használata nem biztonságos.

1. Kattintson a **Telepítés** gombra a projekt telepítéséhez az Azure szolgáltatásban. Az telepítés folyamata a **Kimenet** ablakban követhető. Az telepítés befejezése több percet is igénybe vehet a konfigurációtól függően. Amikor a rendszer kéri, adja meg az adatbázis rendszergazdai jelszavát a PowerShell-konzolon. Ha a telepítés folyamata elakad, elképzelhető, hogy a folyamat arra várakozik, hogy megadja a jelszót a PowerShell-konzolon.

    >[AZURE.NOTE] Előfordulhat, hogy a rendszer megkéri, hogy telepítse az Azure PowerShell-parancsmagokat. Mivel a parancsmagok szükségesek az Azure erőforráscsoportok telepítéséhez, telepítenie kell őket.
    
1. Amint a telepítés véget ért, az alábbihoz hasonló üzenet jelenik meg a **Kimenet** ablakban:

        ...
        15:19:19 - DeploymentName     : websitesqldatabase-0212-2318
        15:19:19 - CorrelationId      : 6cb43be5-86b4-478f-9e2c-7e7ce86b26a2
        15:19:19 - ResourceGroupName  : DemoSiteGroup
        15:19:19 - ProvisioningState  : Succeeded
        ...

1. Egy böngészőben nyissa meg az [Azure portált](https://portal.azure.com/), és jelentkezzen be a fiókjával. Az erőforráscsoport megtekintéséhez válassza az **Erőforráscsoportok** lehetőséget, valamint az erőforráscsoportot, amelyiken a telepítést végezte.

    ![csoport kijelölése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-group.png)

1. Az összes telepített erőforrás megjelenik.

    ![erőforrások megjelenítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-resources.png)

1. Amennyiben módosításokat végez, és újra el kívánja végezni a projekt telepítését, a meglévő erőforráscsoportot kiválaszthatja közvetlenül az Azure erőforráscsoport-projekt helyi menüjéből. A helyi menün válassza a **Telepítés** lehetőséget, majd válassza ki az erőforrást, amelyre az imént a telepítést végezte.

    ![Azure erőforráscsoport üzembe helyezve](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/redeploy.png)

## Kód telepítése az infrastruktúrával

Ezen a ponton az alkalmazás infrastruktúrája már telepítve van, tényleges kód azonban még nincs telepítve a projekttel. Ez a témakör a webalkalmazások és az SQL Database telepítésének módját ismerteti az telepítés során. Amennyibe webalkalmazás helyett virtuális gépet telepít, a telepítés keretében valamennyi kódot is érdemes futtatni a gépen. A kód telepítésének folyamata a webalkalmazások és a virtuális gépek telepítésénél szinte teljesen megegyezik.

1. A Visual Studióban adjon hozzá egy **ASP.NET webalkalmazást**. 

    ![webalkalmazás hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-app.png)
    
1. Válassza az **MVC** lehetőséget, és törölje az **Üzemeltetés a felhőben** mezőt, mivel ezt a feladatot az erőforráscsoport-projekt fogja ellátni.

    ![MVC kiválasztása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/select-mvc.png)
    
1. Miután létrehozta a webalkalmazást, adjon hozzá egy hivatkozást az erőforráscsoport-projektben a webalkalmazás-projekthez.

    ![hivatkozás hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-reference.png)
    
    A hivatkozás hozzáadásával a webalkalmazás-projektet összekapcsolja az erőforráscsoport-projekttel, és automatikusan beállít három kulcsfontosságú tulajdonságot.  
    
    - A **További tulajdonságok** a webes telepítési web csomag előkészítési helyét tartalmazza, amely át lesz helyezve az Azure Storage szolgáltatásba. 
    - A **Fájl elérési útjának belefoglalása** azt az útvonalat tartalmazza, ahol a csomag létre fog jönni.  A **Célok belefoglalása** a telepítés során végrehajtott parancsot tartalmazza. 
    - Az alapértelmezett **Build;Csomag** érték egy webes telepítési csomag (package.zip) felépítését és létrehozását teszi lehetővé.  
    
    Közzétételi profil nem szükséges, mivel a telepítési folyamat a szükséges információkat a csomag létrehozásához használt tulajdonságokból meríti.
    
      ![hivatkozás megtekintése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/see-reference.png)
      
1. Adjon egy új erőforrást a sablonhoz, és ez alkalommal válassza **A Web Apps webes telepítése** lehetőséget. 

    ![webes telepítés hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/add-web-deploy.png)
    
1. Végezze el ismét az erőforráscsoport-projekt telepítését az erőforráscsoporton. Ez alkalommal van néhány új paraméter. Az **_artifactsLocation** vagy **_artifactsLocationSasToken** paraméterek értékét nem kell megadnia, mivel a rendszer ezeket automatikusan osztja ki. A mappa és a fájl nevét a telepítési csomag elérési útvonalának megfelelően adja meg.

    ![webes telepítés hozzáadása](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/set-new-parameters.png)
    
    Az **Összetevő tárfiókja** paraméternél használhatja az adott erőforráscsoporttal üzembe helyezett tárfiókot.
    
Miután a telepítés véget ért, a helyre ellátogatva láthatja, hogy az alapértelmezett ASP.NET alkalmazás telepítve lett.

![telepített alkalmazás megjelenítése](./media/vs-azure-tools-resource-groups-deployment-projects-create-deploy/show-deployed-app.png)

## További lépések

- Az erőforrásoknak a portálon keresztül történő kezelésével kapcsolatos információkért lásd: [Az Azure portál használata az Azure erőforrások kezeléséhez](./azure-portal/resource-group-portal.md).
- A sablonokkal kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).



<!--HONumber=Jun16_HO2--->


