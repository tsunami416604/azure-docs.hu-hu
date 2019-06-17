---
title: A B2B-együttműködés – Azure Active Directory vendégmeghívás beváltása |} A Microsoft Docs
description: Ismerteti az Azure AD B2B együttműködés meghívó érvényesítési élmény a végfelhasználók számára, beleértve a szerződést, adatvédelmi feltételeket.
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
ms.openlocfilehash: a80eaa134130195fce00ee6a4d68851e478c4532
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052466"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Az Azure Active Directory B2B együttműködés vendégmeghívás beváltása

Ez a cikk leírja, hogyan vendég felhasználók férhetnek hozzá az erőforrások és a jóváhagyási folyamat akkor ütközik. A Vendég-meghívó e-mailt küld, ha a meghívó egy hivatkozást tartalmaz a Vendég beválthatja érhet el az alkalmazást vagy a portálon. A meghívó e-mailben csak egyik módja a vendégek hozzáférhet az erőforrásokhoz. Alternatív megoldásként Vendégek hozzáadása a címtárhoz, és tegye lehetővé számukra egy közvetlen hivatkozást a portálon vagy a megosztani kívánt alkalmazáshoz. Módszertől függetlenül használnak vendégek végigvezeti egy első jóváhagyási folyamatot. Ez a folyamat biztosítja, hogy a vendégek adatvédelmi feltételek elfogadása, és fogadja el bármelyik [használati feltételeket tartalmazó fájl](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) beállítását.

Vendégfelhasználó hozzáadása a címtárhoz, a Vendég felhasználói fiókhoz rendelkezik egy jóváhagyási állapota (megtekinthető a PowerShellben), amely kezdetben **PendingAcceptance**. Ez a beállítás marad mindaddig, amíg a Vendég elfogadja a meghívást, és vállalja, hogy az adatvédelmi szabályzat és a használati feltételeket. Ezt követően a jóváhagyás állapota **elfogadva**, és a jóváhagyás lapok már nem jelennek meg a Vendég.

## <a name="redemption-through-the-invitation-email"></a>A meghívó e-mailes beváltása

Ha a vendégfelhasználó hozzáadása a könyvtárhoz való [az Azure portal használatával](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal), a folyamat a Vendég-meghívó e-mailt küld. Azt is beállíthatja a meghívó e-mailek küldése, amikor Ön [PowerShell-lel](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell) vendégfelhasználók hozzáadása a címtárhoz. Ha azok beváltása az e-mailben szereplő hivatkozásra, Íme a vendégeknek kínált élményt leírását.

1. A Vendég kap egy [meghívó e-mail](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) érkezett, amely **Microsoft Invitations**.
2. A Vendég kiválasztja **Ismerkedés** az e-mailben.
3. Ha a Vendég egy összevont szervezet nem rendelkezik egy Azure AD-fiókot, egy Microsoft fiók (MSA) vagy egy e-mail-fiókot, hozzon létre egy MSA kell adnia (kivéve, ha a [egyszer használatos jelszót](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) szolgáltatás engedélyezve van, amely nem igényel az MSA ).
4. A Vendég rendszer végigvezeti a [élmény hozzájárulás](#consent-experience-for-the-guest) az alábbiakban.

## <a name="redemption-through-a-direct-link"></a>Közvetlen hivatkozás segítségével elvégezhetné beváltása

A meghívó e-mailek alternatívájaként biztosíthat a Vendég mutató közvetlen hivatkozást az alkalmazásához, vagy a portál. Először hozzá kell a vendégfelhasználót a címtár-n keresztül a [az Azure portal](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal) vagy [PowerShell](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-invite-powershell). Ezt követően használhatja bármelyik a [testre szabható módon telepíthet alkalmazásokat a felhasználók számára](https://docs.microsoft.com/azure/active-directory/manage-apps/end-user-experiences), beleértve a közvetlen bejelentkezés hivatkozásokat. Amikor egy Vendég helyett a meghívó e-mailt mutató közvetlen hivatkozást használja, akkor továbbra is rendszer végigvezeti az első jóváhagyási felületen.

> [!IMPORTANT]
> A közvetlen hivatkozás bérlőspecifikus kell lennie. Más szóval a bérlő Azonosítóját kell tartalmaznia, vagy ellenőrzött tartomány, így a Vendég hitelesítése a saját bérlőjében, ahol a megosztott alkalmazás megtalálható. Egy általános URL-címet, például https://myapps.microsoft.com a vendég nem fog működni, mert azt a saját bérlőjén hitelesítéshez fogja átirányítani Önt. Íme néhány példa a bérlő kontextusú közvetlen hivatkozásokat:
 > - Alkalmazások hozzáférési panelje: https://myapps.microsoft.com/?tenantid=&lt; bérlő azonosítója&gt; 
 > - Alkalmazások hozzáférési panelje egy ellenőrzött tartomány: https://myapps.microsoft.com/&lt; ellenőrzött tartomány&gt;
 > - Az Azure portal: https://portal.azure.com/&lt; bérlő azonosítója&gt;
 > - Az egyes alkalmazásokra: hogyan használhatja a [közvetlen bejelentkezés hivatkozás](../manage-apps/end-user-experiences.md#direct-sign-on-links)

Vannak bizonyos esetekben, ahol a meghívó e-mailt ajánlott egy közvetlen kapcsolaton keresztül. Ha ilyen speciális esetekben fontos a szervezetben, azt javasoljuk, hogy a felhasználók a meghívó e-mailben is elküldheti módszerrel meghívása:
 - A felhasználó nem rendelkezik egy Azure AD-fiókot, egy MSA vagy e-mail-fiók egy összevont szervezetben. A kapott egyszer használatos jelszót funkciót használja, ha a Vendég kell beváltani a meghívót tartalmazó e, és haladjon végig a lépéseket az MSA létrehozásához.
 - Egyes esetekben a meghívott felhasználó objektumot nem lehet egy e-mail-cím kapcsolatobjektummal (például az Outlook kapcsolattartási objektum) való ütközés miatt. Ebben az esetben a felhasználónak rá kell kattintania a beváltási URL-címet a meghívó e-mailben.
 - A felhasználó lehet, hogy jelentkezzen be az e-mail-cím, amely meghívták alias. (Alias az e-mail-fiók társított további e-mail címmel.) Ebben az esetben a felhasználónak rá kell kattintania a beváltási URL-címet a meghívó e-mailben.

## <a name="consent-experience-for-the-guest"></a>A Vendég a jóváhagyási felületen

Vendég jelentkezik be először egy fiókpartner-szervezet erőforrásaihoz, ha azok még végigvezeti a következő lapokon. 

1. A Vendég felülvizsgálatok a **tekintse át az engedélyek** a meghívó szervezetet adatvédelmi nyilatkozat ismertető oldal. A felhasználó kell **elfogadás** adataik nevezhetnek, a folytatáshoz a meghívó szervezetet adatvédelmi szabályzatok használatát.

   ![Képernyőfelvétel: a felülvizsgálati – engedélyek lap](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > További információ a hogyan bérlői rendszergazdaként hozzákapcsolhatja a szervezete adatvédelmi nyilatkozatát: [Útmutató: Adja hozzá a szervezet adatvédelmi információ az Azure Active Directoryban](https://aka.ms/adprivacystatement).

2. Ha konfigurálva van a használati feltételeket tartalmazó fájl, a Vendég megnyitja, és áttekinti a használati feltételeket, majd kiválasztja **elfogadás**. 

   ![Képernyőfelvétel: az új használati feltételek](media/redemption-experience/terms-of-use-accept.png) 

   > [!NOTE]
   > Lásd: konfigurálhatja [használati feltételeket tartalmazó fájl](../governance/active-directory-tou.md) a **kezelés** > **szervezeti kapcsolatok** > **használatifeltételekettartalmazófájl**.

3. Amennyiben másként nem szerepel, a rendszer átirányítja a Vendég a alkalmazások hozzáférési panelen, amely megjeleníti azokat az alkalmazásokat, a Vendég hozzáférhet.

   ![Képernyőfelvétel: az alkalmazások hozzáférési panelje](media/redemption-experience/myapps.png) 

A címtárban, a Vendég a **Meghívás elfogadva** értéke módosul **Igen**. Ha az MSA lett létrehozva, a vendég **forrás** látható **Microsoft Account**. További információ a Vendég felhasználói fiók tulajdonságainál: [egy Azure AD B2B együttműködés felhasználói tulajdonságainak](user-properties.md). 

## <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [Azure Active Directory B2B együttműködés felhasználók hozzáadása az Azure Portalon](add-users-administrator.md)
- [Az információkkal dolgozó szakemberek hogyan hozzá B2B együttműködési felhasználókat az Azure Active Directory?](add-users-information-worker.md)
- [Azure Active Directory B2B együttműködési felhasználókat adja hozzá a PowerShell használatával](customize-invitation-api.md#powershell)
- [Egy szervezet meg vendégként](leave-the-organization.md)
