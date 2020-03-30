---
title: Az Azure AD tartományi szolgáltatások kezelési fogalmai | Microsoft dokumentumok
description: Tudnivalók az Azure Active Directory tartományi szolgáltatások felügyelt tartományainak felügyeletéről, valamint a felhasználói fiókok és jelszavak viselkedéséről
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 682935fa2324b8de4992ab2f90c7f71e05c4f8ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264231"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Felügyeleti fogalmak a felhasználói fiókokhoz, jelszavakhoz és felügyelethez az Azure Active Directory tartományi szolgáltatásokban

Az Azure Active Directory tartományi szolgáltatások (AD DS) felügyelt tartományának létrehozásakor és futtatásakor a hagyományos helyszíni Active DS-környezethez képest bizonyos különbségek vannak a viselkedésben. Az Azure AD DS-ben ugyanazokat a felügyeleti eszközöket használja, mint egy saját felügyelt tartományt, de nem tud közvetlenül hozzáférni a tartományvezérlőkhöz (DC). A jelszóházirendek és a jelszókimondottak viselkedésében is vannak különbségek a felhasználói fiók létrehozásának forrásától függően.

Ez az általános cikk ismerteti, hogyan felügyelheti az Azure AD DS felügyelt tartományt, és a felhasználói fiókok különböző viselkedését a létrehozásuk módjától függően.

## <a name="domain-management"></a>Tartománykezelés

Az Azure AD DS-ben a tartományvezérlők (DCs), amelyek tartalmazzák az összes erőforrást, például a felhasználók és csoportok, hitelesítő adatok és szabályzatok a felügyelt szolgáltatás részét képezik. A redundancia két tartományvezérlők egy Azure AD DS felügyelt tartomány részeként jönnek létre. Felügyeleti feladatok végrehajtásához nem jelentkezhet be ezekbe a tartományvezérlőkre. Ehelyett hozzon létre egy felügyeleti virtuális gép, amely csatlakozott az Azure AD DS felügyelt tartományhoz, majd telepítse a rendszeres AD DS felügyeleti eszközök. Használhatja például az Active Directory felügyeleti központot vagy a Microsoft Management Console (MMC) beépülő modulokat, például a DNS- vagy csoportházirend-objektumokat.

## <a name="user-account-creation"></a>Felhasználói fiók létrehozása

Felhasználói fiókok az Azure AD DS-ben többféleképpen hozhatók létre. A legtöbb felhasználói fiókok szinkronizálva az Azure AD, amely magában foglalhatja a felhasználói fiók szinkronizált egy helyszíni ActiveAngeles-szolgáltatások környezetben. Manuálisan is létrehozhat fiókokat közvetlenül az Azure AD DS-ben. Egyes funkciók, például a kezdeti jelszó-szinkronizálás vagy a jelszóházirend eltérően viselkednek a felhasználói fiókok létrehozásának módjától és tartózkodási helytől függően.

* A felhasználói fiók szinkronizálható az Azure AD-ből. Ez magában foglalja a közvetlenül az Azure AD-ben létrehozott csak felhőalapú felhasználói fiókokat, valamint a helyszíni AD DS-környezetből az Azure AD Connect használatával szinkronizált hibrid felhasználói fiókokat.
    * Az Azure AD DS felhasználói fiókjainak többsége az Azure AD szinkronizálási folyamatán keresztül jön létre.
* A felhasználói fiók manuálisan létrehozható egy Azure AD DS felügyelt tartományban, és nem létezik az Azure AD-ben.
    * Ha olyan alkalmazásokhoz kell szolgáltatási fiókokat létrehoznia, amelyek csak az Azure AD DS-ben futnak, manuálisan is létrehozhatja őket a felügyelt tartományban. Mivel a szinkronizálás egyik módja az Azure AD-től, az Azure AD DS-ben létrehozott felhasználói fiókok nincsenek szinkronizálva az Azure AD-vel.

## <a name="password-policy"></a>Jelszóházirend

Az Azure AD DS tartalmaz egy alapértelmezett jelszóházirendet, amely meghatározza a fiókzárolás, a jelszó maximális életkora és a jelszó összetettségének beállításait. Beállítások, például a fiók zárolására vonatkozó szabályzat az Azure AD DS összes felhasználójára vonatkozik, függetlenül attól, hogy a felhasználó hogyan jött létre az előző szakaszban ismertetett módon. Néhány beállítás, például a jelszó minimális hossza és a jelszó összetettsége, csak az Azure AD DS-ben közvetlenül létrehozott felhasználókra vonatkozik.

Létrehozhat saját egyéni jelszóházirendeket az Azure AD DS alapértelmezett szabályzatának felülbírálásához. Ezek az egyéni házirendek ezután szükség szerint alkalmazhatók a felhasználók adott csoportjaira.

A jelszóházirendek alkalmazásának módjáról a felhasználó létrehozásának forrásától függően további információt a [felügyelt tartományok jelszó- és fiókzárolási házirendjei című témakörben talál.][password-policy]

## <a name="password-hashes"></a>Jelszókimondottak

A felügyelt tartomány felhasználóinak hitelesítéséhez az Azure AD DS jelszókivonatokat igényel az NT LAN Manager (NTLM) és a Kerberos-hitelesítéshez megfelelő formátumban. Az Azure AD nem hoz létre és tárol jelszókivonatokat az NTLM- vagy Kerberos-hitelesítéshez szükséges formátumban, amíg nem engedélyezi az Azure AD DS-t a bérlő számára. Biztonsági okokból az Azure AD is nem tárolja a jelszó hitelesítő adatokat a tiszta szöveges formában. Ezért az Azure AD nem tudja automatikusan generálni ezeket az NTLM- vagy Kerberos-jelszókivét a felhasználók meglévő hitelesítő adatai alapján.

Csak felhőalapú felhasználói fiókok esetén a felhasználóknak módosítaniuk kell a jelszavukat, mielőtt használhatnák az Azure AD DS-t. Ez a jelszómódosítási folyamat a Kerberos- és NTLM-hitelesítés jelszókivonatait hozza létre és tárolja az Azure AD-ben.

A helyszíni Active Ád DS-környezetből az Azure AD Connect használatával szinkronizált felhasználók számára [engedélyezze a jelszókivék szinkronizálását.][hybrid-phs]

> [!IMPORTANT]
> Az Azure AD Connect csak akkor szinkronizálja az örökölt jelszókitagokat, ha engedélyezi az Azure AD DS-t az Azure AD-bérlőszámára. A régi jelszókitagok at nem használja, ha csak az Azure AD Connect használatával szinkronizálja a helyszíni AD DS-környezetet az Azure AD-vel.
>
> Ha az örökölt alkalmazások nem használnak NTLM-hitelesítést vagy LDAP egyszerű kötéseket, azt javasoljuk, hogy tiltsa le az NTLM jelszókivonat-szinkronizálást az Azure AD DS-hez. További információ: [A gyenge titkosítási csomagok letiltása és az NTLM hitelesítő adatok kivonatának szinkronizálása.][secure-domain]

A megfelelően konfigurált, a használható jelszó kikeresések az Azure AD DS felügyelt tartományban tárolja. Ha törli az Azure AD DS felügyelt tartományt, az adott ponton tárolt jelszókikeresések is törlődnek. Szinkronizált hitelesítő adatok az Azure AD nem használható fel újra, ha később létrehoz egy Azure AD DS felügyelt tartományt – újra kell konfigurálnia a jelszókivonat-szinkronizálást a jelszókivonat-kivonatok újbóli tárolásához. Korábban a tartományhoz csatlakozó virtuális gépek vagy felhasználók nem lesznek képesek azonnal hitelesíteni – az Azure AD-nek létre kell hoznia és tárolnia kell a jelszókimondottakat az új Azure AD DS felügyelt tartományban. További információ: [Password hash sync process for Azure AD DS and Azure AD Connect][azure-ad-password-sync].

> [!IMPORTANT]
> Az Azure AD Connect csak telepíteni és konfigurálni a helyszíni AD DS-környezetekkel való szinkronizáláshoz. Nem támogatott az Azure AD Connect telepítése egy Azure AD DS által felügyelt tartományban az objektumok Azure AD-vel való szinkronizálásához.

## <a name="forests-and-trusts"></a>Erdők és megbízhatóságok

Az *erdő* egy logikai konstrukció, amelyet az Active Directory tartományi szolgáltatások (AD DS) egy vagy több *tartomány csoportosítására*használnak. A tartományok ezután tárolóobjektumokat tárolnak a felhasználók vagy csoportok számára, és hitelesítési szolgáltatásokat nyújtanak.

Az Azure AD DS-ben az erdő csak egy tartományt tartalmaz. A helyszíni AD DS-erdők gyakran sok tartományt tartalmaznak. A nagy szervezetek, különösen az egyesülések és felvásárlások után, előfordulhat, hogy a végén több helyszíni erdők, amelyek mindegyike több tartományt tartalmaz.

Alapértelmezés szerint egy Azure AD DS felügyelt tartomány jön *létre,* mint egy felhasználói erdő. Ez az erdőtípus szinkronizálja az Azure AD összes objektumát, beleértve a helyszíni Activeád-ds környezetben létrehozott felhasználói fiókokat is. A felhasználói fiókok közvetlenül hitelesíthetik magukat az Azure AD DS felügyelt tartományával szemben, például bejelentkezhetnek egy tartományhoz csatlakozó virtuális gépbe. A felhasználói erdő akkor működik, ha a jelszókivonatok szinkronizálhatók, és a felhasználók nem használnak kizárólagos bejelentkezési módszereket, például intelligens kártya hitelesítést.

Egy Azure AD *DS-erőforrás-erdőben* a felhasználók hitelesítik egy egyirányú *erdőszintű bizalmi kapcsolat* a helyszíni AD DS-ből. Ezzel a módszerrel a felhasználói objektumok és a jelszó kimondott kibék nem szinkronizálva az Azure AD DS. A felhasználói objektumok és hitelesítő adatok csak a helyszíni AD DS-ben találhatók. Ez a megközelítés lehetővé teszi, hogy a vállalatok erőforrásokat és alkalmazásplatformokat üzemeltetjenek az Azure-ban, amelyek a klasszikus hitelesítéstől, például az LDAPS-től, a Kerberos-tól vagy az NTLM-től függenek, de a hitelesítési problémák vagy problémák törlődnek. Az Azure AD DS erőforrás-erdők jelenleg előzetes verzióban.

Az Azure AD DS erdőtípusairól a [Mik az erőforráserdők][concepts-forest] és [az erdőszintű bizalmi kapcsolatok az Azure AD DS-ben?][concepts-trust]

## <a name="azure-ad-ds-skus"></a>Azure AD DS SKUs

Az Azure AD DS-ben a rendelkezésre álló teljesítmény és a funkciók a termékváltozaton alapulnak. A felügyelt tartomány létrehozásakor kiválaszthat egy termékváltozatot, és átválthat a termékváltozatok között, ahogy az üzleti követelmények megváltoznak a felügyelt tartomány telepítése után. Az alábbi táblázat ismerteti a rendelkezésre álló sk-eket és a köztük lévő különbségeket:

| Termékváltozat neve   | Objektumok maximális száma | Biztonsági mentés gyakorisága | Kimenő erdőszintű bizalmi kapcsolatok maximális száma |
|------------|----------------------|------------------|----|
| Standard   | Korlátlan            | 7 naponta     | 0  |
| Enterprise | Korlátlan            | 3 naponta     | 5  |
| Prémium    | Korlátlan            | Napi            | 10 |

Az Azure AD DS-sk-ek előtt az Azure AD DS felügyelt tartományban lévő objektumok (felhasználói és számítógépfiókok) alapján használt számlázási modellt használták. A felügyelt tartományban lévő objektumok száma alapján már nincs változó árképzés.

További információ: az [Azure AD DS díjszabási lapja.][pricing]

### <a name="managed-domain-performance"></a>Felügyelt tartomány teljesítménye

A tartomány teljesítménye az alkalmazás hitelesítésének megvalósításától függ. További számítási erőforrások segíthetnek a lekérdezési válaszidő növelésében és a szinkronizálási műveletekben töltött idő csökkentésében. A termékváltozat szintjének növekedésével a felügyelt tartomány számára elérhető számítási erőforrások növekednek. Figyelje az alkalmazások teljesítményét, és tervezze meg a szükséges erőforrásokat.

Ha a vállalkozás vagy az alkalmazás igényei megváltoznak, és további számítási teljesítményre van szüksége az Azure AD DS felügyelt tartományához, átválthat egy másik termékváltozatra.

### <a name="backup-frequency"></a>Biztonsági mentés gyakorisága

A biztonsági mentés gyakorisága határozza meg, hogy milyen gyakran készül a felügyelt tartomány pillanatképe. A biztonsági mentések az Azure platform által felügyelt automatizált folyamat. A felügyelt tartománnyal kapcsolatos probléma esetén az Azure-támogatás segítséget nyújt a biztonsági mentésből való visszaállításban. Mivel a szinkronizálás csak egy módon történik az Azure *AD-től,* az Azure AD DS felügyelt tartományában felmerülő problémák nem befolyásolják az Azure AD vagy a helyszíni AD DS-környezeteket és funkciókat.

A termékváltozat szintjének növekedésével a biztonsági mentési pillanatképek gyakorisága nő. Tekintse át az üzleti követelmények és a helyreállítási pont célja (RPO) a felügyelt tartomány szükséges biztonsági mentési gyakoriságának meghatározásához. Ha a vállalkozás vagy az alkalmazás követelményei megváltoznak, és gyakoribb biztonsági mentésre van szüksége, átválthat egy másik termékváltozatra.

### <a name="outbound-forests"></a>Kimenő erdők

Az előző szakasz részletes en egyirányú kimenő erdőszintű bizalmi kapcsolatok egy Azure AD DS felügyelt tartományból egy helyszíni AD DS-környezet (jelenleg előzetes verzióban). A termékváltozat határozza meg, hogy az Azure AD DS felügyelt tartományhoz létrehozhat-e erdőszintű bizalmi kapcsolatok maximális számát. Tekintse át az üzleti és az alkalmazás követelményeit, és határozza meg, hogy ténylegesen hány bizalmi kapcsolatra van szüksége, és válassza ki a megfelelő Azure AD DS Termékváltozatot. Ismét, ha az üzleti követelmények megváltoznak, és további erdőszintű bizalmi kapcsolatokat kell létrehoznia, átválthat egy másik termékváltozatra.

## <a name="next-steps"></a>További lépések

Első lépésekhez [hozzon létre egy Azure AD DS felügyelt tartományt.][create-instance]

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
