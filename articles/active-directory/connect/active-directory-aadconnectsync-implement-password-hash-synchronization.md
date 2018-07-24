---
title: Jelszókivonat-szinkronizálás és az Azure AD Connect-szinkronizálás megvalósítása |} A Microsoft Docs
description: A Jelszókivonat-szinkronizálás működését, és hogyan állítható be arról nyújt tájékoztatást.
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
ms.date: 07/20/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 5d62eb4d5f43625b336ade68532cf734ef0cde6a
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214693"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Jelszókivonat-szinkronizálás és az Azure AD Connect-szinkronizálás megvalósítása
A cikk ismerteti, hogy az egy felhőalapú Azure Active Directory (Azure AD) példány egy helyszíni Active Directory-példányból származó felhasználói jelszavakat szinkronizálja szükséges információkat.

## <a name="what-is-password-hash-synchronization"></a>Mi az a Jelszókivonat-szinkronizálás
Annak a valószínűsége, hogy már letiltotta az Elfelejtett jelszó miatt a munkát a jelszavak számát, meg kell jegyeznie kapcsolódik. A további jelszavakat, ne feledje, hogy minél nagyobb a valószínűsége megfeledkezni egy kell. Kérdések és a jelszó alaphelyzetbe állítása és egyéb jelszó-kapcsolatos problémák hívások kereslet a legtöbb segélyszolgálat-erőforrást.

A Jelszókivonat-szinkronizálás funkciója szinkronizálása egy felhőbeli Azure-bA egy helyszíni Active Directory-példányból származó felhasználói jelszavakat használt AD-példányt.
Ez a funkció használatával jelentkezzen be az Azure AD szolgáltatások, például az Office 365-höz, a Microsoft Intune, CRM Online-hoz és az Azure Active Directory Domain Services (Azure AD DS). Jelentkezzen be a szolgáltatás használatával jelentkezzen be a helyszíni Active Directory-példányra ugyanazzal a jelszóval.

![Mi az az Azure AD Connect?](./media/active-directory-aadconnectsync-implement-password-hash-synchronization/arch1.png)

Csökkentse a jelszavak, a felhasználók csak egyetlen biztosítani kell. A Jelszókivonat-szinkronizálás segítségével:

* A felhasználók termelékenységének javítása.
* Csökkenti az ügyfélszolgálati kiadásokat.  

Emellett ha úgy dönt, hogy használja [összevonási az Active Directory összevonási szolgáltatások (AD FS)](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), igény szerint beállíthatja a Jelszókivonat-szinkronizálás biztonsági abban az esetben az AD FS-infrastruktúra nem sikerül.

A Jelszókivonat-szinkronizálás az a címtár szinkronizálási szolgáltatás által az Azure AD Connect-szinkronizálás megvalósítása a kiterjesztése. Jelszókivonat-szinkronizálást használ a környezetében, meg kell:

* Az Azure AD Connect telepítése.  
* Konfigurálja a címtár-szinkronizálás a helyszíni Active Directory-példányból, és az Azure Active Directory-példány között.
* Jelszókivonat-szinkronizálás engedélyezése.

További részletekért lásd: [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).

> [!NOTE]
> Az Azure Active Directory Domain Services FIPS és a jelszót a Jelszókivonat-szinkronizálás a konfigurált kapcsolatos további részletekért lásd "a Jelszókivonat-szinkronizálás és a FIPS" a cikk későbbi részében.
>
>

## <a name="how-password-hash-synchronization-works"></a>A Jelszókivonat-szinkronizálás működése
Az Active Directory tartományi szolgáltatások tárolja a jelszavak a tényleges felhasználói jelszó kivonat értéke reprezentációját formájában. A Jelszókivonat értéke egy egyirányú matematikai függvény eredménye (a *kivonatoló algoritmus*). Nincs semmilyen metódus vissza a jelszót egyszerű szöveges verziójának egy egyirányú függvény eredménye. Jelentkezzen be a helyszíni hálózat Jelszókivonat nem használható.

A jelszó szinkronizálása, az Azure AD Connect szinkronizálása a Jelszókivonat kigyűjti a helyszíni Active Directory-példányból. További biztonsági feldolgozási alkalmazza a rendszer a Jelszókivonat előtt, a rendszer szinkronizálja az Azure Active Directory hitelesítési szolgáltatás. Jelszavak szinkronizálódnak, felhasználónkénti alapon és időrendi sorrendben.

A tényleges adatok folyamatot, a jelszó Jelszókivonat-szinkronizálási folyamat a felhasználói adatok, például DisplayName vagy E-mail-címek szinkronizálásának hasonló. Azonban jelszavak gyakrabban, mint a standard szintű könyvtár szinkronizálási időszak más attribútumok vannak szinkronizálva. A jelszó Jelszókivonat-szinkronizálási folyamat 2 percenként fut. Ez a folyamat gyakoriságának nem módosítható. Jelszó szinkronizálásakor felülírja a meglévő cloud jelszót.

A jelszó Jelszókivonat szinkronizálása funkció engedélyezéséhez először az összes érintett felhasználót a jelszavak újbóli használatának kezdeti szinkronizálást végez. Nem lehet explicit módon meghatározni egy szinkronizálni kívánt felhasználói jelszavak részét.

Amikor módosítja egy a helyszíni jelszót, a módosított jelszó szinkronizált, leggyakrabban a percek alatt.
A jelszó Jelszókivonat-szinkronizálási szolgáltatás automatikusan újrapróbálkozik a sikertelen szinkronizálási kísérlet. Hiba esetén egy jelszó szinkronizálása tett kísérlet során hiba az eseménynaplóban naplózza.

A jelszó szinkronizálása nem befolyásolja az aktuálisan bejelentkezett felhasználó.
Az aktuális munkamenet a felhőalapú szolgáltatás azonnal nem érinti, amely akkor fordul elő, amikor be van jelentkezve felhőszolgáltatás szinkronizált jelszómódosítás. Azonban a felhőszolgáltatás újra hitelesíteni kell, amikor meg kell adnia az új jelszót.

A felhasználónak meg kell adnia vállalati hitelesítői adataikkal másodszor az Azure Active Directory, függetlenül attól, hogy azok jelentkezett be, hogy a vállalati hálózaton való hitelesítésére. Ezek a minta minimalizálható, azonban, ha a felhasználó a maradjak bejelentkezve megtartása bejelentkezéskor (KMSI) jelölőnégyzetet. Ez a beállítás egy munkamenetcookie-t, amely 180 napig hitelesítési megkerüli állítja be. KMSI viselkedés engedélyezhető vagy az Azure AD-rendszergazda letiltotta. Ezenfelül csökkentheti a jelszó utasításokat bekapcsolásával [közvetlen egyszeri bejelentkezés](active-directory-aadconnect-sso.md) amely automatikusan aláírja a felhasználók mikor legyenek a vállalati eszközeiket a vállalati hálózathoz csatlakozik.

> [!NOTE]
> Jelszó-szinkronizálás csak a támogatott a objektum típusa felhasználót az Active Directoryban. A iNetOrgPerson objektum típus nem támogatott.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Részletes leírása, hogyan működik a Jelszókivonat-szinkronizálás
Az alábbi részletes Jelszókivonat-szinkronizálás működését ismerteti az Active Directory és az Azure AD között.

![A folyamat részletes jelszó](./media/active-directory-aadconnectsync-implement-password-hash-synchronization/arch3.png)


1. Két percenként, a jelszó kivonatoló szinkronizálási ügynököt az AD Connect-kiszolgáló kér (az unicodePwd attribútum) tárolt jelszavak kivonatait egy tartományvezérlő, a standard keresztül [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx) adatok szinkronizálásához használt replikációs protokoll közötti tartományvezérlők. A szolgáltatás fiók a beszerzése a jelszót a kivonatok Címtárváltozások replikálása és replikálása összes AD jogosultságaitól (alapértelmezés szerint a telepítési) kell rendelkeznie.
2. Küldés előtt, a tartományvezérlő az MD4 Jelszókivonat titkosítja, amely egy kulcs használatával egy [MD5](http://www.rfc-editor.org/rfc/rfc1321.txt) az RPC munkamenetkulcs és a egy salt kivonatát. Majd elküldi az eredményt a jelszó Jelszókivonat-szinkronizálási ügynök RPC-n keresztül. A tartományvezérlő is továbbítja a védőérték a szinkronizálási ügynöknek a tartományvezérlő replikációs protokoll használatával, így az ügynök tudja majd visszafejteni a boríték.
3.  A jelszó Jelszókivonat-szinkronizálási ügynök, amelyet a titkosított boríték, követően használja [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) és a egy vissza az eredeti MD4 formátumára a fogadott adatok visszafejtéséhez kulcs létrehozásához. A jelszó Jelszókivonat-szinkronizálási ügynök pontján sem rendelkezik hozzáféréssel a tiszta szöveges jelszó. A jelszó kivonatoló szinkronizálási ügynök használatát MD5 szigorúan replikációs protokollal kompatibilis-e a tartományvezérlő, és csak használja a helyszínen, a tartományvezérlő és a jelszó Jelszókivonat-szinkronizálási ügynök között.
4.  A jelszó Jelszókivonat-szinkronizálási ügynök bontja ki a 16 bájtos bináris Jelszókivonat 64 bájt átalakításával első UTF-16 kódolással bináris biztonsági 32 bájtos hexadecimális karakterlánc, majd átalakítja a következő karakterláncot a kivonatot.
5.  A jelszó Jelszókivonat-szinkronizálási ügynök ad hozzá egy felhasználói Salt érték, egy 10 bájtos hossza Salt érték, a 64 bájtos bináris fokozott védelme érdekében az eredeti Jelszókivonat álló száma.
6.  A jelszó Jelszókivonat-szinkronizálási ügynök, majd egyesíti az MD4 kivonatoló és a egy felhasználó Salt érték, és bemenetekben be azt a [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) függvény. 1000 ismétlésének a [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) kulcsalgoritmus kivonatoló algoritmust használja. 
7.  A jelszó Jelszókivonat-szinkronizálási ügynök vesz igénybe az eredményül kapott 32 bájtos kivonat, mindkettő fűz össze a felhasználói Salt érték és az SHA256 számának ismétlések rá (használata az Azure AD), majd továbbítja a karakterláncot, az Azure AD Connect, az Azure ad-hez SSL-en keresztül.</br> 
8.  Ha egy felhasználó megpróbál bejelentkezni az Azure ad-hez, és beírja a jelszavát, a jelszó útján az azonos MD4 + védőérték + PBKDF2 + HMAC-SHA256 folyamat fut le. Az eredményül kapott ujjlenyomat megegyezik az Azure AD-ben tárolt kivonat, ha a felhasználó beírta a helyes jelszót, és van hitelesítve. 

>[!Note] 
>Az eredeti MD4 kivonatoló az Azure AD nem lesznek továbbítva. Ehelyett továbbítása az eredeti MD4 kivonatoló SHA256-kivonatát. Ennek eredményeképpen az Azure AD-ben tárolt hash nyerik, ha azt nem használható a helyszínen a pass-the-hash típusú támadás.

### <a name="how-password-hash-synchronization-works-with-azure-active-directory-domain-services"></a>Az Azure Active Directory Domain Services Jelszókivonat-szinkronizálás működése
A jelszó Jelszókivonat-szinkronizálási szolgáltatás is használhatja a helyszíni jelszavak szinkronizálása [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). Ebben a forgatókönyvben az Azure Active Directory Domain Services-példány hitelesíti a felhasználókat a felhőben a helyszíni Active Directory-példányában elérhető összes módszer. Ebben a forgatókönyvben élménye hasonlít az Active Directory áttelepítési eszköz (ADMT) használatával a helyszíni környezetben.

### <a name="security-considerations"></a>Biztonsági szempontok
Amikor jelszó-szinkronizálás, a jelszót egyszerű szöveges verziójában nem lesz közzétéve a jelszó kivonatoló szinkronizálási szolgáltatás az Azure AD vagy bármely olyan társított szolgáltatásokat.

Felhasználói hitelesítés történik, Azure AD-val, nem pedig a szervezet saját Active Directory-példányból ellen. Ha a szervezete jelszó adatokat bármely kapcsolatos elvárásainak alkotnak, így a helyi, vegye figyelembe, hogy az SHA256 jelszó adatok tárolása az Azure AD –, az eredeti MD4 kivonatoló--kivonatát jóval biztonságosabb, mint az Active Directoryban tárolja. További az SHA256-kivonat nem lehet visszafejteni, mert azt nem kell a szervezet Active Directory-környezetet állapotba és mutatják be, a pass-the-hash típusú támadások érvényes felhasználói jelszó.

### <a name="password-policy-considerations"></a>Jelszó házirenddel kapcsolatos megfontolások
Jelszókivonat-szinkronizálás engedélyezése által érintett jelszóházirendek két típusa van:

* Jelszó bonyolultsága házirend
* Jelszó-elévülési szabályzatának

#### <a name="password-complexity-policy"></a>Jelszó bonyolultsága házirend  
Ha a Jelszókivonat-szinkronizálás engedélyezve van, a jelszó összetettségi szabályzatok a helyszíni Active Directory-példányában felülbírálása összetettséget házirendek a szinkronizált felhasználók számára a felhőben. Használhatja az összes érvényes jelszavak a helyszíni Active Directory-példánynak az Azure AD-szolgáltatások eléréséhez.

> [!NOTE]
> Közvetlenül a felhőben létrehozott felhasználó jelszavát vonatkozik továbbra is jelszóházirendet a felhőben meghatározottak szerint.

#### <a name="password-expiration-policy"></a>Jelszó-elévülési szabályzatának  
Ha egy felhasználó a Jelszókivonat-szinkronizálás hatókörébe esik, a felhő-fiók jelszavát értéke *soha ne járjon le*.

Továbbra is a helyszíni környezetben lejárt szinkronizált jelszó használatával jelentkezzen be a cloud services. A felhő jelszó módosítja a jelszavát a helyszíni környezetben a következő alkalommal frissül.

#### <a name="account-expiration"></a>Fiók lejárati
Ha a szervezet a accountExpires attribútum felhasználóifiók-kezelés részeként használja, vegye figyelembe, hogy ez az attribútum nincs szinkronizálva az Azure ad-hez. Ennek eredményeképpen egy lejárt Active Directory-fiókot konfigurálni a Jelszókivonat-szinkronizálás környezetben továbbra is aktív lesz az Azure ad-ben. Azt javasoljuk, hogy ha a fiók lejárt, egy munkafolyamat-művelet indíthat el egy PowerShell-parancsprogram, amely letiltja a felhasználó Azure AD-fiókot (használja a [Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0) parancsmag). Ezzel szemben ha a fiók be van kapcsolva, az Azure AD-példányt kell bekapcsolni.

### <a name="overwrite-synchronized-passwords"></a>Szinkronizált jelszavak felülírása
A rendszergazda Windows PowerShell használatával manuálisan alaphelyzetbe állíthassa jelszavát.

Ebben az esetben az új jelszó felülírja a szinkronizált jelszavát, és a felhőben definiált összes jelszóházirendet érvényesek az új jelszót.

Ha módosítja a helyszíni jelszót újra, az új jelszó szinkronizálása a felhőbe, és ez a beállítás felülbírálja a manuálisan frissített jelszót.

A jelszó szinkronizálása nem befolyásolja a az Azure-felhasználó, aki be van jelentkezve. Az aktuális munkamenet a felhőalapú szolgáltatás azonnal nem érinti a szinkronizált jelszómódosítás, amíg egy felhőszolgáltatáshoz van bejelentkezve. KMSI kibővíti ezt a különbséget időtartamára. Ha a felhőszolgáltatás újra-hitelesítést igényel, meg kell adnia az új jelszót.

### <a name="additional-advantages"></a>További előnyök

- Általában a Jelszókivonat-szinkronizálás megvalósítása egyszerűbb, mint egy összevonási szolgáltatás. Nincs szükség semmilyen további kiszolgálókat, és kiküszöböli a felhasználók hitelesítése a magas rendelkezésre állású összevonási szolgáltatás függés.
- A Jelszókivonat-szinkronizálás összevonási mellett is engedélyezhető. Azt is használható tartalékként Ha az összevonási szolgáltatás szolgáltatáskimaradás következik be.

## <a name="enable-password-hash-synchronization"></a>Jelszókivonat-szinkronizálás engedélyezése

>[!IMPORTANT]
>A Jelszókivonat-szinkronizálást az AD FS (vagy más összevonási technológiákkal) telepít át, ha kifejezetten ajánljuk, hogy kövesse a részletes üzembe helyezési útmutató, közzétett [Itt](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Password%20Hash%20Synchronization.docx).

Amikor telepíti az Azure AD Connect használatával a **Gyorsbeállítások** , a Jelszókivonat-szinkronizálás automatikusan engedélyezve van. További részletekért lásd: [Ismerkedés az Azure AD Connecttel a gyorsbeállítások használatával](active-directory-aadconnect-get-started-express.md).

Ha az Azure AD Connect telepítése egyéni beállításokat használja, ha a felhasználó bejelentkezési oldalán a Jelszókivonat-szinkronizálás érhető el. További részletekért lásd: [az Azure AD Connect egyéni telepítési](active-directory-aadconnect-get-started-custom.md).

![Jelszókivonat-szinkronizálás engedélyezése](./media/active-directory-aadconnect-get-started-custom/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>A Jelszókivonat-szinkronizálás és a FIPS
A kiszolgáló zárolva lett megfelelően Federal információk Processing Standard (FIPS), ha MD5-tel is le van tiltva.

**A Jelszókivonat-szinkronizálás MD5 engedélyezéséhez hajtsa végre az alábbi lépéseket:**

1. Nyissa meg az AD %programfiles%\Azure Sync\Bin.
2. Open miiserver.exe.config.
3. Nyissa meg a fájl végén a konfiguráció-és futásidő-csomópont.
4. Adja hozzá a következő csomópont: `<enforceFIPSPolicy enabled="false"/>`
5. Mentse a módosításokat.

Ez a kódrészlet hivatkozásként, hogy milyen hasonlóan kell kinéznie:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

További információ a biztonsági és FIPS: [AAD jelszó-szinkronizálás, a titkosítás és a FIPS megfelelőségi](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/).

## <a name="troubleshoot-password-hash-synchronization"></a>A Jelszókivonat-szinkronizálás hibaelhárítása
Ha problémába ütközik a Jelszókivonat-szinkronizálás, [Jelszókivonat-szinkronizálás hibaelhárítása](active-directory-aadconnectsync-troubleshoot-password-hash-synchronization.md).

## <a name="next-steps"></a>További lépések
* [Az Azure AD Connect-szinkronizálás: szinkronizálási beállítások testreszabása](active-directory-aadconnectsync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
