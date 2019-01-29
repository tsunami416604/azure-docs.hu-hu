---
title: 'Azure AD Connect: Szinkronizálási hibák elhárítása |} A Microsoft Docs'
description: Ismerteti az Azure AD Connect-szinkronizálás során észlelt hibák elhárítása.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 2209d5ce-0a64-447b-be3a-6f06d47995f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: c48ffd92362fa19fc0abfb7ddc36eac378283938
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55156332"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Szinkronizálási hibák elhárítása
Hibák fordulhatnak elő, amikor a azonosító adatok szinkronizálása a Windows Server Active Directory (AD DS) az Azure Active Directory (Azure AD). Ez a cikk szinkronizálási hibák, a lehetséges forgatókönyvek ezeket a hibákat és lehetséges módon javítsa a hibákat okozó némelyike különböző típusainak áttekintése. Ez a cikk a gyakori alkalmazáshiba-típusok tartalmazza, és a lehetséges hibák terjedhet ki.

 Ez a cikk feltételezi, hogy az olvasó ismerik a mögöttes [tervezési alapelvei, az Azure AD és az Azure AD Connect](plan-connect-design-concepts.md).

Az Azure AD Connect legújabb verziójának \(augusztus 2016-os vagy magasabb\), a szinkronizálási hibák jelentés érhető el a [az Azure portal](https://aka.ms/aadconnecthealth) az Azure AD Connect Health szinkronizálási szolgáltatás részeként.

2016. szeptember 1-től [Azure Active Directory ismétlődő attribútumok rugalmassága](how-to-connect-syncservice-duplicate-attribute-resiliency.md) funkció alapértelmezés szerint az összes engedélyezzük a *új* Azure Active Directory-bérlők. Ez a funkció automatikusan engedélyezve lesz a meglévő bérlők számára a soron következő hónapban.

Az Azure AD Connect három típusú műveletek szinkronban tartja a címtárakból hajtja végre: Importálás, a szinkronizálás és exportálási. Hibák az összes művelet elvégezhető. Ez a cikk elsősorban az Azure Active Directoryba való exportálás során hibák összpontosít.

## <a name="errors-during-export-to-azure-ad"></a>Az Azure Active Directoryba való exportálás során hibák
Azure ad-bA az Azure AD-összekötő az exportálási művelet során előforduló hibák különböző típusú a következő szakasz ismerteti. Ez az összekötő segítségével azonosítható a nevének formátuma folyamatban van a "contoso. *onmicrosoft.com*".
Az Azure Active Directoryba való exportálás során hibák jelzi, hogy a művelet \(hozzáadása, frissítése és törlése stb.\) próbált meg Azure AD Connect \(szinkronizálási motor\) az Azure Active Directory nem sikerült.

![Exportálási hibák – áttekintés](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Adateltérési hiba
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Leírás
* Ha az Azure AD Connect \(szinkronizálási motor\) arra utasítja a hozzáadandó vagy frissítendő objektumok Azure Active Directory, Azure ad-ben megegyezik a bejövő objektum használatával a **sourceAnchor** attribútumot a **immutableid azonosítója**  objektumok az Azure AD-attribútum. A megfelelés nevezzük egy **rögzített megfelelő**.
* Ha az Azure AD **nem talál** minden olyan objektum, amely megfelel a **immutableId** az attribútum a **sourceAnchor** attribútum a bejövő objektum egy új kiépítése előtt objektum visszavált a ProxyAddresses és a UserPrincipalName attribútum használatával egyezést talál. A megfelelés nevezzük egy **helyreállítható megfelelő**. A helyreállítható felel meg az új képviselő objektumokkal való szinkronizálás során hozzáadása/frissítése folyamatban ugyanaz az entitás (felhasználók, csoportok) a helyszínen már jelen van, az Azure AD (forrásai, az Azure AD-) objektumok tervezték.
* **InvalidSoftMatch** hiba akkor fordul elő, ha a rögzített egyeztetése nem talál minden megfeleltetett ilyen objektumhoz **és** helyreállítható egyezést talál egy megfeleltetett ilyen objektumhoz, de az objektum értéke egy másik *immutableId* , mint a bejövő objektum *SourceAnchor*, amely arra utal, hogy a megfelelő objektum be egy másik objektum lett szinkronizálva a helyszíni Active Directory.

Más szóval ahhoz, hogy működjön a helyreállítható egyezést, helyreállítható egyezik az objektum nem kell semmilyen értéket a *immutableId*. Ha bármelyik rendelkező objektum *immutableId* értékkel beállítása sikertelen a merevlemez-egyezés, de a egyezéssel feltételeknek eleget tevő, a művelet eredményezne InvalidSoftMatch szinkronizálási hiba.

Az Azure Active Directory-séma nem engedélyezi két vagy több objektum ugyanazt az értéket a következő attribútumokat. \(Ez nem egy kimerítően teljes lista.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> [Az Azure AD attribútum ismétlődő attribútumok rugalmassága](how-to-connect-syncservice-duplicate-attribute-resiliency.md) funkció is tesszük elérhetővé az Azure Active Directory alapértelmezett viselkedésként.  Ez csökkenti az Azure AD Connect (valamint más szinkronizálási ügyfelek) által látott szinkronizálási hibák száma szerint az Azure AD ellenállóbbá teszi a helyszíni AD-környezetekben található duplikált ProxyAddresses és a UserPrincipalName attribútum kezelését. Ez a funkció nem oldja meg az ismétlődési hibák. Így az adatok továbbra is ki kell javítani van szüksége. De lehetővé teszi új objektumokat, amelyek egyébként hozzáférése a duplikált értékek miatt az Azure ad-ben kiépítése folyamatban kiépítését. Ez is csökkenti, a szinkronizációs ügyfél szinkronizálási hibák száma.
> Ha ez a funkció engedélyezve van a bérlő számára, nem láthatja a InvalidSoftMatch szinkronizálási hibák új objektumokat az üzembe helyezés alatt látható.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Példaforgatókönyvek InvalidSoftMatch
1. Két vagy több objektumot a ProxyAddresses attribútum ugyanazzal az értékkel szerepel a helyszíni Active Directoryban. Csak az egyik van első üzembe helyezve, az Azure ad-ben.
2. Ugyanaz az érték a userPrincipalName attribútum a két vagy több objektum létezik, a helyszíni Active Directoryban. Csak az egyik van első üzembe helyezve, az Azure ad-ben.
3. Egy objektum hozzáadva a helyi a ProxyAddresses attribútum azonos értékű az Active Directory-e egy meglévő objektumot az Azure Active Directoryban. A helyszíni hozzáadott objektum nincs kiépítve első az Azure Active Directoryban.
4. Egy objektum hozzáadva a helyszíni userPrincipalName attribútum azonos értékű az Active Directory-e az Azure Active Directory-fiók. Az objektum nincs kiépítve Bevezetés az Azure Active Directoryban.
5. A szinkronizált erdő áthelyezték A b erdő az Azure AD Connect (szinkronizálási motor) volt ObjectGUID attribútum használata a SourceAnchor számítási. Az erdő áthelyezés után a SourceAnchor értéke eltér. (A B erdő) az új objektum nem tudta szinkronizálni a meglévő objektumot az Azure ad-ben.
6. A szinkronizált objektum van véletlenül törölve a helyszíni Active Directory és a egy új objektum nem lett létrehozva az Active Directory ugyanahhoz az entitáshoz (például a felhasználó) az Azure Active Directoryban a fiók törlése folyamatban van. Az új fiók nem tudja szinkronizálni a meglévő Azure AD-objektum.
7. Az Azure AD Connect volt eltávolították és újratelepítették. Az újratelepítés során egy másik attribútum lett kiválasztva a SourceAnchor a. Összes objektum, amely korábban a korábban szinkronizált le InvalidSoftMatch hiba való szinkronizálását.

#### <a name="example-case"></a>Példa. eset:
1. **Bob Smith** a szinkronizált felhasználók az Azure Active Directory a helyszíni Active Directory *contoso.com*
2. Bob Smith **UserPrincipalName** van beállítva, **bobs@contoso.com**.
3. **"abcdefghijklmnopqrstuv =="** van a **SourceAnchor** számítja ki az Azure AD Connect használatával Bob Smith **objectGUID** a helyszíni Active Directoryban, amely már van a  **immutableId** Bob Smith az Azure Active Directoryban.
4. Bob is tartalmaz a következő értékeket a **proxyAddresses** attribútum:
   * SMTP bobs@contoso.com
   * SMTP bob.smith@contoso.com
   * **smtp: bob@contoso.com**
5. Egy új felhasználóhoz **Bob Taylor**, adnak hozzá a helyszíni Active Directoryban.
6. Bob Taylor **UserPrincipalName** van beállítva, **bobt@contoso.com**.
7. **"abcdefghijkl0123456789 ==" "** van a **sourceAnchor** számítja ki az Azure AD Connect használatával Bob Taylor **objectGUID** , a helyszíni Active Directoryban. Bob Taylor objektum még nincs szinkronizálva az Azure Active Directory még.
8. Bob Taylor rendelkezik a következő értékeket a proxyAddresses attribútum
   * SMTP bobt@contoso.com
   * SMTP bob.taylor@contoso.com
   * **smtp: bob@contoso.com**
9. Szinkronizálás során az Azure AD Connect Bob Taylor a helyszíni Active Directory is ismeri fel, és kérje meg Azure AD-be a korábbi módosításokat.
10. Azure ad-ben először végrehajtja a rögzített egyezést. Azt jelenti, ha bármely objektum immutableid azonosítója egyenlő megkeresi "abcdefghijkl0123456789 ==". Rögzített egyeztetés sikertelen lesz, mivel nincs más objektumot az Azure ad-ben, hogy immutableid azonosítója lesz.
11. Az Azure AD megpróbálja majd egyezéssel Bob Taylor. Megkeresi azt jelenti, ha bármely objektum proxyAddresses három érték, beleértve az smtp egyenlő: bob@contoso.com
12. Az Azure AD megkeresi, Bob Smith egyezéssel feltételeknek megfelelő objektum. Ez az objektum immutableId érték szerepel-e, de = "abcdefghijklmnopqrstuv ==". ami azt jelenti, hogy ez az objektum lett szinkronizálva a helyszíni Active Directory egy másik objektum. Így az Azure AD nem egyezéssel ezek az objektumok és az eredményeket egy **InvalidSoftMatch** szinkronizálási hibát.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>InvalidSoftMatch hiba javítása
A InvalidSoftMatch hiba leggyakoribb oka objektumok két különböző SourceAnchor \(immutableId\) ugyanazt az értéket a ProxyAddresses és/vagy a UserPrincipalName attribútum, egyezéssel során használt az Azure AD feldolgozni. Annak érdekében, hogy javítsa az érvénytelen helyreállítható egyezés

1. Azonosítsa a duplikált proxyAddresses, userPrincipalName vagy más attribútum-érték, amely a hibát okoz. Is azonosítja két \(vagy több\) az ütköző objektumot is érint. A jelentés által generált [az Azure AD Connect Health szinkronizálási szolgáltatás](https://aka.ms/aadchsyncerrors) segítségével azonosíthatja a két objektum.
2. Azonosítsa, mely objektumnak kell továbbra is az ismétlődő értéket, és mely objektumnak kell nem.
3. Távolítsa el az ismétlődő értéket az objektumot, nem kell ezt az értéket. Érdemes a módosítást a könyvtárban, ahol az objektum forrása. Bizonyos esetekben szükség lehet egy ütközést-objektum törlése.
4. Ha a módosítás a helyszíni AD meg, lehetővé teszik az Azure AD Connect szinkronizálja a módosítást.

Szinkronizálási hibajelentések belül az Azure AD Connect Health szinkronizálási szolgáltatás 30 percenként frissülnek, és tartalmazzák a legutóbbi szinkronizálási kísérlet a hibákat.

> [!NOTE]
> ImmutableId, definíció szerint kell nem változtatható meg az objektum élettartamát. Az Azure AD Connect nem lett konfigurálva néhány forgatókönyv szem előtt a fenti listában, ha Ön sikerült végül olyan helyzetekben, ahol az Azure AD Connect az AD-objektum, amely ugyanahhoz az entitáshoz a SourceAnchor eltérő érték alapján számítja ki (ugyanazon felhasználó/csoport / kapcsolattartási stb.), amely rendelkezik egy meglévő Azure AD-objektum, amely továbbra is használni szeretné.
>
>

#### <a name="related-articles"></a>Kapcsolódó cikkek
* [Ismétlődő vagy érvénytelen attribútumok megelőzése az Office 365-ben a címtár-szinkronizálás](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Leírás
Amikor az Azure AD megpróbálja helyreállítható egyezik a két objektum, akkor lehet, hogy a különböző két objektum "objektumtípus" (például felhasználó, csoport, az ügyfél stb) az attribútumok a helyreállítható egyeztetés elvégzéséhez használt ugyanazzal az értékkel rendelkeznek. Mivel ezek az attribútumok a párhuzamos nem engedélyezett az Azure ad-ben, a művelet "ObjectTypeMismatch" szinkronizálási hiba eredményezhet.

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Példaforgatókönyvek ObjectTypeMismatch hiba
* Levelezéses biztonsági csoport jön létre az Office 365-ben. Rendszergazda helyszíni (nem szinkronizált még az Azure ad) a ProxyAddresses attribútum ugyanazzal az értékkel, az Office 365-csoportot ad hozzá egy új felhasználót vagy az ügyfélhez.

#### <a name="example-case"></a>Példa eset
1. Rendszergazdai hoz létre egy új levelezéses biztonsági csoportot az Office 365-ben a könyvelőjéhez, és a egy e-mail címet, biztosít tax@contoso.com. Ehhez a csoporthoz van hozzárendelve a ProxyAddresses attribútum értékét **smtp: tax@contoso.com**
2. Új felhasználó csatlakozik a Contoso.com és a egy fiók létrehozása a helyszínen a proxyAddress, mint a felhasználó **smtp: tax@contoso.com**
3. Ha az Azure AD Connect szinkronizálja az új felhasználói fiók, akkor a "ObjectTypeMismatch" hiba jelenik meg.

#### <a name="how-to-fix-objecttypemismatch-error"></a>ObjectTypeMismatch hiba javítása
A ObjectTypeMismatch hiba leggyakoribb oka, különböző típusú (felhasználó, csoport, az ügyfél stb) két objektum a ProxyAddresses attribútum ugyanazzal az értékkel rendelkeznek. Annak érdekében, hogy javítsa ki a ObjectTypeMismatch:

1. Azonosítsa a duplikált proxyAddresses (vagy más attribútum) érték, amely a hibát okoz. Is azonosítja két \(vagy több\) az ütköző objektumot is érint. A jelentés által generált [az Azure AD Connect Health szinkronizálási szolgáltatás](https://aka.ms/aadchsyncerrors) segítségével azonosíthatja a két objektum.
2. Azonosítsa, mely objektumnak kell továbbra is az ismétlődő értéket, és mely objektumnak kell nem.
3. Távolítsa el az ismétlődő értéket az objektumot, nem kell ezt az értéket. Vegye figyelembe, hogy kell-e a módosítás végrehajtása a könyvtárban, ahol az objektum forrása. Bizonyos esetekben szükség lehet egy ütközést-objektum törlése.
4. Ha a módosítás a helyszíni AD meg, lehetővé teszik az Azure AD Connect szinkronizálja a módosítást. Szinkronizálási hibajelentés belül az Azure AD Connect Health szinkronizálási szolgáltatás 30 percenként frissül, és a hibák a legutóbbi szinkronizálási kísérlet tartalmazza.

## <a name="duplicate-attributes"></a>Ismétlődő attribútumok
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Leírás
Az Azure Active Directory-séma nem engedélyezi két vagy több objektum ugyanazt az értéket a következő attribútumokat. Ez az Azure AD-ben minden egyes objektum kényszeríti a két attribútumnak egyedi értékkel rendelkezik: egy adott példány.

* ProxyAddresses
* UserPrincipalName

Az Azure AD Connect megpróbál adjon hozzá egy új objektumot, vagy frissíteni egy meglévő objektum, amely az Azure Active Directoryban egy másik objektum már hozzá van rendelve a fenti attribútumok értéket, ha a művelet a "AttributeValueMustBeUnique" szinkronizálási hibát eredményezi.

#### <a name="possible-scenarios"></a>Lehetséges alkalmazási helyzetek:
1. Ismétlődő értéket már szinkronizált objektumot, amely ütközik egy másik szinkronizált objektum van hozzárendelve.

#### <a name="example-case"></a>Példa. eset:
1. **Bob Smith** a szinkronizált felhasználók az Azure Active Directoryban a contoso.com Active Directory helyszíni
2. Bob Smith **UserPrincipalName** a helyszínen van beállítva, **bobs@contoso.com**.
3. Bob is tartalmaz a következő értékeket a **proxyAddresses** attribútum:
   * SMTP bobs@contoso.com
   * SMTP bob.smith@contoso.com
   * **smtp: bob@contoso.com**
4. Egy új felhasználóhoz **Bob Taylor**, adnak hozzá a helyszíni Active Directoryban.
5. Bob Taylor **UserPrincipalName** van beállítva, **bobt@contoso.com**.
6. **Bob Taylor** rendelkezik a következő értékeket a **ProxyAddresses** i attribútum. SMTP: bobt@contoso.com ii. SMTP bob.taylor@contoso.com
7. Bob Taylor objektum sikeresen szinkronizálva az Azure ad-ben.
8. Rendszergazda úgy döntött, hogy frissítse Bob Taylor **ProxyAddresses** attribútum a következő értékkel: i. **smtp: bob@contoso.com**
9. Az Azure AD megpróbálja frissítse Bob Taylor objektumot a fenti értéket, az Azure AD-ben azonban, hogy a művelet sikertelen lesz hogy ProxyAddresses érték már hozzá van rendelve Bob Smith, "AttributeValueMustBeUnique" hibát eredményez.

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>AttributeValueMustBeUnique hiba javítása
A AttributeValueMustBeUnique hiba leggyakoribb oka objektumok két különböző SourceAnchor \(immutableId\) ugyanazt az értéket a ProxyAddresses és/vagy a UserPrincipalName attribútum. Annak érdekében, hogy AttributeValueMustBeUnique hiba javítása

1. Azonosítsa a duplikált proxyAddresses, userPrincipalName, vagy a hibát okozó más attribútum-érték. Is azonosítja két \(vagy több\) az ütköző objektumot is érint. A jelentés által generált [az Azure AD Connect Health szinkronizálási szolgáltatás](https://aka.ms/aadchsyncerrors) segítségével azonosíthatja a két objektum.
2. Azonosítsa, mely objektumnak kell továbbra is az ismétlődő értéket, és mely objektumnak kell nem.
3. Távolítsa el az ismétlődő értéket az objektumot, nem kell ezt az értéket. Vegye figyelembe, hogy kell-e a módosítás végrehajtása a könyvtárban, ahol az objektum forrása. Bizonyos esetekben szükség lehet egy ütközést-objektum törlése.
4. Ha a módosítás a helyszíni AD meg, lehetővé teszik az első kijavítva a hiba a módosítás szinkronizálása az Azure AD Connect.

#### <a name="related-articles"></a>Kapcsolódó cikkek
-[Ismétlődő vagy érvénytelen attribútumok megelőzése az Office 365-ben a címtár-szinkronizálás](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Ellenőrzési hibával
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Leírás
Az Azure Active Directory magát az adatokat az adatokat a címtárba írandó engedélyezése előtt különféle korlátozások érvényesítése. Ezek a korlátozások vonatkoznak, annak érdekében, hogy a végfelhasználók a legjobb lehetséges élményt az adatok ettől az alkalmazások használata során.

#### <a name="scenarios"></a>Forgatókönyvek
a. A UserPrincipalName attribútum értéke érvénytelen vagy nem támogatott karaktereket tartalmaz.
b. A UserPrincipalName attribútum nem követi a kötelező.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>IdentityDataValidationFailed hiba javítása
a. Győződjön meg arról, hogy a userPrincipalName attribútum támogatott karakterek és a szükséges formátumban tartalmaz-e.

#### <a name="related-articles"></a>Kapcsolódó cikkek
* [Felhasználók – Office 365-höz a címtár-szinkronizálás előkészítése](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>FederatedDomainChangeError
#### <a name="description"></a>Leírás
Ebben az esetben eredményez olyan **"FederatedDomainChangeError"** szinkronizálási hiba, amikor a felhasználó UserPrincipalName utótagját változtatják egy összevont tartományt egy másik, összevont tartományhoz.

#### <a name="scenarios"></a>Forgatókönyvek
A szinkronizált felhasználó számára a UserPrincipalName utótag módosult a egy összevont tartományt a egy másik, összevont tartományt a helyszínen. Ha például *UserPrincipalName = bob@contoso.com*  módosítva lett, *UserPrincipalName = bob@fabrikam.com* .

#### <a name="example"></a>Példa
1. Bob Smith, egy fiókot a contoso.com, az Active Directory a UserPrincipalName és az új felhasználó hozzáadása bob@contoso.com
2. Bob áthelyezi a Contoso.com, Fabrikam.com nevű másik része, és a UserPrincipalName változott bob@fabrikam.com
3. A contoso.com és fabrikam.com tartományt az Azure Active Directoryval összevont tartományokban.
4. Bob a userPrincipalName nem módosul, és a "FederatedDomainChangeError" szinkronizálási hibát eredményezi.

#### <a name="how-to-fix"></a>Hogyan háríthatja el a
Ha a felhasználó UserPrincipalName utótag frissítve lett-e a bob @**contoso.com** , Belinszky**fabrikam.com**, ahol mindkettő **contoso.com** és **fabrikam.com** vannak **összevont tartományok**, majd kövesse az alábbi lépéseket a szinkronizálási hiba javításához

1. Az Azure AD-ben a felhasználó UserPrincipalName frissítése bob@contoso.com való bob@contoso.onmicrosoft.com. A következő PowerShell-parancsot használhatja az Azure AD PowerShell-modullal: `Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Engedélyezi a szinkronizálási kísérlet a következő szinkronizálási ciklus. Ez alkalommal a szinkronizálás sikeres lesz, és frissíti a Bob UserPrincipalName való bob@fabrikam.com elvárt módon.

#### <a name="related-articles"></a>Kapcsolódó cikkek
* [Az egyszerű felhasználónév a felhasználói fiókok különböző összevont tartományt használandó módosítása után a módosítások nem az Azure Active Directory Sync Tool szinkronizálása](https://support.microsoft.com/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Leírás
Amikor egy attribútum túllép az engedélyezett méretkorlátot, maximális hossz vagy mennyiségkorláton, Azure Active Directory-séma, a szinkronizálási művelet eredményeként a **LargeObject** vagy **ExceededAllowedLength**szinkronizálási hibát. Általában ez a hiba akkor fordul elő a következő attribútumoknál

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Lehetséges forgatókönyvek
1. Bob userCertificate attribútum túl sok tanúsítvány Bob rendelt tárolja. A régebbi és a lejárt tanúsítványok is tartalmazhatnak. A rögzített korlátja az 15 tanúsítványok. UserCertificate attribútummal rendelkező LargeObject-hibák kezeléséről további információkért tekintse meg a cikk [userCertificate attribútum által okozott LargeObject hibák](tshoot-connect-largeobjecterror-usercertificate.md).
2. Bob a userSMIMECertificate attribútum túl sok tanúsítvány Bob rendelt tárolja. A régebbi és a lejárt tanúsítványok is tartalmazhatnak. A rögzített korlátja az 15 tanúsítványok.
3. Az Active Directoryban beállított Bob thumbnailPhoto mérete túl nagy, szinkronizálja az Azure ad-ben.
4. Automatikus feltöltése a ProxyAddresses attribútum az Active Directoryban, során az objektumnak túl sok ProxyAddresses hozzárendelve.

### <a name="how-to-fix"></a>Hogyan háríthatja el a
1. Győződjön meg arról, hogy az attribútum okozza a hibát az engedélyezett korlát belül van-e.

## <a name="existing-admin-role-conflict"></a>Meglévő rendszergazdai szerepkör-ütközés

### <a name="description"></a>Leírás
Egy **meglévő rendszergazdai szerepkör ütközés** történjen a user objektum a szinkronizálás során a felhasználói objektum rendelkezik:

- rendszergazdai engedélyekkel, és
- az azonos UserPrincipalName olyan meglévő Azure AD-objektum

Az Azure AD Connect nem megengedett a helyreállítható találatra felhasználói objektum a helyszíni AD-felhasználói objektum, amely rendelkezik hozzárendelt felügyeleti szerepkörű Azure AD-ben.  További információ: [Azure AD UserPrincipalName feltöltése](plan-connect-userprincipalname.md)

![Meglévő felügyeleti](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>Hogyan háríthatja el a
Ez a probléma tegye a következők egyikét megoldása:


- Módosítsa a UserPrincipalName értéke nem egyezik meg egy rendszergazdai felhasználót az Azure AD - hoz létre egy új felhasználót a megfelelő UserPrincipalName az Azure AD-ben
- az Azure ad-ben, amely lehetővé teszi a helyreállítható egyeznek a helyi felhasználói objektum és a meglévő Azure AD-felhasználói objektum távolítsa el a rendszergazdai felhasználót a rendszergazdai szerepkört.

>[!NOTE]
>Hozzárendelheti a rendszergazdai szerepkör a meglévő felhasználói objektum újra a helyreállítható egyeznek a helyi felhasználói objektum és az Azure AD-felhasználói objektum befejeződése után.

## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Keresse meg az Active Directory-objektumok az Active Directory felügyeleti központ](https://technet.microsoft.com/library/dd560661.aspx)
* [Azure Active Directory lekérdezése az Azure Active Directory PowerShell-lel objektum](https://msdn.microsoft.com/library/azure/jj151815.aspx)
