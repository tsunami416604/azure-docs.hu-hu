---
title: Microsoft Teams a Windows virtuális asztalon – Azure
description: A Microsoft Teams használata Windows virtuális asztalon.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8b9b33076a2c2cea27fea181b760a721488682c9
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657016"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>A Microsoft Teams használata a Windows virtuális asztalon

> A következőkre vonatkozik: Windows 10 és Windows 10 IoT Enterprise

A virtualizált környezetek egyedi kihívást jelentenek az olyan együttműködési alkalmazások számára, mint a Microsoft Teams, beleértve a megnövekedett késést, a magas gazdaprocesszor-használatot, valamint a gyenge általános hang- és videoteljesítményt. Ha többet szeretne tudni a Microsoft Teams VDI-környezetekben való használatáról, olvassa el [a Teams a virtualizált asztali infrastruktúrához című részt.](https://docs.microsoft.com/microsoftteams/teams-for-vdi)

## <a name="prerequisites"></a>Előfeltételek

A Microsoft Teams Windows virtuális asztalon való használata előtt az alábbi dolgokat kell megtennie:

- Telepítse a [Windows Asztali ügyfelet](connect-windows-7-and-10.md) egy olyan Windows 10-es eszközre, amely megfelel a Microsoft Teams [hardverkövetelményeinek.](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)
- Csatlakozás Windows 10 többmunkamenetes vagy Windows 10 enterprise virtuális géphez.Connect to a Windows 10 Multi-session or Windows 10 Enterprise virtual machine (VM).
- [Készítse elő hálózatát](https://docs.microsoft.com/microsoftteams/prepare-network) a Microsoft Teams számára.

## <a name="use-unoptimized-microsoft-teams"></a>Nem optimalizált Microsoft Teams használata

Windows Virtual Desktop környezetében a nem optimalizált Microsoft Teams segítségével kihasználhatja a Microsoft Teams teljes csevegési és együttműködési funkcióit, valamint a hanghívásokat. A hívások hangminősége a gazdagép konfigurációjától függően változik, mivel az nem optimalizált hívások a gazdaprocesszor több processzorát használják.

### <a name="prepare-your-image-for-teams"></a>Készítse elő a képet a teamsszámára

A Teams számítógépenkénti telepítésének engedélyezéséhez állítsa be a következő beállításkulcsot az állomáson:

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 0x1
```

### <a name="install-microsoft-teams"></a>A Microsoft Teams telepítése

A Teams asztali alkalmazást számítógépenkénti telepítéssel telepítheti. A Microsoft Teams telepítése a Windows virtuális asztali környezetben:

1. Töltse le a [Teams MSI csomagot,](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) amely megfelel a környezetének. Javasoljuk, hogy a 64 bites telepítőt 64 bites operációs rendszeren használja.
2. Futtassa ezt a parancsot az MSI gazdagépre való telepítéséhez.

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSERS=1
      ```

      Ezzel a Teams programfájlokba vagy programfájlokba (x86) telepíti a Csapatokat. Amikor legközelebb bejelentkezik, és elindítja a Teamst, az alkalmazás kérni fogja a hitelesítő adatait.

      > [!NOTE]
      > A felhasználók és a rendszergazdák jelenleg nem tilthatják le a Teams automatikus indítását a bejelentkezés során.

      Az MSI eltávolításához a gazdagép virtuális gépéről futtassa a következő parancsot:

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > Ha az ALLUSERS=1 MSI beállítással telepíti a Teamst, az automatikus frissítések le lesznek tiltva. Javasoljuk, hogy legalább havonta egyszer frissítse a Csapatokat.
