---
title: 'Azure AD Connect: Mi az ADConnectivityTool PowerShell modul | Microsoft dokumentumok'
description: Ez a dokumentum bemutatja az új ADConnectivity PowerShell modult, és hogyan használható a hibaelhárításhoz.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 4/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5340cd8c802df4ffbe0207b5401d2fee4e207e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "64571115"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>Az Azure AD-kapcsolat – az ADConnectivityTool PowerShell modullal – problémamegoldás

Az ADConnectivity eszköz egy PowerShell-modul, amelyet a következők egyikében használnak:

- A telepítés során, amikor a hálózati kapcsolat problémája megakadályozza a varázslóban megadott Active Directory-hitelesítő adatok sikeres érvényesítését.
- A telepítést egy powershell-munkamenetből a függvényeket meghívó felhasználó által idoben történő telepítését követően.

Az eszköz a következő helyen található: **C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool.psm1** 

## <a name="adconnectivitytool-during-installation"></a>ADConnectivityTool telepítés közben

A **Könyvtárak csatlakoztatása** lap, az Azure AD Connect varázsló, ha hálózati probléma merül fel, az ADConnectivitytool automatikusan használja az egyik függvénye, hogy meghatározzák, mi folyik itt.  Az alábbiak bármelyike hálózati problémának tekinthető:

- A felhasználó által megadott erdő neve helytelenül lett beírva, vagy az erdő nem létezik. 
- A 389-es UDP-port a felhasználó által megadott erdőhöz társított tartományvezérlőkön van bezárva
- Az "AD erdőfiók" ablakban megadott hitelesítő adatok nem rendelkeznek jogosultságokkal a célerdőhöz társított tartományvezérlők lekéréséhez.
- Az 53-as, 88-as vagy 389-es TCP-portok bármelyike be van zárva a felhasználó által megadott erdőhöz társított tartományvezérlőkön. 
- Mind az UDP 389, mind a TCP-port (vagy portok) zárva vannak
- A DNS nem oldható fel a megadott erdőhöz és\vagy a hozzá tartozó tartományvezérlőkhöz

Ha a probléma bármelyike megtalálható, az AADConnect varázslóban egy kapcsolódó hibaüzenet jelenik meg:


![Hiba](media/how-to-connect-adconnectivitytools/error1.png)

Ha például megpróbálunk hozzáadni egy könyvtárat a **Könyvtárak csatlakoztatása** képernyőn, az Azure AD Connectnek ellenőriznie kell ezt, és elvárja, hogy képes legyen kommunikálni egy tartományvezérlővel a 389-es porton keresztül.  Ha nem, látni fogjuk a fenti képernyőképen látható hibát.  

Mi történik valójában a színfalak mögött, hogy `Start-NetworkConnectivityDiagnosisTools` az Azure AD Connect hívja a függvényt.  Ez a függvény akkor jön létre, ha a hitelesítő adatok érvényesítése hálózati kapcsolati probléma miatt sikertelen.

Végül egy részletes naplófájl jön létre, amikor az eszközt a varázslóból hívják meg. A napló a **C:\ProgramData\AADConnect\ADConnectivityTool\<mappában\<található,> időpont>.log**

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools telepítés után
Az Azure AD Connect telepítése után az ADConnectivityTools PowerShell-modul bármely függvénye használható.  

A függvényekkel kapcsolatos referenciainformációkat az [ADConnectivityTools Referencia](reference-connect-adconnectivitytools.md)

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

Ezt a funkciót fogjuk kihívni, mert **csak** akkor hívható meg manuálisan, ha az ADConnectivityTool.psm1 importálása megtörtént a PowerShellbe. 

Ez a függvény ugyanazt a logikát hajtja végre, mint az Azure AD Connect varázsló fut a megadott AD hitelesítő adatok érvényesítéséhez.  Azonban ez egy sokkal bőbeszédű magyarázatot a problémára, és a javasolt megoldás. 

A kapcsolat ellenőrzése a következő lépésekből áll:
-   Tartomány teljes tartománynév (teljesen minősített tartománynév) objektumának beolvasása
-   Annak ellenőrzése, hogy ha a felhasználó az "Új AD-fiók létrehozása" lehetőséget választotta, ezek a hitelesítő adatok a Vállalati rendszergazdák csoporthoz tartoznak
-   Erdő FQDN-objektumának beolvasása
-   Annak ellenőrzése, hogy a korábban beszerzett erdő fqdn-objektumához társított legalább egy tartomány elérhető-e
-   Ellenőrizze, hogy az erdő működési szintje Windows Server 2003 vagy nagyobb.

A felhasználó hozzáadhat egy könyvtárat, ha ezeket a műveleteket sikeresen végrehajtották.

Ha a felhasználó a probléma megoldása után futtatja ezt a funkciót (vagy ha egyáltalán nem probléma áll fenn), a kimenet jelzi, hogy a felhasználó visszatérjen az Azure AD Connect varázslóhoz, és próbálja meg újra beszúrni a hitelesítő adatokat.



## <a name="next-steps"></a>Következő lépések
- [Azure AD Connect: fiókok és engedélyek](reference-connect-accounts-permissions.md)
- [Expressz telepítés](how-to-connect-install-express.md)
- [Egyéni telepítés](how-to-connect-install-custom.md)
- [Az ADConnectivityTools referenciája](reference-connect-adconnectivitytools.md)

