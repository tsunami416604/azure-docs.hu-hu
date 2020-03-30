---
title: Active Directory-hitelesítés – Azure Database for MySQL
description: Ismerje meg az Azure Active Directory azure-beli MySQL-adatbázissal való hitelesítésre vonatkozó fogalmait
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 960536c3f80aa7870d6f2056d8e95cd1a4338dfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299022"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Az Azure Active Directory használata a MySQL-rel való hitelesítéshez

A Microsoft Azure Active Directory (Azure AD) hitelesítés egy olyan mechanizmus, amely az Azure Database for MySQL az Azure AD-ben meghatározott identitások használatával csatlakozik.
Az Azure AD-hitelesítéssel az adatbázis-felhasználók identitásait és más Microsoft-szolgáltatásokat egy központi helyen kezelheti, ami leegyszerűsíti az engedélykezelést.

> [!IMPORTANT]
> Az Azure AD-hitelesítés az Azure Database for MySQL jelenleg nyilvános előzetes verzióban.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Az Azure AD használatának előnyei a következők:

- A felhasználók hitelesítése az Azure-szolgáltatásokban egységes módon
- A jelszóházirendek és a jelszóelforgatás kezelése egyetlen helyen
- Az Azure Active Directory által támogatott többhitelesítési forma, amelyek szükségtelenné teszik a jelszavak tárolását
- Az ügyfelek kezelhetik az adatbázis-engedélyeket külső (Azure AD) csoportok használatával.
- Az Azure AD-hitelesítés a MySQL-adatbázis felhasználói által az adatbázis szintjén az identitások hitelesítésére
- Jogkivonat-alapú hitelesítés támogatása a MySQL Azure Database szolgáltatáshoz csatlakozó alkalmazások számára

Az Azure Active Directory-hitelesítés konfigurálásához és használatához használja a következő eljárást:

1. Hozza létre és népesítse be az Azure Active Directoryt szükség szerint felhasználói identitásokkal.
2. Szükség esetén társíthatja vagy módosíthatja az Azure-előfizetéséhez jelenleg társított Active Directoryt.
3. Hozzon létre egy Azure AD-rendszergazdát az Azure Database for MySQL-kiszolgálóhoz.
4. Hozzon létre adatbázis-felhasználók at az adatbázisban az Azure AD-identitások.
5. Csatlakozzon az adatbázishoz egy Azure AD-identitás jogkivonatának beolvasásával és a bejelentkezéssel.

> [!NOTE]
> Az Azure AD létrehozásáról és feltöltéséről, majd az Azure AD azure-adatbázissal a MySQL-hez való [konfigurálásáról és bejelentkezésről az Azure AD for Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)című témakörben olvashat.

## <a name="architecture"></a>Architektúra

Az alábbi magas szintű diagram összefoglalja, hogyan működik a hitelesítés az Azure AD-hitelesítés használatával az Azure Database for MySQL használatával. A nyilak kommunikációs útvonalakat jeleznek.

![hitelesítési folyamat][1]

## <a name="administrator-structure"></a>Rendszergazdai struktúra

Az Azure AD-hitelesítés használatakor két rendszergazdai fiók van a MySQL-kiszolgálóhoz; az eredeti MySQL-rendszergazda és az Azure AD-rendszergazda. Csak az Azure AD-fiókon alapuló rendszergazda hozhatja létre az első Azure AD-ben szereplő adatbázis-felhasználót egy felhasználói adatbázisban. Az Azure AD rendszergazdai bejelentkezés lehet egy Azure AD-felhasználó vagy egy Azure AD-csoport. Ha a rendszergazda egy csoportfiók, bármely csoporttag használhatja, amely lehetővé teszi több Azure AD-rendszergazda a MySQL-kiszolgáló számára. A csoportfiók rendszergazdaként való használata növeli a kezelhetőséget azáltal, hogy lehetővé teszi a csoporttagok központi hozzáadását és eltávolítását az Azure AD-ben a felhasználók vagy engedélyek módosítása nélkül a MySQL-kiszolgálón. Csak egy Azure AD-rendszergazda (egy felhasználó vagy csoport) konfigurálható bármikor.

![rendszergazdai struktúra][2]

## <a name="permissions"></a>Engedélyek

Új felhasználók létrehozásához, amelyek hitelesíthetők az Azure AD-vel, meg kell a tervezett Azure AD-rendszergazda. Ez a felhasználó van hozzárendelve az Azure AD-rendszergazdai fiók konfigurálásával egy adott Azure-adatbázis a MySQL-kiszolgálóhoz.

Új Azure AD-adatbázis-felhasználó létrehozásához az Azure AD-rendszergazdaként kell csatlakoznia. Ezt a [Konfigurálás és a Bejelentkezés az Azure AD for Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)című fájlban mutatja be.

Bármilyen Azure AD-hitelesítés csak akkor lehetséges, ha az Azure AD-rendszergazda jött létre az Azure Database for MySQL. Ha az Azure Active Directory-rendszergazda lett távolítva a kiszolgálóról, a korábban létrehozott meglévő Azure Active Directory-felhasználók már nem tudnak csatlakozni az adatbázishoz az Azure Active Directory hitelesítő adataival.

## <a name="connecting-using-azure-ad-identities"></a>Csatlakozás azure AD-identitások használatával

Az Azure Active Directory-hitelesítés a következő módszereket támogatja az azure AD-identitások használatával az adatbázishoz való csatlakozáshoz:

- Az Azure Active Directory jelszava
- Azure Active Directory integrált
- Azure Active Directory univerzális az MFA-val
- Active Directory alkalmazástanúsítványok vagy ügyféltitkok használata

Miután hitelesítette az Active Directoryt, lehív egy jogkivonatot. Ez a jogkivonat a bejelentkezéshez szükséges jelszó.

> [!NOTE]
> Az Active Directory-jogkivonatokkal való kapcsolatfelvételről a [Konfigurálás és bejelentkezés az Azure AD for Azure Database for MySQL című témakörben](howto-configure-sign-in-azure-ad-authentication.md)talál további információt.

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

- A MySQL-kiszolgálóhoz készült Azure Database-hez bármikor csak egy Azure AD-rendszergazda konfigurálható.
- Kezdetben csak a MySQL Azure AD-rendszergazdája csatlakozhat az Azure Database for MySQL-hez egy Azure Active Directory-fiók használatával. Az Active Directory rendszergazdája konfigurálhatja a további Azure AD-adatbázis-felhasználók.
- Ha egy felhasználó törlődik az Azure AD-ből, akkor a felhasználó már nem lesz képes hitelesíteni az Azure AD-vel, és ezért a továbbiakban nem lehet hozzáférési jogkivonatot beszerezni az adott felhasználószámára. Ebben az esetben, bár az egyező felhasználó továbbra is az adatbázisban marad, az adott felhasználóval nem lehet csatlakozni a kiszolgálóhoz.
> [!NOTE]
> A törölt Azure AD-felhasználóval való bejelentkezés a jogkivonat lejáratáig (a jogkivonat kiadásától számított 60 percig) elvégezhető.  Ha a felhasználót is eltávolítja az Azure Database for MySQL-ből, ez a hozzáférés azonnal visszavonásra kerül.
- Ha az Azure AD-rendszergazda törlődik a kiszolgálóról, a kiszolgáló már nem lesz társítva egy Azure AD-bérlőhöz, és ezért az összes Azure AD-bejelentkezés le lesz tiltva a kiszolgálón. Ha ugyanabból a bérlőből ad hozzá egy új Azure AD-rendszergazdát, akkor újra engedélyezi az Azure AD-bejelentkezéseket.
- Az Azure Database for MySQL a hozzáférési jogkivonatokat a MySQL-felhasználó Azure-adatbázisához egyezteti a felhasználó egyedi Azure AD-felhasználói azonosítójával, nem pedig a felhasználónév használatával. Ez azt jelenti, hogy ha egy Azure AD-felhasználó törlődik az Azure AD-ben, és egy új felhasználó ugyanazzal a névvel jön létre, az Azure Database for MySQL úgy véli, hogy egy másik felhasználó. Ezért ha egy felhasználó törlődik az Azure AD-ből, majd egy új felhasználó az azonos nevű hozzáadott, az új felhasználó nem lesz képes csatlakozni a meglévő felhasználóhoz.

## <a name="next-steps"></a>További lépések

- Az Azure AD létrehozásáról és feltöltéséről, majd az Azure AD azure-adatbázissal a MySQL-hez való [konfigurálásáról és bejelentkezésről az Azure AD for Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md)című témakörben olvashat.
- A bejelentkezések és az adatbázis-felhasználók a MySQL-hez szolgáltatáshoz szolgáltatásban a [Felhasználók létrehozása az Azure Database for MySQL](howto-create-users.md)alkalmazásban című témakörben olvashat.

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
