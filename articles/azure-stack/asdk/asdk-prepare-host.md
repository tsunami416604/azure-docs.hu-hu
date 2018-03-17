---
title: "A Azure verem Development Kit (ASDK) számítógép előkészítése |} Microsoft Docs"
description: "Ismerteti, hogyan lehet a Azure verem Development Kit (ASDK) számítógép ASDK telepítés előkészítéséhez."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 8b1a6298ab32dc364aa1543e4a8d5db47b02a098
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/17/2018
---
# <a name="prepare-the-asdk-host-computer"></a>A ASDK számítógép előkészítése
A ASDK az állomáson telepítése előtt a ASDK környezet telepítési kell készíteni. Ha a development kit gazdaszámítógép készült, akkor a CloudBuilder.vhdx virtuális gép merevlemez-meghajtóról ASDK telepítésének megkezdéséhez fog indulni.

## <a name="prepare-the-development-kit-host-computer"></a>Készítse elő a development kit gazdaszámítógépen
A ASDK az állomáson telepítése előtt elő kell készíteni a ASDK állomás számítógépes környezetekben.
1. Jelentkezzen be a helyi rendszergazdájaként a development kit gazdaszámítógéphez.
2. Győződjön meg arról, hogy a CloudBuilder.vhdx fájlt a C:\ meghajtó (C:\CloudBuilder.vhdx), a legfelső szintű át lett helyezve.
3. Futtassa a következő parancsfájlt a development kit telepítőfájl (asdk-installer.ps1) töltheti le a [Azure verem GitHub-eszközök adattár](https://github.com/Azure/AzureStack-Tools) számára a **C:\AzureStack_Installer** mappájában található a fejlesztői csomag gazdaszámítógépen:

  ```powershell
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. Egy rendszergazda jogú PowerShell-konzolon, indítsa el a **C:\AzureStack_Installer\asdk-installer.ps1** parancsfájlt, és kattintson a **környezet előkészítése**.

    ![](media/asdk-prepare-host/1.PNG) 

5. Az a **Cloudbuilder válasszon vhdx** oldalon a telepítő, keresse meg és válassza ki a **cloudbuilder.vhdx** letöltött és kibontott a fájl [az előző lépések](asdk-download.md). Ezen a lapon, ha szükséges, engedélyezheti a **illesztőprogramok hozzáadásával** jelölőnégyzetet, ha további illesztőprogramok hozzáadásához a development kit gazdaszámítógépre van szüksége. Kattintson a **Tovább** gombra.  

    ![](media/asdk-prepare-host/2.PNG)

6. A a **választható beállítások** lapján adja meg a helyi rendszergazda fiókot a development kit fogadó számítógép információi, és kattintson a **következő**. A következő választható beállítások értékeket is megadhatja:
  - **Számítógépnév**: Ez a beállítás egy development kit állomás neve. A név FQDN követelményeknek kell megfelelniük, és lehet 15 karakternél rövidebb szerepnevet. Az alapértelmezett érték a Windows által generált véletlenszerű számítógép nevét.
  - **Időzóna**: időzónáját adja meg a development kit gazdagép. Az alapértelmezett érték (UTC-8:00) csendes-óceáni idő (Egyesült Államok és Kanada).
  - **Statikus IP-konfiguráció**: Beállítja azt a környezetet, hogy statikus IP-cím. Ellenkező esetben amikor a telepítő újraindítja a cloudbuilder.vhx, a hálózati adapterek vannak konfigurálva a DHCP.

    ![](media/asdk-prepare-host/3.PNG)

  > [!IMPORTANT]
  > Ha nem ad meg ebben a lépésben a helyi rendszergazdai hitelesítő adatokat, szüksége lesz közvetlen vagy a gazdagép eléréséhez KVM beállítása a csomag részeként a számítógép újraindítása után.

7. Ha az előző lépésben a statikus IP-konfigurációt, akkor most le kell:
    - Válasszon olyan hálózati adaptert. Győződjön meg arról is elérheti az adapter kattintás előtt **következő**.
    - Győződjön meg arról, hogy a **IP-cím**, **átjáró**, és **DNS** értékek megfelelőek, és kattintson a **következő**.
13. Kattintson a **következő** az előkészítési folyamat elindításához.
14. Az előkészítési azt jelzi, ha **befejezve**, kattintson a **következő**.
15. Kattintson a **Újraindítás most** a development kit gazdaszámítógép indul a cloudbuilder.vhdx és [a telepítési folyamat folytatásához](asdk-install.md).

    ![](media/asdk-prepare-host/4.PNG)


## <a name="next-steps"></a>További lépések
[A ASDK telepítése](asdk-install.md)