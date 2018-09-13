---
title: 'Az Azure AD Connect: Felhasználói bejelentkezés |} A Microsoft Docs'
description: Az Azure AD Connect felhasználói bejelentkezési az egyéni beállításokhoz.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 1bf20ebec5792fc01c62966a0454c37c3c950182
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35923549"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Az Azure AD Connect felhasználói bejelentkezési lehetőségek
Az Azure Active Directory (Azure AD) Connect lehetővé teszi, hogy a felhasználókat, hogy jelentkezzen be a felhő- és a helyszíni erőforrásokhoz ugyanazt a jelszót. Ez a cikk azokat a fogalmakat minden identitás-modell segítségével válassza ki az Azure ad-ben való bejelentkezéshez használni kívánt identitást.

Ha már ismeri az Azure AD identity modellel, és a egy adott módszerrel kapcsolatos további, tekintse meg a megfelelő hivatkozásra:

* [A Jelszókivonat-szinkronizálás](#password-hash-synchronization) az [zökkenőmentes egyszeri bejelentkezés (SSO)](active-directory-aadconnect-sso.md)
* [Az átmenő hitelesítés](active-directory-aadconnect-pass-through-authentication.md) az [zökkenőmentes egyszeri bejelentkezés (SSO)](active-directory-aadconnect-sso.md)
* [Összevont egyszeri bejelentkezés (az Active Directory összevonási szolgáltatások (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Összevonás a pingfederate-tel](#federation-with-pingfederate)

> [!NOTE] 
> Fontos megjegyezni, hogy az Azure ad összevonási konfigurál, megbízhatósági kapcsolatot létesít az Azure AD-bérlő és a összevont tartományok között. A bizalmi kapcsolat összevont tartományt a felhasználók hozzáférhetnek a bérlőn belül az Azure AD felhőalapú erőforrásokhoz.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>A felhasználói bejelentkezési módszer a szervezet kiválasztása
Az első döntése megvalósítása az Azure AD Connect megválasztása használt hitelesítési módszert, a felhasználók fognak használni a bejelentkezéshez. Fontos, hogy úgy dönt, hogy a megfelelő módszer, amely megfelel a szervezete biztonsági és speciális követelmények. Hitelesítési fontos, mert azt fogja ellenőrizni a felhasználói identitások hozzáférés az alkalmazásokhoz és a felhőbeli adatok. Válassza ki a megfelelő hitelesítési módszert, fontolja meg a idő, a meglévő infrastruktúra, az összetettséget és a költség végrehajtási választott kell. Ezek a tényezők különböző minden szervezet számára, és idővel változhatnak.

Azure ad-ben az alábbi hitelesítési módszereket támogatja: 

* **Felhőalapú hitelesítés** – Ha úgy dönt, ezt a hitelesítési módszert az Azure AD kezeli a hitelesítési folyamat a felhasználó jelentkezzen be. A felhőalapú hitelesítés két lehetőség közül választhat: 
   * **A Jelszókivonat-szinkronizálás (nál)** -Jelszókivonat-szinkronizálás lehetővé teszi a felhasználók ugyanazt a felhasználónevet és jelszót használják-e a helyszíni használata mellett az Azure AD Connect további infrastruktúra üzembe helyezése nélkül. 
   * **Az átmenő hitelesítés (ESP)** – Ez a beállítás hasonlít a Jelszókivonat-szinkronizálás, de egy egyszerű jelszó érvényesítése használatával a helyszíni szoftver ügynökök erős biztonsági és megfelelőségi szabályzatokat használó szervezetek számára nyújt.
* **Az összevont hitelesítés** – Ha úgy dönt, ezt a hitelesítési módszert az Azure AD a hitelesítési folyamat egy külön megbízható hitelesítési rendszere lesz kiosztják, mint például az AD FS vagy egy harmadik féltől származó összevonási rendszer ellenőrzi a felhasználó a bejelentkezést. 

A legtöbb szervezet számára, amely csak a felhasználói bejelentkezés az Office 365-höz, SaaS-alkalmazásokhoz és más Azure AD-alapú erőforrások engedélyezni szeretné javasoljuk, hogy az alapértelmezett jelszó Jelszókivonat szinkronizálása beállítást.
 
Hitelesítési módszerként válassza a részletes információkért lásd: [válassza ki a megfelelő hitelesítési módszert az Azure Active Directory hibrid identitáskezelési megoldás](../../security/azure-ad-choose-authn.md)

### <a name="password-hash-synchronization"></a>Jelszókivonat szinkronizálása
A Jelszókivonat-szinkronizálás a felhasználói jelszavak kivonatait vannak szinkronizálva a helyszíni Active Directoryból az Azure AD. Jelszavak módosítják, vagy alaphelyzetbe állítása a helyszínen, az új jelszavak kivonatait szinkronizált Azure ad-hez közvetlenül, hogy a felhasználók mindig használhatja ugyanazt a jelszót a felhőbeli erőforrásokat és a helyszíni erőforrásokhoz. A jelszavak soha nem küldeni az Azure AD vagy az Azure ad-ben szövegként tárolt. Használhatja együtt a jelszóvisszaírást a Jelszókivonat-szinkronizálás engedélyezése az önkiszolgáló jelszó-visszaállítás, az Azure ad-ben.

Emellett engedélyezheti [közvetlen egyszeri bejelentkezés](active-directory-aadconnect-sso.md) a tartományhoz csatlakoztatott gépeket, amelyek a vállalati hálózaton lévő felhasználók számára. Az egyszeri bejelentkezést az engedélyezett felhasználók csak meg kell adnia egy felhasználónevet, amelyekkel biztonságos felhőalapú erőforrások eléréséhez.

![Jelszókivonat szinkronizálása](./media/active-directory-aadconnect-user-signin/passwordhash.png)

További információkért lásd: a [Jelszókivonat-szinkronizálás](active-directory-aadconnectsync-implement-password-hash-synchronization.md) cikk.

### <a name="pass-through-authentication"></a>Átmenő hitelesítés
Az átmenő hitelesítést a felhasználó jelszava ellenőrzi a helyszíni Active Directory-vezérlőn. A jelszó nem kell lennie az Azure AD bármilyen formában szerepel. Ez lehetővé teszi a helyi házirendek óra bejelentkezési korlátozásokat, például a felhőalapú hitelesítés során ki kell értékelni services.

Az átmenő hitelesítés a helyszíni környezetben a Windows Server 2012 R2 tartományhoz csatlakoztatott gép egy egyszerű ügynököt használ. Ez az ügynök a jelszó érvényesítése kéréseket figyeli. Az Internet felé bejövő portra nincs szükség.

Emellett engedélyezheti egyszeri bejelentkezést a tartományhoz csatlakoztatott gépeket, amelyek a vállalati hálózaton lévő felhasználók számára. Az egyszeri bejelentkezést az engedélyezett felhasználók csak meg kell adnia egy felhasználónevet, amelyekkel biztonságos felhőalapú erőforrások eléréséhez.
![Az átmenő hitelesítés](./media/active-directory-aadconnect-user-signin/pta.png)

További információkért lásd:
- [Az átmenő hitelesítés](active-directory-aadconnect-pass-through-authentication.md)
- [Egyszeri bejelentkezés](active-directory-aadconnect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Amely egy új vagy meglévő farmot használ a Windows Server 2012 R2 AD FS-Összevonás
Az összevont bejelentkezés a felhasználók a saját helyszíni jelszavak az Azure AD-alapú szolgáltatások jelentkezhetnek be. Közben, netán a vállalati hálózaton, azok nem is kell a jelszavát adja meg. Az összevonási lehetőség az AD FS használatával, egy új vagy meglévő és a Windows Server 2012 R2 AD FS-farm üzembe helyezése. Ha egy meglévő farmra megadását választja, az Azure AD Connect konfigurálja a farm és az Azure AD közötti bizalmi kapcsolat, úgy, hogy a felhasználók bejelentkezhetnek.

<center>![Összevonás az AD FS a Windows Server 2012 R2 rendszerben](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Összevonás az AD FS a Windows Server 2012 R2 telepítése

Ha telepít egy új farmot, lesz szüksége:

* Egy Windows Server 2012 R2 kiszolgálóra az összevonási kiszolgálóhoz.
* A webalkalmazás-Proxy a Windows Server 2012 R2 kiszolgálója.
* Egy .pfx fájlba egy SSL-tanúsítvány számára az importálni kívánt összevonási szolgáltatás neve. Például: fs.contoso.com.

Ha telepít egy új farmot, vagy egy meglévő farmot használ, meg kell:

* Helyi rendszergazdai hitelesítő adatokat az összevonási kiszolgálókon.
* Helyi rendszergazdai hitelesítő adatok bármely munkacsoport-kiszolgálók (nincsenek tartományhoz csatlakoztatva), hogy szeretne-e a webalkalmazás-Proxy szerepkör telepítése.
* A gép, amely futtatja a varázslót a lehessen csatlakozni más gépek, amikor szeretné az AD FS- vagy webalkalmazás-Proxy telepítése a Windows távoli felügyelet használatával.

További információkért lásd: [egyszeri bejelentkezés konfigurálása az AD FS-sel](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs).

### <a name="federation-with-pingfederate"></a>Összevonás a PingFederate-tel
Az összevont bejelentkezés a felhasználók a saját helyszíni jelszavak az Azure AD-alapú szolgáltatások jelentkezhetnek be. Közben, netán a vállalati hálózaton, azok nem is kell a jelszavát adja meg.

Az Azure Active Directoryval történő használatra a PingFederate konfigurálásáról további információkért lásd: [a PingFederate-integráció az Azure Active Directory és az Office 365](https://www.pingidentity.com/AzureADConnect)

Az Azure AD Connect használatával a PingFederate beállításával kapcsolatos további információkért lásd: [az Azure AD Connect testreszabott telepítése](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-pingfederate)

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Jelentkezzen be egy korábbi AD FS vagy egy külső megoldás használatával
Ha már konfigurálta a felhőbeli bejelentkezéshez az Active Directory összevonási szolgáltatások (például AD FS 2.0-s) vagy egy harmadik féltől származó összevonási szolgáltató korábbi verzióját, ha szeretné, hagyja ki a felhasználói bejelentkezés konfigurálása Azure AD Connecten keresztül. Ez lehetővé teszi, hogy a legújabb szinkronizálási és egyéb lehetőségeket az Azure AD Connect beolvasása közben továbbra is használja a meglévő megoldás a bejelentkezéshez.

További információkért lásd: a [az Azure AD külső összevonás kompatibilitási listája](active-directory-aadconnect-federation-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Felhasználói bejelentkezés és a felhasználó egyszerű neve
### <a name="understanding-user-principal-name"></a>Understanding egyszerű felhasználónév
Az Active Directoryban az alapértelmezett egyszerű felhasználónév (UPN) utótagja hol jött létre a felhasználói fióknak a tartomány DNS-nevét. A legtöbb esetben ez a tartománynév, amely regisztrálva van, a vállalati tartományhoz az interneten. Azonban több UPN-utótagot is hozzáadhat Active Directory-tartományok és megbízhatósági kapcsolatok segítségével.

A felhasználó egyszerű Felhasználónevét formátuma a username@domain. Például egy "contoso.com" nevű Active Directory-tartományban, John nevű felhasználó előfordulhat, hogy rendelkezik az egyszerű felhasználónév "john@contoso.com". A felhasználó egyszerű Felhasználónevét az RFC 822 alapul. Bár az UPN és e-mailben oszt meg ugyanazt a formátumot, az érték a felhasználó egyszerű Felhasználóneve előfordulhat, hogy, vagy nem lehet ugyanaz, mint a felhasználó e-mail-címe.

### <a name="user-principal-name-in-azure-ad"></a>Az Azure ad-ben a felhasználó egyszerű neve
Az Azure AD Connect varázsló a userPrincipalName attribútum használ, vagy megadhatja a attribútumot (egyéni telepítés) használható a helyszíni, egyszerű felhasználónév az Azure ad-ben. Ez az Azure ad-ben való bejelentkezéshez használt értéket. Ha a userPrincipalName attribútum értéke nem felel meg egy ellenőrzött tartomány Azure AD-ben, majd az Azure AD felülírja az alapértelmezett. onmicrosoft.com értéket.

Minden címtárat az Azure Active Directoryban tartalmaz egy beépített tartomány nevét, a következő formátumban: contoso.onmicrosoft.com, amely lehetővé teszi Azure-ban vagy más Microsoft-szolgáltatások használatának megkezdésében. Javítása, és a bejelentkezési élmény egyszerűsítése érdekében egyéni tartományok használatával. Információ az egyéni tartománynevek az Azure ad-ben és a egy tartomány ellenőrzéséhez: [az egyéni tartománynév hozzáadása az Azure Active Directory](../fundamentals/add-custom-domain.md#add-the-custom-domain-name-to-your-directory).

## <a name="azure-ad-sign-in-configuration"></a>Azure AD-bejelentkezés konfigurálása
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Az Azure AD bejelentkezés konfigurálása az Azure AD Connecttel
Az Azure AD bejelentkezési felület attól függ, hogy az Azure AD meg tudja a az Azure AD-címtárban lévő ellenőrzött egyéni tartománynak egy szinkronizált felhasználói UPN-utótag. Az Azure AD Connect segítséget nyújt az Azure AD bejelentkezési beállításainak konfigurálása közben, hogy a felhasználói bejelentkezési élmény a felhőben hasonlít a helyszíni felhasználói élményt.

Az Azure AD Connect sorolja fel, amely a tartományra határozza meg, és összehasonlítja azokat az egyéni tartomány az Azure AD UPN-utótagot. Ezután segít a megfelelő művelettel, amely állapotba kell helyezni.
Az Azure AD bejelentkezési oldal az UPN-utótagot, amely a helyszíni Active Directory határozza meg, és megjeleníti a megfelelő állapot minden utótag alapján sorolja fel. Az állapot értékei a következők egyike lehet:

| Állapot | Leírás | Beavatkozás szükséges |
|:--- |:--- |:--- |
| Ellenőrizve |Az Azure AD Connect található egy egyező ellenőrzött tartomány Azure AD-ben. Ebben a tartományban az összes felhasználó által a helyszíni hitelesítő adataikkal jelentkezhetnek be. |Nem kell módosítania. |
| Nincs ellenőrizve |Az Azure AD Connect talált egyező egyéni tartományt az Azure ad-ben, de nem ellenőrzi, hogy. Ez a tartomány felhasználói UPN-utótagját változnak az alapértelmezett. onmicrosoft.com utótag fog szerepelni, ha a tartomány nem ellenőrzött szinkronizálás után. | [Az egyéni tartomány ellenőrzése az Azure ad-ben.](../fundamentals/add-custom-domain.md#verify-the-custom-domain-name-in-azure-ad) |
| Nincs hozzáadva |Az Azure AD Connect nem talált egy egyéni tartományt, amely megfelelt annak az UPN-utótagot. Ez a tartomány felhasználói UPN-utótagját változnak az alapértelmezett. onmicrosoft.com utótag, ha a tartomány nem hozzáadva, és ellenőrizte az Azure-ban. | [Adja hozzá, és ellenőrizze az egyéni tartományt, amely megfelel az UPN-utótagot.](../fundamentals/add-custom-domain.md) |

Az Azure AD bejelentkezési oldal sorolja fel a helyszíni Active Directory és a kapcsolódó egyéni tartomány ellenőrzési aktuális állapotát az Azure AD-ben definiált UPN-utótagot. Egyéni telepítés, mostantól kiválaszthatja az egyszerű felhasználónév attribútum az a **az Azure AD-be** lapot.

![Az Azure AD bejelentkezési oldal](./media/active-directory-aadconnect-user-signin/custom_azure_sign_in.png)

Újból beolvasni a legutóbbi állapota, valamint az egyéni tartományok az Azure ad-ből a frissítés gombra kattinthat.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Az attribútum az egyszerű felhasználónév kiválasztása az Azure ad-ben
Az attribútum userPrincipalName attribútum, amelyet a felhasználók használhatják, amikor bejelentkeznek az Azure ad-ben, és Office 365-höz. Ellenőrizze a tartomány (más néven UPN-utótagot), mielőtt a felhasználók szinkronizálása az Azure AD-ben használt.

Javasoljuk, hogy őrizze meg a userPrincipalName alapértelmezett attribútum. Ez az attribútum útválasztást és nem lehet ellenőrizni, majd, hogy egy másik attribútum (például e-mail) választhatja, amely tárolja a bejelentkezési azonosítót. Ez az úgynevezett másodlagos azonosítóját. A másik azonosító értékének kell követnie az RFC 822 szabvány. A másik azonosító jelszavas egyszeri Bejelentkezést és összevonási Egyszeri bejelentkezési megoldásként is használható.

> [!NOTE]
> A másik azonosító használata nem kompatibilis az összes Office 365 számítási feladattal. További információkért lásd: [másik bejelentkezési azonosító konfigurálása](https://technet.microsoft.com/library/dn659436.aspx).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Egyéni tartomány különböző állapotait és azok hatása az Azure bejelentkezési élmény az
Nagyon fontos tudni, hogy az egyéni tartomány állapotok az Azure AD-címtárban közötti kapcsolatot, és az UPN-utótagot, amelyek meghatározott helyszíni. Vegyük a különböző módokon lehetséges Azure jelentkezzen be, hogy szinkronizálást állítson be az Azure AD Connect használatával.

A következő információkat, feltételezzük, hogy használja az érintett használatban van a helyi könyvtár UPN – részeként például egyszerű felhasználónév utótagja contoso.com user@contoso.com.

###### <a name="express-settingspassword-hash-synchronization"></a>Express beállításokat és a Jelszókivonat-szinkronizálás
| Állapot | Az Azure bejelentkezési felhasználói élmény hatása |
|:---:|:--- |
| Nincs hozzáadva |Ebben az esetben nincs egyéni tartomány contoso.com hozzá lett adva az Azure AD-címtárban. Egyszerű felhasználónév a helyszíni az utótaggal rendelkező felhasználók @contoso.com nem tudja használni a helyszíni egyszerű Felhasználónévvel jelentkezzen be az Azure-bA. Ehelyett rendelkeznek őket az Azure AD által biztosított alapértelmezett Azure AD-címtárat az utótag hozzáadása új UPN használatára. Például, ha a felhasználók számára az Azure AD directory azurecontoso.onmicrosoft.com, akkor a helyszíni felhasználót szinkronizál user@contoso.com kap egy egyszerű Felhasználóneve user@azurecontoso.onmicrosoft.com. |
| Nincs ellenőrizve |Ebben az esetben van egy egyéni a contoso.com tartományt, amely adnak hozzá a az Azure AD-címtárban. Azonban ez még nincs ellenőrizve. Lépjen tovább a felhasználók szinkronizálását a tartomány ellenőrzése nélkül, ha ezután a felhasználók hozzá fog rendelni egy új egyszerű Felhasználónevükkel Azure AD-ben csakúgy, mint a "Nincs hozzáadva" forgatókönyvben. |
| Ellenőrizve |Ebben az esetben van egy egyéni tartományt a contoso.com már hozzáadott és ellenőrizte az UPN-utótagot az Azure AD-ben. Azok a helyszínen egyszerű felhasználónév használata, például a felhasználók tudják user@contoso.com, miután azok már szinkronizált Azure ad-ben az Azure-bA bejelentkezni. |

###### <a name="ad-fs-federation"></a>Az AD FS összevonási
Egy összevonási nem hozható létre az alapértelmezett. onmicrosoft.com tartomány Azure AD-ben vagy az Azure ad-ben nem ellenőrzött egyéni tartományt. Az Azure AD Connect varázsló, ha nem ellenőrzött tartományok létrehozása az összevonási futtatja, majd az Azure AD Connect kéri a szükséges rekordokat a DNS-kiszolgálót üzemeltető tartomány létrehozni. További információkért lásd: [az összevonáshoz kiválasztott Azure AD-tartomány ellenőrzése](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

A felhasználói bejelentkezési lehetőség választásakor **és az AD FS összevonási**, akkor telepíteni kell egy egyéni tartományt az Azure AD összevonási létrehozásának folytatásához. Beszélgetéshez Ez azt jelenti, hogy egy egyéni tartományt a contoso.com hozzáadva az Azure AD-címtárban kell állnia.

| Állapot | A felhasználó Azure bejelentkezési élmény hatása |
|:---:|:--- |
| Nincs hozzáadva |Az Azure AD Connect ebben az esetben az egyszerű felhasználónév utótagja contoso.com az Azure AD-címtárban nem található egyező egyéni tartományt. Adja hozzá a contoso.com egy egyéni tartományt, ha a felhasználók jelentkezhetnek be az AD FS használatával a helyszíni egyszerű Felhasználónévvel kell kell (például user@contoso.com). |
| Nincs ellenőrizve |Ebben az esetben az Azure AD Connect kéri, hogyan ellenőrizheti a tartomány egy későbbi szakaszban a megfelelő adatokkal. |
| Ellenőrizve |Ebben az esetben akkor is lépjen tovább a konfigurációval további műveletek nélkül. |

## <a name="changing-the-user-sign-in-method"></a>A felhasználói bejelentkezési mód módosítása
Összevonási, a Jelszókivonat-szinkronizálás és átmenő hitelesítés az Azure AD Connect a varázsló az kezdeti konfigurálása után az Azure AD Connectben elérhető feladatok használatával módosíthatja a felhasználói bejelentkezés módját. Futtassa újra az Azure AD Connect varázslót, és látni fogja az elvégezhető feladatok listáját. Válassza ki **felhasználói bejelentkezés módosítása** feladatok listájából.

![Felhasználói bejelentkezés módosítása](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

A következő oldalon kéri, hogy az Azure ad hitelesítő adatok megadása.

![Csatlakozás az Azure AD szolgáltatáshoz](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

Az a **felhasználói bejelentkezés** lapon, válassza ki a kívánt felhasználói bejelentkezés.

![Csatlakozás az Azure AD szolgáltatáshoz](./media/active-directory-aadconnect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Ha csak a Jelszókivonat-szinkronizálást hajt végre egy ideiglenes kapcsolót, majd válassza ki a **felhasználói fiókok konvertálásának** jelölőnégyzetet. Minden felhasználó összevont konvertálja nem ellenőrzi a beállítást, és több órát is igénybe vehet.
>
>

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [a helyszíni identitások integrálása az Azure Active Directory](active-directory-aadconnect.md).
- Tudjon meg többet [az Azure AD Connect tervezési alapelvei](active-directory-aadconnect-design-concepts.md).
