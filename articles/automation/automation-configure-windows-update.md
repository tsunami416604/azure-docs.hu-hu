---
title: A Windows Update beállításainak konfigurálása az Azure Update Management szolgáltatással való együttműködésre
description: Ez a cikk az Azure Update Management szolgáltatással való együttműködésre konfigurált Windows Update-beállításokat ismerteti.
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279025"
---
# <a name="configure-windows-update-settings-for-update-management"></a>A Windows Update beállításainak konfigurálása a frissítéskezeléshez

Az Azure Update Management a [Windows Update ügyfélprogramra](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) támaszkodik a Windows-frissítések letöltéséhez és telepítéséhez. A Windows Update ügyfél által a Windows Server Update Services (WSUS) vagy a Windows Update szolgáltatáshoz való csatlakozáskor használt speciális beállítások vannak. A beállítások közül sok a következőkkel kezelhető:

- Helyicsoportházirend-szerkesztő
- Csoportházirend
- PowerShell
- A rendszerleíró adatbázis közvetlen szerkesztése

Az Update Management a Windows Update ügyfél vezérléséhez megadott számos beállítást tiszteletben tartja. Ha a nem Windows-frissítések engedélyezéséhez beállításokat használ, az Update Management is kezeli ezeket a frissítéseket. Ha azt szeretné, hogy a frissítések letöltése a frissítés központi telepítése előtt, a frissítés központi telepítése gyorsabb, hatékonyabb és kevésbé valószínű, hogy meghaladja a karbantartási időszakot.

## <a name="pre-download-updates"></a>Frissítések előzetes letöltése

A frissítések automatikus letöltésének konfigurálásához, de nem automatikusan történő telepítéséhez a Csoportházirend segítségével állítsa az [Automatikus frissítések konfigurálása beállítást](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) **3-ra.** Ez a beállítás lehetővé teszi a szükséges frissítések letöltését a háttérben, és értesíti, hogy a frissítések készen állnak a telepítésre. Ily módon az Update Management továbbra is az ütemezések ellenőrzése alatt marad, de a frissítések letölthetők az Update Management karbantartási időszakán kívül. Ez a viselkedés megakadályozza, hogy a Karbantartási időszak túllépje a frissítéskezelés **hibáit.**

Ezt a beállítást a PowerShell használatával a következő parancs futtatásával engedélyezheti:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Újraindítási beállítások konfigurálása

Az automatikus frissítések [konfigurálása az](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) újraindítás kezeléséhez használt beállításjegyzék- és [beállításkulcsok](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) szerkesztésével című beállításkulcs ban felsorolt beállításkulcsok a gépek újraindítását okozhatják, még akkor is, ha a **Telepítés frissítése** beállításokban a Soha **nem újraindul.** Konfigurálja ezeket a beállításkulcsokat a környezetéhez legjobban.

## <a name="enable-updates-for-other-microsoft-products"></a>Frissítések engedélyezése más Microsoft-termékekhez

Alapértelmezés szerint a Windows Update ügyfél úgy van beállítva, hogy csak a Windows rendszerhez biztosítson frissítéseket. Ha engedélyezi a Frissítések más **Microsoft-termékekhez a Windows-beállítás frissítésekor** című értesítést, akkor más termékekhez is frissítéseket kap, beleértve a Microsoft SQL Server és más Microsoft-szoftverek biztonsági javításait is. Ez a beállítás akkor konfigurálható, ha a Windows 2016-os és újabb verzióihoz elérhető legújabb [felügyeleti sablonfájlokat](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) töltötte le és másolta.

Ha Windows Server 2012 R2 rendszert futtat, ezt a beállítást csoportházirend nem konfigurálhatja. Futtassa a következő PowerShell-parancsot ezeken a gépeken. Az Update Management megfelel ennek a beállításnak.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>A WSUS konfigurációs beállításai

Az Update Management támogatja a WSUS-beállításokat. Az Update Management szolgáltatással való használatra konfigurálható WSUS-beállítások az alábbiakban találhatók.

### <a name="intranet-microsoft-update-service-location"></a>Intranetes Microsoft frissítési szolgáltatás helye

A frissítések beolvasásához és letöltéséhez szükséges forrásokat a [Microsoft Update szolgáltatás helyének megadása](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)csoportban adhatja meg. Alapértelmezés szerint a Windows Update ügyfél úgy van beállítva, hogy frissítéseket töltsön le a Windows Update szolgáltatásból. Ha a WSUS-kiszolgálót a gépek forrásaként adja meg, ha a frissítéseket nem hagyja jóvá a WSUS, a frissítés telepítése sikertelen lesz. 

Ha a gépeket csak a belső frissítési szolgáltatásra szeretné korlátozni, konfigurálja [a Ne csatlakozzon a Windows Update internetes helyéhez](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations). 

## <a name="next-steps"></a>További lépések

A Windows Update beállításainak konfigurálása után ütemezheti a frissítések központi telepítését az [Azure-beli virtuális gépek frissítéseinek és javításainak kezelése](automation-tutorial-update-management.md)című részben található utasításokat követve.
