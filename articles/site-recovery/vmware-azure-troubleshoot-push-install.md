---
title: "Az Azure Site Recovery hibaelhárítási VMware az Azure-bA |} Microsoft Docs"
description: "Hibaelhárítás a replikált Azure virtuális gépeken."
services: site-recovery
author: anoopkv
manager: gauravd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: anoopkv
ms.openlocfilehash: bdd5d87670ddb26d2c4474a319fe19bef1db62f5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Mobilitási szolgáltatás leküldéses telepítési problémák elhárításához

Ez a cikk szembesülhetnek, amikor megpróbálja telepíteni az Azure Site Recovery mobilitási szolgáltatás kívánja engedélyezni a védelmet a forráskiszolgálón esetleg gyakori hibáinak az elhárítását ismerteti.

## <a name="error-78007---the-requested-operation-could-not-be-completed"></a>78007 - hiba történt a kért művelet nem sikerült.
Ez a hiba a szolgáltatás több okból is okozhat. Válassza ki a megfelelő szolgáltatói hibát további hibaelhárítást.

* [Hiba 95103](#error-95103---protection-could-not-be-enabled-ep0854) 
* [Hiba 95105](#error-95105---protection-could-not-be-enabled-ep0856) 
* [Hiba 95107](#error-95107---protection-could-not-be-enabled-ep0858) 
* [Error 95108](#error-95108---protection-could-not-be-enabled-ep0859) 
* [Hiba 95117](#error-95117---protection-could-not-be-enabled-ep0865) 
* [Hiba 95213](#error-95213---protection-could-not-be-enabled-ep0874) 
* [Hiba 95224](#error-95224---protection-could-not-be-enabled-ep0883) 
* [Hiba 95265](#error-95265---protection-could-not-be-enabled-ep0902) 


## <a name="error-95105---protection-could-not-be-enabled-ep0856"></a>Hiba 95105 - védelmet nem sikerült engedélyezni a (EP0856)

**Hibakód:** | **Lehetséges okok** | **Hiba vonatkozó javaslatok**
--- | --- | ---
95105 </br>**Üzenet:** kódú hiba miatt sikertelen a forrásgépen a mobilitási szolgáltatás leküldéses telepítéséhez **EP0856**. <br> Vagy **fájl- és nyomtatómegosztás** nem engedélyezett, a forrás gép, vagy mert rendszer hálózati kapcsolódási problémái vannak a folyamatkiszolgáló és a forrásgép között.| **Fájl- és nyomtatómegosztás** nincs engedélyezve. | Engedélyezése **fájl- és nyomtatómegosztás** a forrásgépen, a Windows tűzfalon. A forrásoldali számítógép alatt **Windows tűzfal** > **lehetővé teszi egy alkalmazás vagy szolgáltatás átengedése a tűzfalon**, jelölje be **fájl- és nyomtatómegosztás az összes profil**. </br> Ezenkívül ellenőrizze a következő előfeltételek teljesülését, a leküldéses telepítés sikeres befejezéséhez.<br> Tudjon meg többet az [WMI hibaelhárítási problémák](#troubleshoot-wmi-issues).


## <a name="error-95107---protection-could-not-be-enabled-ep0858"></a>Hiba 95107 - védelmet nem sikerült engedélyezni a (EP0858)

**Hibakód:** | **Lehetséges okok** | **Hiba vonatkozó javaslatok**
--- | --- | ---
95107 </br>**Üzenet:** kódú hiba miatt sikertelen a forrásgépen a mobilitási szolgáltatás leküldéses telepítéséhez **EP0858**. <br> A mobilitási szolgáltatás telepítése a megadott hitelesítő adatok helytelenek, vagy a felhasználói fiók nem rendelkezik megfelelő jogosultsággal. | Telepíteni a mobilitási szolgáltatás a forrásgépen megadott felhasználói hitelesítő adatok helytelenek. | Győződjön meg arról, hogy a konfigurációs kiszolgálón a forrásgép megadott felhasználói hitelesítő adatok helyesek-e. <br> Hozzáadása vagy szerkesztése a felhasználói hitelesítő adatokat, nyissa meg a konfigurációs kiszolgálón, és válassza ki **Cspsconfigtool** > **fiók kezelése**. </br> Nézze meg a következő [Előfeltételek](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) a leküldéses telepítés sikeres befejezéséhez.


## <a name="error-95117---protection-could-not-be-enabled-ep0865"></a>Hiba 95117 - védelmet nem sikerült engedélyezni a (EP0865)

**Hibakód:** | **Lehetséges okok** | **Hiba vonatkozó javaslatok**
--- | --- | ---
95117 </br>**Üzenet:** kódú hiba miatt sikertelen a forrásgépen a mobilitási szolgáltatás leküldéses telepítéséhez **EP0865**. <br> Nem fut-e a forrásgép, vagy nincsenek a hálózati kapcsolódási problémái vannak a folyamatkiszolgáló és a forrásgép között. | Hálózati kapcsolódási problémái vannak a folyamatkiszolgáló és a forráskiszolgáló között. | Ellenőrizze a folyamatkiszolgáló és a forráskiszolgáló közötti kapcsolatot. </br> Nézze meg a következő [Előfeltételek](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) a leküldéses telepítés sikeres befejezéséhez.|

## <a name="error-95103---protection-could-not-be-enabled-ep0854"></a>Hiba 95103 - védelmet nem sikerült engedélyezni a (EP0854)

**Hibakód:** | **Lehetséges okok** | **Hiba vonatkozó javaslatok**
--- | --- | ---
95103 </br>**Üzenet:** kódú hiba miatt sikertelen a forrásgépen a mobilitási szolgáltatás leküldéses telepítéséhez **EP0854**. <br> A Windows Management Instrumentation (WMI) számára nem engedélyezett a forrásgépen, vagy nincsenek a hálózati kapcsolódási problémái vannak a folyamatkiszolgáló és a forrásgép között.| WMI le van tiltva, a Windows tűzfalon. | A Windows tűzfal engedélyezése a WMI. A **Windows tűzfal** > **lehetővé teszi egy alkalmazás vagy szolgáltatás átengedése a tűzfalon**, jelölje be **az összes profil WMI**. </br> Nézze meg a következő [Előfeltételek](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) a leküldéses telepítés sikeres befejezéséhez.|

## <a name="error-95213---protection-could-not-be-enabled-ep0874"></a>Hiba 95213 - védelmet nem sikerült engedélyezni a (EP0874)

**Hibakód:** | **Lehetséges okok** | **Hiba vonatkozó javaslatok**
--- | --- | ---
95213 </br>**Üzenet:** telepíteni a mobilitási szolgáltatást a(z) % SourceIP; hibakóddal sikertelenül **EP0874**. <br> | A forrásgép az operációs rendszer verziója nem támogatott. <br>| Győződjön meg arról, hogy a forrásgép operációs rendszerének verziója támogatott. Olvassa el a [támogatási mátrix](https://aka.ms/asr-os-support). </br> Nézze meg a következő [Előfeltételek](https://aka.ms/pushinstallerror) a leküldéses telepítés sikeres befejezéséhez.| 


## <a name="error-95108---protection-could-not-be-enabled-ep0859"></a>Hiba 95108 - védelmet nem sikerült engedélyezni a (EP0859)

**Hibakód:** | **Lehetséges okok** | **Hiba vonatkozó javaslatok**
--- | --- | ---
95108 </br>**Üzenet:** kódú hiba miatt sikertelen a forrásgépen a mobilitási szolgáltatás leküldéses telepítéséhez **EP0859**. <br>| A mobilitási szolgáltatás telepítése a megadott hitelesítő adatok helytelenek, vagy a felhasználói fiók nem rendelkezik megfelelő jogosultsággal. <br>| Győződjön meg arról, hogy a megadott hitelesítő adatok a **legfelső szintű** fiók hitelesítő adataival. Hozzáadása vagy szerkesztése a felhasználói hitelesítő adatokat, nyissa meg a konfigurációs kiszolgáló, és válassza ki a **Cspsconfigtool** parancsikonját az asztalon. Válassza ki **fiók kezelése** adhat hozzá vagy szerkesztheti a hitelesítő adatokat.|

## <a name="error-95265---protection-could-not-be-enabled-ep0902"></a>Hiba 95265 - védelmet nem sikerült engedélyezni a (EP0902)

**Hibakód:** | **Lehetséges okok** | **Hiba vonatkozó javaslatok**
--- | --- | ---
95265 </br>**Üzenet:** a forrásgép sikeres volt, de a forrásgép mobilitási szolgáltatás leküldéses telepítéséhez szükséges néhány rendszer módosítások érvénybe lépéséhez újraindítás. <br>| A mobilitási szolgáltatás régebbi verziója már telepítve lett a kiszolgálón.| A virtuális gép replikációs zökkenőmentesen továbbra is.<br> Indítsa újra a kiszolgálót az új fejlesztései előnyei beolvasása a mobilitási szolgáltatást a következő karbantartási időszakban.|


## <a name="error-95224---protection-could-not-be-enabled-ep0883"></a>Hiba 95224 - védelmet nem sikerült engedélyezni a (EP0883)

**Hibakód:** | **Lehetséges okok** | **Hiba vonatkozó javaslatok**
--- | --- | ---
95224 </br>**Üzenet:** leküldéssel telepíteni a mobilitási szolgáltatás forrásszámítógépen: % SourceIP; hibakóddal sikertelenül **EP0883**. Egy korábbi telepítés vagy frissítés a rendszer újraindítása függőben.| A rendszer nem újraindul, ha a mobilitási szolgáltatás régebbi vagy nem kompatibilis verziója.| Győződjön meg arról, hogy a mobilitási szolgáltatás változata nem létezik a kiszolgálón. <br> Indítsa újra a kiszolgálót, majd futtassa újra a védelemengedélyezési feladatot.|

## <a name="resource-to-troubleshoot-push-installation-problems"></a>Erőforrás leküldéses telepítési problémák megoldásához

#### <a name="troubleshoot-file-and-print-sharing-issues"></a>Hibaelhárítás – fájl és a nyomtató megosztási problémák
* [Engedélyezheti vagy tilthatja le a fájlmegosztás a csoportházirenddel](https://technet.microsoft.com/library/cc754359(v=ws.10).aspx)
* [Fájl-és nyomtatómegosztás a Windows tűzfalon keresztül](https://technet.microsoft.com/library/ff633412(v=ws.10).aspx)

#### <a name="troubleshoot-wmi-issues"></a>WMI-problémák elhárítása
* [Alapszintű WMI-ellenőrzés](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [WMI-hibaelhárítás](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [WMI-parancsfájlok és a WMI-szolgáltatások kapcsolatos problémák](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="next-steps"></a>További lépések

[Megtudhatja, hogyan](vmware-azure-tutorial.md) vész-helyreállítási beállítása a VMware virtuális gépek esetén.