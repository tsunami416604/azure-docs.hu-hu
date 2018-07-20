---
title: Mikor és hogyan használhatja az Azure multi-factor Auth szolgáltatót?
description: Mikor használjam a hitelesítésszolgáltatót az Azure MFA használatba Vételekor?
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 4a6ce07bfe641d9efdbe0eac841bb4f27f468b34
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39161464"
---
# <a name="when-to-use-an-azure-multi-factor-authentication-provider"></a>Mikor érdemes használni az Azure multi-factor Authentication-szolgáltatót

A kétlépéses ellenőrzés alapértelmezés szerint elérhető az Azure Active Directory- és Office 365-felhasználókkal rendelkező globális adminisztrátorok számára. De ha ki szeretné használni a [speciális szolgáltatásokat](howto-mfa-mfasettings.md), az Azure Multi-Factor Authentication (MFA) teljes verzióját meg kell vásárolnia.

Az Azure Multi-Factor Auth szolgáltatókkal kihasználhatja az Azure MFA teljes verziója által nyújtott szolgáltatásokat. A felhasználók számára **nem rendelkeznek licenccel az Azure MFA, Azure AD Premium vagy prémium szintű Azure AD vagy az Azure MFA tartalmazó csomagok**. Az Azure MFA és az Azure AD Premium alapértelmezés szerint, az Azure MFA teljes verzióját tartalmazzák.

Ha rendelkezik licencekkel, mind a minden felhasználó a szervezetben, majd nincs szüksége Azure multi-factor Auth szolgáltatót. Hozzon létre egy Azure multi-factor Authentication-szolgáltatót, csak akkor, ha is meg kell adnia a kétlépéses ellenőrzést, amelyek nem rendelkeznek licenccel az egyes felhasználók számára.

## <a name="caveats-related-to-the-azure-mfa-sdk"></a>Az Azure MFA SDK kapcsolatos figyelmeztetések

Az SDK letöltéséhez egy Azure Multi-Factor Auth szolgáltató szükséges. Megjegyzés: az SDK elavult, és az új ügyfelek már nem támogatott, és csak továbbra is működni fognak 2018. November 14-ig. Ezután az SDK felé indított hívások meghiúsulnak.

Az SDK letöltéséhez hozzon létre egy Azure multi-factor Auth szolgáltatót, ha már rendelkezik Azure MFA, AAD Premium vagy egyéb csomagolt licencek. Ha Azure Multi-Factor Auth szolgáltatót hoz létre erre a célra, és már rendelkezik licencekkel, ügyeljen arra, hogy a szolgáltatót az **Engedélyezett felhasználónként** modellel hozza létre. Ezt követően kapcsolja a szolgáltatót arra a könyvtárra, amely tartalmazza az Azure MFA, Azure AD Premium vagy egyéb csomagolt licencek. Ezzel a konfigurációval biztosíthatja, hogy csak akkor számlázzunk Önnek, amikor több egyedi felhasználója végez kétlépéses ellenőrzést, mint ahány licenccel rendelkezik.

## <a name="what-is-an-mfa-provider"></a>Mi az az MFA-szolgáltató?

Ha az Azure multi-factor Authentication nem rendelkezik licencekkel, létrehozhat egy hitelesítési szolgáltatót a kétlépéses ellenőrzés megkövetelése a felhasználók számára.

Hitelesítésszolgáltatók két típusa van, és a különbséget körül módját az Azure-előfizetés után kell fizetni. A hitelesítésenkénti lehetőség választásakor a rendszer kiszámítja a bérlőn havonta végzett hitelesítések számát. Ezt a lehetőséget akkor érdemes használni, ha néhány felhasználó csak alkalmanként végez hitelesítést. A felhasználónkénti lehetőség választásakor a rendszer kiszámítja a bérlőn egy hónap alatt kétlépéses ellenőrzést végző egyének számát. Ez a lehetőség akkor a legjobb, ha van néhány licenccel rendelkező felhasználója, de ki kell terjesztenie az MFA-t a licenckorlátján túl több felhasználóra.

## <a name="create-an-mfa-provider"></a>MFA-szolgáltató létrehozása

A következő lépésekkel hozhat létre Azure Multi-Factor Authentication-szolgáltatót az Azure Portalon:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) globális rendszergazdaként.
2. Válassza az **Azure Active Directory** > **MFA-kiszolgáló** > **Szolgáltatók** lehetőséget.

   ![Szolgáltatók][Providers]

3. Válassza a **Hozzáadás** lehetőséget.
4. Töltse ki az alábbi mezőket, és válassza a **Hozzáadás** lehetőséget:
   - **Név** – A szolgáltató neve.
   - **Használati modell** – Válassza az alábbi két lehetőség egyikét:
      * Hitelesítésenként – Hitelesítésenként díjat felszámító vásárlási modell. Általában ügyfél felől elérhető alkalmazásokban Azure Multi-Factor Authentication hitelesítést használó forgatókönyvekben használják.
      * Engedélyezett felhasználónként – Felhasználónként díjat felszámító vásárlási modell. Általában az alkalmazottak alkalmazásokhoz (pl. az Office 365-höz) való hozzáféréséhez használják. Akkor válassza ezt a beállítást, ha már van néhány Azure MFA-licenccel ellátott felhasználója.
   - **Előfizetés** – Az Azure-előfizetés, amely után fizet a szolgáltatón keresztül végzett kétlépéses ellenőrzési tevékenységért.
   - **Címtár** – Az Azure Active Directory-bérlő, amelyhez a szolgáltató társítva van.
      * Szolgáltató létrehozásához nincs szükség Azure AD-címtárra. Ha csak az Azure Multi-Factor Authentication-kiszolgálót szeretné letölteni, hagyja üresen a mezőt.
      * A szolgáltatót egy Azure AD-címtárral kell társítani a speciális szolgáltatások igénybevételéhez.
      * Egy Azure AD-címtárral csak egyetlen szolgáltató társítható.

## <a name="manage-your-mfa-provider"></a>Az MFA-szolgáltató kezelése

Az MFA szolgáltató létrehozását követően már nem módosíthatja a használati modellt (engedélyezett felhasználónként vagy hitelesítésenként). Azonban törölheti az MFA szolgáltatót, majd létrehozhat egy újat egy eltérő használati modellel.

Ha az aktuális Multi-Factor Auth szolgáltató Azure AD-címtárral (más néven Azure AD-bérlővel) van társítva, akkor biztonságosan törölheti az MFA szolgáltatót, majd létrehozhat egy újat, amely ugyanahhoz az Azure AD-bérlőhöz van kapcsolva. Azt is megteheti Ha elegendő MFA, prémium szintű Azure AD vagy az összes olyan felhasználó, amelyek engedélyezve vannak a multi-factor Authentication prémium szintű Azure AD vagy az Azure MFA licenceit is tartalmazó csomagok vásárolt, törölheti az MFA-szolgáltató teljesen.

Ha az MFA szolgáltató nincs Azure AD-bérlőhöz kapcsolva, vagy új MFA szolgáltatót kapcsol egy másik Azure AD-bérlőhöz, a felhasználói és konfigurációs beállításokat a rendszer nem viszi át. Emellett a meglévő Azure MFA-kiszolgálókat újra aktiválni kell az új MFA szolgáltatón keresztül létrehozott aktiváló hitelesítők adatokkal. Az MFA-kiszolgálók az új MFA szolgáltatóhoz történő kapcsolás céljából újraaktiválása nem érinti, telefonhívás vagy szöveges üzenetben, de a mobilalkalmazás-értesítések leáll az összes felhasználó számára addig, amíg a mobilalkalmazás hitelesítésekre.

## <a name="next-steps"></a>További lépések

[A Multi-Factor Authentication beállításainak konfigurálása](howto-mfa-mfasettings.md)

[Providers]: ./media/concept-mfa-authprovider/add-providers.png "MFA-szolgáltatók hozzáadása"
