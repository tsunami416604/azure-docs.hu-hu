---
title: Az Azure AD UserPrincipalName feltöltése
description: A következő dokumentum ismerteti, hogyan a UserPrincipalName attribútum fel van töltve.
author: billmath
ms.component: hybrid
ms.author: billmath
ms.date: 06/26/2018
ms.topic: article
ms.workload: identity
ms.service: active-Directory
manager: mtillman
ms.openlocfilehash: 59df0dc61be1f670f21b94fe24e56a2f040f950e
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426808"
---
# <a name="azure-ad-userprincipalname-population"></a>Az Azure AD UserPrincipalName feltöltése

Ez a cikk bemutatja, hogyan a UserPrincipalName attribútum feltöltése az Azure Active Directory (Azure AD).
A UserPrincipalName attribútum értéke a felhasználói fiókok Azure ad-ben felhasználónév.

## <a name="upn-terminology"></a>Egyszerű felhasználónév terminológiája
Ez a cikk a következő kifejezésekkel használja:

|Időtartam|Leírás|
|-----|-----|
|Kezdeti tartomány|Az alapértelmezett tartomány (onmicrosoft.com) az Azure AD-bérlőben. Ha például contoso.onmicrosoft.com.|
|A Microsoft Online e-mailek útválasztási címe (MOERA)|Az Azure AD számítja ki az Azure AD MailNickName attribútum és az Azure AD kezdeti tartományban MOERA &lt;MailNickName&gt;&#64;&lt;kezdeti tartomány&gt;.|
|A helyszíni mailNickName attribútum|Az attribútum az Active Directoryban, amelynek értéke az Exchange-szervezetben egy felhasználó aliast jelenti.|
|A helyszíni mail attribútum|Az attribútum az Active Directoryban, amelynek értéke jelöli a felhasználó e-mail-címe|
|Elsődleges SMTP-cím|A címzett az Exchange-objektum elsődleges e-mail-cím. Ha például SMTP:user\@contoso.com.|
|Alternatív bejelentkezési Azonosítóval|Egy helyszíni attribútuma eltérő UserPrincipalName, például a mail attribútum, a bejelentkezéshez használt.|

## <a name="what-is-userprincipalname"></a>Mit jelent a UserPrincipalName?
UserPrincipalName olyan attribútum, amely az internetes szabvány alapján a felhasználó internetes bejelentkezési neve, [RFC 822](https://www.ietf.org/rfc/rfc0822.txt). 

### <a name="upn-format"></a>Egyszerű felhasználónév formátuma
Egy egyszerű felhasználónév egy UPN-előtag (a felhasználói fiók nevét) és a egy egyszerű Felhasználónévi utótagot (DNS-tartománynevet) áll. Az előtag csatlakozik az utótag használata a "\@" szimbólumot. Például "valaki\@example.com". Egy egyszerű felhasználónév az összes biztonsági egyszerű objektumok directory erdőn belül egyedinek kell lennie. 

## <a name="upn-in-azure-ad"></a>Az Azure AD egyszerű felhasználónév 
Az UPN-jét használják az Azure AD, hogy a felhasználók bejelentkezhetnek.  Az egyszerű Felhasználónevet, amelyet a felhasználó használhat, függ-e a tartomány ellenőrzése után.  Ha a tartomány ellenőrzése után, majd az adott utótaggal rendelkező felhasználók jelentkezhetnek be az Azure AD-engedélyezni.  

Az attribútum az Azure AD Connect szinkronizálása.  A telepítés során a tartományok, amelyek igazoltan és az azokat, amelyeket nem is megtekintheti.

   ![Nem ellenőrzött tartományok](./media/plan-connect-userprincipalname/unverifieddomain.png) 

## <a name="alternate-login-id"></a>Alternatív bejelentkezési Azonosítóval
Bizonyos környezetekben a végfelhasználók előfordulhat, hogy csak akkor e-mail-címükkel, és nem az egyszerű felhasználónév.  E-mail-cím használatát a vállalati házirend, vagy egy helyszíni üzleti függőség okozhatja.

Másodlagos bejelentkezési Azonosítót ahol felhasználók is jelentkezzen be egy egyszerű, például a levelezés eltérő attribútum lehetővé teszi a bejelentkezési felület konfigurálása.

Ahhoz, hogy az Azure AD-vel a másodlagos bejelentkezési Azonosítót, nincs további konfigurációs lépésekre van szükség, ha az Azure AD Connect használatával. A másik azonosító a varázsló közvetlenül a konfigurálható. Tekintse meg az Azure AD bejelentkezés konfigurálása a Szinkronizáló szakasz alatt a felhasználók számára. Alatt a **egyszerű felhasználónév** legördülő menüben válassza ki az attribútum a másodlagos bejelentkezési azonosítóhoz.

![Nem ellenőrzött tartományok](./media/plan-connect-userprincipalname/altloginid.png)  

További információkért lásd: [konfigurálása alternatív bejelentkezési azonosító](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) és [az Azure AD bejelentkezés konfigurálása](how-to-connect-install-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Nem ellenőrzött UPN-utótagja
Ha az Azure AD-bérlő nem ellenőrzi a helyszíni UserPrincipalName attribútum/másodlagos bejelentkezési azonosító utótagot, majd az Azure AD UserPrincipalName attribútum értéke értéke MOERA. Az Azure AD számítja ki az Azure AD MailNickName attribútum és a kezdeti tartomány Azure ad-ben, a MOERA &lt;MailNickName&gt;&#64;&lt;kezdeti tartomány&gt;.

## <a name="verified-upn-suffix"></a>Ellenőrzött UPN-utótagja
Ha a helyszíni UserPrincipalName attribútum/másodlagos bejelentkezési azonosító utótag ellenőrzése az Azure AD-bérlőben, majd az Azure AD UserPrincipalName attribútum értéke lehet ugyanaz, mint a helyszíni UserPrincipalName attribútum/másodlagos bejelentkezési azonosító értékét fogja.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Az Azure AD MailNickName attribútum-érték kiszámításához
Az Azure AD UserPrincipalName attribútum értéke beállítható MOERA, mert fontos tudni, hogyan kerül kiszámításra a az Azure AD MailNickName attribútum értéke, amely a MOERA előtagot.

Felhasználói objektum szinkronizált Azure AD-bérlő első alkalommal, amikor az Azure AD a következő elemek a megadott sorrendben ellenőrzi, és beállítja a MailNickName attribútum értéke az első meglévőt:

- A helyszíni mailNickName attribútum
- Elsődleges SMTP-cím előtagja
- A helyszíni mail attribútum előtagja
- A helyszíni userPrincipalName attribútum/másodlagos bejelentkezési azonosító előtagja
- Másodlagos smtp-cím előtagja

Felhasználói objektum a frissítések szinkronizálása az Azure AD-bérlővel, az Azure AD frissíti a MailNickName attribútum értéke csak abban az esetben, a helyszíni mailNickName attribútum értéke egy frissítést.

>[!IMPORTANT]
>Az Azure AD UserPrincipalName attribútum értéke újraszámítja, csak abban az esetben a helyszíni UserPrincipalName attribútum/másodlagos bejelentkezési azonosító értéke egy frissítést a szinkronizált Azure AD-bérlőhöz. 
>
>Minden alkalommal, amikor az Azure AD UserPrincipalName attribútum újraszámítja, is újraszámítja a MOERA. 

## <a name="upn-scenarios"></a>Egyszerű felhasználónév forgatókönyvek
Az alábbiakban megtekinteni, hogyan az egyszerű felhasználónév alapján van kiszámítva az adott esethez.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>1. forgatókönyv: Nem ellenőrzött UPN-utótagot – kezdeti szinkronizálás

![Scenario1](./media/plan-connect-userprincipalname/example1.png)

A helyszíni felhasználói objektum:
- mailNickName: &lt;nincs beállítva&gt;
- proxyAddresses: {SMTP:us1@contoso.com}
- e-mail: us2@contoso.com
- userPrincipalName: us3@contoso.com"

A felhasználói objektumot az Azure AD-Bérlőben szinkronizált első alkalommal
- Az Azure AD MailNickName attribútum értéke elsődleges SMTP-cím előtagja.
- Állítsa MOERA &lt;MailNickName&gt;&#64;&lt;kezdeti tartomány&gt;.
- Az Azure AD UserPrincipalName attribútum értéke MOERA.

Az Azure AD-bérlő felhasználói objektum:
- MailNickName: us1           
- UserPrincipalName: us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>2. forgatókönyv: Nem ellenőrzött UPN-utótagot – beállítása a helyszíni mailNickName attribútum

![Scenario2](./media/plan-connect-userprincipalname/example2.png)

A helyszíni felhasználói objektum:
- mailNickName: us4
- proxyAddresses: {SMTP:us1@contoso.com}
- e-mail: us2@contoso.com
- UserPrincipalName: us3@contoso.com

Frissítés az Azure AD-bérlőhöz helyszíni mailNickName attribútum szinkronizálása
- Az Azure AD MailNickName attribútum frissítse helyszíni mailNickName attribútum.
- Mivel nem a helyszíni userPrincipalName attribútum kell frissíteni, nem történik változás az Azure AD UserPrincipalName attribútum.

Az Azure AD-bérlő felhasználói objektum:
- mailNickName: us4
- UserPrincipalName: us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>3. eset: Nem ellenőrzött UPN-utótagot – frissítés a helyszíni userPrincipalName attribútum

![Scenario3](./media/plan-connect-userprincipalname/example3.png)

A helyszíni felhasználói objektum:
- mailNickName: us4
- proxyAddresses: {SMTP:us1@contoso.com}
- e-mail: us2@contoso.com
- UserPrincipalName: us5@contoso.com

Frissítés az Azure AD-bérlőhöz a helyszíni userPrincipalName attribútum szinkronizálása
- A helyszíni userPrincipalName attribútum frissítése aktivál újraszámítás MOERA és az Azure AD UserPrincipalName attribútum.
- Állítsa MOERA &lt;MailNickName&gt;&#64;&lt;kezdeti tartomány&gt;.
- Az Azure AD UserPrincipalName attribútum értéke MOERA.

Az Azure AD-bérlő felhasználói objektum:
- mailNickName: us4
- UserPrincipalName: us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-primary-smtp-address-and-on-premises-mail-attribute"></a>4. forgatókönyv: A nem ellenőrzött UPN-utótagot – frissítés elsődleges SMTP-cím és a helyszíni mail attribútum

![Scenario4](./media/plan-connect-userprincipalname/example4.png)

A helyszíni felhasználói objektum:
- mailNickName: us4
- proxyAddresses: {SMTP:us6@contoso.com}
- e-mail: us7@contoso.com
- UserPrincipalName: us5@contoso.com

Szinkronizálja a helyszíni mail attribútum, és az Azure AD-bérlőhöz elsődleges SMTP-cím frissítése
- A kezdeti szinkronizálás, a felhasználói objektum frissítései után a helyszíni mail attribútum, és az elsődleges SMTP-cím nem lesz hatással, az Azure AD MailNickName vagy a UserPrincipalName attribútum.

Az Azure AD-bérlő felhasználói objektum:
- mailNickName: us4
- UserPrincipalName: us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>5. forgatókönyv: Ellenőrzött UPN-utótagot – frissítés a helyszíni userPrincipalName attribútum utótag

![Scenario5](./media/plan-connect-userprincipalname/example5.png)

A helyszíni felhasználói objektum:
- mailNickName: us4
- proxyAddresses: {SMTP:us6@contoso.com}
- e-mail: us7@contoso.com
- UserPrincipalName: us5@verified.contoso.com

Frissítés az Azure AD-bérlőhöz a helyszíni userPrincipalName attribútum szinkronizálása
- Frissítés a helyszíni userPrincipalName attribútum eseményindító újraszámítási Azure AD UserPrincipalName attribútum.
- Állítsa be az Azure AD UserPrincipalName attribútum a helyszíni userPrincipalName attribútum, mint az UPN-utótagot ellenőrzése az Azure AD-bérlőben.

Az Azure AD-bérlő felhasználói objektum:
- mailNickName: us4     
- UserPrincipalName: us5@verified.contoso.com

## <a name="next-steps"></a>További lépések
- [A helyszíni címtárak integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)
- [Az Azure AD Connect testreszabott telepítése](how-to-connect-install-custom.md)
