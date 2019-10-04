---
title: A Azure AD Domain Services bejelentkezési problémáinak elhárítása | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani a Azure Active Directory Domain Services gyakori felhasználói bejelentkezési problémáit és hibáit.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: aa03e388019bf696324ea7af6062ec98386df5fa
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827051"
---
# <a name="troubleshoot-account-sign-in-problems-with-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services felügyelt tartományhoz tartozó bejelentkezési problémák elhárítása

Egy olyan felhasználói fiók leggyakoribb oka, amely nem tud bejelentkezni egy Azure AD DS felügyelt tartományba, például a következő helyzetekben:

* [A fiók még nincs szinkronizálva az Azure AD DSba.](#account-isnt-synchronized-into-azure-ad-ds-yet)
* [Az Azure AD DS nem rendelkezik jelszó-kivonatokkal, hogy a fiók bejelentkezzen.](#azure-ad-ds-doesnt-have-the-password-hashes)
* [A fiók ki van zárva.](#the-account-is-locked-out)

> [!TIP]
> Az Azure AD DS nem tud szinkronizálni az Azure AD-bérlőn kívüli fiókok hitelesítő adataival. A külső felhasználók nem jelentkezhetnek be az Azure AD DS felügyelt tartományba.

## <a name="account-isnt-synchronized-into-azure-ad-ds-yet"></a>A fiók még nincs szinkronizálva az Azure AD DS

A címtár méretétől függően eltarthat egy ideig, amíg a felhasználói fiókok és a hitelesítőadat-kivonatok elérhetők lesznek az Azure AD DSban. Nagyméretű könyvtárak esetén ez az Azure AD-ből származó első egyirányú szinkronizálás néhány órát és akár két napot is igénybe vehet. Győződjön meg arról, hogy elég sokáig vár a hitelesítés újrapróbálkozása előtt.

Olyan hibrid környezetek esetén, amelyeket a felhasználó Azure AD Connect a helyszíni címtáradatok Azure AD-ba való szinkronizálására, győződjön meg arról, hogy a Azure AD Connect legújabb verzióját futtatja, és úgy [konfigurálta a Azure ad Connect, hogy az Azure engedélyezése után teljes szinkronizálást végezzen. AD DS][azure-ad-connect-phs]. Ha letiltja az Azure AD DS, majd újra engedélyezi, kövesse az alábbi lépéseket.

Ha továbbra is problémákba ütközik a Azure AD Connect használatával nem szinkronizált fiókok esetében, indítsa újra a Azure AD-szinkronizáló szolgáltatást. Nyisson meg egy parancssori ablakot a Azure AD Connect telepített számítógépen, és futtassa a következő parancsokat:

```console
net stop 'Microsoft Azure AD Sync'
net start 'Microsoft Azure AD Sync'
```

## <a name="azure-ad-ds-doesnt-have-the-password-hashes"></a>Az Azure AD DS nem rendelkezik jelszó-kivonatokkal

Az Azure AD az NTLM-vagy Kerberos-hitelesítéshez szükséges formátumban nem állítja elő vagy tárolja a jelszó-kivonatokat, amíg nem engedélyezi az Azure-AD DS a bérlő számára. Biztonsági okokból az Azure AD nem tárolja a jelszó hitelesítő adatait a tiszta szöveges formában. Ezért az Azure AD nem tudja automatikusan előállítani ezeket az NTLM-vagy Kerberos-jelszó-kivonatokat a felhasználók meglévő hitelesítő adatai alapján.

### <a name="hybrid-environments-with-on-premises-synchronization"></a>Hibrid környezetek helyszíni szinkronizálással

A helyszíni AD DS-környezetből Azure AD Connectt használó hibrid környezetek esetén helyileg létrehozhatja és szinkronizálhatja a szükséges NTLM-vagy Kerberos-jelszó kivonatait az Azure AD-ben. Miután létrehozta az Azure AD DS felügyelt tartományt, [engedélyezze a jelszó-kivonatolási szinkronizálást Azure Active Directory Domain Services][azure-ad-connect-phs]. A jelszó-kivonat szinkronizációs lépése nélkül nem tud bejelentkezni egy fiókba az Azure AD DS használatával. Ha letiltja az Azure AD DS, majd újra engedélyezi, ezeket a lépéseket újra végre kell hajtania.

További információkért lásd: [a jelszó-kivonatolási szinkronizálás működése az Azure ad DSban][phs-process].

### <a name="cloud-only-environments-with-no-on-premises-synchronization"></a>Csak felhőalapú környezetek helyszíni szinkronizálás nélkül

Az Azure AD DS felügyelt tartományokat nem helyszíni szinkronizálás nélkül, hanem csak az Azure AD-ben lévő fiókokat kell létrehoznia a szükséges NTLM-vagy Kerberos-jelszó kivonatok létrehozásához. Ha a csak felhőalapú fiók nem tud bejelentkezni, a fiók sikeresen befejeződött a jelszó módosítási folyamata az Azure AD DS engedélyezése után?

* **Nem, a jelszó nem módosult.**
    * [Módosítsa a fiók jelszavát][enable-user-accounts] a szükséges jelszó-kivonatok létrehozásához, majd várjon 15 percet, mielőtt újra bejelentkezik.
    * Ha letiltja az Azure AD DS, majd újra engedélyezi, minden fióknak újra kell követnie a lépéseket a jelszavuk módosításához és a szükséges jelszó-kivonatok létrehozásához.
* **Igen, a jelszó megváltozott.**
    * Próbáljon meg az *egyszerű felhasználónév (UPN* ) formátumával bejelentkezni, például `driley@contoso.com`, a *sAMAccountName* formátum helyett, például `CONTOSO\deeriley`.
    * Előfordulhat, hogy a *sAMAccountName* automatikusan jön létre azon felhasználók számára, akiknek UPN-előtagja túl hosszú, vagy a felügyelt tartomány egy másik felhasználója. Az *UPN* formátuma garantáltan egyedi az Azure ad-bérlőn belül.

## <a name="the-account-is-locked-out"></a>A fiók ki van zárva

Az Azure AD DSban lévő felhasználói fiók ki van zárva, ha a sikertelen bejelentkezési kísérletek meghatározott küszöbértéke teljesül. Ez a fiókzárolási viselkedés úgy lett kialakítva, hogy megvédje a nem kötelezően kikényszerített, automatikus digitális támadásra utaló kísérleteket.

Alapértelmezés szerint, ha 2 percen belül 5 hibás jelszóval próbálkozik, a fiók 30 percig ki lesz zárva.

További információ és a fiókzárolási problémák megoldása: fiókzárolási [problémák elhárítása az Azure ad DSban][troubleshoot-account-lockout].

## <a name="next-steps"></a>További lépések

Ha továbbra sem sikerül csatlakoztatni a virtuális gépet az Azure AD DS felügyelt tartományhoz, [keressen segítséget a Azure Active Directory támogatási jegyének megnyitásához][azure-ad-support].

<!-- INTERNAL LINKS -->
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-ad-connect-phs]: active-directory-ds-getting-started-password-sync-synced-tenant.md
[enable-user-accounts]:  tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[phs-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
