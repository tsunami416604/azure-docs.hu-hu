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
ms.topic: conceptual
ms.date: 02/26/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6269d00c9a6a8f827a4e31044d9d20efb0f8471b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58092582"
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


1. Az SQL-rendszergazda az ADSync-adatbázis létrehozása egy kis-és nagybetű nincs megkülönböztetve rendezési sorrend rendelkezik **(Latin1_General_CI_AS)**.  Az adatbázis neve legyen **ADSync**.  A helyreállítási modell kompatibilitási szintje és elszigetelési típusa frissülnek, hogy a helyes értékeket az Azure AD Connect telepítése során.  Azonban a rendezési sorrend megfelelően kell állítani az SQL-rendszergazda által ellenkező esetben az Azure AD Connect meggátolja a telepítést.  A biztonsági Társítás helyreállítása törölje, majd hozza létre újból az adatbázist.
 
   ![Rendezés](./media/how-to-connect-install-sql-delegation/sql4.png)
2. Az Azure AD Connect-rendszergazda és a tartományi szolgáltatásfióknak a következő engedélyek megadására:
   - SQL-bejelentkezés 
   - **adatbázis-owner(dbo)** jogokat.
 
   ![Engedélyek](./media/how-to-connect-install-sql-delegation/sql3a.png)

   >[!NOTE]
   >Az Azure AD Connect nem támogatja a beágyazott tagságot használatának.  Ez azt jelenti, hogy az Azure AD Connect rendszergazdai fiókot és a tartományi szolgáltatásfióknak kell társítani, amely a dbo jogokat kapnak a bejelentkezési adatokat.  Egyszerűen egy olyan bejelentkezési dbo jogosultságokkal hozzárendelt csoport tagjai nem lehet.

3. E-mail küldése az Azure AD Connect rendszergazdájának kell használni, amikor az Azure AD Connect telepítése SQL kiszolgáló és példánya nevének jelző.

## <a name="additional-information"></a>További információ
Miután az adatbázishoz ki van építve, az Azure AD Connect-rendszergazda telepítheti és konfigurálhatja a helyszíni szinkronizálási, saját igényeik.

Abban az esetben, ha az SQL-rendszergazda rendelkezik ADSync-adatbázis biztonsági másolatból történt visszaállítása egy korábbi az Azure AD Connect, szüksége lesz az új Azure AD Connect kiszolgáló telepítése egy már létező adatbázist használva. Egy meglévő adatbázist az Azure AD Connect telepítésével további információkért lásd: [meglévő ADSync-adatbázis használatával telepítse az Azure AD Connect](how-to-connect-install-existing-database.md).

## <a name="next-steps"></a>További lépések
- [Első lépések az Azure AD Connecttel a gyorsbeállítások használatával](how-to-connect-install-express.md)
- [Az Azure AD Connect testreszabott telepítése](how-to-connect-install-custom.md)
- [Az Azure AD Connect telepítése meglévő ADSync-adatbázis használatával](how-to-connect-install-existing-database.md)  
