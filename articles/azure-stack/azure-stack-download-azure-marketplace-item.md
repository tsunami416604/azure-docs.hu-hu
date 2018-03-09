---
title: "Töltse le a Piactéri elemek az Azure-ból |} Microsoft Docs"
description: "A saját Azure Alkalmazásveremben üzembe az Azure-ból letölthető Piactéri elemek."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/27/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: cdadf48aa23e3dd76d8a511794f00725f073611d
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Töltse le a Piactéri elemek az Azure-ból az Azure-verem

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*


Hogy mely tartalmak ahhoz, hogy szerepeljen a verem Azure piactér mellett dönt, érdemes a tartalom érhető el az Azure piactérről. A tesztelt futtatásához Azure veremben volt az Azure piactéren elemek válogatott listáját is letölthető. Új elem gyakran felkerül a listára, ezért ügyeljen arra, hogy keressen új tartalom.

## <a name="download-marketplace-items-in-a-connected-scenario-with-internet-connectivity"></a>Töltse le a Piactéri elemek csatlakoztatott forgatókönyv esetében (internetes kapcsolattal rendelkező)

1. Töltse le a Piactéri elemek, először [Azure verem regisztrálni Azure](azure-stack-register.md).
2. Jelentkezzen be a verem Azure felügyeleti portálra (https://portal.local.azurestack.external).
3. Néhány Piactéri elemek tekintélyes lehet. Győződjön meg arról, hogy elegendő lemezterület a számítógépen kattintva **erőforrás-szolgáltató** > **tárolási**.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

4. Kattintson a **további szolgáltatások** > **piactér felügyeleti**.

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. Kattintson a **hozzáadása az Azure-ból** a letölthető elemek listájának megjelenítéséhez. Kattintson a leírásának megtekintéséhez és letöltési méret a lista minden eleme.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. Válassza ki a elemet a listában, és kattintson a **letöltése**. A Virtuálisgép-lemezkép letöltése elkezdte kijelölt elemhez. Letöltési ideje eltérők lehetnek.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. A letöltés befejezése után telepítheti az új Piactéri elemet egy Azure verem operátor vagy a felhasználó. Kattintson a **+ új**, keresni az új piactér elem kategóriák közül, majd válassza ki az elemet.
7. Kattintson a **létrehozása** az létrehozása élményét, az újonnan letöltött elem megnyitása. Hajtsa végre a központi telepítése a cikk részletesen.

## <a name="download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Töltse le a Piactéri elemek egy leválasztott vagy részben csatlakoztatott forgatókönyv (az korlátozott internetkapcsolat)

Ha Azure verem (nélkül bármely internetkapcsolat) kapcsolat nélküli módban telepíti, a verem Azure portál használatával nem tudja letölteni a Piactéri elemek. Azonban a piactér szindikálási eszköz használatával töltse le a Piactéri elemek egy internetkapcsolattal rendelkező gép, és majd átviszi a verem Azure környezetben.

### <a name="prerequisites"></a>Előfeltételek
A piactér szindikálási eszköz használata előtt győződjön meg arról, hogy rendelkezik [Azure verem regisztrálva az Azure-előfizetéssel való](azure-stack-register.md).  

A gépen, amely internetkapcsolattal rendelkezik az alábbi lépések segítségével töltse le a szükséges Piactéri elemek:

1. Nyissa meg rendszergazdaként a PowerShell-konzolban és [Azure verem adott PowerShell-modulok telepítése](azure-stack-powershell-install.md). Győződjön meg arról, hogy telepítse **PowerShell 1.2.11 verzió vagy újabb**.  

2. Adja hozzá az Azure-fiókot regisztrálni Azure verem használt. A fiók hozzáadásához futtassa a **Add-AzureRmAccount** parancsmag paraméter nélkül. Az Azure-fiók hitelesítő adatainak megadását kéri, és előfordulhat, hogy a fiók konfigurációja alapján 2-factor authentication használatával.  

3. Ha több előfizetéssel rendelkezik, a következő paranccsal válassza ki, amelyiket a regisztrációhoz használja:  

   ```powershell
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
   cd \AzureStack-Tools-master

   ```

5. A szindikálási modul importálása, és indítsa el az eszközt a következő parancsok futtatásával:  

   ```powershell
   Import-Module .\ Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "<Destination folder path>" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. Amikor az eszköz fut, a Azure-fiók hitelesítő adatainak megadását kéri. Jelentkezzen be az Azure-fiókot regisztrálni Azure verem használt. Miután a bejelentkezés sikeres, a rendelkezésre álló Piactéri elemek listáját a következő képernyő kell megjelennie.  

   ![Az Azure piactéren elemek előugró ablak](./media/azure-stack-download-azure-marketplace-item/image05.png)

7. Válassza ki a lemezképet, amelyet szeretne letölteni, és jegyezze fel, a lemezkép verziója. A Ctrl billentyűt lenyomva több lemezképet választhat. A kép verzióját használja a következő szakaszban-lemezkép importálása.  Ezután kattintson **Ok**, majd fogadja el a jogi feltételeket kattintva **Igen**. Képek listájának használatával is végezhet a **adja meg a feltételeket** lehetőséget. 

   A letöltés eltart egy ideig, attól függően, hogy a lemezkép méretét. Egyszer kép letöltése érhető el a korábban megadott cél elérési útban. A letöltés Azpkg formátuma VHD fájlt, és a gyűjtemény elemeinek tartalmazza.

### <a name="import-the-image-and-publish-it-to-azure-stack-marketplace"></a>A lemezkép importálása, és tegye közzé a verem Azure piactér

1. A kép és a gyűjtemény csomag letöltése után mentse őket, és a tartalom cserélhető lemezmeghajtókra AzureStack főkiszolgálós eszközök mappában, és másolja az Azure-verem környezetben (másolhatja azt helyileg bárhova például: "C:\MarketplaceImages").     

2. A kép importálása, előtt csatlakoztatni kell az Azure-verem üzemeltető környezetben ismertetett lépések segítségével [Azure verem operátor PowerShell környezet konfigurálása](azure-stack-powershell-configure-admin.md).  

3. A lemezkép importálása Azure verem az Add-AzsVMImage parancsmag segítségével. A parancsmag használatakor győződjön meg arról, hogy a *publisher*, *kínálnak*, és más paraméterértékeket a kép importált értékekkel. Beszerezheti a *publisher*, *kínálnak*, és *sku* értékek a képet az imageReference objektum korábban letöltött Azpkg fájl és a  *verzió* az előző szakaszban 6. lépés értékét.

   ```json
   "imageReference": {
      "publisher": "MicrosoftWindowsServer",
      "offer": "WindowsServer",
      "sku": "2016-Datacenter-Server-Core"
    }
   ```

   Cserélje le a paraméterértékeket, és futtassa a következő parancsot:

   ```powershell
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   Add-AzsVMImage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
    -CreateGalleryItem $False `
    -Location Local
   ```

4. Töltse fel a Piactéri elemet használja portal (. Azpkg) Azure-verem blobtárolóba. Töltse fel a helyi Azure verem tárterület, vagy töltse fel az Azure-tárhelyre. (A csomag egy ideiglenes helyre szó.) Győződjön meg arról, hogy a blob nyilvánosan elérhető, és jegyezze fel az URI azonosító.  

5. A Piactéri elemet közzétételét Azure verem használatával a **Add-AzsGalleryItem**. Példa:

   ```powershell
   Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.2.azpkg" `
     –Verbose
   ```

6. A gyűjteményelem közzététele után tekintheti át a **új** > **piactér** ablaktáblán.  

   ![Piactér](./media/azure-stack-download-azure-marketplace-item/image06.png)

## <a name="next-steps"></a>További lépések

[Hozzon létre, és tegye közzé a Piactéri elemet](azure-stack-create-and-publish-marketplace-item.md)
