---
title: A piactér eszközkészlet használatával létrehozása és közzététele a marketplace-elemek |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre gyorsan marketplace-elemek a közzétételi eszközkészlet
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 9f1c433be7e8adc0779860c3ec3bfb5d7174fca5
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52970722"
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>Közzétételi eszköz használatával marketplace-elemek hozzáadása

A tartalom hozzáadása a [Azure Stack piactéren](azure-stack-marketplace.md) elérhetővé teszi a megoldások az Ön és a központi telepítés a bérlők számára. A Marketplace-eszközkészlet az Azure Marketplace-en (.azpkg) csomagfájlok az IaaS Azure Resource Manager-sablonok vagy Virtuálisgép-bővítmények alapján hoz létre. Használhatja a Marketplace-en eszközkészlet .azpkg fájlokat, akár az eszközzel létrehozott segítségével közzététele [manuális](azure-stack-create-and-publish-marketplace-item.md) lépéseket. Ez a témakör ismerteti az eszköz letöltése, a Virtuálisgép-sablonok alapján Piactéri elem létrehozása és majd tegye közzé a cikk az Azure Stack piactéren.     

## <a name="prerequisites"></a>Előfeltételek

 - Az eszközkészlet futtatása az Azure Stack gazdagépen vagy rendelkeznie kell [VPN](./asdk/asdk-connect.md#connect-with-vpn) kapcsolat ASDK gazdagépre a gépen, amelyen az eszközt futtatja.

 - Töltse le a [Azure Stack-gyorssablonok](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip) és csomagolja ki.

 - Töltse le a [Azure Gallery csomagolás eszköz](https://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe). 

 - A Marketplace-en való közzététel szükséges ikonok és a egy miniatűr fájlt. Használhatja a saját, vagy mentse a [minta](azure-stack-marketplace-publisher.md#support-files) helyileg ebben a példában a fájlokat.

## <a name="download-the-tool"></a>Az eszköz letöltéséhez

Letöltheti a Marketplace-en eszközkészlet [az Azure Stack-Tools-adattárból](azure-stack-powershell-download.md).

##  <a name="create-marketplace-items"></a>Marketplace-elemek létrehozása

Ebben a szakaszban a Marketplace-en eszközkészlet használhatja .azpkg formátumban Piactéri elem csomagot hozhat létre.  

### <a name="provide-marketplace-information-with-wizard"></a>Adja meg a Piactérrel kapcsolatos információkat a varázslóval

1. A Marketplace-en eszközkészlet futtatni egy PowerShell-munkamenetből:
   ```PowerShell
   .\MarketplaceToolkit.ps1
   ```

2. Kattintson a **megoldás** fülre. Ezen a képernyőn fogadja el a piactér elem adatait. Adja meg az elem adatait, ahogyan szeretné megjeleníteni a Marketplace-en. Azt is megadhatja a [paraméterfájl](azure-stack-marketplace-publisher.md#use-a-parameters-file) hogy feltöltse az űrlapot.  
    
    ![Képernyőkép a Marketplace-en eszközkészlet első képernyő](./media/azure-stack-marketplace-publisher/image7.png)
3. Kattintson a **Tallózás** , és jelöljön ki egy lemezképfájlt, ikon és a képernyőkép mezők. Használhatja saját ikonok, vagy a minta ikonjai a [támogatófájljait](azure-stack-marketplace-publisher.md#support-files) szakaszban.
4. Ha minden mező fel van töltve, válassza ki a "Előzetes megoldás" a megoldás a Marketplace-en belül megtekintheti. Vizsgálja felül, és a szöveg, képek és képernyőkép szerkesztése gombra kattintás előtt **tovább**.  

### <a name="import-template-and-create-package"></a>Sablon importálása és a csomag létrehozása

Ebben a szakaszban a sablon importálása, és a megoldás a bemeneti használata.

1.  Kattintson a **Tallózás** , és válassza ki a *azuredeploy.json* a letöltött sablonok a 101-egyszerű-Windows-VM mappájából.

    ![Képernyőkép a Marketplace-en eszközkészlet második képernyő](./media/azure-stack-marketplace-publisher/image8.png)
2.  A telepítési varázsló a rendszer kitölti a *alapszintű* lépést és a bemeneti elemek a sablonban megadott paraméterek. További lépéseket adhat hozzá, és helyezze át a bemenetek lépések között. Tegyük fel célszerű lehet az "Előtér-konfiguráció" és "Háttér-konfiguráció" lépés a megoldáshoz.
3.  Adja meg a AzureGalleryPackager.exe elérési útját.  
4.  Kattintson a **Create** (Létrehozás) gombra. A Marketplace-en eszközkészlet csomagokat a megoldás egy .azpkg fájlt. Amikor végzett, a varázsló a megoldás fájl elérési útja, és lehetővé teszi az folytatja, a csomag közzététele az Azure Stackhez.

## <a name="publish-marketplace-items"></a>Piactéri elem közzététele

Ebben a szakaszban a Piactéri elem az Azure Stack piactéren való közzétételéhez.

![Képernyőkép a Marketplace-en eszközkészlet első képernyő](./media/azure-stack-marketplace-publisher/image9.png)

1.  A varázsló közzététele a megoldás információt igényel:
    
    |Mező|Leírás|
    |-----|-----|
    | Szolgáltatás-rendszergazda neve | Szolgáltatás-rendszergazdai fiókjával.  Például: ServiceAdmin@mydomain.onmicrosoft.com |
    | Jelszó | Szolgáltatás-rendszergazdai fiók jelszavát. |
    | API-végpont | Az Azure Stack az Azure Resource Manager-végpontot. Példa: management.local.azurestack.external |
2.  Kattintson a **közzététel** , és a közzétételi napló jelenik meg.
3.  A közzétett cikk az Azure Stack portálon keresztül telepíteni tud olyan áll.

## <a name="use-a-parameters-file"></a>A paraméterfájl használata

Használhatja egy paraméterfájl adja meg a piactér elem adatokat.  

A piactér toolkit tartalmaz egy *solution.parameters.ps1* használhatja a saját paraméterfájl létrehozásához.

## <a name="support-files"></a>Fájlok támogatása

| Leírás | Sample |
| ----- | ----- |
| 40x40 .png icon | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| 90x90 .png icon | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| 115 x 115 .png ikon | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| 255x115 .png icon | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| 533 x 324 .png miniatűrje | ![](./media/azure-stack-marketplace-publisher/image5.png) |

## <a name="next-steps"></a>További lépések

[Piactéri termékek letöltése](azure-stack-download-azure-marketplace-item.md)  
[Hozzon létre, és a Piactéri elem közzététele](azure-stack-create-and-publish-marketplace-item.md)