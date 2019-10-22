---
title: Windows Update beállítások konfigurálása az Azure-Update Management való működéshez
description: Ez a cikk azokat a Windows Update beállításokat ismerteti, amelyeket az Azure Update Management való együttműködéshez konfigurál.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 813d34f9c07e6c2909c483f040d4f3bf09b3ad24
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690840"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Update Management Windows Update beállításainak konfigurálása

Az Azure Update Management Windows Update a Windows-frissítések letöltésére és telepítésére támaszkodik. Ennek eredményeképpen a Update Management a Windows Update által használt beállítások többségét tiszteletben tartja. Ha a nem Windows rendszerű frissítéseket engedélyező beállításokat használ, akkor a Update Management a frissítéseket is kezeli. Ha szeretné engedélyezni a frissítések letöltését a frissítés központi telepítése előtt, akkor a frissítés központi telepítése gyorsabb, hatékonyabb és kevésbé valószínű, hogy túllépi a karbantartási időszakot.

## <a name="pre-download-updates"></a>Frissítések előzetes letöltése

Ha a frissítések automatikus letöltését szeretné konfigurálni a Csoportházirendban, állítsa az [Automatikus frissítések konfigurálása beállítást](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) **3**értékre. Ez a beállítás lehetővé teszi a szükséges frissítések letöltését a háttérben, de nem telepíti őket. Így Update Management továbbra is az ütemtervek felügyelete alatt marad, de a frissítések a Update Management karbantartási időszakon kívül is letölthetők. Ez a viselkedés megakadályozza a "karbantartási időszak meghaladta" hibákat a Update Managementban.

Ezt a beállítást a következő PowerShell-parancs futtatásával is bekapcsolhatja a frissítések automatikus letöltéséhez konfigurálni kívánt rendszeren:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>Automatikus telepítés letiltása

Alapértelmezés szerint az Azure Virtual Machines (VM) szolgáltatásban a frissítések automatikus telepítése engedélyezve van. Ennek hatására előfordulhat, hogy a frissítések telepítésének megkezdése előtt Update Management. Ezt a viselkedést letilthatja a `NoAutoUpdate` beállításkulcs `1` értékre való beállításával. A következő PowerShell-kódrészlet ennek módját mutatja be:

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>Újraindítási beállítások konfigurálása

Az automatikus frissítések konfigurálása az [újraindításhoz használt](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) beállításjegyzék és beállításkulcsok [szerkesztésével című témakörben](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) felsorolt beállításkulcsokat a gépek újraindítását okozhatja, még akkor is, ha a **frissítés központi telepítési** beállításaiban nem ad meg **újraindítást** . . Ezeket a beállításkulcsokat úgy kell konfigurálni, hogy az megfeleljen a környezetének.

## <a name="enable-updates-for-other-microsoft-products"></a>Egyéb Microsoft-termékek frissítéseinek engedélyezése

Alapértelmezés szerint a Windows Update csak a Windows rendszerhez biztosít frissítéseket. Ha engedélyezi a **Windows-termékek frissítéseinek más Microsoft-termékekhez való frissítését** , akkor más termékek frissítései is megjelenhetnek, beleértve a Microsoft SQL Server és más Microsoft-szoftverek biztonsági javításait is. Ez a beállítás nem konfigurálható Csoportházirend. Futtassa a következő PowerShell-parancsot azokon a rendszereken, amelyeken engedélyezni szeretné a többi Microsoft-frissítést. A Update Management megfelel ennek a beállításnak.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS-konfigurációs beállítások

A Update Management megfelel a Windows Server Update Services-(WSUS-) beállításoknak. Az Update Management használatához konfigurálható WSUS-beállítások az alábbi listában láthatók.

### <a name="intranet-microsoft-update-service-location"></a>Intranetes Microsoft frissítési szolgáltatás helye

A frissítések kereséséhez és letöltéséhez az [intranetes Microsoft Update szolgáltatás helyének megadása](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)területen adhat meg forrásokat.

## <a name="next-steps"></a>Következő lépések

Windows Update beállítások konfigurálása után az Azure-beli [virtuális gépek frissítéseinek és javításának kezelése](automation-tutorial-update-management.md)című témakör útmutatását követve ütemezhet egy frissítés központi telepítését.