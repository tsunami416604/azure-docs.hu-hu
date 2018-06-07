---
title: Az Azure AD UserPrincipalName feltöltése
description: A következő dokumentum ismerteti, hogyan a UserPrincipalName attribútum fel van töltve.
author: billmath
ms.component: hybrid
ms.author: billmath
ms.date: 02/02/2018
ms.topic: article
ms.workload: identity
ms.service: active-Directory
manager: mtillman
ms.openlocfilehash: 73238b1f79e639f832499eed15ac1e4499eb6e84
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593397"
---
# <a name="azure-ad-userprincipalname-population"></a>Az Azure AD UserPrincipalName feltöltése

Ez a cikk ismerteti, hogyan fel van töltve a UserPrincipalName attribútum az Azure Active Directory (Azure AD).
A UserPrincipalName attribútum értéke a felhasználói fiókok Azure AD felhasználónév.

## <a name="upn-terminology"></a>UPN-terminológia
Ez a cikk a következő kifejezésekkel használja:

|Időtartam|Leírás|
|-----|-----|
|Kezdeti tartomány|Az alapértelmezett tartomány (onmicrosoft.com) az Azure AD-bérlő. Például a contoso.onmicrosoft.com.|
|Microsoft Online E-mail útválasztási címet (MOERA)|Az Azure AD kiszámítja az Azure AD MailNickName attribútum és az Azure AD kezdeti tartományban MOERA &lt;MailNickName&gt;&#64;&lt;kezdeti tartomány&gt;.|
|A helyszíni mailNickName attribútum|Az attribútum az Active Directoryban, amelynek értéke egy olyan felhasználó, az Exchange-szervezetben aliast jelenti.|
|A helyszíni levél attribútum|Az attribútum az Active Directoryban, amelynek értéke jelöli egy felhasználó e-mail címe|
|Elsődleges SMTP-cím|Az Exchange címzett objektum elsődleges e-mail címét. Például: SMTP:user@contoso.com.|
|Másodlagos bejelentkezési Azonosítóval|A helyszíni attribútum eltérő UserPrincipalName, például a mail attribútum használt a bejelentkezéshez.|

## <a name="what-is-userprincipalname"></a>Mi az a UserPrincipalName?
UserPrincipalName olyan attribútum, amely egy internetes bejelentkezési név az internetes szabvány alapján felhasználó [RFC 822](http://www.ietf.org/rfc/rfc0822.txt). 

### <a name="upn-format"></a>Egyszerű felhasználónév formátuma
Egy egyszerű felhasználónév UPN előtag (felhasználói fiók nevét) és egy egyszerű Felhasználónévi utótagot (egy DNS-tartománynév) áll. Az előtag a utótag használatával kapcsolódik a "@" szimbólummal. Például: „someone@example.com”. Egy egyszerű felhasználónév összes rendszerbiztonsági tagnak directory erdőn belül egyedinek kell lennie. 

## <a name="upn-in-azure-ad"></a>Az Azure AD egyszerű felhasználónév 
Az egyszerű Felhasználónevet a felhasználó bejelentkezhet az Azure AD használják.  A felhasználónév, amellyel a felhasználó, függ-e a tartomány ellenőrzése után.  Ha a tartomány ellenőrzése után, akkor a felhasználó adott utótaggal rendelkező rendszer engedélyezi-e bejelentkezés az Azure AD.  

Az attribútum az Azure AD Connect szinkronizálása.  Telepítés közben tekintheti meg a tartomány ellenőrzése után, és nem rendelkeznek.

   ![Nem ellenőrzött tartományok](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Másodlagos bejelentkezési Azonosítóval
Bizonyos környezetekben, vállalati házirend, vagy a helyszíni üzleti alkalmazásfüggőség, mert a végfelhasználók csak akkor e-mail címüket és nem az egyszerű Felhasználónevük tudomást.

Másodlagos bejelentkezési Azonosítóval is lehetővé teszi a bejelentkezés során tapasztal élmény konfigurálását ahol felhasználók bejelentkezhetnek csak az egyszerű Felhasználónevük, például a mail attribútum.

Ahhoz, hogy az Azure ad-vel a másodlagos bejelentkezési Azonosítóval, nincs további konfigurációs lépésekre van szükség az Azure AD Connect használata esetén. A másik azonosító közvetlenül a a varázsló segítségével konfigurálhatók. Tekintse meg a felhasználók szakaszban szinkronizálása az Azure AD-bejelentkezés konfigurálása. Az a **egyszerű felhasználónév** legördülő listából válassza ki az attribútum a másodlagos bejelentkezési azonosítóval.

![Nem ellenőrzött tartományok](./media/active-directory-aadconnect-userprincipalname/altloginid.png)  

További információ: [konfigurálása másodlagos bejelentkezési azonosító](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) és [az Azure AD-bejelentkezés konfigurálása](active-directory-aadconnect-get-started-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Nem ellenőrzött UPN-utótag
Ha az Azure AD-bérlő nem ellenőrzi a helyszíni UserPrincipalName attribútum/másodlagos bejelentkezési azonosító utótagot, majd az Azure AD UserPrincipalName attribútum értéke értéke MOERA. Az Azure AD kiszámítja az Azure AD MailNickName attribútum és az Azure AD kezdeti tartományban MOERA &lt;MailNickName&gt;&#64;&lt;kezdeti tartomány&gt;.

## <a name="verified-upn-suffix"></a>Ellenőrzött UPN-utótag
Ha a helyszíni UserPrincipalName attribútum/másodlagos bejelentkezési azonosító utótag ellenőrzi az Azure AD-bérlő, majd az Azure AD UserPrincipalName attribútum értéke lehet ugyanaz, mint a helyszíni UserPrincipalName attribútum/másodlagos bejelentkezési azonosító értéke lesz.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Az Azure AD MailNickName attribútum értékének kiszámítása
Az Azure AD UserPrincipalName attribútum értéke beállítható MOERA, mert fontos megérteni, hogyan számítható ki az Azure AD MailNickName attribútum értéke, amely a MOERA előtag.

Amikor első alkalommal olyan felhasználói objektum szinkronizálja az Azure AD-bérlő számára, az Azure AD a következő, a megadott sorrendben ellenőrzi, és az első meglévőt MailNickName attribútum értéket állítja be:

- A helyszíni mailNickName attribútum
- Elsődleges SMTP-cím előtagja
- A helyszíni levél attribútum előtagja
- A helyszíni userPrincipalName attribútum/másodlagos bejelentkezési azonosító előtagja
- Másodlagos SMTP-cím előtagja

Ha olyan felhasználói objektum a frissítéseket az Azure AD-bérlő történő szinkronizálása befejeződik, az Azure AD frissíti a MailNickName attribútum értéke csak abban az esetben, ha van egy frissítést adunk ki a helyszíni mailNickName attribútum értéke.

>[!IMPORTANT]
>Az Azure AD újraszámítja a UserPrincipalName attribútum értéke csak abban az esetben egy frissítést adunk ki a helyszíni UserPrincipalName attribútum/másodlagos bejelentkezési azonosító értéke szinkronizált az Azure AD-bérlő. 
>
>Az Azure AD újraszámítja a UserPrincipalName attribútum, amikor is a MOERA számítja újra. 

## <a name="upn-scenarios"></a>Egyszerű felhasználónév forgatókönyvek
Az alábbiakban példát arra vonatkozóan, hogyan az egyszerű felhasználónév értéke alapján számítja ki az adott forgatókönyv esetén.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>1. eset: Nem ellenőrzött UPN-utótagot – kezdeti szinkronizálás

A helyszíni felhasználói objektum:
- mailNickName: &lt;nincs beállítva&gt;
- proxyAddresses: {SMTP:us1@contoso.com}
- e-mail: us2@contoso.com
- userPrincipalName: us3@contoso.com"

Szinkronizálja az Azure AD-bérlő felhasználói objektum első alkalommal
- Elsődleges SMTP-cím előtagján Azure AD MailNickName attribútum értéke.
- MOERA beállítása &lt;MailNickName&gt;&#64;&lt;kezdeti tartomány&gt;.
- Azure AD UserPrincipalName attribútum értéke MOERA.

Az Azure AD-bérlő felhasználói objektum:
- MailNickName: us1           
- userPrincipalName: us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>2. eset: Nem ellenőrzött UPN-utótagot – beállítása a helyszíni mailNickName attribútum

A helyszíni felhasználói objektum:
- MailNickName: us4
- proxyAddresses: {SMTP:us1@contoso.com}
- e-mail: us2@contoso.com
- userPrincipalName: us3@contoso.com

Frissítés Azure AD-bérlő helyszíni mailNickName attribútum szinkronizálása
- Frissítés Azure AD MailNickName attribútum helyszíni mailNickName attribútummal.
- Nincs a helyszíni userPrincipalName attribútum a frissítés, mert nincs az Azure AD UserPrincipalName attribútum nem változik.

Az Azure AD-bérlő felhasználói objektum:
- MailNickName: us4
- userPrincipalName: us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>3. eset: Nem ellenőrzött UPN-utótagot – frissítés a helyszíni userPrincipalName attribútum

A helyszíni felhasználói objektum:
- MailNickName: us4
- proxyAddresses: {SMTP:us1@contoso.com}
- e-mail: us2@contoso.com
- userPrincipalName: us5@contoso.com

Frissítés a userPrincipalName attribútum a helyszíni Azure AD-bérlő szinkronizálni
- A userPrincipalName attribútum a helyszíni frissítés MOERA és az Azure AD UserPrincipalName attribútum újraszámítása váltja ki.
- MOERA beállítása &lt;MailNickName&gt;&#64;&lt;kezdeti tartomány&gt;.
- Azure AD UserPrincipalName attribútum értéke MOERA.

Az Azure AD-bérlő felhasználói objektum:
- MailNickName: us4
- userPrincipalName: us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>4. forgatókönyv: A nem ellenőrzött UPN-utótagot – frissítés elsődleges SMTP-cím és a helyszíni mail attribútum

A helyszíni felhasználói objektum:
- MailNickName: us4
- proxyAddresses: {SMTP:us6@contoso.com}
- e-mail: us7@contoso.com
- userPrincipalName: us5@contoso.com

Szinkronizálás a helyszíni levél attribútum és az Azure AD-bérlő elsődleges SMTP-címének frissítése
- Miután frissíti a felhasználói objektum, a kezdeti szinkronizálás a helyszíni mail attribútum, és elsődleges SMTP-cím nem lesz hatással az Azure AD MailNickName sem a UserPrincipalName attribútum.

Az Azure AD-bérlő felhasználói objektum:
- MailNickName: us4
- userPrincipalName: us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>5. forgatókönyv: Ellenőrzött UPN-utótagot – frissítés a helyszíni userPrincipalName attribútum utótag

A helyszíni felhasználói objektum:
- MailNickName: us4
- proxyAddresses: {SMTP:us6@contoso.com}
- e-mail: us7@contoso.com
- serPrincipalName: us5@verified.contoso.com

Szinkronizálja a helyszíni userPrincipalName attribútum a Azure AD-bérlő frissítése
- Frissítés Azure AD UserPrincipalName attribútum a helyszíni userPrincipalName attribútum eseményindítók újraszámítás.
- Azure AD UserPrincipalName attribútum a helyszíni userPrincipalName attribútum beállítani az Azure AD-bérlő ellenőrzi az egyszerű Felhasználónévi utótagot.

Az Azure AD-bérlő felhasználói objektum:
- MailNickName: us4     
- userPrincipalName: us5@verified.contoso.com

## <a name="next-steps"></a>További lépések
- [A helyszíni címtárak integrálása az Azure Active Directoryval](active-directory-aadconnect.md)
- [Az Azure AD Connect testreszabott telepítése](active-directory-aadconnect-get-started-custom.md)
