---
title: Folyamatos integráció az Azure DevOps szolgáltatás használatával Azure erőforráscsoport-projektek |} A Microsoft Docs
description: Ismerteti, hogyan lehet az Azure DevOps-szolgáltatásokkal a folyamatos integráció beállítása az Azure erőforráscsoport-telepítési projekt a Visual Studio használatával.
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
ms.openlocfilehash: a2a730e2c3ca466a2705f053d7db0db12d7941da
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047322"
---
# <a name="continuous-integration-in-azure-devops-services-using-azure-resource-group-deployment-projects"></a>Az Azure DevOps szolgáltatás használatával Azure erőforráscsoport-telepítési projektek folyamatos integráció
Azure-sablon üzembe helyezéséhez, feladatokat különböző szakaszaiban: Buildelési, tesztelési, másolása az Azure-ba (más néven "Átmeneti"), és a sablon üzembe helyezése. Sablonok üzembe helyezése az Azure DevOps-szolgáltatásokkal való két különböző módja van. Mindkét módszer ugyanazt az eredményt adja meg, tehát azt, amelyik leginkább megfelel a munkafolyamatot.

1. Egyetlen lépésben adja hozzá a buildelési folyamat, amely futtatja a PowerShell-parancsfájlt, amely az Azure-erőforráscsoport üzembe helyezési projektben (üzembe helyezés – AzureResourceGroup.ps1) szerepel. A parancsfájl másolja át az összetevőket, és majd üzembe helyezi a sablont.
2. Adja hozzá a több Azure-fejlesztési és üzemeltetési szolgáltatásokat hozhat létre lépéseket, mindegyiknél fázis feladatot végez.

Ez a cikk mindkét lehetőséget bemutatja. Az első lehetőség az előnye, ugyanazzal a parancsfájllal használják a fejlesztők a Visual Studióban és biztosító konzisztencia életciklusa során. A második lehetőség alternatívája a beépített parancsfájlt. Mindkét eljárások feltételezik, hogy már rendelkezik Visual Studio-telepítési projekt be van jelölve, az Azure DevOps-szolgáltatásokkal.

[!INCLUDE [updated-for-az](../includes/updated-for-az.md)]

## <a name="copy-artifacts-to-azure"></a>Összetevők másolása az Azure-bA
A forgatókönyv, függetlenül összes összetevőt, amely szükséges ahhoz, hogy a sablon telepítése, ha meg kell hozzáférést Azure Resource Manager őket. Ezek az összetevők például a fájlok lehetnek:

* Beágyazott sablonok
* Konfiguráció és a DSC-parancsfájlok
* Alkalmazás bináris fájljainak

### <a name="nested-templates-and-configuration-scripts"></a>Beágyazott sablont és parancsfájlokat
A Visual Studio által kínált sablonok használatakor (vagy a Visual Studio-kódrészletek használatával létrehozott), a PowerShell-parancsprogram nem csupán előkészíti az összetevők, azt is felparaméterezi az URI-t, az erőforrások más környezetekben. A parancsfájl, majd másolja át az összetevők biztonságos tárolóba az Azure-ban, hoz létre a tároló SaS-tokent, és ezután továbbítja a be a sablon üzembe helyezéséhez. Lásd: [sablon üzembe helyezése](/previous-versions/azure/reference/dn790564(v=azure.100)) beágyazott sablonokkal kapcsolatos további.  Feladatok használata az Azure DevOps-szolgáltatásokkal, válassza ki a megfelelő feladatokat. a sablon központi telepítéséhez, és szükség esetén át paraméter értékét az előkészítési lépés a sablon üzembe helyezéséhez.

## <a name="set-up-continuous-deployment-in-azure-pipelines"></a>Folyamatos üzembe helyezés az Azure-folyamatok beállítása
A PowerShell-szkriptet hívja meg az Azure-folyamatok, frissíteni szeretné a buildelési folyamat. Röviden a lépések a következők: 

1. Szerkessze a buildelési folyamat.
2. Állítsa be az Azure-folyamatok Azure engedélyezés.
3. Adjon hozzá egy Azure PowerShell-lel buildelési lépést, amely hivatkozik az Azure-erőforráscsoport üzembe helyezési projektben található PowerShell-szkriptet.
4. Az értékét állítsa be a *- ArtifactsStagingDirectory* paraméter egy projektet egy Azure-folyamatokban dolgozhat.

### <a name="detailed-walkthrough-for-option-1"></a>Részletes bemutató az 1. lehetőség
Az alábbi eljárások végigvezetik a folyamatos üzembe helyezés konfigurálása az Azure DevOps-szolgáltatásokkal, amelyek a PowerShell-parancsfájl futtatása a projektben egy feladat használatával szükséges lépéseket. 

1. Az Azure DevOps-szolgáltatásokkal buildelési folyamat szerkesztése és a egy Azure PowerShell-lel felépítési lépés hozzáadása. Válassza ki a buildelési folyamat alatt a **folyamatok alakíthatók ki** kategória majd válassza a **szerkesztése** hivatkozásra.
   
   ![Felépítési folyamat szerkesztése][0]
2. Vegyen fel egy új **Azure PowerShell-lel** hozhat létre a buildelési folyamat a lépést, és válassza ki a **felépítési lépés hozzáadása...** gombra.
   
   ![Felépítési lépés hozzáadása][1]
3. Válassza ki a **telepítés feladat** kategória, válassza a **Azure PowerShell** feladat, és válassza a **Hozzáadás** gombra.
   
   ![Tevékenységek hozzáadása][2]
4. Válassza ki a **Azure PowerShell-lel** létrehozási lépést, és töltse ki az értékeket.
   
   1. Ha már rendelkezik egy Azure-szolgáltatás végpontot hozzá az Azure DevOps-szolgáltatásokkal, válassza ki az előfizetést, a **Azure-előfizetés** legördülő lista és majd kihagyása a következő szakaszra. 
      
      Ha nem rendelkezik Azure-szolgáltatás a végpont az Azure DevOps-szolgáltatásokkal, hozzá kell egyet. Ez a rész végigvezeti Önt a folyamaton. Ha az Azure-fiókját egy Microsoft-fiókkal (például a Hotmail) használ, a következő lépéseket a a szolgáltatásnév hitelesítési szükségesek.

   2. Válassza ki a **kezelés** mellett kapcsolni a **Azure-előfizetés** legördülő listában.
      
      ![Azure-előfizetések kezelése][3]
   3. Válasszon **Azure** a a **új szolgáltatásvégpont** legördülő listában.
      
      ![Új szolgáltatási végpont][4]
   4. Az a **Azure-előfizetés hozzáadása** párbeszédpanelen válassza ki a **szolgáltatásnév** lehetőséget.
      
      ![Egyszerű beállítás][5]
   5. Adja meg az Azure-előfizetés adatait a **Azure-előfizetés hozzáadása** párbeszédpanel bezárásához. Meg kell adnia a következő elemek:
      
      * Előfizetési azonosító
      * Előfizetés neve
      * Szolgáltatásnév azonosítója
      * Egyszerű szolgáltatásnév kulcsa
      * Bérlő azonosítója
   6. Adjon hozzá egy szabadon választott nevet a **előfizetés** neve mező. Ez az érték jelenik meg, később a a **Azure-előfizetés** legördülő listából válassza ki az Azure DevOps-szolgáltatásokkal. 

   7. Ha nem ismeri az Azure-előfizetés azonosítója, használhatja a következő parancsok egyikét is lekérheti azt.
      
      PowerShell-parancsfájlok használata:
      
      `Get-AzSubscription`
      
      Azure CLI esetén használja az alábbi parancsot:
      
      `azure account show`
   8. Úgy szerezheti be egy egyszerű szolgáltatás azonosítója, szolgáltatásnév kulcsa és bérlő azonosítója, kövesse az eljárást a [hozzon létre egy Active Directory-alkalmazás és -portál használatával szolgáltatásnév](active-directory/develop/howto-create-service-principal-portal.md) vagy [hitelesítése egy egyszerű szolgáltatást az Azure-ral Erőforrás-kezelő](active-directory/develop/howto-authenticate-service-principal-powershell.md).
   9. Adja meg a szolgáltatásnév Azonosítóját, szolgáltatásnév kulcsa és bérlői azonosító értéket, a **Azure-előfizetés hozzáadása** párbeszédpanel mezőbe, és válassza ki a **OK** gombra.
      
      Most már rendelkezik egy érvényes szolgáltatásnevet, az Azure PowerShell-szkript futtatásához.
5. Szerkessze a buildelési folyamat, és válassza ki a **Azure PowerShell-lel** létrehozási lépést. Válassza ki az előfizetést a **Azure-előfizetés** legördülő listában. (Ha az előfizetés nem jelenik meg, válassza ki a **frissítése** gomb mellett a **kezelés** hivatkozást.) 
   
   ![Azure PowerShell-lel összeállítási feladat konfigurálása][8]
6. Adja meg az üzembe helyezés – AzureResourceGroup.ps1 PowerShell-parancsprogram elérési útját. Ehhez válassza a három pontra (...) gomb melletti a **parancsprogram elérési útja** mezőben keresse meg az üzembe helyezés – AzureResourceGroup.ps1 PowerShell-parancsprogram a **parancsfájlok** mappát a projekt, válassza ki, majd Válassza ki a **OK** gombra.    
   
   ![Válassza ki a parancsfájl elérési útja][9]
7. Után, válassza ki a parancsprogramot, frissítse az elérési út a parancsfájlt úgy, hogy fut, a Build.StagingDirectory (ugyanabban a könyvtárban, amely *ArtifactsLocation* értékre van állítva). Ehhez adja hozzá a "$(Build.StagingDirectory)/", a parancsfájl elérési útján kezdete.
   
    ![Parancsfájl elérési útját szerkesztése][10]
8. Az a **Argumenty Skriptu** mezőbe írja be a következő paramétereket (a egyetlen sor). A szkript futtatásakor a Visual Studióban láthatja, hogyan VS használja-e a paramétereket a **kimeneti** ablak. Ezzel kiindulási pontként a build lépésben állítja a paraméter értékét.
   
   | Paraméter | Leírás |
   | --- | --- |
   | -ResourceGroupLocation |A földrajzihely-értéket, ahol az erőforráscsoport megtalálható, például **eastus** vagy **: USA keleti RÉGIÓJA"**. (Adja hozzá szimpla idézőjelek között, ha nincs a hely nevét.) Lásd: [Azure-régiók](https://azure.microsoft.com/regions/) további információt. |
   | -ResourceGroupName |A központi telepítéshez használt erőforráscsoport neve. |
   | -UploadArtifacts |Ezt a paramétert, ha jelen van, azt jelenti, hogy összetevők, amelyek fel kell tölteni az Azure-bA a helyi rendszerről. Csak ki kell állítsa ezt a kapcsolót, ha a sablon telepítéséhez van szükség a további összetevők, amelyet szeretne a PowerShell-parancsfájllal (például konfigurációs szkripteket vagy beágyazott sablonok) szakaszban. |
   | -StorageAccountName |A fázis összetevők ehhez a központi telepítéshez használt tárfiók neve. Ez a paraméter csak akkor használható, ha az üzembe helyezési összetevők, átmeneti. Ha ez a paraméter meg van adva, egy új storage-fiók jön létre, ha a parancsfájl nem létrehozva egy korábbi központi telepítés során. Ha a paraméter van megadva, a tárfiók már léteznie kell. |
   | -StorageAccountResourceGroupName |A storage-fiókhoz társított az erőforráscsoport neve. Ez a paraméter kötelező, csak akkor, ha megad egy értéket a StorageAccountName paraméterhez. |
   | -TemplateFile |Az Azure-erőforráscsoport üzembe helyezési projektben a sablonfájl elérési útja. Nagyobb rugalmasság érdekében ez a paraméter, amely a PowerShell-parancsfájl abszolút elérési út helyett helyéhez viszonyított elérési utat használjon. |
   | -TemplateParametersFile |Az Azure-erőforráscsoport üzembe helyezési projektben a paraméterfájl elérési útja. Nagyobb rugalmasság érdekében ez a paraméter, amely a PowerShell-parancsfájl abszolút elérési út helyett helyéhez viszonyított elérési utat használjon. |
   | -ArtifactStagingDirectory |Ez a paraméter lehetővé teszi, hogy a PowerShell-parancsfájl a mappa tudja, hova lehet másolni a projekt bináris fájlokat. Ez az érték felülbírálja az alapértelmezett érték, amelyet a PowerShell-parancsfájlt. Az Azure fejlesztési és üzemeltetési szolgáltatásokat használja, állítsa az értékét: - ArtifactStagingDirectory $(Build.StagingDirectory) |
   
   Íme egy parancsfájl argumentumai példa (az olvashatóság érdekében a megszakadt. sor):
   
   ```    
   -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
   -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
   –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'    
   ```
   
   Ha elkészült, a **Argumenty Skriptu** be az alábbi lista kell hasonlítania:
   
   ![Szkript argumentumai][11]
9. Miután hozzáadott a szükséges elemeket az Azure PowerShell-lel lépést, válassza ki a **várólista** létrehozása gombra kattintva a projekt buildjének elkészítéséhez. A **összeállítása** képernyőn látható a PowerShell-parancsfájl kimenetében.

### <a name="detailed-walkthrough-for-option-2"></a>Részletes útmutató 2. lehetőséget
Az alábbi eljárások végigvezetik a folyamatos üzembe helyezés konfigurálása az Azure fejlesztési és üzemeltetési szolgáltatásokat a beépített tevékenységeket kínál a szükséges lépéseket.

1. Szerkessze az Azure DevOps-szolgáltatásokkal buildelési folyamat két új build lépések hozzáadása. Válassza ki a buildelési folyamat alatt a **Builddefinícióiról** kategória majd válassza a **szerkesztése** hivatkozásra.
   
   ![Builddefiníció szerkesztése][12]
2. Az új létrehozási lépések hozzáadása a buildelési folyamat használatával a **felépítési lépés hozzáadása...** gombra.
   
   ![Felépítési lépés hozzáadása][13]
3. Válassza ki a **telepítés** Feladatkategória, válassza a **Azure File Copy** feladat, és válassza a **Hozzáadás** gombra.
   
   ![Az Azure-fájl másolása tevékenység hozzáadása][14]
4. Válassza ki a **Azure erőforráscsoport-telepítés** feladat, majd válassza a **Hozzáadás** gombra, majd **Bezárás** a **feladat katalógus**.
   
   ![Azure erőforráscsoport-telepítés feladat hozzáadása][15]
5. Válassza ki a **Azure fájlmásolás** feladatot, és adja meg az értékeket.
   
   Ha már rendelkezik egy Azure-szolgáltatás végpontot hozzá az Azure DevOps-szolgáltatásokkal, válassza ki az előfizetést, a **Azure-előfizetés** legördülő listában. Ha nem rendelkezik előfizetéssel, lásd: [1. lehetőség](#detailed-walkthrough-for-option-1) beállítása egy, az Azure DevOps-szolgáltatásokkal kapcsolatban.
   
   * Forrás – adja meg **$(Build.StagingDirectory)**
   * Az Azure kapcsolattípus - válassza **Azure Resource Manager**
   * Azure RM-előfizetés – a tárfiók a használni kívánt előfizetés kiválasztásához a **Azure-előfizetés** legördülő listában. Ha az előfizetés nem jelenik meg, válassza ki a **frissítése** gomb mellett a **kezelés** hivatkozásra.
   * Cél típusa – válassza ki **Azure Blob**
   * Erőforrás-kezelő Tárfiók – válassza ki a storage-fiók szeretnék használni az átmeneti tárolási összetevők
   * Tárolónév - adja meg az átmeneti; használni szeretné a tároló nevét. az bármilyen érvényes tároló nevet, hanem egy másikra a buildelési folyamat használata
   
   A kimeneti értékeket:
   
   * Adja meg a tároló URI - **artifactsLocation**
   * Tárolási tároló SAS-jogkivonat - adja meg **artifactsLocationSasToken**
   
   ![Az Azure-fájl másolása tevékenység konfigurálása][16]
6. Válassza ki a **Azure erőforráscsoport-telepítés** létrehozási lépést, és töltse ki az értékeket.
   
   * Az Azure kapcsolattípus - válassza **Azure Resource Manager**
   * Azure RM-előfizetés – válassza ki az előfizetést a központi telepítést, a **Azure-előfizetés** legördülő listában. Ez általában megegyezik az előző lépésben használt ugyanahhoz az előfizetéshez
   * Művelet – select **létrehozás vagy frissítés erőforráscsoport**
   * Erőforráscsoport – válasszon ki egy erőforráscsoportot, vagy adja meg a központi telepítés egy új erőforráscsoport nevét
   * Hely – válassza ki az erőforráscsoport helyét
   * Sablon - adja meg az elérési útja és neve a sablon üzembe helyezni fertőző **$(Build.StagingDirectory)**, például: **$(Build.StagingDirectory/DSC-CI/azuredeploy.json)**
   * Sablon paraméterei – adja meg az elérési útját és nevét a paramétereket használja, előtag-Beillesztés **$(Build.StagingDirectory)**, például: **$(Build.StagingDirectory/DSC-CI/azuredeploy.parameters.json)**
   * Sablonparaméterek felülbírálása – adja meg vagy másolja és illessze be a következő kódot:
     
     ```    
     -_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken (ConvertTo-SecureString -String "$(artifactsLocationSasToken)" -AsPlainText -Force)
     ```
     ![Azure erőforráscsoport üzembe helyezési feladat konfigurálása][17]
7. Miután hozzáadta a szükséges elemeket, a buildelési folyamat mentése, és válassza a **új build várólistára** tetején.

## <a name="next-steps"></a>További lépések
Olvasási [Azure Resource Manager áttekintése](azure-resource-manager/resource-group-overview.md) tudhat meg többet az Azure Resource Manager és az Azure-erőforráscsoportot.

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
