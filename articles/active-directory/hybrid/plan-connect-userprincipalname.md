---
title: Az Azure AD UserPrincipalName feltöltése
description: A következő dokumentum leírja, hogyan van feltöltve a UserPrincipalName attribútum.
author: billmath
ms.subservice: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: conceptual
ms.workload: identity
ms.service: active-directory
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: c198b329f07c5c7459f25165b2dc0a3bfa032276
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60382065"
---
# <a name="azure-ad-userprincipalname-population"></a>Az Azure AD UserPrincipalName feltöltése

Ez a cikk ismerteti, hogyan a UserPrincipalName attribútum van feltöltve az Azure Active Directoryban (Azure AD).
A UserPrincipalName attribútum értéke az Azure AD felhasználónév a felhasználói fiókok.

## <a name="upn-terminology"></a>UPN terminológia
Ebben a cikkben a következő terminológiát használjuk:

|Időtartam|Leírás|
|-----|-----|
|Kezdeti tartomány|Az alapértelmezett tartomány (onmicrosoft.com) az Azure AD-bérlőben. Például contoso.onmicrosoft.com.|
|Microsoft online e-mail útválasztási cím (MOERA)|Az Azure AD kiszámítja a MOERA-t az Azure AD &lt;MailNickName&gt; &lt;attribútumból&gt;és az Azure AD kezdeti tartományából MailNickName&#64;kezdeti tartományban.|
|Helyszíni mailNickName attribútum|Az Active Directory egyik attribútuma, amelynek értéke egy Exchange-szervezet felhasználójának aliasát jelöli.|
|Helyszíni levelezési attribútum|Az Active Directory ban található attribútum, amelynek értéke egy felhasználó e-mail címét jelöli|
|Elsődleges SMTP-cím|Az Exchange-címzett objektum elsődleges e-mail címe. Például az SMTP:user\@contoso.com.|
|Alternatív bejelentkezési azonosító|A felhasználófőnévtől eltérő helyszíni attribútum, például mail attribútum, amely et a bejelentkezéshez használják.|

## <a name="what-is-userprincipalname"></a>Mi az a UserPrincipalName?
UserPrincipalName egy olyan attribútum, amely egy internet-stílusú bejelentkezési nevet a felhasználó alapuló internetes szabvány [RFC 822](https://www.ietf.org/rfc/rfc0822.txt). 

### <a name="upn-format"></a>UPN formátum
Az upn egy UPN-előtagból (a felhasználói fiók nevéből) és egy UPN-utótagból (DNS-tartománynévből) áll. Az előtag a " "\@szimbólummal csatlakozik az utótaghoz. Például a\@"valaki example.com". Az egyszerű felhasználónévnek egyedinek kell lennie a címtárerdő összes rendszerbiztonsági tagobjektuma között. 

## <a name="upn-in-azure-ad"></a>Upn az Azure AD-ben 
Az upn az Azure AD segítségével lehetővé teszi a felhasználók számára, hogy jelentkezzen be.  A felhasználó által használható összetett felhasználónév attól függ, hogy a tartomány t ellenőrizték-e.  Ha a tartomány ellenőrizték, majd az ilyen utótagot használó felhasználó bejelentkezhet az Azure AD-be.  

Az attribútumot az Azure AD Connect szinkronizálja.  A telepítés során megtekintheti az ellenőrzött tartományokat, és azokat, amelyek nem.

   ![Nem ellenőrzött tartományok](./media/plan-connect-userprincipalname/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Alternatív bejelentkezési azonosító
Bizonyos környezetekben előfordulhat, hogy a végfelhasználók csak az e-mail címükkel tudnak, az online felhasználónokkal nem.  Az e-mail cím használata lehet a vállalati házirend vagy a helyszíni üzletági alkalmazás függősége.

Az alternatív bejelentkezési azonosító lehetővé teszi egy bejelentkezési élmény konfigurálását, ahol a felhasználók nem az online felhasználónjuk, például a levelezési attribútumukkal jelentkezhetnek be.

Az Azure AD-vel való alternatív bejelentkezési azonosító engedélyezéséhez nincs szükség további konfigurációs lépésekre az Azure AD Connect használatakor. Az alternatív azonosító közvetlenül a varázslóból konfigurálható. Tekintse meg az Azure AD bejelentkezési konfigurációa a felhasználók a Szinkronizálás szakaszban. Az **Egyszerű felhasználónév** legördülő menüben válassza ki az Alternatív bejelentkezési azonosító attribútumát.

![Nem ellenőrzött tartományok](./media/plan-connect-userprincipalname/altloginid.png)  

További információ: [Alternatív bejelentkezési azonosító konfigurálása](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) és [az Azure AD bejelentkezési konfigurációja](how-to-connect-install-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Nem ellenőrzött UPN-utótag
Ha a helyszíni UserPrincipalName attribútum/alternatív bejelentkezési azonosító utótag nincs ellenőrizve az Azure AD tenant, majd az Azure AD UserPrincipalName attribútum értéke MOERA. Az Azure AD kiszámítja a MOERA az Azure AD MailNickName &lt;attribútum&gt; és &lt;az&gt;Azure AD kezdeti tartomány MailNickName&#64;kezdeti tartomány.

## <a name="verified-upn-suffix"></a>Ellenőrzött UPN-utótag
Ha a helyszíni UserPrincipalName attribútum/alternatív bejelentkezési azonosító utótag ellenőrzése az Azure AD-bérlő, majd az Azure AD UserPrincipalName attribútum értéke meg fog egyeznie a helyszíni UserPrincipalName attribútum/alternatív bejelentkezési azonosító értéke.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Az Azure AD MailNickName attribútumérték-számítása
Mivel az Azure AD UserPrincipalName attribútum értéke lehet állítani MOERA, fontos megérteni, hogyan az Azure AD MailNickName attribútum értéke, amely a MOERA előtag, a moera-előtag, kiszámítása.

Amikor egy felhasználói objektum ot szinkronizál egy Azure AD-bérlővel, az Azure AD ellenőrzi a következő elemeket a megadott sorrendben, és beállítja a MailNickName attribútum értékét az első létezőre:

- Helyszíni mailNickName attribútum
- Elsődleges SMTP-cím előtagja
- Helyszíni levelezési attribútum előtagja
- Helyszíni felhasználó elsődleges felhasználóPrincipalName attribútum/alternatív bejelentkezési azonosító
- Másodlagos smtp-cím előtagja

Amikor egy felhasználói objektum frissítései szinkronizálva vannak az Azure AD-bérlővel, az Azure AD csak abban az esetben frissíti a MailNickName attribútum értékét, ha a helyszíni mailNickName attribútumértéke frissül.

>[!IMPORTANT]
>Az Azure AD újraszámítja a UserPrincipalName attribútum értékét csak abban az esetben, ha a helyszíni UserPrincipalName attribútum/alternatív bejelentkezési azonosító érték frissítése szinkronizálódik az Azure AD-bérlővel. 
>
>Amikor az Azure AD újraszámítja a UserPrincipalName attribútumot, újraszámítja a MOERA-t is. 

## <a name="upn-scenarios"></a>Upn forgatókönyvek
Az alábbi példák bemutatják, hogyan számítja ki az upn számítását az adott forgatókönyv alapján.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>1. forgatókönyv: Nem ellenőrzött UPN-utótag – kezdeti szinkronizálás

![1. forgatókönyv](./media/plan-connect-userprincipalname/example1.png)

Helyszíni felhasználói objektum:
- mailNickName &lt;: nincs beállítva&gt;
- proxyAddresses :SMTP:us1@contoso.com{ }
- Mail:us2@contoso.com
- userPrincipalName us3@contoso.com: '

Első alkalommal szinkronizálta a felhasználói objektumot az Azure AD-bérlővel
- Állítsa az Azure AD MailNickName attribútumot az elsődleges SMTP-címelőtagra.
- Állítsa a MOERA-t &lt;MailNickName&gt;&#64;&lt;kezdeti tartományra.&gt;
- Állítsa az Azure AD UserPrincipalName attribútumot MOERA-ra.

Azure AD-bérlő felhasználói objektuma:
- MailNickName : us1           
- UserPrincipalName :us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>2. forgatókönyv: Nem ellenőrzött UPN-utótag – helyszíni mailNickName attribútum

![2. forgatókönyv](./media/plan-connect-userprincipalname/example2.png)

Helyszíni felhasználói objektum:
- mailNickName : us4
- proxyAddresses :SMTP:us1@contoso.com{ }
- Mail:us2@contoso.com
- userPrincipalName :us3@contoso.com

Frissítés szinkronizálása a helyszíni mailNickName attribútumban az Azure AD tenant szolgáltatással
- Frissítse az Azure AD MailNickName attribútumot helyszíni mailNickName attribútummal.
- Mivel nincs frissítés a helyszíni userPrincipalName attribútumhoz, az Azure AD UserPrincipalName attribútum nem módosul.

Azure AD-bérlő felhasználói objektuma:
- MailNickName : us4
- UserPrincipalName :us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>3. forgatókönyv: Nem ellenőrzött UPN-utótag – frissítés helyszíni userPrincipalName attribútum

![3. forgatókönyv](./media/plan-connect-userprincipalname/example3.png)

Helyszíni felhasználói objektum:
- mailNickName : us4
- proxyAddresses :SMTP:us1@contoso.com{ }
- Mail:us2@contoso.com
- userPrincipalName :us5@contoso.com

Frissítés szinkronizálása a helyszíni userPrincipalName attribútumon az Azure AD tenant szolgáltatással
- Frissítés a helyszíni userPrincipalName attribútum eseményindítók újraszámítása MOERA és az Azure AD UserPrincipalName attribútum.
- Állítsa a MOERA-t &lt;MailNickName&gt;&#64;&lt;kezdeti tartományra.&gt;
- Állítsa az Azure AD UserPrincipalName attribútumot MOERA-ra.

Azure AD-bérlő felhasználói objektuma:
- MailNickName : us4
- UserPrincipalName :us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>4. forgatókönyv: Nem ellenőrzött UPN-utótag – az elsődleges SMTP-cím és a helyszíni levelezési attribútum frissítése

![4. forgatókönyv](./media/plan-connect-userprincipalname/example4.png)

Helyszíni felhasználói objektum:
- mailNickName : us4
- proxyAddresses :SMTP:us6@contoso.com{ }
- Mail:us7@contoso.com
- userPrincipalName :us5@contoso.com

Frissítés szinkronizálása a helyszíni levelezési attribútumon és az elsődleges SMTP-cím en az Azure AD-bérlővel
- A felhasználói objektum kezdeti szinkronizálása után a helyszíni mail attribútum és az elsődleges SMTP-cím frissítései nem befolyásolják az Azure AD MailNickName vagy a UserPrincipalName attribútumot.

Azure AD-bérlő felhasználói objektuma:
- MailNickName : us4
- UserPrincipalName :us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>5. forgatókönyv: Ellenőrzött UPN-utótag – frissítés a helyszíni userPrincipalName attribútumutótagról

![5. forgatókönyv](./media/plan-connect-userprincipalname/example5.png)

Helyszíni felhasználói objektum:
- mailNickName : us4
- proxyAddresses :SMTP:us6@contoso.com{ }
- Mail:us7@contoso.com
- userPrincipalName :us5@verified.contoso.com

Frissítés szinkronizálása a helyszíni userPrincipalName attribútumon az Azure AD-bérlővel
- Frissítés a helyszíni userPrincipalName attribútum elindítja az Azure AD UserPrincipalName attribútum újraszámítása.
- Állítsa be az Azure AD UserPrincipalName attribútumot a helyszíni userPrincipalName attribútumra, mivel az UPN-utótagot az Azure AD tenant ellenőrzi.

Azure AD-bérlő felhasználói objektuma:
- MailNickName : us4     
- UserPrincipalName :us5@verified.contoso.com

## <a name="next-steps"></a>Következő lépések
- [A helyszíni címtárak integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
- [Az Azure AD Connect testreszabott telepítése](how-to-connect-install-custom.md)
