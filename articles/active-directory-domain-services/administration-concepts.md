---
title: A Azure AD Domain Services Felügyeleti fogalmai | Microsoft Docs
description: Útmutató Azure Active Directory Domain Services felügyelt tartomány felügyeletéhez, valamint a felhasználói fiókok és jelszavak működésének megismeréséhez
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: iainfou
ms.openlocfilehash: b82927efa9054e71379d01993d1669527bc71402
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249424"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Felügyeleti fogalmak a felhasználói fiókokhoz, jelszavakhoz és felügyelethez Azure Active Directory Domain Services

Ha Azure Active Directory Domain Services (AD DS) felügyelt tartományt hoz létre és futtat, a viselkedés a hagyományos helyszíni AD DS környezethez képest valamilyen eltérést mutat. Ugyanazokat a felügyeleti eszközöket használja az Azure AD DS önálló felügyelt tartományként, de nem férhet hozzá közvetlenül a tartományvezérlőhöz (DC). A felhasználói fiókok létrehozásának forrásától függően a jelszóházirend és a jelszó-kivonatok viselkedése is fennáll.

Ez a fogalmi cikk azt ismerteti, hogyan lehet felügyelni egy Azure AD DS felügyelt tartományt és a felhasználói fiókok különböző viselkedését a létrehozásuk módjától függően.

## <a name="domain-management"></a>Tartományi felügyelet

Az Azure AD DSban az összes erőforrást (például a felhasználókat és a csoportokat, a hitelesítő adatokat és a szabályzatokat) tartalmazó tartományvezérlők (DCs-EK) a felügyelt szolgáltatás részét képezik. A redundancia érdekében a rendszer két tartományvezérlőt hoz létre egy Azure AD DS felügyelt tartomány részeként. A felügyeleti feladatok elvégzéséhez nem lehet bejelentkezni a tartományvezérlőre. Ehelyett létre kell hoznia egy, az Azure AD DS felügyelt tartományhoz csatlakoztatott felügyeleti virtuális gépet, majd telepítenie kell a normál AD DS felügyeleti eszközöket. Használhatja például a Active Directory felügyeleti központ vagy a Microsoft Management Console (MMC) beépülő modult, például a DNS-vagy Csoportházirend-objektumokat.

## <a name="user-account-creation"></a>Felhasználói fiók létrehozása

A felhasználói fiókok több módon is létrehozhatók az Azure AD DSban. A legtöbb felhasználói fiók szinkronizálva van az Azure AD-ből, amely tartalmazhatja a helyszíni AD DS környezetből szinkronizált felhasználói fiókot is. A fiókokat manuálisan is létrehozhatja az Azure AD DSban. Bizonyos funkciók, például a kezdeti jelszó-szinkronizálás vagy a jelszóházirend, eltérően viselkednek, attól függően, hogyan és hol jönnek létre a felhasználói fiókok.

* A felhasználói fiók szinkronizálható az Azure AD-ből. Ez magában foglalja a közvetlenül az Azure AD-ben létrehozott felhőalapú felhasználói fiókokat, valamint a helyi AD DS környezetből Azure AD Connect használatával szinkronizált hibrid felhasználói fiókokat.
    * Az Azure AD DS felhasználói fiókjainak többsége az Azure AD szinkronizálási folyamatán keresztül jön létre.
* A felhasználói fiók manuálisan is létrehozható egy Azure AD DS felügyelt tartományban, és nem létezik az Azure AD-ben.
    * Ha olyan alkalmazásokhoz kell szolgáltatásfiókot létrehoznia, amelyek csak az Azure AD DSban futnak, manuálisan is létrehozhatja őket a felügyelt tartományban. Mivel az Azure AD-vel való szinkronizálás egyirányú, az Azure AD DSban létrehozott felhasználói fiókok nincsenek szinkronizálva az Azure AD-vel.

## <a name="password-policy"></a>Jelszóházirend

Az Azure AD DS tartalmaz egy alapértelmezett jelszóházirend-szabályzatot, amely meghatározza a fiókok zárolási beállításait, a jelszavak maximális élettartamát és a jelszó bonyolultságát. Az olyan beállítások, mint a fiókzárolási házirend az Azure AD DS összes felhasználójára érvényesek, függetlenül attól, hogy a felhasználó hogyan lett létrehozva az előző szakaszban leírt módon. Néhány beállítás (például a jelszó minimális hossza és a jelszó bonyolultsága) csak az Azure AD DSban közvetlenül létrehozott felhasználókra vonatkozik.

Az Azure-AD DS alapértelmezett házirendjének felülbírálásához létrehozhat saját egyéni jelszóházirend-szabályzatokat is. Ezeket az egyéni házirendeket ezután a felhasználók adott csoportjaira lehet alkalmazni, igény szerint.

A jelszó-szabályzatok felhasználói létrehozási forrástól függően történő alkalmazásával kapcsolatos további információkért lásd: [jelszó-és fiókzárolási házirendek a felügyelt tartományokon][password-policy].

## <a name="password-hashes"></a>Jelszó-kivonatok

A felügyelt tartományon lévő felhasználók hitelesítéséhez az Azure AD DS a jelszó-kivonatokat az NT LAN Manager (NTLM) és a Kerberos-hitelesítéshez megfelelő formátumban kell megadni. Az Azure AD az NTLM-vagy Kerberos-hitelesítéshez szükséges formátumban nem állítja elő vagy tárolja a jelszó-kivonatokat, amíg nem engedélyezi az Azure-AD DS a bérlő számára. Biztonsági okokból az Azure AD nem tárolja a jelszó hitelesítő adatait a tiszta szöveges formában. Ezért az Azure AD nem tudja automatikusan előállítani ezeket az NTLM-vagy Kerberos-jelszó-kivonatokat a felhasználók meglévő hitelesítő adatai alapján.

A csak felhőalapú felhasználói fiókok esetében a felhasználóknak meg kell változtatniuk a jelszavukat, mielőtt használniuk tudják az Azure AD DS-t. Ez a jelszó-módosítási folyamat okozza a Kerberos és az NTLM hitelesítés jelszavas kivonatait az Azure AD-ben való létrehozásához és tárolásához.

A helyszíni AD DS környezetből Azure AD Connect használatával szinkronizált felhasználók számára [engedélyezze a jelszó-kivonatok szinkronizálását][hybrid-phs].

> [!IMPORTANT]
> Azure AD Connect csak a régi jelszavakat szinkronizálja, amikor engedélyezi az Azure AD DSt az Azure AD-bérlő számára. A rendszer nem használja az örökölt jelszavas kivonatokat, ha a helyszíni AD DS-környezet Azure AD-vel való szinkronizálásához csak Azure AD Connectt használ.
>
> Ha az örökölt alkalmazásai nem használnak NTLM-hitelesítést vagy LDAP egyszerű kötéseket, javasoljuk, hogy tiltsa le az NTLM-jelszó kivonatának szinkronizálását az Azure AD DS. További információ: a [gyenge titkosítási csomagok letiltása és az NTLM hitelesítő adatok kivonatának szinkronizálása][secure-domain].

A megfelelő konfigurálást követően a rendszer a használható jelszavak kivonatait az Azure AD DS felügyelt tartományában tárolja. Ha törli az Azure AD DS felügyelt tartományt, az adott ponton tárolt jelszó-kivonatok is törlődnek. Az Azure AD-ben szinkronizált hitelesítő adatok nem használhatók újra, ha később létrehoz egy Azure AD DS felügyelt tartományt – újra kell konfigurálnia a jelszó-kivonatok szinkronizálását a jelszó-kivonatok újbóli tárolására. Korábban a tartományhoz csatlakoztatott virtuális gépek vagy felhasználók nem tudják azonnal hitelesíteni magukat – az Azure AD-nek az új Azure AD DS felügyelt tartományban kell megadnia és tárolnia a jelszó-kivonatokat. További információ: jelszó- [kivonatolási szinkronizálási folyamat az Azure AD DS és Azure ad Connect][azure-ad-password-sync].

## <a name="next-steps"></a>Következő lépések

Első lépésként [hozzon létre egy Azure AD DS felügyelt tartományt][create-instance].

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
