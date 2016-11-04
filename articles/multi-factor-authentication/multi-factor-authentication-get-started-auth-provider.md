---
title: Első lépések a Microsoft Azure Multi-Factor Auth szolgáltatóval
description: Megtudhatja, hogyan hozhat létre Azure Multi-Factor Auth szolgáltatót.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtand

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/04/2016
ms.author: kgremban

---
# Első lépések az Azure Multi-Factor Auth szolgáltatóval
A Multi-Factor Authentication alapértelmezés szerint elérhető az Azure Active Directory- és Office 365-felhasználókkal rendelkező globális adminisztrátorok számára. De ha ki szeretné használni a [speciális szolgáltatásokat](multi-factor-authentication-whats-next.md), az Azure MFA teljes verzióját meg kell vásárolnia.

> [!NOTE]
> Az Azure Multi-Factor Auth szolgáltatókkal kihasználhatja az Azure MFA teljes verziója által nyújtott szolgáltatásokat. Ez olyan felhasználóknak készült, akik **nem rendelkeznek Azure MFA, Azure AD Prémium vagy EMS licenccel**.  Az Azure MFA, az Azure AD Prémium és az EMS alapértelmezés szerint tartalmazza az Azure MFA teljes verzióját.  Ha rendelkezik licencekkel, akkor nincs szüksége Azure Multi-Factor Auth szolgáltatóra.
> 
> 

Az SDK letöltéséhez egy Azure Multi-Factor Auth szolgáltató szükséges.

> [!IMPORTANT]
> Ha le szeretné tölteni az SDK-t, akkor létre kell hoznia egy Azure Multi-Factor Auth szolgáltatót, akkor is, ha rendelkezik Azure MFA, AAD Premium vagy EMS licencekkel.  Ha e célból létrehoz egy Azure Multi-Factor Auth szolgáltatót, és már rendelkezik licencekkel, akkor a szolgáltatót az **Engedélyezett felhasználónként** modellel kell létrehoznia, és össze kell kötnie azzal a könyvtárral, amely az Azure MFA, Azure AD Premium vagy EMS licenceket tartalmazza.  Így biztosítható, hogy csak akkor számlázzunk Önnek, amikor több egyedi felhasználója használja az SDK-t, mint ahány licenccel rendelkezik.
> 
> 

A következő lépésekkel hozhat létre Azure Multi-Factor Auth szolgáltatót.

## Multi-Factor Auth szolgáltató létrehozása
- - -
1. Jelentkezzen be a **klasszikus Azure portálra** rendszergazdaként.
2. A bal oldalon válassza az **Active Directory** elemet.
3. Az Active Directory lap tetején válassza a **Multi-Factor Authentication szolgáltatók** elemet.
   ![MFA-szolgáltató létrehozása](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. Alul kattintson az **Új** elemre.
   ![MFA-szolgáltató létrehozása](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. Az **Alkalmazásszolgáltatások** területen válassza a **Multi-Factor Auth szolgáltató**
   ![MFA-szolgáltató létrehozása elemet](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. Kattintson a **Gyors létrehozás** gombra.
   ![MFA-szolgáltató létrehozása](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
7. Töltse ki az alábbi mezőket, és válassza a **Létrehozás** lehetőséget.
   1. **Név** – A Multi-Factor Auth szolgáltató neve.
   2. **Használati modell** – A Multi-Factor Authentication szolgáltató használati modellje.
      * Hitelesítésenként – Hitelesítésenként díjat felszámító vásárlási modell. Általában ügyfél felől elérhető alkalmazásokban Azure Multi-Factor Authentication hitelesítést használó forgatókönyvekben használják.
      * Engedélyezett felhasználónként – Felhasználónként díjat felszámító vásárlási modell. Általában az alkalmazottak alkalmazásokhoz (pl. az Office 365-höz) való hozzáféréséhez használják.
   3. **Címtár** – Az Azure Active Directory-bérlő, amelyhez a többtényezős hitelesítésszolgáltató társítva van. Vegye figyelembe a következőket:
      * Nincs szüksége Azure AD-címtárra Multi-Factor Auth szolgáltató létrehozásához.  Egyszerűen hagyja üresen a mezőt, ha csak az Azure Multi-Factor Authentication-kiszolgálót vagy az SDK-t szeretne használni.
      * A Multi-Factor Auth szolgáltatót Azure AD-címtárral kell társítani a speciális szolgáltatások kihasználása érdekében.
      * Az Azure AD Connect, az AAD Sync vagy a DirSync csak akkor követelmény, ha a helyszíni Active Directory-környezetet Azure AD-címtárral szinkronizálja.  Ha csak nem szinkronizált Azure AD-címtárat használ, akkor erre nincs szükség.
        ![MFA-szolgáltató létrehozása](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
8. Ha a Létrehozás gombra kattint, létrejön a többtényezős hitelesítésszolgáltató, és megjelenik a következő üzenet: **Sikeresen létrejött a többtényezős hitelesítésszolgáltató**. Kattintson az **OK** gombra.
   ![MFA-szolgáltató létrehozása](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)

<!--HONumber=Sep16_HO4-->


