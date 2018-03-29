---
title: Jelszókivonat-szinkronizálást és az Azure AD Connect-szinkronizálás megvalósítása |} Microsoft Docs
description: Hogyan működik a Jelszókivonat-szinkronizálást és beállításával kapcsolatos információkat biztosít.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: billmath
ms.openlocfilehash: c223091e423d0f342f14424c58d6b7447cda50e8
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Jelszókivonat-szinkronizálást és az Azure AD Connect-szinkronizálás megvalósítása
A cikkből megtudhatja, hogy szeretné-e a felhőalapú Azure Active Directory (Azure AD) példánya a helyszíni Active Directory-példányról a felhasználói jelszavakat szinkronizálja.

## <a name="what-is-password-hash-synchronization"></a>Mi az, hogy a Jelszókivonat-szinkronizálást
A valószínűségi hány különböző jelszó megjegyzése kell arról, hogy van tiltva az Elfelejtett jelszó miatt a munka kapcsolódik. A további jelszavakat, ne feledje, hogy minél nagyobb a hibalehetőségek törlésére egy kell. Kérdések és a jelszó alaphelyzetbe állítását és egyéb jelszó-problémák hívások igény a legtöbb segélyszolgálat erőforrást.

Jelszókivonat-szinkronizálást egy olyan funkció használatával a felhőalapú Azure Active Directory helyszíni példánya származó felhasználói jelszavakat szinkronizálja AD-példányban.
E szolgáltatás használatával jelentkezzen be Azure AD szolgáltatásokba, mint az Office 365, a Microsoft Intune, CRM Online-hoz és Azure Active Directory tartományi szolgáltatások (az Azure AD DS). Bejelentkezik a szolgáltatás által ugyanazzal a jelszóval bejelentkezni a helyszíni Active Directory-példányát használja.

![Mi az az Azure AD Connect?](./media/active-directory-aadconnectsync-implement-password-hash-synchronization/arch1.png)

Csökkentse a jelszavak, a felhasználóknak kell csak egy karbantartása. Jelszókivonat-szinkronizálást nyújt segítséget:

* A felhasználók a termelékenység növelése.
* Csökkenti az ügyfélszolgálati kiadásokat.  

Is ha úgy dönt, hogy használjon [az Active Directory összevonási szolgáltatások (AD FS) összevonásának](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), akkor is beállíthat Jelszókivonat-szinkronizálást biztonsági abban az esetben, ha az AD FS infrastruktúra sikertelen lesz.

Jelszókivonat-szinkronizálást a címtár-szinkronizálási szolgáltatás, az Azure AD Connect szinkronizálási szolgáltatás által megvalósított kiterjesztése. Jelszókivonat-szinkronizálást használ a környezetben, akkor kell:

* Azure AD Connect telepítése.  
* Konfigurálja a címtár-szinkronizálás a helyszíni Active Directory példány és az Azure Active Directory-példány között.
* Engedélyezze a Jelszókivonat-szinkronizálást.

További részletekért lásd: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).

> [!NOTE]
> Azure Active Directory tartományi szolgáltatások FIPS és a jelszó Jelszókivonat-szinkronizálást konfigurált kapcsolatos további tudnivalókért lásd: a "Jelszókivonat-szinkronizálást, és FIPS" a cikk későbbi részében.
>
>

## <a name="how-password-hash-synchronization-works"></a>Jelszókivonat-szinkronizálást működése
Az Active Directory tartományi szolgáltatások tárolja a jelszavakat a kivonat értéke megjelenítése a tényleges felhasználói jelszó formájában. A kivonat értéke egy egyirányú matematikai függvény eredményeként (a *kivonatoló algoritmus*). Nincs módszer eredménye egy egyirányú működnek, mint az egyszerű szövegként a jelszavak visszaállítását. Jelentkezzen be a helyszíni hálózat Jelszókivonat nem használható.

A jelszó szinkronizálásához a Azure AD Connect szinkronizálási szolgáltatás a Jelszókivonat kiolvassa a a helyszíni Active Directory-példányban. További biztonsági feldolgozási a Jelszókivonat vonatkozik előtt szinkronizálja az Azure Active Directory hitelesítési szolgáltatás. Jelszavak szinkronizálódnak, felhasználónkénti alapon és időrendben sorolja fel.

A tényleges adatfolyama a jelszó kivonatát szinkronizálási folyamat hasonlít a szinkronizálás a felhasználói adatok, például DisplayName vagy E-mail címekre. Azonban a jelszavak szinkronizálódnak gyakrabban egyéb attribútumai a szabványos directory szinkronizálási ablakot. A jelszó kivonatát szinkronizálási folyamat 2 percenként fut. Ez a folyamat gyakoriságának nem módosítható. A jelszó szinkronizálása során felülírja a meglévő felhőalapú jelszót.

Az első alkalommal engedélyezi a jelszó kivonatát szinkronizálási szolgáltatás hajtja végre: a jelszavak az összes hatókör felhasználót egy kezdeti szinkronizálása. Szinkronizálni kívánt felhasználói jelszavak egy részét nem definiálhat explicit módon.

A helyszíni jelszó módosítása a módosított jelszó legyenek szinkronizálva, általában a percek.
A jelszó kivonatát szinkronizálási szolgáltatás automatikusan újrapróbálkozik a sikertelen szinkronizálás kísérletek. A jelszó tett kísérlet során hiba lép fel, ha hiba történt az Eseménynapló van bejelentkezve.

A jelszó-szinkronizálás nincs hatással van a jelenleg bejelentkezett felhasználó.
A felhőalapú szolgáltatás jelenlegi munkamenet azonnal nem érinti a szinkronizált jelszómódosítás, amely akkor fordul elő, amikor be van jelentkezve egy felhőalapú szolgáltatás. Azonban a felhőszolgáltatás meg újra hitelesíteni kell, amikor meg kell adnia az új jelszót.

A felhasználó kell adnia a vállalati hitelesítő adatok még egyszer hitelesítéséhez az Azure AD, függetlenül attól, hogy azok van bejelentkezve a vállalati hálózathoz. A minta minimalizálható, azonban ha a felhasználó kijelöli a tárolás során is garantálják me aláírt jel (KMSI) jelölőnégyzetet. Ez a beállítás egy rövid ideig hitelesítési megkerüli munkamenetcookie-t állítja be. KMSI viselkedés engedélyezve, vagy az Azure AD-rendszergazda letiltotta.

> [!NOTE]
> A jelszó-szinkronizálás csak az objektum típusa felhasználó Active Directory esetén támogatott. Az iNetOrgPerson objektumtípus nem támogatott.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Hogyan működik a Jelszókivonat-szinkronizálást részletes leírása
A következő részletes Jelszókivonat-szinkronizálást működését ismerteti az Active Directory és az Azure AD között.

![A jelszó részletes folyamata](./media/active-directory-aadconnectsync-implement-password-hash-synchronization/arch3.png)


1. Két percenként az AD Connect-kiszolgáló jelszó kivonat-szinkronizálási ügynök kérelmek tárolt jelszókivonatainak (unicodePwd attribútum) keresztül normál tartományvezérlőt [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) replikációs protokoll által használt adatok szinkronizálása közötti tartományvezérlők. A szolgáltatás fiók beszerzése a jelszót a kivonatok rendelkeznie kell Directory változások replikálása és replikálja könyvtár az összes AD jogosultságaitól (alapértelmezés szerint a telepítés).
2. Küldés előtt, a tartományvezérlő a MD4 Jelszókivonat titkosítja egy kulcs, amely használatával a [MD5](http://www.rfc-editor.org/rfc/rfc1321.txt) RPC munkamenetkulcsot és az egy kivonatát. Majd küld az eredmény a jelszó kivonatát szinkronizálási ügynök RPC-n keresztül. A tartományvezérlő is átadása a védőérték a szinkronizálási ügynök által a tartományvezérlő replikációs protokoll segítségével, hogy az ügynök tudja majd visszafejteni a boríték történjen.
3.  A jelszó kivonatát szinkronizálási ügynök van a titkosított boríték, miután használ [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) és a védőérték vissza az eredeti MD4 formátumában fogadott adatok visszafejtéséhez a kulcs létrehozásához. A jelszó kivonatát szinkronizálási ügynök nem rendelkezik a tiszta szöveges jelszót. A jelszó kivonatát szinkronizálási ügynök használt MD5 használatát szigorúan replikációs protokoll kompatibilitás a DC-vel, és csak használja a helyszínen, a tartományvezérlő és a jelszó kivonatát szinkronizálási ügynök között.
4.  A jelszó kivonatát szinkronizálási ügynök bontja ki a 16 bájtos bináris Jelszókivonat 64 bájt átalakításával első 32 bájtos hexadecimális karakterlánc, majd ezt átalakítják Ez a karakterlánc a kivonat biztonsági UTF-16 kódolással bináris formátumra.
5.  A jelszó kivonatát szinkronizálási ügynök ad hozzá egy salt egy 10 bájtos hossza védőérték, a 64 bájtos bináris fokozott védelme érdekében az eredeti kivonatoló álló.
6.  A jelszó kivonatát szinkronizálási ügynök ezután egyesíti a MD4 kivonatoló túl a védőérték, és a bemeneti be azt a [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) függvény. 1000 ismétlésének a [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) létre kivonatoló algoritmust használnak. 
7.  A jelszó kivonatát szinkronizálási ügynök az eredményül kapott 32 bájtos kivonatoló vesz igénybe, összefűzi a védőérték, mind az SHA-256 lépésszám rá (az Azure ad használatára), majd továbbítja a karakterláncot az Azure AD Connect, az Azure AD SSL-en keresztül.</br> 
8.  Ha egy felhasználó megpróbál bejelentkezni az Azure AD, és beírja a jelszavát, a jelszó fut a azonos MD4 + védőérték + PBKDF2 + HMAC-SHA256 folyamat keresztül. Az eredményül kapott kivonatoló egyezik a Azure AD-ben tárolt, ha a felhasználó a helyes jelszót került, és van hitelesítve. 

>[!Note] 
>Az eredeti MD4-kivonata nem továbbít, az Azure AD. Ehelyett az eredeti MD4 kivonatoló SHA256-kivonata továbbít. Ennek eredményeképpen az Azure AD-ben tárolt kivonatoló kapjuk meg, ha azt nem használható az helyszíni a pass-the-hash támadások.

### <a name="how-password-hash-synchronization-works-with-azure-active-directory-domain-services"></a>Jelszókivonat-szinkronizálást az Azure Active Directory tartományi szolgáltatások működése
A jelszó kivonatát szinkronizálási szolgáltatás is használhatja a helyszíni jelszavak szinkronizálása [Azure Active Directory tartományi szolgáltatások](../../active-directory-domain-services/active-directory-ds-overview.md). Ebben a forgatókönyvben az Azure Active Directory tartományi szolgáltatások példány hitelesíti a felhasználók a felhőben a helyszíni Active Directory-példányban elérhető összes módszer. Ebben a forgatókönyvben a élménye hasonlít az Active Directory áttelepítési eszköz (ADMT) használatával a helyszíni környezetben.

### <a name="security-considerations"></a>Biztonsági szempontok
Ha a jelszó-szinkronizálás, a jelszót egyszerű szövegként a jelszó kivonatát szinkronizálási szolgáltatás, az Azure ad Szolgáltatásba, vagy a kapcsolódó szolgáltatások nem kommunikál.

Felhasználóhitelesítés kerül sor, szemben az Azure AD, nem pedig a szervezet saját Active Directory-példányban ellen. Ha a szervezete alkotnak, a helyi elhagyása, vegye figyelembe, hogy az SHA-256 jelszó adatokat tárolja, az Azure AD--, sem jelszóadatok kétségei egy eredeti MD4 kivonatoló--kivonata jóval biztonságosabb Mi az Active Directoryban tárolja. További az SHA256-kivonata nem fejthető vissza, mert az nem lehet teszik vissza a szervezet Active Directory-környezetben a és mutatják be, a pass-the-hash támadások érvényes felhasználói jelszó.





### <a name="password-policy-considerations"></a>Jelszó házirenddel kapcsolatos megfontolások
A Jelszókivonat-szinkronizálást engedélyezésével érintett jelszóházirendek két típusa van:

* Jelszó bonyolultságára vonatkozó házirendet
* Jelszó-elévülési szabályzatának

#### <a name="password-complexity-policy"></a>Jelszó bonyolultságára vonatkozó házirendet  
Ha engedélyezve van a Jelszókivonat-szinkronizálást, a jelszó összetettségét házirendek a helyszíni Active Directory-példányban felülbírálása összetettsége házirendek a szinkronizált felhasználók számára a felhőben. Használhatja az összes érvényes jelszavak a helyszíni Active Directory-példányát az Azure AD-szolgáltatások eléréséhez.

> [!NOTE]
> Jelszavak felhasználók közvetlenül a felhőben létrehozott lépnek továbbra is jelszóházirendet a felhőben.

#### <a name="password-expiration-policy"></a>Jelszó-elévülési szabályzatának  
Ha egy felhasználó hatókörébe tartozó Jelszókivonat-szinkronizálást, a felhő fiók jelszavát értéke *nem jár le*.

Továbbra is jelentkezzen be a felhőszolgáltatás, amely a helyszíni környezetben lejárt szinkronizált jelszavakat. A felhő jelszó frissül, amikor legközelebb a helyszíni környezetben a jelszó módosítása.

#### <a name="account-expiration"></a>Fiók lejárati
Ha a szervezet használja a accountExpires attribútumot felhasználóifiók-kezelés részeként, vegye figyelembe, hogy ez az attribútum nincs szinkronizálva az Azure ad Szolgáltatásba. Ennek eredményeképpen Jelszókivonat-szinkronizálást konfigurált környezetben lejárt Active Directory-fiókkal marad aktív az Azure ad-ben. Azt javasoljuk, hogy ha a fiók lejárt, a munkafolyamat-művelet indíthat el egy PowerShell-parancsfájlt, amely letiltja a felhasználó Azure AD-fiókot. Ezzel szemben ha a fiók be van kapcsolva, az Azure AD-példányhoz be kell kapcsolni.

### <a name="overwrite-synchronized-passwords"></a>Szinkronizált jelszavakat felülírása
A rendszergazda Windows PowerShell használatával manuálisan új jelszó.

Ebben az esetben az új jelszót felülírja a szinkronizált jelszavát, és a felhőben definiált összes jelszóházirendet vonatkoznak az új jelszót.

Ha módosítja a helyszíni jelszavát újra, az új jelszó szinkronizálása a felhőbe, és felülírja a manuálisan frissített jelszót.

A jelszó-szinkronizálás nincs hatással van bejelentkezve az Azure felhasználó rendelkezik. A felhőalapú szolgáltatás jelenlegi munkamenet azonnal nem érinti a szinkronizált jelszómódosítás, amely be van jelentkezve cloud Service szolgáltatásra. KMSI terjeszti ki ezt a különbséget időtartama. Ha a felhőszolgáltatás szükséges, hogy újra hitelesíteni, meg kell adnia az új jelszót.

### <a name="additional-advantages"></a>További előnyei

- Általában a Jelszókivonat-szinkronizálást megvalósítása egyszerűbb, mint egy összevonási szolgáltatás. Nincs szükség semmilyen további kiszolgálókat, és megszünteti a felhasználók hitelesítésére, magas rendelkezésre állású összevonási szolgáltatás függés. 
- Jelszókivonat-szinkronizálást is engedélyezheti összevonási mellett. Azt is használható tartalékként Ha az összevonási szolgáltatás kimaradás.












## <a name="enable-password-hash-synchronization"></a>Jelszókivonat-szinkronizálást engedélyezése
Ha telepíti az Azure AD Connect használatával a **Gyorsbeállítások** , Jelszókivonat-szinkronizálást automatikusan engedélyezve van. További részletekért lásd: [Ismerkedés az Azure AD Connect a gyorsbeállításokkal](active-directory-aadconnect-get-started-express.md).

Ha az Azure AD Connect telepítése egyéni beállításokat használja, ha a felhasználó bejelentkezési oldal a Jelszókivonat-szinkronizálást érhető el. További részletekért lásd: [az Azure AD Connect egyéni telepítési](active-directory-aadconnect-get-started-custom.md).

![Jelszókivonat-szinkronizálást engedélyezése](./media/active-directory-aadconnect-get-started-custom/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Jelszókivonat-szinkronizálást, és FIPS
Ha a kiszolgáló zárolva lett megfelelően Federal Information Processing Standard (FIPS), MD5 is le van tiltva.

**Jelszókivonat-szinkronizálást az MD5 engedélyezéséhez hajtsa végre az alábbi lépéseket:**

1. Ugrás a %programfiles%\Azure Active Sync\Bin.
2. Open miiserver.exe.config.
3. Nyissa meg a konfiguráció/runtime csomópontot a fájl végén.
4. Adja hozzá a következő csomópont: `<enforceFIPSPolicy enabled="false"/>`
5. Mentse a módosításokat.

Összehasonlításul a részlet, mi azt kell hasonlítania:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

További információ a biztonsági és FIPS: [AAD jelszó-szinkronizálás, a titkosítás és a FIPS megfelelőségi](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>Jelszókivonat-szinkronizálást hibaelhárítása
Ha problémába ütközik a Jelszókivonat-szinkronizálást, lásd: [hibaelhárítása a Jelszókivonat-szinkronizálást](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md).

## <a name="next-steps"></a>További lépések
* [Azure AD Connect szinkronizálása: szinkronizálási beállítások testreszabása](active-directory-aadconnectsync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
