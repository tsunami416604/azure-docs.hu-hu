---
title: "Az Azure-ban a TFS-sel szolgáltatások folyamatos kézbesítési felhő |} Microsoft Docs"
description: "Ismerje meg, hogyan állíthat be az Azure felhőalapú alkalmazásokat folyamatos kézbesítési. Kódminták MSBuild parancssori utasításokat és a PowerShell-parancsfájlokat."
services: cloud-services
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: 4f3c93c6-5c82-4779-9d19-7404a01e82ca
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/12/2017
ms.author: kraigb
ms.openlocfilehash: 470fda7722e6a22e50ed66a7bc193fc7c9f71536
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="continuous-delivery-for-cloud-services-in-azure"></a>Azure felhőszolgáltatások folyamatos kézbesítés
A cikkben leírt eljárás bemutatja, hogyan állíthatja be az Azure felhőalapú alkalmazásokat folyamatos kézbesítési. Ez a folyamat lehetővé teszi csomagok automatikus létrehozását, valamint csomagok Azure-beli telepítését minden kódbeadás után. A jelen cikkben ismertetett csomag felépítési folyamat megegyezik a **csomag** parancs a Visual Studio és a közzétételi lépéseket egyenértékűek legyenek az **közzététel** parancsot a Visual Studio.
A cikk ismerteti a módszert szeretné használni a build kiszolgáló MSBuild parancssori utasításokat és a Windows PowerShell-parancsfájlok létrehozása, és azt is bemutatja, hogyan kell igény szerint a Visual Studio Team Foundation Server - definíciók Team Build használatára állítsa be a MSBuild parancsok és a PowerShell-parancsfájlokat. A folyamat nem testre szabható a összeállító környezetet és a cél Azure környezetben.

Visual Studio Team Services, a könnyebb ehhez az Azure-ban üzemeltetett TFS verziójának is használható. 

Kezdés előtt kell közzé tenni az alkalmazást a Visual Studio eszközből.
Ezzel biztosíthatja, hogy az összes erőforrás elérhető-e és inicializálva amikor megpróbál a kiadvány folyamat automatizálására.

## <a name="1-configure-the-build-server"></a>1: a Build kiszolgáló konfigurálása
Egy Azure-csomag létrehozása MSBuild használatával, előtt telepítenie kell a szükséges szoftverek és az eszközök a build kiszolgálón.

A Visual Studio nincs szükség a build kiszolgálóra kell telepíteni. Ha a build kiszolgáló kezelését Team Foundation Build szolgáltatás használni kívánt, kövesse az utasításokat a a [Team Foundation Build Service] [ Team Foundation Build Service] dokumentációját.

1. A build kiszolgálón telepítse a [.NET-keretrendszer 4.5.2-es][.NET Framework 4.5.2], mert az MSBuild tartalmazza.
2. Telepítse a legújabb [.NET-keretrendszerhez készült Azure szerzői eszközök](https://azure.microsoft.com/develop/net/).
3. Telepítse a [Azure-könyvtárakban .NET](http://go.microsoft.com/fwlink/?LinkId=623519).
4. Másolja a Microsoft.WebApplication.targets fájlt a Visual Studio telepítése a build kiszolgáló.

   A számítógépen telepített Visual Studio ezt a fájlt a C: könyvtárában található\\Program Files(x86)\\MSBuild\\Microsoft\\VisualStudio\\v14.0\\WebApplications. Másolja az ugyanabban a könyvtárban, a build kiszolgálón.
5. Telepítse a [Azure Tools for Visual Studio](https://www.visualstudio.com/features/azure-tools-vs.aspx).

## <a name="2-build-a-package-using-msbuild-commands"></a>2: MSBuild parancsokkal csomag létrehozása
Ez a szakasz ismerteti, hogyan az MSBuild parancs egy Azure-csomag épülő összeállításához. Futtassa ezt a lépést a build kiszolgálón győződjön meg arról, hogy minden megfelelően van konfigurálva, és hogy az MSBuild parancs valóban rá. Ez a parancssor vagy hozzáadása meglévő build parancsfájlok a build kiszolgálón, vagy használhatja a parancssorban a TFS Build definícióban, a következő szakaszban leírtak szerint. Parancssori paraméterek és MSBuild kapcsolatos további információkért lásd: [MSBuild parancssori hivatkozás](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).

1. Visual Studio a build kiszolgálóra van telepítve, keresse meg és válassza a **Visual Studio parancssorból** a a **Visual Studio eszközök** Windows-mappa.

   Ha a build kiszolgálón nincs telepítve a Visual Studio, nyisson meg egy parancssort, és győződjön meg arról, hogy MSBuild.exe elérhető-e az elérési út. MSBuild van telepítve a .NET-keretrendszer, az elérési út: % WINDIR %\\Microsoft.NET\\keretrendszer\\*verzió*. Például MSBuild.exe hozzáadása a PATH környezeti változóba, ha a .NET Framework 4 telepítve van, írja be a következő parancsot a parancssorba:

       set PATH=%PATH%;"C:\Windows\Microsoft.NET\Framework\v4.0.30319"
2. A parancssorban keresse meg a mappát, amely a használni kívánt Azure-projekt fájlt tartalmazó.
3. MSBuild futtassa a/TARGET: Publish beállítást, az alábbi példában látható módon:

       MSBuild /target:Publish

   Ez a beállítás rövidíthető /t: közzététele. Az MSBuild /t:Publish beállítást kell nem keverendő össze a Visual Studio Publish parancsai Ha az Azure SDK telepítve van. A /t: a beállítás csak buildek közzététele az Azure csomagokat. Azt nem kell telepítenie a csomagokat, mint a Visual Studio Publish parancsokat.

   Szükség esetén megadhatja az MSBuild paraméter a projekt nevét. Ha nincs megadva, az aktuális könyvtárat használja. MSBuild parancssori beállításokkal kapcsolatos további információkért lásd: [MSBuild parancssori hivatkozás](https://msdn.microsoft.com/library/ms164311%28v=vs.140%29.aspx).
4. Keresse meg a kimenetet. Alapértelmezés szerint ez a parancs létrehoz egy könyvtárat a projekthez, amely a gyökérmappában található viszonyítva, mint *ProjectDir*\\bin\\*konfigurációs*\\app.publish \\. Azure-projekt összeállításakor létrehozhat két fájlt, a csomagfájl és a hozzá tartozó konfigurációs fájlt:

   * Project.cspkg
   * ServiceConfiguration. *TargetProfile*.cscfg

   Alapértelmezés szerint minden Azure-projekt tartalmazza egy szolgáltatás konfigurációs fájljában (.cscfg fájl) (hibakereséshez) helyi buildek és egy másikat a felhő (átmeneti vagy üzemi) buildek, de adhat hozzá vagy távolítsa el a szolgáltatás konfigurációs fájlokat, igény szerint. Visual Studio csomag összeállításakor kérni fogja melyik szolgáltatás konfigurációs fájlja mellett a csomag tartalmazza.
5. Adja meg a szolgáltatás konfigurációs fájljában. Ha egy csomag MSBuild hozhat létre, a helyi szolgáltatás konfigurációs fájlja veszi fel alapértelmezés szerint. Egy másik szolgáltatáskonfigurációs fájlt, adja meg a TargetProfile tulajdonság MSBuild parancs az alábbi példában látható módon:

       MSBuild /t:Publish /p:TargetProfile=Cloud
6. Adja meg a kimeneti helyét. A /p:PublishDir használatával beállítani az elérési utat =*Directory* \\ beállítást, többek között a záró perjelet elválasztó, a következő példában látható módon:

       MSBuild /target:Publish /p:PublishDir=\\myserver\drops\

   Miután össze, és egy megfelelő MSBuild parancssort a projekt felépítéséhez és összefogni azokat az Azure-csomag tesztelni, ez a parancssor a build parancsfájlokat is hozzáadhat. Ha a build server egyéni parancsfájlokat használ, ez a folyamat az egyéni létrehozási folyamata során a mintaadatokról függ. Ha TFS egy összeállító környezetet használ, akkor is kövesse az utasításokat a következő lépésben az Azure-csomag létrehozási hozzáadása a felépítési folyamat.

## <a name="3-build-a-package-using-tfs-team-build"></a>3: build a csomagot, a TFS-csoport létrehozása
Ha rendelkezik TFS létrehozási gépként beállítása Team Foundation Server (TFS) állítsa be a build tartományvezérlő és a build kiszolgálóként, majd igény szerint állíthatja be az automatikus létrehozási az Azure-csomagnak a. Állítsa be, és a Team Foundation server használja a buildelési rendszer módjáról további információkért lásd: [a buildelési rendszer kibővítési][Scale out your build system]. Különösen a következő eljárás feltételezi, hogy konfigurálta a build kiszolgáló leírtak [központi telepítése és build kiszolgálók][Deploy and configure a build server], és létrehozott egy csapatprojekt létrehozott felhő a csapatprojekt projektre.

Az Azure csomagok TFS konfigurálásához hajtsa végre az alábbi lépéseket:

1. Válassza ki a Visual Studio a fejlesztési számítógépen, a Nézet menü **Team Explorer**, vagy válassza a Ctrl +\\, Ctrl + M. A csapat Explorer-ablakban bontsa ki a **buildek** csomópont, vagy válasszon a **buildek** lapon, és válassza a **új Build Definition**.

   ![Új beállítás található definíció létrehozása][0]
2. Válassza ki a **eseményindító** lapot, és adja meg, ha azt szeretné, hogy a csomag létrehozása a kívánt feltételeket. Adja meg például **folyamatos integrációt** a csomag létrehozásához, amikor egy forrás szabályozása be következik be.
3. Válassza ki a **adatforrás-beállítások** lapot, és győződjön meg arról, hogy a projektmappa szerepel-e a **vezérlő forrásmappa** oszlop, és a állapotát **aktív**.
4. Válassza ki a **Build alapértelmezett** fülre, és a Build vezérlő, ellenőrizze a build kiszolgáló nevét.  Válassza ki, a beállítás **másolat létrehozása a következő eldobási mappába kimeneti** , és adja meg a kívánt eldobott üzenetek helye.
5. Válassza ki a **folyamat** fülre. A folyamat lapon válassza ki az alapértelmezett sablon, a **Build**, válassza ki a projekt, ha nincs bejelölve, és bontsa ki a **speciális** szakasz a **Build** szakasz a rács.
6. Válasszon **MSBuild-argumentumok**, és állítsa be a megfelelő MSBuild parancssori argumentumok fenti a 2. lépésben leírtak szerint. Adja meg például **/t: /p:PublishDir közzététele =\\\\myserver\\esik\\**  csomag létrehozásához, és másolja a csomagfájlok a helyre \\ \\ myserver\\esik\\:

   ![MSBuild-argumentumok][2]

   > [!NOTE]
   > A fájlok másolása egy nyilvános megosztás megkönnyíti a csomagot a fejlesztési számítógépen manuális telepítése.
7. Tesztelje a build lépés sikeres ellenőrzés módosítva lett a projekt, vagy egy új build sorba. Új buildverziót, a csapat Explorer sorba kattintson a jobb gombbal **összes Build definíciók** majd **várólista új Build**.

## <a name="4-publish-a-package-using-a-powershell-script"></a>4: a PowerShell-parancsfájl használatával csomag közzététele
Ez a szakasz ismerteti, hogyan közzétehető a Cloud app csomag kimeneti Azure-ban a választható paraméterek: Windows PowerShell-parancsfájl összeállításához. Ezt a parancsfájlt a build a build egyéni automatizálás lépés után hívható. Azt a folyamatsablon munkafolyamat tevékenységei a Visual Studio TFS Team Build is hívható.

1. Telepítse a [Azure PowerShell-parancsmagok] [ Azure PowerShell cmdlets] (v0.6.1 vagy újabb).
   A parancsmag a telepítés fázisban szeretné telepíteni, a beépülő modulként. Vegye figyelembe, hogy a hivatalosan támogatott verziójú váltja fel a régebbi kínált a Codeplex webhelyen, bár a korábbi verziók volt számozott 2.x.x.
2. Indítsa el a Start menü használatával Azure PowerShell vagy a Start lap. Ily módon indul el, ha az Azure PowerShell-parancsmagok lesz betöltve.
3. A PowerShell-parancssorba, győződjön meg arról, hogy a PowerShell-parancsmagok a részleges parancs vannak betöltve `Get-Azure` majd nyomja le a Tab billentyűt a nyilatkozatot befejezésére.

   Ha ismételten nyomja meg a Tab billentyűt, különböző Azure PowerShell-parancsokat kell megjelennie.
4. Győződjön meg arról, hogy a ehhez az előfizetési adatai a .publishsettings fájlt importálja az Azure-előfizetéshez csatlakozhat.

   `Import-AzurePublishSettingsFile c:\scripts\WindowsAzure\default.publishsettings`

   Írja be a parancsot

   `Get-AzureSubscription`

   Ez az előfizetés információkat jeleníti meg. Győződjön meg arról, hogy minden rendben.
5. Ez a cikk a parancsfájlok mappába c: végéig elérhető parancsfájl-sablon mentése\\parancsfájlok\\WindowsAzure\\**PublishCloudService.ps1**.
6. Tekintse át a parancsfájl a Paraméterek szakaszban. Adja hozzá, vagy módosítsa az alapértelmezett értékeket. Sikeres explicit paraméterek mindig felülbírálhatja ezeket az értékeket.
7. Győződjön meg arról nincs érvényes felhőalapú szolgáltatás és a tárolási fiók létrejönnek az előfizetés, amely a közzététel parancsfájl célpontja is lehet. A tárfiók (blob-tároló) feltöltése és a központi telepítési csomag- és konfigurációs fájl ideiglenesen tárolja, a központi telepítés létrehozása közben használható.

   * Új felhőalapú szolgáltatás létrehozása, hívja meg ezt a parancsfájlt vagy használja a [Azure-portálon](https://portal.azure.com). A felhőszolgáltatás neve lesz egy teljesen minősített tartománynevet az előtag, és ezért egyedinek kell lennie.

         New-AzureService -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
   * Hozzon létre egy új tárfiókot, hogy a parancsprogram hívása vagy használja a [Azure-portálon](https://portal.azure.com). A tárfiók neve lesz egy teljesen minősített tartománynevet az előtag, és ezért egyedinek kell lennie. Próbálja meg az azonos név használata a felhőalapú szolgáltatás.

         New-AzureStorageAccount -ServiceName "mytestcloudservice" -Location "North Central US" -Label "mytestcloudservice"
8. A parancsprogram hívása közvetlenül az Azure PowerShell vagy a gazdagép build automatizálás a csomag létrehozás után a parancsfájl kábelezést.

   > [!IMPORTANT]
   > A parancsfájl mindig törölje, vagy cserélje le a meglévő telepítések alapértelmezés szerint, ha azokat. Ez akkor szükségesek, hogy lehetővé teszik a folyamatos Automation ahol nincs felhasználói adatkérésekhez lehetséges.
   >
   >

   **1. példa:** a szolgáltatás átmeneti környezet folyamatos üzembe helyezés:

       PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Staging -serviceName mycloudservice -storageAccountName mystoragesaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

   Ez általában következnek teszt futtatásakor ellenőrzési és a virtuális IP-címcsere. A virtuális IP-címcsere megteheti a [Azure-portálon](https://portal.azure.com) vagy a Move-üzembe helyezési parancsmagjával.

   **2. példa:** folyamatos üzembe helyezést az éles környezetbe, egy dedikált teszt szolgáltatás

       PowerShell c:\scripts\windowsazure\PublishCloudService.ps1 -environment Production -enableDeploymentUpgrade 1 -serviceName mycloudservice -storageAccountName mystorageaccount -packageLocation c:\drops\app.publish\ContactManager.Azure.cspkg -cloudConfigLocation c:\drops\app.publish\ServiceConfiguration.Cloud.cscfg -subscriptionDataFile c:\scripts\default.publishsettings

   **Távoli asztali:**

   Ha a távoli asztal engedélyezve van az Azure-projekt további egyszeri lépések végrehajtásával győződjön meg arról, ez a parancsfájl által megcélzott összes felhőszolgáltatás a megfelelő tanúsítványt a felhőalapú szolgáltatás feltöltött kell.

   Keresse meg a tanúsítvány ujjlenyomata értékeket, amelyet a szerepkörök várt. Az ujjlenyomat értékei látható a felhő konfigurációs fájl (pl. ServiceConfiguration.Cloud.cscfg) tanúsítványok szakaszában. Célszerű is látható, a távoli asztali konfigurálása párbeszédpanel a Visual Studio megjelenítésekor beállítások és megtekintése a kiválasztott tanúsítvány.

       <Certificates>
             <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption" thumbprint="C33B6C432C25581601B84C80F86EC2809DC224E8" thumbprintAlgorithm="sha1" />
       </Certificates>

   A távoli asztal-tanúsítványok feltöltésére használja a következő parancsmag parancsfájlt egyszeri telepítő lépésként:

       Add-AzureCertificate -serviceName <CLOUDSERVICENAME> -certToDeploy (get-item cert:\CurrentUser\MY\<THUMBPRINT>)

   Példa:

       Add-AzureCertificate -serviceName 'mytestcloudservice' -certToDeploy (get-item cert:\CurrentUser\MY\C33B6C432C25581601B84C80F86EC2809DC224E8

   Másik lehetőségként exportálása a titkos kulcsot tartalmazó PFX tanúsítványfájl és -tanúsítványok feltöltésére minden cél felhőalapú szolgáltatás használata a [Azure-portálon](https://portal.azure.com).

   <!---
   Fixing broken links for Azure content migration from ACOM to DOCS. I'm unable to find a replacement links, so I'm commenting out this reference for now. The author can investigate in the future. "Read the following article to learn more: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx.
   -->
   **Frissítés a központi telepítés vagy. Törölje a központi telepítés –\> új központi telepítés**

   A parancsfájl alapértelmezés szerint végrehajtja az frissítés központi telepítése ($enableDeploymentUpgrade = 1) Ha nem paraméter átadott vagy explicit módon átadott 1 érték. Az egyetlen példány Ez azt az előnyt, teljes körű telepítésére rövidebb ideig tart. A magas rendelkezésre állású, ez is azzal az előnnyel jár, hogy bizonyos esetekben, mások pedig futtató igénylő példányok frissítése (a frissítési tartomány érdekében), valamint a VIP nem lesz törölve.

   Frissítés telepítése le kell tiltani a parancsfájl ($enableDeploymentUpgrade = 0), vagy úgy, hogy *- enableDeploymentUpgrade 0* paraméterként, amely megváltoztatja a parancsfájl viselkedését, először törölje a meglévő telepítést, és ezután hozzon létre egy új központi telepítés.

   > [!IMPORTANT]
   > A parancsfájl mindig törölje, vagy cserélje le a meglévő telepítések alapértelmezés szerint, ha azokat. Ez az Automation folyamatos kézbesítési ahhoz szükséges, hogy nincs felhasználói/operátor kérdés esetén lehetséges.
   >
   >

## <a name="5-publish-a-package-using-tfs-team-build"></a>5: közzétenni a csomagot, a TFS-csoport létrehozása
Ez az opcionális lépés csatlakozik a TFS csoport létrehozása a 4. lépésében létrehozott parancsfájlt, amely a csomag build Azure közzétételi kezeli. Ez terjed ki, hogy fut a közzététel tevékenység a munkafolyamat végén build definition használt folyamat sablon módosításával. A közzététel tevékenység végrehajtása addig a build a paraméterek átadása a PowerShell-parancsot. Az MSBuild kimenete célozza, és parancsfájl közzé lesz a szabványos felépítési művelet kimenetében be kell adatcsatornán.

1. Szerkessze a Build definíciót felelős folyamatos központi telepítése.
2. Válassza ki a **folyamat** fülre.
3. Hajtsa végre a [ezeket az utasításokat](http://msdn.microsoft.com/library/dd647551.aspx) egy tevékenység-projektjét, amely az összeállítási folyamat sablon hozzáadásához töltse le az alapértelmezett sablon, adja hozzá a projekthez, és jelölje be. A létrehozási folyamat sablon adjon új nevet, például a AzureBuildProcessTemplate.
4. Lépjen vissza a **folyamat** lapot, és használjon **részletek megjelenítése** elérhető összeállítási folyamat sablonok listájának megjelenítéséhez. Válassza ki a **új...**  gombra, és keresse meg a most hozzáadott és be van jelölve, projekt. Keresse meg az újonnan létrehozott sablont, és válassza a **OK**.
5. Nyissa meg a kijelölt folyamatsablont szerkesztésre. Úgy is megnyithatja közvetlenül a munkafolyamat-tervezőben vagy a XAML használható az XML-szerkesztőt.
6. Adja hozzá az alábbi listán szereplő új argumentumok külön sorban elemeket a munkafolyamat-Tervező argumentumok lapján. Minden argumentum irányba kell rendelkeznie, és írja be a = = karakterlánc. Ezek használandó adatfolyam paramétereit a munkafolyamatba build definícióból használt majd beolvasni a közzététel parancsfájl hívni.

       SubscriptionName
       StorageAccountName
       CloudConfigLocation
       PackageLocation
       Environment
       SubscriptionDataFileLocation
       PublishScriptLocation
       ServiceName

   ![Argumentumok listájának megjelenítése][3]

   A megfelelő XAML így néz ki:

       <Activity  _ />
         <x:Members>
           <x:Property Name="BuildSettings" Type="InArgument(mtbwa:BuildSettings)" />
           <x:Property Name="TestSpecs" Type="InArgument(mtbwa:TestSpecList)" />
           <x:Property Name="BuildNumberFormat" Type="InArgument(x:String)" />
           <x:Property Name="CleanWorkspace" Type="InArgument(mtbwa:CleanWorkspaceOption)" />
           <x:Property Name="RunCodeAnalysis" Type="InArgument(mtbwa:CodeAnalysisOption)" />
           <x:Property Name="SourceAndSymbolServerSettings" Type="InArgument(mtbwa:SourceAndSymbolServerSettings)" />
           <x:Property Name="AgentSettings" Type="InArgument(mtbwa:AgentSettings)" />
           <x:Property Name="AssociateChangesetsAndWorkItems" Type="InArgument(x:Boolean)" />
           <x:Property Name="CreateWorkItem" Type="InArgument(x:Boolean)" />
           <x:Property Name="DropBuild" Type="InArgument(x:Boolean)" />
           <x:Property Name="MSBuildArguments" Type="InArgument(x:String)" />
           <x:Property Name="MSBuildPlatform" Type="InArgument(mtbwa:ToolPlatform)" />
           <x:Property Name="PerformTestImpactAnalysis" Type="InArgument(x:Boolean)" />
           <x:Property Name="CreateLabel" Type="InArgument(x:Boolean)" />
           <x:Property Name="DisableTests" Type="InArgument(x:Boolean)" />
           <x:Property Name="GetVersion" Type="InArgument(x:String)" />
           <x:Property Name="PrivateDropLocation" Type="InArgument(x:String)" />
           <x:Property Name="Verbosity" Type="InArgument(mtbw:BuildVerbosity)" />
           <x:Property Name="Metadata" Type="mtbw:ProcessParameterMetadataCollection" />
           <x:Property Name="SupportedReasons" Type="mtbc:BuildReason" />
           <x:Property Name="SubscriptionName" Type="InArgument(x:String)" />
           <x:Property Name="StorageAccountName" Type="InArgument(x:String)" />
           <x:Property Name="CloudConfigLocation" Type="InArgument(x:String)" />
           <x:Property Name="PackageLocation" Type="InArgument(x:String)" />
           <x:Property Name="Environment" Type="InArgument(x:String)" />
           <x:Property Name="SubscriptionDataFileLocation" Type="InArgument(x:String)" />
           <x:Property Name="PublishScriptLocation" Type="InArgument(x:String)" />
           <x:Property Name="ServiceName" Type="InArgument(x:String)" />
         </x:Members>

         <this:Process.MSBuildArguments>
7. Felvehet egy új sorozatot futtassa az ügynök végén:

   1. Először vegyen fel egy érvényes parancsfájl kereséséhez Ha utasítás tevékenységgel. Erre az értékre állítsa be a következő feltételt:

          Not String.IsNullOrEmpty(PublishScriptLocation)
   2. A Then esetben ha utasítás adjon hozzá egy új feladatütemezési tevékenységet. Állítsa be a kezdő közzététele megjelenített neve
   3. A kezdő és a kijelölt feladatütemezési közzétételéhez adja hozzá az alábbi listán szereplő új változók a munkafolyamat-Tervező változók lapján külön sorként. Minden változót kell változótípus = karakterlánc és a hatókör = Start közzététele. Ezek használandó adatfolyam paramétereit a munkafolyamatba build definícióból használt majd beolvasni a közzététel parancsfájl hívni.

      * SubscriptionDataFilePath, karakterlánc típusú
      * PublishScriptFilePath, karakterlánc típusú

        ![Új változók][4]
   4. Ha a TFS 2012 vagy régebbi használ, egy ConvertWorkspaceItem tevékenység hozzáadása az új feladatütemezési elején. Ha a TFS 2013 vagy újabb verzió használata esetén egy GetLocalPath tevékenység hozzáadása az új feladatütemezési elején. Egy ConvertWorkspaceItem beállítása a tulajdonságok az alábbiak szerint: irány ServerToLocal, DisplayName = = "Convert közzétételéhez parancsfájl fájlnév" bemeneti = "PublishScriptLocation", az eredmény = "PublishScriptFilePath" munkaterület = "Munkaterület". GetLocalPath tevékenységhez a "PublishScriptLocation" IncomingPath, és az eredményt a "PublishScriptFilePath" tulajdonságának beállítása. Ez a tevékenység az elérési út a közzététel parancsfájlt a kiszolgáló helyét (ha van ilyen) TFS egy normál helyi lemez elérési útja alakítja.
   5. Ha TFS 2012 vagy régebbi használ, adja hozzá egy másik ConvertWorkspaceItem tevékenység az új feladatütemezési végén. Irány ServerToLocal, DisplayName = = "Átalakításáról előfizetés fájlnév" bemeneti = "SubscriptionDataFileLocation", az eredmény = "SubscriptionDataFilePath" munkaterület = "Munkaterület". Ha a TFS 2013, vagy később, a másik GetLocalPath hozzáadása. IncomingPath = "SubscriptionDataFileLocation", és az eredmény = "SubscriptionDataFilePath."
   6. InvokeProcess tevékenység hozzáadása az új feladatütemezési végén.
      Ez a tevékenység PowerShell.exe meghívja a Build definíció által átadott argumentumok.

      + Argumentumok = String.Format ("-""{0}" "- szolgáltatásnév {1} - storageAccountName {2} - packageLocation""{3}" "- cloudConfigLocation""{4}" "– subscriptionDataFile""{5}" "– selectedSubscription {6} fájlt-környezet""{7}" "",  PublishScriptFilePath, ServiceName, StorageAccountName, PackageLocation, CloudConfigLocation, SubscriptionDataFilePath, SubscriptionName, környezet)
      + DisplayName = Execute parancsfájl közzététele
      + FileName = "PowerShell" (idézőjelek között)
      + OutputEncoding = System.Text.Encoding.GetEncoding(System.Globalization.CultureInfo.InstalledUICulture.TextInfo.OEMCodePage)
   7. Az a **szabványos kimeneti kezelni** a InvokeProcess a szövegmező területen állítsa a szövegmező "data". Ez a változó a szabványos kimeneti adatok tárolására.
   8. Adjon hozzá egy WriteBuildMessage tevékenységet, csak az alábbiakban a **szabványos kimeneti kezelni** szakasz. Állítsa be a fontosság = "Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High" és az üzenet = "data". Ez biztosítja, hogy a normál a kimenetbe a parancsfájl a felépítési művelet kimenetében beolvasása írni.
   9. Az a **kezelni hiba kimeneti** a InvokeProcess a szövegmező területen állítsa a szövegmező "data". Ez a változó a standard hiba adatok tárolására.
   10. Adjon hozzá egy WriteBuildError tevékenységet, csak az alábbiakban a **kezelni hiba kimeneti** szakasz. Állítsa be a Message = "data". Ez biztosítja, a standard hibák, a parancsfájl a build hiba kimeneti beolvasása írni.
   11. Javítsa ki a hibákat, kék felkiáltójel jelek jelölik. A hibával kapcsolatos javaslat lekérni az felkiáltójel közötti mutasson. Törölje a hibákat a munkafolyamat mentése.

   A közzététel munkafolyamat-tevékenység a végeredményt fog kinézni a tervezőben:

   ![Munkafolyamat-tevékenységek][5]

   A közzététel munkafolyamat-tevékenység a végeredményt fog kinézni XAML-kódban:

       <If Condition="[Not String.IsNullOrEmpty(PublishScriptLocation)]" sap2010:WorkflowViewState.IdRef="If_1">
           <If.Then>
             <Sequence DisplayName="Start Publish" sap2010:WorkflowViewState.IdRef="Sequence_4">
               <Sequence.Variables>
                 <Variable x:TypeArguments="x:String" Name="SubscriptionDataFilePath" />
                 <Variable x:TypeArguments="x:String" Name="PublishScriptFilePath" />
               </Sequence.Variables>
               <mtbwa:ConvertWorkspaceItem DisplayName="Convert publish script filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_1" Input="[PublishScriptLocation]" Result="[PublishScriptFilePath]" Workspace="[Workspace]" />
               <mtbwa:ConvertWorkspaceItem DisplayName="Convert subscription filename" sap2010:WorkflowViewState.IdRef="ConvertWorkspaceItem_2" Input="[SubscriptionDataFileLocation]" Result="[SubscriptionDataFilePath]" Workspace="[Workspace]" />
               <mtbwa:InvokeProcess Arguments="[String.Format(&quot; -File &quot;&quot;{0}&quot;&quot; -serviceName {1}&#xD;&#xA;            -storageAccountName {2} -packageLocation &quot;&quot;{3}&quot;&quot;&#xD;&#xA;            -cloudConfigLocation &quot;&quot;{4}&quot;&quot; -subscriptionDataFile &quot;&quot;{5}&quot;&quot;&#xD;&#xA;            -selectedSubscription {6} -environment &quot;&quot;{7}&quot;&quot;&quot;,&#xD;&#xA;            PublishScriptFilePath, ServiceName, StorageAccountName,&#xD;&#xA;            PackageLocation, CloudConfigLocation,&#xD;&#xA;            SubscriptionDataFilePath, SubscriptionName, Environment)]" DisplayName="'Execute Publish Script'" FileName="[PowerShell]" sap2010:WorkflowViewState.IdRef="InvokeProcess_1">
                 <mtbwa:InvokeProcess.ErrorDataReceived>
                   <ActivityAction x:TypeArguments="x:String">
                     <ActivityAction.Argument>
                       <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                     </ActivityAction.Argument>
                     <mtbwa:WriteBuildError Message="{x:Null}" sap2010:WorkflowViewState.IdRef="WriteBuildError_1" />
                   </ActivityAction>
                 </mtbwa:InvokeProcess.ErrorDataReceived>
                 <mtbwa:InvokeProcess.OutputDataReceived>
                   <ActivityAction x:TypeArguments="x:String">
                     <ActivityAction.Argument>
                       <DelegateInArgument x:TypeArguments="x:String" Name="data" />
                     </ActivityAction.Argument>
                     <mtbwa:WriteBuildMessage sap2010:WorkflowViewState.IdRef="WriteBuildMessage_2" Importance="[Microsoft.TeamFoundation.Build.Client.BuildMessageImportance.High]" Message="[data]" mva:VisualBasic.Settings="Assembly references and imported namespaces serialized as XML namespaces" />
                   </ActivityAction>
                 </mtbwa:InvokeProcess.OutputDataReceived>
               </mtbwa:InvokeProcess>
             </Sequence>
           </If.Then>
         </If>
       </Sequence>
8. Mentse a build munkafolyamat-sablon és a felvétel ezt a fájlt.
9. Szerkessze a build definíciót (lezárhatja, amennyiben már meg nyitva), és válassza ki a **új** gombra kattint, ha még nem látja az új sablon folyamat sablonok listáján.
10. A paraméter tulajdonságértékei állíthatók egyebek szakaszában az alábbiak szerint:

    1. CloudConfigLocation = "c:\\esik\\app.publish\\ServiceConfiguration.Cloud.cscfg" *ezt az értéket, amelyből származik: ($PublishDir)ServiceConfiguration.Cloud.cscfg*
    2. PackageLocation = "c:\\esik\\app.publish\\ContactManager.Azure.cspkg" *ezt az értéket, amelyből származik: ($PublishDir)($ProjectName) .cspkg*
    3. PublishScriptLocation = "c:\\parancsfájlok\\WindowsAzure\\PublishCloudService.ps1"
    4. ServiceName = "mycloudservicename" *itt használja a megfelelő felhőszolgáltatás neve*
    5. Környezet = "Átmeneti"
    6. StorageAccountName = "mystorageaccountname" *itt használja a megfelelő tárfiók neve*
    7. SubscriptionDataFileLocation = "c:\\parancsfájlok\\WindowsAzure\\Subscription.xml"
    8. SubscriptionName = "alapértelmezett"

    ![Tulajdonság értékei][6]
11. Menti a módosításokat a Build definíciójához.
12. Feldolgozási sor végrehajtása mindkét a csomag összeállítása és közzététele Build. Ha egy eseményindító folyamatos integráció beállítása, végrehajtja a Ez a viselkedés a minden egyes bejelentkezéskor.

### <a name="publishcloudserviceps1-script-template"></a>PublishCloudService.ps1 parancsfájl sablon
```powershell
Param(  $serviceName = "",
        $storageAccountName = "",
        $packageLocation = "",
        $cloudConfigLocation = "",
        $environment = "Staging",
        $deploymentLabel = "ContinuousDeploy to $servicename",
        $timeStampFormat = "g",
        $alwaysDeleteExistingDeployments = 1,
        $enableDeploymentUpgrade = 1,
        $selectedsubscription = "default",
        $subscriptionDataFile = ""
     )


function Publish()
{
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot -ErrorVariable a -ErrorAction silentlycontinue
    if ($a[0] -ne $null)
    {
        Write-Output "$(Get-Date -f $timeStampFormat) - No deployment is detected. Creating a new deployment. "
    }
    #check for existing deployment and then either upgrade, delete + deploy, or cancel according to $alwaysDeleteExistingDeployments and $enableDeploymentUpgrade boolean variables
    if ($deployment.Name -ne $null)
    {
        switch ($alwaysDeleteExistingDeployments)
        {
            1
            {
                switch ($enableDeploymentUpgrade)
                {
                    1  #Update deployment inplace (usually faster, cheaper, won't destroy VIP)
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Upgrading deployment."
                        UpgradeDeployment
                    }
                    0  #Delete then create new deployment
                    {
                        Write-Output "$(Get-Date -f $timeStampFormat) - Deployment exists in $servicename.  Deleting deployment."
                        DeleteDeployment
                        CreateNewDeployment

                    }
                } # switch ($enableDeploymentUpgrade)
            }
            0
            {
                Write-Output "$(Get-Date -f $timeStampFormat) - ERROR: Deployment exists in $servicename.  Script execution cancelled."
                exit
            }
        } #switch ($alwaysDeleteExistingDeployments)
    } else {
            CreateNewDeployment
    }
}

function CreateNewDeployment()
{
    write-progress -id 3 -activity "Creating New Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: In progress"

    $opstat = New-AzureDeployment -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Creating New Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Creating New Deployment: Complete, Deployment ID: $completeDeploymentID"

    StartInstances
}

function UpgradeDeployment()
{
    write-progress -id 3 -activity "Upgrading Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: In progress"

    # perform Update-Deployment
    $setdeployment = Set-AzureDeployment -Upgrade -Slot $slot -Package $packageLocation -Configuration $cloudConfigLocation -label $deploymentLabel -ServiceName $serviceName -Force

    $completeDeployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $completeDeploymentID = $completeDeployment.deploymentid

    write-progress -id 3 -activity "Upgrading Deployment" -completed -Status "Complete"
    Write-Output "$(Get-Date -f $timeStampFormat) - Upgrading Deployment: Complete, Deployment ID: $completeDeploymentID"
}

function DeleteDeployment()
{

    write-progress -id 2 -activity "Deleting Deployment" -Status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: In progress"

    #WARNING - always deletes with force
    $removeDeployment = Remove-AzureDeployment -Slot $slot -ServiceName $serviceName -Force

    write-progress -id 2 -activity "Deleting Deployment: Complete" -completed -Status $removeDeployment
    Write-Output "$(Get-Date -f $timeStampFormat) - Deleting Deployment: Complete"

}

function StartInstances()
{
    write-progress -id 4 -activity "Starting Instances" -status "In progress"
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: In progress"

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $runstatus = $deployment.Status

    if ($runstatus -ne 'Running')
    {
        $run = Set-AzureDeployment -Slot $slot -ServiceName $serviceName -Status Running
    }
    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $oldStatusStr = @("") * $deployment.RoleInstanceList.Count

    while (-not(AllInstancesRunning($deployment.RoleInstanceList)))
    {
        $i = 1
        foreach ($roleInstance in $deployment.RoleInstanceList)
        {
            $instanceName = $roleInstance.InstanceName
            $instanceStatus = $roleInstance.InstanceStatus

            if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
            {
                $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
                Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
            }

            write-progress -id (4 + $i) -activity "Starting Instance '$instanceName'" -status "$instanceStatus"
            $i = $i + 1
        }

        sleep -Seconds 1

        $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    }

    $i = 1
    foreach ($roleInstance in $deployment.RoleInstanceList)
    {
        $instanceName = $roleInstance.InstanceName
        $instanceStatus = $roleInstance.InstanceStatus

        if ($oldStatusStr[$i - 1] -ne $roleInstance.InstanceStatus)
        {
            $oldStatusStr[$i - 1] = $roleInstance.InstanceStatus
            Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instance '$instanceName': $instanceStatus"
        }

        $i = $i + 1
    }

    $deployment = Get-AzureDeployment -ServiceName $serviceName -Slot $slot
    $opstat = $deployment.Status

    write-progress -id 4 -activity "Starting Instances" -completed -status $opstat
    Write-Output "$(Get-Date -f $timeStampFormat) - Starting Instances: $opstat"
}

function AllInstancesRunning($roleInstanceList)
{
    foreach ($roleInstance in $roleInstanceList)
    {
        if ($roleInstance.InstanceStatus -ne "ReadyRole")
        {
            return $false
        }
    }

    return $true
}

#configure powershell with Azure 1.7 modules
Import-Module Azure

#configure powershell with publishsettings for your subscription
$pubsettings = $subscriptionDataFile
Import-AzurePublishSettingsFile $pubsettings
Set-AzureSubscription -CurrentStorageAccountName $storageAccountName -SubscriptionName $selectedsubscription
Select-AzureSubscription $selectedsubscription

#set remaining environment variables for Azure cmdlets
$subscription = Get-AzureSubscription $selectedsubscription
$subscriptionname = $subscription.subscriptionname
$subscriptionid = $subscription.subscriptionid
$slot = $environment

#main driver - publish & write progress to activity log
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script started."
Write-Output "$(Get-Date -f $timeStampFormat) - Preparing deployment of $deploymentLabel for $subscriptionname with Subscription ID $subscriptionid."

Publish

$deployment = Get-AzureDeployment -slot $slot -serviceName $servicename
$deploymentUrl = $deployment.Url

Write-Output "$(Get-Date -f $timeStampFormat) - Created Cloud Service with URL $deploymentUrl."
Write-Output "$(Get-Date -f $timeStampFormat) - Azure Cloud Service deploy script finished."
```

## <a name="next-steps"></a>Következő lépések
Engedélyezheti a távoli hibakeresés folyamatos kézbesítés használata esetén [folyamatos kézbesítési közzététele az Azure-bA használatakor távoli hibakeresésének engedélyezése](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md).

[Team Foundation Build Service]: https://msdn.microsoft.com/library/ee259687.aspx
[.NET Framework 4]: https://www.microsoft.com/download/details.aspx?id=17851
[.NET Framework 4.5]: https://www.microsoft.com/download/details.aspx?id=30653
[.NET Framework 4.5.2]: https://www.microsoft.com/download/details.aspx?id=42643
[Scale out your build system]: https://msdn.microsoft.com/library/dd793166.aspx
[Deploy and configure a build server]: https://msdn.microsoft.com/library/ms181712.aspx
[Azure PowerShell cmdlets]: /powershell/azureps-cmdlets-docs
[0]: ./media/cloud-services-dotnet-continuous-delivery/tfs-01bc.png
[2]: ./media/cloud-services-dotnet-continuous-delivery/tfs-02.png
[3]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-03.png
[4]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-04.png
[5]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-05.png
[6]: ./media/cloud-services-dotnet-continuous-delivery/common-task-tfs-06.png
