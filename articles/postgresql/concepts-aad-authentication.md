---
title: Active Directory-hitelesítés – Azure Database for PostgreSQL – Egykiszolgálós
description: Ismerje meg az Azure Active Directory által a PostgreSQL-adatbázissal való hitelesítésre vonatkozó fogalmakat – Egykiszolgálós
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ec853657d6dd1f3b019d8a414cfa28edc1083b29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769914"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>Az Azure Active Directory használata a PostgreSQL-rel való hitelesítéshez

A Microsoft Azure Active Directory (Azure AD) hitelesítés egy olyan mechanizmus, amely az Azure Database for PostgreSQL-hez való csatlakozást az Azure AD-ben meghatározott identitások használatával csatlakozik.
Az Azure AD-hitelesítéssel az adatbázis-felhasználók identitásait és más Microsoft-szolgáltatásokat egy központi helyen kezelheti, ami leegyszerűsíti az engedélykezelést.

> [!IMPORTANT]
> Az Azure AD-hitelesítés az Azure Database for PostgreSQL jelenleg nyilvános előzetes verzióban.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Az Azure AD használatának előnyei a következők:

- A felhasználók hitelesítése az Azure-szolgáltatásokban egységes módon
- A jelszóházirendek és a jelszóelforgatás kezelése egyetlen helyen
- Az Azure Active Directory által támogatott többhitelesítési forma, amelyek szükségtelenné teszik a jelszavak tárolását
- Az ügyfelek kezelhetik az adatbázis-engedélyeket külső (Azure AD) csoportok használatával.
- Az Azure AD-hitelesítés PostgreSQL adatbázis-szerepköröket használ az identitások adatbázisszintű hitelesítéséhez
- Jogkivonat-alapú hitelesítés támogatása a PostgreSQL Azure-adatbázishoz csatlakozó alkalmazások számára

Az Azure Active Directory-hitelesítés konfigurálásához és használatához használja a következő eljárást:

1. Hozza létre és népesítse be az Azure Active Directoryt szükség szerint felhasználói identitásokkal.
2. Szükség esetén társíthatja vagy módosíthatja az Azure-előfizetéséhez jelenleg társított Active Directoryt.
3. Hozzon létre egy Azure AD-rendszergazdát az Azure Database for PostgreSQL-kiszolgálóhoz.
4. Hozzon létre adatbázis-felhasználók at az adatbázisban az Azure AD-identitások.
5. Csatlakozzon az adatbázishoz egy Azure AD-identitás jogkivonatának beolvasásával és a bejelentkezéssel.

> [!NOTE]
> Az Azure AD létrehozásáról és feltöltéséről, majd az Azure AD-nek az Azure Database for PostgreSQL-lel való konfigurálásáról és bejelentkezésről az [Azure AD for Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md)című témakörben olvashat.

## <a name="architecture"></a>Architektúra

Az alábbi magas szintű diagram összefoglalja, hogyan működik a hitelesítés az Azure AD-hitelesítés használatával az Azure Database for PostgreSQL használatával. A nyilak kommunikációs útvonalakat jeleznek.

![hitelesítési folyamat][1]

## <a name="administrator-structure"></a>Rendszergazdai struktúra

Az Azure AD-hitelesítés használatakor a PostgreSQL-kiszolgálóhoz két rendszergazdai fiók tartozik; az eredeti PostgreSQL-rendszergazda és az Azure AD-rendszergazda. Csak az Azure AD-fiókon alapuló rendszergazda hozhatja létre az első Azure AD-ben szereplő adatbázis-felhasználót egy felhasználói adatbázisban. Az Azure AD rendszergazdai bejelentkezés lehet egy Azure AD-felhasználó vagy egy Azure AD-csoport. Ha a rendszergazda egy csoportfiók, bármely csoporttag használhatja, amely lehetővé teszi több Azure AD-rendszergazda a PostgreSQL-kiszolgáló számára. A csoportfiók rendszergazdaként való használata növeli a kezelhetőséget azáltal, hogy lehetővé teszi a csoporttagok központi hozzáadását és eltávolítását az Azure AD-ben a felhasználók vagy engedélyek módosítása nélkül a PostgreSQL-kiszolgálón. Csak egy Azure AD-rendszergazda (egy felhasználó vagy csoport) konfigurálható bármikor.

![rendszergazdai struktúra][2]

## <a name="permissions"></a>Engedélyek

Új felhasználók létrehozásához, amelyek hitelesíthetők az `azure_ad_admin` Azure AD-vel, rendelkeznie kell a szerepkör az adatbázisban. Ez a szerepkör az Azure AD-rendszergazdai fiók konfigurálásával van hozzárendelve egy adott Azure Database for PostgreSQL-kiszolgálóhoz.

Új Azure AD-adatbázis-felhasználó létrehozásához az Azure AD-rendszergazdaként kell csatlakoznia. Ezt a Konfigurálás és a Bejelentkezés az [Azure AD for Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md)című fájlban mutatja be.

Bármilyen Azure AD-hitelesítés csak akkor lehetséges, ha az Azure AD-rendszergazda jött létre az Azure Database for PostgreSQL. Ha az Azure Active Directory-rendszergazda lett távolítva a kiszolgálóról, a korábban létrehozott meglévő Azure Active Directory-felhasználók már nem tudnak csatlakozni az adatbázishoz az Azure Active Directory hitelesítő adataival.

## <a name="connecting-using-azure-ad-identities"></a>Csatlakozás azure AD-identitások használatával

Az Azure Active Directory-hitelesítés a következő módszereket támogatja az azure AD-identitások használatával az adatbázishoz való csatlakozáshoz:

- Az Azure Active Directory jelszava
- Azure Active Directory integrált
- Azure Active Directory univerzális az MFA-val
- Active Directory alkalmazástanúsítványok vagy ügyféltitkok használata

Miután hitelesítette az Active Directoryt, lehív egy jogkivonatot. Ez a jogkivonat a bejelentkezéshez szükséges jelszó.

> [!NOTE]
> Az Active Directory-jogkivonatokkal való kapcsolatfelvételről a [Konfigurálás és bejelentkezés az Azure AD for Azure Database for PostgreSQL című témakörben](howto-configure-sign-in-aad-authentication.md)talál további információt.

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

- A kezelhetőség növelése érdekében azt javasoljuk, hogy egy dedikált Azure AD-csoportot rendszergazdaként létesítsen.
- Csak egy Azure AD-rendszergazda (felhasználó vagy csoport) konfigurálható egy Azure Database for PostgreSQL-kiszolgáló bármikor.
- Kezdetben csak a PostgreSQL Azure AD-rendszergazdája csatlakozhat a PostgreSQL Azure-adatbázisához egy Azure Active Directory-fiók használatával. Az Active Directory rendszergazdája konfigurálhatja a további Azure AD-adatbázis-felhasználók.
- Ha egy felhasználó törlődik az Azure AD-ből, akkor a felhasználó már nem lesz képes hitelesíteni az Azure AD-vel, és ezért a továbbiakban nem lehet hozzáférési jogkivonatot beszerezni az adott felhasználószámára. Ebben az esetben, bár az egyező szerepkör továbbra is az adatbázisban marad, ezzel a szerepkörrel nem lehet csatlakozni a kiszolgálóhoz.
> [!NOTE]
> A törölt Azure AD-felhasználóval való bejelentkezés a jogkivonat lejáratáig (a jogkivonat kiadásától számított 60 percig) elvégezhető.  Ha a felhasználót is eltávolítja a PostgreSQL-hez készült Azure Database for PostgreSQL-ből, a rendszer azonnal visszavonja ezt a hozzáférést.
- Ha az Azure AD-rendszergazda törlődik a kiszolgálóról, a kiszolgáló már nem lesz társítva egy Azure AD-bérlőhöz, és ezért az összes Azure AD-bejelentkezés le lesz tiltva a kiszolgálón. Ha ugyanabból a bérlőből ad hozzá egy új Azure AD-rendszergazdát, akkor újra engedélyezi az Azure AD-bejelentkezéseket.
- Az Azure Database for PostgreSQL a hozzáférési jogkivonatokat a felhasználó egyedi Azure AD-felhasználói azonosítójával egyezteti az Azure Database for PostgreSQL szerepkörrel, nem pedig a felhasználónév használatával. Ez azt jelenti, hogy ha egy Azure AD-felhasználó törlődik az Azure AD-ben, és egy új felhasználó ugyanazzal a névvel jön létre, az Azure Database for PostgreSQL úgy véli, hogy egy másik felhasználó. Ezért ha egy felhasználó törlődik az Azure AD-ből, majd egy új felhasználó az azonos nevű hozzáadott, az új felhasználó nem lesz képes csatlakozni a meglévő szerepkörhöz. Ahhoz, hogy lehetővé tegye, hogy az Azure Database for PostgreSQL AD-rendszergazda vissza kell vonnia, majd adja meg a szerepkör "azure_ad_user" a felhasználónak az Azure AD felhasználói azonosító frissítéséhez.

## <a name="next-steps"></a>További lépések

- Az Azure AD létrehozásáról és feltöltéséről, majd az Azure AD-nek az Azure Database for PostgreSQL-lel való konfigurálásáról és bejelentkezésről az [Azure AD for Azure Database for PostgreSQL](howto-configure-sign-in-aad-authentication.md)című témakörben olvashat.
- A bejelentkezések, a felhasználók és az Azure Database for PostgreSQL adatbázisának áttekintését a [Felhasználók létrehozása az Azure Database for PostgreSQL – Single Server](howto-create-users.md)című témakörben találja.

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/admin-structure.png
