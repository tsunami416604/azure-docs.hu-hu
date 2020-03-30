---
title: Az Azure felhasználói alapelvenév (UPN) módosítási hibáinak megtervezése és hibaelhárítása
description: Az upn-változások ismert problémáinak és hatásainak megértése
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 695773da624bc8d4ccff09119d64fc43319ff488
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246432"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Az egyszerű felhasználónevek változásainak megtervezése és hibaelhárítása az Azure Active Directoryban

Az egyszerű felhasználónév (UPN) egy olyan attribútum, amely a felhasználói fiókok internetes kommunikációs szabványa. Az upn egy UPN-előtagból (a felhasználói fiók nevéből) és egy UPN-utótagból (DNS-tartománynévből) áll. Az előtag a "@" szimbólummal csatlakozik az utótaghoz. Például: someone@example.com. Az egyszerű felhasználónévnek egyedinek kell lennie a címtárerdő összes rendszerbiztonsági tagobjektuma között. 

> [!NOTE]
> A fejlesztők számára azt javasoljuk, hogy a felhasználói objektumazonosítót használja nem egyszerű felhasználóként, hanem nem megváltoztathatatlan azonosítóként. Ha az alkalmazások jelenleg az upn-t használják, javasoljuk, hogy az upn-t úgy kell beállítani, hogy megfeleljen a felhasználó elsődleges e-mail címének, hogy javítsa a felhasználói élményt.<br> **Hibrid környezetben fontos, hogy a felhasználó upn azonos a helyszíni címtárban és az Azure Active Directoryban.**

**Ez a cikk feltételezi, hogy az UPN-t használja felhasználói azonosítóként. Foglalkozik az upn-módosítások tervezéséről, és az upn-változásokból eredő problémák helyreállításáról.**

## <a name="learn-about-upns-and-upn-changes"></a>További információ az UPN-ek és az UPN-változásokról
A bejelentkezési oldalak gyakran kérik a felhasználókat, hogy adják meg e-mail címüket, ha a szükséges érték valójában az upn. Ezért győződjön meg arról, hogy bármikor módosítja a felhasználók upn-jét, amikor az elsődleges e-mail címük megváltozik.

A felhasználók elsődleges e-mail címei több okból is változhatnak:

* cég márkaváltás

* különböző vállalati részlegekbe költöző alkalmazottak 

* egyesülések és felvásárlások

* alkalmazott neve megváltozik

### <a name="types-of-upn-changes"></a>Az upn-változások típusai

Az upn az előtag, utótag vagy mindkettő módosításával módosítható.

* **Az előtag módosítása**.

   *  Ha például egy személy neve megváltozott, módosíthatja a fiók nevét:  
BSimon@contoso.com aBJohnson@contoso.com

   * Az előtagok vállalati szabványát is módosíthatja:  
Bsimon@contoso.com aBritta.Simon@contoso.com

* **Az utótag módosítása**. <br>

    Ha például egy személy körzetet váltott, a tartománya is megváltozhat: 

   * Britta.Simon@contoso.comaBritta.Simon@contosolabs.com <br>
     Vagy<br>
    *   Britta.Simon@corp.contoso.comaBritta.Simon@labs.contoso.com 

Módosítsa a felhasználó upn-ját minden alkalommal, amikor egy felhasználó elsődleges e-mail címe frissül. Az e-mail változásának okáttól függetlenül az upn-t mindig frissíteni kell, hogy megfeleljen.

Az Active Directory és az Azure AD kezdeti szinkronizálása során győződjön meg arról, hogy a felhasználók e-mailjei megegyeznek az upn-okkal

### <a name="upns-in-active-directory"></a>UPN-ek az Active Directoryban

Az Active Directoryban az alapértelmezett UPN-utótag annak a tartománynak a DNS-neve, amelyhez a felhasználói fiókot létrehozta. A legtöbb esetben ez az a tartománynév, amelyet vállalati tartományként regisztrál az interneten. Ha a felhasználói fiókot a contoso.com tartományban hozza létre, az alapértelmezett

username@contoso.com

 Az Active Directory-tartományok és megbízhatósági kapcsolatok használatával azonban [további UPN-utótagokat](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) is hozzáadhat. 

Előfordulhat például, hogy labs.contoso.com szeretne hozzáadni, és ezt a felhasználók upns és e-mail tükrözik. Ezután

username@labs.contoso.com.

>[!IMPORTANT]
> Ha az Active Directoryban és az Azure Active Directoryban az UPN-ek nem egyeznek, problémák merülnek fel. Ha [módosítja az utótagot az Active Directoryban,](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)gondoskodnia kell arról, hogy az Azure AD megfelelő egyéni tartománynevet [adott hozzá és ellenőrzött legyen.](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) 

![Az ellenőrzött tartományok képernyőképe](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>UPN-ek az Azure Active Directoryban

A felhasználók jelentkezzen be az Azure AD az érték a userPrincipalName attribútumban. 

Ha az Azure AD-t a helyszíni Active Directoryval együtt használja, a felhasználói fiókok szinkronizálása az Azure AD Connect szolgáltatás használatával történik. Alapértelmezés szerint az Azure AD Connect varázsló a helyszíni Active Directory userPrincipalName attribútumát használja egyszerű felhasználónévként az Azure AD-ben. Egyéni telepítésben módosíthatja azt egy másik attribútumra.

Fontos, hogy egy meghatározott folyamattal rendelkezik, amikor egyetlen felhasználó egyszerű felhasználónevét (UPN) frissíti, vagy a teljes szervezetre vonatkozóan. 

Tekintse meg a dokumentum ismert problémáit és kerülő megoldásait.

Amikor felhasználói fiókokat szinkronizál az Active Directoryból az Azure AD-be, győződjön meg arról, hogy az Active Directoryban lévő upn-ek leképezik az Azure AD ellenőrzött tartományait.

![Ellenőrzött tartományok képernyőképe](./media/howto-troubleshoot-upn-changes/verified-domains.png)

Ha a userPrincipalName attribútum értéke nem felel meg egy ellenőrzött tartománynak az Azure AD-ben, a szinkronizálási folyamat lecseréli az utótagot egy alapértelmezett .onmicrosoft.com értékre.


### <a name="roll-out-bulk-upn-changes"></a>Tömeges upn-módosítások bevezetése

Kövesse a kísérleti adatok ajánlott eljárások[tömeges](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) upn változások. Is volna egy baldachin visszagörgetés tervez részére visszatér upns ha ön talál kérdés amit ki nem tud lenni gyorsan megfejt. Miután a próbagép fut, elkezdheti a különböző szervezeti szerepkörökkel rendelkező felhasználók kis csoportjait, valamint az alkalmazások vagy eszközök adott készleteit.

Megy keresztül ez az első részhalmaza a felhasználók kapsz egy jó ötlet, hogy mit kell várni a felhasználók részeként a változás. Adja meg ezt az információt a felhasználói kommunikációban.

Hozzon létre egy meghatározott eljárást az upn-ek módosítására az egyes felhasználókon a normál műveletek részeként. Javasoljuk, hogy készítsen olyan tesztelt eljárást, amely tartalmazza az ismert problémák és kerülő megoldások dokumentációját.

A következő szakaszok részletesen ismertetik a lehetséges ismert problémákat és kerülő megoldásokat, amikor az upn-ek módosulnak.

## <a name="user-provisioning-known-issues-and-workarounds"></a>a felhasználók kiépítése ismert problémák és kerülő megoldások

[A szoftverszolgáltatásként (SaaS)](https://azure.microsoft.com/overview/what-is-saas/) és a Line of Business (LoB) alkalmazások gyakran támaszkodnak az UPN-ekre a felhasználók megkereséséhez és a felhasználói profil adatainak tárolásához, beleértve a szerepköröket is. A [Just in Time kiépítése](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) a felhasználói profil létrehozása, amikor a felhasználók először jelentkeznek be az alkalmazásba, az upn-változások hatással lehetnek a Just in Time kiépítése korra.

**Ismert probléma**<br>
A felhasználó upn módosítása megszakíthatja az Azure AD-felhasználó és az alkalmazáson létrehozott felhasználói profil közötti kapcsolatot. Ha az alkalmazás a [Just in Time kiépítése,](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)előfordulhat, hogy hozzon létre egy vadonatúj felhasználói profilt. Ehhez az alkalmazás rendszergazdájának manuális módosításokat kell végrehajtania a kapcsolat javításához.

**Workaround**<br>
[Az Azure AD automatikus felhasználói kiépítés](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) lehetővé teszi, hogy automatikusan létrehozza, karbantartja és távolítsa el a felhasználói identitások a támogatott felhőalapú alkalmazásokban. Az alkalmazások automatikus felhasználói kiépítésének konfigurálása automatikusan frissíti az upn-eket az alkalmazásokon. Tesztelje az alkalmazásokat a fokozatos bevezetés részeként annak ellenőrzésére, hogy az okat nem érintik-e az upn-változások.

## <a name="managed-devices-known-issues-and-workarounds"></a>A felügyelt eszközök ismert problémái és kerülő megoldásai

Az [Azure AD-be való kattintással](https://docs.microsoft.com/azure/active-directory/devices/overview)maximalizálhatja a felhasználók termelékenységét a felhőbeli és helyszíni erőforrásokon keresztül történő egyszeri bejelentkezéssel .You to bringing your devices to Azure AD, you to maximalizálhatja a felhasználók termelékenységét egyszeri bejelentkezéssel (SSO) a felhőben és a helyszíni erőforrásokban.

### <a name="azure-ad-joined-devices"></a>Azure AD-hez csatlakoztatott eszközök

[Az Azure AD-hez csatlakozó](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join) eszközök közvetlenül csatlakoznak az Azure AD-hez, és lehetővé teszik a felhasználók számára, hogy a szervezet identitásával jelentkezzenek be az eszközre.

**Ismert problémák** <br>
A felhasználók egyszeri bejelentkezési problémákat tapasztalhatnak az Azure AD-től a hitelesítéshez függő alkalmazásokkal.

**Workaround** <br>
Hagyjon elegendő időt az upn-módosítás nak az Azure AD-vel való szinkronizálásához. Miután meggyőződött arról, hogy az új upn tükröződik az Azure AD Portalon, kérje meg a felhasználót, hogy válassza ki az "Egyéb felhasználó" csempét az új upn-nel való bejelentkezéshez. a [PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0)segítségével is ellenőrizheti. Miután bejelentkezett az új upn-nal, a régi upn-ra mutató hivatkozások továbbra is megjelenhetnek a "Munkahelyi vagy iskolai hozzáférés" Windows-beállításban.

![Ellenőrzött tartományok képernyőképe](./media/howto-troubleshoot-upn-changes/other-user.png)

### <a name="hybrid-azure-ad-joined-devices"></a>Hibrid Azure AD-csatlakoztatott eszközök

[A hibrid Azure AD-hez csatlakozó](https://docs.microsoft.com/azure/active-directory/devices/concept-azure-ad-join-hybrid) eszközök csatlakoznak az Active Directoryhoz és az Azure AD-hez. Hibrid Azure AD-csatlakozás, ha a környezet egy helyszíni Active Directory lábnyom, és azt is szeretné kihasználni az Azure AD által biztosított képességek.

**Ismert problémák** 

A Windows 10 hibrid Azure AD-hez csatlakozó eszközök valószínűleg váratlan újraindításokat és hozzáférési problémákat tapasztalnak.

Ha a felhasználók az új UPN-nek az Azure AD-vel való szinkronizálása előtt jelentkeznek be a Windows rendszerbe, vagy továbbra is egy meglévő Windows-munkamenetet használnak, egyszeri bejelentkezési problémákat tapasztalhatnak az Azure AD-t hitelesítésre használó alkalmazásokkal, ha feltételes hozzáférés van konfigurálva a hibrid összekapcsolt eszközök használatának kényszerítése az erőforrások eléréséhez. 

Ezenkívül a következő üzenet jelenik meg, amely egy perc elteltével újraindítást kényszerít ki. 

"A számítógép automatikusan újraindul egy perc alatt. A Windows egy problémába ütközött, és újra kell indítani. Most zárja be ezt az üzenetet, és mentse a munkáját.".

**Workaround** 

Az eszköz nek le kell választania az Azure AD-ből, és újra kell indítani. Újraindítás után az eszköz automatikusan csatlakozik az Azure AD-hez újra, és a felhasználónak be kell jelentkeznie az új upn az "Egyéb felhasználó" csempe kiválasztásával. Ha le szeretne választani egy eszközt az Azure AD-ből, futtassa a következő parancsot a parancssorból:

**dsregcmd /szabadság**

A felhasználónak újra be kell [igényelnie](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) a Windows Hello for Business alkalmazást, ha azt használja. A Windows 7 és 8.1-es eszközöket ez a probléma nem érinti az upn-módosítások után.

## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>A Microsoft Hitelesítő ismert problémái és kerülő megoldásai

Előfordulhat, hogy a szervezetnek a [Microsoft Authenticator alkalmazás](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) használatára van szüksége a bejelentkezéshez és a szervezeti alkalmazások és adatok eléréséhez. Bár egy felhasználónév is megjelenhet az alkalmazásban, a fiók nincs beállítva, hogy ellenőrzési módszerként működjön, amíg a felhasználó be nem fejezi a regisztrációs folyamatot.

A [Microsoft Authenticator alkalmazás](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview) négy fő funkcióval rendelkezik:

* Többtényezős hitelesítés leküldéses értesítéssel vagy ellenőrző kóddal

* Hitelesítési brókerként működhet iOS és Android rendszerű eszközökön, hogy egyszeri bejelentkezést biztosítson a [közvetített hitelesítést](https://docs.microsoft.com/azure/active-directory/develop/brokered-auth) használó alkalmazásokhoz

* Eszközregisztráció (más néven munkahelyi csatlakozás) az Azure AD-hez, amely más funkciók, például az Intune Alkalmazásvédelem és az Eszközregisztrálás/-kezelés követelménye,

* Telefonos bejelentkezés, amelyhez többfa és eszköz regisztrációszükséges.

### <a name="multi-factor-authentication-with-android-devices"></a>Többtényezős hitelesítés Android-eszközökkel

A Microsoft Authenticator alkalmazás sávon kívüli ellenőrzési lehetőséget kínál. Ahelyett, hogy a bejelentkezés során automatikus telefonhívást vagy SMS-t kezdeményezne a felhasználónak, a [többtényezős hitelesítés (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) értesítést küld a Microsoft Authenticator alkalmazásnak a felhasználó okostelefonján vagy táblagépén. A felhasználó egyszerűen a Jóváhagyás gombra koppint (vagy pinkódot vagy biometrikus értéket ad meg, és a "Hitelesítés" gombra koppint) az alkalmazásban a bejelentkezés befejezéséhez.

Ha módosítja egy felhasználó felhasználói felületének felhasználóáltali felhasználókénti felhasználókészülékét, a mobileszközök a következő problémákat tapasztalhatják:

**Ismert problémák** 

A régi upn továbbra is megjelenik a felhasználói fiókban, és előfordulhat, hogy nem érkezik értesítés. [Az ellenőrző kódok](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-faq) továbbra is működnek.

**Workaround**

Ha értesítést kap, utasítsa a felhasználót, hogy utasítsa el az értesítést, nyissa meg a Hitelesítő alkalmazást, koppintson az "Értesítések ellenőrzése" lehetőségre, és hagyja jóvá az MFA-kérdést. Ezt követően a fiókban megjelenő upn frissül. Megjegyzés: előfordulhat, hogy a frissített upn új fiókként jelenik meg, ez más Hitelesítő funkcióknak köszönhető. További információ további ismert problémák ebben a cikkben.

### <a name="brokered-authentication"></a>Közvetített hitelesítés

Az Android és iOS brókerek, mint a Microsoft Authenticator lehetővé teszi:

* Egyszeri bejelentkezés (SSO) – A felhasználóknak nem kell minden alkalmazásba bejelentkezniük.

* Eszközazonosítás – A közvetítő hozzáfér az eszközön létrehozott eszköztanúsítványhoz, amikor a munkahelyi csatlakoztatása volt.

* Alkalmazás-azonosító ellenőrzése - Amikor egy alkalmazás meghívja a brókert, átadja az átirányítási URL-címét, és a bróker ellenőrzi azt.

Emellett lehetővé teszi az alkalmazások számára, hogy speciálisabb funkciókban, például [feltételes hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/)vegyenek részt, és támogatja a [Microsoft Intune-forgatókönyveket.](https://docs.microsoft.com/azure/active-directory/develop/msal-net-use-brokers-with-xamarin-apps)

**Ismert problémák**<br>
A felhasználó több interaktív hitelesítési kérdést tartalmaz az új alkalmazások, amelyek a bróker által támogatott bejelentkezés miatt az alkalmazás által átadott login_hint és a közvetítőtárolt UPN közötti eltérés miatt.

**Workaround** <br> A felhasználónak manuálisan el kell távolítania a fiókot a Microsoft Hitelesítőből, és új bejelentkezést kell indítania egy közvetítő által támogatott alkalmazásból. A fiók automatikusan hozzáadódik a kezdeti hitelesítés után.

### <a name="device-registration"></a>Eszközregisztráció

A Microsoft Authenticator alkalmazás felelős az eszköz regisztrálásáért az Azure AD-be. Az eszközregisztráció lehetővé teszi, hogy az eszköz hitelesítse magát az Azure AD-ben, és a következő forgatókönyvek követelménye:

* Intune-alkalmazásvédelem

* Intune-eszközregisztráció

* Bejelentkezés telefonba

**Ismert problémák**<br>
Amikor módosítja az eredeti névazonosítót, egy új felhasználói partner jelenik meg a Microsoft Authenticator alkalmazásban, míg a régi upn-fiók továbbra is szerepel a listában. Ezenkívül a régi UPN megjelenik az alkalmazásbeállítások Eszközregisztrációs szakaszában. Az Eszközregisztráció normál funkcionalitásában vagy az függő forgatókönyvekben nincs változás.

**Workaround** <br> Ha el szeretné távolítani a régi upn-ra mutató összes hivatkozást a Microsoft Authenticator alkalmazásban, utasítsa a felhasználót, hogy manuálisan távolítsa el a régi és az új fiókokat a Microsoft Authenticator programból, regisztráljon újra az MFA-ra, és csatlakozzon újra az eszközhöz.

### <a name="phone-sign-in"></a>Telefonos bejelentkezés

A telefonos bejelentkezés lehetővé teszi a felhasználók számára, hogy jelszó nélkül jelentkezzenek be az Azure AD-be. A telefonos bejelentkezés engedélyezéséhez a felhasználónak regisztrálnia kell az MFA-ra az Authenticator alkalmazással, majd közvetlenül a hitelesítőn kell engedélyeznie a telefonos bejelentkezést. A konfiguráció részeként az eszköz regisztrálja az Azure AD.As part of the configuration, the device registers with Azure AD.

**Ismert problémák** <br>
A felhasználók nem használhatják a Telefonos bejelentkezést, mert nem kapnak értesítést. Ha a felhasználó az Értesítések ellenőrzése elemre koppint, hibaüzenetet kap.

**Workaround**<br>
A felhasználónak ki kell választania a telefonbejelentkezéshez engedélyezett fiók legördülő menüjét, és válassza a Telefon-bejelentkezés letiltása lehetőséget. Ha szükséges, a telefonos bejelentkezés újra engedélyezhető.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>Biztonsági kulcs (FIDO2) ismert problémák és kerülő megoldások

**Ismert problémák** <br>
Ha több felhasználó van regisztrálva ugyanazon a kulcson, a bejelentkezési képernyőn megjelenik egy fiókválasztó oldal, ahol a régi upn jelenik meg. A biztonsági kulccsal történő bejelentkezéseket az upn-módosítások nem érintik.  

**Workaround**<br>
A régi upn-okra mutató hivatkozások eltávolításához a felhasználóknak alaphelyzetbe kell [állítaniuk a biztonsági kulcsot, és újra regisztrálniuk](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key#known-issues)kell.

## <a name="onedrive-known-issues-and-workarounds"></a>A OneDrive ismert problémái és kerülő megoldásai

A OneDrive-felhasználókról ismert, hogy az upn-módosítások után problémákat tapasztalnak. További információt a [Hogyan hatnak az upn-módosítások a OneDrive URL-címére és a OneDrive szolgáltatásaira.](https://docs.microsoft.com/onedrive/upn-changes)

## <a name="next-steps"></a>További lépések

Tekintse meg ezeket az erőforrásokat:
* [Azure AD Connect: Tervezési fogalmak](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts)

* [Az Azure AD UserPrincipalName feltöltése](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-userprincipalname)

* [Microsoft-identitásplatform-azonosító tokenjei](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
