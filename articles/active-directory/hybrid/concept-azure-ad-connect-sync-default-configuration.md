---
title: 'Azure AD Connect szinkronizálás: Az alapértelmezett konfiguráció ismertetése | Microsoft dokumentumok'
description: Ez a cikk az Azure AD Connect szinkronizálásalapértelmezett konfigurációját ismerteti.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2886b842aab81732beec0fdd7957aab8e2b4f5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76548866"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Az Azure AD Connect szinkronizálása: az alapértelmezett konfiguráció ismertetése
Ez a cikk ismerteti a beépített konfigurációs szabályokat. Dokumentálja a szabályokat, és azt, hogy ezek a szabályok hogyan befolyásolják a konfigurációt. Emellett végigvezeti az Azure AD Connect szinkronizálásalapértelmezett konfigurációját. A cél az, hogy az olvasó tisztában van azzal, hogy a deklaratív kiépítés nek nevezett konfigurációs modell hogyan működik egy valós példában. Ez a cikk feltételezi, hogy már telepítette és konfigurálta az Azure AD Connect szinkronizálását a telepítővarázsló val.

A konfigurációs modell részleteinek megértéséhez olvassa el [a Deklaratív kiépítés ismertetése című témakört.](concept-azure-ad-connect-sync-declarative-provisioning.md)

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Beépített szabályok a helyszíni és az Azure AD-ig
A következő kifejezések találhatók a beépített konfigurációban.

### <a name="user-out-of-box-rules"></a>Felhasználó beépített szabályai
Ezek a szabályok az iNetOrgPerson objektumtípusra is vonatkoznak.

A szinkronizáláshoz a felhasználói objektumnak meg kell felelnie a következőknek:

* Kell egy sourceAnchor.
* Miután az objektum létrejött az Azure AD-ben, majd sourceAnchor nem módosíthatja. Ha az érték a helyszínen módosul, az objektum leállítja a szinkronizálást, amíg a sourceAnchor vissza nem változik az előző értékre.
* A accountEnabled (userAccountControl) attribútumot kell kitölteni. A helyszíni Active Directory, ez az attribútum mindig jelen van, és kivan töltve.

A következő felhasználói objektumok **nincsenek** szinkronizálva az Azure AD-vel:

* `IsPresent([isCriticalSystemObject])`. Győződjön meg arról, hogy az Active Directory számos beépített objektuma, például a beépített rendszergazdai fiók nincs szinkronizálva.
* `IsPresent([sAMAccountName]) = False`. Győződjön meg arról, hogy a sAMAccountName attribútummal nem rendelkező felhasználói objektumok nincsenek szinkronizálva. Ez az eset gyakorlatilag csak az NT4-ről frissített tartományban történne.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Ne szinkronizálja az Azure AD Connect szinkronizálása és korábbi verziói által használt szolgáltatásfiókot.
* Ne szinkronizálja azexchange-fiókokat, amelyek nem működnének az Exchange Online-ban.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Ne szinkronizálja azokat az objektumokat, amelyek nem működnének az Exchange Online-ban.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  Ez a bitmaszk (&H21C07000) kiszűri a következő objektumokat:
  * Levelezésre alkalmas nyilvános mappa (előzetes verzióaz 1.1.524.0-s verziótól)
  * Rendszerkísérői postaláda
  * Postaláda-adatbázis-fiókláda (rendszerpostaláda)
  * Univerzális biztonsági csoport (nem vonatkozik a felhasználó, de jelen van az örökölt okok miatt)
  * Nem univerzális csoport (nem vonatkozik a felhasználó, de jelen van a régebbi okok miatt)
  * Postaláda-séma
  * Felderítési postaláda
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Ne szinkronizálja a replikáció áldozatának objektumait.

A következő attribútumszabályok érvényesek:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. A sourceAnchor attribútum nem csatolt postaládából származik. Feltételezzük, hogy ha csatolt postaládát talál, a tényleges fiók később csatlakozik.
* Az Exchange-hez kapcsolódó attribútumok csak akkor lesznek szinkronizálva, ha a **MailNickName** attribútumnak van értéke.
* Ha több erdő van, akkor az attribútumok a következő sorrendben kerülnek felhasználásra:
  1. A bejelentkezéshez kapcsolódó attribútumok (például userPrincipalName) az erdőből származnak egy engedélyezett fiókkal.
  2. Az Exchange GAL-ben (globális címlistában) található attribútumok az erdőből származnak, és exchange-postaládával rendelkeznek.
  3. Ha nem található postaláda, akkor ezek az attribútumok bármelyik erdőből származhatnak.
  4. A cserékhez kapcsolódó attribútumok (a gal-ban nem `mailNickname ISNOTNULL`látható technikai jellemzők) abból az erdőből származnak, ahol .
  5. Ha több erdő van, amely megfelel a szabályok egyikének, akkor az összekötők (erdők) létrehozási sorrendje (dátum/idő) határozza meg, hogy melyik erdő járul hozzá az attribútumokhoz. Az első csatlakoztatott erdő lesz az első szinkronizált erdő. 

### <a name="contact-out-of-box-rules"></a>Kapcsolatfelvételi szabályok
A szinkronizáláshoz a névjegyobjektumnak meg kell felelnie a következőknek:

* A kapcsolattartónak e-mailben engedélyezve kell lennie. Ezt a következő szabályokkal ellenőrizzük:
  * `IsPresent([proxyAddresses]) = True)`. A proxyAddresses attribútumot ki kell tölteni.
  * Az elsődleges e-mail cím megtalálható a proxyAddresses attribútumban vagy a mail attribútumban. A megléte \@ annak ellenőrzésére szolgál, hogy a tartalom e-mail cím-e. A két szabály egyikét Igaz értékre kell kiértékelni.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Van olyan bejegyzés, amelynek "SMTP:" (SMTP:) van, és ha van, megtalálható-e a \@ karakterláncban?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. A mail attribútum feltöltve van, és \@ ha az, akkor megtalálható a karakterláncban?

A következő névjegyobjektumok **nincsenek** szinkronizálva az Azure AD-vel:

* `IsPresent([isCriticalSystemObject])`. Győződjön meg arról, hogy a kritikusként megjelölt névjegyobjektumok nincsenek szinkronizálva. Nem lehet olyan, az alapértelmezett konfiguráció.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Ezek az objektumok nem működnek az Exchange Online-ban.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Ne szinkronizálja a replikáció áldozatának objektumait.

### <a name="group-out-of-box-rules"></a>Csoportbeépített szabályok
A csoportobjektumnak a szinkronizáláshoz meg kell felelnie a következőknek:

* Kevesebb, mint 50.000 tagja lehet. Ez a szám a helyszíni csoport tagjainak száma.
  * Ha több tagja van a szinkronizálás első indítása előtt, a csoport nem lesz szinkronizálva.
  * Ha a tagok száma az eredeti létrehozásidőpontjától növekszik, akkor amikor eléri az 50 000 tagot, leállítja a szinkronizálást, amíg a tagsági szám ismét 50 000-nél alacsonyabb nem lesz.
  * Megjegyzés: Az 50 000-es tagsági számot az Azure AD is kényszeríti. A több taggal rendelkező csoportok akkor sem szinkronizálhatók, ha módosítja vagy eltávolítja ezt a szabályt.
* Ha a csoport **terjesztési csoport,** akkor a levelezésnek is engedélyezve kell lennie. Lásd: [Kapcsolaton kívüli szabályok](#contact-out-of-box-rules) ehhez a szabályhoz érvényesítve.

A következő csoportobjektumok **nincsenek** szinkronizálva az Azure AD-vel:

* `IsPresent([isCriticalSystemObject])`. Győződjön meg arról, hogy az Active Directory számos beépített objektuma, például a beépített rendszergazdák csoportja nincs szinkronizálva.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. A DirSync által használt örökölt csoport.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Szerepkörcsoport.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Ne szinkronizálja a replikáció áldozatának objektumait.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal out-of-box szabályok
Az FSP-k a metaverzumban lévő "any" ( )\*objektumhoz csatlakoznak. A valóságban ez az illesztés csak a felhasználók és a biztonsági csoportok esetében történik. Ez a konfiguráció biztosítja, hogy az erdők közötti tagságok feloldása és megfelelően jelennek meg az Azure AD-ben.

### <a name="computer-out-of-box-rules"></a>Számítógép-beépített szabályok
A számítógép-objektumnak a szinkronizáláshoz meg kell felelnie a következőknek:

* `userCertificate ISNOTNULL`. Csak a Windows 10 számítógépek népesítik be ezt az attribútumot. A rendszer szinkronizálja az attribútumban szereplő értékkel rendelkező összes számítógép-objektumot.

## <a name="understanding-the-out-of-box-rules-scenario"></a>A beépített szabályok forgatókönyvének ismertetése
Ebben a példában egy központi telepítést használunk egy fiókerdővel (A), egy erőforráserdővel (R) és egy Azure AD-könyvtárral.

![Kép a forgatókönyv leírásával](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

Ebben a konfigurációban feltételezzük, hogy van egy engedélyezett fiók a fiókerdőben, és egy letiltott fiók az erőforráserdőben egy csatolt postaládával.

Célunk az alapértelmezett konfigurációval:

* A bejelentkezéshez kapcsolódó attribútumok szinkronizálódnak az erdőből az engedélyezett fiókkal.
* A gez-ben (globális címlistában) található attribútumok szinkronizálódnak az erdőből a postaládával. Ha nem található postaláda, a rendszer más erdőt használ.
* Ha egy csatolt postaláda található, a csatolt engedélyezett fiókot kell találni az objektum ot az Azure AD-be kell exportálni.

### <a name="synchronization-rule-editor"></a>Szinkronizálási szabály szerkesztője
A konfiguráció megtekinthető és módosítható a Szinkronizálási szabályok szerkesztő (SRE) eszközzel, és egy parancsikon t a Start menüben található.

![Szinkronizálási szabályok szerkesztője ikon](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

Az SRE egy erőforrás-készlet eszköz, és az Azure AD Connect szinkronizálásával van telepítve. Az elindítható elindításához az ADSyncAdmins csoport tagjának kell lennie. Amikor elkezdődik, valami ilyesmit látsz:

![Bejövő szinkronizálási szabályok](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

Ezen az ablaktáblán láthatja a konfigurációhoz létrehozott összes szinkronizálási szabályt. A tábla minden sora egy szinkronizálási szabály. A szabálytípusok csoportbal a két különböző típus szerepel a listában: Bejövő és Kimenő. A bejövő és a kimenő a metaverzum nézetéből van. Ebben az áttekintésben elsősorban a bejövő szabályokra fog összpontosítani. A szinkronizálási szabályok tényleges listája az AD-ben észlelt sémáttól függ. A fenti képen a fiókerdő (fabrikamonline.com) nem rendelkezik szolgáltatásokkal, például az Exchange és a Lync, és nem hoztak létre szinkronizálási szabályokat ezekhez a szolgáltatásokhoz. Az erőforráserdőben (res.fabrikamonline.com) azonban megtalálja a szolgáltatások szinkronizálási szabályait. A szabályok tartalma az észlelt verziótól függően eltérő. Az Exchange 2013-as környezetben például több attribútumfolyamat van konfigurálva, mint az Exchange 2010/2007-ben.

### <a name="synchronization-rule"></a>Szinkronizálási szabály
A szinkronizálási szabály olyan konfigurációs objektum, amelynek attribútumai akkor áramlanak, ha egy feltétel teljesül. Azt is leírja, hogy az összekötő térben lévő objektum hogyan kapcsolódik a metaverzumban lévő objektumhoz, más néven **illesztéshez** vagy **egyezéshez.** A szinkronizálási szabályok prioritási értékkel rendelkeznek, amely azt jelzi, hogy hogyan viszonyulnak egymáshoz. Az alacsonyabb numerikus értékkel rendelkező szinkronizálási szabály elsőbbséget élvez, és egy attribútum-folyamatütközésesetén a magasabb prioritás nyeri az ütközésfeloldást.

Például tekintse meg a Szinkronizálási szabály **az AD – User AccountEnabled**. Jelölje meg ezt a sort az SRE-ben, és válassza a **Szerkesztés**lehetőséget.

Mivel ez a szabály egy beépített szabály, figyelmeztetést kap a szabály megnyitásakor. Ne [változtasson a beépített szabályokon,](how-to-connect-sync-best-practices-changing-default-configuration.md)ezért megkérdezi, hogy mik a szándékai. Ebben az esetben csak a szabályt szeretné megtekinteni. Válassza a **Nem** lehetőséget.

![Szinkronizálási szabályok figyelmeztetés](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

A szinkronizálási szabály négy konfigurációs szakaszból állt: Leírás, Hatókör-szűrő, Illesztési szabályok és Átalakítások.

#### <a name="description"></a>Leírás
Az első szakasz alapvető információkat tartalmaz, például nevet és leírást.

![Leírás lap a Szinkronizálási szabályszerkesztőben](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

Azt is megkeresheti, hogy a szabály melyik csatlakoztatott rendszerhez kapcsolódik, melyik objektumtípusra vonatkozik a csatlakoztatott rendszerben, valamint a metaverzum-objektum típusát. A metaverzum objektumtípusa mindig személy, függetlenül attól, hogy a forrásobjektum típusa felhasználó, iNetOrgPerson vagy kapcsolattartó. A metaverzum objektumtípusa soha nem változhat meg, így általános típusként jön létre. A kapcsolat típusa beállítható Csatlakozás, StickyJoin vagy Provision. Ez a beállítás együttműködik a Join Rules (Illesztési szabályok) szakaszsal, és később jelenik meg.

Azt is láthatja, hogy ez a szinkronizálási szabály jelszó-szinkronizálásra szolgál. Ha egy felhasználó hatókörben van ehhez a szinkronizálási szabályhoz, a jelszó szinkronizálása a helyszíni felhőből történik (feltéve, hogy engedélyezte a jelszószinkronizálási funkciót).

#### <a name="scoping-filter"></a>Hatókörszűrő
A Hatókörszűrő szakasz annak konfigurálására szolgál, hogy mikor kell alkalmazni a szinkronizálási szabályt. Mivel a megkeresett szinkronizálási szabály neve azt jelzi, hogy csak az engedélyezett felhasználókra kell alkalmazni, a hatókör úgy van beállítva, hogy az AD attribútum **userAccountControl** attribútuma ne legyen beállítva a 2. Amikor a szinkronizálási motor talál egy felhasználót az AD-ben, akkor alkalmazza ezt a szinkronizálási szabályt, ha a **userAccountControl** értéke 512 (engedélyezett normál felhasználó). Nem alkalmazza a szabályt, ha a felhasználó **userAccountControl** beállítása 514 (letiltott normál felhasználó).

![A Hatókör lap a Szinkronizálási szabályszerkesztőben](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

A hatókörszűrő csoportosítható csoportokat és záradékokat rendelkezik. A csoporton belüli összes záradéknak meg kell felelnie ahhoz, hogy a szinkronizálási szabály alkalmazása meglegyen. Ha több csoport van definiálva, akkor legalább egy csoportnak meg kell felelnie ahhoz, hogy a szabály vonatkozzon. Ez azt, hogy egy logikai VAGY kiértékelése a csoportok között, és egy logikai ÉS kiértékelése egy csoporton belül. Erre a konfigurációra példa a kimenő szinkronizálási kikötési **kibontása AAD - csoportcsatlakozás**. Több szinkronizálási szűrőcsoport létezik, például egy`securityEnabled EQUAL True`biztonsági csoporthoz (`securityEnabled EQUAL False`) és egy a terjesztési csoportokhoz ( ).

![A Hatókör lap a Szinkronizálási szabályszerkesztőben](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

Ez a szabály határozza meg, hogy mely csoportokat kell kiépíteni az Azure AD-be. Terjesztési csoportok engedélyezni kell a levelezést, hogy szinkronizálható legyen az Azure AD-vel, de a biztonsági csoportok egy e-mail nem szükséges.

#### <a name="join-rules"></a>Csatlakozás imént
A harmadik szakasz annak konfigurálására szolgál, hogy az összekötő térben lévő objektumok hogyan viszonyulnak a metaverzumobjektumaihoz. A korábban megvizsgált szabály nem rendelkezik a csatlakozási szabályok konfigurációjával, ezért ehelyett az **AD - User Join**.

![Illesztési szabályok lap a Szinkronizálási szabályszerkesztőben](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

Az illesztési szabály tartalma a telepítővarázslóban kiválasztott egyeztetési beállítástól függ. Bejövő szabály esetén a kiértékelés egy objektummal kezdődik a forrásösszekötő térben, és az illesztési szabályok minden csoportja egymás után kiértékelve lesz kiértékelve. Ha egy forrásobjektum kiértékelése pontosan egy objektumnak felel meg a metaverzumban az illesztési szabályok egyikével, az objektumok összekapcsolódnak. Ha az összes szabály ki lett értékelve, és nincs egyezés, akkor a program a leírás oldalon lévő Hivatkozástípust használja. Ha ez a konfiguráció provision ( **kiépítés)** értékre van állítva, akkor egy új objektum jön létre a célban, a metaverzum, ha az illesztési feltételeklegalább egy attribútuma jelen van (van értéke). Új objektum létesítése a metaverzumba is ismert, mint egy objektum **kivetítése** a metaverzumra.

Az illesztési szabályok kiértékelése csak egyszer történik meg. Összekötő térobjektum és metaverzum-objektum összefűzése esetén azok mindaddig összemaradnak, amíg a szinkronizálási szabály hatóköre továbbra is teljesül.

A szinkronizálási szabályok kiértékelésekor csak egy szinkronizálási szabálynak kell lennie a definiált illesztési szabályokkal. Ha egy objektumhoz több szinkronizálási szabály található illesztési szabályokkal, a rendszer hibát jelez. Ezért az ajánlott eljárás az, hogy csak egy szinkronizálási szabály illesztési definiált, ha több szinkronizálási szabályok hatóköre egy objektum. Az Azure AD Connect-szinkronizálás beépített konfigurációjában ezek a szabályok a név megkeresésével és a **Csatlakozás** a név végén található szavak kal. A szinkronizálási szabály nélkül illesztési szabályok definiálva alkalmazza az attribútum folyamatok, ha egy másik szinkronizálási szabály összeaz objektumokat, vagy kiépített egy új objektumot a cél.

Ha a fenti képet nézi, láthatja, hogy a szabály az **objectSID-hez** próbál csatlakozni **az msExchMasterAccountSid** (Exchange) és **az msRTCSIP-OriginatorSid** (Lync) segítségével, ami az, amit egy fiókerőforrás-erdőtopológiában elvárunk. Minden erdőben ugyanazt a szabályt találja. A feltételezés az, hogy minden erdő lehet egy fiók vagy erőforrás erdő. Ez a konfiguráció akkor is működik, ha egyetlen erdőben lévő fiókokkal rendelkezik, és nem kell csatlakoznia.

#### <a name="transformations"></a>Átalakítások
Az átalakítási szakasz határozza meg az összes attribútumfolyamatokat, amelyek a célobjektumra vonatkoznak, amikor az objektumok összevannak fűzve, és a hatókörszűrő teljesül. Visszalépés az **AD - User AccountEnabled** Synchronization Rule alkalmazáshoz a következő átalakításokat találja:

![Átalakítások lap a Szinkronizálási szabályszerkesztőben](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

Ahhoz, hogy ezt a konfigurációt kontextusba helyezze, a Fiók-erőforrás erdő telepítésében várhatóan talál egy engedélyezett fiókot a fiókerdőben, és egy letiltott fiókot az erőforráserdőben az Exchange és a Lync beállításaival. A megkeresett szinkronizálási szabály tartalmazza a bejelentkezéshez szükséges attribútumokat, és ezeknek az attribútumoknak abból az erdőből kell folyniuk, ahol engedélyezett fiók található. Mindezek az attribútumfolyamatok egy szinkronizálási szabályban vannak összehelyezve.

Az átalakítások különböző típusúak lehetnek: Állandó, Közvetlen és Kifejezés.

* Az állandó folyamat mindig egy kódolt értéket folyik. A fenti esetben mindig beállítja az **Igaz** értéket a **accountEnabled**nevű metaverzum attribútumban.
* A közvetlen folyamat mindig a forrásattribútum értékét a célattribútumhoz irányítja.
* A harmadik folyamattípus a Kifejezés, és fejlettebb konfigurációkat tesz lehetővé.

A kifejezés nyelve VBA (Visual Basic for Applications), így a Microsoft Office vagy a VBScript területén jártas személyek felismerik a formátumot. Az attribútumok szögletes zárójelek közé vannak bezárva, [attributeName]. Az attribútumnevek és a függvénynevek nem veszik figyelembe a kis- és nagybetűket, de a Szinkronizálási szabályok szerkesztője kiértékeli a kifejezéseket, és figyelmeztetést ad meg, ha a kifejezés érvénytelen. Minden kifejezés egyetlen sorban van kifejezve egymásba ágyazott függvényekkel. A konfigurációs nyelv teljesítményének megjelenítéséhez itt van a pwdLastSet folyamata, de további megjegyzésekkel:

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .NET datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

[A deklaratív kiépítési kifejezések ismertetése](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) című témakörben további információt talál az attribútumfolyamatok kifejezésnyelvével kapcsolatos további információkért.

### <a name="precedence"></a>Precedencia
Most már megtekintett néhány egyedi szinkronizálási szabályok, de a szabályok együtt működnek a konfigurációban. Bizonyos esetekben egy attribútumértéket atöbbszörös szinkronizálási szabályok adnak hozzá ugyanahhoz a célattribútumhoz. Ebben az esetben az attribútumprioritás határozza meg, hogy melyik attribútum nyer. Példaként tekintse meg az attribútum sourceAnchor. Ez az attribútum egy fontos attribútum, amely képes bejelentkezni az Azure AD-be. Ehhez az attribútumhoz attribútumfolyamatot két különböző szinkronizálási szabályban, **Az AD -In – User AccountEnabled** és In from AD – User Common ( **In- AD – User Common**. A szinkronizálási szabály prioritása miatt a sourceAnchor attribútum először egy engedélyezett fiókkal járul hozzá az erdőből, amikor több objektum csatlakozik a metaverzum-objektumhoz. Ha nincsenek engedélyezett fiókok, akkor a szinkronizálási motor az **AD – User Common ( User Common**) összes szinkronizálási szabályt használja. Ez a konfiguráció biztosítja, hogy még a le tiltott fiókok esetében is van sourceAnchor.

![Bejövő szinkronizálási szabályok](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

A szinkronizálási szabályok prioritását a telepítővarázsló csoportokban állítja be. A csoport összes szabályának ugyanaz a neve, de különböző csatlakoztatott könyvtárakhoz kapcsolódnak. A telepítővarázsló megadja a szabályt **az AD-ből – A felhasználó csatlakozása** a legmagasabb prioritást, és végigjelzi az összes csatlakoztatott AD-könyvtárat. Ezután a következő szabálycsoportokkal egy előre meghatározott sorrendben folytatódik. Egy csoporton belül a szabályok abban a sorrendben kerülnek hozzáadásra, ahogy az összekötők a varázslóban vannak hozzáadva. Ha egy másik összekötőt ad hozzá a varázslón keresztül, a szinkronizálási szabályok átrendeződnek, és az új összekötő szabályai az egyes csoportokba kerülnek utoljára.

### <a name="putting-it-all-together"></a>Végső összeállítás
Most már eleget tudunk a szinkronizálási szabályokról ahhoz, hogy megértsük, hogyan működik a konfiguráció a különböző szinkronizálási szabályokkal. Ha megnéz egy felhasználót és a metaverzumhoz megadott attribútumokat, a szabályok a következő sorrendben lesznek alkalmazva:

| Név | Megjegyzés |
|:--- |:--- |
| AD-ből – Felhasználói csatlakozás |Az összekötő térobjektumok metaverzummal való összekapcsolásához szükséges szabály. |
| AD-ből – UserAccount engedélyezve |Az Azure AD-be és az Office 365-be való bejelentkezéshez szükséges attribútumok. Ezeket az attribútumokat az engedélyezett fiókból szeretnénk. |
| Ad-ból – Az Exchange-ből gyakori felhasználó |A globális címlistában található attribútumok. Feltételezzük, hogy az adatok minősége a legjobb abban az erdőben, ahol megtaláltuk a felhasználó postaládáját. |
| Az AD - User Common |A globális címlistában található attribútumok. Abban az esetben, ha nem találtunk postaládát, bármely más egyesített objektum hozzájárulhat az attribútum értékéhez. |
| Az AD -tól – Felhasználói csere |Csak akkor létezik, ha a rendszer észlelte az Exchange-et. Az összes infrastruktúra Exchange-attribútumot forgalmaz. |
| AD -ból – Felhasználói lync |Csak akkor létezik, ha a Rendszer a Lyncet észlelte. Az infrastruktúra Lync-attribútumait forgalmazi. |

## <a name="next-steps"></a>További lépések
* A konfigurációs modellről a [Deklaratív kiépítés ismertetése](concept-azure-ad-connect-sync-declarative-provisioning.md)című témakörben olvashat bővebben.
* A kifejezés nyelvéről a [Deklaratív kiépítési kifejezések ismertetése című](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)témakörben olvashat bővebben.
* A [felhasználók és partnerek ismertetése](concept-azure-ad-connect-sync-user-and-contacts.md) párbeszédpanelen a beépített konfiguráció működésének folytatása
* Tekintse meg, hogyan lehet gyakorlati módosítást eszközolni a deklaratív kiépítés használatával [az Alapértelmezett konfiguráció módosítása](how-to-connect-sync-change-the-configuration.md)című témakörben.

**Áttekintő témakörök**

* [Azure AD Connect szinkronizálás: A szinkronizálás megértése és testreszabása](how-to-connect-sync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md)

