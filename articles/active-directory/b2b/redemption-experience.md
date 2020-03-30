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
ms.openlocfilehash: 043e0f3a0ff2c1c642c63a387c571b575f77cf7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050835"
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

## <a name="consent-experience-for-the-guest"></a>Hozzájárulási tapasztalat a vendég számára

Amikor egy vendég először jelentkezik be egy partnerszervezet erőforrásainak eléréséhez, a program végigvezeti őket a következő oldalakon. 

1. A vendég áttekinti az **Engedélyek áttekintése** lapot, amely leírja a meghívó szervezet adatvédelmi nyilatkozatát. A felhasználónak el kell **fogadnia** az adatai felhasználását a meghívó szervezet adatvédelmi irányelveinek megfelelően a folytatáshoz.

   ![Az Engedélyek ellenőrzése lapot bemutató képernyőkép](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Arról, hogy ön, mint bérlői rendszergazda hogyan hivatkozhat a szervezet adatvédelmi nyilatkozatára, olvassa [el az Útmutató: A szervezet adatvédelmi adatainak hozzáadása az Azure Active Directoryban című témakörben.](https://aka.ms/adprivacystatement)

2. Ha a használati feltételek be vannak állítva, a vendég megnyitja és ellenőrzi a használati feltételeket, majd az **Elfogadás**lehetőséget választja. 

   ![Új használati feltételeket ábrázoló képernyőkép](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > A használati [feltételeket a](../governance/active-directory-tou.md) Szervezeti kapcsolatok **kezelése** > használati**feltételek**című témakörben**állíthatja** > be.

3. Eltérő rendelkezés hiányában a vendég átlesz irányítva az Alkalmazások hozzáférési panelre, amely felsorolja azokat az alkalmazásokat, amelyekhez a vendég hozzáférhet.

   ![Az Alkalmazások hozzáférési paneljét bemutató képernyőkép](media/redemption-experience/myapps.png) 

A címtárban a vendég **meghívója elfogadta** az **Értékváltozásait Igen**értékre. Ha MSA-t hoztak létre, a vendég **forrása** a **Microsoft-fiókot**jeleníti meg. A vendégfelhasználói fiókok tulajdonságairól további információt az [Azure AD B2B együttműködési felhasználó tulajdonságai című](user-properties.md)témakörben talál. 

## <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [Azure Active Directory B2B együttműködési felhasználók hozzáadása az Azure Portalon](add-users-administrator.md)
- [Hogyan adják hozzá az informatikai dolgozók a B2B együttműködési felhasználókat az Azure Active Directoryhoz?](add-users-information-worker.md)
- [Azure Active Directory B2B együttműködési felhasználók hozzáadása a PowerShell használatával](customize-invitation-api.md#powershell)
- [Kilépés a szervezetből vendégfelhasználóként](leave-the-organization.md)
