---
title: "A felhőerőforrások védelme az Azure MFA és AD FS szolgáltatással | Microsoft Docs"
description: "Ez az Azure Multi-Factor Authentication-oldal leírja, hogyan kezdheti el az Azure MFA és az AD FS használatát a felhőben."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.assetid: 0927fc67-8090-4fdd-913a-b3cfed3fbe77
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/29/2017
ms.author: joflore
ms.openlocfilehash: 99052718d268135e1b9c51d019513aa59afede84
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="securing-cloud-resources-with-azure-multi-factor-authentication-and-ad-fs"></a>A felhőerőforrások védelme Azure Multi-Factor Authentication hitelesítéssel és AD FS-sel
Ha a szervezete Azure Active Directory-összevonást használ, és az Azure AD által elért erőforrásokkal rendelkezik, az Azure Multi-Factor Authentication segítségével vagy az Active Directory összevonási szolgáltatásokkal (AD FS) védheti meg ezeket az erőforrásokat. Az alábbi eljárásokkal védheti meg az Azure Active Directory-erőforrásokat az Azure Multi-Factor Authentication segítségével vagy az Active Directory összevonási szolgáltatásokkal.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Az Azure AD-erőforrások AD FS-sel való védelme
A felhőszolgáltatás biztosításához állítson be egy jogcímszabályt, hogy az Active Directory összevonási szolgáltatások a multipleauthn jogcímet adja ki, amikor egy felhasználó sikeresen végez kétlépéses ellenőrzést. Ez a jogcím átkerül az Azure AD-re. Az alábbi eljárás bemutatja ennek lépéseit:


1. Nyissa meg az AD FS felügyeleti konzolt.
2. A bal oldalon válassza a **Függő entitás megbízhatóságai** elemet.
3. Kattintson a jobb gombbal a **Microsoft Office 365 Identity Platform** elemre, és válassza a **Jogcímszabályok szerkesztése** lehetőséget.

   ![Felhő](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)

4. A Kiadás átalakítási szabályai részben kattintson a **Szabály hozzáadása** elemre.

   ![Felhő](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)

5. Az Átalakítási jogcímszabály hozzáadása varázslóban válassza a **Bejövő jogcím továbbítása vagy szűrése** elemet a legördülő menüből, majd kattintson a **Tovább** gombra.

   ![Felhő](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)

6. Adjon nevet a szabálynak. 
7. Válassza a **Hitelesítési módszerek hivatkozásai** lehetőséget a Bejövő jogcím típusaként.
8. Válassza **Az összes jogcímérték továbbítása** lehetőséget.
    ![Átalakítási jogcímszabály hozzáadása varázsló](./media/multi-factor-authentication-get-started-adfs-cloud/configurewizard.png)
9. Kattintson a **Befejezés** gombra. Zárja be az AD FS felügyeleti konzolt.

## <a name="trusted-ips-for-federated-users"></a>Megbízható IP-címek összevont felhasználók számára
Az adminisztrátorok a megbízható IP-címek segítségével megkerülhetik a kétlépéses ellenőrzést olyan IP-címek vagy összevont felhasználók esetében, akiknek a kérései a saját intranetes hálózatukról származnak. A következő szakaszok leírják az Azure Multi-Factor Authentication megbízható IP-címei és az összevont felhasználók konfigurálását, valamint a kétlépéses ellenőrzés megkerülését, amikor egy kérés összevont felhasználó intranetes hálózatáról származik. Ehhez úgy kell konfigurálni az AD FS-t, hogy áteresztést vagy a bejövő jogcímsablonok szűrését használja a vállalati hálózaton belüli jogcímtípushoz.

Ez a példa az Office 365-öt használja a függőentitás-megbízhatóságokhoz.

### <a name="configure-the-ad-fs-claims-rules"></a>Az AD FS-jogcímszabályok konfigurálása
Az első lépés az AD FS-jogcímek konfigurálása. Két jogcímszabályt hozzon létre: egyet a vállalati hálózaton belüli jogcímtípushoz és egy másikat ahhoz, hogy a felhasználók bejelentkezve maradjanak.

1. Nyissa meg az AD FS felügyeleti konzolt.
2. A bal oldalon válassza a **Függő entitás megbízhatóságai** elemet.
3. Középen kattintson a jobb gombbal a **Microsoft Office 365 Identity Platform** elemre, és válassza a **Jogcímszabályok szerkesztése…** lehetőséget.
   ![Felhő](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. A Kiadás átalakítási szabályai részben kattintson a **Szabály hozzáadása** lehetőségre.
   ![Felhő](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. Az Átalakítási jogcímszabály hozzáadása varázslóban válassza a **Bejövő jogcím továbbítása vagy szűrése** elemet a legördülő menüből, majd kattintson a **Tovább** gombra.
   ![Felhő](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. A Jogcímszabály neve melletti mezőben adjon nevet a szabálynak. Például: InsideCorpNet.
7. A Bejövő jogcím típusa melletti legördülő listából válassza a **Vállalati hálózaton belül** elemet.
   ![Felhő](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. Kattintson a **Finish** (Befejezés) gombra.
9. A Kiadás átalakítási szabályai részben kattintson a **Szabály hozzáadása** elemre.
10. Az Átalakítási jogcímszabály hozzáadása varázslóban válassza a **Jogcímek küldése egyéni szabállyal** elemet a legördülő menüből, és kattintson a **Tovább** gombra.
11. A Jogcímszabály neve: alatti mezőbe írja be a következőt: *Keep Users Signed In* (A felhasználók maradjanak bejelentkezve).
12. Az Egyéni szabály mezőbe írja be a következőt:

        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Felhő](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. Kattintson a **Befejezés** gombra.
14. Kattintson az **Alkalmaz** gombra.
15. Kattintson az **OK** gombra.
16. Zárja be az AD FS felügyeleti konzolt.

### <a name="configure-azure-multi-factor-authentication-trusted-ips-with-federated-users"></a>Azure Multi-Factor Authentication megbízható IP-címeinek konfigurálása összevont felhasználókkal
Most, hogy megvannak a jogcímek, konfigurálhatjuk a megbízható IP-címeket.

1. Jelentkezzen be a [klasszikus Azure portálra](https://manage.windowsazure.com).
2. A bal oldalon kattintson az **Active Directory** elemre.
3. A Címtár területen válassza ki a címtárat, ahová a megbízható IP-címeket be szeretné állítani.
4. A kiválasztott címtár esetében kattintson a **Konfigurálás** elemre.
5. A Multi-Factor Authentication szakaszban kattintson a **Szolgáltatásbeállítások kezelése** elemre.
6. A Szolgáltatásbeállítások oldalon, a Megbízható IP-címek területen jelölje be a **Többtényezős hitelesítés kihagyása az összevont felhasználók intranetről indított kérelmei esetén** elemet.  

   ![Felhő](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
   
7. Kattintson a **mentés** gombra.
8. A frissítések alkalmazása után kattintson a **bezárás** gombra.

Készen is van. Ekkor az összevont Office 365-felhasználóknak csak az MFA-t kell használniuk, amikor egy jogcím a vállalati intraneten kívülről származik.
