---
title: Meghívás megváltása B2B-együttműködésben – Azure Active Directory | Microsoft Docs
description: Ismerteti az Azure AD B2B együttműködés meghívásának beváltási élményét a végfelhasználók számára, beleértve az adatvédelmi feltételeket is.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d32818f9e96e931f9e8c3c13554752327c5c456
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622610"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Azure Active Directory B2B együttműködés meghívásának beváltása

Ez a cikk bemutatja, hogyan érhetik el a vendég felhasználói az erőforrásokat és az azokhoz való belekapcsolódási folyamatot. Ha meghívó e-mailt küld a vendégnek, a meghívás tartalmaz egy hivatkozást, amely beválthat, hogy hozzáférjen az alkalmazáshoz vagy a portálhoz. A meghívás e-mail-címe csak egyike annak, hogy a vendégek hozzáférjenek az erőforrásokhoz. Másik lehetőségként hozzáadhat vendégeket a címtárhoz, és közvetlenül a megosztani kívánt portálra vagy alkalmazásra mutató hivatkozást adhat hozzájuk. Az általuk használt módszertől függetlenül a vendégek az első alkalommal történő belefoglalási folyamaton keresztül vezérlik. Ez a folyamat biztosítja, hogy a vendégek elfogadják az adatvédelmi feltételeket, és elfogadják a beállított használati [feltételeket](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) .

Amikor vendég felhasználót ad hozzá a címtárhoz, a vendég felhasználói fióknak van egy engedélyezési állapota (megtekinthető a PowerShellben), amely eredetileg **PendingAcceptance**-re van állítva. Ez a beállítás addig marad, amíg a vendég elfogadja a meghívót, és elfogadja az adatvédelmi szabályzatát és használati feltételeit. Ezt követően a hozzájárulási állapot elfogadvaértékűre változik, és a jóváhagyás lapjai már nem jelennek meg a vendég számára.

## <a name="redemption-through-the-invitation-email"></a>Visszaváltás a meghívó e-mailben

Ha [a Azure Portal használatával](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal)ad hozzá egy vendég felhasználót a címtárhoz, a rendszer meghívót küld a vendégnek a folyamatban. Azt is megteheti, hogy a [PowerShell használatával](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) küldi el a meghívó e-maileket, ha vendég felhasználókat ad hozzá a címtárhoz. Itt látható a vendég felhasználói felületének leírása, amikor beváltják a hivatkozást az e-mailben.

1. A vendég a **Microsoft**meghívótól kapott [meghívó e-mailt](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) kap.
2. A vendég kiválasztja az **első lépéseket** az e-mailben.
3. Ha a vendég nem rendelkezik Azure AD-fiókkal, egy Microsoft-fiókkal (MSA) vagy egy összevont szervezethez tartozó e-mail-fiókkal, a rendszer kéri, hogy hozzon létre egy MSA (kivéve, ha az [egyszeri jelszó](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) funkció engedélyezve van, és nincs szükség MSA).
4. A vendég irányítását az alábbiakban ismertetett belefoglalási [élmény](#consent-experience-for-the-guest) vezérli.

## <a name="redemption-through-a-direct-link"></a>Váltás közvetlen kapcsolaton keresztül

A meghívót tartalmazó e-mailek alternatívájaként az alkalmazásra vagy a portálra mutató közvetlen hivatkozást is megadhat. Először hozzá kell adnia a vendég felhasználót a címtárhoz a [Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) vagy a [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell)használatával. Ezután bármilyen [testreszabható módszert használhat az alkalmazások felhasználók számára történő központi telepítéséhez](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), beleértve a közvetlen bejelentkezési hivatkozásokat is. Ha egy vendég közvetlen hivatkozást használ a meghívó e-mail-címe helyett, a rendszer továbbra is az első belefoglalási élményt fogja követni.

> [!IMPORTANT]
> A közvetlen hivatkozásnak bérlő-specifikusnak kell lennie. Más szóval tartalmaznia kell egy bérlői azonosítót vagy egy ellenőrzött tartományt, hogy a vendég hitelesíthető legyen a bérlőben, ahol a megosztott alkalmazás található. Egy közös URL- https://myapps.microsoft.com cím, például nem fog működni a vendég számára, mert az átirányítja a saját bérlőre a hitelesítéshez. Íme néhány példa a bérlői környezettel kapcsolatos közvetlen hivatkozásokra:
 > - Alkalmazások hozzáférési panelje https://myapps.microsoft.com/?tenantid=&lt:; bérlő azonosítója&gt; 
 > - Az ellenőrzött tartományhoz tartozó alkalmazások hozzáférési panelje: https://myapps.microsoft.com/&lt; ellenőrzött tartomány&gt;
 > - Azure Portal: https://portal.azure.com/&lt; bérlő azonosítója&gt;
 > - Egyéni alkalmazás: lásd a [közvetlen bejelentkezési hivatkozás](../manage-apps/end-user-experiences.md#direct-sign-on-links) használatát ismertető témakört.

Vannak olyan esetek, amikor a meghívó e-mail-címe közvetlen hivatkozáson keresztül ajánlott. Ha ezek a speciális esetek fontosak a szervezet számára, javasoljuk, hogy a meghívó e-mailt még elküldő metódusok használatával meghívja a felhasználókat:
 - A felhasználónak nincs Azure AD-fiókja, MSA vagy e-mail-fiókja egy összevont szervezetben. Hacsak nem használja az egyszeri PIN-kódot, a vendégnek be kell váltania a meghívó e-mail-címét, hogy a MSA létrehozásának lépésein legyenek vezérelve.
 - Előfordulhat, hogy a meghívott felhasználói objektum nem rendelkezik e-mail-címmel, mert ütközik egy Contact objektummal (például egy Outlook Contact objektummal). Ebben az esetben a felhasználónak a meghívót tartalmazó e-mailben kell kattintania a beváltási URL-címre.
 - A felhasználó a meghívott e-mail-cím aliasával jelentkezhet be. (Az alias egy e-mail-fiókhoz társított e-mail-cím.) Ebben az esetben a felhasználónak a meghívót tartalmazó e-mailben kell kattintania a beváltási URL-címre.

## <a name="consent-experience-for-the-guest"></a>A vendég beleegyezett a felhasználói élménybe

Amikor egy vendég először jelentkezik be a partner szervezet erőforrásaihoz, a következő lapokon keresztül vezérelheti őket. 

1. A vendég áttekinti a **felülvizsgálati engedélyek** lapot, amely a meghívó szervezet adatvédelmi nyilatkozatát ismerteti. A felhasználóknak **el kell fogadniuk** az adataikat a meghívó szervezet adatvédelmi szabályzatai alapján a folytatáshoz.

   ![A felülvizsgálati engedélyek oldalát ábrázoló képernyőkép](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Arról, hogy a bérlői rendszergazda hogyan hivatkozhat a szervezete adatvédelmi nyilatkozatára, tekintse [meg a útmutató: Adja hozzá a szervezete adatvédelmi adatait Azure Active Directory](https://aka.ms/adprivacystatement).

2. Ha a használati feltételek konfigurálva vannak, a vendég megnyílik, és áttekinti a használati feltételeket, majd kiválasztja az **elfogadás**lehetőséget. 

   ![Az új használati feltételeket bemutató képernyőkép](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > A következő témakörben megadhatja a [használati feltételeket](../governance/active-directory-tou.md) a**szervezeti kapcsolatok** >  **kezelése** > **használati feltételek**.

3. Ha másként nincs megadva, a rendszer átirányítja a vendéget az alkalmazások hozzáférési paneljére, amely felsorolja a vendég által elérhető alkalmazásokat.

   ![Az alkalmazások hozzáférési paneljét ábrázoló képernyőfelvétel](media/redemption-experience/myapps.png) 

A címtárban a vendég meghívásának **elfogadott** értéke **Igen**értékre változik. Ha létrejött egy MSA, a vendég **forrása** a Microsoft- **fiókot**jeleníti meg. További információ a vendég felhasználói fiók tulajdonságairól: [Azure ad B2B csoportmunka-felhasználó tulajdonságai](user-properties.md). 

## <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [Azure Active Directory B2B együttműködés felhasználók hozzáadása az Azure Portalon](add-users-administrator.md)
- [Hogyan vehetik igénybe az Information Worker VÁLLALATKÖZI együttműködési felhasználókat a Azure Active Directory?](add-users-information-worker.md)
- [Azure Active Directory B2B csoportmunka-felhasználók hozzáadása a PowerShell használatával](customize-invitation-api.md#powershell)
- [A szervezet kihagyása vendég felhasználóként](leave-the-organization.md)
