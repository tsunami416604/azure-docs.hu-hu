---
title: Folyamatos integrációt, a Visual STUDIO Team Services használatával Azure erőforráscsoport-projektek |} Microsoft Docs
description: Ismerteti, hogyan lehet a Visual Studio Team Services folyamatos integráció beállítása a Visual Studio használatával Azure erőforráscsoport-telepítési projektek alapján.
services: visual-studio-online
documentationcenter: na
author: mlearned
manager: erickson-doug
editor: ''
ms.assetid: b81c172a-be87-4adc-861e-d20b94be9e38
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: mlearned
ms.openlocfilehash: fc5a45c899cd72c051dd08f7db039565a57381a7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32192944"
---
# <a name="continuous-integration-in-visual-studio-team-services-using-azure-resource-group-deployment-projects"></a>A Visual Studio Team Services használatával Azure erőforráscsoport-telepítési projektek folyamatos integrációt
Központi telepítése egy Azure-sablon alapján, hogy feladatokat különböző szakaszaiban: Build, tesztelési, másolása az Azure-ba (más néven "Tesztelés"), és a sablon telepítéséhez. Sablonok telepíteni a Visual Studio Team Services (Visual STUDIO Team Services) két különböző módja van. Mindkét módszer adja meg ugyanazokat az eredményeket, tehát azt, amelyik a legjobban megfelel a munkafolyamat.

1. Egyetlen lépésben hozzáadása a build-definíciót, amely futtatja a PowerShell-parancsfájlt, amely az Azure-erőforráscsoport telepítési projekt (telepítés-AzureResourceGroup.ps1) szerepel. A parancsfájl összetevők másolja, és ezután telepíti a sablon.
2. Vegyen fel több Visual STUDIO Team Services build lépéseket, egyenként a szakasz feladatok.

Ez a cikk mindkét lehetőséget bemutatja. Az első lehetőség azt az előnyt, ugyanazzal a parancsfájllal fejlesztők a Visual Studio és az életciklus olyan egységének használják. A második lehetőség alternatívája a beépített parancsfájlt. Mindkét eljárás során feltételezzük, hogy már be van jelölve, a Visual STUDIO Team Services Visual Studio telepítési projekt.

## <a name="copy-artifacts-to-azure"></a>Összetevők másolása az Azure-bA
Függetlenül a forgatókönyvben, ha bármely, a sablon-üzembehelyezés szükséges összetevők kell Azure Resource Manager hozzáférést adhat őket. Ezen összetevők például a fájlok közé tartoznak:

* Beágyazott sablonok
* Konfigurációs és DSC parancsfájlok
* Bináris alkalmazásfájlokat

### <a name="nested-templates-and-configuration-scripts"></a>Beágyazott sablonok és konfigurációs parancsfájlokat
A Visual Studio által biztosított sablonok használatakor (vagy a Visual Studio kódtöredékek építését), a PowerShell-parancsfájl nem csak előkészíti az összetevők, azt is parameterizes tartozó különféle központi telepítéseken erőforrás URI-JÁNAK. A parancsfájl, majd másolja át az összetevők biztonságos tárolót az Azure-ban létrehoz egy SaS-jogkivonatot, hogy a tároló és ezután továbbítja ezt az információt a sablon-üzembehelyezés be. Lásd: [hozzon létre egy sablon-üzembehelyezés](https://msdn.microsoft.com/library/azure/dn790564.aspx) beágyazott sablonok tájékozódhat.  Feladatok használata a Visual STUDIO Team Services, válassza ki a megfelelő feladatokat a sablon üzembe helyezésére, és szükség esetén továbbítsa paraméterértékeket az átmeneti lépésben a sablon telepítése.

## <a name="set-up-continuous-deployment-in-vs-team-services"></a>A Visual STUDIO Team Services folyamatos üzembe helyezés beállítása
A PowerShell parancsfájl hívni a Visual STUDIO Team Services, módosítania a build definícióját. Röviden a lépések a következők: 

1. Szerkessze a build definíciót.
2. A Visual STUDIO Team Services Azure engedélyezési beállítása.
3. Adjon hozzá egy Azure PowerShell build lépést, amely a PowerShell-parancsfájlt a Azure erőforráscsoport-telepítési projekt hivatkozik.
4. Állítsa a *- ArtifactsStagingDirectory* paraméter egy projektet a Visual STUDIO Team Services együttműködni.

### <a name="detailed-walkthrough-for-option-1"></a>Részletes bemutató az 1. lehetőség
Az alábbi eljárások végigvezetik a Visual STUDIO Team Services, amelyen a PowerShell-parancsfájlt a projektben egy feladat használatával folyamatos telepítéséhez szükséges lépéseket. 

1. Szerkesztés a Visual STUDIO Team Services build definícióját, és adjon hozzá egy Azure PowerShell build lépést. Válassza ki a build definíciója szerint a **definíciók Build** kategória és válassza a **szerkesztése** hivatkozásra.
   
   ![Build definíciójának szerkesztése][0]
2. Adjon hozzá egy új **Azure PowerShell** összeállítása lépés a build definíciójához, és válassza ki a **összeállítása lépés hozzáadása...** gombra.
   
   ![Build lépés hozzáadása][1]
3. Válassza ki a **telepítés a feladat** kategória, jelölje be a **Azure PowerShell** feladat, és válassza a **Hozzáadás** gombra.
   
   ![Feladatok hozzáadása][2]
4. Válassza ki a **Azure PowerShell** összeállítása lépés, és töltse ki az értékeket.
   
   1. Ha már rendelkezik egy Azure-szolgáltatások végpontot hozzáadni a Visual STUDIO Team Services, válassza ki az előfizetést a **Azure-előfizetés** legördülő lista és a majd váltson át a következő szakaszban. 
      
      Ha Azure-szolgáltatás a végpont nem rendelkezik a Visual STUDIO Team Services, akkor vegyen fel egyet. Ez az alszakasz végigvezeti Önt a folyamaton. Ha az Azure-fiókjával (például Hotmail) Microsoft-fiókot használ, akkor szükséges, a következő lépéseket a egy egyszerű hitelesítést.
   2. Válassza ki a **kezelése** melletti hivatkozásra a **Azure-előfizetés** legördülő listában.
      
      ![Az Azure-előfizetések kezelése][3]
   3. Válasszon **Azure** a a **új szolgáltatási végpont** legördülő listában.
      
      ![Új szolgáltatási végpont][4]
   4. Az a **Azure-előfizetés hozzáadása** párbeszédpanelen jelölje ki a **egyszerű** lehetőséget.
      
      ![Szolgáltatás egyszerű beállítás][5]
   5. Adja hozzá az Azure-előfizetés adatainak a **Azure-előfizetés hozzáadása** párbeszédpanel megnyitásához. Meg kell adni a következő elemek:
      
      * Előfizetés azonosítója
      * Előfizetés neve
      * Egyszerű szolgáltatás azonosítója
      * Szolgáltatás egyszerű kulcs
      * Bérlő azonosítója
   6. Olyan nevet az Ön által választott, hogy a **előfizetés** neve mező. Ez az érték kövessék a a **Azure-előfizetés** legördülő listából válassza ki a Visual STUDIO Team Services. 
   7. Ha nem ismeri az Azure-előfizetése Azonosítóját, használhatja a következő parancsok valamelyikét, ennek lekéréséhez.
      
      PowerShell-parancsfájlok használata:
      
      `Get-AzureRmSubscription`
      
      Azure CLI esetén használja az alábbi parancsot:
      
      `azure account show`
   8. Beolvasása a szolgáltatás egyszerű Azonosítót, a szolgáltatás egyszerű kulcs és a bérlő azonosítója, hajtsa végre az eljárást [létrehozása az Active Directory-alkalmazás és szolgáltatás egyszerű portálon](resource-group-create-service-principal-portal.md) vagy [hitelesítése egy egyszerű Azure-ral Erőforrás-kezelő](resource-group-authenticate-service-principal.md).
   9. A szolgáltatás egyszerű azonosító, a szolgáltatás egyszerű kulcs és a bérlő azonosítója értékeket adja hozzá a **Azure-előfizetés hozzáadása** párbeszédpanel mezőbe, majd válassza ki a **OK** gombra.
      
      Most már rendelkezik egy érvényes egyszerű az Azure PowerShell-parancsfájl futtatásához.
5. Szerkessze a build definíciót, majd válassza a **Azure PowerShell** összeállítása lépés. Válassza ki az előfizetést a **Azure-előfizetés** legördülő listában. (Ha az előfizetés nem jelenik meg, válassza ki azt a **frissítése** Tovább gombra a **kezelése** hivatkozást.) 
   
   ![Azure PowerShell összeállítási feladat konfigurálása][8]
6. Adja meg a telepítés-AzureResourceGroup.ps1 PowerShell parancsfájl elérési útja. Ehhez az szükséges, válassza a három ponttal (…) gombra a a **parancsfájl elérési útján** a telepítés-AzureResourceGroup.ps1 PowerShell parancsfájl navigáljon a **parancsfájlok** mappa a projekt, válassza ki azt, majd Válassza ki a **OK** gombra.    
   
   ![Válassza ki a parancsfájl elérési útját][9]
7. Miután kiválasztotta a parancsfájlt, frissítse az elérési út a parancsfájlt úgy, hogy akkor fut a Build.StagingDirectory (ugyanabban a könyvtárban, amely *ArtifactsLocation* értékre van állítva). Ehhez adja hozzá a "$(Build.StagingDirectory)/"az elején a parancsfájl elérési útját.
   
    ![Parancsfájl elérési útját szerkesztése][10]
8. Az a **parancsfájl argumentumai** mezőbe írja be a következő paramétereket (egy sorban). Amikor futtatja a parancsfájlt a Visual Studio, ellenőrizheti, hogyan VS használ a paramétereket a a **kimeneti** ablak. Ezzel kiindulási pontként a paramétert értékek beállításakor a build lépésben.
   
   | Paraméter | Leírás |
   | --- | --- |
   | -ResourceGroupLocation |A földrajzihely-értéket, ahol az erőforráscsoport megtalálható, például **eastus** vagy **"USA keleti régiója"**. (Ha adja hozzá szimpla idézőjelben a névben szóköz található.) Lásd: [Azure-régiókat](https://azure.microsoft.com/regions/) további információt. |
   | -ResourceGroupName |Az ehhez a központi telepítéshez használt erőforráscsoport neve. |
   | -UploadArtifacts |Ezt a paramétert, ha létezik, azt jelenti, hogy a helyi rendszerről az Azure-bA feltölteni kívánt igénylő összetevőket. Csak kell erre a kapcsolóra lesz beállítva, ha a sablon telepítéséhez szükséges további összetevők, amelyet szeretne tesztelése (például a parancsfájlokat vagy beágyazott sablonok) a PowerShell-parancsfájl használatával. |
   | -StorageAccountName |A szakasz az összetevők ehhez a központi telepítéshez használt tárfiók neve. Ezt a paramétert csak akkor használja, ha meg vannak átmeneti összetevőket a telepítéshez. Ha meg van adva ez a paraméter, egy új tárfiókot jön létre, ha a parancsfájl nem létrehozva egy korábbi központi telepítés során. Ha a paraméter van megadva, a tárfiók már léteznie kell. |
   | -StorageAccountResourceGroupName |A storage-fiókjához tartozó erőforráscsoport neve. Ez a paraméter megadása kötelező, csak akkor, ha megad egy értéket a StorageAccountName paraméter. |
   | -TemplateFile |A fájl elérési útját a sablon a Azure-erőforráscsoport telepítésben található. Nagyobb rugalmasság érdekében használjon ezt a paramétert, amely a PowerShell parancsfájl abszolút elérési út helyett helyéhez viszonyított elérési utat. |
   | -TemplateParametersFile |A fájl elérési útját a paraméterek a Azure-erőforráscsoport telepítésben található. Nagyobb rugalmasság érdekében használjon ezt a paramétert, amely a PowerShell parancsfájl abszolút elérési út helyett helyéhez viszonyított elérési utat. |
   | -ArtifactStagingDirectory |Ez a paraméter lehetővé teszi, hogy a parancsfájl a mappa tudja, hova lehet másolni a projekt bináris fájlok a PowerShell. Ez az érték felülbírálja az alapértelmezett érték a PowerShell parancsfájlhoz használt. A Visual STUDIO Team Services használatához állítsa be az értéket: - ArtifactStagingDirectory $(Build.StagingDirectory) |
   
   Íme egy parancsfájl argumentumai példa (az olvashatóság hibás sor):
   
   ```    
   -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
   -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
   –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'    
   ```
   
   Amikor végzett, a **parancsfájl argumentumai** mezőben a következő lista kell hasonlítania:
   
   ![Szkript argumentumai][11]
9. Miután az Azure PowerShell összeállítása lépés való felvételét a szükséges elemeket, válassza ki azt a **várólista** Szerkesztés gomb billentyűkombinációt a projekt létrehozásához. A **Build** a képernyőn látható a PowerShell-parancsfájl kimenete.

### <a name="detailed-walkthrough-for-option-2"></a>2. lehetőség részletes bemutató
Az alábbi eljárások végigvezetik a Visual STUDIO Team Services beépített feladatainak folyamatos telepítéséhez szükséges lépéseket.

1. Szerkesztés a Visual STUDIO Team Services build definíció két új build lépések hozzáadása. Válassza ki a build definíciója szerint a **definíciók Build** kategória és válassza a **szerkesztése** hivatkozásra.
   
   ![Build attribútumdefiníciós szerkesztése][12]
2. A build definition használatával adjon hozzá az új build lépéseket a **összeállítása lépés hozzáadása...** gombra.
   
   ![Build lépés hozzáadása][13]
3. Válassza ki a **telepítés** Feladatkategória, jelölje be a **Azure fájl másolása** feladat, és válassza a **Hozzáadás** gombra.
   
   ![Azure-fájl másolása tevékenység hozzáadása][14]
4. Válassza ki a **Azure erőforrás-csoport központi telepítésének** feladat, majd kattintson a **hozzáadása** gombra, majd **Bezárás** a **feladat katalógus**.
   
   ![Adja hozzá Azure-erőforráscsoport telepítési feladat][15]
5. Válassza ki a **Azure fájlmásolással** feladatot, és adja meg az értékeket.
   
   Ha már rendelkezik egy Azure-szolgáltatások végpontot hozzáadni a Visual STUDIO Team Services, válassza ki az előfizetést a **Azure-előfizetés** legördülő listában. Ha nem rendelkezik előfizetéssel, lásd: [1. lehetőség](#detailed-walkthrough-for-option-1) a Visual STUDIO Team Services egyik beállításával kapcsolatos utasításokat.
   
   * A forrás - írja be **$(Build.StagingDirectory)**
   * Az Azure kapcsolattípus - válassza **Azure Resource Manager**
   * Az Azure erőforrás-kezelő előfizetési - válassza ki a használni kívánt tárfiók az előfizetést a **Azure-előfizetés** legördülő listában. Ha az előfizetés nem jelenik meg, válassza ki azt a **frissítése** Tovább gombra a **kezelése** hivatkozásra.
   * Cél típusának - válassza **Azure-Blobba**
   * Erőforrás-kezelő Tárfiók – válassza ki a tárolási fiók, akkor az összetevők előkészítési használni szeretné
   * Tároló neve – adja meg az átmeneti; használni szeretné a tároló neve az bármilyen érvényes tároló nevet, hanem egy másikkal pedig a build definícióját használata
   
   A kimeneti értékeket:
   
   * Adja meg a tároló URI - **artifactsLocation**
   * Tárolási tároló SAS-jogkivonat - meg **artifactsLocationSasToken**
   
   ![Azure-fájl másolása tevékenység konfigurálása][16]
6. Válassza ki a **Azure erőforrás-csoport központi telepítésének** összeállítása lépés, és töltse ki az értékeket.
   
   * Az Azure kapcsolattípus - válassza **Azure Resource Manager**
   * Az Azure erőforrás-kezelő előfizetési - válassza ki az előfizetést a központi telepítést, a **Azure-előfizetés** legördülő listában. Ez általában megegyezik az előző lépésben használt ugyanahhoz az előfizetéshez
   * Művelet – válassza **vagy frissítés erőforráscsoport létrehozása**
   * Erőforráscsoport - válasszon egy erőforráscsoportot, vagy adja meg a központi telepítés egy új erőforráscsoport nevét
   * Hely - adja meg az erőforrásnak a helyét
   * Sablon - adja meg az elérési útja és neve a sablon telepítendő fertőző **$(Build.StagingDirectory)**, például: **$(Build.StagingDirectory/DSC-CI/azuredeploy.json)**
   * Sablonparaméterek - adja meg az elérési útja és neve használandó, a paraméterek fertőző **$(Build.StagingDirectory)**, például: **$(Build.StagingDirectory/DSC-CI/azuredeploy.parameters.json)**
   * Sablon paraméterének - adja meg vagy másolja és illessze be a következő kódot:
     
     ```    
     -_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken (ConvertTo-SecureString -String "$(artifactsLocationSasToken)" -AsPlainText -Force)
     ```
     ![Azure erőforráscsoport-telepítési feladat konfigurálása][17]
7. A szükséges elemeket hozzáadása után a build definition mentés, és válassza **új build várólistára** tetején.

## <a name="next-steps"></a>További lépések
Olvasási [Azure Resource Manager áttekintése](azure-resource-manager/resource-group-overview.md) további információt az Azure Resource Manager és az Azure erőforráscsoport-sablonok.

[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png
[12]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough13.png
[13]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough14.png
[14]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough15.png
[15]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough16.png
[16]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough17.png
[17]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough18.png
