---
title: Meghívóbeváltás a B2B együttműködésben - Azure AD
description: Ismerteti az Azure AD B2B együttműködési meghívásbeváltási élményt a végfelhasználók számára, beleértve az adatvédelmi feltételekre vonatkozó megállapodást is.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0645807aa40557c163643f1393c310668518f9be
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535135"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Az Azure Active Directory B2B együttműködési meghívók beváltása

Ez a cikk azt ismerteti, hogy a vendégfelhasználók hogyan férhetnek hozzá az erőforrásokhoz, és milyen jóváhagyási folyamattal találkozhatnak. Ha meghívó e-mailt küld a vendégnek, a meghívó tartalmaz egy hivatkozást, amelyet a vendég beválthat, hogy hozzáférjen az alkalmazáshoz vagy a portálhoz. A meghívó e-mail csak az egyik módja annak, hogy a vendégek hozzáférjenek az erőforrásaihoz. Alternatív megoldásként vendégeket adhat hozzá a címtárhoz, és közvetlen hivatkozást adhat nekik a megosztani kívánt portálra vagy alkalmazásra. Függetlenül attól, hogy milyen módszert használnak, a vendégek et az első jóváhagyási folyamat on keresztül vezetik. Ez a folyamat biztosítja, hogy vendégei elfogadják az adatvédelmi feltételeket, és elfogadják az Ön által beállított [használati feltételeket.](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou)

Amikor vendégfelhasználót ad hozzá a címtárhoz, a vendégfelhasználói fiók hozzájárulási állapota (a PowerShellben látható), amely eredetileg **PendingAcceptance**értékre van állítva. Ez a beállítás addig marad, amíg a vendég elfogadja a meghívást, és nem fogadja el az adatvédelmi szabályzatot és a felhasználási feltételeket. Ezt követően a hozzájárulási állapot **Elfogadottra**változik, és a beleegyező oldalak már nem jelennek meg a vendég számára.

   > [!IMPORTANT]
   > **2021. március 31-től a**Microsoft már nem támogatja a meghívók beváltását nem felügyelt Azure AD-fiókok és bérlők létrehozásával a B2B együttműködési forgatókönyvekhez. Az előkészítés során arra bátorítjuk ügyfeleinket, hogy válasszák az [e-mailes egyszeri jelkód-hitelesítést.](one-time-passcode.md) Örömmel fogadjuk a nyilvános előnézeti funkcióval kapcsolatos visszajelzéseit, és izgatottan várjuk, hogy még több együttműködési módot hozzon létre.

## <a name="redemption-through-the-invitation-email"></a>Beváltás a meghívó e-mailen keresztül

Amikor egy vendégfelhasználót ad hozzá a címtárhoz [az Azure Portal használatával,](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)a rendszer meghívó e-mailt küld a vendégnek. Azt is választhatja, hogy meghívó e-maileket küldjön, amikor [a PowerShell használatával](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) vendégfelhasználókat ad hozzá a címtárhoz. Íme a vendég élményének leírása, amikor beváltja az e-mailben található hivatkozást.

1. A vendég kap egy [meghívó e-mailt,](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) amelyet a **Microsoft-meghívók**küld.
2. A vendég az **Első lépések** lehetőséget választja az e-mailben.
3. Ha a vendég nem rendelkezik Azure AD-fiókkal, Microsoft-fiókkal (MSA) vagy e-mail fiókkal egy összevont szervezetben, a rendszer kéri, hogy hozzon létre egy MSA-t (kivéve, ha az [egyszeri jelkód-funkció](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) engedélyezve van, amely nem igényel MSA-t).
4. A vendég et az alábbiakban ismertetett [hozzájárulási élményen](#consent-experience-for-the-guest) keresztül vezetjük.

## <a name="redemption-through-a-direct-link"></a>Közvetlen kapcsolaton keresztüli megváltás

A meghívó e-mail alternatívájaként közvetlen hivatkozást adhat a vendégnek az alkalmazásra vagy a portálra. Először hozzá kell adnia a vendégfelhasználót a címtárhoz az [Azure Portalon](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) vagy a [PowerShellen](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)keresztül. Ezután használhatja az [alkalmazások felhasználók számára történő központi telepítésének bármely testreszabható módját,](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences)beleértve a közvetlen bejelentkezési hivatkozásokat is. Ha egy vendég közvetlen hivatkozást használ a meghívó e-mail helyett, a rendszer továbbra is végigvezeti őket az első hozzájárulási élményen.

> [!IMPORTANT]
> A közvetlen kapcsolatnak bérlőspecifikusnak kell lennie. Más szóval tartalmaznia kell egy bérlői azonosítót vagy ellenőrzött tartományt, hogy a vendég hitelesíthető legyen a bérlőben, ahol a megosztott alkalmazás található. Egy közös https://myapps.microsoft.com URL-cím, mint nem működik a vendég, mert átirányítja a saját bérlő hitelesítésre. Íme néhány példa a bérlői környezettel való közvetlen kapcsolatokra:
 > - Alkalmazások hozzáférési https://myapps.microsoft.com/?tenantid=&ltpanelje: ;bérlői azonosító&gt; 
 > - Alkalmazások hozzáférési panelje ellenőrzött https://myapps.microsoft.com/&lttartományhoz: ;ellenőrzött tartomány&gt;
 > - Azure portal: https://portal.azure.com/&lt;bérlőazonosító&gt;
 > - Egyéni alkalmazás: tekintse meg, hogyan használhatja a [közvetlen bejelentkezési hivatkozást](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Vannak olyan esetek, amikor a meghívó e-mail ajánlott egy közvetlen linken keresztül. Ha ezek a különleges esetek fontosak a szervezet számára, javasoljuk, hogy olyan módszerekkel hívja meg a felhasználókat, amelyek továbbra is elküldik a meghívó e-mailt:
 - A felhasználó nem rendelkezik Azure AD-fiókkal, MSA-val vagy e-mail fiókkal egy összevont szervezetben. Hacsak nem használja az egyszeri jelkód funkciót, a vendégnek be kell váltania a meghívó e-mailt, amely végigvezeti az MSA létrehozásának lépésein.
 - Előfordulhat, hogy a meghívott felhasználói objektumnak nincs e-mail címe egy névjegyobjektummal (például egy Outlook-névjegyobjektummal) való ütközés miatt. Ebben az esetben a felhasználónak a meghívó e-mailben a beváltási URL-címre kell kattintania.
 - A felhasználó a meghívott e-mail-cím aliasával jelentkezhet be. (Az alias egy e-mail fiókhoz társított további e-mail cím.) Ebben az esetben a felhasználónak a meghívó e-mailben a beváltási URL-címre kell kattintania.

## <a name="invitation-redemption-flow"></a>Meghívó-visszaváltási folyamat

Amikor egy felhasználó egy [meghívó e-mailben](invitation-email-elements.md)a **Meghívás elfogadása** hivatkozásra kattint, az Azure AD automatikusan beváltja a meghívót a beváltási folyamat alapján az alábbiak szerint:

![Képernyőkép a beváltási folyamat diagramjának ábrázolása](media/redemption-experience/invitation-redemption-flow.png)

1. A beváltási folyamat ellenőrzi, hogy a felhasználó rendelkezik-e már személyes [Microsoft-fiókkal (MSA).](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create)

2. Ha egy rendszergazda engedélyezte a [közvetlen összevonást](direct-federation.md), az Azure AD ellenőrzi, hogy a felhasználó tartományutótagja megegyezik-e egy konfigurált SAML/WS-Fed identitásszolgáltató tartományával, és átirányítja a felhasználót az előre konfigurált identitásszolgáltatóra.

3. Ha egy rendszergazda engedélyezte a [Google-összevonást,](google-federation.md)az Azure AD ellenőrzi, hogy a felhasználó tartományutótagja gmail.com vagy googlemail.com, és átirányítja a felhasználót a Google-hoz.

4. Az Azure AD felhasználói alapú felderítést hajt végre annak megállapítására, hogy a felhasználó létezik-e egy [meglévő Azure AD-bérlőben.](what-is-b2b.md#easily-add-guest-users-in-the-azure-ad-portal)

5. A felhasználó **kezdőkönyvtárának** azonosítását követően a rendszer elküldi a felhasználót a megfelelő identitásszolgáltatónak a bejelentkezéshez.  

6. Ha az 1–4. [Email one-time passcode (OTP)](one-time-passcode.md)

7. Ha [az e-mail egyszeri jelkód a vendégek számára engedélyezve van,](one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode)a rendszer a meghívott e-mailen keresztül jelkódot küld a felhasználónak. A felhasználó lekéri és adja meg ezt a jelszót az Azure AD bejelentkezési lapon.

8. Ha az e-mail egyszeri jelkód a vendégek számára le van tiltva, az Azure AD ellenőrzi a tartomány utótagot a Microsoft által fenntartott fogyasztói tartománylistával. Ha a tartomány megfelel a fogyasztói tartománylista bármely tartományának, a rendszer kéri a felhasználót, hogy hozzon létre egy személyes Microsoft-fiókot. Ha nem, a rendszer kéri, hogy hozzon létre egy [Azure AD önkiszolgáló fiókot](../users-groups-roles/directory-self-service-signup.md) (vírusos fiók).

9. Az Azure AD megkísérli az Azure AD önkiszolgáló fiók (vírusfiók) létrehozását az e-mailhez való hozzáférés ellenőrzésével. A fiók ellenőrzése úgy történik, hogy egy kódot küld az e-mailnek, és a felhasználó lekéri és elküldi az Azure AD-nek. Ha azonban a meghívott felhasználó bérlője össze van vonva, vagy ha az AllowEmailVerifiedUsers mező hamis értékre van állítva a meghívott felhasználó bérlőjében, a felhasználó nem tudja befejezni a beváltást, és a folyamat hibát eredményez. További információt az [Azure Active Directory B2B együttműködés hibaelhárítása című témakörben talál.](troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption)

10. A rendszer kéri a felhasználót, hogy hozzon létre egy személyes Microsoft-fiókot (MSA).

11. A megfelelő identitásszolgáltatónak való hitelesítés után a felhasználó átlesz irányítva az Azure AD-be a [jóváhagyási élmény](redemption-experience.md#consent-experience-for-the-guest)befejezéséhez.  

A just-in-time (JIT) beváltások, ahol a beváltás egy bérlői alkalmazás linken keresztül, lépések 8-10 nem érhetők el. Ha a felhasználó eléri a 6. Ennek elkerülése érdekében a rendszergazdáknak engedélyezniük kell [az egyszeri e-mail jelkódot,](one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode) vagy biztosítaniuk kell, hogy a felhasználó egy meghívóhivatkozásra kattintson.

## <a name="consent-experience-for-the-guest"></a>Hozzájárulási tapasztalat a vendég számára

Amikor egy vendég először jelentkezik be egy partnerszervezet erőforrásainak eléréséhez, a program végigvezeti őket a következő oldalakon. 

1. A vendég áttekinti az **Engedélyek áttekintése** lapot, amely leírja a meghívó szervezet adatvédelmi nyilatkozatát. A felhasználónak el kell **fogadnia** az adatai felhasználását a meghívó szervezet adatvédelmi irányelveinek megfelelően a folytatáshoz.

   ![Az Engedélyek ellenőrzése lapot bemutató képernyőkép](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Arról, hogy ön, mint bérlői rendszergazda hogyan hivatkozhat a szervezet adatvédelmi nyilatkozatára, olvassa [el az Útmutató: A szervezet adatvédelmi adatainak hozzáadása az Azure Active Directoryban című témakörben.](https://aka.ms/adprivacystatement)

2. Ha a használati feltételek be vannak állítva, a vendég megnyitja és ellenőrzi a használati feltételeket, majd az **Elfogadás**lehetőséget választja. 

   ![Új használati feltételeket ábrázoló képernyőkép](media/redemption-experience/terms-of-use-accept.png) 

   A használati [feltételeket a](../governance/active-directory-tou.md) Szervezeti kapcsolatok **kezelése** > használati**feltételek**című témakörben**állíthatja** > be.

3. Eltérő rendelkezés hiányában a vendég átlesz irányítva az Alkalmazások hozzáférési panelre, amely felsorolja azokat az alkalmazásokat, amelyekhez a vendég hozzáférhet.

   ![Az Alkalmazások hozzáférési paneljét bemutató képernyőkép](media/redemption-experience/myapps.png) 

A címtárban a vendég **meghívója elfogadta** az **Értékváltozásait Igen**értékre. Ha MSA-t hoztak létre, a vendég **forrása** a **Microsoft-fiókot**jeleníti meg. A vendégfelhasználói fiókok tulajdonságairól további információt az [Azure AD B2B együttműködési felhasználó tulajdonságai című](user-properties.md)témakörben talál. 

## <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [Azure Active Directory B2B együttműködési felhasználók hozzáadása az Azure Portalon](add-users-administrator.md)
- [Hogyan adják hozzá az informatikai dolgozók a B2B együttműködési felhasználókat az Azure Active Directoryhoz?](add-users-information-worker.md)
- [Azure Active Directory B2B együttműködési felhasználók hozzáadása a PowerShell használatával](customize-invitation-api.md#powershell)
- [Kilépés a szervezetből vendégfelhasználóként](leave-the-organization.md)
