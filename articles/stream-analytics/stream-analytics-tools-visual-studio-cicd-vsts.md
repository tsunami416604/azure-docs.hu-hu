---
title: Azure Stream Analytics-feladat üzembe helyezése a CI/CD-vel és egy VSTS-oktatóanyag használatával
description: A cikk azt írja le, hogyan helyezhet üzembe egy Azure Stream Analytics-feladatot a CI/CD-vel, a VSTS használatával.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 7/10/2018
ms.openlocfilehash: d4f1e188a1a145ba3be5fb45d2b0ea4d0bfd57a7
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2018
ms.locfileid: "41920087"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-vsts"></a>Oktatóanyag: Azure Stream Analytics-feladat üzembe helyezése a CI/CD-vel, a VSTS használatával
Ez az oktatóanyag azt ismerteti, hogyan lehet folyamatos integrációt és üzembe helyezést beállítani egy Azure Stream Analytics-feladathoz a Visual Studio Team Services használatával. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Forráskezelés hozzáadása a projekthez
> * Builddefiníció létrehozása a Team Services használatával
> * Kiadási definíció létrehozása a Team Services használatával
> * Alkalmazás automatikus üzembe helyezése és frissítése

## <a name="prerequisites"></a>Előfeltételek
Mielőtt hozzálátna, győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Telepítse a [Visual Studiót](stream-analytics-tools-for-visual-studio-install.md) és az **Azure-fejlesztési** vagy az **Adattárolási és -feldolgozási** számítási feladatokat.
* Hozzon létre egy [Stream Analytics-projektet a Visual Studióban](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-vs).
* Hozzon létre egy [Visual Studio Team Services](https://visualstudio.microsoft.com/team-services/)-fiókot.

## <a name="configure-nuget-package-dependency"></a>NuGet csomagfüggőség konfigurálása
Amennyiben automatikus létrehozást vagy automatikus telepítést kíván alkalmazni egy tetszőleges gépen, a `Microsoft.Azure.StreamAnalytics.CICD` NuGet-csomagot kell használnia. Ez tartalmazza az MSBuild-, helyi futtatási, valamint a telepítési eszközöket, amelyek támogatják a Stream Analytics Visual Studio-projektek folyamatos integrációs és üzembehelyezési folyamatait. További információ: [Stream Analytics CI/CD-eszközök](stream-analytics-tools-for-visual-studio-cicd.md).

Adja hozzá a **packages.config** fájlt a projekt könyvtárához.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>A Visual Studio-megoldás megosztása egy új Team Services Git-adattárban
Az alkalmazás forrásfájljait megoszthatja a Team Services egyik csoportprojektjében, és ezáltal buildeket hozhat létre.  

1. Hozzon létre egy új helyi Git-adattárat a projekthez. Ehhez válassza ki a Visual Studio jobb alsó sarkában található állapotsoron az **Add to Source Control** (Hozzáadás a forráskezelőhöz), majd a **Git** elemet. 

2. A **Team Explorer** **Synchronization** (Szinkronizálás) nézetében válassza ki a **Push to Visual Studio Team Services** (Leküldés a Visual Studio Team Services szolgáltatásba) alatt található **Publish Git Repo** (Git-adattár közzététele) gombot.

   ![Leküldéses Git-adattár](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishgitrepo.png)

3. Ellenőrizze az e-mail-címet, és válassza ki a saját fiókját a **Team Services Domain** (Team Services tartomány) legördülő listájából. Adja meg az adattár nevét, majd válassza ki a **Publish repository** (Adattár közzététele) lehetőséget.

   ![Leküldéses Git-adattár](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishcode.png)

    Az adattár közzétételével egy új csoportprojekt jön létre a fiókjában a helyi adattáréval azonos néven. Ha egy már meglévő csapatprojektben kíván adattárat létrehozni, az **Adattár neve** mellett kattintson az **Advanced** (Speciális) elemre, és válassza ki a csoportprojektet. A kód böngészőben való megtekintéséhez válassza a **See it on the web** (Megtekintés a weben) lehetőséget.
 
## <a name="configure-continuous-delivery-with-vsts"></a>Folyamatos továbbítás konfigurálása a VSTS használatával
A Team Services builddefiníciója egy olyan munkafolyamatot ír le, amely egymás után végrehajtott létrehozási lépések sorozatából áll. További tudnivalók [a Team Services builddefinícióiról](https://www.visualstudio.com/docs/build/define/create). 

A Team Services kiadási definíciója olyan munkafolyamatot ír le, amely egy alkalmazáscsomagot telepít egy fürtre. Együttes használatuk esetén a builddefiníció és a kiadási definíció a teljes munkafolyamatot végrehajtja, amely a forrásfájlokkal kezdődik, és a fürtön futó alkalmazással ér véget. További tudnivalók a Team Services [kiadási definícióiról](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition).

### <a name="create-a-build-definition"></a>Builddefiníció létrehozása
Nyisson meg egy webböngészőt, majd keresse meg a [Visual Studio Team Servicesben](https://app.vsaex.visualstudio.com/) létrehozott csapatprojektet. 

1. A **Build & Release** (Build és kiadás) lapon válassza a **Builds** (Buildek) és a **+ New** (+ Új) lehetőséget.  Válassza a **VSTS Git** és a **Continue** (Folytatás) lehetőséget.
    
    ![Forrás kiválasztása](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source.png)

2. A **Select a template** (Sablon kiválasztása) területen kattintson az **Empty Process** (Üres folyamat) elemre, ha egy üres definícióval kezdené meg a munkát.
    
    ![Buildsablon kiválasztása](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template.png)

3. A **Triggers** (Eseményindítók) lehetőségnél engedélyezze a folyamatos integrációt az **Enable continuous integration** (Folyamatos integráció engedélyezése) triggerállapot bejelölésével.  Válassza ki a **Save and queue** (Mentés és üzenetsorba helyezés) elemet a build manuális elindításához. 
    
    ![Triggerállapot](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger.png)

4. A buildek leküldés vagy bejelentkezés hatására is aktiválódnak. A build folyamatának ellenőrzéséhez váltson át a **Builds** (Buildek) lapra.  Miután meggyőződött arról, hogy a build végrehajtása sikeresen megtörtént, létre kell hoznia a kiadási definíciót, amely telepíti az alkalmazást egy fürtre. Kattintson a jobb gombbal a builddefinícióra, és válassza az **Edit** (Szerkesztés) lehetőséget.

5.  A **Tasks** (Feladatok) között az **Agent queue** (Ügynöküzenetsor) megfelelőjeként adja meg a következőt: Hosted.
    
    ![Ügynöküzenetsor kiválasztása](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue.png) 

6. Kattintson a **Phase 1** (1. fázis) szakasz **+** elemére, majd adjon hozzá egy **NuGet**-feladatot.
    
    ![NuGet-feladat hozzáadása](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget.png)

7. Bontsa ki az **Advanced** (Speciális) pontot, és adja hozzá a `$(Build.SourcesDirectory)\packages` elemet a **célkönyvtárhoz**. Tartsa meg a fennmaradó alapértelmezett NuGet konfigurációs értékeket.

   ![NuGet-feladat konfigurálása](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-config.png)

8. Kattintson a **Phase 1** (1. fázis) szakasz **+** elemére, majd adjon hozzá egy **MSBuild**-feladatot.

   ![MSBuild-feladat hozzáadása](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild-task.png)

9. Cserélje az **MSBuild-argumentumokat** a következőkre:

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![MSBuild-feladat konfigurálása](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild.png)

10. Kattintson a **Phase 1** (1. fázis) szakasz **+** elemére, majd adja hozzá egy **Azure-erőforráscsoport üzembehelyezési** feladatát. 
    
    ![Azure-erőforráscsoport üzembehelyezési feladatának hozzáadása](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy.png)

11. Bontsa ki az **Azure Details** (Azure – részletek) pontot, és töltse ki a konfiguráció mezőit a következők szerint:
    
    |**Beállítás**  |**Ajánlott érték**  |
    |---------|---------|
    |Előfizetés  |  Válassza ki az előfizetését.   |
    |Műveletek  |  Erőforráscsoport létrehozása vagy frissítése   |
    |Erőforráscsoport  |  Adja meg az erőforráscsoport nevét.   |
    |Sablon  | [Saját megoldás elérési útja]\bin\Debug\Deploy\\[Saját projekt neve].JobTemplate.json   |
    |Sablon paraméterei  | [Saját megoldás elérési útja]\bin\Debug\Deploy\\[Saját projekt neve].JobTemplate.parameters.json   |
    |Sablon paramétereinek felülbírálása  | Írja be a szövegmezőbe a felülbírálni kívánt sablonparamétereket. Például: –storageName fabrikam –adminUsername $(vmusername) -adminPassword $(password) –azureKeyVaultName $(fabrikamFibre). Ez a tulajdonság nem kötelező, de a build hibaüzeneteket eredményezhet, ha a paraméterek nincsenek felülbírálva.    |
    
    ![Tulajdonságok beállítása](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy-2.png)

12. A builddefiníció teszteléséhez kattintson a **Save & Queue** (Mentés és várakozási sorba helyezés) gombra.
    
    ![Felülbírálási paraméterek beállítása](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-queue.png)

### <a name="failed-build-process"></a>Sikertelen buildelési folyamat
Lehet, hogy null értékű üzembehelyezési paraméterekről szóló hibaüzenetet kap, ha nem bírálta felül a sablon paramétereit a builddefiníció **Azure-erőforráscsoport üzembe helyezésének** feladatában. A probléma megoldásához térjen vissza a builddefinícióhoz, és bírálja felül a null értékű paramétereket.

   ![A buildelési folyamat sikertelen](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>Módosítások véglegesítse és leküldése a kiadás indításához
A folyamatos integrációs folyamat működésének ellenőrzéséhez adjon be néhány kódmódosítást a Team Servicesbe.    

A kód írása közben eszközölt módosításokat a Visual Studio automatikusan követi. A helyi Git-adattár módosításainak véglegesítéséhez válassza ki a függőben lévő módosítások ikonját, amely a képernyő jobb oldalának alján lévő állapotsoron található.

1. A Team Explorer **Changes** (Módosítások) nézetében adjon meg egy üzenetet a frissítés leírásával, majd véglegesítse a módosításokat.

    ![Módosítások véglegesítése és leküldése](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes.png)

2. Válassza ki a közzé nem tett változások ikonját az állapotsávon vagy a Sync (Szinkronizálás) nézetet a Team Explorerben. A **Push** (Leküldés) elem kiválasztásával frissítheti a kódot a Team Services/TFS szolgáltatásban.

    ![Módosítások véglegesítése és leküldése](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-2.png)

A Team Services szolgáltatásba leküldött módosítások automatikusan aktiválnak egy buildet.  Ha a builddefiníció sikeresen befejeződött, a kiadás automatikusan létrejön, és elindítja a fürtön a feladat frissítését.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a streamelési feladatot és az összes kapcsolódó erőforrást. A feladat törlésével megelőzheti a feladat által használt streamelési egységek kiszámlázását. Ha a jövőben szeretné még használni a feladatot, leállíthatja, és később újraindíthatja, amikor szüksége lesz rá. Ha már nem használja a feladatot, akkor a következő lépésekkel az oktatóanyagban létrehozott összes erőforrást törölheti:

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a létrehozott erőforrás nevére.  
2. Az erőforráscsoport lapján kattintson a **Törlés** elemre, írja be a törölni kívánt erőforrás nevét a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni arról, hogyan használhatja a Visual Studio Azure Stream Analytics-eszközeit egy folyamatos integrációs és üzembehelyezési folyamat létrehozásához, folytassa az olvasást a CI/CD-folyamatot bemutató cikkel:

> [!div class="nextstepaction"]
> [Folyamatos integráció és fejlesztés a Stream Analytics eszközeivel](stream-analytics-tools-for-visual-studio-cicd.md)