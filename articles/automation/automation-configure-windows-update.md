---
title: Windows Update beállítások konfigurálása az Azure-Update Management való működéshez
description: Ez a cikk azokat a Windows Update beállításokat ismerteti, amelyeket a Update Management való együttműködéshez konfigurál.
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f50ca9515f12e8c9b5943904c4d0226f2ca3353c
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377563"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Update Management Windows Update beállításainak konfigurálása

A Update Management Windows Update a Windows-frissítések letöltésére és telepítésére támaszkodik. Ennek eredményeképpen tiszteletben tartjuk a Windows Update által használt beállításokat. Ha a nem Windows rendszerű frissítéseket engedélyező beállításokat használ, akkor a Update Management a frissítéseket is kezeli. Ha szeretné engedélyezni a frissítések letöltését a frissítés központi telepítése előtt, a frissítés központi telepítései gyorsabban és kevésbé valószínű, hogy túllépik a karbantartási időszakot.

## <a name="pre-download-updates"></a>Frissítések előzetes letöltése

A frissítések Csoportházirendban való automatikus letöltésének konfigurálásához beállíthatja az [Automatikus frissítések konfigurálása beállítást](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) **3**értékre. Ezzel letölti a háttérben a szükséges frissítéseket, de nem telepíti őket. Ez Update Management az ütemtervek vezérlésében, de a frissítések letöltésének engedélyezése a Update Management karbantartási időszakon kívül. Ez megakadályozhatja, hogy a **karbantartási időszak túllépte** a hibákat a Update Managementban.

Ezt a PowerShell-lel is beállíthatja, ha egy olyan rendszeren futtatja a következő PowerShell-t, amelyen szeretné automatikusan letölteni a frissítéseket.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>Automatikus telepítés letiltása

Az Azure-beli virtuális gépek alapértelmezés szerint automatikusan telepíthetők a frissítések. Ez azt eredményezheti, hogy a frissítések telepítésének megkezdése előtt telepítenie kell a frissítéseket Update Management. Ezt a viselkedést letilthatja a `NoAutoUpdate` beállításkulcs `1` értékre való beállításával. A következő PowerShell-kódrészlet az egyik módját mutatja be.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>Újraindítási beállítások konfigurálása

Az [Automatikus frissítések konfigurálása az](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-rej7uijui7jgistry) [újraindításhoz használt](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) beállításjegyzék és beállításkulcsok szerkesztésével című témakörben felsorolt beállításkulcsokat a gépek újraindítását okozhatja, még akkor is, ha a központi telepítési beállításokban nem adott meg **újraindítást** . . Ezeket a beállításkulcsokat szükség szerint kell konfigurálnia a környezet számára.

## <a name="enable-updates-for-other-microsoft-products"></a>Egyéb Microsoft-termékek frissítéseinek engedélyezése

Alapértelmezés szerint a Windows Update csak Windows-frissítéseket biztosít. Ha engedélyezi, **hogy a Windows frissítése során más Microsoft-termékek**is engedélyezzék a frissítéseket, más termékek frissítései is elérhetők, beleértve a SQL Server vagy más, az első féltől származó szoftverek biztonsági javításait is. Ez a beállítás nem konfigurálható Csoportházirend. Futtassa a következő PowerShellt azokon a rendszereken, amelyeken engedélyezni kívánja a más, első féltől származó javítások bekapcsolását, Update Management pedig tiszteletben tartja ezt a beállítást.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS-konfigurációs beállítások

**Update Management** tiszteletben tartja a WSUS konfigurációs beállításait. Az alábbi listában a Update Management használatához konfigurálható WSUS-beállítások listáját láthatja.

### <a name="intranet-microsoft-update-service-location"></a>Intranetes Microsoft frissítési szolgáltatás helye

A frissítések kereséséhez és letöltéséhez az [Intranet Microsoft Update szolgáltatás helye](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)területen adhatja meg a forrásokat.

## <a name="next-steps"></a>Következő lépések

Windows Update beállítások konfigurálása után az Azure-beli [virtuális gépek frissítéseinek és javításának kezelése](automation-tutorial-update-management.md) című témakör útmutatását követve ütemezhet egy frissítés központi telepítését.