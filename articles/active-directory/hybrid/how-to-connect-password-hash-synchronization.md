---
title: Jelszókivonat-szinkronizálás megvalósítása az Azure AD Connect szinkronizálásával | Microsoft dokumentumok
description: Tájékoztatást nyújt a jelszókivonat-szinkronizálás működéséről és a beállításról.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: c41b11ab65f5710d338ce0041579e1eb4678ec42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331364"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>A jelszókivonat-szinkronizálás implementálása Azure AD Connect-szinkronizálással
Ez a cikk olyan információkat tartalmaz, amelyeket a felhasználói jelszavak szinkronizálásához egy helyszíni Active Directory-példányból egy felhőalapú Azure Active Directory (Azure AD) példányba kell szinkronizálni.

## <a name="how-password-hash-synchronization-works"></a>A jelszókivonat-szinkronizálás működése
Az Active Directory tartományi szolgáltatás a jelszavakat a tényleges felhasználói jelszó kivonatérték-ábrázolása formájában tárolja. A kivonatérték egyirányú matematikai függvény (a *kivonatoló algoritmus)* eredménye. Az egyirányú függvény eredménye semmilyen módszerrel nem fejthető vissza a jelszó egyszerű szöveges verziójára. 

A jelszó szinkronizálásához az Azure AD Connect szinkronizáláskinyeri a jelszókivonatot a helyszíni Active Directory-példányból. További biztonsági feldolgozás a jelszó kivonatára vonatkozik, mielőtt szinkronizálna az Azure Active Directory hitelesítési szolgáltatás. A jelszavak szinkronizálása felhasználónként és időrendi sorrendben történik.

A jelszókivonat-szinkronizálási folyamat tényleges adatfolyama hasonló a felhasználói adatok szinkronizálásához. A jelszavak szinkronizálása azonban gyakrabban történik, mint más attribútumok szokásos címtár-szinkronizálási ablaka. A jelszókivonat-szinkronizálási folyamat 2 percenként fut. A folyamat gyakorisága nem módosítható. A jelszó szinkronizálásakor felülírja a meglévő felhőjelszót.

Amikor először engedélyezi a jelszókivonat-szinkronizálási szolgáltatást, az összes hatókörbe tartozó felhasználó jelszavainak kezdeti szinkronizálását hajtja végre. A szinkronizálni kívánt felhasználói jelszavak részhalmaza nem határozható meg kifejezetten. Ha azonban több összekötő van, egyes összekötők esetében letilthatja a jelszókivonat-szinkronizálást, másokat azonban nem a [Set-ADSyncAADPasswordSyncConfiguration](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant) parancsmag használatával.

Amikor módosítja a helyszíni jelszót, a frissített jelszó szinkronizálása, leggyakrabban percek alatt.
A jelszókivonat-szinkronizálási szolgáltatás automatikusan újrapróbálkozik a sikertelen szinkronizálási kísérletekkel. Ha hiba történik a jelszó szinkronizálása közben, a rendszer hibát naplóz az eseménynaplóba.

A jelszó szinkronizálása nincs hatással a jelenleg bejelentkezett felhasználóra.
A jelenlegi felhőszolgáltatási munkamenetet nem érinti azonnal egy szinkronizált jelszóváltozás, amely akkor következik be, amikor be van jelentkezve egy felhőszolgáltatásba. Ha azonban a felhőszolgáltatás ismételt hitelesítését igényli, meg kell adnia az új jelszót.

A felhasználónak meg kell adnia a vállalati hitelesítő adatait másodszor is az Azure AD-ben való hitelesítéshez, függetlenül attól, hogy be vannak-e jelentkezve a vállalati hálózatukba. Ez a minta minimalizálható, azonban ha a felhasználó bejelöli a Bejelentkezve (KMSI) bejelentéskor bejelentve jelölőnégyzetet. Ez a beállítás beállít egy munkamenet-cookie-t, amely 180 napra megkerüli a hitelesítést. KMSI viselkedés az Azure AD-rendszergazda által engedélyezhető vagy letiltható. Emellett csökkentheti a jelszókérések beállítását a [zökkenőmentes egyszeri bejelentkezés](how-to-connect-sso.md)bekapcsolásával, amely automatikusan bejelentkezik a felhasználókhoz, amikor a vállalati hálózathoz csatlakoztatott vállalati eszközökön vannak.

> [!NOTE]
> A jelszószinkronizálás csak az Active Directory objektumtípus-felhasználója számára támogatott. Az iNetOrgPerson objektumtípus nem támogatott.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>A jelszókivonat-szinkronizálás működésének részletes leírása

A következő szakasz részletesen ismerteti, hogyan működik a jelszókivonat-szinkronizálás az Active Directory és az Azure AD között.

![Részletes jelszóáramlás](./media/how-to-connect-password-hash-synchronization/arch3b.png)

1. Az AD Connect kiszolgálón a jelszókivonat-szinkronizáló ügynök kétpercenként tárolt jelszókivonatokat (a unicodePwd attribútumot) kér egy tartományvezérlőről.  Ez a kérés a tartományvezérlők közötti adatok szinkronizálására használt [szabványos MS-DRSR replikációs](https://msdn.microsoft.com/library/cc228086.aspx) protokollon keresztül történik. A jelszókiképek beszerzéséhez a szolgáltatásfióknak rendelkeznie kell a címtármódosítások replikálása és a címtármódosítások replikálása szükséges összes AD-engedély (alapértelmezés szerint a telepítéskor) értékének.
2. A küldés előtt a tartományvezérlő titkosítja az MD4 jelszókivonatot az [RPC-munkamenetkulcs MD5](https://www.rfc-editor.org/rfc/rfc1321.txt) kivonatával és egy sóval. Ezután elküldi az eredményt a jelszókivonat-szinkronizálási ügynöknek az RPC-n keresztül. A tartományvezérlő a sót a tartományvezérlő replikációs protokolljával továbbítja a szinkronizáló ügynöknek is, így az ügynök képes lesz visszafejteni a borítékot.
3. Miután a jelszókivonat-szinkronizálási ügynök rendelkezik a titkosított borítékkal, [az MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) és a só segítségével létrehoz egy kulcsot a fogadott adatok visszafejtéséhez az eredeti MD4 formátumba. A jelszókivonat-szinkronizáló ügynök soha nem fér hozzá a tiszta szöveges jelszóhoz. A jelszókivonat-szinkronizáló ügynök md5 használata szigorúan a replikációs protokoll kompatibilitása a tartományvezérlővel, és csak a tartományvezérlő és a jelszó kivonatoló szinkronizálási ügynök között a helyszínen használható.
4. A jelszókivonat-szinkronizáló ügynök a 16 bájtos bináris jelszókivonatot 64 bájtra bontja ki úgy, hogy először a kivonatot 32 bájtos hexadecimális karakterláncra konvertálja, majd ezt a karakterláncot UTF-16 kódolással visszakonvertálja binárissá.
5. A jelszókivonat-szinkronizáló ügynök egy felhasználónkénti sót ad hozzá, amely egy 10 bájtos hosszúságú sót tartalmaz a 64 bájtos bináris fájlhoz, hogy tovább védje az eredeti kivonatot.
6. A jelszókivonat-szinkronizáló ügynök ezután egyesíti az MD4 kivonatot és a felhasználónkénti sót, és beírja a [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) függvénybe. A [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) kulcsos kivonatoló algoritmus 1000 iterációját használjuk. 
7. A jelszó-kivonatoló szinkronizálási ügynök veszi az eredményül kapott 32 bájtos kivonat, összefűzi mind a felhasználónkénti só és az SHA256 iterációk számát hozzá (az Azure AD általi használatra), majd továbbítja a karakterláncot az Azure AD-ből az Azure AD-t TLS-en keresztül.</br> 
8. Amikor egy felhasználó megpróbál bejelentkezni az Azure AD-be, és beírja a jelszavát, a jelszó ugyanazon az MD4+salt+PBKDF2+HMAC-SHA256 folyamaton fut keresztül. Ha az eredményül kapott kivonat megegyezik az Azure AD-ben tárolt kivonattal, a felhasználó megadta a megfelelő jelszót, és hitelesíti.

> [!NOTE]
> Az eredeti MD4-kivonat nem kerül továbbításra az Azure AD-be. Ehelyett az eredeti MD4-kivonat SHA256 kivonatát továbbítja a rendszer. Ennek eredményeképpen az Azure AD-ben tárolt kivonat beszerzése, nem használható a helyszíni pass-the-hash támadás.

### <a name="security-considerations"></a>Biztonsági szempontok

A jelszavak szinkronizálásakor a jelszó egyszerű szöveges verziója nem érhető el a jelszókivonat-szinkronizálási szolgáltatás, az Azure AD vagy a kapcsolódó szolgáltatások.

A felhasználói hitelesítés az Azure AD-n, nem pedig a szervezet saját Active Directory-példányán történik. Az Azure AD-ben tárolt SHA256 jelszóadatok – az eredeti MD4-kivonat kivonata – biztonságosabbak, mint az Active Directoryban tároltadatok. Továbbá, mivel ez az SHA256 kivonat nem fejthető vissza, nem hozható vissza a szervezet Active Directory-környezetébe, és érvényes felhasználói jelszóként jelenik meg egy pass-the-hash támadás esetén.

### <a name="password-policy-considerations"></a>A jelszóházirendekkel kapcsolatos szempontok

A jelszókivonat-szinkronizálás engedélyezése kétféle jelszóházirendet érint:

* Jelszó összetettségére vonatkozó házirend
* Jelszó elévülési szabályzata

#### <a name="password-complexity-policy"></a>Jelszó összetettségére vonatkozó házirend

Ha a jelszókivonat-szinkronizálás engedélyezve van, a helyszíni Active Directory-példány jelszó-összetettségi házirendjei felülbírálják a felhőbeli összetettségi házirendeket a szinkronizált felhasználók számára. Használhatja az összes érvényes jelszavakat a helyszíni Active Directory-példány azure AD-szolgáltatások eléréséhez.

> [!NOTE]
> A közvetlenül a felhőben létrehozott felhasználók nak szóló jelszavakra továbbra is vonatkoznak a felhőben meghatározott jelszóházirendek.

#### <a name="password-expiration-policy"></a>Jelszó elévülési szabályzata

Ha egy felhasználó a jelszókivonat-szinkronizálás hatókörében van, alapértelmezés szerint a felhőfiók jelszava *soha nem jár le*.

Továbbra is bejelentkezhet a felhőszolgáltatásokba egy szinkronizált jelszó használatával, amely lejárt a helyszíni környezetben. A felhőbeli jelszó frissül a következő alkalommal, amikor módosítja a jelszót a helyszíni környezetben.

##### <a name="public-preview-of-the-enforcecloudpasswordpolicyforpasswordsyncedusers-feature"></a>Az *EnforceCloudPasswordPolicyForPasswordSyncedUsers* szolgáltatás nyilvános előzetes verziója

Ha vannak szinkronizált felhasználók, akik csak az Azure AD integrált szolgáltatásokkal kommunikálnak, és meg kell felelniük a jelszó elévülési szabályzatának is, kényszerítheti őket az Azure AD jelszó elévülési szabályzatának való megfelelésre az *EnforceCloudPasswordPolicyForPasswordSyncedUsers* funkció engedélyezésével.

Ha *az EnforceCloudPasswordPolicyForPasswordSyncedUsers* le van tiltva (ez az alapértelmezett beállítás), az Azure AD Connect a szinkronizált felhasználók PasswordPolicies attribútumát "DisablePasswordExpiration" értékre állítja. Ez történik minden alkalommal, amikor egy felhasználó jelszavát szinkronizálja, és arra utasítja az Azure AD figyelmen kívül hagyja a felhőjelszó elévülési szabályzatot, hogy a felhasználó. Az attribútum értékét az Azure AD PowerShell-modul segítségével ellenőrizheti a következő paranccsal:

`(Get-AzureADUser -objectID <User Object ID>).passwordpolicies`


Az EnforceCloudPasswordPolicyForPasswordSyncedUsers szolgáltatás engedélyezéséhez futtassa a következő parancsot az MSOnline PowerShell modul használatával az alábbi módon. Az alábbi módon kell beírnia az igen t az Engedélyezés paramétert:

```
Set-MsolDirSyncFeature -Feature EnforceCloudPasswordPolicyForPasswordSyncedUsers
cmdlet Set-MsolDirSyncFeature at command pipeline position 1
Supply values for the following parameters:
Enable: yes
Confirm
Continue with this operation?
[Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): y
```

Ha engedélyezve van, az Azure AD nem `DisablePasswordExpiration` megy minden szinkronizált felhasználó az érték eltávolítása a PasswordPolicies attribútumból. Ehelyett az érték az `None` egyes felhasználók következő jelszó-szinkronizálása során lesz beállítva, amikor legközelebb módosítják a jelszavukat a helyszíni AD-ben.  

Javasoljuk, hogy engedélyezze az EnforceCloudPasswordPolicyForPasswordSyncedUsers szolgáltatást a jelszókivonat-szinkronizálás engedélyezése előtt, hogy a `DisablePasswordExpiration` jelszókivék kezdeti szinkronizálása ne adja hozzá a passwordPolicies attribútum értékét a felhasználók számára.

Az alapértelmezett Azure AD jelszóházirend megköveteli a felhasználóktól, hogy 90 naponta módosítsák a jelszavukat. Ha az AD-ben megadott házirend is 90 nap, a két szabályzatnak meg kell egyeznie. Ha azonban az AD-szabályzat nem 90 nap, frissítheti az Azure AD jelszóházirendet, hogy megfeleljen a Set-MsolPasswordPolicy PowerShell parancs használatával.

Az Azure AD regisztrált tartományonként külön jelszóeljárati szabályzatot támogat.

Figyelmeztetés: Ha vannak szinkronizált fiókok, amelyek nem lejáró jelszavakat az Azure AD-ben, explicit módon hozzá kell adnia az `DisablePasswordExpiration` értéket a PasswordPolicies attribútum a felhasználói objektum az Azure AD-ben.  Ezt a következő parancs futtatásával teheti meg.

`Set-AzureADUser -ObjectID <User Object ID> -PasswordPolicies "DisablePasswordExpiration"`

> [!NOTE]
> Ez a funkció jelenleg nyilvános előzetes verzióban érhető el.
> A Set-MsolPasswordPolicy PowerShell parancs nem működik összevont tartományokon. 

#### <a name="public-preview-of-synchronizing-temporary-passwords-and-force-password-change-on-next-logon"></a>Az ideiglenes jelszavak és a "Jelszómódosítás kényszerítése a következő bejelentkezéskor" szinkronizálásának nyilvános előzetes verziója

Jellemző, hogy a felhasználót arra kényszerítik, hogy az első bejelentkezés során módosítsa a jelszavát, különösen a rendszergazdai jelszó visszaállítása után.  Ez általában az "ideiglenes" jelszó beállításának nevezett, és az Active Directoryban (AD) lévő felhasználói objektum "A felhasználónak meg kell változtatnia a jelszót a következő bejelentkezéskor" jelzőjének ellenőrzésével egészül ki.
  
Az ideiglenes jelszó funkció segít annak biztosításában, hogy a hitelesítő adatok tulajdonjogának átadása az első használatkor befejeződjön, hogy minimálisra csökkentse azt az időtartamot, amely alatt egynél több személy ismeri ezt a hitelesítő adatot.

Az ideiglenes jelszavak támogatásához az Azure AD a szinkronizált felhasználók számára, engedélyezheti a *ForcePasswordChangeOnLogOn* funkciót, futathata a következő parancsot az Azure AD Connect kiszolgálón:

`Set-ADSyncAADCompanyFeature  -ForcePasswordChangeOnLogOn $true`

> [!NOTE]
> A felhasználó kényszerítése a jelszó következő bejelentkezéskori módosítására egyszerre szükséges jelszómódosításra.  Az Azure AD Connect önmagában nem veszi fel a jelszómódosítási jelzőt; ez kiegészíti az észlelt jelszóváltozás során előforduló jelszó kivonatoló szinkronizálás.

> [!CAUTION]
> Ezt a funkciót csak akkor használja, ha az SSPR és a Jelszó-visszaírás engedélyezve van a bérlőn.  Ez azért van így, hogy ha egy felhasználó módosítja a jelszavát az SSPR-en keresztül, akkor szinkronizálni fogja az Active Directoryval.

> [!NOTE]
> Ez a funkció jelenleg nyilvános előzetes verzióban érhető el.

#### <a name="account-expiration"></a>Fiók lejárata

Ha a szervezet a accountExpires attribútumot használja a felhasználói fiók kezelés részeként, ez az attribútum nem szinkronizálódik az Azure AD-vel. Ennek eredményeképpen egy lejárt Active Directory-fiók egy jelszókivonat-szinkronizálásra konfigurált környezetben továbbra is aktív lesz az Azure AD-ben. Azt javasoljuk, hogy ha a fiók lejárt, egy munkafolyamat-művelet nek el kell indítania egy PowerShell-parancsfájlt, amely letiltja a felhasználó Azure AD-fiókját (használja a [Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) parancsmag). Ezzel szemben, ha a fiók be van kapcsolva, az Azure AD-példánybe be kell kapcsolni.

### <a name="overwrite-synchronized-passwords"></a>Szinkronizált jelszavak felülírása

A rendszergazda manuálisan alaphelyzetbe állíthatja a jelszót a Windows PowerShell használatával.

Ebben az esetben az új jelszó felülbírálja a szinkronizált jelszót, és a felhőben definiált összes jelszóházirend az új jelszóra vonatkozik.

Ha ismét módosítja a helyszíni jelszót, az új jelszó szinkronizálódik a felhővel, és felülírja a manuálisan frissített jelszót.

A jelszó szinkronizálása nincs hatással az Azure-felhasználó, aki be van jelentkezve. A jelenlegi felhőszolgáltatási munkamenetet nem érinti azonnal a szinkronizált jelszóváltozás, amely akkor következik be, amikor ön be van jelentkezve egy felhőszolgáltatásba. A KMSI meghosszabbítja ennek a különbségnek az időtartamát. Ha a felhőszolgáltatás megköveteli, hogy újra hitelesítse magát, meg kell adnia az új jelszót.

### <a name="additional-advantages"></a>További előnyök

- A jelszókivonat-szinkronizálás általában egyszerűbb, mint egy összevonási szolgáltatás. Nem igényel további kiszolgálókat, és kiküszöböli a felhasználók hitelesítéséhez magas rendelkezésre állású összevonási szolgáltatástól való függést.
- A jelszókivonat-szinkronizálás az összevonás mellett is engedélyezhető. Tartalékként is használható, ha az összevonási szolgáltatás kimaradást tapasztal.

## <a name="password-hash-sync-process-for-azure-ad-domain-services"></a>Az Azure AD tartományi szolgáltatások jelszókivonat-szinkronizálási folyamata

Ha az Azure AD tartományi szolgáltatások használatával örökölt hitelesítést biztosít a Kerberos, LDAP vagy NTLM használatához, néhány további folyamat a jelszókivonat-szinkronizálási folyamat részét képezi. Az Azure AD Connect a következő további folyamatot használja a jelszókijelentések azure AD-vel való szinkronizálásához az Azure AD-ben való használatra:

> [!IMPORTANT]
> Az Azure AD Connect csak telepíteni és konfigurálni a helyszíni AD DS-környezetekkel való szinkronizáláshoz. Nem támogatott az Azure AD Connect telepítése egy Azure AD DS által felügyelt tartományban az objektumok Azure AD-vel való szinkronizálásához.
>
> Az Azure AD Connect csak akkor szinkronizálja az örökölt jelszókitagokat, ha engedélyezi az Azure AD DS-t az Azure AD-bérlőszámára. A következő lépések nem használatosak, ha csak az Azure AD Connect használatával szinkronizálja a helyszíni Activeangeles-beli DS-környezetet az Azure AD-vel.
>
> Ha az örökölt alkalmazások nem használnak NTLM-hitelesítést vagy LDAP egyszerű kötéseket, azt javasoljuk, hogy tiltsa le az NTLM jelszókivonat-szinkronizálást az Azure AD DS-hez. További információ: [A gyenge titkosítási csomagok letiltása és az NTLM hitelesítő adatok kivonatának szinkronizálása.](../../active-directory-domain-services/secure-your-domain.md)

1. Az Azure AD Connect lekéri a nyilvános kulcsot a bérlő iadapéldányához az Azure AD tartományi szolgáltatások.
1. Amikor egy felhasználó módosítja a jelszavát, a helyszíni tartományvezérlő két attribútumban tárolja a jelszómódosítás eredményét (kih-jeit):
    * *unicodePwd* az NTLM jelszó kivonat.
    * *a* Kerberos jelszókivonat kiegészítő hitelesítő adatait.
1. Az Azure AD Connect észleli a jelszó változásokat a címtár replikációs csatornán keresztül (attribútum változásokat kell replikálni más tartományvezérlőkre).
1. Minden olyan felhasználó esetében, akinek a jelszava megváltozott, az Azure AD Connect a következő lépéseket hajtja végre:
    * Véletlenszerű AES 256 bites szimmetrikus kulcsot hoz létre.
    * Véletlenszerű inicializálási vektort hoz létre, amely a titkosítás első köréhez szükséges.
    * Kinyeri a Kerberos jelszókivonatokat a *supplementalCredentials attribútumokból.*
    * Ellenőrzi az Azure AD tartományi szolgáltatások biztonsági *konfigurációját SyncNtlmPasswords* beállítás.
        * Ha ez a beállítás le van tiltva, véletlenszerű, magas entrópia NTLM-kivonatot hoz létre (amely eltér a felhasználó jelszavától). Ezt a kivonatot ezután a *kiegészítőCrendetials* attribútum pontos Kerberos jelszókivonataival kombinálja egy adatstruktúrába.
        * Ha engedélyezve van, a *unicodePwd* attribútum értékét a *supplementalCredentials* attribútum kinyert Kerberos jelszókivonataival egyetlen adatstruktúrába egyesíti.
    * Az AES szimmetrikus kulccsal titkosítja az egyetlen adatstruktúrát.
    * Titkosítja az AES szimmetrikus kulcsot a bérlő I. AD domain services nyilvános kulcshasználatával.
1. Az Azure AD Connect továbbítja a titkosított AES szimmetrikus kulcsot, a jelszókivét eket tartalmazó titkosított adatstruktúrát és az inicializálási vektort az Azure AD-be.
1. Az Azure AD tárolja a titkosított AES szimmetrikus kulcsot, a titkosított adatstruktúrát és a felhasználó inicializálási vektort.
1. Az Azure AD leküldéses a titkosított AES szimmetrikus kulcs, a titkosított adatstruktúra és az inicializálási vektor egy belső szinkronizálási mechanizmus egy titkosított HTTP-munkameneten keresztül az Azure AD tartományi szolgáltatások.
1. Az Azure AD tartományi szolgáltatások lekéri a bérlői példány személyes kulcsát az Azure Key Vaultból.
1. Az Azure AD tartományi szolgáltatások minden egyes titkosított adathalmaz (amely egyetlen felhasználó jelszavának módosítását képviseli) esetében a következő lépéseket hajtja végre:
    * A személyes kulcsát használja az AES szimmetrikus kulcs visszafejtéséhez.
    * Az AES szimmetrikus kulcsot és az inicializálási vektort használja a jelszókivéteket tartalmazó titkosított adatstruktúra visszafejtésére.
    * Az Azure AD tartományi szolgáltatások tartományvezérlőjére kapott Kerberos-jelszó-kiírásokat írja. A kivét a rendszer a felhasználói objektum *azon supplementalCredentials* attribútumába menti, amely az Azure AD tartományi szolgáltatások tartományvezérlő nyilvános kulcsára van titkosítva.
    * Az Azure AD tartományi szolgáltatások az Azure AD tartományi szolgáltatások tartományvezérlőjére kapott NTLM-jelszókivonatot írja. A kivonat ot a rendszer a felhasználói objektum *unicodePwd* attribútumába menti, amely az Azure AD tartományi szolgáltatások tartományvezérlő nyilvános kulcsára van titkosítva.

## <a name="enable-password-hash-synchronization"></a>Jelszókivonat szinkronizálásának engedélyezése

>[!IMPORTANT]
>Ha az AD FS-ről (vagy más összevonási technológiákról) a jelszókivonat-szinkronizálásra vált, javasoljuk, hogy kövesse [az itt](https://aka.ms/adfstophsdpdownload)közzétett részletes telepítési útmutatónkat.

Ha az Azure AD Connectet az **Express Settings** beállítással telepíti, a jelszókivonat-szinkronizálás automatikusan engedélyezve lesz. További információ: [Az Azure AD Connect használatának első lépései expressz beállítások használatával](how-to-connect-install-express.md)című témakörben olvashat.

Ha egyéni beállításokat használ az Azure AD Connect telepítésekor, a jelszókivonat-szinkronizálás elérhető a felhasználói bejelentkezési lapon. További információ: [Az Azure AD Connect egyéni telepítése.](how-to-connect-install-custom.md)

![Jelszókivonat szinkronizálásának engedélyezése](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Jelszókivonat-szinkronizálás és FIPS
Ha a kiszolgáló a Federal Information Processing Standard (FIPS) szerint zárolva van, akkor az MD5 le van tiltva.

**Ha engedélyezni szeretné az MD5-öt a jelszókivonat-szinkronizáláshoz, hajtsa végre az alábbi lépéseket:**

1. Nyissa meg a %programfiles%\Azure AD Sync\Bin webhelyet.
2. Nyissa meg a miiserver.exe.config.-t.
3. Lépjen a fájl végén lévő konfigurációs/futásidejű csomópontra.
4. Adja hozzá a következő csomópontot:`<enforceFIPSPolicy enabled="false"/>`
5. Mentse a módosításokat.

Referenciaként ez a kódrészlet az, aminek így kell kinéznie:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

A biztonságról és a FIPS-ről az [Azure AD jelszókivonat-szinkronizálása, a titkosítás és a FIPS-megfelelőség című témakörben](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)talál.

## <a name="troubleshoot-password-hash-synchronization"></a>Jelszókivonat-szinkronizálás – problémamegoldás
Ha problémái vannak a jelszókivonat-szinkronizálással, olvassa el [a Jelszókivonat-szinkronizálás hibaelhárítása című témakört.](tshoot-connect-password-hash-synchronization.md)

## <a name="next-steps"></a>További lépések
* [Azure AD Connect szinkronizálása: Szinkronizálási beállítások testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
* [Lépésről lépésre történő telepítési terv beszerzése az ADFS-ről a jelszókivonat-szinkronizálásra való áttéréshez](https://aka.ms/authenticationDeploymentPlan)
