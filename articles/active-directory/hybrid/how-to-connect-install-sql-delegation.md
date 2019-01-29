---
title: SQL meghatalmazott rendszergazdai engedélyek használatával az Azure AD Connect telepítése |} A Microsoft Docs
description: Ez a témakör ismerteti az Azure AD Connect, amely lehetővé teszi, hogy egy olyan fiókkal, amely csak az SQL dbo engedélyekkel rendelkezik a telepítéshez egy frissítés.
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.reviewer: jparsons
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: bec7a7d363cf65cc8f7b2fb0cb5a8c529efec3e9
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155442"
---
# <a name="install-azure-ad-connect-using-sql-delegated-administrator-permissions"></a>Telepítse az Azure AD Connect SQL-lel delegált rendszergazdai engedélyekkel
Az Azure AD Connect legújabb buildjének, mielőtt felügyeleti delegálás, SQL, a szükséges konfigurációk központi telepítésekor nem támogatott.  Felhasználók számára az Azure AD Connect telepítése szükséges az SQL Server kiszolgáló (SA) rendszergazdai jogosultságokkal rendelkezik.

Az Azure AD Connect legújabb kiadása most kiépítése az adatbázis is az SQL-rendszergazda által sávon kívül végrehajtott és az Azure AD Connect rendszergazdája az adatbázis-tulajdonosi jogosultságok telepítve.

## <a name="before-you-begin"></a>Előkészületek
Ez a funkció használatához szüksége, vegye figyelembe, hogy több mozgó részből áll, és mindegyik is igénybe vehet a szervezet egy másik rendszergazdája.  Az alábbi táblázat foglalja össze az egyes szerepkörök és üzembe helyezése az Azure AD Connect ezzel a funkcióval a feladataik.

|Szerepkör|Leírás|
|-----|-----|
|Tartomány vagy erdő AD-rendszergazda|A tartományi szintű szolgáltatás által az Azure AD Connect szinkronizálási szolgáltatás futtatásához használt fiókot hoz létre.  Szolgáltatásfiókok további információkért lásd: [fiókok és engedélyek](reference-connect-accounts-permissions.md).
|SQL-rendszergazda|Az ADSync adatbázist hoz létre, és a bejelentkezési + dbo biztosít hozzáférést az Azure AD Connect-rendszergazda és a tartományban/erdőben a rendszergazda által létrehozott szolgáltatásfiók|
Az Azure AD Connect-rendszergazda|Az Azure AD Connect telepíti, és adja meg a szolgáltatás fiók egyéni telepítés során.

## <a name="steps-for-installing-azure-ad-connect-using-sql-delegated-permissions"></a>A lépésekkel telepíthetők az Azure AD Connect SQL-lel delegált engedélyek
A sávon kívüli-adatbázis kiépítése, és az Azure AD Connect telepítése adatbázis tulajdonosi engedélyekkel rendelkező, kövesse az alábbi lépéseket.

>[!NOTE]
>Bár ez nem szükséges, célszerű **erősen ajánlott** , hogy az adatbázis létrehozásakor a Latin1_General_CI_AS rendezés van-e kiválasztva.


1.  Az SQL-rendszergazda az ADSync-adatbázis létrehozása egy kis-és nagybetű nincs megkülönböztetve rendezési sorrend rendelkezik **(Latin1_General_CI_AS)**.  Az adatbázis neve legyen **ADSync**.  A helyreállítási modell kompatibilitási szintje és elszigetelési típusa frissülnek, hogy a helyes értékeket az Azure AD Connect telepítése során.  Azonban a rendezési sorrend megfelelően kell állítani az SQL-rendszergazda által ellenkező esetben az Azure AD Connect meggátolja a telepítést.  A biztonsági Társítás helyreállítása törölje, majd hozza létre újból az adatbázist.</br>
![Rendezés](./media/how-to-connect-install-sql-delegation/sql4.png)
2.  Az Azure AD Connect-rendszergazda és a tartományi szolgáltatásfióknak a következő engedélyek megadására:
    - SQL-bejelentkezés 
    - **adatbázis-owner(dbo)** jogokat.  </br>
![Engedélyek](./media/how-to-connect-install-sql-delegation/sql3a.png)
3.  E-mail küldése az Azure AD Connect rendszergazdájának kell használni, amikor az Azure AD Connect telepítése SQL kiszolgáló és példánya nevének jelző.

## <a name="additional-information"></a>További információ
Miután az adatbázishoz ki van építve, az Azure AD Connect-rendszergazda telepítheti és konfigurálhatja a helyszíni szinkronizálási, saját igényeik.  

A **/useexistingdatabase** jelző egy előre létrehozott adatbázis használata esetén kötelező.  Nem csak használatos helyreállítási helyzetekben.

Mellett támogatása az Azure AD Connect új telepítések esetén ez a funkció is lehetővé teszi a delegálás bármilyen forgatókönyvhöz a kapcsolódó a **/useexistingdatabase** jelzőt.  Egy meglévő adatbázist az Azure AD Connect telepítésével további információkért lásd: [telepítse az Azure AD Connect használatával a meglévő ADSync-adatbázis](how-to-connect-install-existing-database.md)


## <a name="next-steps"></a>További lépések
- [Első lépések az Azure AD Connecttel a gyorsbeállítások használatával](how-to-connect-install-express.md)
- [Az Azure AD Connect testreszabott telepítése](how-to-connect-install-custom.md)
- [Az Azure AD Connect telepítése meglévő ADSync-adatbázis használatával](how-to-connect-install-existing-database.md)  
