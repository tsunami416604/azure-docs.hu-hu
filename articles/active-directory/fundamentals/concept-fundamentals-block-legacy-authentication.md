---
title: Örökölt hitelesítési protokollok blokkolása az Azure AD-ben
description: Megtudhatja, hogy a szervezetek hogyan és miért blokkolhatják az örökölt hitelesítési protokollokat
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63271567e70955f6dfb0b10a5c882b6dce9545ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74932488"
---
# <a name="blocking-legacy-authentication"></a>Örökölt hitelesítés blokkolása
 
Annak érdekében, hogy a felhasználók könnyen hozzáférhessenek a felhőalapú alkalmazásokhoz, az Azure Active Directory (Azure AD) a hitelesítési protokollok széles skáláját támogatja, beleértve az örökölt hitelesítést is. Az örökölt hitelesítés olyan kifejezés, amely a következő hitelesítési kérelemre hivatkozik:

- Régebbi Office-ügyfelek, amelyek nem használnak modern hitelesítést (például Office 2010-ügyfél)
- Bármely ügyfél, amely örökölt leveli protokollokat, például IMAP/SMTP/POP3 protokollt használ

Ma az összes kompromittáló bejelentkezési kísérlet többsége örökölt hitelesítésből származik. Az örökölt hitelesítés nem támogatja a többtényezős hitelesítést (MFA). Még akkor is, ha egy MFA-házirend engedélyezve van a címtárban, egy rossz aktor hitelesítheti magát egy örökölt protokoll használatával, és megkerülheti az MFA-t. A fiók régebbi protokollok által imitált hitelesítési kérelmekkel szembeni védelmének legjobb módja, ha teljesen letiltja ezeket a kísérleteket.

## <a name="identify-legacy-authentication-use"></a>Örökölt hitelesítési használat azonosítása

Mielőtt letiltaná az örökölt hitelesítést a címtárban, először meg kell értenie, hogy a felhasználók rendelkeznek-e olyan alkalmazásokkal, amelyek örökölt hitelesítést használnak, és hogy ez hogyan befolyásolja a teljes könyvtárat. Az Azure AD bejelentkezési naplók segítségével megismerheti, ha örökölt hitelesítést használ.

1. Keresse meg az Azure Portalon > Azure Active Directory > bejelentkezések.
1. Adja hozzá az Ügyfélalkalmazás oszlopot, ha az nem jelenik meg, ha az oszlopok > ügyfélalkalmazásra kattint.
1. Szűrés ügyfélalkalmazás szerint, > ellenőrizze az összes egyéb ügyfél beállítást, és kattintson az Alkalmaz gombra.
1. Szűrj állapot szerint > sikeres, és kattintson az Alkalmaz gombra. 
1. Szükség esetén bővítse a dátumtartományt a Dátum szűrő vel.

A szűrés csak a kiválasztott örökölt hitelesítési protokollok által végrehajtott sikeres bejelentkezési kísérleteket jeleníti meg. Az egyes bejelentkezési próbálkozásokra kattintva további részleteket jeleníthet meg. Az Ügyfélalkalmazás oszlop vagy az Alapszintű adatok lap Ügyfélalkalmazás mezője, miután kiválasztotta az egyes adatsorokat, jelzi, hogy melyik örökölt hitelesítési protokollt használta. Ezek a naplók jelzik, hogy mely felhasználók továbbra is az örökölt hitelesítéstől függően vannak, és mely alkalmazások használnak örökölt protokollokat a hitelesítési kérelmek hez. Azon felhasználók számára, akik nem jelennek meg ezekben a naplókban, és megerősítést nyer, hogy nem használnak örökölt hitelesítést, valósítson meg egy feltételes hozzáférési házirendet, vagy engedélyezze az Alapkonfiguráció házirendet: csak ezeknek a felhasználóknak tiltsa le az örökölt hitelesítést.

## <a name="moving-away-from-legacy-authentication"></a>Elmozdulás az örökölt hitelesítéstől 

Ha már jobb anamtalán már rendelkezik azzal, hogy ki használ örökölt hitelesítést a címtárban, és mely alkalmazások függenek tőle, a következő lépés a felhasználók modern hitelesítésre való frissítése. A modern hitelesítés az identitáskezelés olyan módja, amely biztonságosabb felhasználói hitelesítést és engedélyezést kínál. Ha van egy MFA-házirend a címtárban, a modern hitelesítés biztosítja, hogy a felhasználó szükség esetén kérje az MFA-t. Ez az örökölt hitelesítési protokollok biztonságosabb alternatívája.

Ez a szakasz lépésről lépésre áttekintést nyújt akörnyezet modern hitelesítésre való frissítéséről. Olvassa el az alábbi lépéseket, mielőtt engedélyezne egy örökölt hitelesítési blokkoló házirendet a szervezetben.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>1. lépés: Modern hitelesítés engedélyezése a címtárban

A modern hitelesítés engedélyezésének első lépése annak biztosítása, hogy a címtár támogatja a modern hitelesítést. 2017. augusztus 1-jén vagy azt követően létrehozott könyvtárak esetében a modern hitelesítés alapértelmezés szerint engedélyezve van. Ha a könyvtárat a jelen dátum előtt hozták létre, manuálisan kell engedélyeznie a modern hitelesítést a könyvtárhoz a következő lépésekkel:

1. Ellenőrizze, hogy a címtár már támogatja-e a modern hitelesítést a `Get-CsOAuthConfiguration` Skype Vállalati online [PowerShell modulból](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell)való futtatással.
1. Ha a parancs `OAuthServers` üres tulajdonságot ad vissza, akkor a modern hitelesítés le van tiltva. Frissítse a beállítást, hogy `Set-CsOAuthConfiguration`a modern hitelesítés t használja. Ha `OAuthServers` a tulajdon tartalmaz egy bejegyzést, akkor jó menni.

Ügyeljen arra, hogy ezt a lépést, mielőtt halad előre. Rendkívül fontos, hogy a címtárkonfigurációk először megváltoznak, mert ezek határozzák meg, hogy az összes Office-ügyfél melyik protokollt fogja használni. Még akkor is, ha modern hitelesítést támogató Office-ügyfeleket használ, alapértelmezés szerint örökölt protokollokat használnak, ha a modern hitelesítés le van tiltva a címtárban.

### <a name="step-2-office-applications"></a>2. lépés: Office-alkalmazások

Miután engedélyezte a modern hitelesítést a címtárban, megkezdheti az alkalmazások frissítését az Office-ügyfelek modern hitelesítésének engedélyezésével. Az Office 2016-os vagy újabb verzióiban az ügyfelek alapértelmezés szerint támogatják a modern hitelesítést. Nincs szükség további lépésekre.

Ha Office 2013 Windows-ügyfeleket vagy régebbi verziókat használ, javasoljuk, hogy frissítsen az Office 2016-ra vagy újabb verzióra. A régebbi Office-alkalmazások a modern hitelesítés engedélyezésének korábbi lépése után is örökölt hitelesítési protokollokat fognak használni. Ha Office 2013-ügyfeleket használ, és nem tud azonnal frissíteni az Office 2016-os vagy újabb verzióra, kövesse az alábbi, [Az Office 2013 modern hitelesítésének engedélyezése Windows-eszközökön](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication)című cikkben leírt lépéseket. A fiók védelme érdekében, miközben örökölt hitelesítést használ, javasoljuk, hogy erős jelszavakat használjon a címtárban. Tekintse meg [az Azure AD jelszavas védelmét,](../authentication/concept-password-ban-bad.md) hogy kitiltsa a gyenge jelszavakat a címtárban.

Az Office 2010 nem támogatja a modern hitelesítést. Az Office 2010-es verzióval rendelkező felhasználókat frissítenie kell az Office újabb verziójára. Javasoljuk, hogy frissítsen az Office 2016-ra vagy újabb verzióra, mivel alapértelmezés szerint blokkolja az örökölt hitelesítést.

MacOS használata esetén javasoljuk, hogy frissítsen a Mac Office 2016-os vagy újabb verzióra. Ha a natív levelezőprogramot használja, minden eszközön rendelkeznie kell a MacOS 10.14-es vagy újabb verziójával.

### <a name="step-3-exchange-and-sharepoint"></a>3. lépés: Exchange és SharePoint

Ahhoz, hogy a Windows-alapú Outlook-ügyfelek modern hitelesítést használhassanak, az Exchange Online-nak is engedélyeznie kell a modern hitelesítést. Ha a modern hitelesítés le van tiltva az Exchange Online-ban, a modern hitelesítést támogató Windows-alapú Outlook-ügyfelek (Outlook 2013-as vagy újabb verziók) alapfokú hitelesítést fognak használni az Exchange Online-postaládákhoz való csatlakozáshoz.

A SharePoint Online engedélyezve van a modern hitelesítés alapértelmezett beállításához. 2017. augusztus 1-je után létrehozott könyvtárak esetében a modern hitelesítés alapértelmezés szerint engedélyezve van az Exchange Online-ban. Ha azonban korábban letiltotta a modern hitelesítést, vagy az ezt megelőzően létrehozott könyvtárat használja, kövesse a következő cikkben a Modern hitelesítés engedélyezése az [Exchange Online-ban](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)című cikkben leírt lépéseket.

### <a name="step-4-skype-for-business"></a>4. lépés: Skype Vállalati verzió

A Skype Vállalati verzió által benyújtott örökölt hitelesítési kérelmek megelőzése érdekében engedélyezni kell a Skype Vállalati online verzió modern hitelesítését. 2017. augusztus 1-je után létrehozott könyvtárak esetében a Skype Vállalati verzió modern hitelesítése alapértelmezés szerint engedélyezve van.

Javasoljuk, hogy váltson át a Microsoft Teamsre, amely alapértelmezés szerint támogatja a modern hitelesítést. Ha azonban jelenleg nem tud áttérni, engedélyeznie kell a modern hitelesítést a Skype Vállalati online verzióban, hogy a Skype Vállalati ügyfelek modern hitelesítést használjanak. A Modern Hitelesítés engedélyezése a Skype Vállalati verzió modern hitelesítésének engedélyezéséhez kövesse a jelen cikkben [ismertetett, modern hitelesítést engedélyező, modern hitelesítést](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)a Modern Hitelesítést a Modern Vállalati verzióban című cikkben.

A Skype Vállalati online verzió modern hitelesítésének engedélyezése mellett azt javasoljuk, hogy a Skype Vállalati verzió modern hitelesítésének engedélyezésekor engedélyezze az Exchange Online modern hitelesítését is. Ez a folyamat segít szinkronizálni a modern hitelesítés állapotát az Exchange Online-ban és a Skype Vállalati verzióonline ban, és megakadályozza a Skype Vállalati ügyfelek több bejelentkezési parancsát.

### <a name="step-5-using-mobile-devices"></a>5. lépés: Mobileszközök használata

A mobileszközön lévő alkalmazásoknak az örökölt hitelesítést is le kell tiltaniuk. Javasoljuk, hogy használja az Outlook for Mobile-t. Az Outlook for Mobile alapértelmezés szerint támogatja a modern hitelesítést, és megfelel a többi MFA alapszintű védelmi házirendnek.

A natív iOS levelezőprogram használatához az iOS 11.0-s vagy újabb verzióját kell futtatnia, hogy a levelezőprogram frissítve legyen az örökölt hitelesítés blokkolásához.

### <a name="step-6-on-premises-clients"></a>6. lépés: Helyszíni ügyfelek

Ha Ön a helyszíni Exchange Server és a helyszíni Skype Vállalati verzió szolgáltatást használó hibrid ügyfél, mindkét szolgáltatást frissíteni kell a modern hitelesítés engedélyezéséhez. Ha hibrid környezetben modern hitelesítést használ, továbbra is hitelesíti a felhasználókat a helyszínen. Az erőforrásokhoz (fájlokhoz vagy e-mailekhez) való hozzáférés engedélyezésének története megváltozik.

Mielőtt elkezdené engedélyezni a modern hitelesítést a helyszínen, kérjük, győződjön meg arról, hogy megfelelt az előfeltételeknek. Most már készen áll a modern hitelesítés engedélyezésére a helyszínen.

A modern hitelesítés engedélyezésének lépései az alábbi cikkekben találhatók:

* [Az Exchange Server helyszíni beállítása hibrid modern hitelesítés használatára](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [A modern hitelesítés (ADAL) használata a Skype Vállalati verzióval](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>További lépések

- [Az Exchange Server helyszíni beállítása hibrid modern hitelesítés használatára](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [A modern hitelesítés (ADAL) használata a Skype Vállalati verzióval](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)
- [Régi hitelesítési folyamat letiltása](../conditional-access/block-legacy-authentication.md)
