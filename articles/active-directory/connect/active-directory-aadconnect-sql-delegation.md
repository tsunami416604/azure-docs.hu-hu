---
title: "Telepítse az Azure AD Connect SQL meghatalmazott rendszergazdai engedélyek használatával |} Microsoft Docs"
description: "Ez a témakör ismerteti az Azure AD Connect, amely lehetővé teszi egy olyan fiókkal, amely csak az SQL dbo jogosultságokkal rendelkezik a telepítéshez tartozó frissítés."
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.reviewer: jparsons
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: billmath
ms.openlocfilehash: c2d77c37f2f65c9a7db1fd5c4010fc43bcbc7ebf
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>SQL meghatalmazott rendszergazdai engedélyek használatával az Azure AD Connect telepítése
Az Azure AD Connect legújabb buildjével, mielőtt felügyeleti delegálás, a szükséges SQL-konfigurációk központi telepítésekor nem támogatott.  Felhasználók számára az Azure AD Connect telepítése az SQL server kiszolgáló rendszergazdai engedélyekre van szükség.

Az Azure AD Connect legújabb kiadása most kiépítés az adatbázis is lehet sávon kívül az SQL-rendszergazda végezheti el és csak utána települ az adatbázis-tulajdonosi jogosultságok az Azure AD Connect rendszergazda.

## <a name="before-you-begin"></a>Előkészületek
Ez a funkció használatához szüksége vegye figyelembe, hogy áthelyezése több részből áll, és mindegyiket egy is tartalmaz, amely a szervezet egy másik rendszergazda.  A következő táblázat összefoglalja az egyes szerepkörök és az ezzel a szolgáltatással az Azure AD Connect telepítése feladataik.

|Szerepkör|Leírás|
|-----|-----|
|Tartomány vagy erdő AD-rendszergazda|Az Azure AD Connect szinkronizálási szolgáltatás futtatásához használt tartományi szintű szolgáltatási fiókot hoz létre.  Szolgáltatásfiókok további információkért lásd: [fiókok és engedélyek](active-directory-aadconnect-accounts-permissions.md).
|SQL-rendszergazdához|Az ADSync adatbázis hoz létre, és bejelentkezési + dbo biztosít hozzáférést az Azure AD Connect rendszergazda és a szolgáltatásfiók hozta létre a tartomány vagy erdő rendszergazdával.|
Az Azure AD Connect-rendszergazda|Az Azure AD Connect telepíti, és megadja, hogy a szolgáltatás egyéni telepítés során.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>Az SQL Azure AD Connect telepítésének lépéseit delegált engedélyek
A sávon kívüli adatbázis létesítéséhez, és az Azure AD Connect telepítése adatbázis-tulajdonos engedélyekkel, az alábbi lépésekkel.

>[!NOTE]
>Bár nem kötelező, **erősen ajánlott** , hogy az adatbázis létrehozásakor az Latin1_General_CI_AS illesztés van-e kiválasztva.


1.  Az SQL-rendszergazdához, hozzon létre az ADSync adatbázis-és nagybetűket rendezési sorrend rendelkezik **(Latin1_General_CI_AS)**.  Az adatbázis neve kell **ADSync**.  A helyreállítási modell kompatibilitási szintje és elszigetelési típusa helyes az értékük az Azure AD Connect telepítésekor frissülnek.  Azonban az SQL rendszergazda megfelelően kell beállítani az során a rendezési sorrend ellenkező esetben az Azure AD Connect letiltja a telepítés.  A rendszergazdai (SA) helyreállításához törölje, majd hozza létre újra az adatbázist.</br>
![Rendezés](media/active-directory-aadconnect-sql-delegation/sql1.png)
2.  Adja meg az Azure AD Connect rendszergazda és a tartományi szolgáltatásfiókot az alábbi engedélyeket:
    - SQL-bejelentkezés 
    - **adatbázis-owner(dbo)** jogok.  </br>
![Engedélyek](media/active-directory-aadconnect-sql-delegation/sql3.png)
3.  E-mail küldése az Azure AD Connect rendszergazda jelző kell használni, amikor az Azure AD Connect telepítése az SQL server és a példány neve.

## <a name="additional-information"></a>További információ
Az adatbázis üzembe helyezése után az Azure AD Connect rendszergazda telepítheti és beállíthatja a helyszínre végzett szinkronizálás kényelme:.  

Mellett támogatása az Azure AD Connect új telepítések esetén ez a funkció is lehetővé teszi a delegálás olyan forgatókönyv kapcsolódik a **/UseExistingDatabase** jelzőt.  Meglévő adatbázissal az Azure AD Connect telepítésével kapcsolatos további információkért lásd: [telepítse az Azure AD Connect az ADSync adatbázis](active-directory-aadconnect-existing-database.md)


## <a name="next-steps"></a>További lépések
- [Első lépések az Azure AD Connecttel a gyorsbeállítások használatával](active-directory-aadconnect-get-started-express.md)
- [Az Azure AD Connect testreszabott telepítése](active-directory-aadconnect-get-started-custom.md)
- [Az ADSync adatbázis Azure AD Connect telepítése](active-directory-aadconnect-existing-database.md)  