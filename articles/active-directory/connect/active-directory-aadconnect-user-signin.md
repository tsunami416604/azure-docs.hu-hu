---
title: "Az Azure AD Connect: Felhasználói bejelentkezés |} Microsoft Docs"
description: "Az Azure AD Connect felhasználói bejelentkezés az egyéni beállításokhoz."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 4670ec3cacd8d69a4ed59aa2bbbeb2e5c893f173
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Az Azure AD Connect felhasználói bejelentkezés lehetőségei
Az Azure Active Directory (Azure AD) Connect lehetővé teszi, hogy a felhasználókat, hogy jelentkezzen be a felhő- és a helyszíni erőforrásokhoz ugyanazt a jelszót. Ez a cikk ismerteti az alapvető fogalmakat minden identitás modell segítségével válassza ki az Azure AD bejelentkezés használni kívánt identitását.

Ha már ismeri az Azure AD identity modell, és szeretné, hogy egy adott módszerrel kapcsolatos további, tekintse meg a megfelelő hivatkozásra:

* [Jelszókivonat-szinkronizálást](#password-synchronization) rendelkező [zökkenőmentes egyszeri bejelentkezést (SSO)](active-directory-aadconnect-sso.md)
* [Áteresztő hitelesítés](active-directory-aadconnect-pass-through-authentication.md) rendelkező [zökkenőmentes egyszeri bejelentkezést (SSO)](active-directory-aadconnect-sso.md)
* [Összevont egyszeri Bejelentkezést (az Active Directory összevonási szolgáltatások (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)

> [!NOTE] 
> Fontos megjegyezni, hogy az Azure AD összevonási konfigurálásával, megbízhatósági kapcsolatot hoz létre az Azure AD-bérlő és a összevont tartományok között. A megbízhatósági összevont tartományt a felhasználók hozzáférhetnek a Azure AD felhőalapú erőforrásokhoz a bérlő belül.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>A felhasználói bejelentkezési módszer a szervezet kiválasztása
A legtöbb szervezet számára, hogy csak szeretné engedélyezni a felhasználói bejelentkezés az Office 365, SaaS-alkalmazásokhoz és más Azure AD-alapú erőforrások az alapértelmezett jelszó kivonatát szinkronizálási beállítás ajánlott. Egyes szervezetek azonban rendelkezik egy adott oka, hogy nem használhatja ezt a beállítást. Azok vagy egy összevont bejelentkezési beállítást használhatja, például az AD FS vagy átmenő hitelesítés. Az alábbi táblázat segítségével segít meghozni a megfelelő választás.

Telepítenem kell | Egyszeri bejelentkezési modellel PHS-ben| ESP-alapú egyszeri| AD FS |
 --- | --- | --- | --- |
Új felhasználói, lépjen kapcsolatba, és a felhőbe a helyszíni Active Directoryban automatikus szinkronizálása.|x|x|x|
A bérlő az Office 365 hibrid forgatókönyvek beállítása.|x|x|x|
Engedélyezze a felhasználók bejelentkezhetnek és elérhetik a felhőszolgáltatások a helyszíni jelszó használatával.|x|x|x|
Egyszeri bejelentkezés megvalósítása a vállalati hitelesítő adatok használatával.|x|x|x|
Győződjön meg arról, hogy a jelszavak nélkül a felhőben vannak tárolva.||x *|x|
A helyi multi-factor Authentication hitelesítés megoldások engedélyezése.|||x|

* Segítségével egy lightweight ügynök.

### <a name="password-hash-synchronization"></a>Jelszókivonat-szinkronizálást
Jelszókivonat-szinkronizálást, a kivonatokat a felhasználói jelszavak rendszer szinkronizálja a helyszíni Active Directoryból az Azure AD. Jelszavak módosulnak, és az újraindítás a helyszíni, az új jelszó kivonatok szinkronizálva az Azure AD azonnal, hogy a felhasználók mindig használhatja ugyanazt a jelszót a felhőben található erőforrásokat és a helyszíni erőforrások. A jelszavak soha nem küld az Azure AD vagy az Azure AD-szövegként tárolt. Jelszókivonat-szinkronizálást együtt jelszóvisszaírás segítségével engedélyezheti az önkiszolgáló jelszó-változtatási Azure AD-ben.

Emellett engedélyezheti [zökkenőmentes SSO](active-directory-aadconnect-sso.md) a tartományhoz csatlakoztatott számítógépeken, amelyek a vállalati hálózaton lévő felhasználók számára. Az engedélyezett felhasználók az egyszeri bejelentkezést, csak kell adjon meg egy felhasználónevet segítségével biztonságos hozzáférés felhőben található erőforrásokat.

![Jelszókivonat-szinkronizálást](./media/active-directory-aadconnect-user-signin/passwordhash.png)

További információkért lásd: a [Jelszókivonat-szinkronizálást](active-directory-aadconnectsync-implement-password-synchronization.md) cikk.

### <a name="pass-through-authentication"></a>Átmenő hitelesítés
Átmenő hitelesítéssel a felhasználó jelszava összevetni a helyszíni Active Directory-tartományvezérlő. A jelszó megtalálható az Azure AD semmilyen formában nem szükséges. Ez lehetővé teszi a helyi házirendek óra bejelentkezési korlátozásokat, például a felhőalapú hitelesítés során kiértékelendő szolgáltatások.

Áteresztő hitelesítés egy egyszerű ügynök a helyszíni környezetben a Windows Server 2012 R2 tartományhoz gép használja. Ez az ügynök a jelszó érvényesítése kéréseket figyeli. Bejövő portra is meg kell nyitni az internethez nem igényel.

Emellett engedélyezheti egyszeri bejelentkezéshez a felhasználók számára a tartományhoz csatlakoztatott számítógépeken, amelyek a vállalati hálózatban találhatók. Az engedélyezett felhasználók az egyszeri bejelentkezést, csak kell adjon meg egy felhasználónevet segítségével biztonságos hozzáférés felhőben található erőforrásokat.
![Áteresztő hitelesítés](./media/active-directory-aadconnect-user-signin/pta.png)

További információkért lásd:
- [Áteresztő hitelesítés](active-directory-aadconnect-pass-through-authentication.md)
- [Egyszeri bejelentkezés](active-directory-aadconnect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Amely egy új vagy meglévő farmot használ, a Windows Server 2012 R2 AD FS összevonási
Az összevont bejelentkezés a felhasználók bármikor beléphet az Azure AD-alapú szolgáltatásokat, helyszíni jelszavukat. Amikor nem a vállalati hálózaton, azok nem is kell adja meg a jelszavukat. Az AD FS összevonási beállítás használatával telepítheti az AD FS a Windows Server 2012 R2 új vagy meglévő farmhoz. Ha egy meglévő farmra megadását választja, az Azure AD Connect a farm és az Azure AD közötti megbízhatósági kapcsolatot konfigurálja, hogy a felhasználók bejelentkezhetnek.

<center>![Összevonás az AD FS a Windows Server 2012 R2 rendszerben](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Összevonás az AD FS a Windows Server 2012 R2 telepítése

Ha telepít egy új farmot, lesz szüksége:

* Egy Windows Server 2012 R2 kiszolgálóra az összevonási kiszolgáló.
* A webalkalmazás-Proxy kiszolgáló a Windows Server 2012 R2.
* A .pfx-fájlját egy SSL-tanúsítványt a kívánt összevonási szolgáltatás neve. Például: fs.contoso.com.

Ha telepít egy új farmot, vagy egy meglévő farmot használ, meg kell:

* Helyi rendszergazdai hitelesítő adatokat az összevonási kiszolgálókon.
* Helyi rendszergazdai hitelesítő adatokat egyetlen (nincsenek tartományhoz csatlakoztatva) munkacsoport-kiszolgálón, hogy szeretné-e telepíteni a webalkalmazás-Proxy szerepkör a.
* A gépet, a varázslóban a tudjanak csatlakozni más számítógépekhez, hogy szeretné, hogy az AD FS vagy webalkalmazás-Proxy telepítése a Windows Remote Management használatával.

További információkért lásd: [egyszeri bejelentkezés konfigurálása az AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs).

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Jelentkezzen be egy korábbi AD FS vagy egy harmadik féltől származó megoldás használatával
Ha már konfigurálta az felhő bejelentkezhet egy korábbi verzióját (például az AD FS 2.0) AD FS vagy egy harmadik féltől származó összevonási szolgáltató használatával, ha szeretné, hagyja ki a felhasználó-bejelentkezés konfigurálása az Azure AD Connect használatával. Ez lehetővé teszi a legutóbbi szinkronizálás és más képességek az Azure AD Connect beolvasása közben továbbra is a meglévő megoldást használni a bejelentkezéshez.

További információkért lásd: a [az Azure AD külső összevonási kompatibilitási lista](active-directory-aadconnect-federation-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Felhasználói bejelentkezés és a felhasználó egyszerű felhasználóneve
### <a name="understanding-user-principal-name"></a>Understanding egyszerű felhasználónév
Az Active Directory az alapértelmezett egyszerű felhasználónév (UPN) utótagját a tartományhoz, ahol a felhasználói fiók létrehozták a DNS-nevét. A legtöbb esetben ez a tartománynév, amely a vállalati tartományhoz, az interneten néven van regisztrálva. Azonban több UPN-utótagot is hozzáadhat Active Directory – tartományok és megbízhatósági kapcsolatok segítségével.

A felhasználó a felhasználónév formátuma a username@domain. Például "contoso.com" nevű Active Directory-tartomány, a John nevű felhasználó lehet az egyszerű felhasználónév "john@contoso.com". A felhasználó egyszerű Felhasználónevét RFC 822 alapul. Bár az UPN- és e-mailek osztani ugyanazt a formátumot, egy felhasználó egyszerű Felhasználóneve értékének lehetnek nem ugyanaz, mint a felhasználó e-mail címe.

### <a name="user-principal-name-in-azure-ad"></a>Az Azure AD-felhasználó egyszerű felhasználóneve
Az Azure AD Connect varázsló a userPrincipalName attribútum használ, vagy lehetővé teszi, hogy adja meg az attribútum (egyéni telepítés) használható a helyszíni egyszerű felhasználónevet az Azure ad-ben. Ez az érték az Azure AD aláírására szolgál. Ha a userPrincipalName attribútum értéke nem felel meg egy ellenőrzött tartomány Azure AD-ben, majd az Azure AD felülírja az alapértelmezett. onmicrosoft.com érték.

Minden címtárat az Azure Active Directoryban egy beépített a tartománynevet, a formátum contoso.onmicrosoft.com Azure vagy más Microsoft-szolgáltatások használatának megkezdésében lehetővé tévő tartalmaz. Javításához, és a bejelentkezés során tapasztal élmény egyszerűsítése egyéni tartományok használatával. Információ az Azure AD-ben egyéni tartománynevek és a tartomány ellenőrzése: [az egyéni tartománynév hozzáadása az Azure Active Directory](../add-custom-domain.md#add-the-custom-domain-name-to-your-directory).

## <a name="azure-ad-sign-in-configuration"></a>Azure AD-bejelentkezés konfigurálása
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Az Azure AD-bejelentkezés konfigurálása az Azure AD Connecttel
Az Azure AD bejelentkezés során tapasztal élmény attól függ, hogy az Azure AD is megegyezhet, a felhasználói UPN-utótag van folyamatban szinkronizált az Azure AD-címtárban lévő ellenőrzött egyéni tartomány közül az egyik felhasználó. Az Azure AD Connect segítséget nyújt a beállításokat az Azure AD-bejelentkezés, amíg úgy, hogy a felhasználói bejelentkezési élmény a felhőben található hasonló a helyszíni.

Az Azure AD Connect sorolja fel az UPN-utótagot, amely a tartományok megadása, és összehasonlítja azokat az egyéni tartományt az Azure ad-ben. Majd segít a megfelelő művelettel, amely kell állítani.
Az Azure AD bejelentkezési oldal megjeleníti az UPN-utótagot, amely a helyszíni Active Directory határozza meg, és minden utótag elleni megfelelő állapotát jeleníti meg. Az állapot értékei a következők egyike lehet:

| Állapot | Leírás | Beavatkozás szükséges |
|:--- |:--- |:--- |
| Hitelesítve |Az Azure AD Connect található megfelelő ellenőrzött tartományához Azure AD-ben. A tartomány minden felhasználó a helyszíni hitelesítő adatokkal jelentkezhetnek be. |Nincs szükség beavatkozásra. |
| Nincs ellenőrizve |Az Azure AD Connect található egyező egyéni tartományt az Azure ad-ben, de nem ellenőrzi, hogy. Ebben a tartományban a felhasználói UPN-utótagot változnak az alapértelmezett. onmicrosoft.com utótag szinkronizálás, ha a tartomány nem ellenőrzése után. | [Az egyéni tartomány ellenőrzéséhez az Azure ad-ben.](../add-custom-domain.md#verify-the-custom-domain-name-in-azure-ad) |
| Nincs hozzáadva |Az Azure AD Connect egyéni tartományok, amelyek megfelelnek az egyszerű Felhasználónévi utótagot nem található. Ebben a tartományban a felhasználói UPN-utótagot változnak az alapértelmezett. onmicrosoft.com utótag, ha a tartomány nem hozzáadja, és ellenőrizte az Azure-ban. | [Adja hozzá, és ellenőrizze, hogy megfelel-e az egyszerű Felhasználónévi utótagot az egyéni tartománynév.](../add-custom-domain.md) |

Az Azure AD bejelentkezési oldal sorolja fel a helyszíni Active Directory és a megfelelő egyéni tartományhoz az Azure AD az aktuális ellenőrzési állapotú meghatározott UPN-utótagot. Egyéni telepítés, mostantól kiválaszthatja a felhasználó egyszerű felhasználóneve attribútumát a a **az Azure AD-bejelentkezés** lap.

![Az Azure AD bejelentkezési oldalára](./media/active-directory-aadconnect-user-signin/custom_azure_sign_in.png)

A frissítés gombra kattintva újból beolvasni az egyéni tartományok legfrissebb állapotának az Azure AD kattinthat.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>A felhasználó egyszerű felhasználóneve attribútumát kiválasztásával az Azure ad-ben
Az attribútum userPrincipalName, amely a felhasználók használni, amikor bejelentkeznek az Azure AD és az Office 365. Ellenőrizze a tartomány (más néven UPN-utótagot), az Azure ad-ben a felhasználók szinkronizálása előtt használt.

Határozottan javasoljuk, hogy őrizze meg az alapértelmezett attribútum userPrincipalName. Ha ez az attribútum útválasztást, és nem lehet ellenőrizni, majd úgy is ki lehet egy másik attribútum (például e-mail), amely tartalmazza a bejelentkezési azonosítót. Ez más néven a másik azonosító. A másik azonosító értékének igazodnia kell az RFC 822 szabvány. Egyszeri jelszó és a összevonási Egyszeri bejelentkezési megoldásként használható a másik azonosító.

> [!NOTE]
> A másik azonosító használata nem felel meg az összes Office 365 számítási feladattal. További információkért lásd: [másodlagos bejelentkezési azonosító beállítása](https://technet.microsoft.com/library/dn659436.aspx).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Egyéni tartomány különböző állapotok és azok hatása az az Azure bejelentkezési élmény
Nagyon fontos tudni, hogy az Azure AD-címtárát az egyéni tartomány állapotok közötti kapcsolatot, és az UPN-utótagot, amelyek a helyszíni definiálva. A különböző módokon lehetséges az Azure bejelentkezési pedig ugorjunk, ha meg van-szinkronizálás beállítása az Azure AD Connect használatával.

A következő információkat tegyük fel, hogy a rendszer szerepel a helyszíni címtár UPN--részeként például egyszerű Felhasználónévi utótagot contoso.com érintett user@contoso.com.

###### <a name="express-settingspassword-hash-synchronization"></a>Express beállítások/Jelszókivonat-szinkronizálást
| Állapot | Az Azure-bejelentkezés a felhasználói élmény hatása |
|:---:|:--- |
| Nincs hozzáadva |Ebben az esetben nem contoso.com tartozó egyéni tartomány hozzá lett adva az Azure AD-címtárban. Egyszerű felhasználónév a helyszíni utótaggal rendelkező felhasználók @contoso.com nem fogja tudni használni a helyszíni egyszerű Felhasználónévvel jelentkezzen be az Azure-bA. Ezek helyette kell őket az Azure ad által biztosított hozzáadása az alapértelmezett Azure AD-címtár utótagja új egyszerű felhasználónév használata. Például, ha a felhasználók számára az Azure Active directory azurecontoso.onmicrosoft.com, majd a helyi felhasználó van szinkronizálása user@contoso.com kap egy egyszerű user@azurecontoso.onmicrosoft.com. |
| Nincs ellenőrizve |Ebben az esetben van egy egyéni tartománynév contoso.com, amely fel van véve az Azure AD-címtár. Azonban ez még nincs ellenőrizve. Ha azokat, amelyek a felhasználók szinkronizálását a tartomány ellenőrzése nélkül, majd a felhasználók hozzá fog rendelni egy új UPN az Azure AD csakúgy, mint a "Nem hozzáadott" forgatókönyvben. |
| Hitelesítve |Ebben az esetben van egy egyéni tartománynév contoso.com már hozzáadott és ellenőrizni az egyszerű Felhasználónévi utótagot az Azure AD-ben. Felhasználók tudják a helyszíni egyszerű felhasználónév használata, például user@contoso.com, jelentkezzen be Azure azok van-e szinkronizálva az Azure AD számára. |

###### <a name="ad-fs-federation"></a>AD FS összevonási
Nem hozható létre egy összevonási az alapértelmezett. onmicrosoft.com tartományt az Azure AD vagy az Azure ad-ben nem ellenőrzött egyéni tartományt. Jelenik meg az Azure AD Connect varázsló, ha az összevonási létrehozásához nem ellenőrzött tartományt, majd az Azure AD Connect jelenít meg a szükséges rekordok hozható létre, amelyen a DNS-kiszolgáló a tartományban található. További információkért lásd: [összevonási kijelölt Azure AD-tartomány ellenőrzése](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Ha a felhasználó bejelentkezési lehetőséget választotta **az AD FS összevonási**, akkor telepíteni kell az Azure AD összevonási létrehozásának folytatásához egyéni tartományt. A leírását ez azt jelenti, hogy kell-e azt egy egyéni tartomány contoso.com, az Azure AD-címtár szerepel.

| Állapot | A felhasználó Azure bejelentkezési élmény hatása |
|:---:|:--- |
| Nincs hozzáadva |Az Azure AD Connect ebben az esetben a egyszerű Felhasználónévi utótagot contoso.com az Azure AD-címtár nem található egyező egyéni tartományt. Kell adnia egy egyéni tartománynév contoso.com, ha felhasználók jelentkezhetnek be az AD FS segítségével, a helyszíni egyszerű Felhasználónévvel (például user@contoso.com). |
| Nincs ellenőrizve |Ebben az esetben az Azure AD Connect rákérdez, hogyan ellenőrizheti a tartomány egy későbbi időpontban a szükséges adatokat. |
| Hitelesítve |Ebben az esetben megkezdheti a konfigurációjával kapcsolatban további műveletek nélkül. |

## <a name="changing-the-user-sign-in-method"></a>A felhasználói bejelentkezési módszer módosítása
A felhasználói bejelentkezési módszer módosíthatja a összevonási, Jelszókivonat-szinkronizálást vagy átmenő hitelesítést követően érhetők el az Azure AD Connectben a varázsló az Azure AD Connect a kezdeti konfigurációs feladatok használatával. Futtassa újra az Azure AD Connect varázslót, és láthatja, hogy elvégezhető feladatok listáját. Válassza ki **felhasználói bejelentkezés módosítása** feladatot a listából.

![Felhasználói bejelentkezés módosítása](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

A következő oldalon kéri a hitelesítő adatokat kér az Azure AD.

![Csatlakozás az Azure AD szolgáltatáshoz](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

Az a **felhasználói bejelentkezés** lapon, válassza ki a kívánt felhasználói bejelentkezés.

![Csatlakozás az Azure AD szolgáltatáshoz](./media/active-directory-aadconnect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Ha a Jelszókivonat-szinkronizálást hajt végre egy ideiglenes kapcsoló csak, majd válassza ki a **felhasználói fiókok nem konvertálhatók** jelölőnégyzetet. A beállítás nem ellenőrzése átkonvertálja összevont felhasználók, és több órát is igénybe vehet.
>
>

## <a name="next-steps"></a>Következő lépések
- További információ [a helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md).
- További információ [az Azure AD Connect tervezési alapelvei](active-directory-aadconnect-design-concepts.md).
