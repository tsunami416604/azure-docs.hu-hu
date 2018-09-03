---
title: Microsoft Azure Data Box Disk üzembe helyezése | Microsoft Docs
description: Az oktatóanyag az Azure Data Box Disk üzembe helyezését mutatja be
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/28/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 6fcc7823a7e2f2f1e280622a1fa05d4417a71546
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43143482"
---
# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>Oktatóanyag: Azure Data Box Disk kicsomagolása, csatlakoztatása és a zárolás feloldása

Az oktatóanyag az Azure Data Box Disk kicsomagolását, csatlakoztatását és a zárolás feloldását mutatja be.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Data Box Disk kicsomagolása.
> * A Data Box Disk csatlakoztatása és a zárolás feloldása.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Befejezte [az Azure Data Box Disk rendelését ismertető oktatóanyagot](data-box-disk-deploy-ordered.md).
2. Megkapta a meghajtókat, és a portálon a feldolgozás **Kézbesítve** állapotra frissül.
3. Rendelkezik egy gazdaszámítógéppel, amelyre telepítheti a Data Box Disk zárolásának feloldására szolgáló eszközt. A gazdaszámítógépen:
    - egy [támogatott operációs rendszernek](data-box-disk-system-requirements.md) kell futnia;
    - a [Windows PowerShell 4-es verziójának telepítve kell lennie](https://www.microsoft.com/download/details.aspx?id=40855);
    - a [.NET Framework 4.5.1-es verziójának telepítve kell lennie](https://www.microsoft.com/download/details.aspx?id=30653);
    - a [BitLockernek](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server) engedélyezve kell lennie;
    - a [Windows Management Framework 4-es verziójának telepítve kell lennie](https://www.microsoft.com/en-us/download/details.aspx?id=40855). 

## <a name="unpack-your-disks"></a>A meghajtók kicsomagolása

 A meghajtók kicsomagolásához kövesse az alábbi lépéseket.

1. A Data Box Disk-meghajtókat kisméretű dobozokban küldjük el. Nyissa ki a dobozt, és vegye ki a tartalmát. Ellenőrizze, hogy a doboz tartalmazza-e az 1–5 SSD-meghajtót, valamint meghajtónként egy USB-csatlakozókábelt. Vizsgálja meg, hogy a dobozon nincsenek-e illetéktelen felnyitás nyomai vagy látható sérülések. 

    ![Data Box Disk szállítási csomag](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. Ha a csomagot illetéktelenül kinyitották vagy jelentős sérülések láthatók rajta, ne nyissa ki. Vegye fel a kapcsolatot a Microsoft támogatási szolgálatával, ahol szakembereink segíthetnek felmérni, hogy a meghajtók működőképes állapotban vannak-e, vagy esetleg küldenünk kell egy újabb csomagot.
3. Ellenőrizze, hogy a dobozon van-e egy átlátszó tartó egy fuvarlevélcímkével (az aktuális címke alatt) a visszaküldéshez. Ha a címke elveszett vagy sérült, az Azure Portalról letöltheti és kinyomtathatja. 

    ![Data Box Disk-fuvarlevélcímke](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Őrizze meg a dobozt és a térkitöltő anyagot a meghajtók visszaküldéséhez.

## <a name="connect-and-unlock-your-disks"></a>A meghajtók csatlakoztatása és a zárolás feloldása

A meghajtók csatlakoztatásához és a zárolás feloldásához kövesse az alábbi lépéseket.

1. A csomagban foglalt kábellel csatlakoztassa a meghajtót a Windows egy támogatott verzióját futtató számítógéphez (lásd az előfeltételeket). 

    ![A Data Box Disk csatlakoztatása](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. Az Azure Portalon lépjen az **Általános > Eszköz adatai** menüpontra. 
3. Kattintson a **Data Box Disk zárolásának feloldására szolgáló eszköz letöltési** hivatkozására. 

    ![A Data Box Disk zárolásának feloldására szolgáló eszköz letöltése](media/data-box-disk-deploy-set-up/data-box-disk-unload1.png)     

4. Csomagolja ki az eszközt ugyanazon a számítógépen, amelyet az adatok másolásához használ majd.
5. Nyisson meg egy parancssori ablakot, vagy futtassa a Windows PowerShellt rendszergazdai jogosultsággal ugyanazon a számítógépen.
6. (Nem kötelező) Annak érdekében, hogy meggyőződhessen róla, hogy a meghajtó zárolásának feloldásához használt számítógép teljesíti az operációs rendszerre vonatkozó követelményeket, futtassa a rendszerellenőrzési parancsot. Az alábbiakban egy példa látható a kimenetre. 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

7. Az Azure Portalon lépjen az **Általános > Eszköz adatai** menüpontra. A hozzáférési kulcs másolásához kattintson a másolás ikonra.
8. Futtassa a `DataBoxDiskUnlock.exe` fájlt, és adja meg a hozzáférési kulcsot. Megjelenik a meghajtóhoz rendelt betűjel. Az alábbiakban egy példa látható a kimenetre.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkeys (format: passkey1;passkey2;passkey3):
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

9. Ismételje meg a 6–8. lépést a további csatlakoztatott meghajtók esetén. Amennyiben segítségre van szüksége a Data Box Disk zárolásának feloldására szolgáló eszköz használatához, használja a súgó parancsot.   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKeys:<passkey_list_separated_by_semicolon>
    
    Example: DataBoxUnlock /PassKeys:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKeys:       Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
10. A meghajtó zárolásának feloldása után megtekintheti annak tartalmát.    

    ![A Data Box Disk tartalma](media/data-box-disk-deploy-set-up/data-box-disk-content.png) 

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az Azure Data Box Diskkel kapcsolatos alábbi témakörökkel ismerkedett meg:

> [!div class="checklist"]
> * A Data Box Disk kicsomagolása.
> * A Data Box Disk csatlakoztatása és a zárolás feloldása.


A következő oktatóanyag azt mutatja be, hogyan másolhat adatokat a Data Box Diskre.

> [!div class="nextstepaction"]
> [Adatok másolása a Data Box Diskre](./data-box-disk-deploy-copy-data.md)

