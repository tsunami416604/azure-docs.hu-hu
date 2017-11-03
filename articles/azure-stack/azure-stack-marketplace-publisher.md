---
title: "A piactér eszközkészlet segítségével létrehozása és közzététele a Piactéri elemek |} Microsoft Docs"
description: "Útmutató: a közzétételi eszközkészlet létrehozhat Piactéri elemek"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: ByronR
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/14/2017
ms.author: helaw
ms.openlocfilehash: 5b2c04d2cbc06e1572dc2e40712f6cf9d886aa1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>Adja hozzá a közzétételi eszközzel Piactéri elemek
Tartalom hozzáadása a [Azure verem piactér](azure-stack-marketplace.md) elérhetővé teszi a megoldás az Ön és a központi telepítés a bérlők számára.  A piactér eszközkészlet Azure piactér csomagok (.azpkg) fájlok, az infrastruktúra-szolgáltatási Azure Resource Manager-sablonok vagy Virtuálisgép-bővítmények alapján hoz létre.  Használhatja a piactér eszközkészlet .azpkg fájlok, akár a eszközzel létrehozott segítségével közzétételét [manuális](azure-stack-create-and-publish-marketplace-item.md) lépéseket.  Ez a témakör végigvezeti Önt az eszköz letöltése, a Virtuálisgép-sablonok alapján piactér elem létrehozása és közzététele majd, hogy az elem a veremben Azure piactér.     


## <a name="prerequisites"></a>Előfeltételek
 - Futtassa az eszközkészlet Azure verem gazdagépen vagy rendelkeznie kell [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) kapcsolat a gépen, amelyen az eszközt futtatja.

 - Töltse le a [Azure verem gyorsindítási sablonok](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip) és kibontásához.

 - Töltse le a [Azure gyűjtemény csomagolás eszköz](http://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe). 

 - A piactéren közzétételhez az ikonok és a miniatűr fájlt.  Használhatja a saját, vagy mentse a [minta](azure-stack-marketplace-publisher.md#support-files) helyileg ebben a példában a fájlokat.

## <a name="download-the-tool"></a>Töltse le az eszközt
A piactér eszközkészlet lehet [letöltötte az Azure verem eszközök tárházból](azure-stack-powershell-download.md).


##  <a name="create-marketplace-items"></a>Hozzon létre Piactéri elemek
Ebben a szakaszban a piactér eszközkészlet létrehozására használhatja a Piactéri elemet csomag .azpkg formátumban.  

### <a name="provide-marketplace-information-with-wizard"></a>Adja meg a varázsló piactér adatokat
1. Futtassa a piactér eszközkészlet egy PowerShell-munkamenetben:
```PowerShell
    .\MarketplaceToolkit.ps1
```

2. Kattintson a **megoldás** fülre.  Ezen a képernyőn fogadja el a Piactéri elemet kapcsolatos információkat. Adja meg az elemre vonatkozó információkat, ahogyan szeretné megjeleníteni a piactéren.  Azt is megadhatja a [paraméterfájl](azure-stack-marketplace-publisher.md#use-a-parameters-file) hogy feltöltse az űrlapot.  
    
    ![Képernyőkép a piactér eszközkészlet képernyője](./media/azure-stack-marketplace-publisher/image7.png)
3. Kattintson a **Tallózás** és jelöljön ki egy lemezképfájlt, ikon és a képernyőkép mezők.  Használhatja a saját ikonok vagy a minta ikonok a [támogatófájljait](azure-stack-marketplace-publisher.md#support-files) szakasz.
4. Ha minden mező fel van töltve, válassza a "Előzetes megoldás" megtekintheti a megoldás a piactér belül.  Módosítása, és a szöveg, képek és képernyőkép szerkesztése való kattintás előtt **következő**.  

### <a name="import-template-and-create-package"></a>Sablon importálása és a csomag létrehozása
Ebben a szakaszban importálja a sablont, és a megoldás bemeneti használata.

1.  Kattintson a **Tallózás** válassza ki a *azuredeploy.json* a letöltött sablonok a 101-egyszerű-Windows-alapú virtuális gép mappájából.

    ![Képernyőkép a piactér eszközkészlet második képernyő](./media/azure-stack-marketplace-publisher/image8.png)
2.  A telepítési varázsló a telepítéskor a *alapvető* lépés és a bemeneti elemek a sablonban megadott mindegyik paraméterhez.  Adjon hozzá további lépéseket, és helyezze át a bemeneti adatok között lépéseket.  Tegyük fel érdemes lehet az "Előtér-konfiguráció" és "A háttér konfiguráció" lépés a megoldás.
3.  Adja meg a AzureGalleryPackager.exe elérési útja.  
4.  Kattintson a **létrehozása** és a piactér eszközkészlet a megoldás csomagolja .azpkg fájlba.  Művelet befejeződése után a varázsló a megoldást fájl elérési útja, és folytatja a csomag közzététele az Azure-verem lehetőséget ad.


## <a name="publish-marketplace-items"></a>Piactéri elemek közzététele
Ebben a szakaszban közzéteszi a Piactéri elemet az Azure-verem piactéren.

![Képernyőkép a piactér eszközkészlet képernyője](./media/azure-stack-marketplace-publisher/image9.png)

1.  A varázsló a megoldás közzététele információra van szüksége:
    
    |Mező|Leírás|
    |-----|-----|
    | Szolgáltatás-rendszergazda neve | Szolgáltatás-rendszergazdai fiókjával.  Példa:ServiceAdmin@mydomain.onmicrosoft.com |
    | Jelszó | Szolgáltatás-rendszergazdai fiók jelszavát. |
    | API-végpont | Azure verem Azure Resource Manager-végpont.  Példa: management.local.azurestack.external |
2.  Kattintson a **közzététel** és a közzétételi napló jelenik meg.
3.  A verem Azure-portálon a közzétett cikk telepíthet áll.


## <a name="use-a-parameters-file"></a>A paraméterfájl használata
A paraméterfájl segítségével végezze el a piactér konfigurációelem-információk.  

A piactér Toolkit tartalmaz egy *solution.parameters.ps1* segítségével hozzon létre egy saját paraméterek fájlt.


## <a name="support-files"></a>Támogatási fájlok
| Leírás | Minta |
| ----- | ----- |
| 40 x 40 .png ikon | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| 90 x 90 .png ikon | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| 115 x 115 .png ikon | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| 255 x 115 .png ikon | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| 533 x 324 .png miniatűr | ![](./media/azure-stack-marketplace-publisher/image5.png) |


