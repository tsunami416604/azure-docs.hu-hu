---
title: Windows Update beállítások konfigurálása az Azure-Update Management való működéshez
description: Ez a cikk azokat a Windows Update beállításokat ismerteti, amelyeket az Azure Update Management való együttműködéshez konfigurál.
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79279025"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Update Management Windows Update beállításainak konfigurálása

Az Azure Update Management [Windows Update ügyfélre](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) támaszkodik a Windows-frissítések letöltéséhez és telepítéséhez. A Windows Update-ügyfél az Windows Server Update Services (WSUS) vagy a Windows Updatehoz való csatlakozáskor adott beállításokat használ. Ezen beállítások közül sokat a következővel kezelhet:

- Helyicsoportházirend-szerkesztő
- Csoportházirend
- PowerShell
- A beállításjegyzék közvetlen szerkesztése

A Update Management a Windows Update-ügyfél vezérléséhez megadott beállítások többségét tiszteletben tartja. Ha a nem Windows rendszerű frissítéseket engedélyező beállításokat használ, akkor a Update Management a frissítéseket is kezeli. Ha szeretné engedélyezni a frissítések letöltését a frissítés központi telepítése előtt, akkor a frissítés központi telepítése gyorsabb, hatékonyabb és kevésbé valószínű, hogy túllépi a karbantartási időszakot.

## <a name="pre-download-updates"></a>Frissítések előzetes letöltése

Ha a frissítések automatikus letöltését szeretné konfigurálni, de nem telepíti automatikusan őket, a Csoportházirend használatával állíthatja be az [Automatikus frissítések konfigurálása beállítást](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) **3**értékre. Ez a beállítás engedélyezi a szükséges frissítések letöltését a háttérben, és értesíti arról, hogy a frissítések készen állnak a telepítésre. Így Update Management továbbra is az ütemtervek felügyelete alatt marad, de a frissítések a Update Management karbantartási időszakon kívül is letölthetők. Ez a viselkedés megakadályozza, hogy a **karbantartási időszak túllépte** a hibákat a Update Managementban.

Ezt a beállítást a PowerShell használatával engedélyezheti a következő parancs futtatásával:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Újraindítási beállítások konfigurálása

Az automatikus frissítések konfigurálása az [újraindításhoz használt](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) beállításjegyzék és beállításkulcsok [szerkesztésével című témakörben](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) felsorolt beállításkulcsokat a gépek újraindítását okozhatja, még akkor is, ha a **frissítés központi telepítési** beállításai között nem ad meg **újraindítást** . Konfigurálja ezeket a beállításkulcsokat úgy, hogy az megfeleljen a környezetének.

## <a name="enable-updates-for-other-microsoft-products"></a>Egyéb Microsoft-termékek frissítéseinek engedélyezése

Alapértelmezés szerint a Windows Update-ügyfél úgy van konfigurálva, hogy csak Windows rendszerű frissítéseket biztosítson. Ha engedélyezi a **Windows-termékek frissítéseinek más Microsoft-termékekhez való frissítését** , akkor más termékek frissítései is megjelenhetnek, beleértve a Microsoft SQL Server és más Microsoft-szoftverek biztonsági javításait is. Ez a beállítás akkor konfigurálható, ha letöltötte és átmásolta a Windows 2016 és újabb verzióhoz elérhető legújabb [felügyeleti sablonfájlokat](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) .

Ha a Windows Server 2012 R2 rendszert futtatja, ez a beállítás nem konfigurálható Csoportházirend. Futtassa az alábbi PowerShell-parancsot ezeken a gépeken. Update Management megfelel ennek a beállításnak.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS-konfigurációs beállítások

Update Management támogatja a WSUS beállításait. Az Update Management használatához konfigurálható WSUS-beállítások az alábbi listában láthatók.

### <a name="intranet-microsoft-update-service-location"></a>Intranetes Microsoft frissítési szolgáltatás helye

A frissítések kereséséhez és letöltéséhez az [intranetes Microsoft Update szolgáltatás helyének megadása](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)területen adhat meg forrásokat. Alapértelmezés szerint a Windows Update ügyfél úgy van konfigurálva, hogy letöltse a frissítéseket a Windows Updateról. Ha a WSUS-kiszolgálót a számítógépek forrásaként adja meg, ha a frissítések nem hagyták jóvá a WSUS-ben, a frissítés telepítése sikertelen lesz. 

Ha a gépeket csak a belső frissítési szolgáltatásra szeretné korlátozni, akkor a konfigurálás ne [kapcsolódjon semmilyen Windows Update internetes helyhez](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations). 

## <a name="next-steps"></a>További lépések

Windows Update beállítások konfigurálása után az Azure-beli [virtuális gépek frissítéseinek és javításának kezelése](automation-tutorial-update-management.md)című témakör útmutatását követve ütemezhet egy frissítés központi telepítését.
