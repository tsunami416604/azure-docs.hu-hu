---
title: Töltse le a Piactéri elemek az Azure-ból |} Microsoft Docs
description: A felhő üzemeltetője letöltheti Piactéri elemek az Azure-ból az Azure Alkalmazásveremben üzembe.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 5d403f7c1d0fff466f6c0fb9942ec777ab820eab
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604532"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Töltse le a Piactéri elemek az Azure-ból az Azure-verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Felhő operátorként töltse le a cikkek az Azure piactérről, és elérhetővé tétele Azure-készletben. Az elemek, dönthet úgy, már előre tesztelése, és Azure verem használható támogatott Azure piactéren elemek válogatott listájából. További gyakori elemekre erre a listára, így továbbra is keressen új tartalom. 

Az Azure piactéren történő kapcsolódáshoz két esetben van: 

- **Egy csatlakoztatott forgatókönyv** -, amelyhez Azure verem környezetében kell csatlakoztatni az internethez. A verem Azure portál használatával keresse meg és töltse le az elemeket. 
- **A leválasztott vagy részben csatlakoztatott forgatókönyv** -igénylő érni az internetet, töltse le a Piactéri elemek a piactér szindikálási eszközzel. Ezt követően a letöltött fájlok átvitele a leválasztott Azure verem telepítését. Ebben a forgatókönyvben PowerShell használja.

Lásd: [Azure piactéren elemek Azure verem](azure-stack-marketplace-azure-items.md) a letöltheti a Piactéri elemek listáját.


## <a name="connected-scenario"></a>Csatlakoztatott forgatókönyv
Ha Azure verem csatlakozik az internethez, a felügyeleti portál segítségével töltse le a Piactéri elemek.

### <a name="prerequisites"></a>Előfeltételek
Az Azure Alkalmazásveremben üzembe kell kapcsolódik az internethez, és kell [regisztrálva van az Azure](azure-stack-register.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>A portál használatával töltse le a Piactéri elemek  
1. Jelentkezzen be a verem Azure felügyeleti portálra.

2.  Tekintse át a rendelkezésre álló szabad hely Piactéri elemek letöltése előtt. Később amikor kiválasztja az elemeket letölthető, összehasonlíthatja a letöltési mérete a rendelkezésre álló tárolókapacitás. Ha kapacitás korlátozott, vegye figyelembe a lehetőségeket [rendelkezésre álló terület kezelése](azure-stack-manage-storage-shares.md#manage-available-space). 

    Szabad terület, áttekintheti a **régió felügyeleti** vizsgálatát, és folytassa a kívánt régiót **erőforrás-szolgáltató** > **tárolási**.

    ![Tekintse át a tárolóhely](media/azure-stack-download-azure-marketplace-item/storage.png)  

    
3. Nyissa meg a verem Azure piactérről, és csatlakozzon az Azure-bA. Ehhez az szükséges, válassza ki a **piactér felügyeleti**, majd válassza ki **hozzáadása az Azure-ból**.

    ![Adja hozzá az Azure-ból](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    A portál letölthető az Azure piactéren elemek listáját jeleníti meg. Minden egyes megtekintése a leírása és a további információt, beleértve a letöltési mérete az elemre kattinthat. 

    ![Piactér-lista](media/azure-stack-download-azure-marketplace-item/image03.png)

4. Válassza ki a elemet, és válassza **letöltése**. Letöltési ideje eltérők lehetnek.

    ![Töltse le az üzenet](media/azure-stack-download-azure-marketplace-item/image04.png)

    A letöltés befejezése után telepítheti az új piactér elemhez egy Azure verem operátor vagy a felhasználó.

5. A letöltött elem telepítése – válasszon ki **+ új**, majd keresse meg az új piactér elem kategóriák között. Ezután válassza ki a elemet, a telepítés megkezdéséhez. A folyamat változó különböző Piactéri elemek. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>Leválasztott vagy részben csatlakoztatott forgatókönyv

Ha Azure verem kapcsolat nélküli módban, és internetkapcsolat nélkül, használhatja a PowerShell és a *piactér szindikálási eszköz* letölthetők a Piactéri elemek géphez internetkapcsolat. Majd vigye át az elemeket a verem Azure környezetben. A leválasztott környezetben Piactéri elemek nem tudja letölteni a verem Azure portál használatával. 

A piactér szindikálási eszköz csatlakoztatott esetén is használható. 

Ez a forgatókönyv két részből áll:
- **1. rész:** töltse le az Azure piactérről. Az internet-hozzáféréssel rendelkező számítógépen PowerShell konfigurálása, töltse le a szindikálási eszközt, és töltse le az Azure piactéren elemek űrlap.  
- **2. lépés:** töltse fel, és tegye közzé az Azure piactérről verem. Helyezze át a fájlokat az Azure-verem környezet letöltött a Azure verem importálja, és közzéteheti a verem Azure piactéren.  


### <a name="prerequisites"></a>Előfeltételek
- Az Azure Alkalmazásveremben üzembe kell [regisztrálva van az Azure](azure-stack-register.md).  

- Rendelkeznie kell internetkapcsolattal rendelkező számítógépen **Azure verem PowerShell modul verzió 1.2.11** vagy újabb verzióját. Ha még nincs letöltve, [Azure verem adott PowerShell-modulok telepítése](azure-stack-powershell-install.md).  

- Ahhoz, hogy a letöltött piactér elem, importálás a [PowerShell-környezet az Azure-verem operátorhoz](azure-stack-powershell-configure-admin.md) úgy kell konfigurálni.  

- Rendelkeznie kell egy [tárfiók](azure-stack-manage-storage-accounts.md) egy nyilvánosan elérhető tároló (amely a tárolási blob) rendelkező Azure-készletben. A Piactéri elemek gyűjteménye fájlok ideiglenes tárolóként használja a tárolót. Ha nem ismeri a storage-fiókok és a tárolók, lásd: [működik a BLOB - Azure-portálon](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) az Azure-dokumentációban.

- A piactér szindikálási eszköz az első eljárás során le. 

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>A piactér szindikálási eszköz használatával töltse le a Piactéri elemek

1. Internetkapcsolattal rendelkező számítógépen nyissa meg rendszergazdaként a PowerShell-konzolban.

2. Adja hozzá az Azure-fiókot regisztrálni Azure verem használt. A fiók hozzáadása a PowerShell futási `Add-AzureRmAccount` paraméter nélkül. Az Azure-fiók hitelesítő adatainak megadását kéri, és lehetséges, hogy a fiók konfigurációja alapján 2-factor authentication használatával.

3. Ha több előfizetéssel rendelkezik, a következő paranccsal válassza ki, amelyiket a regisztrációhoz használja:  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Töltse le a piactér szindikálási eszköz legújabb verzióját a következő parancsfájl használatával:  

   ```PowerShell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. A szindikálási modul importálása, és ezután indítsa el az eszköz a következő parancsfájl futtatásával. Cserélje le a *a célmappa útvonala* az Azure piactérről letöltött fájlok tárolási helyét.   

   ```PowerShell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "Destination folder path" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. Amikor az eszköz fut, a Azure-fiók hitelesítő adatainak megadását kéri. Jelentkezzen be az Azure-fiókot regisztrálni Azure verem használt. Után a bejelentkezés sikeres, megjelenik egy képernyőt, például az alábbi képen a rendelkezésre álló Piactéri elemek listáját.  

   ![Az Azure piactéren elemek előugró ablak](media/azure-stack-download-azure-marketplace-item/image05.png)

7. Válassza ki a letölteni, és jegyezze fel a kívánt elemet a *verzió*. (Több lemezképet kijelöléséhez tartsa a *Ctrl* kulcs.) Amelyre hivatkozhat a *verzió* a következő eljárással-elem importálásakor. 
   
   Képek listájának használatával is végezhet a **adja meg a feltételeket** lehetőséget.

8. Válassza ki **OK**, és tekintse át és fogadja el a jogi feltételeket. 

9. Az, hogy a letöltési mennyi idő függ az elem méretét. A letöltés befejezése után az elem nem áll rendelkezésre a a parancsfájlban megadott mappában. A letöltés tartalmaz egy VHD-fájlt (a virtuális gépek) vagy egy. A ZIP-fájlt (virtuálisgép-bővítmények). Egy gyűjteménycsomag a is a *.azpkg* formátumban. (A *.azpkg* a csomag egy *.zip* fájl.)
 

### <a name="import-the-download-and-publish-to-azure-stack-marketplace"></a>Importálja a letöltés, és tegye közzé az Azure verem piactér
1. A virtuálisgép-lemezképeket vagy megoldás sablonok, hogy rendelkezik a fájlok [korábban letöltött](#use-the-marketplace-syndication-tool-to-download-marketplace-items) az Azure-verem környezethez helyileg elérhetővé kell tenni.  

2. Importálja. Azure verem VHD-fájlokat. A virtuális gép (VM)-lemezkép importálása sikeresen megtörtént, a virtuális Gépet a következő információkat kell rendelkeznie:
   - A *verzió*, amint az előző eljárás 7. lépés.
   - A virtuális gépek értékek *publisher*, *kínálnak*, és *sku*. Ahhoz, hogy ezeket az értékeket, nevezze át egy példányát a **.azpkg** fájlját, hogy a fájl kiterjesztése a **.zip**. Majd segítségével egy szövegszerkesztőben nyissa meg a **DeploymentTemplates\CreateUiDefinition.json**. A .JSON kiterjesztésű fájlt, keresse meg a *imageReference* szakaszt, amely tartalmazza ezeket az értékeket a Piactéri elemet. A következő példa bemutatja, hogyan Ez az információ jelenik meg:

     ```json  
     "imageReference": {  
        "publisher": "MicrosoftWindowsServer",  
        "offer": "WindowsServer",  
        "sku": "2016-Datacenter-Server-Core"  
      }
     ```  

   A kép importálása Azure verem segítségével a **Add-AzsPlatformimage** parancsmag. A parancsmag használatakor győződjön meg arról, hogy a *publisher*, *kínálnak*, és más paraméterértékeket a kép importált értékekkel. Beszerezheti a *publisher*, *kínálnak*, és *sku* a képet a szövegfájlt, amely a AZPKG fájl együtt letölti és a célként megadott helyen található értékek . 

   A következő példa parancsfájlt a Windows Server 2016 Datacenter - Server Core virtuális gép értékeket használ. 

   ```PowerShell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
   ```
   **Megoldás sablonokkal kapcsolatos:** egyes sablonok lehetnek egy kis 3 MB. VHD-fájl nevű **fixed3.vhd**. Nem kell, hogy a fájl importálása a Azure verem. Fixed3.vhd.  Ez a fájl megtalálható egyes megoldás sablonok az Azure piactéren a közzététel követelményeinek.

   Tekintse át a sablonok leírása, és töltse le, és importálja a VHD-k, amelyek szükségesek a megoldás sablonnal dolgozni például további követelmények.

3. A felügyeleti portál segítségével a Piactéri elemet alkalmazáscsomag (a .azpkg fájl) feltöltésére Azure verem Blob Storage tárolóban. A csomag feltöltése lehetővé teszi az Azure-vermet, később is közzéteheti a cikk verem Azure piactérről.

   Feltöltés szükséges hozzá egy tárfiókot, nyilvánosan elérhető tárolóval (lásd: a forgatókönyv előfeltételei)   
   1. A verem Azure felügyeleti portálon, a váltson **további szolgáltatások** > **tárfiókok**.  
   
   2. Válasszon egy tárfiókot, az előfizetésből, majd a **BLOB szolgáltatás**, jelölje be **tárolók**.  
      ![BLOB szolgáltatás](media/azure-stack-download-azure-marketplace-item/blob-service.png)  
   
   3. Válassza ki a tárolót használja, és válassza ki a kívánt **feltöltése** megnyitásához a **feltöltése a blob** ablaktáblán.  
      ![Tároló](media/azure-stack-download-azure-marketplace-item/container.png)  
   
   4. A feltöltés blob panelen keresse meg a tároló betölteni, és válassza ki a fájlokat **feltöltése**.  
      ![upload](media/azure-stack-download-azure-marketplace-item/upload.png)  

   5. Feltöltött fájlok a tároló ablaktáblán jelennek meg. Válasszon ki egy fájlt, és másolja az URL-CÍMÉT a **Blob-tulajdonságok** ablaktáblán. A következő lépésben URL-címet fogja használni a Piactéri elemet Azure verem való importálása során.  Az alábbi ábrán a tárolóban van *teszt-blobtárolóba* és a fájl *Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  A fájl URL-cím *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  
      ![A BLOB tulajdonságai](media/azure-stack-download-azure-marketplace-item/blob-storage.png)  

4.  Tegye közzé a Piactéri elemet Azure verem használatával PowerShell modullal a **Add-AzsGalleryItem** parancsmag. Példa:  
    ```PowerShell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. Egy gyűjteményelem közzététele után megtekintheti az **további szolgáltatások** > **piactér**.  Ha a letöltés a megoldássablon, ellenőrizze, függő VHD képet solution sablon.  
  ![Nézet piactér](media/azure-stack-download-azure-marketplace-item/view-marketplace.png)  

> [!NOTE]
> Azure verem PowerShell 1.3.0 kiadása most adhat hozzá a virtuálisgép-bővítmények.

Példa:

````PowerShell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
````

## <a name="next-steps"></a>További lépések
[Hozzon létre, és tegye közzé a Piactéri elemet](azure-stack-create-and-publish-marketplace-item.md)