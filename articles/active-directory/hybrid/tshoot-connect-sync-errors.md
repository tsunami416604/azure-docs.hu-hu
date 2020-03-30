---
title: 'Azure AD Connect: Hibaelhárítás a szinkronizálás során | Microsoft dokumentumok'
description: A cikk ismerteti, hogyan háríthatja el az Azure AD Connecttel való szinkronizálás során észlelt hibákat.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 745ddcc95bb91e61478307265aec1ac8a7ebba54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609196"
---
# <a name="troubleshooting-errors-during-synchronization"></a>Hibák elhárítása szinkronizálás közben
Hibák fordulhatnak elő, ha az identitásadatok szinkronizálása a Windows Server Active Directory (AD DS) és az Azure Active Directory (Azure AD). Ez a cikk áttekintést nyújt a különböző típusú szinkronizálási hibákról, néhány lehetséges forgatókönyvről, amelyek ezeket a hibákat okozzák, és a hibák lehetséges kijavításának lehetséges módjait. Ez a cikk a gyakori hibatípusokat tartalmazza, és nem feltétlenül fedi le az összes lehetséges hibát.

 Ez a cikk feltételezi, hogy az olvasó ismeri az Azure AD és az [Azure AD Connect alaptervezési fogalmait.](plan-connect-design-concepts.md)

Az Azure AD Connect \(2016 augusztusi\)vagy újabb verziójával a szinkronizálási hibákról szóló jelentés érhető el az [Azure Portalon](https://aka.ms/aadconnecthealth) az Azure AD Connect Health szinkronizálásra című részeként.

2016. szeptember 1-től kezdődően az [Azure Active Directory duplikált attribútumrugalmassági](how-to-connect-syncservice-duplicate-attribute-resiliency.md) funkciója alapértelmezés szerint engedélyezve lesz az összes *új* Azure Active Directory-bérlőnél. Ez a funkció automatikusan engedélyezve lesz a meglévő bérlők számára a következő hónapokban.

Az Azure AD Connect háromféle műveletet hajt végre a szinkronban lévő könyvtárakból: importálás, szinkronizálás és exportálás. Hibák történhetnek az összes műveletben. Ez a cikk elsősorban az Azure AD-be exportálás során elkövetett hibákra összpontosít.

## <a name="errors-during-export-to-azure-ad"></a>Hibák az Azure AD-be való exportálás során
A következő szakasz ismerteti a szinkronizálási hibák különböző típusait, amelyek az Azure AD-összekötő használatával az Azure AD-be történő exportálássorán fordulhatnak elő. Ez az összekötő a "contoso" névformátummal azonosítható. *onmicrosoft.com*".
Az Azure AD-be való \(exportálás során elkövetett hibák\) azt jelzik, \(hogy\) az Azure AD Connect Sync Engine által az Azure Active Directoryn megkísérelt művelet hozzáadása, frissítése, törlése stb.

![Exportálási hibák – áttekintés](./media/tshoot-connect-sync-errors/Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Adateltérési hibák
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Leírás
* Amikor az Azure \(AD Connect szinkronizálási motor\) utasítja az Azure Active Directoryt objektumok hozzáadása vagy frissítése, az Azure AD egyezteti a bejövő objektum a **sourceAnchor** attribútum használatával az objektumok **nem módosíthatóIazonosító** attribútum az Azure AD-ben. Ez a mérkőzés az úgynevezett **Hard Match**.
* Ha az Azure AD **nem talál** olyan objektumot, amely megfelel a **nem módosíthatóI attribútumnak** a bejövő objektum **sourceAnchor** attribútumával, mielőtt új objektumot létesítene, visszaesik a ProxyAddresses és a UserPrincipalName attribútumok használatára az egyezés kereséséhez. Ezt a mérkőzést **soft match-nek nevezzük.** A soft match úgy lett kialakítva, hogy az Azure AD-ben már meglévő (az Azure AD-ben beszerzett) objektumokat egyeztetve az új objektumok at a szinkronizálás során, amelyek ugyanazt az entitást (felhasználókat, csoportokat) képviselik a helyszínen.
* **Az InvalidSoftMatch** hiba akkor fordul elő, ha a nem megfelelő objektum nem talál egyező **objektumot, és** a soft match megkeresi a megfelelő objektumot, de az objektum értéke eltér a bejövő objektum *SourceAnchorazonosítójától,* ami arra utal, hogy az egyező objektum szinkronizálva lett egy másik objektummal a helyszíni Active Directoryból. *immutableId*

Más szóval, annak érdekében, hogy a puha mérkőzés működjön, az objektum, hogy puha illeszkedik nem kell semmilyen értéket a *nemmódosíthatóiId*. Ha egy értékkel *rendelkező, nem módosítható azonosítóval* rendelkező objektum nem felel meg a nem megfelelő, de megfelel a soft-match feltételeknek, a művelet InvalidSoftMatch szinkronizálási hibát eredményezne.

Az Azure Active Directory-séma nem teszi lehetővé, hogy két vagy több objektum azonos értékű a következő attribútumok. \(Ez nem egy kimerítő lista.\)

* ProxyAddresses
* UserPrincipalName
* onPremisesSecurityIdentifier
* ObjectId

> [!NOTE]
> [Az Azure AD attribútumdauplikált attribútum rugalmassági](how-to-connect-syncservice-duplicate-attribute-resiliency.md) szolgáltatás is bevezetésre kerül az Azure Active Directory alapértelmezett viselkedéseként.  Ez csökkenti az Azure AD Connect (valamint más szinkronizálási ügyfelek) által látott szinkronizálási hibák számát azáltal, hogy az Azure AD-t rugalmasabbá teszi a helyszíni AD-környezetekben található duplikált ProxyAddresses és UserPrincipalName attribútumok kezelésében. Ez a szolgáltatás nem javítja ki a párhuzamos hibákat. Tehát az adatokat még rögzíteni kell. De lehetővé teszi az új objektumok kiépítését, amelyek egyébként le vannak tiltva az Azure AD duplikált értékek miatt. Ez csökkenti a szinkronizálási ügyfélnek visszaadott szinkronizálási hibák számát is.
> Ha ez a funkció engedélyezve van a bérlő, nem fogja látni az InvalidSoftMatch szinkronizálási hibák során látható kiépítése során az új objektumok.
>
>

#### <a name="example-scenarios-for-invalidsoftmatch"></a>Példa az InvalidSoftMatch forgatókönyveire
1. A helyszíni Active Directoryban két vagy több azonos értékű objektum található a ProxyAddresses attribútumhoz. Csak egy van szerzés kiépítve az Azure AD-ben.
2. Két vagy több azonos értékű objektum létezik a helyszíni Active Directoryban, amelynek értéke megegyezik a userPrincipalName attribútummal. Csak egy van szerzés kiépítve az Azure AD-ben.
3. Egy objektum lett hozzáadva a helyszíni Active Directoryban, amelynek értéke megegyezik a ProxyAddresses attribútumértékkel, mint az Azure Active Directoryban lévő meglévő objektum. A helyszíni hozzáadott objektum nem érhető el az Azure Active Directoryban.
4. Egy objektum került a helyszíni Active Directory ba, amelynek értéke userPrincipalName attribútum, mint az Azure Active Directoryban lévő fiók. Az objektum nem érhető el az Azure Active Directoryban.
5. Egy szinkronizált fiók lett áthelyezve az A erdőből a B erdőbe. Az erdő áthelyezése után a SourceAnchor értéke eltérő. Az új objektum (a B erdőből) nem sikerül szinkronizálni a meglévő objektum az Azure AD-ben.
6. Egy szinkronizált objektum véletlenül törlődött a helyszíni Active Directoryból, és egy új objektum jött létre az Active Directoryban ugyanahhoz az entitáshoz (például a felhasználóhoz) anélkül, hogy törölte volna a fiókot az Azure Active Directoryban. Az új fiók nem szinkronizálható a meglévő Azure AD-objektummal.
7. Az Azure AD Connect et eltávolították és újratelepítették. Az újratelepítés során egy másik attribútum lett kiválasztva SourceAnchor. A korábban szinkronizált összes objektum leállt az InvalidSoftMatch hibával való szinkronizálással.

#### <a name="example-case"></a>Példa eset:
1. **Bob Smith** egy szinkronizált felhasználó az Azure Active Directoryban az Active Directory *contoso.com*
2. Bob Smith **UserPrincipalName** van beállítva, mint **bobcontoso.com\@**.
3. **Az "abcdefghijklmnopqrstuv=="** az Azure AD Connect által bob Smith **objectGUID** használatával a helyszíni Active Directoryból számított **SourceAnchor,** amely Bob Smith **nem módosítható azonosítója** az Azure Active Directoryban.
4. Bob a következő értékekkel is rendelkezik a **proxyAddresses** attribútumhoz:
   * Smtp:bobs@contoso.com
   * Smtp:bob.smith@contoso.com
   * **smtp:\@bob contoso.com**
5. Egy új felhasználó, **Bob Taylor**, hozzáadódik a helyszíni Active Directoryhoz.
6. Bob Taylor **UserPrincipalName** van beállítva, mint **\@bobt contoso.com**.
7. **"abcdefghijkl0123456789==""** az Azure AD Connect által bob Taylor **objectGUID** használatával a helyszíni Active Directory ból számított **sourceAnchor.** Bob Taylor objektuma még nem lett szinkronizálva az Azure Active Directoryval.
8. Bob Taylor a következő értékekkel rendelkezik a proxyAddresses attribútumhoz
   * Smtp:bobt@contoso.com
   * Smtp:bob.taylor@contoso.com
   * **smtp:\@bob contoso.com**
9. A szinkronizálás során az Azure AD Connect felismeri Bob Taylor hozzáadását a helyszíni Active Directoryban, és megkéri az Azure AD-t, hogy ugyanezt a módosítást hajtsa elő.
10. Az Azure AD először végre kemény egyezést. Ez azt, hogy meg fogja keresni, ha van olyan objektum, amelynek immutableId egyenlő "abcdefghijkl0123456789=". Hard Match sikertelen lesz, mivel az Azure AD egyetlen más objektum a nem módosíthatóIazonosítód.
11. Az Azure AD ezután megpróbálja soft-match Bob Taylor. Ez azt, hogy a keresés, ha van olyan objektum proxyAddresses egyenlő a három értéket, beleértve a smtp:bob@contoso.com
12. Az Azure AD megtalálja Bob Smith objektumát, hogy megfeleljen a soft-match feltételeknek. De ez az objektum értéke immutableId = "abcdefghijklmnopqrstuv==". ami azt jelzi, hogy az objektum egy másik objektumból lett szinkronizálva a helyszíni Active Directoryból. Így az Azure AD nem tudja soft-match ezeket az objektumokat, és az eredmények egy **InvalidSoftMatch** szinkronizálási hiba.

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Az InvalidSoftMatch hiba javítása
Az InvalidSoftMatch hiba leggyakoribb oka két, különböző \(SourceAnchor immutableId\) azonosítóval rendelkező objektum azonos értékkel rendelkezik a ProxyAddresses és/vagy a UserPrincipalName attribútumok esetében, amelyeket az Azure AD soft-match folyamata során használnak. Az érvénytelen soft match javítása érdekében

1. Azonosítsa a hibát okozó duplikált proxycímeket, userPrincipalName vagy más attribútumértéket. Azonosítsa \(azt\) is, hogy melyik két vagy több objektum érintett a konfliktusban. Az [Azure AD Connect Health](https://aka.ms/aadchsyncerrors) for sync által létrehozott jelentés segítségével azonosíthatja a két objektumot.
2. Azonosítsa, hogy melyik objektumnak kell továbbra is rendelkeznie a duplikált értékkel, és melyik objektumnak nem szabad.
3. Távolítsa el a duplikált értéket abból az objektumból, amelynek NEM kellene rendelkeznie ezzel az értékkel. A módosítást abban a könyvtárban kell elkészíteni, ahonnan az objektum származik. Bizonyos esetekben előfordulhat, hogy törölnie kell az ütközésben lévő objektumok egyikét.
4. Ha a helyszíni AD-ben hajtotta végre a módosítást, hagyja, hogy az Azure AD Connect szinkronizálja a módosítást.

Az Azure AD Connect Health szinkronizálási hibajelentései 30 percenként frissülnek, és tartalmazzák a legutóbbi szinkronizálási kísérlet hibáit.

> [!NOTE]
> Az ImmutableId definíció szerint nem változhat az objektum élettartamában. Ha az Azure AD Connect nem volt konfigurálva a fenti listából néhány forgatókönyvet szem előtt tartva, előfordulhat, hogy olyan helyzetbe kerül, amelyben az Azure AD Connect kiszámítja a SourceAnchor egy másik értékét az AD-objektumhoz, amely ugyanazt az entitást (ugyanazt a felhasználót/csoportot/kapcsolattartót stb.) jelöli, amely rendelkezik egy meglévő Azure AD-objektumhasználatával.
>
>

#### <a name="related-articles"></a>Kapcsolódó cikkek
* [Ismétlődő vagy érvénytelen attribútumok megakadályozzák a címtár-szinkronizálást az Office 365-ben](https://support.microsoft.com/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Leírás
Amikor az Azure AD megpróbálja a két objektum lágy megfeleltetni, lehetséges, hogy két különböző "objektum típusú" objektum (például felhasználó, csoport, kapcsolattartó stb.) azonos értékekkel rendelkezik a soft match végrehajtásához használt attribútumok. Mivel ezek az attribútumok megkettőzése nem engedélyezett az Azure AD-ben, a művelet "ObjectTypeMismatch" szinkronizálási hibát eredményezhet.

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Példa az ObjectTypeMismatch hiba esetére
* Az Office 365-ben létrejön egy levelezésre engedélyezett biztonsági csoport. Rendszergazda új felhasználót vagy kapcsolattartót ad hozzá a helyszíni AD-hez (amely még nincs szinkronizálva az Azure AD-vel) ugyanazzal az értékkel, mint az Office 365-csoport.

#### <a name="example-case"></a>Példa eset
1. A Rendszergazda új levelezési biztonsági csoportot hoz létre az Office 365-ben az adóhivatal számára, és e-mail címet ad meg a rendszerhez. tax@contoso.com Ez a csoport az **smtp:\@tax contoso.com** ProxyAddresses attribútumértékéhez van rendelve.
2. Egy új felhasználó csatlakozik Contoso.com és egy fiókot hoz létre a felhasználó számára a helyszínen a proxyAddress as **smtp:\@tax contoso.com**
3. Amikor az Azure AD Connect szinkronizálja az új felhasználói fiókot, megjelenik az "ObjectTypeMismatch" hibaüzenet.

#### <a name="how-to-fix-objecttypemismatch-error"></a>Az ObjectTypeMismatch hiba javítása
Az ObjectTypeMismatch hiba leggyakoribb oka két különböző típusú objektum (Felhasználó, Csoport, Kapcsolattartó stb.) azonos értékkel rendelkezik a ProxyAddresses attribútumhoz. Az ObjectTypeMismatch javításához:

1. Azonosítsa a hibát okozó duplikált proxycímek (vagy más attribútum) értéket. Azonosítsa \(azt\) is, hogy melyik két vagy több objektum érintett a konfliktusban. Az [Azure AD Connect Health](https://aka.ms/aadchsyncerrors) for sync által létrehozott jelentés segítségével azonosíthatja a két objektumot.
2. Azonosítsa, hogy melyik objektumnak kell továbbra is rendelkeznie a duplikált értékkel, és melyik objektumnak nem szabad.
3. Távolítsa el a duplikált értéket abból az objektumból, amelynek NEM kellene rendelkeznie ezzel az értékkel. Ne feledje, hogy a módosítást abban a könyvtárban kell elkészíteni, ahonnan az objektum származik. Bizonyos esetekben előfordulhat, hogy törölnie kell az ütközésben lévő objektumok egyikét.
4. Ha a helyszíni AD-ben hajtotta végre a módosítást, hagyja, hogy az Azure AD Connect szinkronizálja a módosítást. Az Azure AD Connect Health szinkronizálási hibajelentés30 percenként frissül, és tartalmazza a legutóbbi szinkronizálási kísérlet hibáit.

## <a name="duplicate-attributes"></a>Ismétlődő attribútumok
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Leírás
Az Azure Active Directory-séma nem teszi lehetővé, hogy két vagy több objektum azonos értékű a következő attribútumok. Ez minden egyes objektum az Azure AD-ben kénytelen egy adott példányban ezek az attribútumok egyedi értéke.

* ProxyAddresses
* UserPrincipalName

Ha az Azure AD Connect megpróbál hozzáadni egy új objektumot, vagy frissíteni egy meglévő objektumot a fenti attribútumok, amelyek már hozzá van rendelve egy másik objektum az Azure Active Directoryban, a művelet eredménye az "AttributeValueMustBeUnique" szinkronizálási hiba.

#### <a name="possible-scenarios"></a>Lehetséges forgatókönyvek:
1. Az ismétlődő érték egy már szinkronizált objektumhoz van rendelve, amely ütközik egy másik szinkronizált objektummal.

#### <a name="example-case"></a>Példa eset:
1. **Bob Smith** egy szinkronizált felhasználó az Azure Active Directoryban az Active Directory contoso.com
2. Bob Smith **UserPrincipalName** a helyszínen van beállítva, mint **\@bobs contoso.com**.
3. Bob a következő értékekkel is rendelkezik a **proxyAddresses** attribútumhoz:
   * Smtp:bobs@contoso.com
   * Smtp:bob.smith@contoso.com
   * **smtp:\@bob contoso.com**
4. Egy új felhasználó, **Bob Taylor**, hozzáadódik a helyszíni Active Directoryhoz.
5. Bob Taylor **UserPrincipalName** van beállítva, mint **\@bobt contoso.com**.
6. **Bob Taylor** a következő értékekkel rendelkezik az i **ProxyAddresses** attribútumhoz. smtp: bobt@contoso.com ii. Smtp:bob.taylor@contoso.com
7. Bob Taylor objektuma sikeresen szinkronizálva van az Azure AD-vel.
8. Admin úgy döntött, hogy frissítse Bob Taylor **ProxyAddresses** attribútum a következő érték: i. **smtp:\@bob contoso.com**
9. Az Azure AD megpróbálja frissíteni Bob Taylor objektumát az Azure AD-ben a fenti értékkel, de ez a művelet sikertelen lesz, mivel a ProxyAddresses érték már hozzá van rendelve Bob Smith- hez, ami "AttributeValueMustBeUnique" hibát eredményez.

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Az AntributeValueMustBeUnique hiba javítása
Az AttributeValueMustBeUnique hiba leggyakoribb oka két különböző \(SourceAnchor immutableId\) azonosítóval rendelkező objektum azonos értékkel rendelkezik a ProxyAddresses és/vagy a UserPrincipalName attribútumok esetében. Az AttributeValueMustBeUnique hiba javítása érdekében

1. Azonosítsa a hibát okozó duplikált proxycímeket, userPrincipalName vagy más attribútumértéket. Azonosítsa \(azt\) is, hogy melyik két vagy több objektum érintett a konfliktusban. Az [Azure AD Connect Health](https://aka.ms/aadchsyncerrors) for sync által létrehozott jelentés segítségével azonosíthatja a két objektumot.
2. Azonosítsa, hogy melyik objektumnak kell továbbra is rendelkeznie a duplikált értékkel, és melyik objektumnak nem szabad.
3. Távolítsa el a duplikált értéket abból az objektumból, amelynek NEM kellene rendelkeznie ezzel az értékkel. Ne feledje, hogy a módosítást abban a könyvtárban kell elkészíteni, ahonnan az objektum származik. Bizonyos esetekben előfordulhat, hogy törölnie kell az ütközésben lévő objektumok egyikét.
4. Ha a helyszíni AD-ben hajtotta végre a módosítást, hagyja, hogy az Azure AD Connect szinkronizálja a módosítást a hiba kijavításához.

#### <a name="related-articles"></a>Kapcsolódó cikkek
-[Ismétlődő vagy érvénytelen attribútumok megakadályozzák a címtár-szinkronizálást az Office 365-ben](https://support.microsoft.com/kb/2647098)

## <a name="data-validation-failures"></a>Adatérvényesítési hibák
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Leírás
Az Azure Active Directory különböző korlátozásokat kényszerít ki magára az adatokra, mielőtt lehetővé teszi az adatok címtárba írását. Ezek a korlátozások biztosítják, hogy a végfelhasználók a lehető legjobb élményben legyen részeaz adatoktól függő alkalmazások használata során.

#### <a name="scenarios"></a>Forgatókönyvek
a. A UserPrincipalName attribútum értéke érvénytelen/nem támogatott karaktereket tartalmaz.
b. A UserPrincipalName attribútum nem követi a szükséges formátumot.

#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Az IdentityDataValidationsikertelen hiba javítása
a. Győződjön meg arról, hogy a userPrincipalName attribútum támogatott karakterekkel és szükséges formátummal rendelkezik.

#### <a name="related-articles"></a>Kapcsolódó cikkek
* [Felkészülés a felhasználók nak az Office 365-tel való címtár-szinkronizáláson keresztültörténő kiépítésére](https://support.office.com/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)

### <a name="federateddomainchangeerror"></a>Összevont DomainChangeError
#### <a name="description"></a>Leírás
Ebben az esetben **a "FederatedDomainChangeError"** szinkronizálási hiba lép, amikor a felhasználó UserPrincipalName utótagja egy összevont tartományról egy másik összevont tartományra változik.

#### <a name="scenarios"></a>Forgatókönyvek
Szinkronizált felhasználó esetén a UserPrincipalName utótag egy összevont tartományról egy másik összevont tartományra lett módosítva a helyszínen. Például *UserPrincipalName =\@bob contoso.com* *userPrincipalname =\@bob fabrikam.com*.

#### <a name="example"></a>Példa
1. Bob Smith, a Contoso.com fiókja új felhasználóként kerül hozzáadásra az Active Directoryban a UserPrincipalName névvelbob@contoso.com
2. Bob költözik egy másik részlege Contoso.com nevű Fabrikam.com és userPrincipalname változikbob@fabrikam.com
3. Mind contoso.com, mind fabrikam.com tartományok összevont tartományok az Azure Active Directoryval.
4. Bob userPrincipalName nem kap frissül, és az eredmények a "FederatedDomainChangeError" szinkronizálási hiba.

#### <a name="how-to-fix"></a>A hiba kijavítása
Ha egy felhasználó UserPrincipalName utótagja bob@**contoso.com-ról** bob\@**fabrikam.com**lett frissítve, ahol **mind contoso.com,** mind **fabrikam.com** **összevont tartományban**vannak, akkor kövesse az alábbi lépéseket a szinkronizálási hiba kijavításához.

1. Frissítse a felhasználó UserPrincipalName az Azure bob@contoso.com bob@contoso.onmicrosoft.comAD-ben a ból. A következő PowerShell-parancs az Azure AD PowerShell-modullal használható:`Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`
2. Hagyja, hogy a következő szinkronizálási ciklus megkísérelje a szinkronizálást. Ez az idő szinkronizálás sikeres lesz, és frissíti a UserPrincipalName bob a bob@fabrikam.com várt módon.

#### <a name="related-articles"></a>Kapcsolódó cikkek
* [Az Azure Active Directory-szinkronizálási eszköz nem szinkronizálja a módosításokat, miután módosította egy felhasználói fiók felhasználói felületét egy másik összevont tartomány használatára.](https://support.microsoft.com/help/2669550/changes-aren-t-synced-by-the-azure-active-directory-sync-tool-after-you-change-the-upn-of-a-user-account-to-use-a-different-federated-domain)

## <a name="largeobject"></a>Nagyobjektum
### <a name="description"></a>Leírás
Ha egy attribútum meghaladja az Azure Active Directory-séma által beállított megengedett méretkorlátot, hosszkorlátot vagy count limitet, a szinkronizálási művelet a **LargeObject** vagy **exceededAllowedLength** szinkronizálási hibát eredményezi. Ez a hiba általában a következő attribútumok esetén fordul elő

* userCertificate
* userSMIMECertificate
* thumbnailPhoto
* proxyAddresses

### <a name="possible-scenarios"></a>Lehetséges forgatókönyvek
1. Bob userCertificate attribútuma túl sok, A Bobhoz rendelt tanúsítványt tárol. Ezek közé tartozhatnak a régebbi, lejárt tanúsítványok. A korlát 15 tanúsítvány. A LargeObject hibák userCertificate attribútummal történő kezeléséről a [UserCertificate attribútum által okozott LargeObject-hibák kezelése](tshoot-connect-largeobjecterror-usercertificate.md)című cikkben olvashat bővebben.
2. Bob userSMIMECertificate attribútuma túl sok, A Bobhoz rendelt tanúsítványt tárol. Ezek közé tartozhatnak a régebbi, lejárt tanúsítványok. A korlát 15 tanúsítvány.
3. Bob thumbnailPhoto készlet az Active Directoryban túl nagy ahhoz, hogy szinkronizálható az Azure AD.Bob's thumbnailPhoto set in Active Directory is too large to be synced in Azure AD.
4. Az Active Directory proxycímek attribútumának automatikus sokasága során egy objektumhoz túl sok ProxyAddresses van hozzárendelve.

### <a name="how-to-fix"></a>A hiba kijavítása
1. Győződjön meg arról, hogy a hibát okozó attribútum a megengedett korlátozáson belül van.

## <a name="existing-admin-role-conflict"></a>Rendszergazdai szerepkör-ütközés

### <a name="description"></a>Leírás
A szinkronizálás során egy felhasználói objektumon **meglévő rendszergazdai szerepkörütközés** lép fel, ha a felhasználói objektum a következőkkel rendelkezik:

- rendszergazdai engedélyek és
- ugyanaz a UserPrincipalName, mint egy meglévő Azure AD-objektum

Az Azure AD Connect nem engedélyezett a helyszíni AD-ből származó felhasználói objektum megfelelővé egyeztetése az Azure AD-ben, amelyhez felügyeleti szerepkör van rendelve.  További információ: [Azure AD UserPrincipalName population](plan-connect-userprincipalname.md)

![Meglévő rendszergazda](media/tshoot-connect-sync-errors/existingadmin.png)


### <a name="how-to-fix"></a>A hiba kijavítása
A probléma megoldásához tegye a következőket:

1. Távolítsa el az Azure AD-fiókot (tulajdonost) az összes rendszergazdai szerepkörből. 
2. **A** karanténba helyezett objektum törlése a felhőben. 
3. A következő szinkronizálási ciklus gondoskodik a helyszíni felhasználó és a felhőalapú fiók lágy megfeleltetéséről (mivel a felhőalapú felhasználó már nem globális ga).The next sync cycle will care of soft-matching the on-premises user to the cloud account (since the cloud user is now no a global GA). 
4. Állítsa vissza a tulajdonos szerepkör-tagságát. 

>[!NOTE]
>A felügyeleti szerepkört a meglévő felhasználói objektum hoz újra, miután a helyszíni felhasználói objektum és az Azure AD felhasználói objektum befejeződött.

## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Active Directory-objektumok megkeresése az Active Directory felügyeleti központban](https://technet.microsoft.com/library/dd560661.aspx)
* [Az Azure Active Directory lekérdezése egy objektumhoz az Azure Active Directory PowerShell használatával](https://msdn.microsoft.com/library/azure/jj151815.aspx)
