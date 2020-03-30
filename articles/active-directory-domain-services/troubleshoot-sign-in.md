---
title: Bejelentkezési problémák elhárítása az Azure AD tartományi szolgáltatásokban | Microsoft dokumentumok
description: Megtudhatja, hogyan háríthatja el az Azure Active Directory tartományi szolgáltatások gyakori felhasználói bejelentkezési problémáit és hibáit.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 0585ced3bc53f216ab203b4686b5800b5e14bbbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612745"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>Fiókbejelentkezési problémák elhárítása az Azure AD tartományi szolgáltatások által kezelt tartománnyal

Az Azure AD DS felügyelt tartományába nem bejelentkező felhasználói fiókok leggyakoribb okai a következő esetek:

* [A fiók még nincs szinkronizálva az Azure AD DS-ben.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Az Azure AD DS nem rendelkezik a fiók bejelentkezéséhez a jelszókimondásokkal.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [A fiók zárolva van.](#the-account-is-locked-out)

> [!TIP]
> Az Azure AD DS nem szinkronizálhatja a hitelesítő adatokat az Azure AD-bérlőn kívüli fiókok esetén. A külső felhasználók nem tudnak bejelentkezni az Azure AD DS felügyelt tartományába.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>A fiók még nincs szinkronizálva az Azure AD DS szolgáltatásban

A címtár méretétől függően eltarthat egy ideig, amíg a felhasználói fiókok és a hitelesítő adatok kivéti elérhetők az Azure AD DS-ben. Nagy könyvtárak esetén ez a kezdeti egyirányú szinkronizálás az Azure AD-ből néhány órát is igénybe vehet, és akár egy-két napig is eltarthat. Győződjön meg arról, hogy elég sokáig vár, mielőtt újra próbálkozna a hitelesítéssel.

Az Azure AD Connect felhasználói által a helyszíni címtáradatok Azure AD-be történő szinkronizálásához használt hibrid környezetek esetében győződjön meg arról, hogy az Azure AD Connect legújabb verzióját [futtatja, és az Azure AD Connect et úgy konfigurálta, hogy az Azure AD DS engedélyezése után teljes körű szinkronizálást hajtson végre.][azure-ad-connect-phs] Ha letiltja az Azure AD DS-t, majd újra engedélyezi, ismét követnie kell ezeket a lépéseket.

Ha továbbra is problémák merülnek fel olyan fiókokkal, amelyek nem szinkronizálódnak az Azure AD Connecten keresztül, indítsa újra az Azure AD sync-szolgáltatást. A számítógépről, amelyen telepítve van az Azure AD Connect, nyisson meg egy parancssori ablakot, és futtassa a következő parancsokat:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Az Azure AD DS nem rendelkezik a jelszókimondottakkal

Az Azure AD nem hoz létre és tárol jelszókivonatokat az NTLM- vagy Kerberos-hitelesítéshez szükséges formátumban, amíg nem engedélyezi az Azure AD DS-t a bérlő számára. Biztonsági okokból az Azure AD is nem tárolja a jelszó hitelesítő adatokat a tiszta szöveges formában. Ezért az Azure AD nem tudja automatikusan generálni ezeket az NTLM- vagy Kerberos-jelszókivét a felhasználók meglévő hitelesítő adatai alapján.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Hibrid környezetek helyszíni szinkronizálással

Az Azure AD Connect használatával a helyszíni Activeád-ds-környezetből történő szinkronizáláshoz használt hibrid környezetekben helyileg generálhatja és szinkronizálhatja a szükséges NTLM- vagy Kerberos-jelszó-kivéteket az Azure AD-ben. Az Azure AD DS felügyelt tartományának létrehozása után engedélyezze a [jelszókivonat-szinkronizálást az Azure Active Directory tartományi szolgáltatásokkal.][azure-ad-connect-phs] A jelszókivonat-szinkronizálási lépés végrehajtása nélkül nem jelentkezhet be egy fiókba az Azure AD DS használatával. Ha letiltja az Azure AD DS-t, majd újra engedélyezi, újra kell végrehajtania ezeket a lépéseket.

További információ: [Hogyan működik a jelszókivonat-szinkronizálás az Azure AD DS-ben.][phs-process]

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Csak felhőalapú környezetek helyszíni szinkronizálás nélkül

Az Azure AD DS felügyelt tartományok nem helyszíni szinkronizálás, csak az Azure AD-fiókok, is létre kell hoznia a szükséges NTLM vagy Kerberos jelszó kihágások. Ha egy csak felhőalapú fiók nem tud bejelentkezni, az Azure AD DS engedélyezése után sikeresen befejeződött a fiók jelszómódosítási folyamata?

* **Nem, a jelszó nem változott.**
    * [Módosítsa a fiók jelszavát a][enable-user-accounts] szükséges jelszókimondottak létrehozásához, majd várjon 15 percet, mielőtt újra megpróbálna bejelentkezni.
    * Ha letiltja az Azure AD DS-t, majd újra engedélyezi, minden fióknak újra követnie kell a lépéseket a jelszó módosításához és a szükséges jelszókiírások létrehozásához.
* **Igen, a jelszó megváltozott.**
    * Próbáljon meg bejelentkezni az *UPN* formátumban, például `driley@aaddscontoso.com`a , a *SAMAccountName* formátum helyett, például `AADDSCONTOSO\deeriley`.
    * A *SAMAccountName* automatikusan létrehozható azon felhasználók számára, akiknek az UPN előtagja túl hosszú, vagy megegyezik a felügyelt tartomány másik felhasználójával. Az *UPN-formátum* garantáltan egyedi az Azure AD-bérlőn belül.

## <a name="the-account-is-locked-out"></a>A fiók zárolva van

Az Azure AD DS-ben egy felhasználói fiók zárolva van, ha a sikertelen bejelentkezési kísérletek meghatározott küszöbértéke teljesült. Ez a fiókzárolási viselkedés úgy van kialakítva, hogy megvédje Önt az ismétlődő találgatásos bejelentkezési kísérletektől, amelyek automatikus digitális támadásra utalhatnak.

Alapértelmezés szerint, ha 2 percen belül 5 rossz jelszókísérlet van, a fiók 30 percre zárolva van.

További információt és a fiókzárolási problémák megoldásáról az [Azure AD DS fiókzárolási problémáinak elhárítása című témakörben][troubleshoot-account-lockout]talál.

## <a name="next-steps"></a>További lépések

Ha továbbra is problémái vannak a virtuális gépnek az Azure AD DS felügyelt tartományhoz való csatlakozásával, [kérjen segítséget, és nyisson meg egy támogatási jegyet az Azure Active Directoryhoz.][azure-ad-support]

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
