---
title: Az Azure Stack Development Kit (ASDK) gazdaszámítógép előkészítése |} A Microsoft Docs
description: Ismerteti, hogyan készíti elő az Azure Stack Development Kit (ASDK) a számítógép ASDK telepítéséhez.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: b5314ce874c253151b88882b086257f96612c019
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51615398"
---
# <a name="prepare-the-asdk-host-computer"></a>A ASDK számítógép előkészítése
Mielőtt telepítené a ASDK a gazdagépen, a ASDK környezet telepítésének kell készíteni. A fejlesztői csomag gazdaszámítógép elő van készítve, amikor az merevlemez-meghajtóról a CloudBuilder.vhdx virtuális gép ASDK üzembe helyezés megkezdéséhez fog elindulni.

## <a name="prepare-the-development-kit-host-computer"></a>A fejlesztői csomag fogadó számítógép előkészítése
A ASDK a gazdagépen telepítése előtt elő kell készíteni a ASDK gazdakörnyezetben számítógép.
1. Jelentkezzen be egy helyi rendszergazdaként a fejlesztői csomag állomás számítógépen.
2. Győződjön meg arról, hogy a CloudBuilder.vhdx fájl át lett helyezve a legfelső szintű a C:\ meghajtó (C:\CloudBuilder.vhdx).
3. Futtassa a következő szkriptet a development kit telepítőfájl (asdk-installer.ps1) töltheti le a [Azure Stack GitHub-eszközök adattár](https://github.com/Azure/AzureStack-Tools) , a **C:\AzureStack_Installer** mappájába a fejlesztői csomag állomás számítógépen:

  ```powershell
  # Variables
  $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
  $LocalPath = 'C:\AzureStack_Installer'
  # Create folder
  New-Item $LocalPath -Type directory
  # Enforce usage of TLSv1.2 to download from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  # Download file
  Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
  ```

4. Egy rendszergazda jogú PowerShell-konzolon indítsa el a **C:\AzureStack_Installer\asdk-installer.ps1** szkriptet, és kattintson a **környezet előkészítését ismertető**.

    ![](media/asdk-prepare-host/1.PNG) 

5. Az a **Cloudbuilder válasszon vhdx** lapján, a telepítő, keresse meg és válassza ki a **cloudbuilder.vhdx** fájlt, amely a letöltött és kibontott az [az előző lépések](asdk-download.md). Ezen a lapon emellett igény szerint engedélyezheti a **illesztőprogramokat** jelölőnégyzetet, ha további illesztőprogramok hozzáadása a development kit gazdaszámítógépre van szüksége. Kattintson a **Tovább** gombra.  

    ![](media/asdk-prepare-host/2.PNG)

6. Az a **választható beállítások** lap, adja meg a helyi rendszergazda a development kit fogadó számítógép fiókját, és kattintson a **tovább**. Az alábbi választható beállításokat is megadhat értékeket:
  - **ComputerName**: Ez a beállítás a development kit gazdagép nevének beállítása. A névnek FQDN-követelményeknek kell megfelelniük, és hosszabb 15 karakternél rövidebb szerepnevet kell lennie. Az alapértelmezett érték a Windows által generált véletlenszerű számítógép nevét.
  - **Statikus IP-konfiguráció**: Beállítja a központi telepítés egy statikus IP-címet használja. Ellenkező esetben a telepítő újraindítja a cloudbuilder.vhdx, ha a hálózati adapterek vannak konfigurálva a DHCP Protokollt.

    ![](media/asdk-prepare-host/3.PNG)

  > [!IMPORTANT]
  > Ha nem ad meg ebben a lépésben a helyi rendszergazdai hitelesítő adatokkal, kell közvetlenül vagy a gazdagépre KVM-hozzáférés beállítása a csomag részeként a számítógép újraindítása után.

7. Ha az előző lépésben statikus IP-konfigurációt, meg kell most:
    - Válasszon ki egy hálózati adaptert. Ellenőrizze, hogy csatlakozhat az adapter gombra való kattintás előtt **tovább**.
    - Győződjön meg arról, hogy a **IP-cím**, **átjáró**, és **DNS** értékek helyesek, és kattintson a **tovább**.
13. Kattintson a **tovább** az előkészítési folyamat elindításához.
14. Az előkészítés azt jelzi, ha **befejezve**, kattintson a **tovább**.
15. Kattintson a **Újraindítás most** , a fejlesztői csomag gazdaszámítógépen indítsa el a cloudbuilder.vhdx és [a telepítési folyamat folytatásához](asdk-install.md).

    ![](media/asdk-prepare-host/4.PNG)


## <a name="next-steps"></a>További lépések
[Telepítse a ASDK](asdk-install.md)
