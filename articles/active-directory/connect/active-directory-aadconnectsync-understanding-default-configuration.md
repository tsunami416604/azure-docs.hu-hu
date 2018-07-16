---
title: 'Az Azure AD Connect szinkronizálása: az alapértelmezett konfiguráció ismertetése |} A Microsoft Docs'
description: Ez a cikk ismerteti az Azure AD Connect szinkronizálása az alapértelmezett konfigurációt.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 7be5569654cb537260117ecd452e58cff9824a88
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044774"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Az Azure AD Connect szinkronizálása: az alapértelmezett konfiguráció ismertetése
Ez a cikk ismerteti az out-of-box konfigurációs szabályok. Dokumentumok, és ezek a szabályok milyen hatással van a konfigurációs szabályok. Ez azt is bemutatja az Azure AD Connect-szinkronizálással használható alapértelmezett konfigurációt. A célja, hogy az olvasó felismeri a deklaratív kiépítés nevű konfigurációs modell egy való életből vett példában működéséről. Ez a cikk azt feltételezi, hogy már telepítette, és a telepítővarázsló használata az Azure AD Connect-szinkronizálás beállítása.

Szeretné megtudni, a konfigurációs modell részleteit, olvassa el a [ismertetése deklaratív kiépítés](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>A helyszíni Azure ad-Out-of-box szabályai
Az alábbi kifejezések az out-of-box konfigurációban található.

### <a name="user-out-of-box-rules"></a>Felhasználói out-of-box szabályok
Ezek a szabályok is vonatkoznak az iNetOrgPerson objektum típusa.

Egy felhasználói objektumot meg kell felelniük szinkronizálását a következő:

* A sourceAnchor kell rendelkeznie.
* Az objektum létrehozása az Azure ad-ben, után sourceAnchor nem módosítható. Módosult a helyi érték esetén az objektum leállítja a szinkronizálás, amíg a sourceAnchor vissza az előző értékre változik.
* Kell rendelkeznie a accountEnabled (userAccountControl) attribútum feltöltve. Egy helyszíni Active Directoryval Ez az attribútum nem mindig jelen, és ki van töltve.

Az alábbi felhasználói objektumok **nem** szinkronizálva az Azure AD:

* `IsPresent([isCriticalSystemObject])`. Győződjön meg, hogy sok out-of-box objektumok az Active Directoryban, például a beépített rendszergazdai fiók, nincsenek szinkronizálva.
* `IsPresent([sAMAccountName]) = False`. Győződjön meg arról, a sAMAccountName attribútum felhasználói objektumok nincsenek szinkronizálva. Ebben az esetben csak gyakorlatilag történnek NT4 verzióról frissített tartományban.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Ne szinkronizáljon az Azure AD Connect-szinkronizálás és a korábbi verziói által használt fiókkal.
* Nem működik az Exchange Online Exchange-fiókok nem szinkronizálja.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Nem működik az Exchange Online-objektumok nem szinkronizálja.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Ez a bitmaszk (& H21C07000) a következő objektumok ki fog szűrni:
  * Levelezési nyilvános mappa (az előzetes verzió 1.1.524.0 kezdődően)
  * Rendszer kísérő postaláda
  * Postaláda-adatbázis postaláda (rendszer postaláda)
  * Univerzális biztonsági csoport (a felhasználó nem vonatkozik, de örökölt okokból megtalálható)
  * Nem – univerzális csoport (a felhasználó nem vonatkozik, de örökölt okokból megtalálható)
  * Postaláda-csomag
  * Felderítési postaláda
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Ne szinkronizáljon a victim replikációs objektumokat.

A következő attribútum szabályok érvényesek:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. A sourceAnchor attribútum nem járult a társított postaládából. Azt feltételezzük, hogy ha a hivatkozott postafiókkal talált, a tényleges fiók tartományhoz később.
* Az Exchange kapcsolódó attribútumok csak legyenek szinkronizálva, ha az attribútum **mailNickName** értékkel rendelkezik.
* Ha több erdő, majd attribútumok felhasználják a következő sorrendben:
  1. A bejelentkezéshez kapcsolódó attribútumok (például userPrincipalName) közzé az erdőben egy olyan engedélyezett fiókkal.
  2. Az erdő egy Exchange-postaládával közzé az attribútumokat, amelyek egy Exchange globális Címlista (globális címlista) található.
  3. Ha a postaláda nem található, majd ezek az attribútumok származhatnak bármely erdőben.
  4. Az Exchange kapcsolódó attribútumok (műszaki attribútumok nem látható a GAL) közzé az erdőből származó ahol `mailNickname ISNOTNULL`.
  5. Ha több erdő, amely a tesztkörnyezettel a szabályokat, majd az összekötők (erdő) létrehozási sorrendje (dátum/időpont) meghatározására szolgál melyik erdő járul hozzá az attribútumokat.

### <a name="contact-out-of-box-rules"></a>Lépjen kapcsolatba az out-of-box szabályok
Egy kapcsolatobjektummal meg kell felelniük a következő szinkronizálni:

* A partner levelezési kell lennie. Ez az a következő szabályok ellenőrzése:
  * `IsPresent([proxyAddresses]) = True)`. A proxyAddresses attribútumot kell lennie töltve.
  * Egy elsődleges e-mail-címet vagy a proxyAddresses attribútumot, vagy a levél attribútum található. Jelenléte egy \@ annak ellenőrzéséhez, hogy a tartalom egy e-mail-címet használja. A két szabályokat True értékre kell kiértékelve.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Van-e egy bejegyzést a "SMTP:" esetén is, és egy \@ a karakterláncban található?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. A levél attribútum kitöltve, és ha igen, akkor is egy \@ a karakterláncban található?

Az alábbi kapcsolattartási objektumok **nem** szinkronizálva az Azure AD:

* `IsPresent([isCriticalSystemObject])`. Győződjön meg arról, lássa el kritikus jelzéssel kapcsolattartási objektumok szinkronizálva van. Nem lehet bármely, az alapértelmezett beállításokkal.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Ezek az objektumok az Exchange Online nem működik.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Ne szinkronizáljon a victim replikációs objektumokat.

### <a name="group-out-of-box-rules"></a>Csoport out-of-box szabályok
A csoportházirend-objektumot meg kell felelniük szinkronizálását a következő:

* 50 000-nél kevesebb a tagok kell rendelkeznie. Ez a számláló értéke a helyszíni csoport tagjainak száma.
  * Ha több tagja van, mielőtt első alkalommal induljon a szinkronizálás, a rendszer nem szinkronizálja a csoport.
  * Ha a tagok száma növekszik a kezdetben létrehozásának időpontját, majd amikor eléri a tagok 50 000 leállítja az szinkronizálása, addig, amíg a tagsági száma kisebb, mint 50 000 újra.
  * Megjegyzés: Az 50 000 tagsági száma is kényszerítve van az Azure AD által. Ön nem tudja taggal rendelkező csoportok szinkronizálása, még akkor is, ha módosítja vagy eltávolítja a szabályt.
* Ha a csoport egy **terjesztési csoport**, majd mail engedélyezve kell lennie. Lásd: [out-of-box szabályok forduljon](#contact-out-of-box-rules) esetében ez a szabály érvényben van.

A következő objektumok vannak **nem** szinkronizálva az Azure AD:

* `IsPresent([isCriticalSystemObject])`. Győződjön meg, hogy számos beépített objektumok az Active Directoryban, például a beépített Rendszergazdák csoport, nincsenek szinkronizálva.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. A DirSync által használt régi csoport.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Szerepkör-csoport.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Ne szinkronizáljon a victim replikációs objektumokat.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal out-of-box szabályok
FSP csatlakoztatott "bármely" (\*) objektum tartozik a metaverzumba. A valóságban a végzett a csatolás fordul elő, a felhasználók és biztonsági csoportokat. Ez a konfiguráció biztosítja, hogy az erdők közötti tagságok megoldott és jelölt megfelelően az Azure ad-ben.

### <a name="computer-out-of-box-rules"></a>Számítógép out-of-box szabályok
Számítógép-objektumot meg kell felelniük a következő szinkronizálni:

* `userCertificate ISNOTNULL`. Csak a Windows 10-számítógépeket adja meg ezt az attribútumot. Ez az attribútum egy értéket az összes számítógép-objektumok szinkronizálódnak.

## <a name="understanding-the-out-of-box-rules-scenario"></a>Az out-of-box szabályok forgatókönyv ismertetése
Ebben a példában használjuk egy fiókerdőben (A) a telepítésben, egy Erőforráserdő (R) és egy Azure AD-címtárral.

![Kép, a forgatókönyv leírása](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

Ebben a konfigurációban feltételezett fiókerdőben található engedélyezett fióknak és a letiltott fiók az hivatkozott postafiókkal az erőforráserdőben.

Az a célunk az alapértelmezett beállításokkal:

* Az erdő engedélyezett fiókkal az bejelentkezési kapcsolódó attribútumok legyenek szinkronizálva.
* Az attribútumokat, amelyek a globális Címlista (globális címlista) található postaláda-erdőből szinkronizálódnak. Ha nincs postaláda található bármely másik erdőben használja.
* Ha talál egy hivatkozott postafiókkal, a társított engedélyezett fiók az objektum, melyet az Azure ad Szolgáltatásba exportálni kell található.

### <a name="synchronization-rule-editor"></a>Szinkronizálási Szabályszerkesztő
A konfiguráció lehet megtekinteni és módosítani a szinkronizálási szabályok szerkesztő (SRE) eszközzel, és a egy parancsikont a start menüben található.

![Szinkronizálási Szabályszerkesztővel ikon](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

A SRE resource kit eszköz, telepítve van az Azure AD Connect-szinkronizálással. Ahhoz, akkor indul el, az ADSyncAdmins csoport tagjának kell lennie. Amikor elindul, valami ilyesmit láthatja:

![Bejövő szinkronizálási szabály](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

Ezen az ablaktáblán megjelenik a konfigurációs létrehozott összes szinkronizálási szabályokat. A tábla minden sora egy szinkronizálási szabály. Szabályok típusai mellett balra, a két különböző felsorolt: bejövő és kimenő. Bejövő és kimenő a metaverzumba nézetéből. Főleg kívánja fókusz ebben a cikkben áttekintjük a bejövő szabályok. A szinkronizálási szabályok tényleges listáját az ad-ben az észlelt séma függ. A fenti képen a fiókerdő (fabrikamonline.com) nem rendelkezik minden olyan szolgáltatások, például Exchange és a Lync, és ezeknek a szolgáltatásoknak nincs szinkronizálási szabály létrejött. Az erőforrás-erdő (res.fabrikamonline.com) szinkronizálási szabályok található ezeknek a szolgáltatásoknak. A szabály a tartalom észlelt verziójától függően eltér. Például az Exchange 2013 egy központi telepítésben vannak konfigurálva, mint az Exchange 2010/2007 további attribútumfolyamok.

### <a name="synchronization-rule"></a>Szinkronizálási szabály
A szinkronizálási szabály egy konfigurációs objektumot az attribútumok áramló, ha teljesül egy feltétel. Írja le, hogy az adott összekötőtérben objektum tartozik a metaverzumba, más néven objektumhoz tartozó is szolgál **illesztési** vagy **megfelelő**. A szinkronizálási szabályok rendelkezik egy elsőbbséget érték, amely jelzi, hogyan kapcsolódnak egymáshoz. A szinkronizálási szabály alacsonyabb numerikus értékkel rendelkezik nagyobb prioritással, és a egy attribútum folyamat ütközik, nagyobb prioritással wins az ütközés feloldásának.

Tegyük fel, tekintse meg a szinkronizálási szabály **a az AD-ből – felhasználói AccountEnabled**. Ezt a sort a SRE, és kattintson a megjelölés **szerkesztése**.

Mivel ez a szabály az out-of-box szabály, figyelmeztetés jelenik meg a szabály megnyitásakor. Nem szabad módosítani bármely [out-of-box szabályok vált](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), így a rendszer kéri, hogy Mik azok a céljaira. Ebben az esetben csak szeretné megtekinteni a szabályt. Válassza ki **nem**.

![Szinkronizálási szabályok figyelmeztetés](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

A szinkronizálási szabály rendelkezik négy konfigurációs szakaszokat: leírás, hatókörkezeléséhez kapcsolódó szűrő illesztési szabályokat és átalakításokat.

#### <a name="description"></a>Leírás
Az első szakaszban biztosít alapvető információkat, például a nevét és leírását.

![Leírás Szinkronizáló üzletiszabály-szerkesztő lap ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

Akkor is talál információt melyik csatlakoztatott rendszerrel kapcsolatos Ez a szabály kapcsolódik, amely objektum vonatkozik a csatlakoztatott rendszer típusa és a metaverzum-objektum típusaként. A metaverzum-objektum típusaként az mindig személy, függetlenül attól, hogy ha felhasználó, iNetOrgPerson, vagy forduljon a Forrásobjektum-típust. A metaverzum-objektum típusa nem kell módosítani az így létrehozott általános típusúként van. A hivatkozás típusát állítható csatlakozni, StickyJoin vagy kiépítése. Ez a beállítás működik együtt a Join szabályok szakaszt, és később tárgyalja.

Is láthatja, hogy a szinkronizálási szabály használja a jelszó-szinkronizálás. Ha egy felhasználó a szinkronizálási szabály hatókörében van, a jelszó szinkronizálása a helyszíni felhőbe (feltéve, hogy engedélyezte a jelszó-szinkronizálási szolgáltatás).

#### <a name="scoping-filter"></a>Hatókör-beállítási szűrője
A hatókör-beállítási szűrője szakasz esetén alkalmazni kell a szinkronizálási szabály konfigurálására szolgál. Az éppen megtekintett szinkronizálási szabályának neve azt jelzi, hogy csak az engedélyezett felhasználók kell alkalmazni, mivel a hatókör van konfigurálva, az AD-attribútum **userAccountControl** nem a 2 bit be kell. Amikor a szinkronizálási motor az ad-ben a felhasználó megtalál, vonatkozik-e a szinkronizálási szabály **userAccountControl** a decimális 512 (engedélyezett normál felhasználó) értékre van állítva. Nem, alkalmazza a szabályt, ha a felhasználó rendelkezik **userAccountControl** 514-es (a letiltott normál felhasználó) értékre.

![Hatókörkezeléshez kapcsolódó szinkronizálási Szabályszerkesztő lapján ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

A Hatókörszűrő van, csoportok és, amelyek ágyazhatók be. A csoporton belül minden záradékok kell teljesülnie ahhoz, hogy a szinkronizálási szabály a alkalmazni. Ha több csoport vannak definiálva, majd legalább egy csoportot kell teljesülnie ahhoz, hogy a szabály vonatkozzon. Logikai vagy azt jelenti, ki lesz értékelve, csoportok és a egy logikai között, és abban az esetben a csoporton belül. Ez a konfiguráció például tekintheti meg a kimenő szinkronizálási szabály **ki az aad-be – csoport csatlakozzon**. Több szinkronizálás szűrése csoportok, például egy, a biztonsági csoportok (`securityEnabled EQUAL True`) és a egy a terjesztési csoportok (`securityEnabled EQUAL False`).

![Hatókörkezeléshez kapcsolódó szinkronizálási Szabályszerkesztő lapján ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

Ez a szabály kell létrehozni az Azure AD-csoportok meghatározására szolgál. Terjesztési csoportok az Azure ad-vel szinkronizálandó levelezéses kell lennie, de a biztonsági csoportokat e-mailt, nem szükséges.

#### <a name="join-rules"></a>Csatlakozzon a szabályok
A harmadik szakasz objektumot az összekötőtérben hogyan kapcsolódik egymáshoz a metaverzumban található objektumok konfigurálására szolgál. A szabályt, hogy korábban áttekintett nincs bármely konfigurációs szabályok csatlakozzon, így helyette tekintse meg fog **a az AD-ből – felhasználói csatlakozás**.

![Szinkronizálási Szabályszerkesztő csatlakozzon szabályai lap ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

A csatlakozás szabály a tartalom attól függ, hogy a megfelelő jelölőnégyzetet, a telepítési varázslóban. Egy bejövő szabályt a kiértékelés a forrás összekötőtérben objektum kezdődik, és sorrendben való csatlakozás szabályok szereplő valamennyi csoport abban az esetben. Egy adatforrás-objektum megfelelően pontosan egy objektum tartozik a metaverzumba, az illesztési szabályok egyikének használatával abban az esetben, ha az objektumok csatlakoznak. Ha nem egyezik meg az összes szabály kiértékelése, a hivatkozás típusát, a leírás oldalon szolgál. Ha ez a konfiguráció értéke **kiépítése**, majd egy új objektum létrehozása a célkiszolgálón, a metaverzumba. Üzembe helyezni egy új objektumot a metaverzumba néven is ismert, hogy **projekt** a metaverzum-objektum.

Az illesztési szabályok csak egyszer értékeli ki. Ha egy összekötőtér objektuma és a metaverzum-objektum tartományhoz, maradnak illesztett mindaddig, amíg a szinkronizálási szabály hatóköre továbbra is eleget.

Szinkronizálási szabályok kiértékelésekor a megadott illesztési szabályok csak egyetlen szinkronizálási szabály hatókörében kell lennie. Ha több szinkronizálási szabály illesztési szabályokkal egy adott objektum észlelhető,-hiba lépett fel. Emiatt az ajánlott eljárás, hogy csak egyetlen szinkronizálási szabály az való csatlakozás, ha több szinkronizálási szabály hatókörébe az objektum van definiálva. Az Azure AD Connect szinkronizálási szolgáltatás out-of-box konfigurációjában, ezek a szabályok megnézzük a neve található is, és keresse meg azokat a Word **csatlakozzon** a név végén. Egy szinkronizálási szabály nélkül illesztési szabályok meghatározva az attribútumfolyamok érvényes, ha egy másik szinkronizálási szabály az objektumok összekapcsolódva, vagy kiépített egy új objektumot a cél.

Ha a fenti képen tekinti meg, látható, hogy a szabály próbál csatlakozni **objectSID** a **msExchMasterAccountSid** (Exchange) és **msRTCSIP-OriginatorSid** (Lync), akkor mit várhatóan egy fiók-erőforrás erdő topológiában. Ugyanaz a szabály az összes erdő találja. Feltételezzük, hogy minden erdő egy fiók vagy az erőforrás-erdő lehet. Ez a konfiguráció is működik, ha egyerdős él, és nem rendelkezik, amelyet egyesíteni kell fiókok.

#### <a name="transformations"></a>Az átalakítások
Transzformációs szakaszából határozza meg az összes attribútum folyamatok, amelyek érvényesek a célobjektum az objektumok tartományhoz csatlakoztatott és a Hatókörszűrő teljesül. Visszatérve a **a az AD-ből – felhasználói AccountEnabled** szinkronizálási szabály, megtalálja a következő átalakítások:

![Az átalakítások lapon szinkronban üzletiszabály-szerkesztő ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

Ez a konfiguráció a környezet-fiók-erőforrás erdő telepítés helyezi várható található engedélyezett fióknak az erdő és a egy letiltott fiók az erőforráserdőben Exchange-hez és a Lync beállításokkal. A szinkronizálási szabály egyszerre a bejelentkezéshez szükséges attribútumokat tartalmazza, és ezek az attribútumok áramlásának az erdőből, ha már van egy engedélyezett. Ezek attribútumfolyamok épülnek, több szinkronizálási szabály.

Átalakítás is rendelkezik különböző: állandó, a közvetlen és a kifejezést.

* Az adatáramlás mindig folyamatok szoftveresen kötött értéket. A fenti esetben mindig beállítja az értéket **igaz** a metaverzum-attribútum neve **accountEnabled**.
* Közvetlen folyamat mindig folyamatok az attribútum a forrásban, mint a target attribútum-van.
* A flow harmadik típus a kifejezés, és lehetővé teszi a speciális konfigurációk.

A kifejezés nyelve VBA (alkalmazások Visual Basic), így azok, akik a Microsoft Office kezelőfelülettel vagy VBScript fogja felismerni a következő formátumban. Attribútumok szögletes zárójelben [attributeName] parancsfájlblokkjában találhatók. Attribútumnevek és függvénynevek kis-és nagybetűket, de a szinkronizálási Szabályszerkesztővel kiértékeli a kifejezések, és adja meg a figyelmeztetést, ha a kifejezés nem érvényes. Összes kifejezést a beágyazott függvények ugyanabban a sorban kell megadni. A teljesítmény, a konfiguráció nyelvi megjeleníthető itt látható a folyamat pwdLastSet, de további megjegyzésekkel beszúrva:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .Net datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

Lásd: [deklaratív kiépítés kifejezéseinek ismertetése](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) a kifejezés sablonnyelvi attribútumfolyamok további tájékoztatást.

### <a name="precedence"></a>Sorrend
Most áttekintett néhány egyéni szinkronizálási szabály, de a szabályok működnek együtt a konfigurációt. Bizonyos esetekben egy attribútumérték van hozzájárult a több szinkronizálási szabály azonos target attribútuma. Ebben az esetben a attribútumsorrend meghatározni, melyik attribútum wins-szolgál. Tegyük fel tekintse meg a sourceAnchor. Ez az attribútum nem fog tudni bejelentkezni az Azure ad-ben fontos attribútum. Az Attribútumfolyam találhat meg két különböző szinkronizálási szabályok ezt az attribútumot **a az AD-ből – felhasználói AccountEnabled** és **a az AD-ből – felhasználói közös**. Szinkronizálási szabály elsőbbséget, mert a sourceAnchor attribútum van viszonyított engedélyezett fiókkal az erdőből először több objektumot csatoltak a metaverzum-objektum esetén. Ha nincsenek engedélyezett fiókok, akkor a szinkronizálási motor használja a kevésbé szinkronizálási szabály **a az AD-ből – felhasználói közös**. Ez a konfiguráció biztosítja, hogy még a letiltott fiókokat, van még egy sourceAnchor.

![Bejövő szinkronizálási szabály](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

A élveznek a szinkronizálási szabályait állítja be a csoportok a telepítővarázsló. A csoportban található összes szabály ugyanazzal a névvel rendelkezik, de más csatlakoztatott könyvtárban csatlakoznak. A telepítővarázsló lehetővé teszi a szabály **a az AD-ből – felhasználói csatlakozás** legmagasabb prioritású és végiglépkedve át az összes kapcsolódó AD-címtár. Majd folytatja az a következő csoportok szabályok egy előre meghatározott sorrendben. A szabályok egy csoporton belül ad hozzá a sorrendet, az összekötők lettek hozzáadva a varázslóban. Egy másik összekötő varázslóval kerül, ha a szinkronizálási szabályok rendezi újra, és az új összekötő szabályok utolsó az egyes csoportokban vannak beszúrva.

### <a name="putting-it-all-together"></a>A teljes kép
Most már tudjuk elegendő kapcsolatos szinkronizálási szabályok tudják a konfigurációt a különböző szinkronizálási szabályok működésének megismerése. Ha egy felhasználó és a rendszer a metaverzumba hozzájáruló attribútumok tekinti meg, a szabályokat alkalmazza a rendszer a következő sorrendben:

| Name (Név) | Megjegyzés |
|:--- |:--- |
| Az ad-felhasználó való csatlakozás |Összekötő metaverzum-összekötő terület objektumok szabály. |
| A az AD-ből – UserAccount engedélyezve |Jelentkezzen be az Azure AD szükséges attribútumok és az Office 365. Ezek az attribútumok engedélyezett fiókból szeretnénk. |
| Az AD – felhasználói közös az Exchange-ből |A globális címlistában találhatók attribútumok. Feltételezzük, hogy a data quality ajánlott az erdő, hol található, a felhasználó postaládájából. |
| Az ad-felhasználó közös |A globális címlistában találhatók attribútumok. Abban az esetben nem találtunk egy postaládát, a többi csatlakoztatott objektum az attribútum értéke közreműködhet. |
| Az AD-felhasználó Exchange-ből |Ha Exchange észlelt csak létezik. Infrastruktúra-Exchange attribútumok biztosítani. |
| Az AD-felhasználó Lync-ből |Ha a Lync észlelt csak létezik. Az összes infrastruktúra Lync attribútum biztosítani. |

## <a name="next-steps"></a>További lépések
* További információ a konfigurációs modell [ismertetése deklaratív kiépítés](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* További információ az a kifejezés nyelv [deklaratív kiépítés kifejezéseinek ismertetése](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
* Hogyan működik az out-of-box konfiguráció olvassa [ismertetése felhasználók és a kapcsolattartók](active-directory-aadconnectsync-understanding-users-and-contacts.md)
* Tekintse meg gyakorlati módosítást használata a deklaratív kiépítés [hogyan lehet módosítani az alapértelmezett konfiguráció](active-directory-aadconnectsync-change-the-configuration.md).

**Áttekintő témakör**

* [Az Azure AD Connect szinkronizálása: ismertetése, és testre szabhatja a szinkronizálás](active-directory-aadconnectsync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)

