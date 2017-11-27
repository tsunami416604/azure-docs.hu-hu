---
title: "Első lépések az Azure Multi-Factor Auth szolgáltatóval | Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre Azure Multi-Factor Auth szolgáltatót."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/02/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: dc1664d382c6e59c125ef00d02a8848079d8bf8d
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2017
---
# <a name="getting-started-with-an-azure-multi-factor-authentication-provider"></a>Első lépések az Azure Multi-Factor Authentication-szolgáltatóval
A kétlépéses ellenőrzés alapértelmezés szerint elérhető az Azure Active Directory- és Office 365-felhasználókkal rendelkező globális adminisztrátorok számára. De ha ki szeretné használni a [speciális szolgáltatásokat](multi-factor-authentication-whats-next.md), az Azure Multi-Factor Authentication (MFA) teljes verzióját meg kell vásárolnia.

Az Azure Multi-Factor Auth szolgáltatókkal kihasználhatja az Azure MFA teljes verziója által nyújtott szolgáltatásokat. Ez olyan felhasználóknak készült, akik **nem rendelkeznek Azure MFA, Azure AD Prémium vagy Enterprise Mobility + Security (EMS) licenccel**.  Az Azure MFA, az Azure AD Prémium és az EMS alapértelmezés szerint tartalmazza az Azure MFA teljes verzióját. Ha rendelkezik licencekkel, akkor nincs szüksége Azure Multi-Factor Auth szolgáltatóra.

Az SDK letöltéséhez egy Azure Multi-Factor Auth szolgáltató szükséges.

> [!IMPORTANT]
> Bejelentettük az Azure Multi-Factor Authentication szoftverfejlesztői készlet (SDK) elavulását. Ez a szolgáltatás már nem támogatott az új ügyfelek számára. A jelenlegi ügyfelek 2018. november 14-ig használhatják az SDK-t. Ezután az SDK felé indított hívások meghiúsulnak.
> [!IMPORTANT]
>Az SDK letöltéséhez létre kell hoznia egy Azure Multi-Factor Auth szolgáltatót, akkor is, ha rendelkezik Azure MFA, AAD Premium vagy EMS licencekkel.  Ha Azure Multi-Factor Auth szolgáltatót hoz létre erre a célra, és már rendelkezik licencekkel, ügyeljen arra, hogy a szolgáltatót az **Engedélyezett felhasználónként** modellel hozza létre. Ezt követően kapcsolja a szolgáltatót az Azure MFA, Azure AD Premium vagy EMS licenceket tartalmazó könyvtárhoz. Ezzel a konfigurációval biztosíthatja, hogy csak akkor számlázzunk Önnek, amikor több egyedi felhasználója végez kétlépéses ellenőrzést, mint ahány licenccel rendelkezik. 

## <a name="what-is-an-mfa-provider"></a>Mi az az MFA-szolgáltató?

Ha nem rendelkezik licencekkel az Azure Multi-Factor Authentication szolgáltatáshoz, akkor létrehozhat egy hitelesítési szolgáltatót, hogy megkövetelje a felhasználóktól a kétlépéses ellenőrzést.

A hitelesítésszolgáltatóknak két típusa van, és aszerint lehet megkülönböztetni őket, hogyan történik az Azure-előfizetési díjának a számítása. A hitelesítésenkénti lehetőség választásakor a rendszer kiszámítja a bérlőn havonta végzett hitelesítések számát. Ezt a lehetőséget akkor érdemes használni, ha néhány felhasználó csak alkalmanként végez hitelesítést. A felhasználónkénti lehetőség választásakor a rendszer kiszámítja a bérlőn egy hónap alatt kétlépéses ellenőrzést végző egyének számát. Ez a lehetőség akkor a legjobb, ha van néhány licenccel rendelkező felhasználója, de ki kell terjesztenie az MFA-t a licenckorlátján túl több felhasználóra.

## <a name="create-an-mfa-provider---public-preview"></a>MFA-szolgáltató létrehozása – Nyilvános előzetes verzió

A következő lépésekkel hozhat létre Azure Multi-Factor Authentication-szolgáltatót az Azure Portalon:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) felületére rendszergazdaként. 
2. Válassza az **Azure Active Directory** > **MFA-kiszolgáló** lehetőséget.
3. Válassza a **Szolgáltatók** elemet.
4. Válassza a **Hozzáadás** lehetőséget.
5. Töltse ki az alábbi mezőket, és válassza a **Hozzáadás** lehetőséget:
   - **Név** – A szolgáltató neve.
   - **Használati modell** – Válassza az alábbi két lehetőség egyikét:
      * Hitelesítésenként – Hitelesítésenként díjat felszámító vásárlási modell. Általában ügyfél felől elérhető alkalmazásokban Azure Multi-Factor Authentication hitelesítést használó forgatókönyvekben használják.
      * Engedélyezett felhasználónként – Felhasználónként díjat felszámító vásárlási modell. Általában az alkalmazottak alkalmazásokhoz (pl. az Office 365-höz) való hozzáféréséhez használják. Akkor válassza ezt a beállítást, ha már van néhány Azure MFA-licenccel ellátott felhasználója.
   - **Előfizetés** – Az Azure-előfizetés, amely után fizet a szolgáltatón keresztül végzett kétlépéses ellenőrzési tevékenységért. 
   - **Címtár** – Az Azure Active Directory-bérlő, amelyhez a szolgáltató társítva van.
      * Szolgáltató létrehozásához nincs szükség Azure AD-címtárra. Ha csak az Azure Multi-Factor Authentication-kiszolgálót szeretné letölteni, hagyja üresen a mezőt.
      * A szolgáltatót egy Azure AD-címtárral kell társítani a speciális szolgáltatások igénybevételéhez.
      * Egy Azure AD-címtárral csak egyetlen szolgáltató társítható.

## <a name="create-an-mfa-provider"></a>MFA-szolgáltató létrehozása
A következő lépésekkel hozhat létre Azure Multi-Factor Authentication-szolgáltatót a klasszikus portálon:

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com) rendszergazdaként.
2. A bal oldalon válassza az **Active Directory** elemet.
3. Az Active Directory lap tetején válassza a **Multi-Factor Authentication szolgáltatók** elemet.
   
   ![MFA-szolgáltató létrehozása](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)

4. Alul kattintson az **Új** elemre.
   
   ![MFA-szolgáltató létrehozása](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)

5. Az App Services alatt válassza a **Multi-Factor Auth szolgáltató** lehetőséget
   
   ![MFA-szolgáltató létrehozása](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)

6. Kattintson a **Gyors létrehozás** gombra.
   
   ![MFA-szolgáltató létrehozása](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)

7. Töltse ki az alábbi mezőket, és válassza a **Létrehozás** lehetőséget.
   1. **Név** – A szolgáltató neve.
   2. **Használati modell** – Válassza az alábbi két lehetőség egyikét:
      * Hitelesítésenként – Hitelesítésenként díjat felszámító vásárlási modell. Általában ügyfél felől elérhető alkalmazásokban Azure Multi-Factor Authentication hitelesítést használó forgatókönyvekben használják.
      * Engedélyezett felhasználónként – Felhasználónként díjat felszámító vásárlási modell. Általában az alkalmazottak alkalmazásokhoz (pl. az Office 365-höz) való hozzáféréséhez használják. Akkor válassza ezt a beállítást, ha már van néhány Azure MFA-licenccel ellátott felhasználója.
   3. **Címtár** – Az Azure Active Directory-bérlő, amelyhez a szolgáltató társítva van.
      * Szolgáltató létrehozásához nincs szükség Azure AD-címtárra. Ha csak az Azure Multi-Factor Authentication-kiszolgálót szeretné letölteni, hagyja üresen a mezőt.
      * A szolgáltatót egy Azure AD-címtárral kell társítani a speciális szolgáltatások igénybevételéhez.
      * Egy Azure AD-címtárral csak egyetlen szolgáltató társítható.  
      ![MFA-szolgáltató létrehozása](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)

8. Ha a Létrehozás gombra kattint, létrejön a többtényezős hitelesítésszolgáltató, és megjelenik a következő üzenet: **Sikeresen létrejött a többtényezős hitelesítésszolgáltató**. Kattintson az **OK** gombra.  
   
   ![MFA-szolgáltató létrehozása](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)  

## <a name="manage-your-mfa-provider"></a>Az MFA-szolgáltató kezelése

Az MFA szolgáltató létrehozását követően már nem módosíthatja a használati modellt (engedélyezett felhasználónként vagy hitelesítésenként). Azonban törölheti az MFA szolgáltatót, majd létrehozhat egy újat egy eltérő használati modellel.

Ha az aktuális Multi-Factor Auth szolgáltató Azure AD-címtárral (más néven Azure AD-bérlővel) van társítva, akkor biztonságosan törölheti az MFA szolgáltatót, majd létrehozhat egy újat, amely ugyanahhoz az Azure AD-bérlőhöz van kapcsolva. Másik lehetőségként, ha elegendő MFA, prémium szintű Azure AD vagy Enterprise Mobility + Security (EMS) licencet vásárolt az összes olyan felhasználó lefedésére, akiknél az MFA engedélyezett, akkor teljes egészében törölheti az MFA szolgáltatót.

Ha az MFA szolgáltató nincs Azure AD-bérlőhöz kapcsolva, vagy új MFA szolgáltatót kapcsol egy másik Azure AD-bérlőhöz, a felhasználói és konfigurációs beállításokat a rendszer nem viszi át. Emellett a meglévő Azure MFA-kiszolgálókat újra aktiválni kell az új MFA szolgáltatón keresztül létrehozott aktiváló hitelesítők adatokkal. Az MFA-kiszolgálók az új MFA szolgáltatóhoz történő kapcsolás céljából történő újbóli aktiválása nincs hatással a telefonhívásban vagy szöveges üzenetben történő hitelesítésekre, a mobilalkalmazás-értesítések viszont minden felhasználó számára megszűnnek működni a mobilalkalmazás újbóli aktiválásáig.

## <a name="next-steps"></a>Következő lépések

[A Multi-Factor Authentication beállításainak konfigurálása](multi-factor-authentication-whats-next.md)
