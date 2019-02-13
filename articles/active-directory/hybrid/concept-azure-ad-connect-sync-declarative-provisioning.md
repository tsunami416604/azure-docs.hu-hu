---
title: 'Azure AD Connect: Deklaratív kiépítés megértése |} A Microsoft Docs'
description: Az Azure AD Connectben a deklaratív üzembe helyezési konfigurációs modell ismerteti.
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
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56171825"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning"></a>Az Azure AD Connect szinkronizálása: A deklaratív üzembe helyezés ismertetése
Ez a témakör ismerteti az Azure AD Connect konfigurációs modell. A modell a deklaratív kiépítés neve, és lehetővé teszi, hogy olyan konfigurációs módosítást egyszerű. Sok-sok dolog ebben a témakörben leírt speciális és a legtöbb ügyfél forgatókönyvhöz nem szükséges.

## <a name="overview"></a>Áttekintés
Deklaratív kiépítés csatlakoztatott forráskönyvtárat érkező objektumok feldolgozása, és határozza meg, hogyan az objektum és attribútumokat kell lesz átalakítva egy forrásból egy cél. Az objektum feldolgozása a szinkronizálási folyamat, és a folyamat megegyezik a bejövő és kimenő szabályok. Egy bejövő szabályt az adott összekötőtérben, a metaverzumba és kimenő szabály származik a metaverzumba egy összekötőterében.

![Szinkronizálási folyamat](./media/concept-azure-ad-connect-sync-declarative-provisioning/sync1.png)  

A folyamat számos különböző modulokat tartalmaz. Mindegyik felelős felügyeltobjektum-szinkronizációs egy fogalom.

![Szinkronizálási folyamat](./media/concept-azure-ad-connect-sync-declarative-provisioning/pipeline.png)  

* Forrás, az adatforrás-objektum
* [Hatókör](#scope), megkeresi az összes szinkronizálási szabály hatókörébe
* [Csatlakozás](#join), meghatározza, hogy összekötőterét és metaverzumbeli közötti kapcsolat
* Átalakítás számítja ki, hogyan lesz átalakítva attribútumok és a flow
* [Elsőbbségi](#precedence), így feloldja az ütköző attribútum hozzájárulások
* Cél, a célobjektum

## <a name="scope"></a>Hatókör
A hatókör-modul az a célja egy objektumot, és meghatározza, hogy a szabályokat, amelyek terjed ki, és szerepelnie kell a feldolgozása. Attribútumok értékek esetén az objektum különböző szinkronizálási szabály hatókörében kell értékeli ki. Például, hogy a letiltott felhasználó nem az Exchange-postaládával különböző szabályokat támaszt, mint az engedélyezett felhasználók egy postaládával rendelkezik.  
![Hatókör](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope1.png)  

A hatókör van meghatározva, csoportok és a kikötéseket. A feltételek a csoporton belül vannak. Logikai és a egy csoport összes záradékai közötti szolgál. Ha például (részleg = informatikai és ország = Dánia). Logikai vagy csoportok közötti szolgál.

![Hatókör](./media/concept-azure-ad-connect-sync-declarative-provisioning/scope2.png)  
Olvassa el ezt a képet a hatókör (részleg = informatikai és ország Dánia =) vagy (ország Svédország =). Ha igaz értékre, majd a szabály kiértékelése történik a csoport 1 vagy 2. csoport hatóköre van.

A hatókör-modul a következő műveleteket támogatja.

| Művelet | Leírás |
| --- | --- |
| EGYENLŐ, NOTEQUAL |Egy karakterlánc hasonlítsa össze, amely kiértékeli, ha az érték az attribútum értéke megegyezik. Többértékű attribútumok ISIN és ISNOTIN témakörben talál. |
| LESSTHAN, LESSTHAN_OR_EQUAL |Egy karakterlánc összehasonlítása, amely kiértékeli, ha az érték kevesebb az attribútum értéke. |
| TARTALMAZ, NOTCONTAINS |Egy karakterlánc hasonlítsa össze, amely kiértékeli, ha az érték megtalálható valahol belüli értéket az attribútum. |
| STARTSWITH, NOTSTARTSWITH |Egy karakterlánc hasonlítsa össze, amely kiértékeli, ha az érték szerepel az attribútum értékének elejére. |
| ENDSWITH, NOTENDSWITH |Egy karakterlánc hasonlítsa össze, amely kiértékeli, ha az attribútum értéke lévő érték. |
| GREATERTHAN, GREATERTHAN_OR_EQUAL |Egy karakterlánc-összehasonlítási, amely kiértékeli, ha az értéke nagyobb, mint az attribútum értéke. |
| ISNULL, ISNOTNULL |Kiértékeli, ha az attribútum hiányzik az objektum. Ha az attribútum nem található, és ezért null értékű, a szabály hatóköre van. |
| ISIN, ISNOTIN |Ha az érték megtalálható a megadott attribútum kiértékeli. Tato operace je egyenlő és NOTEQUAL többértékű változata. Az attribútum egy többértékű attribútumot várt és ha az érték található az attribútum értékeket, majd a szabály hatókörébe. |
| ISBITSET, ISNOTBITSET |Kiértékeli, ha egy adott bit be van állítva. Például segítségével kiértékelheti a bits a userAccountControl megtekintheti, ha a felhasználó engedélyezve van-e vagy le van tiltva. |
| ISMEMBEROF, ISNOTMEMBEROF |Az érték egy csoporthoz az összekötőtérben DN tartalmaznia kell. Ha az objektum a megadott csoport tagja, a szabály hatóköre van. |

## <a name="join"></a>Csatlakozás
A szinkronizálási folyamat a join modul feladata a cél az objektum a forrás és a egy objektum közötti kapcsolat keresése. Egy bejövő szabályt a ezt a kapcsolatot az adott összekötőtérben, egy kapcsolat objektum keresése a metaverzum-objektum lehet.  
![Csatlakozzon a cs- és mv között](./media/concept-azure-ad-connect-sync-declarative-provisioning/join1.png)  
A cél, hogy tekintse meg, ha egy objektum már megtalálható a metaverzumban, egy másik összekötő által létesített, érdemes lehet társítva. Például egy fiók-erőforrás erdőben a felhasználónak a fiókerdőben kell csatlakoztatni az erőforráserdőből a felhasználóval.

Illesztések összekötő terület objektumok együtt csatlakoztatása az azonos metaverzum-objektum főleg a bejövő szabályok szolgálnak.

Egy vagy több csoportot az illesztések vannak meghatározva. Záradékok rendelkezik egy csoporton belül. Logikai és a egy csoport összes záradékai közötti szolgál. Logikai vagy csoportok közötti szolgál. A csoportok feldolgozása sorrendben felülről lefelé történik. Egy csoport egy objektummal pontosan egy egyezést talált a célkiszolgálón, ha nincsenek más illesztési szabályok értékeli. Ha nulla vagy több mint egy objektum található, a szabályok a következő csoport feldolgozás továbbra is. Ebből kifolyólag a szabályok létrehozott legtöbb explicit első sorrendjében, és több intelligens végén kell lennie.  
![Csatlakozzon a definíció](./media/concept-azure-ad-connect-sync-declarative-provisioning/join2.png)  
Ez a kép illesztéseket feldolgozása alsó történik. Először a szinkronizálási folyamat azt látja, hogy van-e az employeeID egyezést. Ha nem, a második szabálynak látja, ha a fiók nevét is használható együtt az objektumok csatlakozni. Ha ez nem egyező vagy, a harmadik és egyben utolsó szabály több algoritmuskódot a felhasználó nevét.

Ha az összes illesztési szabályok kiértékelése, és nem pontosan egy egyezést, a **hivatkozás típusa** a a **leírás** lap szolgál. Ha ez a beállítás értéke **kiépítése**, akkor jön létre egy új objektumot a célkiszolgálón.  
![Kiépítése, vagy csatlakozzon](./media/concept-azure-ad-connect-sync-declarative-provisioning/join3.png)  

Az objektum kell rendelkeznie illesztési szabályok több egyetlen szinkronizálási szabály hatókörében. Ha több szinkronizálási szabály ahol való csatlakozás van definiálva, akkor hiba történik. Sorrend nem használatos illesztés az ütközések feloldása. Egy objektum rendelkeznie kell egy illesztési szabály hatókörébe tartozó attribútumokat tartalmazó ugyanabban az bejövő/kimenő irányban folyamat. Ha szeretne folyamat attribútumainak bejövő és kimenő ugyanazon az objektumon, egy bejövő és a egy kimenő szinkronizálási szabály az illesztési kell rendelkeznie.

Kimenő illesztési rendelkezik egy különleges viselkedését, való üzembe helyezése egy objektum egy cél összekötőterében. A megkülönböztető név attribútuma szolgál, hogy először kipróbálják a fordított csatlakozzon. Ha már van egy objektumot a céloldali összekötőtér a azonos DN-nel, az objektumok csatlakoznak.

Az illesztési modul csak akkor történik meg után Ha egy új szinkronizálási szabály hatóköre kerül. Ha csatlakozott egy objektumot, a rendszer nem disjoining akkor is, ha az illesztési feltétel nem teljesül. Ha meg szeretné tartományról való leválasztás egy objektumot, be kell lépnie a szinkronizálási szabály, amely az objektumok csatlakoztatott hatókörén kívül.

### <a name="metaverse-delete"></a>Metaverzum delete
A metaverzum-objektum megmarad, amíg nincs egy szinkronizálási szabály hatókörében az **hivatkozás típusa** beállítása **kiépítése** vagy **StickyJoin**. Egy StickyJoin használatos, ha az összekötő egy új objektumot a metaverzumba kiépítése nem engedélyezett, azonban csatlakoztatva van, akkor törölni kell a forrás a metaverzum-objektum törlése előtt.

A törölt metaverzum-objektum egy kimenő szinkronizálási szabály társított összes objektumot megjelölve **kiépítése** törlési jelennek meg.

## <a name="transformations"></a>Átalakítások
Az átalakítások meghatározásához attribútumok áramlásának módját a forrásról a célra használják. A flow a következők egyike lehet **típusok flow**: Közvetlen, állandó vagy kifejezés. Közvetlen folyamat, mint egy attribútumérték folyamatok-átalakítás nélküli további van. Állandó érték megadása esetén a megadott értéket. Egy kifejezés deklaratív üzembe helyezési kifejezés nyelvét használja, Express, hogyan kell lennie az átalakítást. A kifejezés nyelve részletei megtalálhatók a [deklaratív üzembe helyezési kifejezésnyelveket ismertetése](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) témakör.

![Kiépítése, vagy csatlakozzon](./media/concept-azure-ad-connect-sync-declarative-provisioning/transformations1.png)  

A **egyszer a alkalmazni** jelölőnégyzet határozza meg, hogy az attribútum csak kell beállítani, ha az objektum először hozza létre. Például ez a konfiguráció segítségével állítsa be az új felhasználói objektumhoz kezdeti jelszavát.

### <a name="merging-attribute-values"></a>Attribútumértékek egyesítése
Az attribútumfolyamok van a beállítás határozza meg, ha a többértékű attribútumok több különböző összekötők össze kell vonni. Az alapértelmezett érték **frissítés**, ami azt jelenti, hogy a szinkronizálási szabály legmagasabb prioritású kell nyerhet.

![Egyesítési típusok](./media/concept-azure-ad-connect-sync-declarative-provisioning/mergetype.png)  

Is **egyesítése** és **MergeCaseInsensitive**. Ezek a beállítások lehetővé teszik, hogy a különböző forrásokból származó értékeket. Ha például használat a tag vagy a proxyAddresses attribútum számos különböző erdőkből származó egyesíteni. Ha ezt a beállítást használja, minden szinkronizálási szabály hatókörébe az objektum egyesítési ugyanolyan típusú kell használnia. Nem lehet definiálni **frissítés** egy összekötő és **egyesítése** másik. Ha megpróbál, hibaüzenetet kap.

A különbség a között **egyesítése** és **MergeCaseInsensitive** , hogyan lehet feldolgozni az ismétlődő attribútumértékekkel. A szinkronizálási motor biztosítja, hogy az ismétlődő értékek nem helyez be a célattribútum. A **MergeCaseInsensitive**, az ismétlődő értékek különbség csak abban az esetben nem fog meglétét. Ha például nem kell megjelennie mindkét "SMTP:bob@contoso.com"és"smtp:bob@contoso.com" a célattribútum. **Egyesítse** csak fér hozzá a pontos értékeket, és több érték esetén csak az eltérő esetben szerepelhet.

A beállítás **cserélje le** ugyanaz, mint **frissítés**, viszont nem.

### <a name="control-the-attribute-flow-process"></a>Az attribútum a folyamat folyamatot vezérlő
Ha több bejövő szinkronizálási szabály ugyanazt a metaverzum-attribútum hozzájárulni vannak beállítva, majd sorrend meghatározására szolgál a győztes. A szinkronizálási szabály (legkisebb numerikus értéket) legmagasabb prioritású működhet közre a érték történik. Kimenő szabályok esetében ugyanez történik. A szinkronizálási szabály a legmagasabb prioritású wins, és hozzájárul az értéket a csatlakoztatott címtárban.

Bizonyos esetekben helyett közreműködés egy értéket, a szinkronizálási szabály meg kell határoznia más szabályok működése kell. Van néhány speciális literálok, ebben az esetben használja.

A bejövő szinkronizálási szabályok, a szövegkonstansban **NULL** jelzi, hogy a folyamat nem rendelkezik értékkel közre is használható. Az alacsonyabb fontossági sorrendű egy másik szabály közreműködhet értéket. Ha nincs szabály viszonyított érték, a metaverzum-attribútum törlődik. Egy kimenő szabályhoz Ha **NULL** nem végső értéke az összes szinkronizálási szabály feldolgozása után a rendszer eltávolítja az értéket a csatlakoztatott címtárban.

A konstans **AuthoritativeNull** hasonló **NULL** , de a különbség az, hogy nincs alacsonyabb prioritású szabály közreműködhet értéket.

Az Attribútumfolyam is használhatja **IgnoreThisFlow**. Ez hasonlít NULL abban az értelemben, amely azt jelzi, semmit nem kell a közreműködéshez. A különbség az, hogy nem távolítja el egy már meglévő értéket a célkiszolgálón. Például az Attribútumfolyam soha nem volt hiba van.

Például:

A *vette az ad - felhasználó Exchange hibrid* található a következő folyamatot:  
`IIF([cloudSOAExchMailbox] = True,[cloudMSExchSafeSendersHash],IgnoreThisFlow)`  
Ez a kifejezés olvassa el: Ha a felhasználó postaláda az Azure ad-ben található, majd a flow az attribútum az Azure AD az AD-ból. Ha nem, nem flow vissza az Active Directory semmit. Ebben az esetben a az ad-ben, akkor megtartja a meglévő értéket.

### <a name="importedvalue"></a>ImportedValue
A függvény ImportedValue eltér a további függvények, mert az attribútum neve szögletes zárójelek helyett idézőjelek közé kell tenni:  
`ImportedValue("proxyAddresses")`.

A szinkronizálás során általában egy attribútumot a várt érték használ, még akkor is, ha ezt még nem még nincsenek exportált, vagy hiba érkezett ("felső részén a tower") exportálása során. Egy bejövő szinkronizálási azt feltételezi, hogy olyan attribútum, amely még nem ért egy csatlakoztatott címtárhoz idővel eléri azt. Bizonyos esetekben fontos csak szinkronizálás érték, amely a csatlakoztatott címtárban ("hologram és a különbözeti tower importálása") is megerősítette.

Ez a függvény egy példát az out-of-box szinkronizálási szabályának található *a az AD-ből – az Exchange közös felhasználói*. Hibrid Exchange-hez, az Exchange online hozzáadott értéket csak a rendszer szinkronizálja az Ha megerősítette, hogy az érték exportálása sikeres:  
`proxyAddresses` <- `RemoveDuplicates(Trim(ImportedValue("proxyAddresses")))`

## <a name="precedence"></a>Sorrend
Ha több szinkronizálási szabály megpróbál működhet közre a cél az azonos attribútum értéke, a sorrend értékét a győztes meghatározására szolgál. Hozzájárul az attribútum egy ütközik legmagasabb prioritású legkisebb numerikus értéket, a szabály fog.

![Egyesítési típusok](./media/concept-azure-ad-connect-sync-declarative-provisioning/precedence1.png)  

A sorrend meghatározására az objektumok kisebb részhalmazát pontosabb attribútumfolyamok használható. Például a out-az-box-szabályok győződjön meg arról, amelyek egy engedélyezett fiók attribútumok (**felhasználói AccountEnabled**) prioritást élveznek a többi fiók.

Elsőbbségi összekötők között lehet definiálni. Amely lehetővé teszi az összekötők, jobb adatokkal való közreműködés először az értékeket.

### <a name="multiple-objects-from-the-same-connector-space"></a>Az azonos összekötőtérben több objektumait
Több objektum esetén az azonos összekötőtérben, ugyanahhoz a metaverzum-objektumhoz csatlakozik, érvénybe kell beállítani. Ha több objektum ugyanaz a szinkronizálási szabály hatókörében van, majd a szinkronizálási motor, nem tudja meghatározni a sorrend. Ez nem egyértelmű melyik adatforrás-objektum hozzá kell járulnia az értéket a metaverzumba. Ez a konfiguráció az elvártnak megfelelően nem egyértelmű akkor is, ha a forrás attribútumokhoz ugyanazt az értéket.  
![Több objektumot csatoltak az azonos mv-objektum](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple1.png)  

Ebben a forgatókönyvben a szinkronizálási szabályok hatókörének módosítása, így az adatforrás-objektumok különböző szinkronizálási szabály hatókörébe kell. Lehetővé teszi a különböző sorrend határozza meg.  
![Több objektumot csatoltak az azonos mv-objektum](./media/concept-azure-ad-connect-sync-declarative-provisioning/multiple2.png)  

## <a name="next-steps"></a>További lépések
* További információ az a kifejezés nyelv [deklaratív kiépítés kifejezéseinek ismertetése](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md).
* Lásd: hogyan deklaratív kiépítés használt out-of-box a [az alapértelmezett konfiguráció ismertetése](concept-azure-ad-connect-sync-default-configuration.md).
* Tekintse meg gyakorlati módosítást használata a deklaratív kiépítés [hogyan lehet módosítani az alapértelmezett konfiguráció](how-to-connect-sync-change-the-configuration.md).
* Felhasználók és a kapcsolattartók együttműködésének olvasni [ismertetése felhasználók és a kapcsolattartók](concept-azure-ad-connect-sync-user-and-contacts.md).

**Áttekintő témakör**

* [Az Azure AD Connect szinkronizálása: Megismerheti, és testre szabhatja a szinkronizálás](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)

**Referencia-témakörök**

* [Az Azure AD Connect szinkronizálása: Functions – referencia](reference-connect-sync-functions-reference.md)
