---
title: 'Azure AD Connect: Felhasználói bejelentkezés | Microsoft dokumentumok'
description: Az Azure AD Connect felhasználói bejelentkezése egyéni beállításokhoz.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a08120b98c7a08bca50453df59df313b1645c5c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331264"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Az Azure AD Connect felhasználói bejelentkezési beállításai
Az Azure Active Directory (Azure AD) Connect lehetővé teszi a felhasználók számára, hogy jelentkezzen be a felhőbeli és a helyszíni erőforrások at ugyanazt a jelszót. Ez a cikk ismerteti az egyes identitásmodellek legfontosabb fogalmait, amelyek segítségével kiválaszthatja az Azure AD-be való bejelentkezéshez használni kívánt identitást.

Ha már ismeri az Azure AD identitásmodellt, és szeretne többet megtudni egy adott módszerről, tekintse meg a megfelelő hivatkozást:

* [Jelszókivonat-szinkronizálás](#password-hash-synchronization) [a zökkenőmentes egyszeri bejelentkezéssel (SSO)](how-to-connect-sso.md)
* [Átmenő hitelesítés](how-to-connect-pta.md) [zökkenőmentes egyszeri bejelentkezéssel (SSO)](how-to-connect-sso.md)
* [Összevont sso (Active Directory összevonási szolgáltatásokkal (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Összevonás a PingFederate-tel](#federation-with-pingfederate)

> [!NOTE] 
> Fontos megjegyezni, hogy az Azure AD-összevonás konfigurálásával megbízhatóságot létesít az Azure AD-bérlő és az összevont tartományok között. Ezzel a megbízhatósági kapcsolattal összevont tartományi felhasználók hozzáférhetnek az Azure AD felhőerőforrásait a bérlőn belül.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>A szervezet felhasználói bejelentkezési módjának kiválasztása
Az Azure AD Connect megvalósításának első döntése az, hogy melyik hitelesítési módszert használja a felhasználók a bejelentkezéshez. Fontos, hogy biztosan a megfelelő módszert válassza, amely megfelel a szervezet biztonsági és speciális követelményeinek. A hitelesítés kritikus fontosságú, mert ellenőrzi a felhasználó identitását az alkalmazások és adatok eléréséhez a felhőben. A megfelelő hitelesítési módszer kiválasztásához figyelembe kell vennie a választás idejét, meglévő infrastruktúráját, összetettségét és költségét. Ezek a tényezők minden szervezetesetében eltérőek, és idővel változhatnak.

Az Azure AD a következő hitelesítési módszereket támogatja: 

* **Felhőalapú hitelesítés** – Ha ezt a hitelesítési módszert választja, az Azure AD kezeli a felhasználói bejelentkezés hitelesítési folyamatát. A felhőalapú hitelesítéssel két lehetőség közül választhat: 
   * **Jelszókivonat-szinkronizálás (PHS)** – A jelszókivonat-szinkronizálás lehetővé teszi a felhasználók számára, hogy ugyanazt a felhasználónevet és jelszót használják, amelyet a helyszínen használnak anélkül, hogy az Azure AD Connecten kívül további infrastruktúrát kellene üzembe helyezniük. 
   * **Átadó hitelesítés (PTA)** – Ez a beállítás hasonló a jelszókivonat-szinkronizáláshoz, de egyszerű jelszó-érvényesítést biztosít a helyszíni szoftverügynökök használatával az erős biztonsági és megfelelőségi házirendekkel rendelkező szervezetek számára.
* **Összevont hitelesítés** – Ha ezt a hitelesítési módszert választja, az Azure AD átadja a hitelesítési folyamatot egy külön megbízható hitelesítési rendszernek, például az AD FS-nek vagy egy külső összevonási rendszernek a felhasználó bejelentkezésének érvényesítéséhez. 

A legtöbb szervezet, amely csak engedélyezni szeretné a felhasználók bejelentkezését az Office 365-be, saas-alkalmazásokba és más Azure AD-alapú erőforrásokba, javasoljuk az alapértelmezett jelszókivonat-szinkronizálási beállítást.
 
A hitelesítési módszer kiválasztásáról az [Azure Active Directory hibrid identitáskezelési megoldásának megfelelő hitelesítési módszer kiválasztása című](../../security/fundamentals/choose-ad-authn.md) témakörben talál részletes információt.

### <a name="password-hash-synchronization"></a>Jelszókivonat szinkronizálása
A jelszókivonat-szinkronizálással a rendszer szinkronizálja a felhasználói jelszavak kivonatait a helyszíni Active Directoryból az Azure AD-be. Amikor a jelszavakat módosítja vagy alaphelyzetbe állítja a helyszíni beállításokat, az új jelszókiállítások azonnal szinkronizálódnak az Azure AD-vel, így a felhasználók mindig ugyanazt a jelszót használhatják a felhőbeli erőforrásokhoz és a helyszíni erőforrásokhoz. A jelszavakat soha nem küldi el az Azure AD-nek, és nem tárolja az Azure AD-ben tiszta szövegben. Használhatja a jelszókivonat-szinkronizálás tiszajelszó-visszaírással az Önkiszolgáló jelszó-visszaállítás engedélyezéséhez az Azure AD-ben.

Emellett engedélyezheti a [zökkenőmentes egyszeri bejelentkezést](how-to-connect-sso.md) a vállalati hálózaton lévő tartományhoz csatlakozó gépeken lévő felhasználók számára. Egyszeri bejelentkezésesetén az engedélyezett felhasználóknak csak egy felhasználónevet kell megadniuk, hogy biztonságosan hozzáférhessenek a felhőbeli erőforrásokhoz.

![Jelszókivonat szinkronizálása](./media/plan-connect-user-signin/passwordhash.png)

További információt a [jelszókivonat-szinkronizálási](how-to-connect-password-hash-synchronization.md) cikkben talál.

### <a name="pass-through-authentication"></a>Átmenő hitelesítés
Az átadó hitelesítéssel a felhasználó jelszavát a rendszer a helyszíni Active Directory-vezérlővel szemben érvényesíti. A jelszó nak semmilyen formában nem kell jelen lennie az Azure AD-ben. Ez lehetővé teszi, hogy a helyszíni szabályzatok, például a bejelentkezési órákkorlátozásai kiértékelése a felhőszolgáltatások hitelesítése során.

Az átmenő hitelesítés egyszerű ügynököt használ egy Windows Server 2012 R2 tartományhoz csatlakozó gépen a helyszíni környezetben. Ez az ügynök figyeli a jelszó-érvényesítési kérelmeket. Nem igényel bejövő portokat, hogy nyitva legyenek az interneten.

Emellett engedélyezheti az egyszeri bejelentkezést a vállalati hálózaton lévő tartományhoz csatlakozó gépekfelhasználói számára is. Egyszeri bejelentkezésesetén az engedélyezett felhasználóknak csak egy felhasználónevet kell megadniuk, hogy biztonságosan hozzáférhessenek a felhőbeli erőforrásokhoz.
![Átmenő hitelesítés](./media/plan-connect-user-signin/pta.png)

További információkért lásd:
- [Átmenő hitelesítés](how-to-connect-pta.md)
- [Egyszeri bejelentkezés](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Új vagy meglévő, AD FS rendszerű farmot használó összevonás Windows Server 2012 R2 rendszerben
Az összevont bejelentkezéssel a felhasználók bejelentkezhetnek az Azure AD-alapú szolgáltatásokba a helyszíni jelszavaikkal. Amíg a vállalati hálózaton vannak, még a jelszavukat sem kell megadniuk. Az AD FS összevonási beállításának használatával a Windows Server 2012 R2 rendszerben telepíthet egy új vagy meglévő, AD FS rendszerű farmot. Ha úgy dönt, hogy megad egy meglévő farmot, az Azure AD Connect konfigurálja a farm és az Azure AD közötti megbízhatóságot, hogy a felhasználók bejelentkezhessenek.

<center>

![Összevonás az AD FS rendszerrel Windows Server 2012 R2 rendszerben](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Összevonás üzembe helyezése az AD FS szolgáltatással Windows Server 2012 R2 rendszerben

Ha új farmot telepít, a következőkre van szüksége:

* Az összevonási kiszolgáló Windows Server 2012 R2 kiszolgálója.
* A webalkalmazás-proxy Windows Server 2012 R2 kiszolgálója.
* Egy .pfx fájl egy TLS/SSL tanúsítvánnyal a tervezett összevonási szolgáltatás nevéhez. Például: fs.contoso.com.

Ha új farmot telepít, vagy meglévő farmot használ, a következőkre van szüksége:

* Helyi rendszergazdai hitelesítő adatok az összevonási kiszolgálókon.
* Helyi rendszergazdai hitelesítő adatok minden olyan munkacsoport-kiszolgálón (nem tartományhoz csatlakoztatva), amelyen a webalkalmazás-proxy szerepkört telepíteni kívánja.
* Az a gép, amelyen a varázslót futtatja, hogy csatlakozni lehessen bármely más olyan géphez, amelyre az AD FS vagy webalkalmazás-proxyt a Windows távkezelés használatával szeretné telepíteni.

További információ: [SSO konfigurálása az AD FS-sel.](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)

### <a name="federation-with-pingfederate"></a>Összevonás a PingFederate-tel
Az összevont bejelentkezéssel a felhasználók bejelentkezhetnek az Azure AD-alapú szolgáltatásokba a helyszíni jelszavaikkal. Amíg a vállalati hálózaton vannak, még a jelszavukat sem kell megadniuk.

A PingFederate Azure Active Directoryval való használatra történő konfigurálásáról az Azure Active Directoryval és az [Office 365-tel való PingFederate-integráció](https://www.pingidentity.com/AzureADConnect) című témakörben talál további információt.

Az Azure AD Connect PingFederate használatával történő beállításáról az [Azure AD Connect egyéni telepítése című](how-to-connect-install-custom.md#configuring-federation-with-pingfederate) témakörben talál további információt.

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Bejelentkezés az AD FS korábbi verziójával vagy harmadik féltől származó megoldással
Ha már konfigurálta a felhőalapú bejelentkezést az AD FS egy korábbi verziójával (például az AD FS 2.0-val) vagy egy külső összevonási szolgáltatóval, az Azure AD Connecten keresztül kihagyhatja a felhasználói bejelentkezési konfigurációt. Ez lehetővé teszi, hogy az Azure AD Connect legújabb szinkronizálási és egyéb képességeit, miközben továbbra is használja a meglévő megoldás a bejelentkezéshez.

További információt az [Azure AD külső gyártók összevonási kompatibilitási listájában](how-to-connect-fed-compatibility.md)talál.


## <a name="user-sign-in-and-user-principal-name"></a>Felhasználó bejelentkezése és egyszerű felhasználónév
### <a name="understanding-user-principal-name"></a>Az egyszerű felhasználónév ismertetése
Az Active Directoryban az alapértelmezett egyszerű felhasználónév (UPN) utótag annak a tartománynak a DNS-neve, ahol a felhasználói fiókot létrehozták. A legtöbb esetben ez az a tartománynév, amely az interneten vállalati tartományként van regisztrálva. Az Active Directory – tartományok és megbízhatósági kapcsolatok szolgáltatással azonban további UPN-utótagokat is hozzáadhat.

A felhasználó upn formátuma username@domaina . Például egy "contoso.com" nevű Active Directory-tartomány esetében a Jánosjohn@contoso.comnevű felhasználó nak lehet az upn " ". A felhasználó upn-je az RFC 822-n alapul. Bár az eredeti felhasználón és az e-mail formátuma megegyezik, az upn értéke egy felhasználó számára megegyezhet a felhasználó e-mail címével.

### <a name="user-principal-name-in-azure-ad"></a>Egyszerű felhasználónév az Azure AD-ben
Az Azure AD Connect varázsló a userPrincipalName attribútumot használja, vagy lehetővé teszi, hogy megadja az attribútumot (egy egyéni telepítésben), amelyet a helyszíni egyszerű felhasználónévként kell használni az Azure AD-ben. Ez az az érték, amely az Azure AD-be való bejelentkezéshez használatos. Ha a userPrincipalName attribútum értéke nem felel meg egy ellenőrzött tartománynak az Azure AD-ben, majd az Azure AD lecseréli azt egy alapértelmezett .onmicrosoft.com értékre.

Az Azure Active Directory minden könyvtára beépített tartománynevet kap, contoso.onmicrosoft.com formátummal, amely lehetővé teszi az Azure vagy más Microsoft-szolgáltatások használatának megkezdését. Egyéni tartományok használatával javíthatja és egyszerűsítheti a bejelentkezési élményt. Az Azure AD-ben az egyéni tartománynevekről és a tartomány ellenőrzéséről az [Egyéni tartománynév hozzáadása az Azure Active Directoryhoz](../fundamentals/add-custom-domain.md)című témakörben talál további információt.

## <a name="azure-ad-sign-in-configuration"></a>Azure AD-bejelentkezés konfigurálása
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Az Azure AD bejelentkezési konfigurációja az Azure AD Connecttel
Az Azure AD bejelentkezési élmény attól függ, hogy az Azure AD megfelelhet-e az Azure AD-címtárban ellenőrzött egyéni tartományok egyikének egyszerű felhasználónév-utótagjának. Az Azure AD Connect segítséget nyújt az Azure AD bejelentkezési beállításainak konfigurálása közben, hogy a felhőben a felhasználói bejelentkezési élmény hasonló a helyszíni élményhez.

Az Azure AD Connect felsorolja a tartományokhoz definiált UPN-utótagokat, és megpróbálja azokat egy egyéni tartománnyal egyeztetni az Azure AD-ben. Ezután segít a megfelelő lépéseket, hogy meg kell tenni.
Az Azure AD bejelentkezési lap felsorolja a helyszíni Active Directoryhoz definiált UPN-utótagokat, és megjeleníti a megfelelő állapotot az egyes utótagokhoz. Az állapotértékek a következők lehetnek:

| Állapot | Leírás | Szükséges intézkedés |
|:--- |:--- |:--- |
| Ellenőrzött |Az Azure AD Connect talált egy megfelelő ellenőrzött tartományt az Azure AD-ben. A tartomány összes felhasználója bejelentkezhet a helyszíni hitelesítő adataikkal. |Nincs szükség beavatkozásra. |
| Nincs ellenőrizve |Az Azure AD Connect talált egy megfelelő egyéni tartományt az Azure AD-ben, de nincs ellenőrizve. Ha a tartomány nincs ellenőrizve, a tartomány felhasználóinak upn-utótagja a szinkronizálás után az alapértelmezett .onmicrosoft.com utótagra változik. | [Ellenőrizze az egyéni tartományt az Azure AD-ben.](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Nincs hozzáadva |Az Azure AD Connect nem talált olyan egyéni tartományt, amely megfelelt az UPN-utótagnak. A tartomány felhasználóinak upn-utótagja az alapértelmezett .onmicrosoft.com utótagra változik, ha a tartománynincs hozzáadva és ellenőrizve az Azure-ban. | [Adjon hozzá és ellenőrizzen egy egyéni tartományt, amely megfelel az UPN-utótagnak.](../fundamentals/add-custom-domain.md) |

Az Azure AD bejelentkezési lap felsorolja a helyszíni Active Directoryhoz definiált UPN-utótagokat és az Azure AD megfelelő egyéni tartományát az aktuális ellenőrzési állapottal. Egy egyéni telepítés, most kiválaszthatja az attribútumot a felhasználó egyszerű felhasználónevét az **Azure AD bejelentkezési** lapon.

![Az Azure AD bejelentkezési lapja](./media/plan-connect-user-signin/custom_azure_sign_in.png)

A frissítés gombra kattintva újra lehívhatja az egyéni tartományok legújabb állapotát az Azure AD-ből.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Az egyszerű felhasználónév attribútumának kiválasztása az Azure AD-ben
A userPrincipalName attribútum az az attribútum, amelyet a felhasználók az Azure AD-be és az Office 365-be való bejelentkezéskor használnak. Ellenőrizze az Azure AD-ben a felhasználók szinkronizálása előtt használt tartományokat (más néven UPN-utótagokat).

Javasoljuk, hogy tartsa meg az alapértelmezett userPrincipalName attribútumot. Ha ez az attribútum nem irányítható, és nem ellenőrizhető, akkor lehetőség van egy másik attribútum (például e-mail) kiválasztására a bejelentkezési azonosítót tartalmazó attribútumként. Ezt alternatív azonosítónak nevezzük. Az Alternatív azonosító attribútum értékének meg kell felelnie az RFC 822 szabványnak. A bejelentkezési megoldáshoz használhatja az alternatív azonosítót jelszó egyszeri bejelentkezéssel és összevonási egyszeri bejelentkezéssel.

> [!NOTE]
> Az alternatív azonosító használata nem kompatibilis az office 365-ös összes számítási feladattal. További információt a [Másodlagos bejelentkezési azonosító beállítása](https://technet.microsoft.com/library/dn659436.aspx) című cikkben talál.
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Különböző egyéni tartományállapotok és hatásuk az Azure bejelentkezési élményére
Nagyon fontos, hogy megismerjék az Azure AD-címtárban lévő egyéni tartományállapotok és a helyszíni en-n definiált UPN-utótagok közötti kapcsolatot. Vegyük végig a különböző lehetséges Azure-bejelentkezési élményeket, amikor az Azure AD Connect használatával állítja be a szinkronizálást.

A következő információk, tegyük fel, hogy mi vagyunk érintett a UPN utótag contoso.com, amely a helyszíni könyvtárban user@contoso.comrészeként UPN -- például .

###### <a name="express-settingspassword-hash-synchronization"></a>Expressz beállítások/Jelszó-kivonat szinkronizálása

| Állapot | Hatása a felhasználói Azure-bejelentkezési élményre |
|:---:|:--- |
| Nincs hozzáadva |Ebben az esetben nincs egyéni contoso.com tartomány az Azure AD-címtárban. Azok a felhasználók, akik helyszíni upn-nel rendelkeznek az utótaggal, @contoso.com nem használhatják a helyszíni UPN-t az Azure-ba való bejelentkezéshez. Ehelyett egy új upn-t kell használniuk, amelyet az Azure AD biztosít számukra az alapértelmezett Azure AD-címtár utótag hozzáadásával. Ha például a felhasználókat az Azure AD-címtárazurecontoso.onmicrosoft.com szinkronizálja, user@contoso.com akkor a helyszíni felhasználó user@azurecontoso.onmicrosoft.comegy egész ítőn jelenik meg. |
| Nincs ellenőrizve |Ebben az esetben egy egyéni contoso.com, amely az Azure AD-címtárban hozzáadott. Azonban ez még nem igazolt. Ha folytatja a felhasználók szinkronizálása a tartomány ellenőrzése nélkül, majd a felhasználók kapnak egy új upn az Azure AD, csakúgy, mint a "Nem hozzáadott" forgatókönyv. |
| Ellenőrzött |Ebben az esetben van egy egyéni tartomány contoso.com, amely már hozzáadott és ellenőrzött az Azure AD az UPN-utótag. A felhasználók használhatják a helyszíni egyszerű felhasználónevet, user@contoso.compéldául az Azure-ba való bejelentkezéshez, miután szinkronizálva vannak az Azure AD-vel. |

###### <a name="ad-fs-federation"></a>AD FS-összevonás
Az Azure AD-ben nem hozhat létre összevonást az alapértelmezett .onmicrosoft.com tartománnyal vagy az Azure AD nem ellenőrzött egyéni tartományával. Amikor az Azure AD Connect varázslót futtatja, ha nem ellenőrzött tartományt választ összevonáslétrehozásához, majd az Azure AD Connect kéri a szükséges rekordokat, amelyeket létre kell hozni, ahol a DNS a tartomány ban található. További információ: [Az összevonásra kiválasztott Azure AD-tartomány ellenőrzése.](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation)

Ha kiválasztotta a felhasználói bejelentkezési lehetőséget **összevonása a AD FS,** majd rendelkeznie kell egy egyéni tartományt, hogy továbbra is hozzon létre egy összevonás az Azure AD-ben. A vitafórum, ez azt jelenti, hogy egy egyéni tartományt contoso.com hozzá az Azure AD címtárban.

| Állapot | A felhasználói Azure bejelentkezési élményre gyakorolt hatás |
|:---:|:--- |
| Nincs hozzáadva |Ebben az esetben az Azure AD Connect nem talált megfelelő egyéni tartományt az UPN-utótag contoso.com az Azure AD-címtárban. Egyéni tartományt kell hozzáadnia, contoso.com ha a felhasználóknak az AD FS használatával kell user@contoso.combejelentkezniük a helyszíni upn-nal (például). |
| Nincs ellenőrizve |Ebben az esetben az Azure AD Connect kéri a megfelelő részleteket arról, hogyan ellenőrizheti a tartományt egy későbbi szakaszban. |
| Ellenőrzött |Ebben az esetben további művelet nélkül folytathatja a konfigurációt. |

## <a name="changing-the-user-sign-in-method"></a>A felhasználói bejelentkezési mód módosítása
Módosíthatja a felhasználói bejelentkezési módszert az összevonásból, a jelszókivonat-szinkronizálásból vagy az átadó hitelesítésből az Azure AD Connectben elérhető feladatok használatával az Azure AD Connect kezdeti konfigurálása után. Futtassa újra az Azure AD Connect varázslót, és megjelenik a végrehajtható feladatok listája. Válassza a **Felhasználói bejelentkezés módosítása** elemet a feladatok listájából.

![A felhasználói bejelentkezés módosítása](./media/plan-connect-user-signin/changeusersignin.png)

A következő oldalon a rendszer kéri, hogy adja meg az Azure AD hitelesítő adatait.

![Csatlakozás az Azure AD szolgáltatáshoz](./media/plan-connect-user-signin/changeusersignin2.png)

A **Felhasználó bejelentkezési** lapján válassza ki a kívánt felhasználói bejelentkezést.

![Csatlakozás az Azure AD szolgáltatáshoz](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Ha csak ideiglenesen vált át a jelszókivonat-szinkronizálásra, jelölje be a **Felhasználói fiókok konvertálásának mellőzése** jelölőnégyzetet. Ha nem jelöli be a beállítást, akkor minden felhasználó tetszésszerint több órát is igénybe vehet.
>
>

## <a name="next-steps"></a>További lépések
- További információ [a helyszíni identitások azure-active Directoryval való integrálásáról.](whatis-hybrid-identity.md)
- További információ az [Azure AD Connect tervezési koncepcióiról.](plan-connect-design-concepts.md)
