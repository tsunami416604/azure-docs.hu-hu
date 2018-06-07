---
title: 'Az Azure AD Connect: Understanding deklaratív kiépítés |} Microsoft Docs'
description: A deklaratív üzembe helyezési modell az Azure AD Connectben ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: bb6a0c16322884afba3d306c491c3cd592fc8595
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593191"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Azure AD Connect szinkronizálása: Understanding deklaratív kiépítés
Ez a témakör ismerteti az Azure AD Connect konfigurációs modell. A modell deklaratív kiépítés nevezik, és lehetővé teszi olyan konfigurációs módosítást alkalmazásaiba. A jelen témakörben ismertetett számos elemet speciális, és a legtöbb ügyfél forgatókönyvhöz nem szükséges.

## <a name="overview"></a>Áttekintés
Deklaratív kiépítés csatlakoztatott forráskönyvtárat érkező objektumok feldolgozása folyamatban van, és határozza meg, hogyan az objektum és attribútumok kell kell alakítani egy forrásból egy olyan cél számára. Az objektum feldolgozása a szinkronizálási folyamat, és a folyamat megegyezik a bejövő és kimenő szabályokat. Egy bejövő forgalomra vonatkozó szabály a kapcsolódási térbe a számára a metaverzumba és egy kimenő forgalomra vonatkozó szabály származik a metaverzumba összekötő területre.

![Szinkronizálási folyamat](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/sync1.png)  

A folyamat számos különböző modul van. Minden egyes felelős szinkronizációs egy fogalom.

![Szinkronizálási folyamat](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/pipeline.png)  

* Forrás, az adatforrás-objektum
* [Hatókör](#scope), megkeresi az összes szinkronizálási szabály hatókörébe
* [Csatlakozás](#join), határozza meg a kapcsolódási térbe és metaverzum közötti kapcsolat
* [Átalakítás](#transform), számítja ki, hogyan kell alakítani attribútumok és folyamata
* [Sorrend](#precedence), oldja fel a rendszer ütköző attribútum hozzájárulások
* Cél, a célobjektum

## <a name="scope"></a>Hatókör
A hatókör modul objektum értékeli, és határozza meg a szabályokat, amelyek hatókörébe, és fel kell venni a feldolgozása. Attól függően, hogy az attribútumok értékeket az objektumon más szinkronizálási szabályok kiértékelése hatókörben kell. A letiltott felhasználó nem Exchange postaládával például különböző szabályok, mint az engedélyezett felhasználók egy postaládával rendelkezik.  
![Hatókör](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope1.png)  

A hatókört csoportok és a kikötéseket típusúként van definiálva. A feltételek alkalmazása a csoporton belül. A logikai és a csoport összes záradékok közötti szolgál. Például (részleg informatikai és ország = = Dánia). Logikai vagy csoportok közötti szolgál.

![Hatókör](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/scope2.png)  
A hatókör képen értelmezhető (részleg informatikai és ország = = Dánia) vagy (ország Svédország =). Ha 1 vagy csoport 2 true, akkor a szabály hatókörében van.

A hatókör modul támogatja a következő műveleteket.

| Művelet | Leírás |
| --- | --- |
| EGYENLŐ, NOTEQUAL |Egy karakterlánc-összehasonlítási, amely kiértékeli, ha az érték a attribútum értéke megegyezik. Többértékű attribútumok ISIN és ISNOTIN témakörben talál. |
| LESSTHAN, LESSTHAN_OR_EQUAL |Egy karakterlánc-összehasonlítási, amely kiértékeli, ha az érték kevesebb az attribútum értéke. |
| TARTALMAZZA, NOTCONTAINS |Egy karakterlánc-összehasonlítási, amely kiértékeli, ha az érték megtalálható valahol belüli értéket az attribútum. |
| MEGADOTT MÓDON KEZDŐDŐ, NOTSTARTSWITH |Egy karakterlánc-összehasonlítási, amely kiértékeli, ha az attribútum értékének elejére értéke. |
| MEGADOTT MÓDON VÉGZŐDŐ, NOTENDSWITH |Egy karakterlánc hasonlítsa össze, amely kiértékeli, ha az attribútum értéke végén értéke. |
| GREATERTHAN, GREATERTHAN_OR_EQUAL |Egy karakterlánc hasonlítsa össze, amely kiértékeli, ha az érték nagyobb, mint az attribútum értéke. |
| ISNULL, ISNOTNULL |Kiértékeli, ha az attribútum hiányzik az objektum. Ha az attribútum nem létezik, és ezért null, a szabály hatóköre van. |
| ISIN, ISNOTIN |Kiértékeli a megadott attribútum szerepel érték esetén. Ez a művelet EGYENLŐSÉGI és NOTEQUAL többértékű változata esetében. Az attribútum kellene lennie a többértékű attribútummal, és ha az érték szerepel az attribútumértékek egyik található, majd a szabály az hatókörén. |
| ISBITSET, ISNOTBITSET |Értékeli ki, ha egy adott bit be van állítva. Például használható userAccountControl megjelenítéséhez, ha a felhasználó engedélyezve van-e a bits kiértékeléséhez. |
| ISMEMBEROF, ISNOTMEMBEROF |Az érték a megkülönböztető név a kapcsolódási térbe csoporthoz kell tartalmaznia. Az objektum a megadott csoport tagja, a szabály akkor hatókörében. |

## <a name="join"></a>Csatlakozás
A szinkronizálási folyamat az illesztés modul felelős az objektum a forrás- és objektum közötti kapcsolat keresése a célkiszolgálón. Egy bejövő forgalomra vonatkozó szabály, a ezt a kapcsolatot a kapcsolódási térbe objektum kapcsolattal keresés a metaverzumban objektum lehet.  
![Csatlakozás cs és mv között](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join1.png)  
A cél, hogy tekintse meg, ha egy objektum már szerepel a metaverzumba, egy másik összekötő által létrehozott, érdemes lehet társítva. Például egy fiók-erőforrás erdőben a felhasználót a fiókerdő keresztül illeszthető a felhasználó az erőforráserdőből.

Illesztések összekötő terület objektumok együtt csatlakoztatása a azonos metaverzum-objektum használt legtöbbször a bejövő szabályok.

Illesztést is meg van adva egy vagy több csoportjára. Egy csoporton belüli záradékok rendelkezik. A logikai és a csoport összes záradékok közötti szolgál. Logikai vagy csoportok közötti szolgál. A csoportok feldolgozása sorrendben fentről lefelé történik. Egy csoport található objektum pontosan egy megfelelő megtalálta a célkiszolgálón, ha nincs más illesztési szabály kell kiértékelni. Ha nulla vagy egynél több objektum található, a következő szabálycsoport feldolgozási továbbra is. Emiatt a szabályok sorrendjében legtöbb kifejezett első létrehozott és több intelligens végén kell lennie.  
![Csatlakozás meghatározása](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join2.png)  
Ez a kép illesztéseket feldolgozott alsó vannak. Először a szinkronizálási folyamat látja-e az employeeID egyezés. Ha nem, a második szabály látja, ha a fiók neve használható együtt az objektumok csatlakozni. Amely nem egyező vagy, a harmadik és végső szabály akkor több egyeztetésű felhasználó neve.

Ha illesztés az összes szabály értékelése, és nem pontosan egy egyezik, a **kapcsolattípus** a a **leírás** lap szolgál. Ha ez a beállítás értéke **rendelkezés**, majd egy új objektum a célzott jön létre.  
![Kiépítés, vagy csatlakozzon](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/join3.png)  

Objektum csak kell rendelkeznie az illesztés szabállyal egy egyetlen szinkronizálási szabály hatókörében. Ha több szinkronizálási szabály ahol illesztés van definiálva, hiba történik. Sorrend illesztési ütközések feloldása nem használatos. Objektum rendelkeznie kell egy illesztési szabály attribútumok ugyanabban az bejövő/kimenő irányban-adatfolyam hatókörébe. Ha kell folyamata attribútumok bejövő és kimenő ugyanazon az objektumon, rendelkeznie kell egy bejövő és egy illesztési kimenő szinkronizálási szabály.

Kimenő illesztés van egy különleges viselkedését, egy objektumot a célként megadott kapcsolódási térbe kiépítése során. Először próbálja meg a névkeresési csatlakozzon a megkülönböztető név attribútum szolgál. Ha már van egy objektumot a célként megadott kapcsolódási térbe az azonos megkülönböztető névvel rendelkező, az objektumok kapcsolódnak-e.

Az illesztési modul csak akkor értékeli ki, amikor egy új szinkronizálási szabály ismét elérhető, a hatókör. Ha csatlakozott egy objektumot, a rendszer nem disjoining akkor is, ha az illesztési feltétel nem teljesül. Ha egy objektum disjoin, be kell lépnie a szinkronizálási szabály, amely az objektumok csatlakoztatott hatókörén kívül.

### <a name="metaverse-delete"></a>Metaverse törlése
A metaverzum-objektum marad, amíg nincs egy szinkronizálási szabály hatóköre, **kapcsolattípus** beállítása **rendelkezés** vagy **StickyJoin**. Egy StickyJoin használatos, ha az összekötő nem engedélyezett új objektum felvételére a metaverzumba kiépítéséhez, azonban csatlakoztatva van, akkor törölni kell a forrás a metaverzum-objektum törlése előtt.

A törölt metaverzum-objektum egy kimenő szinkronizálási szabályt társított összes objektumot megjelölve **rendelkezés** törlési vannak megjelölve.

## <a name="transformations"></a>Átalakítás
Az átalakítás segítségével határozza meg, hogyan attribútumok kell flow-bejegyzéseket a cél. A forgalom a következők egyike lehet **típusok flow**: közvetlen, állandó vagy kifejezés. A közvetlen folyamat zajlik egy attribútum értékét,-átalakítás nélküli további van. Konstans érték megadása esetén a megadott érték. Egy kifejezés Express, hogyan kell lennie az átalakítás deklaratív létesítési kifejezés nyelvét használja. A kifejezés nyelven részletek megtalálhatók a [deklaratív létesítési kifejezés nyelvi ismertetése](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) témakör.

![Kiépítés, vagy csatlakozzon](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/transformations1.png)  

A **egyszer alkalmazása** jelölőnégyzet határozza meg, hogy az attribútum csak állítható be, amikor az objektum először hozza létre. Ez a konfiguráció például egy új felhasználói objektum egy kezdeti jelszó beállításához használható.

### <a name="merging-attribute-values"></a>Az egyesítés attribútumértékek
Az attribútumfolyamok van egy beállítást, ha többértékű attribútumok össze kell vonni az számos különböző összekötők meghatározásához. Az alapértelmezett érték **frissítés**, ami azt jelenti, hogy a legmagasabb prioritású szinkronizálási szabály win kell.

![Egyesítési típusok](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/mergetype.png)  

Is **egyesítése** és **MergeCaseInsensitive**. Ezek a beállítások lehetővé teszik, hogy a különböző forrásokból származó értékek. Például használhatná a tag vagy a proxyAddresses attribútum számos különböző erdőkből származó egyesíteni. Ha ezt a beállítást használja, hatókörébe az objektum összes szinkronizálási szabály egyesítési ugyanolyan kell használnia. Nem adhat meg **frissítés** a egy összekötőt és **egyesítése** másik. Ha megpróbál, hibaüzenet.

A különbség a között **egyesítése** és **MergeCaseInsensitive** megtudhatja, hogyan feldolgozhat duplikált attribútum. A szinkronizálási motor biztosítja, hogy az ismétlődő értékek nem kerülnek a target attribútummal. A **MergeCaseInsensitive**, ismétlődő értékek csak különbség, abban az esetben, ha nem tervezi, hogy jelen lennie. Például nem kell megjelennie mindkét "SMTP:bob@contoso.com"és"smtp:bob@contoso.com" a célattribútum. **Egyesítési** csak megnézi a pontos és több értékek esetén csak a különbségek eset jelen lehet.

A beállítás **cserélje le** ugyanaz, mint **frissítés**, de semmi nem használja.

### <a name="control-the-attribute-flow-process"></a>Az attribútum folyamata folyamatot vezérlő
Ha több bejövő szinkronizálási szabály ugyanazt a metaverzum-attribútum hozzájárulás vannak beállítva, sorrend szolgál a győztes határozza meg. A szinkronizálási szabály legmagasabb prioritású (legkisebb numerikus értéket) közre értéke lesz. Ugyanez történik, a kimenő szabályok. A szinkronizálás a legmagasabb sorrend wins-szabályok, és hozzájárul az értéket a csatlakoztatott címtárhoz.

Bizonyos esetekben helyett közre értéket, a szinkronizálási szabályt kell meghatározni, más szabályok működése kell. Nincsenek ebben az esetben használt néhány speciális literálok.

A bejövő szinkronizálási szabályok, a literális **NULL** annak jelzésére, hogy a folyamat nem rendelkezik értékkel közre is használható. Egy másik szabály alacsonyabb fontossági sorrendű értéket vehet részt. Ha nincs szabály része volt egy értéket, a metaverzum-attribútum eltávolítása. Az egy kimenő forgalomra vonatkozó szabály Ha **NULL** érték a végső szinkronizálási szabályok feldolgozása után, akkor a rendszer eltávolítja az értéket a csatlakoztatott könyvtárban.

A szövegkonstans **AuthoritativeNull** hasonló **NULL** , de a különbség az, hogy nincs alsó végrehajtására vonatkozó elsőbbségi szabályok értéket vehet részt.

Az Attribútumfolyam is használható **IgnoreThisFlow**. Ez hasonlít NULL abban az értelemben, amely azt jelzi, hogy semmilyen közre. A különbség, hogy nem távolítja el egy már meglévő érték a célkiszolgálón. Például a Attribútumfolyam soha nem volt hiba van.

Például:

A *ki az AD - felhasználó Exchange hibrid* található a következő folyamatot:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Ebben a kifejezésben értelmezhető: Ha a felhasználó postaládájához Azure AD-ben található, majd flow az attribútum az Azure AD az AD-ból. Ha nem, nem flow semmit, és az Active Directory. Ebben az esetben azt megakadályozná a meglévő értéket az ad-ben.

### <a name="importedvalue"></a>ImportedValue
A függvény ImportedValue eltér a egyéb funkciók, mert az attribútumnak a nevét szögletes zárójelbe helyett idézőjelek között kell tenni:  
`ImportedValue("proxyAddresses")`.

Általában a szinkronizálás során egy attribútumot a várt érték használ, akkor is, ha az még nem lett még exportálása, vagy hiba történt ("tetején a torony") exportálása során. Egy bejövő szinkronizálási azt feltételezi, hogy olyan attribútum, amely még nem ért egy csatlakoztatott címtárhoz idővel eléri azt. Néhány esetben fontos csak szinkronizálás egy érték, amely megerősítette a csatlakoztatott címtárhoz ("hologram és a különbözeti importálás torony").

Ez a függvény egy példát a out-of-box szinkronizálási szabály található *található az Active Directoryból – az Exchange-ből közös felhasználói*. A hibrid Exchange-ben az Exchange online hozzáadott érték csak szinkronizálnia kell, ha az érték sikeresen exportált visszaigazolását:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Sorrend
Több szinkronizálási szabályok kísérli meg a cél az azonos attribútumérték közre, amikor a sorrend értékét segítségével határozza meg a győztes. A legmagasabb prioritású legkisebb numerikus értéket, a szabály ütközést attribútum közre állapotra vált.

![Egyesítési típusok](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/precedence1.png)  

Ebben a rendezése segítségével határozza meg az objektumok kisebb részhalmazát pontosabb attribútumfolyamok. Például a out-az-mezőben-szabályok győződjön meg arról, amelyek egy engedélyezett fiók attribútumok (**felhasználói AccountEnabled**) prioritást élveznek a más fiókokhoz.

Sorrend összekötők között definiálhatók. Amely lehetővé teszi, hogy összekötők értékek először közre jobb adatokkal.

### <a name="multiple-objects-from-the-same-connector-space"></a>Ugyanazt a kapcsolódási térbe több objektumokat
Ha több olyan objektum az az azonos metaverzum-objektum csatlakozik egy összekötő helyen, érvénybe kell beállítani. Ha több objektumhoz ugyanaz a szinkronizálási szabály hatóköre, majd a szinkronizálási motor nincs meg tudja határozni elsőbbséget. Ez nem egyértelmű melyik adatforrás-objektum hozzá kell járulnia az értéket a metaverzumba. Ez a konfiguráció az elvártnak megfelelően nem egyértelmű, még akkor is, ha az attribútumok a forráshelyen ugyanazon értékekkel rendelkeznek.  
![Több objektumot csatoltak az azonos mv-objektum](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple1.png)  

A jelen esetben módosítani szeretné a szinkronizálási szabályok hatókörének így az adatforrás-objektumok különböző szinkronizálási szabályok hatókörében. Amely lehetővé teszi, hogy különböző sorrend meghatározásához.  
![Több objektumot csatoltak az azonos mv-objektum](./media/active-directory-aadconnectsync-understanding-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>További lépések
* További információk az a kifejezés nyelv [ismertetése deklaratív kiépítés kifejezések](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
* Lásd: hogyan deklaratív kiépítés használt out-of-box a [az alapértelmezett konfiguráció ismertetése](active-directory-aadconnectsync-understanding-default-configuration.md).
* Lásd: how to gyakorlati módosítja a deklaratív kiépítés használatával [hogyan lehet módosítani az alapértelmezett konfiguráció](active-directory-aadconnectsync-change-the-configuration.md).
* Továbbra is olvassa a felhasználók és névjegyek működése együtt az [felhasználók és névjegyek](active-directory-aadconnectsync-understanding-users-and-contacts.md).

**Áttekintő témakör**

* [Azure AD Connect szinkronizálása: megértéséhez, valamint a szinkronizálás testreszabása](active-directory-aadconnectsync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)

**Referencia-témakörei**

* [Azure AD Connect szinkronizálása: funkciók referencia](active-directory-aadconnectsync-functions-reference.md)
