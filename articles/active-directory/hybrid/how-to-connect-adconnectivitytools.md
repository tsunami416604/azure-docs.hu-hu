---
title: 'Azure AD Connect: Mi az a PowerShell-modul ADConnectivityTool |} A Microsoft Docs'
description: Ez a dokumentum bemutatja az új ADConnectivity PowerShell-modul
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b2471b28132b26c41844b620f6dcf49e3802a6e
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58436710"
---
# <a name="what-is-the-adconnectivitytool-powershell-module"></a>Mi az a ADConnectivityTool PowerShell-modult?

A ADConnectivity eszköz el egy PowerShell-modul, amely szerepel az alábbi lehetőségek közül:

- Telepítéskor megakadályozza a sikeres érvényesítésének feltétele lesz az Active Directory hitelesítő adatok a felhasználó a varázslóban megadott hálózati csatlakozási probléma esetén.
- Meghívja a függvényt, egy PowerShell-munkamenetet a felhasználó által a telepítés után.

Az eszköz található: **C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool.psm1** 

## <a name="adconnectivitytool-during-installation"></a>A telepítés során ADConnectivityTool

Az a **csatlakoztassa a címtárakat** lapon, az Azure AD Connect varázsló, ha hálózati hiba történik, a ADConnectivityTool lesz automatikusan használatával a függvények egyikét határozza meg, mi is történik.  Az alábbi tekinthető hálózati problémák:

- Rosszul írta be a nevét, az erdő, a felhasználó által megadott vagy említett erdő nem létezik. 
- 389-es UDP-port le van zárva, a tartományvezérlők, az erdőben, a felhasználó által megadott társított
- Az "AD-erdőfiók" ablakban megadott hitelesítő adatok nem rendelkezik jogosultságokkal a tartományvezérlők, a cél erdő társított beolvasása
- Bármely 53-as, 88 vagy 389-es TCP-port zárva van a tartományvezérlők, az erdőben, a felhasználó által megadott társított a 
- Mind a 389-es UDP és TCP-port (vagy -portokat) nincs megnyitva
- Nem sikerült a DNS a társított tartományvezérlők számára a megadott erdő kijelöljön Megoldva

Találhatók esetleges ezeket a problémákat, amikor egy kapcsolódó hibaüzenet jelenik meg az aad Connect varázslóban:


![Hiba](media/how-to-connect-adconnectivitytools/error1.png)

Például ha helyreállításon dolgozunk a könyvtár hozzáadása a **csatlakoztassa a címtárakat** képernyő, az Azure AD Connect kell ellenőriznie ezt, és vár a tartományvezérlővel 389-es porton keresztül kommunikálnak.  Ha nem érhető el, hogy a fenti képernyőképen látható hibaüzenet jelenik meg.  

Mi ténylegesen történik a színfalak mögött, hogy az Azure AD Connect hívja a `Start-NetworkConnectivityDiagnosisTools` függvény.  Ez a függvény nevezzük, amikor hitelesítő adatok érvényesítése hálózati kapcsolati hiba miatt sikertelen.

Végül a részletes naplófájl jön létre, amikor az eszköz neve a varázslóból. A naplóban található **C:\ProgramData\AADConnect\ADConnectivityTool-\<dátum >-\<idő > .log**

## <a name="adconnectivitytools-post-installation"></a>A telepítés után ADConnectivityTools
Az Azure AD Connect telepítése után a ADConnectivityTools PowerShell-modul az a funkciók bármelyike használható.  

Az a funkciók részletes információkat talál a [ADConnectivityTools referencia](reference-connect-adconnectivitytools.md)

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

Ez a függvény hívásához, mert képes kezelni fogjuk **csak** a ADConnectivityTool.psm1 PowerShell alkalmazásba importálása után manuálisan hívható. 

Ez a függvény végrehajtja ugyanazt a logikát, amely az Azure AD Connect varázsló a megadott AD hitelesítő adatok ellenőrzése.  Azonban sokkal több részletes magyarázatát a problémát, és a egy javasolt megoldást biztosít. 

A kapcsolat érvényesítése a következő lépésekből áll:
-   Tartomány teljes Tartományneve (teljesen minősített tartománynév) objektum
-   Ellenőrizze, hogy a felhasználó kiválasztott a "Létrehozás új AD-fiók", ha ezeket a hitelesítő adatokat tartozik a vállalati rendszergazdák csoport
-   Get-erdő teljes Tartományneve objektum
-   Győződjön meg arról, hogy legalább egy tartományt a korábban kapott erdő FQDN-objektumhoz társított érhető el
-   Ellenőrizze, hogy az erdő működési szintje Windows Server 2003 vagy újabb.

A felhasználó fogja tudni adjon hozzá egy könyvtárat, ha ezek a műveletek végrehajtása sikerült.

Ha a felhasználó ezt a funkciót futtat, miután egy zárhatók (vagy ha nincs probléma egyáltalán létezik) a kimenetben megjelenik a felhasználó, lépjen vissza az Azure AD Connect varázsló, és próbálja meg újra szúr be a hitelesítő adatokat.



## <a name="next-steps"></a>További lépések
- [Azure AD Connect: Fiókok és engedélyek](reference-connect-accounts-permissions.md)
- [Az Expressz telepítés](how-to-connect-install-express.md)
- [Egyéni telepítés](how-to-connect-install-custom.md)
- [ADConnectivityTools referencia](reference-connect-adconnectivitytools.md)

