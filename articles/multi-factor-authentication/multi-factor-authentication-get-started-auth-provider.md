---
title: "Első lépések az Azure Multi-Factor Auth szolgáltatóval | Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre Azure Multi-Factor Auth szolgáltatót."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 977640041f4b58a751848c96e2aa48eb2b284154
ms.contentlocale: hu-hu
ms.lasthandoff: 06/30/2017

---

# <a name="getting-started-with-an-azure-multi-factor-auth-provider"></a>Első lépések az Azure Multi-Factor Auth szolgáltatóval
A kétlépéses ellenőrzés alapértelmezés szerint elérhető az Azure Active Directory- és Office 365-felhasználókkal rendelkező globális adminisztrátorok számára. De ha ki szeretné használni a [speciális szolgáltatásokat](multi-factor-authentication-whats-next.md), az Azure Multi-Factor Authentication (MFA) teljes verzióját meg kell vásárolnia.

Az Azure Multi-Factor Auth szolgáltatókkal kihasználhatja az Azure MFA teljes verziója által nyújtott szolgáltatásokat. Ez olyan felhasználóknak készült, akik **nem rendelkeznek Azure MFA, Azure AD Prémium vagy Enterprise Mobility + Security (EMS) licenccel**.  Az Azure MFA, az Azure AD Prémium és az EMS alapértelmezés szerint tartalmazza az Azure MFA teljes verzióját. Ha rendelkezik licencekkel, akkor nincs szüksége Azure Multi-Factor Auth szolgáltatóra.

Az SDK letöltéséhez egy Azure Multi-Factor Auth szolgáltató szükséges.

> [!IMPORTANT]
> Az SDK letöltéséhez hozzon létre egy Azure Multi-Factor Auth szolgáltatót, akkor is, ha rendelkezik Azure MFA, AAD Premium vagy EMS licencekkel.  Ha Azure Multi-Factor Auth szolgáltatót hoz létre erre a célra, és már rendelkezik licencekkel, ügyeljen arra, hogy a szolgáltatót az **Engedélyezett felhasználónként** modellel hozza létre. Ezt követően kapcsolja a szolgáltatót az Azure MFA, Azure AD Premium vagy EMS licenceket tartalmazó könyvtárhoz. Ezzel a konfigurációval biztosíthatja, hogy csak akkor számlázzunk Önnek, amikor több egyedi felhasználója végez kétlépéses ellenőrzést, mint ahány licenccel rendelkezik.

## <a name="what-is-an-azure-multi-factor-auth-provider"></a>Mi az az Azure Multi-Factor hitelesítési szolgáltató?

Ha nem rendelkezik licencekkel az Azure Multi-Factor Authentication szolgáltatáshoz, akkor létrehozhat egy hitelesítési szolgáltatót, hogy megkövetelje a felhasználóktól a kétlépéses ellenőrzést. Ha egyéni alkalmazást fejleszt, és szeretné engedélyezni az Azure MFA-t, hozzon létre egy hitelesítési szolgáltatót, és [töltse le az SDK-t](multi-factor-authentication-sdk.md).

A hitelesítésszolgáltatóknak két típusa van, és aszerint lehet megkülönböztetni őket, hogyan történik az Azure-előfizetési díjának a számítása. A hitelesítésenkénti lehetőség választásakor a rendszer kiszámítja a bérlőn havonta végzett hitelesítések számát. Ez a lehetőség akkor a legjobb, ha néhány felhasználó csak alkalmanként végez hitelesítést, mint amikor az MFA-ra egyéni alkalmazáshoz van szüksége. A felhasználónkénti lehetőség választásakor a rendszer kiszámítja a bérlőn egy hónap alatt kétlépéses ellenőrzést végző egyének számát. Ez a lehetőség akkor a legjobb, ha van néhány licenccel rendelkező felhasználója, de ki kell terjesztenie az MFA-t a licenckorlátján túl több felhasználóra.

## <a name="create-a-multi-factor-auth-provider"></a>Multi-Factor Auth szolgáltató létrehozása
A következő lépésekkel hozhat létre Azure Multi-Factor Auth szolgáltatót.

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
   1. **Név** – A Multi-Factor Auth szolgáltató neve.
   2. **Használati modell** – Válassza az alábbi két lehetőség egyikét:
      * Hitelesítésenként – Hitelesítésenként díjat felszámító vásárlási modell. Általában ügyfél felől elérhető alkalmazásokban Azure Multi-Factor Authentication hitelesítést használó forgatókönyvekben használják.
      * Engedélyezett felhasználónként – Felhasználónként díjat felszámító vásárlási modell. Általában az alkalmazottak alkalmazásokhoz (pl. az Office 365-höz) való hozzáféréséhez használják. Akkor válassza ezt a beállítást, ha már van néhány Azure MFA-licenccel ellátott felhasználója.
   3. **Címtár** – Az Azure Active Directory-bérlő, amelyhez a többtényezős hitelesítésszolgáltató társítva van. Vegye figyelembe a következőket:
      * Nincs szüksége Azure AD-címtárra Multi-Factor Auth szolgáltató létrehozásához. Ha csak az Azure Multi-Factor Authentication-kiszolgálót vagy az SDK-t szeretne használni, hagyja üresen a mezőt.
      * A Multi-Factor Auth szolgáltatót Azure AD-címtárral kell társítani a speciális szolgáltatások kihasználása érdekében.
      * Az Azure AD Connect, az AAD Sync vagy a DirSync csak akkor követelmény, ha a helyszíni Active Directory-környezetet Azure AD-címtárral szinkronizálja.  Ha csak nem szinkronizált Azure AD-címtárat használ, akkor erre nincs szükség.
        
        ![MFA-szolgáltató létrehozása](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)

8. Ha a Létrehozás gombra kattint, létrejön a többtényezős hitelesítésszolgáltató, és megjelenik a következő üzenet: **Sikeresen létrejött a többtényezős hitelesítésszolgáltató**. Kattintson az **OK** gombra.
   
   ![MFA-szolgáltató létrehozása](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)  

