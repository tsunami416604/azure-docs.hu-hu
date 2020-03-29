---
title: Azure AD Connect Health Version History (Az Azure AD Connect Health verzióelőzményei)
description: Ez a dokumentum ismerteti az Azure AD Connect Health kiadásait, és hogy mi szerepel a kiadásokban.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/20/2019
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ace8d170a5ba48c00775c3b376df8bb70a337d5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897033"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: Version History (Az Azure AD Connect Health verzióelőzményei)
Az Azure Active Directory csapata rendszeresen frissíti az Azure AD Connect Health új funkciókkal és funkciókkal. Ez a cikk a kiadott verziókat és szolgáltatásokat sorolja fel.  

> [!NOTE]
> A Connect Health ügynökök automatikusan frissülnek az új verzió megjelenésekor. Ellenőrizze, hogy az automatikus frissítési beállítások engedélyezve vannak-e az Azure Portalon.
>

Az Azure AD Connect Health for Sync integrálva van az Azure AD Connect telepítésével. További információ az [Azure AD Connect kiadási előzményeiről](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history) A funkciók visszajelzései ért, szavazzon a [Connect Health User Voice csatornára](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)

## <a name="july-2019"></a>2019. július
**Ügynök frissítése**
* Azure AD Connect health ügynök az AD FS-hez (3.1.59.0-s verzió) 
   1. Szövegmódosítás a TestWindowsTransport szolgáltatásban
   2. Változások az AD FS RP feltöltéséhez
   
* Azure AD Connect health ügynök az AD FS-hez (3.1.56.0-s verzió) 
   1. TestWindowsTransport teszt hozzáadása és wstrust végpont-ellenőrzések eltávolítása a CheckOffice365Endpoints tesztben
   2. Az operációs rendszer és a .NET adatainak naplózása
   3. Növelje rp konfigurációs üzenet feltöltési mérete 1MB.
   4. Hibajavítások
   
* Azure AD Connect health ügynök az AD DS-hez (3.1.56.0-s verzió) 
   1. Az operációs rendszer és a .NET adatainak naplózása 
   2. Hibajavítások

## <a name="may-2019"></a>2019. május
**Ügynök frissítése:** 
* Azure AD Connect health ügynök az AD FS-hez (3.1.51.0-s verzió) 
   1. Hibajavítás az azonos ügyfél-kérelemazonosítóval rendelkező többszörös bejelentkezések megkülönböztetésére.
   2. Hibajavítás a hibás felhasználónév/jelszó hibák elemzéséhez a nyelvi honosított kiszolgálókon.   

## <a name="april-2019"></a>2019. április
**Ügynök frissítése:** 
* Azure AD Connect health ügynök az AD FS-hez (3.1.46.0-s verzió) 
   1. Az Ismétlődő SPN-riasztási folyamat javítása az ADFS-hez

## <a name="march-2019"></a>2019. március
**Ügynök frissítése:** 
* Azure AD Connect health ügynök az AD DS-hez (3.1.41.0-s verzió)  
   1. .NET verziógyűjtemény
   2. A teljesítményszámlálók gyűjtésének javítása bizonyos kategóriák hiánya esetén
   3. Hibajavítás több Felügyeleti ügynök példány ívásának megakadályozásáról

* Azure AD Connect health ügynök az AD FS-hez (3.1.41.0-s verzió) 
   1. Az AD FS tesztparancsfájlok integrálása és frissítése az ADFSToolBox segítségével
   2. .NET verziógyűjtemény megvalósítása
   3. A teljesítményszámlálók gyűjtésének javítása bizonyos kategóriák hiánya esetén
   4. Hibajavítás több Felügyeleti ügynök példány ívásának megakadályozásáról


## <a name="november-2018"></a>2018. november
**Új GA funkciók:** 
* Azure AD Connect health for Sync – Duplikált attribútumszinkronizálási hibák diagnosztizálása és javítása a portálról

**Ügynök frissítése:** 
* Azure AD Connect health ügynök az AD DS-hez (3.1.24.0-s verzió) 
   1. Transport Layer Security (TLS) protokoll 1.2-es verziójú megfelelőség és végrehajtás
   2. A globális katalógus riasztási zajának csökkentése
   3. Az egészségügyi ügynökök regisztrálásának hibajavításai

* Azure AD Connect health ügynök az AD FS-hez (3.1.24.0-s verzió)  
   1. Transport Layer Security (TLS) protokoll 1.2-es verziójú megfelelőség és végrehajtás
   2. A Test-ADFSRequestToken támogatása honosított operációs rendszerhez
   3. Megoldott diagnosztikai ügynök EventHandler zárolási probléma
   4. Az egészségügyi ügynökök regisztrálásának hibajavításai

## <a name="august-2018"></a>2018. augusztus 
*  Az Azure AD Connect health ügynöke a Szinkronizáláshoz (3.1.7.0-s verzió) az Azure AD Connect 1.1.880.0-s verziójával    
   1. Gyorsjavítás a [.](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>2018. június 
**Új előzetes verziós funkciók:** 
* Azure AD Connect health for Sync – Duplikált attribútumszinkronizálási hibák diagnosztizálása és javítása a portálról 

**Ügynök frissítése:** 
* Azure AD Connect health ügynök az AD DS-hez (3.1.7.0-s verzió)    
  1. Gyorsjavítás a [.](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
* Azure AD Connect health ügynök az AD FS-hez (3.1.7.0-s verzió)  
  1. Gyorsjavítás a [.](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
  2. Vizsgálati eredmények javítása az ADFS Server 2016 másodlagos kiszolgálóján
   
* Azure AD Connect health ügynök az AD FS-hez (3.1.2.0-s verzió)  
  1. Gyorsjavítás az ügynökmemória kezeléséhez és a kapcsolódó riasztásokhoz kifejezetten a 3.0.244.0-s verzióhoz


## <a name="may-2018"></a>2018. május
**Ügynök frissítése:**
* Azure AD Connect health ügynök az AD DS-hez (3.0.244.0-s verzió)
  1. Az ügynökök adatvédelmének javítása  
  2. Hibajavítások és általános fejlesztések

* Azure AD Connect health ügynök az AD FS-hez (3.0.244.0-s verzió)
  1. Az Ügynökdiagnosztikai szolgáltatás és a kapcsolódó PowerShell-modul fejlesztések
  2. Az ügynökök adatvédelmének javítása  
  3. Hibajavítások és általános fejlesztések

* Az Azure AD Connect 1.1.819.0-s verziójával kiadott Azure AD Connect health ügynök szinkronizáláshoz (3.0.164.0-s verzió) 
  1. Az ügynökök adatvédelmének javítása  
  2. Hibajavítások és általános fejlesztések


## <a name="march-2018"></a>2018. március
**Új előzetes verziós funkciók:**
* Azure AD Connect Health az AD FS – kockázatos IP-jelentés és riasztás.

**Ügynök frissítése:**

* Azure AD Connect health ügynök az AD DS-hez (3.0.176.0-s verzió)
  1. Ügynök rendelkezésre állásának fejlesztései 
  2. Hibajavítások és általános fejlesztések
* Azure AD Connect health ügynök az AD FS-hez (3.0.176.0-s verzió)
  1. Ügynök rendelkezésre állásának fejlesztései 
  2. Hibajavítások és általános fejlesztések
* Az Azure AD Connect 1.1.750.0-s verziójával kiadott Azure AD Connect health ügynök szinkronizáláshoz (3.0.129.0-s verzió)  
  1. Ügynök rendelkezésre állásának fejlesztései 
  2. Hibajavítások és általános fejlesztések

## <a name="december-2017"></a>2017. december
**Ügynök frissítése:**

* Azure AD Connect health ügynök az AD DS-hez (3.0.145.0-s verzió)
  1. Ügynök rendelkezésre állásának fejlesztései 
  2. Új ügynökhibaelhárítási parancsok hozzáadása
  3. Hibajavítások és általános fejlesztések
* Azure AD Connect health ügynök az AD FS-hez (3.0.145.0-s verzió)
  1. Új ügynökhibaelhárítási parancsok hozzáadása
  2. Ügynök rendelkezésre állásának fejlesztései 
  3. Hibajavítások és általános fejlesztések
  
## <a name="october-2017"></a>2017. október
**Ügynök frissítése:**

 * Az Azure AD Connect 1.1.649.0-s verziójával kiadott Azure AD Connect health ügynök szinkronizáláshoz (3.0.129.0-s verzió)
<br></br> Kijavítottuk az Azure AD Connect és az Azure AD Connect Health Agent for Sync közötti verziókompatibilitási problémát. Ez a probléma azokat az ügyfeleket érinti, akik az Azure AD Connect et az 1.1.647.0-s verzióra való helyben frissítik, de jelenleg a Health Agent 3.0.127.0-s verzióját használják. A frissítés után az állapotügynök már nem küldhet állapotadatokat az Azure AD Connect szinkronizálási szolgáltatásról az Azure AD Health Service szolgáltatásnak. Ezzel a javítással az Állapotügynök 3.0.129.0-s verziója települ az Azure AD Connect helybeni frissítése során. Az Health Agent 3.0.129.0-s verziója nem rendelkezik kompatibilitási problémával az Azure AD Connect 1.1.649.0-s verziójával.

## <a name="july-2017"></a>2017. július
**Ügynök frissítése:**

* Azure AD Connect health ügynök az AD DS-hez (3.0.68.0-s verzió)
  1. Hibajavítások és általános fejlesztések
  2. Szuverén felhőtámogatás
* Azure AD Connect health ügynök az AD FS-hez (3.0.68.0-s verzió)
  1. Hibajavítások és általános fejlesztések
  2. Szuverén felhőtámogatás
* Az Azure AD Connect health ügynöke a Szinkronizáláshoz (3.0.68.0-s verzió) az Azure AD Connect 1.1.614.0-s verziójával
  1. A Microsoft Azure Government Cloud és a Microsoft Cloud Germany támogatása

## <a name="april-2017"></a>2017. április      
**Ügynök frissítése:**

* Azure AD Connect health ügynök az AD FS-hez (3.0.12.0-s verzió)
  1. Hibajavítások és általános fejlesztések
* Azure AD Connect health ügynök az AD DS-hez (3.0.12.0-s verzió)
  1. A teljesítményszámlálók feltöltési fejlesztései
  2. Hibajavítások és általános fejlesztések

## <a name="october-2016"></a>2016. október
**Ügynök frissítése:**

* Azure AD Connect health ügynök az AD FS-hez (2.6.408.0-s verzió)
* Az ügyfél IP-címeinek észlelése a hitelesítési kérelmekben
* A figyelmeztetésekkel kapcsolatos hibajavítások
* Azure AD Connect health ügynök az AD DS-hez (2.6.408.0-s verzió)
* A riasztásokhoz kapcsolódó hibajavítások.
* Az Azure AD Connect 1.1.281.0-s verziójával kiadott Azure AD Connect health ügynök szinkronizáláshoz (2.6.353.0-s verzió)
* A szinkronizálási hibajelentésekhez szükséges adatok biztosítása
* A riasztásokhoz kapcsolódó hibajavítások

**Új előzetes verziós funkciók:**

* Szinkronizálási hibajelentések az Azure AD Connecthez

**Új funkciók:**

* Az Azure AD Connect Health for AD FS – IP-cím mező a jelentésben érhető el a legjobb 50 rossz felhasználónévvel/jelszóval rendelkező felhasználókról.

## <a name="july-2016"></a>2016. július
**Új előzetes verziós funkciók:**

* [Az Azure AD Connect állapota az AD DS-hez.](how-to-connect-health-adds.md)

## <a name="january-2016"></a>2016. január
**Ügynök frissítése:**

* Azure AD Connect health ügynök az AD FS-hez (2.6.91.1512-es verzió)

**Új funkciók:**

* [Az Azure AD Connect állapotügynökeinek tesztkapcsolati eszköze](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>2015. november
**Új funkciók:**

* [Szerepköralapú hozzáférés-vezérlés](how-to-connect-health-operations.md#manage-access-with-role-based-access-control) támogatása

**Új előzetes verziós funkciók:**

* [Az Azure AD Connect állapota a szinkronizáláshoz.](how-to-connect-health-sync.md)

**Javítottuk a problémákat:**

* Hibajavítások az ügynökregisztrációk során észlelt hibákra.

## <a name="september-2015"></a>2015. szeptember
**Új funkciók:**

* Hibás felhasználónévjelszó-jelentés az AD FS-hez
* A nem hitelesített HTTP-proxy konfigurálásának támogatása
* Az ügynök konfigurálásának támogatása a kiszolgálómagon
* Az AD FS riasztásainak fejlesztései
* Fejlesztések az Azure AD Connect health Agent for AD FS kapcsolatok és az adatok feltöltése.

**Javítottuk a problémákat:**

* Hibajavítások az AD FS-hibatípusok használati elemzési adataiban.

## <a name="june-2015"></a>2015. június
**Az Azure AD Connect Health kezdeti kiadása AD FS és AD FS proxyhoz.**

**Új funkciók:**

* Riasztások az AD FS és az AD FS proxykiszolgálók e-mailes értesítésekkel való figyelésére.
* Könnyű hozzáférés az AD FS topológiához és mintáihoz az AD FS teljesítményszámlálókban.
* A sikeres tokenkérelmek trendje az AD FS-kiszolgálókon alkalmazások, hitelesítési módszerek, hálózati hely kérése stb.
* Az AD FS-kiszolgálókon az alkalmazások, hibatípusok stb.
* Egyszerűbb ügynök telepítés az Azure AD globális rendszergazdai hitelesítő adataival.  

## <a name="next-steps"></a>További lépések
További információ [a helyszíni identitás-infrastruktúra figyeléséről és a szinkronizálási szolgáltatások felhőben című szolgáltatásáról.](whatis-hybrid-identity-health.md)

