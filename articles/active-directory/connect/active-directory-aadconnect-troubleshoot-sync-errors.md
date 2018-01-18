---
title: "Az Azure AD Connect: A szinkronizálás során hibák elhárítása |} Microsoft Docs"
description: "Az Azure AD Connect-szinkronizálás során észlelt hibák elhárítását ismerteti."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: curtand
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: billmath
ms.openlocfilehash: aaa374d5a11ef5b5860f83a87386ff981319189f
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="troubleshooting-errors-during-synchronization"></a>A szinkronizálás során hibák elhárítása
Hibák fordulhatnak elő, amikor azonosító adataihoz az Azure Active Directory (Azure AD) szinkronizálása a Windows Server Active Directory (AD DS). Ez a cikk áttekintést nyújt a szinkronizálási hibák, a következő eljárások érintett hibák, lehetséges módjai a javítsa ki a hibákat okozó némelyike különböző típusú. Ez a cikk tartalmazza a hiba gyakori hibatípusokat, és nem vonatkozhat a lehetséges hibákat.

 Ez a cikk azt feltételezi, hogy az olvasó ismeri az alapul szolgáló [tervezési alapelvek az Azure AD és az Azure AD Connect](active-directory-aadconnect-design-concepts.md).

Az Azure AD Connect legújabb verziójával \(augusztus 2016 vagy újabb\), szinkronizálási hibák jelentés érhető el a [Azure Portal](https://aka.ms/aadconnecthealth) az Azure AD Connect Health szinkronizálási szolgáltatás részeként.

2016 szeptemberétől 1 indítása [Azure Active Directory ismétlődő attribútum rugalmassági](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) szolgáltatás engedélyezve lesz alapértelmezés szerint az összes a *új* Azure Active Directory-bérlő. Ez a funkció automatikusan engedélyezve lesz a meglévő bérlők számára a jövőbeli hónapon belül.

Az Azure AD Connect 3 típusú műveleteket végez a címtárakban szinkronban tartja: Import, a szinkronizálás és az exportálás. Hibák az operatív végrehajthatók. Ez a cikk elsősorban az Azure Active Directoryba való exportálás során hibák összpontosít.

## <a name="errors-during-export-to-azure-ad"></a>Az Azure Active Directoryba való exportálás során hibák
A következő szakasz ismerteti a különböző típusú, amely akkor fordulhat elő, az exportálás során az Azure AD az Azure AD-összekötő szinkronizálási hibák. Ez az összekötő segítségével azonosítható a "contoso éppen formátuma. *onmicrosoft.com*".
Az Azure Active Directoryba való exportálás során hibát jelezve, hogy a művelet \(hozzáadása, frissítése és törlése stb.\) történt kísérlet az Azure AD Connect \(szinkronizálási motor\) az Azure Active Directoryban nem sikerült.

![Exportálási hiba – áttekintés](./media/active-directory-aadconnect-troubleshoot-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Adatok eltérés hibák
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Leírás
* Ha az Azure AD Connect \(szinkronizálási motor\) arra utasítja az Azure Active Directory hozzáadása vagy frissítése objektumok, az Azure AD megegyezik a bejövő objektum használatával a **sourceAnchor** attribútumot a **immutableId**  az Azure Active Directory-objektumok attribútum. A megfelelés nevezik egy **rögzített megfelelő**.
* Ha az Azure AD **nem talál** bármely objektum, amely megfelel a **immutableId** az attribútum a **sourceAnchor** attribútum egy új kiépítése előtt a bejövő objektum objektum visszavált található egyezés a ProxyAddresses és a UserPrincipalName attribútum használatával. A megfelelés nevezik egy **egyeznek helyreállítható**. A letölthető megfelelő célja, hogy felel meg az új objektumok szinkronizálás során hozzáadott vagy frissített, amelyek megfelelnek a ugyanaz az entitás (felhasználók, csoportok) a helyszíni már jelen van, az Azure ad-ben (azaz az Azure AD-forrása) objektumok.
* **InvalidSoftMatch** hiba akkor fordul elő, amikor a rögzített egyezés minden egyező objektum nem található **és** enyhe egyezést talál egy hozzá tartozó objektum, de az objektumokat, amelyek különböző értékre van *immutableId* mint a bejövő objektum *SourceAnchor*, amelyek arra utalnak, hogy a megfelelő objektum volt-e szinkronizálva a másik objektum az Active Directory helyszíni.

Más szóval ahhoz, hogy az enyhe match működéséhez, kell lennie a soft-feleltethető meg az objektum nem kell bármely értéket a *immutableId*. Ha bármelyik rendelkező objektum *immutableId* érték beállítása nem sikerült a merevlemez-egyezés, de a soft-match feltételeknek eleget tevő, a művelet eredményeznének InvalidSoftMatch szinkronizálási hiba történt.

Az Azure Active Directory-séma nem engedélyezi két vagy több objektum ugyanazt az értéket a következő attribútumokat. \(Ez nem egy tárgyal minden releváns kérdést.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> [Az Azure AD attribútum ismétlődő attribútum rugalmassági](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) szolgáltatás akkor is megkezdődött az Azure Active Directory alapértelmezett viselkedésként.  Ez csökkenti az Azure AD Connect (valamint más szinkronizálási ügyfelek) által látott szinkronizálási hibák száma az Azure AD rugalmasabb, hogy a duplikált ProxyAddresses és a UserPrincipalName attribútum a helyszíni AD-környezetek kezelését. Ez a funkció nem oldja meg az ismétlődési hibák. Így az adatok továbbra is meg kell javítani. De az új objektumokat, amelyek egyébként blokkolva vannak az Azure AD létre duplikált értékek miatt kiépítése lehetővé teszi. Ez a szinkronizálás vissza szinkronizálási hibák száma is csökkenti.
> Ha ezt a szolgáltatást a bérlő számára, a InvalidSoftMatch szinkronizálási hibák látható az új objektumok kiépítése során nem jelenik meg.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Példák InvalidSoftMatch
1. A helyszíni Active Directory létezik ugyanarra az értékre ProxyAddresses attribútum két vagy több objektumot. Csak az egyik van első üzembe helyezve az Azure ad-ben.
2. A helyszíni Active Directory létezik userPrincipalName azonos értékű két vagy több objektumot. Csak az egyik van első üzembe helyezve az Azure ad-ben.
3. Az objektum hozzá lett adva a a helyszíni Active Directoryról szinkronizálva a ProxyAddresses attribútum ugyanazt az értéket, amely az Azure Active Directory objektum. Az objektum, a helyszíni hozzáadott nincs kiépítve beolvasása az Azure Active Directoryban.
4. Objektum hozzá lett adva a helyszíni Active Directoryról szinkronizálva a userPrincipalName attribútum ugyanazt az értéket, amely egy Azure Active Directory-fiókba. Az objektum nincs kiépítve beolvasása az Azure Active Directoryban.
5. Egy szinkronizált fiókkal erdőből lett áthelyezve A az erdő b Azure AD Connect (szinkronizálási motor) a SourceAnchor kiszámításához használt ObjectGUID attribútum. Az erdő áthelyezés után a SourceAnchor értéke eltér. (A B erdő) az új objektum nem képes szinkronizálni a meglévő objektumot az Azure ad-ben.
6. A szinkronizált objektum kapott véletlenül törölve a helyszíni Active Directory és az új objektum lett létrehozva az Active Directory ugyanaz az entitás (például felhasználói) az Azure Active Directory-fiók törlése nélkül. Az új fiók nem tudja szinkronizálni a meglévő Azure AD-objektum.
7. Az Azure AD Connect lett eltávolítása és újratelepítése. A újbóli telepítése során a SourceAnchor, akkor másik attribútum választott ki. Összes objektum, amely korábban volt-e szinkronizálva leállt InvalidSoftMatch hibával szinkronizálása.

#### <a name="example-case"></a>Példa eset:
1. **Bob Smith** , a szinkronizált felhasználót az Azure Active Directoryban a helyszíni Active Directory *contoso.com*
2. Bob Smith **UserPrincipalName** be van állítva az  **bobs@contoso.com** .
3. **"abcdefghijklmnopqrstuv =="** van a **SourceAnchor** Bob Smith használata az Azure AD Connect számított **objectGUID** a helyszíni Active Directoryban, amely nem a  **immutableId** Bob Smith az Azure Active Directoryban.
4. Bob is rendelkezik a következő értékeket a **proxyAddresses** attribútum:
   * smtp:bobs@contoso.com
   * smtp:bob.smith@contoso.com
   * **smtp:bob@contoso.com**
5. Egy új felhasználóhoz **Bob Taylor**, a helyszíni Active Directory kerül.
6. Bob Taylor **UserPrincipalName** be van állítva az  **bobt@contoso.com** .
7. **"abcdefghijkl0123456789 ==" "** van a **sourceAnchor** Bob Taylor használata az Azure AD Connect számított **objectGUID** a a helyszíni Active Directoryban. Bob Taylor objektum rendelkezik még nincs szinkronizálva az Azure Active Directoryhoz.
8. Bob Taylor rendelkezik a következő értékeket a proxyAddresses attribútum
   * smtp:bobt@contoso.com
   * smtp:bob.taylor@contoso.com
   * **smtp:bob@contoso.com**
9. Szinkronizálás során az Azure AD Connect ismeri fel a helyszíni Active Directory a Bob Taylor hozzáadása, és kérje meg az Azure AD-be ezt a változtatást.
10. Az Azure AD először hajt végre rögzített egyezés. Ez azt jelenti, hogy ha a immutableId bármely objektum egyenlő megkeresi "abcdefghijkl0123456789 ==". Rögzített egyeztetés sikertelen lesz, mivel nincs más objektumot az Azure ad-ben, hogy immutableId fog rendelkezni.
11. Az Azure AD majd megpróbál Bob Taylor soft-találatra. Ez azt jelenti, hogy ha a proxyAddresses objektum a következő három érték, beleértve a egyenlő megkeresismtp:bob@contoso.com
12. Az Azure AD megkeresi a soft-match feltételeknek Bob Smith objektumot. Azonban ez az objektum értéke immutableId = "abcdefghijklmnopqrstuv ==". ami azt jelenti, hogy ez az objektum a helyszíni Active Directory a másik objektum lett szinkronizálva. Így az Azure AD nem soft-match ezek az objektumok és az eredményeket egy **InvalidSoftMatch** szinkronizálási hiba.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>InvalidSoftMatch hiba megoldásával kapcsolatban
A InvalidSoftMatch hiba leggyakoribb oka objektumok két különböző SourceAnchor \(immutableId\) ugyanazt az értéket a ProxyAddresses és/vagy a UserPrincipalName attribútumok, amelyek a soft-egyeztetés során használt az Azure AD feldolgozni. Ahhoz, hogy javítsa ki a érvénytelen enyhe egyezés

1. Azonosítsa a duplikált proxyAddresses, userPrincipalName, vagy a hibát okozó más attribútum-érték. Is azonosítani tudja az két \(vagy több\) objektumok részt az ütközést. A jelentés által generált [az Azure AD Connect Health szinkronizálási szolgáltatás](https://aka.ms/aadchsyncerrors) segítenek azonosítani azokat a két objektum.
2. Határozza meg, mely objektumtípusok továbbra is a duplikált értéket, és melyik objektum nem kell.
3. Az objektum, amely ezt az értéket nem lehet távolítsa el az ismétlődő érték. Vegye figyelembe, hogy kell a változtatásokat a könyvtárban, ahol az objektum származik. Bizonyos esetekben szükség lehet egy az objektumok ütköznek.
4. A módosítást a helyszíni AD a lehetővé teszik az Azure AD Connect szinkronizálása a módosítást.

Vegye figyelembe, hogy a szinkronizálási jelentést belül az Azure AD Connect Health szinkronizálási szolgáltatás 30 percenként frissül, és magában foglalja a hibák a legutóbbi szinkronizálási kísérlet után.

> [!NOTE]
> ImmutableId, definíció, nem szükséges módosítani a objektum élettartama során. Ha az Azure AD Connect nincs konfigurálva néhány forgatókönyv szem előtt, a fenti listából, sikerült befejezi a egy olyan esetben, ha az Azure AD Connect kiszámítja az AD-objektum ugyanaz az entitás jelölő SourceAnchor eltérő érték (azonos felhasználó/csoport / kapcsolattartási stb.), amely rendelkezik egy meglévő Azure AD-objektum, amely továbbra is használni kívánja.
>
>

#### <a name="related-articles"></a>Kapcsolódó cikkek
* [Érvénytelen vagy ismétlődő attribútumok megelőzése az Office 365-ben a címtár-szinkronizálás](https://support.microsoft.com/en-us/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Leírás
Az Azure AD megpróbálja világos felel meg a két objektum, esetén lehetséges, hogy a különböző két objektum "típusú objektum" (például felhasználó, csoport, az ügyfél stb) értékük azzal megegyezik a letölthető egyeztetés elvégzéséhez használt attribútumoknál. Ezek az attribútumok párhuzamos nem engedélyezett az Azure ad-ben, a művelet "ObjectTypeMismatch" szinkronizálási hiba eredményezhet.

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Példák ObjectTypeMismatch hiba
* Engedélyezése levelezési biztonsági csoport jön létre az Office 365-ben. Felügyeleti a helyszíni AD (az Azure AD még nincs szinkronizálva) a ProxyAddresses attribútum azonos értékű legyen, mint az Office 365 csoport hozzáadja az új felhasználó vagy az ügyfélhez.

#### <a name="example-case"></a>Példa eset
1. Felügyelet engedélyezése levelezési új biztonsági csoportot hoz létre az Office 365-ben az adó részleg, és itt egy e-mail címet, tax@contoso.com. Ez a ProxyAddresses attribútum értékét ehhez a csoporthoz rendeli hozzá**smtp:tax@contoso.com**
2. Új felhasználó csatlakozik a Contoso.com és a fiók jön létre a proxyAddress, mint a helyszínen a használatra**smtp:tax@contoso.com**
3. Ha az Azure AD Connect szinkronizálja az új felhasználói fiók, akkor a "ObjectTypeMismatch" hiba jelenik meg.

#### <a name="how-to-fix-objecttypemismatch-error"></a>ObjectTypeMismatch hiba megoldásával kapcsolatban
A leggyakoribb a ObjectTypeMismatch hiba oka, hogy két különböző típusú (felhasználó, csoport, az ügyfél stb) objektumoknak a ProxyAddresses attribútum ugyanazt az értéket. Ahhoz, hogy javítsa ki a ObjectTypeMismatch:

1. Azonosítsa a duplikált proxyAddresses (vagy más attribútum) érték, amely a hiba okozza. Is azonosítani tudja az két \(vagy több\) objektumok részt az ütközést. A jelentés által generált [az Azure AD Connect Health szinkronizálási szolgáltatás](https://aka.ms/aadchsyncerrors) segítenek azonosítani azokat a két objektum.
2. Határozza meg, mely objektumtípusok továbbra is a duplikált értéket, és melyik objektum nem kell.
3. Az objektum, amely ezt az értéket nem lehet távolítsa el az ismétlődő érték. Vegye figyelembe, hogy kell a változtatásokat a könyvtárban, ahol az objektum származik. Bizonyos esetekben szükség lehet egy az objektumok ütköznek.
4. A módosítást a helyszíni AD a lehetővé teszik az Azure AD Connect szinkronizálása a módosítást. Szinkronizálási hiba jelentés belül az Azure AD Connect Health szinkronizálási szolgáltatás 30 percenként frissül, és magában foglalja a hibák a legutóbbi szinkronizálási kísérlet után.

## <a name="duplicate-attributes"></a>Ismétlődő attribútumok
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Leírás
Az Azure Active Directory-séma nem engedélyezi két vagy több objektum ugyanazt az értéket a következő attribútumokat. Ez az Azure AD-ben az egyes objektumok kényszeríti, hogy ezek az attribútumok egyedi értéket a megadott példány.

* ProxyAddresses
* UserPrincipalName

Ha az Azure AD Connect megkísérli egy új objektum hozzáadása vagy egy meglévő objektum frissítése a fenti attribútumok értékkel, amely már hozzá van rendelve egy másik objektum az Azure Active Directoryban, a művelet a "AttributeValueMustBeUnique" hibát eredményez.

#### <a name="possible-scenarios"></a>A következő eljárások:
1. Ismétlődő értéket már szinkronizált objektum, amely ütközik egy másik szinkronizált objektum van hozzárendelve.

#### <a name="example-case"></a>Példa eset:
1. **Bob Smith** , a szinkronizált felhasználót az Azure Active Directoryban a contoso.com Active Directory helyszíni
2. Bob Smith **UserPrincipalName** a helyszíni be van állítva az  **bobs@contoso.com** .
3. Bob is rendelkezik a következő értékeket a **proxyAddresses** attribútum:
   * smtp:bobs@contoso.com
   * smtp:bob.smith@contoso.com
   * **smtp:bob@contoso.com**
4. Egy új felhasználóhoz **Bob Taylor**, a helyszíni Active Directory kerül.
5. Bob Taylor **UserPrincipalName** be van állítva az  **bobt@contoso.com** .
6. **Bob Taylor** a következő értékeket a **ProxyAddresses** i attribútum. smtp:bobt@contoso.com ii. smtp:bob.taylor@contoso.com
7. Bob Taylor objektum sikeresen szinkronizált Azure AD-val.
8. Rendszergazda úgy döntött, hogy Bálint Taylor frissítése **ProxyAddresses** attribútum a következő értékkel: i. **smtp:bob@contoso.com**
9. Az Azure AD megkísérli Bob Taylor objektum frissítése az Azure AD a fenti értékű, de ez a művelet sikertelen lesz hogy ProxyAddresses érték már hozzá van rendelve Bob Smith "AttributeValueMustBeUnique" hibát eredményezte.

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>AttributeValueMustBeUnique hiba megoldásával kapcsolatban
A AttributeValueMustBeUnique hiba leggyakoribb oka objektumok két különböző SourceAnchor \(immutableId\) ugyanazt az értéket a ProxyAddresses és/vagy a UserPrincipalName attribútum. Ahhoz, hogy javítsa ki a AttributeValueMustBeUnique hiba

1. Azonosítsa a duplikált proxyAddresses, userPrincipalName, vagy a hibát okozó más attribútum-érték. Is azonosítani tudja az két \(vagy több\) objektumok részt az ütközést. A jelentés által generált [az Azure AD Connect Health szinkronizálási szolgáltatás](https://aka.ms/aadchsyncerrors) segítenek azonosítani azokat a két objektum.
2. Határozza meg, mely objektumtípusok továbbra is a duplikált értéket, és melyik objektum nem kell.
3. Az objektum, amely ezt az értéket nem lehet távolítsa el az ismétlődő érték. Vegye figyelembe, hogy kell a változtatásokat a könyvtárban, ahol az objektum származik. Bizonyos esetekben szükség lehet egy az objektumok ütköznek.
4. A módosítást a helyszíni AD a lehetővé teszik az Azure AD Connect szinkronizálása a módosítás lehet javítani a hibát.

#### <a name="related-articles"></a>Kapcsolódó cikkek
-[Érvénytelen vagy ismétlődő attribútumok megelőzése az Office 365-ben a címtár-szinkronizálás](https://support.microsoft.com/en-us/kb/2647098)

## <a name="data-validation-failures"></a>Ellenőrzési hibával
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Leírás
Az Azure Active Directory érvénybe lépteti az adatokat mozgatná mielőtt engedélyezné a könyvtárba kell megírni, hogy az adatok különböző korlátozásait. Ez azért szükséges, hogy a végfelhasználók le a legjobb lehetséges használatakor a ezek az adatok függő alkalmazások használata közben.

#### <a name="scenarios"></a>Forgatókönyvek
a. A UserPrincipalName attribútum értéke érvénytelen vagy nem támogatott karaktereket tartalmaz.
b. A UserPrincipalName attribútum nem követi a kötelező.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>IdentityDataValidationFailed hiba megoldásával kapcsolatban
a. Győződjön meg arról, hogy a userPrincipalName attribútum támogatott tartalmaz karakterek és a megkívánt formátumban.

#### <a name="related-articles"></a>Kapcsolódó cikkek
* [Konfigurálta a felhasználókat keresztül Office 365 címtár-szinkronizálás előkészítése](https://support.office.com/en-us/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Leírás
Ez az egy adott esetben az eredmények a **"FederatedDomainChangeError"** szinkronizálási hiba, ha egy másik összevont tartományt a utótagját, a felhasználó UserPrincipalName változtatják egy összevont tartományt.

#### <a name="scenarios"></a>Forgatókönyvek
A szinkronizált felhasználó számára a UserPrincipalName utótag módosult a egy összevont tartományt a másik összevont tartományt a helyszínen. Például *UserPrincipalName = bob@contoso.com*  értékre változott az *UserPrincipalName = bob@fabrikam.com* .

#### <a name="example"></a>Példa
1. Bob Smith, egy fiókot a Contoso.com, az Active Directory a UserPrincipalName és az új felhasználói bekerülbob@contoso.com
2. Bob áthelyezése egy másik osztás a Contoso.com, Fabrikam.com nevezik, és a UserPrincipalName változottbob@fabrikam.com
3. A contoso.com és fabrikam.com tartományai az Azure Active Directoryval összevont tartományt.
4. Bob userPrincipalName nem frissül, és a "FederatedDomainChangeError" hibát eredményez.

#### <a name="how-to-fix"></a>Megoldásával kapcsolatban
Ha egy felhasználó UserPrincipalName utótagot a bob @ frissült**contoso.com** a Belinszky**fabrikam.com**, ahol mindkettő **contoso.com** és **fabrikam.com** vannak **tartományok összevont**, majd kövesse az alábbi lépéseket a hiba elhárításához

1. Az Azure AD-ben a felhasználó UserPrincipalName frissítése bob@contoso.com való bob@contoso.onmicrosoft.com. A következő PowerShell-parancs használatával az Azure AD PowerShell modult:`Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Engedélyezze a következő szinkronizálási ciklusban szinkronizálási kísérletet. Az időszinkronizálást sikeres lesz, és frissíti a Bob UserPrincipalName való bob@fabrikam.com várt módon.

#### <a name="related-articles"></a>Kapcsolódó cikkek
* [A felhasználói fiók egyszerű másik összevont tartományt használandó módosítása után a módosítások nem az Azure Active Directory szinkronizáló eszköz által szinkronizálása](https://support.microsoft.com/en-us/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Leírás
Egy attribútum meghaladja a megengedett méretét, a korlát vagy az Azure Active Directory-séma által beállított maximális száma, amikor a szinkronizálási művelet eredményeként a **LargeObject** vagy **ExceededAllowedLength**szinkronizálási hiba. Általában ez a hiba akkor fordul elő, az alábbi attribútumok

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>A következő eljárások
1. Bob userCertificate attribútum Bob rendelt túl sok tanúsítvány tárolja. Ezek közé tartozhatnak a régebbi, a lejárt tanúsítványok. A rögzített határértéke 15 tanúsítványokat. A userCertificate attribútummal rendelkező LargeObject hibák kezelésének módját további információkért tekintse meg a cikk [userCertificate attribútum által okozott kezelése LargeObject hibákat](active-directory-aadconnectsync-largeobjecterror-usercertificate.md).
2. Bob userSMIMECertificate attribútum Bob rendelt túl sok tanúsítvány tárolja. Ezek közé tartozhatnak a régebbi, a lejárt tanúsítványok. A rögzített határértéke 15 tanúsítványokat.
3. Az Active Directoryban beállított Bob thumbnailPhoto tehát túl nagy a szinkronizált az Azure AD-ben.
4. A ProxyAddresses attribútum az Active Directory automatikus feltöltése során az objektumnak túl sok ProxyAddresses hozzárendelve.

### <a name="how-to-fix"></a>Megoldásával kapcsolatban
1. Győződjön meg arról, hogy az attribútum, ez okozta a hibát az engedélyezett korlátozás belül van-e.

## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Keresse meg az Active Directory-objektumok az Active Directory felügyeleti központ](https://technet.microsoft.com/library/dd560661.aspx)
* [Azure Active Directory lekérdezése az Azure Active Directory PowerShell objektumhoz](https://msdn.microsoft.com/library/azure/jj151815.aspx)
