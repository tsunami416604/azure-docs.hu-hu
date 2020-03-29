---
title: 'Azure AD Connect: A deklaratív kiépítés ismertetése | Microsoft dokumentumok'
description: A deklaratív kiépítési konfigurációs modell az Azure AD Connect ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: cfbb870d-be7d-47b3-ba01-9e78121f0067
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 543c1a6706f794b81c4f93fc6fff3a61ed3fb9e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60246321"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Azure AD Connect szinkronizálás: A deklaratív kiépítés ismertetése
Ez a témakör ismerteti a konfigurációs modell az Azure AD Connect. A modell neve deklaratív kiépítés, és lehetővé teszi, hogy a konfiguráció sokkal könnyedén. A témakörben ismertetett számos dolog speciális, és a legtöbb ügyfélforgatókönyvesetében nem szükséges.

## <a name="overview"></a>Áttekintés
A deklaratív kiépítés a forráshoz csatlakoztatott könyvtárból érkező objektumok feldolgozása, és meghatározza, hogy az objektumot és az attribútumokat hogyan kell forrásból céldá alakítani. Egy objektum feldolgozása egy szinkronizálási folyamat, és a folyamat megegyezik a bejövő és kimenő szabályok. A bejövő szabály egy összekötő térből a metaverzumba, a kimenő szabály pedig a metaverzumból egy összekötő térbe.

![Szinkronizálási folyamat](./media/concept-azure-ad-connect-sync-declarative-provisioning/sync1.png)  

A folyamat több különböző modullal rendelkezik. Mindegyik felelős egy fogalomért az objektum-szinkronizálásban.

![Szinkronizálási folyamat](./media/concept-azure-ad-connect-sync-declarative-provisioning/pipeline.png)  

* Forrás, A forrásobjektum
* [Hatókör](#scope), Megkeresi a hatókörben lévő összes szinkronizálási szabályt
* [Illesztés](#join), Az összekötő tér és a metaverzum közötti kapcsolatot határozza meg
* Átalakítás, Kiszámítja, hogyan kell átalakítani és folyni az attribútumokat
* [Elsőbbség](#precedence), Ütköző attribútum-hozzájárulások feloldása
* Cél, A célobjektum

## <a name="scope"></a>Hatókör
A hatókörmodul kiértékel egy objektumot, és meghatározza a hatókörben lévő szabályokat, amelyeket a feldolgozásnak tartalmaznia kell. Az objektum attribútumértékeitől függően a rendszer kiértékeli a különböző szinkronizálási szabályokat, hogy hatókörben legyenek. Egy Exchange-postaládával nem rendelkező letiltott felhasználónak például más szabályai vannak, mint a postaládával rendelkező engedélyezett felhasználóknak.  
![Hatókör](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope1.png)  

A hatókör csoportokként és záradékokként van definiálva. A záradékok egy csoporton belül vannak. A logikai ÉS a csoport összes záradéka között használatos. Például (részleg =IT és ország = Dánia). A csoportok között logikai VAGY használatos.

![Hatókör](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope2.png)  
A képen szereplő hatókört a következőképpen kell értelmezni: (osztály = IT ÉS ország = Dánia) VAGY (ország=Svédország). Ha az 1- es vagy a 2-es csoport értéke igaz, akkor a szabály hatóköre.

A hatókörmodul a következő műveleteket támogatja.

| Művelet | Leírás |
| --- | --- |
| EGYENLŐ, NEM EGYENLŐ |Egy karakterlánc összehasonlítása, amely kiértékeli, hogy az érték megegyezik-e az attribútum értékével. A többértékű attribútumokról az ISIN és AZ ISNOTIN. |
| Kevesebb, LESSTHAN_OR_EQUAL |Egy karakterlánc összehasonlítása, amely kiértékeli, hogy az érték kisebb-e, mint az attribútum ban lévő érték. |
| TARTALMAZ, NOTCONTAINS |Egy karakterlánc-összehasonlítás, amely kiértékeli, hogy az érték megtalálható-e valahol az attribútumban az értéken belül. |
| STARTSWITH, NOTSTARTSWITH |Egy karakterlánc-összehasonlítás, amely kiértékeli, hogy az érték az attribútum értékének elején van-e. |
| VÉGZŐDIK, NOTENDSWITH |Egy karakterlánc-összehasonlítás, amely kiértékeli, hogy az érték az attribútum értékének végén van-e. |
| NAGYOBB, GREATERTHAN_OR_EQUAL |Egy karakterlánc összehasonlítása, amely kiértékeli, hogy az érték nagyobb-e, mint az attribútum ban lévő érték. |
| ISNULL, ISNOTNULL |Kiértékeli, hogy az attribútum hiányzik-e az objektumból. Ha az attribútum nincs jelen, és ezért null, akkor a szabály hatóköre. |
| ISIN, ISNOTIN |Kiértékeli, hogy az érték szerepel-e a definiált attribútumban. Ez a művelet az EQUAL és a NOTEQUAL többértékű változata. Az attribútum nak többértékű attribútumnak kell lennie, és ha az érték megtalálható az attribútumértékek bármelyikében, akkor a szabály hatókörben van. |
| ISBITSET, ISNOTBITSET |Kiértékeli, hogy egy adott bit be van-e állítva. Például a userAccountControl bitjének kiértékelésére használható, hogy lássa, engedélyezve van-e vagy le van-e tiltva egy felhasználó. |
| ISMEMBEROF, ISNOTMEMBEROF |Az értéknek dn-t kell tartalmaznia az összekötő térben lévő csoportszámára. Ha az objektum a megadott csoport tagja, a szabály hatókörben van. |

## <a name="join"></a>Csatlakozás
A szinkronizálási folyamat illesztési modulja felelős a forrásban lévő objektum és a célobjektum közötti kapcsolat megtalálásáért. Bejövő szabálynál ez a kapcsolat egy összekötőtérben lévő objektum lenne, amely kapcsolatot talál a metaverzumban lévő objektummal.  
![Csatlakozás között cs és mv](./media/concept-azure-ad-connect-sync-declarative-provisioning/join1.png)  
A cél az, hogy ha van egy objektum már a metaverzumban, egy másik összekötő által létrehozott, azt kell társozni. Egy fiókerőforrás-erdőben például a fiókerdő felhasználóját össze kell vonni az erőforráserdőből származó felhasználóval.

Az illesztések többnyire bejövő szabályokon használatosak az összekötő térobjektumok ugyanazon metaverzum-objektumhoz való összekötéséhez.

Az illesztések egy vagy több csoportként vannak definiálva. Egy csoporton belül vannak záradékok. A logikai ÉS a csoport összes záradéka között használatos. A csoportok között logikai VAGY használatos. A csoportok feldolgozása fentről lefelé haladva. Ha az egyik csoport pontosan egy egyezést talált egy objektummal a célban, akkor a rendszer nem értékeli ki a többi illesztési szabályt. Ha nulla vagy egynél több objektum található, a feldolgozás folytatódik a következő szabálycsoportra. Ezért a szabályokat a legegyértelműbb első és homályosabb sorrendben kell létrehozni a végén.  
![Csatlakozás definíciója](./media/concept-azure-ad-connect-sync-declarative-provisioning/join2.png)  
A képen szereplő illesztések feldolgozása fentről lefelé halad. Először a szinkronizálási folyamat látja, ha van egyezés az employeeID. Ha nem, a második szabály azt látja, hogy a fióknév használható-e az objektumok összekapcsolására. Ha ez sem egyezik, a harmadik és egyben utolsó szabály a felhasználó nevének használatával egy homályosabb egyezés.

Ha az összes illesztési szabály ki lett értékelve, és nincs pontosan egy egyezés, a Program a **Leírás** lapon lévő **Hivatkozástípust** használja. Ha ez a beállítás **kiépítés**re van állítva, akkor a célban egy új objektum jön létre.  
![Rendelkezés vagy csatlakozás](./media/concept-azure-ad-connect-sync-declarative-provisioning/join3.png)  

Egy objektumnak csak egyetlen szinkronizálási szabálya lehet, amelynek hatóköre illesztési szabályokat. Ha több szinkronizálási szabály van definiálva, hiba történik. Az elsőbbség nem használható illesztési ütközések feloldására. Egy objektumnak illesztési szabállyal kell rendelkeznie az azonos bejövő/kimenő irányú attribútumok áramlásához. Ha ugyanabba az objektumba bejövő és kimenő attribútumokat is kell folyatnia, akkor egy bejövő és egy kimenő szinkronizálási szabálynak is rendelkeznie kell az illesztéssel.

A kimenő illesztés különleges viselkedéssel rendelkezik, amikor megpróbál egy objektumot egy célösszekötő területére kiépíteni. A DN attribútum először fordított illesztést próbál meg. Ha már van egy objektum a célösszekötő térben ugyanazzal a DN-nel, az objektumok összekapcsolódnak.

Az illesztési modul csak egyszer kerül kiértékelésre, amikor egy új szinkronizálási szabály lép hatókörbe. Ha egy objektum csatlakozott, akkor nem lesz kiválasztva, még akkor sem, ha az illesztési feltételek már nem teljesülnek. Ha le szeretne választadni egy objektumról, az objektumokat egyesítő szinkronizálási szabálynak nem szabad hatókörből kilépnie.

### <a name="metaverse-delete"></a>Metaverzum törlése
A metaverzum-objektum mindaddig megmarad, amíg van egy szinkronizálási szabály a hatókörben, ha a **Kapcsolattípusa** **beállítás kiépítés** vagy **StickyJoin**lesz. A StickyJoin akkor használatos, ha egy összekötő nem építhet új objektumot a metaverzumba, de ha csatlakozott, törölni kell a forrásban a metaverzum objektum törlése előtt.

Metaverzum-objektum törlésekor a **leépítésre** megjelölt kimenő szinkronizálási szabályhoz társított összes objektum törlésre lesz megjelölve.

## <a name="transformations"></a>Átalakítások
Az átalakítások segítségével határozható meg, hogyan kell az attribútumok a forrásból a célba áramlani. A folyamatok a következő **folyamattípusok**egyikével rendelkezhetnek: Közvetlen, Állandó vagy Kifejezés. Közvetlen folyamat, egy attribútumértéket úgy folyik, ahogy van, további átalakítások nélkül. Egy állandó érték állítja be a megadott értéket. Egy kifejezés a deklaratív létesítési kifejezés nyelvét használja az átalakítás módjának kifejezésére. A kifejezés nyelvének részletei a [deklaratív kiépítési kifejezés nyelvi](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) témakörében találhatók.

![Rendelkezés vagy csatlakozás](./media/concept-azure-ad-connect-sync-declarative-provisioning/transformations1.png)  

Az **Alkalmaz egyszer** jelölőnégyzet azt határozza meg, hogy az attribútumot csak az objektum kezdeti létrehozásakor kell beállítani. Ezzel a konfigurációval például egy új felhasználói objektum hoz létre egy kezdeti jelszót.

### <a name="merging-attribute-values"></a>Attribútumértékek egyesítése
Az attribútumfolyamatokban van egy beállítás annak meghatározására, hogy a többértékű attribútumokat több különböző összekötőkből kell-e egyesíteni. Az alapértelmezett érték a **Frissítés**, amely azt jelzi, hogy a legmagasabb prioritású szinkronizálási szabálynak kell nyernie.

![Egyesítési típusok](./media/concept-azure-ad-connect-sync-declarative-provisioning/mergetype.png)  

Van is **Merge** és **MergeCaseInsensitive**. Ezek a beállítások lehetővé teszik a különböző forrásokból származó értékek egyesítését. Például használható a tag vagy proxyAddresses attribútum egyesítésére több különböző erdőből. Ha ezt a beállítást használja, az objektum hatókörében lévő összes szinkronizálási szabálynak ugyanazt az egyesítési típust kell használnia. Nem definiálhat **frissítést** az egyik összekötőből, és **nem adhat hozzá** egy másiktól. Ha megpróbálja, hibaüzenetet kap.

Az **Egyesítés** és **a MergeCaseInsensitive** közötti különbség az ismétlődő attribútumértékek feldolgozása. A szinkronizálási motor gondoskodik arról, hogy a rendszer ne szúrja be az ismétlődő értékeket a célattribútumba. A **MergeCaseInin**esetében a csak az esetben fennálló különbséggel rendelkező ismétlődő értékek nem lesznek jelen. Például nem láthatja aSMTP:bob@contoso.com"smtp:bob@contoso.com" és a " " " szót a célattribútumban. **Az egyesítés** csak a pontos értékeket és a több értéket vizsgálja, ahol csak az esetekben lehet különbség.

A **Csere** beállítás megegyezik a **Frissítés**beállítással, de nem használatos.

### <a name="control-the-attribute-flow-process"></a>Az attribútumfolyamat folyamatának szabályozása
Ha több bejövő szinkronizálási szabály van beállítva, hogy hozzájáruljon ugyanahhoz a metaverzum-attribútumhoz, akkor a rendszer a prioritást használja a győztes meghatározásához. A legmagasabb prioritású szinkronizálási szabály (a legalacsonyabb numerikus érték) hozzá fog járulni az értékhez. Ugyanez történik a kimenő szabályok esetében is. A legmagasabb prioritású szinkronizálási szabály nyer, és hozzájárul a csatlakoztatott könyvtár értékéhez.

Bizonyos esetekben ahelyett, hogy egy értéket, a szinkronizálási szabály meg kell határoznia, hogyan kell más szabályokat kell viselkednie. Van néhány speciális szó használt ebben az esetben.

Bejövő szinkronizálási szabályok esetén a **null** érték segítségével jelezhető, hogy a folyamatnak nincs hozzájárulási értéke. Egy másik, alacsonyabb prioritású szabály is hozzájárulhat egy értékhez. Ha egyetlen szabály sem járult hozzá értékhez, akkor a metaverzum attribútum törlődik. Kimenő szabály esetén, ha a **NULL** a végső érték az összes szinkronizálási szabály feldolgozása után, akkor az érték törlődik a csatlakoztatott könyvtárból.

A **literali Mérvadó null** értéke hasonló a **NULL-hoz,** de azzal a különbséggel, hogy egyetlen alacsonyabb elsőbbségi szabály sem járulhat hozzá értékhez.

Az attribútumfolyamat az **IgnoreThisFlow -t**is használhatja. Ez hasonló a NULL abban az értelemben, hogy azt jelzi, nincs semmi, hogy hozzájáruljon. A különbség az, hogy nem távolítja el a cél egy már meglévő értékét. Olyan, mintha az attribútum áramlása soha nem volt ott.

Például:

Az *Out to AD – User Exchange hibrid* ben a következő folyamat található:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Ezt a kifejezést a következőképpen kell értelmezni: ha a felhasználói postaláda az Azure AD-ben található, majd az attribútumot az Azure AD-ből az AD-be. Ha nem, ne folyjon vissza semmit az Active Directoryba. Ebben az esetben megtartaná a meglévő értéket az AD-ben.

### <a name="importedvalue"></a>Importált érték
Az ImportedValue függvény különbözik az összes többi függvénytől, mivel az attribútum nevét szögletes zárójelek helyett idézőjelek közé kell tenni:  
`ImportedValue("proxyAddresses")`.

A szinkronizálás során az attribútum általában a várt értéket használja, még akkor is, ha még nem exportálták, vagy hiba történt az exportálás során ("a torony teteje"). A bejövő szinkronizálás feltételezi, hogy egy attribútum, amely még nem érte el a csatlakoztatott könyvtárvégül eléri azt. Bizonyos esetekben fontos, hogy csak a csatlakoztatott könyvtár által megerősített értéket ("hologram és delta importtorony") szinkronizálja.

Erre a funkcióra példa az AD – User Common *from Exchange in*in párbeszédpanel beépített szinkronizálási szabályában található. A Hibrid exchange programban az Exchange online által hozzáadott értéket csak akkor kell szinkronizálni, ha megerősítést nyert, hogy az érték exportálása sikeres volt:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Precedencia
Ha több szinkronizálási szabály próbálja meg hozzájárulni ugyanazt az attribútumértéket a célhoz, a rendszer a prioritási értéket használja a győztes meghatározásához. A legmagasabb prioritású, legalacsonyabb numerikus értéket képviselő szabály egy ütközésben hozzá fog járulni az attribútumhoz.

![Egyesítési típusok](./media/concept-azure-ad-connect-sync-declarative-provisioning/precedence1.png)  

Ez a rendezés az objektumok kis részhalmazának pontosabb attribútumfolyamatainak meghatározására használható. A beépített szabályok például győződjön meg arról, hogy az engedélyezett fiókból (**User AccountEnabled**) származó attribútumok elsőbbséget élveznek a többi fiókkal szemben.

Az összekötők között elsőbbség definiálható. Ez lehetővé teszi, hogy a jobb adatokkal rendelkező összekötők először értékekkel járuljanak hozzá.

### <a name="multiple-objects-from-the-same-connector-space"></a>Több objektum ugyanabból az összekötőtérből
Ha ugyanabban az összekötő térben több objektum is kapcsolódik ugyanahhoz a metaverzum-objektumhoz, az elsőbbséget módosítani kell. Ha több objektum is ugyanannak a szinkronizálási szabálynak a hatókörében van, akkor a szinkronizálási motor nem tudja meghatározni a prioritást. Nem egyértelmű, hogy melyik forrásobjektumnak kell hozzájárulnia a metaverzum értékéhez. Ez a konfiguráció akkor is nem egyértelmű, ha a forrás attribútumai azonos értékűek.  
![Több objektum ugyanahhoz az mv objektumhoz csatlakoztatva](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple1.png)  

Ebben az esetben módosítania kell a szinkronizálási szabályok hatókörét, hogy a forrásobjektumok hatóköre eltérő legyen. Ez lehetővé teszi, hogy különböző prioritást határozzon meg.  
![Több objektum ugyanahhoz az mv objektumhoz csatlakoztatva](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>További lépések
* A kifejezés nyelvéről a [Deklaratív kiépítési kifejezések ismertetése című](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)témakörben olvashat bővebben.
* Tekintse meg, hogyan használja a deklaratív kiépítést a beépített használat [az alapértelmezett konfiguráció ismertetése című](concept-azure-ad-connect-sync-default-configuration.md)témakörben.
* Tekintse meg, hogyan lehet gyakorlati módosítást eszközolni a deklaratív kiépítés használatával [az Alapértelmezett konfiguráció módosítása](how-to-connect-sync-change-the-configuration.md)című témakörben.
* A Felhasználók és kapcsolattartók [ismertetése](concept-azure-ad-connect-sync-user-and-contacts.md)című témakörben továbbra is olvashatja, hogyan működnek együtt a felhasználók és a kapcsolattartók.

**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: A szinkronizálás megértése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)

**Referencia-témakörök**

* [Azure AD Connect szinkronizálás: Függvények – referencia](reference-connect-sync-functions-reference.md)
