---
title: Azure AD Connect Health Version History (Az Azure AD Connect Health verzióelőzményei)
description: Ez a dokumentum ismerteti a kiadások, az Azure AD Connect Health és milyen megtalálható a ezeket a kiadásokat.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/20/2019
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46e70850ba9e5984e36643f1b9ecc9db29eec149
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294535"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: Verziókiadások előzményei
Az Azure Active Directory-csapat rendszeresen frissíti az Azure AD Connect Health új szolgáltatásait és funkcióit. Ez a cikk felsorolja és kiadott funkciókról.  

> [!NOTE]
> Connect Health-ügynök automatikusan frissül, amikor új verziója. Ellenőrizze, hogy az automatikus frissítési beállítások engedélyezve van az Azure Portalról. 
>

Az Azure AD Connect Health szinkronizálási szolgáltatás integrálva van az Azure AD Connect-telepítésre. Tudjon meg többet [az Azure AD Connect verziókiadások előzményei](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history) szavazhat a visszajelzés funkciót, [Connect Health User Voice-csatorna](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)

## <a name="march-2019"></a>2019. március
**Ügynök frissítése:** 
* Az Azure AD Connect Health agent for AD DS (3.1.41.0 verzió) 
* .NET-verzió gyűjtemény.
* Ha hiányzik az adott kategóriába tartozó gyűjtése javítása.
* Hibajavítás a Monitoring Agent több példány származtatását megelőzése.

* Az Azure AD Connect Health agent for AD FS (3.1.41.0 verzió) 
* Integrálja, és frissítse az AD FS test parancsfájlok ADFSToolBox használatával.
* .NET-verzió gyűjtemény.
* Ha hiányzik az adott kategóriába tartozó gyűjtése javítása.
* Hibajavítás a Monitoring Agent több példány származtatását megelőzése.


## <a name="november-2018"></a>November 2018
**Általánosan elérhető új funkciók:** 
* Az Azure AD Connect Health for Sync - diagnosztizálásához és elhárításához a duplikált attribútummal szinkronizálási hibák a portálról

**Ügynök frissítése:** 
* Az Azure AD Connect Health agent for AD DS (3.1.24.0 verzió) 
* Transport Layer Security (TLS) protokoll 1.2-es verziójában megfelelőségi és végrehajtás
* Globális katalógus riasztási zaj csökkentésére
* Health ügynök regisztrációs hibajavítások

* Az Azure AD Connect Health agent for AD FS (3.1.24.0 verzió)
* Transport Layer Security (TLS) protokoll 1.2-es verziójában megfelelőségi és végrehajtás
* A Test-ADFSRequestToken honosított operációs rendszer támogatása
* Diagnosztikai ügynök EventHandler zárolási problémát megoldani.
* Health ügynök regisztrációs hibajavítások

## <a name="august-2018"></a>2018. augusztus 
*  Az Azure AD Connect Health agent for Sync (3.1.7.0 verzió), amely az Azure AD Connect verziója 1.1.880.0    
   1. A gyorsjavítás [figyelés .NET Framework KB-os-ügynök kiadása magas CPU-kiadások](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>2018. június 
**Új előzetes verziójú funkciók:** 
* Az Azure AD Connect Health for Sync - diagnosztizálásához és elhárításához a duplikált attribútummal szinkronizálási hibák a portálról 

**Ügynök frissítése:** 
* Az Azure AD Connect Health agent for AD DS (3.1.7.0 verzió)    
  1. A gyorsjavítás [figyelés .NET Framework KB-os-ügynök kiadása magas CPU-kiadások](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
* Az Azure AD Connect Health agent for AD FS (3.1.7.0 verzió)  
  1. A gyorsjavítás [figyelés .NET Framework KB-os-ügynök kiadása magas CPU-kiadások](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
  2. A vizsgálati eredmények javításokat másodlagos Server 2016 AD FS-kiszolgálón
   
* Az Azure AD Connect Health agent for AD FS (3.1.2.0 verzió)  
  1. Ügynökfelügyeleti memória és a kapcsolódó riasztások verziójának 3.0.244.0 gyorsjavítás


## <a name="may-2018"></a>2018. május
**Ügynök frissítése:**
* Az Azure AD Connect Health agent for AD DS (3.0.244.0 verzió)
  1. Az ügynök adatvédelmi fokozása  
  2. Hibajavításokat tartalmaz, és általános fejlesztései

* Az Azure AD Connect Health agent for AD FS (3.0.244.0 verzió)
  1. Az ügynök diagnosztikai szolgáltatás és a kapcsolódó PowerShell-modul fejlesztései
  2. Az ügynök adatvédelmi fokozása  
  3. Hibajavításokat tartalmaz, és általános fejlesztései

* Az Azure AD Connect Health agent for Sync (3.0.164.0 verzió), amely az Azure AD Connect verziója 1.1.819.0 
  1. Az ügynök adatvédelmi fokozása  
  2. Hibajavításokat tartalmaz, és általános fejlesztései


## <a name="march-2018"></a>2018. március
**Új előzetes verziójú funkciók:**
* Az Azure AD Connect Health for AD FS – kockázatos IP jelentés és a riasztás.

**Ügynök frissítése:**

* Az Azure AD Connect Health agent for AD DS (3.0.176.0 verzió)
  1. Az ügynök rendelkezésre állással kapcsolatos fejlesztések 
  2. Hibajavításokat tartalmaz, és általános fejlesztései
* Az Azure AD Connect Health agent for AD FS (3.0.176.0 verzió)
  1. Az ügynök rendelkezésre állással kapcsolatos fejlesztések 
  2. Hibajavításokat tartalmaz, és általános fejlesztései
* Az Azure AD Connect Health agent for Sync (3.0.129.0 verzió), amely az Azure AD Connect verziója 1.1.750.0  
  1. Az ügynök rendelkezésre állással kapcsolatos fejlesztések 
  2. Hibajavításokat tartalmaz, és általános fejlesztései

## <a name="december-2017"></a>2017. december
**Ügynök frissítése:**

* Az Azure AD Connect Health agent for AD DS (3.0.145.0 verzió)
  1. Az ügynök rendelkezésre állással kapcsolatos fejlesztések 
  2. Új ügynök hibaelhárítási parancsok hozzáadva
  3. Hibajavításokat tartalmaz, és általános fejlesztései
* Az Azure AD Connect Health agent for AD FS (3.0.145.0 verzió)
  1. Új ügynök hibaelhárítási parancsok hozzáadva
  2. Az ügynök rendelkezésre állással kapcsolatos fejlesztések 
  3. Hibajavításokat tartalmaz, és általános fejlesztései
  
## <a name="october-2017"></a>2017. október
**Ügynök frissítése:**

 * Az Azure AD Connect Health agent for Sync (3.0.129.0 verzió), amely az Azure AD Connect verziója 1.1.649.0
<br></br> Az Azure AD Connect és az Azure AD Connect Health Agent for Sync közötti verziójú kompatibilitási probléma kijavítva. A probléma hatással van az ügyfelek, akik az Azure AD Connect helyben frissítés 1.1.647.0 verzióra végez, de jelenleg a Health-ügynök verziója 3.0.127.0 rendelkezik. A frissítés után a Health Agent többé nem küldhet állapot adatait az Azure AD Connect szinkronizálási szolgáltatás az Azure AD Health szolgáltatásba. A javítás verzió 3.0.129.0 Health-ügynök telepítve van az Azure AD Connect helyben frissítés során. Rendszerállapot-ügynök verziója 3.0.129.0 nincs kompatibilitási probléma az Azure AD Connect verziója 1.1.649.0.

## <a name="july-2017"></a>2017. július
**Ügynök frissítése:**

* Az Azure AD Connect Health agent for AD DS (3.0.68.0 verzió)
  1. Hibajavításokat tartalmaz, és általános fejlesztései
  2. Szuverén felhő támogatása
* Az Azure AD Connect Health agent for AD FS (3.0.68.0 verzió)
  1. Hibajavításokat tartalmaz, és általános fejlesztései
  2. Szuverén felhő támogatása
* Az Azure AD Connect Health agent for Sync (3.0.68.0 verzió), amely az Azure AD Connect verziója 1.1.614.0
  1. A Microsoft Azure Government felhőben és a Microsoft Cloud németországi adatközpontjában támogatása

## <a name="april-2017"></a>2017. április      
**Ügynök frissítése:**

* Az Azure AD Connect Health agent for AD FS (3.0.12.0 verzió)
  1. Hibajavításokat tartalmaz, és általános fejlesztései
* Az Azure AD Connect Health agent for AD DS (3.0.12.0 verzió)
  1. Teljesítményszámlálók feltöltése fejlesztései
  2. Hibajavításokat tartalmaz, és általános fejlesztései

## <a name="october-2016"></a>2016. október
**Ügynök frissítése:**

* Az Azure AD Connect Health agent for AD FS (2.6.408.0 verzió)
* A hitelesítési kérések az ügyfél IP-címek észlelése fejlesztései
* Riasztásokkal kapcsolatos hibajavítások
* Az Azure AD Connect Health agent for AD DS (2.6.408.0 verzió)
* Riasztásokkal kapcsolatos hibajavítások.
* Szinkronizálási szolgáltatás (verzió 2.6.353.0) az Azure AD Connect 1.1.281.0 verziója, amely az Azure AD Connect Health-ügynök
* Adja meg a szükséges adatokat a szinkronizálási hibajelentések
* Riasztásokkal kapcsolatos hibajavítások

**Új előzetes verziójú funkciók:**

* Szinkronizálási hiba jelentések az Azure ad Connect

**Új funkciók:**

* Az Azure AD Connect Health for AD FS - IP-cím mezőt a jelentés felső 50 felhasználó rossz felhasználónévvel/jelszóval kapcsolatos érhető el.

## <a name="july-2016"></a>2016. július
**Új előzetes verziójú funkciók:**

* [Az Azure AD Connect Health for AD DS](how-to-connect-health-adds.md).

## <a name="january-2016"></a>2016. január
**Ügynök frissítése:**

* Az Azure AD Connect Health agent for AD FS (2.6.91.1512 verzió)

**Új funkciók:**

* [Teszt kapcsolódási eszköz az Azure ad Connect Health-ügynökök](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>2015. november
**Új funkciók:**

* Támogatja a [szerepköralapú hozzáférés-vezérlés](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)

**Új előzetes verziójú funkciók:**

* [Az Azure AD Connect Health szinkronizálási szolgáltatás](how-to-connect-health-sync.md).

**Rögzített problémák:**

* Hibajavítások a hibák ügynök regisztrációk alatt látható.

## <a name="september-2015"></a>2015. szeptember
**Új funkciók:**

* Helytelen a felhasználónév-jelszó jelentés az AD FS-hez
* Támogatja a nem hitelesített HTTP-Proxy konfigurálása
* Támogatja az ügynök konfigurálása Server core-on
* Riasztások az AD FS fejlesztései
* Töltse fel a kapcsolat és az AD FS az Azure AD Connect Health-ügynök javítását.

**Rögzített problémák:**

* Hibák javítása a használati adatokat az AD FS alkalmazáshiba-típusok.

## <a name="june-2015"></a>2015. június
**Megjelenése az Azure AD Connect Health for AD FS és az AD FS Proxy.**

**Új funkciók:**

* Riasztások az e-mail-értesítések az AD FS és az AD FS-Proxy kiszolgálók monitorozásához.
* Az AD FS-topológia és minták a az AD FS teljesítményszámlálói könnyen elérhetők.
* Trend a sikeres jogkivonat-kérelmeket az AD FS-kiszolgálók, alkalmazások, a hitelesítési módszereket, a kérelmek hálózati helye stb szerint csoportosítva.
* Az AD FS-kiszolgálók, alkalmazások, hiba típusok stb szerint csoportosítva sikertelen kérelmek trendjei.
* Egyszerűbb ügynök telepítése az Azure AD globális rendszergazdai hitelesítő adatokkal.  

## <a name="next-steps"></a>További lépések
Tudjon meg többet [figyelni a helyszíni identitás infrastruktúra és a szinkronizálási szolgáltatások a felhőben](whatis-hybrid-identity-health.md).

