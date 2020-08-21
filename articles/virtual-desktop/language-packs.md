---
title: Nyelvi csomagok telepítése Windows 10 rendszerű virtuális gépekre a Windows Virtual Desktopban – Azure
description: Windows 10 rendszerű, többmunkamenetes virtuális gépek nyelvi csomagjainak telepítése a Windows Virtual Desktopban.
author: Heidilohr
ms.topic: how-to
ms.date: 08/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: de495d18220500e5aa5653e89776c2634d5b1c85
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2020
ms.locfileid: "88719145"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Nyelvi csomagok hozzáadása Windows 10 több munkamenetes rendszerképhez

A Windows virtuális asztal egy olyan szolgáltatás, amelyet a felhasználók bármikor, bárhol üzembe helyezhetnek. Ezért fontos, hogy a felhasználók testre tudják szabni, hogy a Windows 10 Enterprise multi-session rendszerkép milyen nyelven jelenjen meg.

Kétféleképpen lehet alkalmazkodni a felhasználók nyelvi igényeihez:

- Dedikált gazdagép-készleteket hozhat létre az egyes nyelvekhez testreszabott képpel.
- Ugyanazon a gazdagépen eltérő nyelvi és honosítási követelményeknek kell megfelelniük a felhasználóknak, de a képek testreszabásával biztosítható, hogy a kívánt nyelv közül melyikre van szükségük.

Az utóbbi módszer sokkal hatékonyabb és költséghatékony. Azonban Ön dönti el, hogy melyik módszer felel meg legjobban az igényeinek. Ez a cikk bemutatja, hogyan szabhatja testre a képek nyelveit.

## <a name="prerequisites"></a>Előfeltételek

Több nyelv hozzáadásához a következő dolgokra van szükség a Windows 10-es nagyvállalati multi-session lemezképek testreszabásához:

- Azure-beli virtuális gép (VM) a Windows 10 Enterprise multi-session, 1903-es vagy újabb verziójával

- A lemezkép által használt operációsrendszer-verzió 1. lemeze, az ISO és az igény szerinti szolgáltatás (Franciaország). Innen töltheti le őket:
     
     - Nyelv ISO:
        - [Windows 10, 1903-es vagy 1909-es nyelvi csomag ISO-verziója](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10, 2004-es verzió nyelvi csomagja ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - Franciaország tengerentúli lemeze 1 ISO:
        - [Windows 10, 1903-es vagy 1909-es verziójú, 1. lemez ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10, 2004-es verzió, Franciaország 1. lemeze ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)

- Egy Azure Files megosztás vagy fájlmegosztás egy Windows fájlkiszolgáló virtuális gépen

>[!NOTE]
>A fájlmegosztás (adattár) számára elérhetőnek kell lennie az egyéni rendszerkép létrehozásához használni kívánt Azure-beli virtuális gépről.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>A nyelvi csomagok és szolgáltatások igény szerinti tárházának létrehozása

A nyelvi csomagok és a FODs tartalmának tárházának létrehozása:

1. Az Azure-beli virtuális gépeken töltse le a Windows 10 multi-Language ISO-és FODs a Windows 10 Enterprise multi-session, 1903, 1909 és 2004 rendszerképeket az [Előfeltételek](#prerequisites)hivatkozásaiban.

2. Nyissa meg és csatlakoztassa az ISO-fájlokat a virtuális gépen.

3. Nyissa meg a Language Pack ISO-t, és másolja a tartalmat a **LocalExperiencePacks** és az **x64 \\ Langpacks** mappából, majd illessze be a tartalmat a fájlmegosztásba.

4. Nyissa meg az Franciaország-beli **ISO-fájlt**, másolja ki az összes tartalmát, majd illessze be a fájlmegosztást.

     >[!NOTE]
     > Ha korlátozott tárterülettel dolgozik, csak azokat a nyelveket másolja, amelyekről a felhasználók számára szüksége van. A fájlokat egymástól eldöntheti, ha a fájlneveit a nyelvi kódokat keresi. A francia fájlban például a "fr-FR" kód szerepel a névben. Az összes elérhető nyelvhez tartozó nyelvi kódok teljes listájáért lásd: [elérhető nyelvi csomagok a Windowshoz](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

     >[!IMPORTANT]
     > Bizonyos nyelvekhez további betűkészletek szükségesek a különböző elnevezési konvenciókat követő szatellit csomagokban. A japán betűkészletek nevei például a következők: "Jpan".
     >
     > [!div class="mx-imgBorder"]
     > ![A japán nyelvi csomagok példája a "Jpan" nyelvi címkével a fájlnevekben.](media/language-pack-example.png)

5. Állítsa be az engedélyeket a nyelvi tartalom tárházának megosztására, hogy rendelkezzen olvasási hozzáféréssel az egyéni rendszerkép létrehozásához használni kívánt virtuális gépről.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>Egyéni Windows 10 Enterprise több munkamenetből álló rendszerkép manuális létrehozása

Egyéni Windows 10 Enterprise több munkamenetből álló rendszerkép manuális létrehozása:

1. Helyezzen üzembe egy Azure-beli virtuális gépet, majd nyissa meg az Azure-katalógust, és válassza ki a Windows 10 Enterprise-munkamenet aktuális verzióját, amelyet használ.
2. A virtuális gép üzembe helyezése után helyi rendszergazdaként az RDP használatával csatlakozhat hozzá.
3. Győződjön meg arról, hogy a virtuális gép rendelkezik a legújabb Windows-frissítésekkel. Töltse le a frissítéseket, és szükség esetén indítsa újra a virtuális gépet.
4. Kapcsolódjon a nyelvi csomaghoz és a FTM-fájlmegosztás adattárához, és csatlakoztassa egy betűjelű meghajtóhoz (például az E meghajtón).

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>Egyéni Windows 10 Enterprise több munkamenetből álló rendszerkép automatikus létrehozása

Ha inkább egy automatizált folyamaton keresztül szeretné telepíteni a nyelveket, beállíthat egy parancsfájlt a PowerShellben. A következő parancsfájl-minta használatával telepítheti a spanyol (spanyolországi), francia (franciaországi) és kínai (PRC) nyelvi csomagokat és a Satellite csomagokat a Windows 10 Enterprise multi-session, 2004-es verzióra. A parancsfájl integrálja a nyelvi kezelőfelületi csomagot és az összes szükséges műhold-csomagot a rendszerképbe. Ezt a szkriptet azonban módosíthatja más nyelvek telepítéséhez is. Csak futtassa a parancsfájlt egy emelt szintű PowerShell-munkamenetből, vagy más nem fog működni.

```powershell
########################################################
## Add Languages to running Windows Image for Capture##
########################################################

##Disable Language Pack Cleanup##
Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"

##Set Language Pack Content Stores##
[string]$LIPContent = "E:"

##Spanish##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\es-es\LanguageExperiencePack.es-es.Neutral.appx -LicensePath $LIPContent\es-es\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_es-es.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
Set-WinUserLanguageList $LanguageList -force

##French##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\fr-fr\LanguageExperiencePack.fr-fr.Neutral.appx -LicensePath $LIPContent\fr-fr\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_fr-fr.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~fr-fr~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("fr-fr")
Set-WinUserLanguageList $LanguageList -force

##Chinese(PRC)##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\zh-cn\LanguageExperiencePack.zh-cn.Neutral.appx -LicensePath $LIPContent\zh-cn\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_zh-cn.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

>[!IMPORTANT]
>A Windows 10 Enterprise 1903-es és 1909-es verziója nem igényli a `Microsoft-Windows-Client-Language-Pack_x64_<language-code>.cab` csomagfájl használatát.

A parancsfájl eltarthat egy ideig a telepítendő nyelvek számától függően.

Ha a parancsfájl futása befejeződött, ellenőrizze, hogy a nyelvi csomagok megfelelően vannak-e telepítve, **majd a**  >  **Beállítások**  >  **időpontja & a nyelv**  >  **nyelve**. Ha a nyelvi fájlok vannak, akkor minden be van állítva.

Ha elkészült, győződjön meg róla, hogy leválasztja a megosztást.

## <a name="finish-customizing-your-image"></a>A rendszerkép testre szabásának befejezése

A nyelvi csomagok telepítése után telepítheti a testreszabott rendszerképbe felvenni kívánt egyéb szoftvereket is.

Miután végzett a rendszerkép testreszabásával, futtatnia kell a rendszer-előkészítő eszközt (Sysprep).

A Sysprep futtatása:

1. Nyisson meg egy rendszergazda jogú parancssort, és futtassa a következő parancsot a rendszerkép általánosítása érdekében:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Állítsa le a virtuális gépet, majd rögzítse azt egy felügyelt lemezképben az [általánosított virtuális gép felügyelt lemezképének létrehozása az Azure-ban](../virtual-machines/windows/capture-image-resource.md)című témakör utasításait követve.

3. Mostantól a testreszabott lemezkép használatával üzembe helyezhet egy Windows rendszerű virtuális asztali címkészletet. A gazdagépek központi telepítésének megismeréséhez tekintse meg [a következő oktatóanyagot: állomáslista létrehozása a Azure Portal](create-host-pools-azure-marketplace.md).

## <a name="enable-languages-in-windows-settings-app"></a>Nyelvek engedélyezése a Windows beállításai alkalmazásban

Végül hozzá kell adnia a nyelvet az egyes felhasználók nyelvi listájához, hogy a beállítások menüben ki tudják választani a kívánt nyelvet.

Annak biztosítása érdekében, hogy a felhasználók kiválaszthatják a telepített nyelveket, jelentkezzen be felhasználóként, majd futtassa a következő PowerShell-parancsmagot a telepített nyelvi csomagok hozzáadásához a nyelvek menühöz. Ezt a parancsfájlt automatikus feladatként is beállíthatja, amely akkor aktiválódik, amikor a felhasználó bejelentkezik a munkamenetbe.

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Miután a felhasználó módosítja a nyelvi beállításokat, ki kell jelentkeznie a Windows rendszerű virtuális asztali munkamenetből, és újra be kell jelentkeznie a módosítások érvénybe léptetéséhez. 

## <a name="next-steps"></a>További lépések

Ha kíváncsi a nyelvi csomagok ismert problémáira, tekintse meg [a nyelvi csomagok hozzáadása a Windows 10 1803-es és újabb verzióiban: ismert problémák](/windows-hardware/manufacture/desktop/language-packs-known-issue)című témakört.

Ha bármilyen egyéb kérdése van a Windows 10-es nagyvállalati multi-Sessions szolgáltatással kapcsolatban, tekintse meg a [gyakori](windows-10-multisession-faq.md)kérdéseket.
