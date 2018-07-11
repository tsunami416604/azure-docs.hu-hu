---
title: Az Azure Site Recovery hibaelhárítása a VMware-ből az Azure-bA |} A Microsoft Docs
description: Hibaelhárítás az Azure-beli virtuális gépek replikálásakor.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.author: ramamill
ms.date: 07/06/2018
ms.openlocfilehash: 8d5db03eeebb659414ea1f554e5b34c938fd2795
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952909"
---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>A mobilitási szolgáltatás leküldéses telepítési problémák elhárítása

Ez a cikk ismerteti az Azure Site Recovery mobilitási szolgáltatás telepítésekor kívánja engedélyezni a védelmet a forráskiszolgálón, előfordulhat, hogy tapasztalt gyakori hibák elhárítása.

## <a name="error-78007---the-requested-operation-could-not-be-completed"></a>Hiba 78007 – a kért művelet nem fejezhető be.
Ez a hiba a szolgáltatás több okból is hibajelzést. Válassza ki a megfelelő szolgáltatói hibát további hibaelhárítást.

* [95103-as](#error-95103---protection-could-not-be-enabled-ep0854) 
* [95105-ös](#error-95105---protection-could-not-be-enabled-ep0856) 
* [95107-es](#error-95107---protection-could-not-be-enabled-ep0858) 
* [95108-as](#error-95108---protection-could-not-be-enabled-ep0859) 
* [95117-es](#error-95117---protection-could-not-be-enabled-ep0865) 
* [95213-as](#error-95213---protection-could-not-be-enabled-ep0874) 
* [95224-es](#error-95224---protection-could-not-be-enabled-ep0883) 
* [95265-ös](#error-95265---protection-could-not-be-enabled-ep0902) 


## <a name="error-95105---protection-could-not-be-enabled-ep0856"></a>95105-ös - védelem nem sikerült engedélyezni a (ep0856 hiba)

**Hibakód:** | **Lehetséges okok** | **Hiba vonatkozó javaslatok**
--- | --- | ---
95105 </br>**Üzenet:** forrásgépen a mobilitási szolgáltatás leküldéses telepítéséhez hibakóddal sikertelenül **EP0856**. <br> Mindkét **fájl- és nyomtatómegosztás** nem engedélyezett a forrásoldali gép, vagy hogy van hálózati kapcsolati problémák a folyamatkiszolgáló és a forrásgép között.| **Fájl- és nyomtatómegosztás** nincs engedélyezve. | Lehetővé teszi **fájl- és nyomtatómegosztás** a Windows tűzfal a forrásgépen. A forrásgépen alatt **Windows tűzfal** > **lehetővé teszik az alkalmazás vagy szolgáltatás tűzfalán keresztül**válassza **fájl- és nyomtatómegosztás az összes profil számára**. </br> Emellett ellenőrizze az alábbiakat a leküldéses telepítés sikeres befejezéséhez.<br> Tudjon meg többet [WMI hibaelhárítási problémák](#troubleshoot-wmi-issues).


## <a name="error-95107---protection-could-not-be-enabled-ep0858"></a>95107-es - védelem nem sikerült engedélyezni a (ep0858 hiba)

**Hibakód:** | **Lehetséges okok** | **Hiba vonatkozó javaslatok**
--- | --- | ---
95107 </br>**Üzenet:** forrásgépen a mobilitási szolgáltatás leküldéses telepítéséhez hibakóddal sikertelenül **EP0858**. <br> Mobilitási szolgáltatás telepítése a megadott hitelesítő adatok helytelenek, vagy a felhasználói fiók nem rendelkezik megfelelő jogosultsággal. | A forrásgépen a mobilitási szolgáltatás telepítése a megadott felhasználói hitelesítő adatok helytelenek. | Győződjön meg arról, hogy a forrásgép a konfigurációs kiszolgálón megadott felhasználói hitelesítő adatok helyesek-e. <br> Hozzáadásához, vagy felhasználói hitelesítő adatok szerkesztése, nyissa meg a konfigurációs kiszolgálót, és válassza ki **Cspsconfigtool** > **fiók kezelése**. </br> Emellett ellenőrizze a következőket [Előfeltételek](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) az ügyfélleküldéses telepítés sikeres befejezéséhez.


## <a name="error-95117---protection-could-not-be-enabled-ep0865"></a>95117-es - védelem nem sikerült engedélyezni a (ep0865 hiba)

**Hibakód:** | **Lehetséges okok** | **Hiba vonatkozó javaslatok**
--- | --- | ---
95117 </br>**Üzenet:** forrásgépen a mobilitási szolgáltatás leküldéses telepítéséhez hibakóddal sikertelenül **EP0865**. <br> Nincs bekapcsolva a forrásgép, vagy hálózati kapcsolati problémák a folyamatkiszolgáló és a forrásgép között. | A hálózati kapcsolati problémák a folyamatkiszolgáló és a forráskiszolgáló között. | Ellenőrizze a kapcsolatot a folyamatkiszolgáló és a forráskiszolgáló között. </br> Emellett ellenőrizze a következőket [Előfeltételek](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) az ügyfélleküldéses telepítés sikeres befejezéséhez.|

## <a name="error-95103---protection-could-not-be-enabled-ep0854"></a>95103-as - védelem nem sikerült engedélyezni a (ep0854 hiba)

**Hibakód:** | **Lehetséges okok** | **Hiba vonatkozó javaslatok**
--- | --- | ---
95103 </br>**Üzenet:** forrásgépen a mobilitási szolgáltatás leküldéses telepítéséhez hibakóddal sikertelenül **EP0854**. <br> Windows Management Instrumentation (WMI) nincs engedélyezve a forrásgépen, vagy hálózati kapcsolati problémák a folyamatkiszolgáló és a forrásgép között.| A WMI le van tiltva, a Windows tűzfal. | A WMI Windows tűzfal engedélyezése. A **Windows tűzfal** > **lehetővé teszik az alkalmazás vagy szolgáltatás tűzfalán keresztül**válassza **WMI az összes profil számára**. </br> Emellett ellenőrizze a következőket [Előfeltételek](vmware-azure-install-mobility-service.md#install-mobility-service-by-push-installation-from-azure-site-recovery) az ügyfélleküldéses telepítés sikeres befejezéséhez.|

## <a name="error-95213---protection-could-not-be-enabled-ep0874"></a>95213-as - védelem nem sikerült engedélyezni a (ep0874 hiba)

**Hibakód:** | **Lehetséges okok** | **Hiba vonatkozó javaslatok**
--- | --- | ---
95213 </br>**Üzenet:** a mobilitási szolgáltatást a(z) % SourceIP; telepítése nem sikerült hibakód **EP0874**. <br> | Az operációs rendszer verzióját a forrásgépen nem támogatott. <br>| Győződjön meg arról, hogy a forrásgép operációsrendszer-verzió támogatott. Olvassa el a [támogatási mátrix](https://aka.ms/asr-os-support). </br> Emellett ellenőrizze a következőket [Előfeltételek](https://aka.ms/pushinstallerror) az ügyfélleküldéses telepítés sikeres befejezéséhez.| 


## <a name="error-95108---protection-could-not-be-enabled-ep0859"></a>95108-as - védelem nem sikerült engedélyezni a (ep0859 hiba)

**Hibakód:** | **Lehetséges okok** | **Hiba vonatkozó javaslatok**
--- | --- | ---
95108 </br>**Üzenet:** forrásgépen a mobilitási szolgáltatás leküldéses telepítéséhez hibakóddal sikertelenül **EP0859**. <br>| Mobilitási szolgáltatás telepítése a megadott hitelesítő adatok helytelenek, vagy a felhasználói fiók nem rendelkezik megfelelő jogosultsággal. <br>| Ellenőrizze, hogy vannak-e a megadott hitelesítő adatok a **legfelső szintű** fiók hitelesítő adataival. Adja hozzá, vagy felhasználói hitelesítő adatok szerkesztése, nyissa meg a konfigurációs kiszolgálót, és válassza a **Cspsconfigtool** parancsikonját az asztalon. Válassza ki **fiók kezelése** hozzáadását vagy szerkesztését a hitelesítő adatokat.|

## <a name="error-95265---protection-could-not-be-enabled-ep0902"></a>95265-ös - védelem nem sikerült engedélyezni a (ep0902 hiba)

**Hibakód:** | **Lehetséges okok** | **Hiba vonatkozó javaslatok**
--- | --- | ---
95265 </br>**Üzenet:** sikeres volt a forrásgépen, de a forrásgépen a mobilitási szolgáltatás leküldéses telepítéséhez a rendszer néhány módosításának érvénybe léptetéséhez újraindítás szükséges. <br>| A mobilitási szolgáltatás régebbi verziója már telepítve lett a kiszolgálón.| A virtuális gép replikációja zökkenőmentesen folytatódik.<br> A mobilitási szolgáltatás új funkcióinak kihasználása érdekében a következő karbantartási időszakban indítsa újra a kiszolgálót.|


## <a name="error-95224---protection-could-not-be-enabled-ep0883"></a>95224-es - védelem nem sikerült engedélyezni a (ep0883 hiba)

**Hibakód:** | **Lehetséges okok** | **Hiba vonatkozó javaslatok**
--- | --- | ---
95224 </br>**Üzenet:** leküldéses telepítése a mobilitási szolgáltatást a(z) % SourceIP; hibakóddal sikertelenül **EP0883**. Egy előző telepítés vagy frissítés a rendszer újraindítása függőben.| A rendszer nem lett újraindítása, ha egy régebbi vagy nem kompatibilis a mobilitási szolgáltatás verziójának eltávolítása.| Győződjön meg arról, hogy a mobilitási szolgáltatás változata nem létezik a kiszolgálón. <br> Indítsa újra a kiszolgálót, majd futtassa újra a védelemengedélyezési feladatot.|

## <a name="resource-to-troubleshoot-push-installation-problems"></a>Erőforrás leküldéses telepítési problémák elhárítása

#### <a name="troubleshoot-file-and-print-sharing-issues"></a>Hibaelhárítás – fájl és megosztási problémák nyomtatása
* [Engedélyezheti vagy tilthatja le a csoportházirend-fájlmegosztás](https://technet.microsoft.com/library/cc754359(v=ws.10).aspx)
* [Fájl- és nyomtatómegosztás keresztül a Windows tűzfal engedélyezése](https://technet.microsoft.com/library/ff633412(v=ws.10).aspx)

#### <a name="troubleshoot-wmi-issues"></a>A WMI-problémák hibaelhárítása
* [Alapszintű WMI tesztelése](https://blogs.technet.microsoft.com/askperf/2007/06/22/basic-wmi-testing/)
* [A WMI-hibaelhárítás](https://msdn.microsoft.com/library/aa394603(v=vs.85).aspx)
* [WMI-parancsfájlok és a WMI-szolgáltatásokkal kapcsolatos problémák](https://technet.microsoft.com/library/ff406382.aspx#H22)

## <a name="next-steps"></a>További lépések

[Ismerje meg, hogyan](vmware-azure-tutorial.md) való vészhelyreállítás beállítása VMware virtuális gépekhez.