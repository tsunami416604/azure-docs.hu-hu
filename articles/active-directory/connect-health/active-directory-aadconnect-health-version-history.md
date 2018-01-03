---
title: "Azure AD Connect Health Version History (Az Azure AD Connect Health verzióelőzményei)"
description: "Ez a dokumentum ismerteti a kiadások, az Azure AD Connect Health és mi ezeket a kiadásokat szerepel."
services: active-directory
documentationcenter: 
author: karavar
manager: mtillman
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: d89e51e61b52e7337b8faf28f42944ec29f1b94f
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: Version History (Az Azure AD Connect Health verzióelőzményei)
Az Azure Active Directory ügyfélszolgálata rendszeresen frissíti az Azure AD Connect Health új szolgáltatásait és funkcióit. Ez a cikk felsorolja a kiadott szolgáltatások és verziókat.

## <a name="december-2017"></a>2017. december
**Ügynök frissítése:**

*   Az Azure AD Connect Health-ügynök az AD DS (3.0.145.0 verzió)
  1. Ügynök rendelkezésre állással kapcsolatos fejlesztések 
  2. A hozzáadott új ügynök hibaelhárítási parancsok
  3. Hibajavításokat tartalmaz, és általános fejlesztések
*   Az Azure AD Connect Health-ügynök az AD FS (3.0.145.0 verzió)
  1. A hozzáadott új ügynök hibaelhárítási parancsok
  2. Ügynök rendelkezésre állással kapcsolatos fejlesztések 
  3. Hibajavításokat tartalmaz, és általános fejlesztések

## <a name="july-2017"></a>2017. július
**Ügynök frissítése:**

*   Az Azure AD Connect Health-ügynök az AD DS (3.0.68.0 verzió)
  1. Hibajavításokat tartalmaz, és általános fejlesztések
  2. Szuverén felhő támogatása
*   Az Azure AD Connect Health-ügynök az AD FS (3.0.68.0 verzió)
  1. Hibajavításokat tartalmaz, és általános fejlesztések
  2. Szuverén felhő támogatása

## <a name="april-2017"></a>2017. április      
**Ügynök frissítése:**

*   Az Azure AD Connect Health-ügynök az AD FS (3.0.12.0 verzió)
  1. Hibajavításokat tartalmaz, és általános fejlesztések
*   Az Azure AD Connect Health-ügynök az AD DS (3.0.12.0 verzió)
  1. Teljesítményszámlálók feltöltése fejlesztései
  2. Hibajavításokat tartalmaz, és általános fejlesztések

## <a name="october-2016"></a>2016. október
**Ügynök frissítése:**

* Az Azure AD Connect Health-ügynök az AD FS \(2.6.408.0 verziója\)
  1. A hitelesítési kérések ügyfél IP-címek kimutatására fejlesztései
  2. Hibajavítások kapcsolatos riasztások
* Az Azure AD Connect Health-ügynök az AD DS (2.6.408.0 verzió)
  1. Riasztások kapcsolatos hibajavítások.
* Az Azure AD Connect Health-ügynök (verzió: 2.6.353.0)-szinkronizáláshoz az Azure AD Connect 1.1.281.0 verziója, amely a
  1. A szinkronizálás hibajelentések adja meg a szükséges adatokat.
  2. Riasztások kapcsolatos hibajavítások

**Új előzetes verziójú funkciók:**

* Szinkronizálási hiba jelentések az Azure AD Connect

**Új szolgáltatások:**

* Az Azure AD Connect Health AD FS - IP-cím mező áll rendelkezésre a rossz felhasználónév/jelszó próbálkozó 50 felhasználó jelentést.

## <a name="july-2016"></a>2016. július
**Új előzetes verziójú funkciók:**

* [Az Azure AD Connect Health AD DS](active-directory-aadconnect-health-adds.md).

## <a name="january-2016"></a>2016. január
**Ügynök frissítése:**

* Az Azure AD Connect Health-ügynök az AD FS (2.6.91.1512 verzió)

**Új szolgáltatások:**

* [Teszt kapcsolódási eszköz az Azure AD Connect Health-ügynökök](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>2015. november
**Új szolgáltatások:**

* Támogatja az [szerepköralapú hozzáférés-vezérlés](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)

**Új előzetes verziójú funkciók:**

* [Az Azure AD Connect Health szinkronizálási szolgáltatás](active-directory-aadconnect-health-sync.md).

**Javított problémák:**

* Hibaelhárítás a ügynök regisztráció során tapasztalt hibákat.

## <a name="september-2015"></a>2015. szeptember
**Új szolgáltatások:**

* Helytelen a felhasználónév-jelszó jelentés az AD FS Szolgáltatásokhoz
* Támogatja a nem hitelesített HTTP-Proxy konfigurálása
* Ügynök konfigurálása Server core-on a támogatási szolgálathoz
* Az AD FS riasztások fejlesztései
* Töltse fel az AD FS Szolgáltatásokhoz a kapcsolat és az Azure AD Connect Health-ügynök javítását.

**Javított problémák:**

* Hibaelhárítás a használati adatok feltárása az AD FS típusok.

## <a name="june-2015"></a>2015. június
**Eredeti kiadását az Azure AD Connect Health AD FS és az AD FS Proxy.**

**Új szolgáltatások:**

* Riasztások értesítő e-mailek az AD FS és az AD FS-Proxy kiszolgálók figyelésére.
* Az AD FS topológia és AD FS teljesítményszámlálók minták könnyen elérhetők.
* Trend a sikeres jogkivonat-kérelmeket az alkalmazásokat, a hitelesítési módszereket, a kérelmek hálózati helye stb szerint csoportosítva AD FS-kiszolgáló.
* Alkalmazások, hiba típusok stb szerint csoportosítva AD FS-kiszolgáló a sikertelen kérelmek trendeket.
* Egyszerűbb ügynök központi telepítése az Azure AD globális rendszergazda hitelesítő adataival.  

## <a name="next-steps"></a>További lépések
További információ [a helyszíni identitás infrastruktúra és a szinkronizálási szolgáltatások megfigyelése a felhőben](active-directory-aadconnect-health.md).

