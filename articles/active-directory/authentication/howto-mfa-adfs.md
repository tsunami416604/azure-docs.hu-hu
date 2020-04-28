---
title: Erőforrások biztonságossá tétele az Azure MFA és az ADFS-Azure Active Directory
description: Ez az Azure Multi-Factor Authentication-oldal leírja, hogyan kezdheti el az Azure MFA és az AD FS használatát a felhőben.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5949fdce9d591a9c02c5c6c4fb2f1dde94c45d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80654021"
---
# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>A felhőerőforrások védelme Azure Multi-Factor Authentication hitelesítéssel és AD FS-sel

Ha a szervezete Azure Active Directory-összevonást használ, és az Azure AD által elért erőforrásokkal rendelkezik, az Azure Multi-Factor Authentication segítségével vagy az Active Directory összevonási szolgáltatásokkal (AD FS) védheti meg ezeket az erőforrásokat. Az alábbi eljárásokkal védheti meg az Azure Active Directory-erőforrásokat az Azure Multi-Factor Authentication segítségével vagy az Active Directory összevonási szolgáltatásokkal.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Az Azure AD-erőforrások AD FS-sel való védelme

A felhőszolgáltatás biztosításához állítson be egy jogcímszabályt, hogy az Active Directory összevonási szolgáltatások a multipleauthn jogcímet adja ki, amikor egy felhasználó sikeresen végez kétlépéses ellenőrzést. Ez a jogcím átkerül az Azure AD-re. Az alábbi eljárás bemutatja ennek lépéseit:

1. Nyissa meg az AD FS felügyeleti konzolt.
2. A bal oldalon válassza a **függő entitás megbízhatóságai**elemet.
3. Kattintson a jobb gombbal a **Microsoft Office 365 Identity Platform** elemre, és válassza a **Jogcímszabályok szerkesztése** lehetőséget.

   ![ADFS-konzol – függő entitások Megbízhatóságai](./media/howto-mfa-adfs/trustedip1.png)

4. A kiadás átalakítási szabályai területen kattintson a **szabály hozzáadása**elemre.

   ![Kiadási átalakítási szabályok szerkesztése](./media/howto-mfa-adfs/trustedip2.png)

5. Az Átalakítási jogcímszabály hozzáadása varázslóban válassza a **Bejövő jogcím továbbítása vagy szűrése** elemet a legördülő menüből, majd kattintson a **Tovább** gombra.

   ![Átalakítási jogcímszabály hozzáadása varázsló](./media/howto-mfa-adfs/trustedip3.png)

6. Adjon nevet a szabálynak. 
7. Válassza a **Hitelesítési módszerek hivatkozásai** lehetőséget a Bejövő jogcím típusaként.
8. Válassza **Az összes jogcímérték továbbítása** lehetőséget.
    ![Átalakítási jogcímszabály hozzáadása varázsló](./media/howto-mfa-adfs/configurewizard.png)
9. Kattintson a **Befejezés** gombra. Zárja be az AD FS felügyeleti konzolt.

## <a name="trusted-ips-for-federated-users"></a>Megbízható IP-címek összevont felhasználók számára

Az adminisztrátorok a megbízható IP-címek segítségével megkerülhetik a kétlépéses ellenőrzést olyan IP-címek vagy összevont felhasználók esetében, akiknek a kérései a saját intranetes hálózatukról származnak. A következő szakaszok leírják az Azure Multi-Factor Authentication megbízható IP-címei és az összevont felhasználók konfigurálását, valamint a kétlépéses ellenőrzés megkerülését, amikor egy kérés összevont felhasználó intranetes hálózatáról származik. Ehhez úgy kell konfigurálni az AD FS-t, hogy áteresztést vagy a bejövő jogcímsablonok szűrését használja a vállalati hálózaton belüli jogcímtípushoz.

Ez a példa az Office 365-öt használja a függőentitás-megbízhatóságokhoz.

### <a name="configure-the-ad-fs-claims-rules"></a>Az AD FS-jogcímszabályok konfigurálása

Az első lépés az AD FS-jogcímek konfigurálása. Két jogcímszabályt hozzon létre: egyet a vállalati hálózaton belüli jogcímtípushoz és egy másikat ahhoz, hogy a felhasználók bejelentkezve maradjanak.

1. Nyissa meg az AD FS felügyeleti konzolt.
2. A bal oldalon válassza a **függő entitás megbízhatóságai**elemet.
3. Kattintson a jobb gombbal a **Microsoft Office 365 Identity platform** elemre, és válassza a **jogcímek szerkesztése... lehetőséget.** 
   ADFS-konzol – jogcím szabályainak ![szerkesztése](./media/howto-mfa-adfs/trustedip1.png)
4. A kiadás átalakítási szabályai területen kattintson a **szabály hozzáadása** elemre. 
    ![](./media/howto-mfa-adfs/trustedip2.png)
5. Az Átalakítási jogcímszabály hozzáadása varázslóban válassza a **Bejövő jogcím továbbítása vagy szűrése** elemet a legördülő menüből, majd kattintson a **Tovább** gombra.
   ![Átalakítási jogcímszabály hozzáadása varázsló](./media/howto-mfa-adfs/trustedip3.png)
6. A Jogcímszabály neve melletti mezőben adjon nevet a szabálynak. Például: InsideCorpNet.
7. A bejövő jogcím típusa melletti legördülő listából válassza a **vállalati hálózaton belül**lehetőséget.
   ![A vállalati hálózat jogcímen belüli Hozzáadás](./media/howto-mfa-adfs/trustedip4.png)
8. Kattintson a **Befejezés** gombra.
9. A kiadás átalakítási szabályai területen kattintson a **szabály hozzáadása**elemre.
10. Az Átalakítási jogcímszabály hozzáadása varázslóban válassza a **Jogcímek küldése egyéni szabállyal** elemet a legördülő menüből, és kattintson a **Tovább** gombra.
11. A jogcím szabály neve alatt található mezőben adja meg a *bejelentkezett felhasználók megtartása*értéket.
12. Az Egyéni szabály mezőbe írja be a következőt:

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Egyéni jogcím létrehozása a bejelentkezett felhasználók megőrzéséhez](./media/howto-mfa-adfs/trustedip5.png)
13. Kattintson a **Befejezés** gombra.
14. Kattintson az **Alkalmaz** gombra.
15. Kattintson az **OK** gombra.
16. Zárja be az AD FS felügyeleti konzolt.

### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>Azure Multi-Factor Authentication megbízható IP-címeinek konfigurálása összevont felhasználókkal

Most, hogy megvannak a jogcímek, konfigurálhatjuk a megbízható IP-címeket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **Azure Active Directory** > **biztonsági** > **feltételes hozzáférés** > **névvel ellátott helyet**.
3. A **feltételes hozzáférés – elnevezett helyek** panelen válassza az **MFA megbízható IP** -címek konfigurálása lehetőséget.

   ![Azure AD feltételes hozzáférés – nevesített helyek konfigurálása MFA megbízható IP-címek](./media/howto-mfa-adfs/trustedip6.png)

4. A Szolgáltatásbeállítások oldalon, a **megbízható IP**-címek területen válassza a **többtényezős hitelesítés kihagyása az összevont felhasználók számára az intraneten**lehetőséget.  
5. Kattintson a **Mentés**gombra.

Ennyi az egész! Ekkor az összevont Office 365-felhasználóknak csak az MFA-t kell használniuk, amikor egy jogcím a vállalati intraneten kívülről származik.
