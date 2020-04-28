---
title: Microsoft Teams a Windows rendszerű virtuális asztalon – Azure
description: A Microsoft Teams használata a Windows rendszerű virtuális asztali gépeken.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a860b005457c6e02187423a3ffbbc63fe7c758b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187528"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>A Microsoft Teams használata a Windows rendszerű virtuális asztalon

> A következőkre vonatkozik: Windows 10 és Windows 10 IoT Enterprise

A virtualizált környezetek egyedi kihívásokat jelentenek az olyan együttműködési alkalmazások számára, mint a Microsoft Teams, többek között a nagyobb késés, a nagy gazda CPU-használat és a gyenge általános hang-és video-teljesítmény. Ha többet szeretne megtudni a Microsoft Teams a VDI-környezetekben való használatáról, tekintse meg [a virtualizált asztali infrastruktúra csapatait](https://docs.microsoft.com/microsoftteams/teams-for-vdi).

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy a Microsoft Teams szolgáltatást használhassa a Windows rendszerű virtuális asztalon, ezeket a következő műveleteket kell végrehajtania:

- Telepítse a [Windows asztali ügyfelet](connect-windows-7-and-10.md) egy olyan Windows 10-es eszközre, amely megfelel a Microsoft Teams [Hardware követelményeinek](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app).
- Csatlakozhat egy Windows 10 rendszerű többmunkamenetes vagy Windows 10 Enterprise rendszerű virtuális géphez (VM).
- [Készítse elő a hálózatát](https://docs.microsoft.com/microsoftteams/prepare-network) a Microsoft Teams szolgáltatásban.

## <a name="use-unoptimized-microsoft-teams"></a>Nem optimalizált Microsoft-csapatok használata

A Microsoft Teams szolgáltatást használhatja a Windows rendszerű virtuális asztali környezetekben a Microsoft Teams csevegési és együttműködési funkcióinak kihasználása érdekében. A Windows virtuális asztal nem támogatja a (z) VDI audio/video (AV) optimalizáción lévő csapatokat. A hívások és értekezletek nem támogatottak. Ha a szervezet szabályzatai lehetővé teszik, a hanghívások és az értekezletek hangtal való csatlakoztatása is megváltozhat, a hívások nem optimalizált hangminősége pedig a gazdagép konfigurációjától függ, és nem lehet megbízható. További információért tekintse meg [a csapatokat a VDI teljesítményének megfontolása](https://docs.microsoft.com/microsoftteams/teams-for-vdi#teams-on-vdi-performance-considerations)című témakörben.

### <a name="prepare-your-image-for-teams"></a>A rendszerkép előkészítése a csapatok számára

A csapatok számítógépenkénti telepítésének engedélyezéséhez állítsa be a következő beállításkulcsot a gazdagépen:

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 1
```

### <a name="install-microsoft-teams"></a>A Microsoft Teams telepítése

A Teams Desktop alkalmazást üzembe helyezheti gépi telepítéssel. A Microsoft Teams telepítése a Windows rendszerű virtuális asztali környezetben:

1. Töltse le a környezetének megfelelő [Teams msi-csomagot](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) . Javasoljuk, hogy a 64 bites telepítőt egy 64 bites operációs rendszeren használja.
2. A parancs futtatásával telepítse az MSI-t a gazda virtuális gépre.

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      Ezzel a csapatokat a program files vagy program Files (x86) rendszerre telepíti. Amikor legközelebb bejelentkezik, és elindítja a csapatokat, az alkalmazás kérni fogja a hitelesítő adatait.

      > [!NOTE]
      > A felhasználók és a rendszergazdák jelenleg nem tudják letiltani a csapatok automatikus indítását a bejelentkezés során.

      A következő parancs futtatásával távolíthatja el az MSI-t a gazdagép virtuális gépről:

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > Ha a ALLUSER = 1 MSI-beállítással rendelkező csapatokat telepít, az automatikus frissítések le lesznek tiltva. Javasoljuk, hogy havonta legalább egyszer frissítse a csapatokat. Ha többet szeretne megtudni a csapatok asztali alkalmazás üzembe helyezéséről, tekintse meg a [csapatok asztali alkalmazás üzembe helyezése a virtuális gépen](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm)című témakört.

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Gazdagépek RDP protokoll tulajdonságainak testreszabása
A gazdagépek RDP protokoll (RDP) tulajdonságainak (például a többmonitoros élmény, a mikrofon és a hangátirányítás) testreszabása lehetővé teszi, hogy a felhasználók igényei alapján optimális élményt nyújtson a felhasználóknak. Az RDP-tulajdonságokat a Windows virtuális asztalon a **set-RdsHostPool** parancsmag **-CustomRdpProperty** paraméterrel szabhatja testre.
A támogatott tulajdonságok és az alapértelmezett értékek teljes listájáért tekintse meg a [támogatott RDP-fájlok beállításait](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) .
