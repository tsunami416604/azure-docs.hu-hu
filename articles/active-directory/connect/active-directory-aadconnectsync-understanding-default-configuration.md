---
title: "Azure AD Connect szinkronizálása: az alapértelmezett konfiguráció ismertetése |} Microsoft Docs"
description: "Ez a cikk ismerteti az Azure AD Connect szinkronizálási szolgáltatás az alapértelmezett konfigurációt."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: dba7a6fcf936e9610a5f1f04e367d32e9aae6643
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Az Azure AD Connect szinkronizálása: az alapértelmezett konfiguráció ismertetése
Ez a cikk ismerteti a out-of-box konfigurációs szabályok. Az dokumentumokat, és ezek a szabályok milyen hatással van a konfigurációs szabályok. Azt is bemutatja, hogyan kell az Azure AD Connect szinkronizálási szolgáltatás az alapértelmezett konfigurációját. Az célja, hogy az olvasó együttműködik a deklaratív kiépítés nevű konfigurációs modell egy valós példában alakulását. Ez a cikk feltételezi, hogy már telepített, és állítsa be a telepítési varázsló segítségével az Azure AD Connect sync.

A konfigurációs modell megértéséhez, olvassa el [ismertetése deklaratív kiépítés](active-directory-aadconnectsync-understanding-declarative-provisioning.md).

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>Az Azure AD helyszíni Out-of-box szabályok
Az alábbi kifejezések out-of-box konfigurációjában található.

### <a name="user-out-of-box-rules"></a>Felhasználói out-of-box szabályok
Ezek a szabályok is vonatkoznak az iNetOrgPerson objektumtípus.

Olyan felhasználói objektum meg kell felelnie a következő szinkronizálásának engedélyezése:

* Rendelkeznie kell egy sourceAnchor.
* Az objektum létrehozása az Azure ad-ben, után sourceAnchor nem módosítható. Módosult a helyi érték esetén az objektum leállítja a szinkronizálása, addig, amíg a sourceAnchor vissza az előző értékre változik.
* Kell rendelkeznie a accountEnabled (userAccountControl) attribútum feltöltve. A helyszíni Active Directory, az ezt az attribútumot mindig jelen és ki van töltve.

Az alábbi felhasználói objektumok **nem** szinkronizálva az Azure AD:

* `IsPresent([isCriticalSystemObject])`. Sok out-of-box objektumok az Active Directoryban, például a beépített rendszergazdai fiók érdekében nincsenek szinkronizálva.
* `IsPresent([sAMAccountName]) = False`. Győződjön meg arról, nincs sAMAccountName attribútummal rendelkező felhasználói objektumok nincsenek szinkronizálva. Ebben az esetben csak gyakorlatilag történne NT4 verzióról frissített tartományban.
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Ne szinkronizáljon az Azure AD Connect szinkronizálási szolgáltatás és a korábbi verziói által használt szolgáltatásfiók.
* Csatlakoztatás nem működik az Exchange Online Exchange-fiókok nem szinkronizálja.
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Csatlakoztatás nem működik az Exchange Online-objektumok nem szinkronizálja.
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  A bitmaszk (& H21C07000) a következő objektumok ki fog szűrni:
  * Levelezési nyilvános mappában (az előzetes verzió frissítésétől 1.1.524.0 verzió)
  * Rendszer kísérő postaláda
  * Postaláda-adatbázis postaláda (rendszer postaláda)
  * Az univerzális biztonsági csoport (egy felhasználó nem tudnák alkalmazni, de nincs örökölt összetevők miatt)
  * Nem-univerzális csoport (egy felhasználó nem tudnák alkalmazni, de nincs örökölt összetevők miatt)
  * Postaláda-terv
  * Felderítési postaláda
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Minden replikációs áldozata objektumok nem szinkronizálja.

A következő attribútum szabályok vonatkoznak:

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. A sourceAnchor attribútum nem része volt, a csatolt postaládából. Feltételezzük, hogy ha a hivatkozott postafiókkal talált, a tényleges fiók egy tartományhoz később.
* Exchange kapcsolódó attribútumok csak vannak szinkronizálva, ha az attribútum **mailNickName** értéke.
* Ha több erdő, majd attribútumok során a következő sorrendben:
  1. Bejelentkezési kapcsolódó attribútumok (például userPrincipalName) közzé az erdőből engedélyezett fiókkal.
  2. Az erdő egy Exchange-postaládával közzé attribútumokat, amelyek az Exchange globális Címlista (globális címlista) található.
  3. Ha nincs postaláda található, majd ezek az attribútumok származhatnak bármely erdőben.
  4. Exchange kapcsolódó attribútumok (nem látható a globális Címlista műszaki attribútumok) közzé az erdőből ahol `mailNickname ISNOTNULL`.
  5. Ha több erdő, amely kielégítéséhez a szabályokat, az összekötők (erdők) létrehozási sorrendjének (dátum/idő) használatos határozza meg, melyik erdő hozzájárul az attribútumokat.

### <a name="contact-out-of-box-rules"></a>Lépjen kapcsolatba az out-of-box szabályok
Egy partner objektuma meg kell felelnie a szinkronizálásra a következő:

* Az ügyfél levelezési kell lennie. A következő szabályokkal igazolható:
  * `IsPresent([proxyAddresses]) = True)`. A proxyAddresses attribútum ki kell tölteni.
  * Egy elsődleges e-mail címét vagy a proxyAddresses, vagy a levél attribútum találhatók. A jelenléte egy @ segítségével győződjön meg arról, hogy a tartalmat egy e-mail címet. A két szabályokat igaz értékre kell kiértékelve.
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. Van-e bejegyzés "SMTP:", és ha nincs, egy @ kell a karakterláncban található?
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. A levél attribútum feltöltve, és ha igen, képes egy @ kell a karakterláncban található?

Az alábbi kapcsolattartási objektumok **nem** szinkronizálva az Azure AD:

* `IsPresent([isCriticalSystemObject])`. Győződjön meg arról, lássa el kritikus jelzéssel kapcsolattartási objektumok szinkronizálja a rendszer. Nem lehet bármely, az alapértelmezett beállításokkal.
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. Ezek az objektumok az Exchange Online nem működik.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Minden replikációs áldozata objektumok nem szinkronizálja.

### <a name="group-out-of-box-rules"></a>Csoport out-of-box szabályok
A csoportházirend-objektumot meg kell felelnie a szinkronizálásra a következő:

* 50 000-nél kevesebb tagot kell rendelkeznie. A számláló értéke a helyi csoport tagjainak számát.
  * Ha több tagja van, mielőtt megkezdődik az első, a csoport nincs szinkronizálva.
  * Ha a tagok száma nő, amikor eredeti létrehozása, majd amikor eléri a 50 000 tagok a szinkronizálása, addig, amíg a tagság száma nem 50 000-nél alacsonyabb újra leállítja.
  * Megjegyzés: Az 50 000 tagsági számát is kényszeríti ki az Azure AD. Nem tudunk csoportok szinkronizálása több tag, még akkor is, ha módosítja vagy szabály eltávolításához.
* Ha a csoport egy **terjesztési csoport**, majd mail engedélyezve kell lennie. Lásd: [out-of-box szabályok forduljon](#contact-out-of-box-rules) esetében ez a szabály érvényesül.

A következő objektum **nem** szinkronizálva az Azure AD:

* `IsPresent([isCriticalSystemObject])`. Sok out-of-box objektumok az Active Directoryban, például a beépített Rendszergazdák csoport érdekében nincsenek szinkronizálva.
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. A DirSync által használt örökölt csoport.
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. Szerepkör-csoport.
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. Minden replikációs áldozata objektumok nem szinkronizálja.

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal out-of-box szabályok
FSP tartományhoz csatlakoztatott "bármely" (\*) objektum a metaverzumban. A valóságban ezt az összekapcsolást csak akkor fordul elő, a felhasználók és biztonsági csoportok. Ez a konfiguráció biztosítja a erdők közötti tagságát feloldani és képviselt megfelelő Azure AD-ben.

### <a name="computer-out-of-box-rules"></a>Számítógép out-of-box szabályok
Számítógép-objektumot meg kell felelnie a szinkronizálásra a következő:

* `userCertificate ISNOTNULL`. Csak Windows 10-es számítógépeken feltöltése ehhez az attribútumhoz. Ez az attribútum egy értéket az összes számítógép-objektumok szinkronizálva.

## <a name="understanding-the-out-of-box-rules-scenario"></a>A out-of-box szabályokat forgatókönyvben ismertetése
Ebben a példában egy fiókerdő (A) a telepítésben, egy Erőforráserdő (R) és egy Azure AD-címtár használunk.

![Forgatókönyv leírása a kép](./media/active-directory-aadconnectsync-understanding-default-configuration/scenario.png)

Ebben a konfigurációban feltételezhető a fiókerdőben engedélyezett fióknak és a letiltott fiók az hivatkozott postafiókkal az erőforráserdőben.

Az alapértelmezett konfigurációnál célunk:

* Bejelentkezési kapcsolódó attribútumok szinkronizálva az erdőből, az engedélyezett fiókkal.
* Itt található: a globális Címlista (globális címlista) attribútumok az erdőből a postaládával szinkronizálva. Ha nincs postaláda található, más erdőkben szolgál.
* Ha hivatkozott postafiókkal található, a csatolt engedélyezett fiók az objektum az Azure ad Szolgáltatásba exportálni kell található.

### <a name="synchronization-rule-editor"></a>Szinkronizálási szabály szerkesztő
A konfigurációs tekinthetők meg és szinkronizálási szabályok Editor (SRE) eszközzel megváltozott, és a start menüben található egy parancsikont.

![Szinkronizálási szabályok szerkesztő ikon](./media/active-directory-aadconnectsync-understanding-default-configuration/sre.png)

A SRE resource kit eszköz, az Azure AD Connect szinkronizálási szolgáltatás telepítve van. Lesz elindítható, hogy az ADSyncAdmins csoport tagjának kell lennie. Ha kezdődik, megjelenik az alábbihoz hasonló:

![Bejövő szinkronizálási szabály](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

Az ezen az ablaktáblán tekintse meg a konfigurációs létrehozott összes szinkronizálási szabályait. A táblázat minden sora egy szinkronizálási szabály. A szabálytípusok balra, a két különböző felsorolt: bejövő és kimenő. Bejövő és kimenő a metaverzumba nézetéből. Főleg kívánja fókusz Ez az Áttekintés a bejövő szabályok. A szinkronizálási szabályok tényleges listáját az ad-ben az észlelt séma függ. A fenti kép a fiókerdő (fabrikamonline.com) nem rendelkezik a szolgáltatások, például Exchange és a Lync, és a szolgáltatások nem szinkronizálási szabály létrejött. Az erőforrás-erdő (res.fabrikamonline.com) szinkronizálási szabályok keresése a szolgáltatások. A szabályok tartalma észlelt verziójától függően eltérnek. Például az Exchange 2013-as központi telepítés nincsenek további attribútumfolyamok, mint az Exchange 2010/2007 konfigurálva.

### <a name="synchronization-rule"></a>Szinkronizálási szabály
A szinkronizálási szabály attribútumok kifelé áramló, ha egy feltétel teljesül-e a konfigurációs objektum. Ismerteti, hogyan viszonyul a kapcsolódási térbe objektumára tartozik a metaverzumba, úgynevezett objektum is szolgál **illesztési** vagy **megfelelő**. A szinkronizálási szabályok rendelkezik egy sorrend érték, amely jelzi, hogyan kapcsolódnak egymáshoz. A szinkronizálási szabály alacsonyabb numerikus értéke magasabb prioritással rendelkezik, és attribútum folyamata ütköznek, a magasabb prioritással wins az ütközések feloldása.

Tegyük fel, nézze meg a szinkronizálási szabály **a az AD-felhasználó AccountEnabled**. A sor a SRE, és válasszon megjelölése **szerkesztése**.

Mivel ez a szabály az out-of-box szabály, riasztást küld a szabály megnyitásakor. Nem szabad módosítani a [vált, out-of-box szabályok](active-directory-aadconnectsync-best-practices-changing-default-configuration.md), így a rendszer kéri, hogy Mik azok a céljaira. Ebben az esetben csak megtekinteni kívánt a szabályt. Válassza ki **nem**.

![Szinkronizálási szabályok figyelmeztetés](./media/active-directory-aadconnectsync-understanding-default-configuration/warningeditrule.png)

A szinkronizálási szabály rendelkezik négy konfigurációs szakasz: hatókörére szűrő illesztési szabályok és átalakítások ismertetését.

#### <a name="description"></a>Leírás
Az első témakör alapvető információkat, például a nevét és leírását.

![Leírás szinkronban szabály szerkesztő lap ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruledescription.png)

Adatokat kaphat mely csatlakoztatott rendszerre vonatkozó Ez a szabály kapcsolódik, amely az objektum típusa a csatlakoztatott rendszer vonatkozik, és a metaverzum-objektum típusaként. A metaverzum-objektum típusaként esetén mindig személy függetlenül a Forrásobjektum-típust adott felhasználó, iNetOrgPerson, vagy lépjen kapcsolatba. A metaverzum-objektum típusaként soha nem kell módosítani, ezért azt a program létrehoz egy általános típus. A kapcsolat típusa csatlakozni, StickyJoin vagy rendelkezés állítható be. Ez a beállítás a csatlakozás szabályok szakasz együtt működik, amelyeket később.

Láthatja, hogy a szinkronizálási szabály használja a jelszó-szinkronizálást. Ha egy felhasználó a szinkronizálási szabály hatóköre, a jelszó szinkronizálása a helyszíni felhőbe (feltéve, hogy engedélyezte a jelszó-szinkronizálási szolgáltatás).

#### <a name="scoping-filter"></a>Hatókör-beállítási szűrője
A hatókör-beállítási szűrője szakasz segítségével konfigurálhatja, amikor egy szinkronizálási szabály vonatkozik. Az éppen megtekintett szinkronizálási szabály nevét jelzi, hogy csak az engedélyezett felhasználók kell alkalmazni, mivel a hatókör van konfigurálva, az AD attribútum **userAccountControl** nem a 2 bit be kell. Ha a szinkronizálási motor AD egy felhasználó talál, a szinkronizálás érvényes szabály **userAccountControl** a decimális 512 (engedélyezett normál felhasználói) értékre van beállítva. Nem alkalmazható a szabályt, ha a felhasználó rendelkezik-e **userAccountControl** 514 (letiltott normál felhasználói) értékre.

![Szinkronizálási szabály szerkesztőben tartalmazó lap ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilter.png)

A hatókört szűrőre csoportok és záradékokat, amelyek egymásba ágyazható. Csoportban lévő összes záradékok kell teljesülnie ahhoz, hogy a szinkronizálási szabályt alkalmazni. Ha több csoport vannak definiálva, majd legalább egy csoportot kell teljesülnie ahhoz a szabályt alkalmazni. Ez azt jelenti, hogy logikai vagy csoportok és a logikai között történik, és ki lesz értékelve a csoporton belül. Ez a konfiguráció példát a kimenő szinkronizálási szabály található **ki az aad-be – csoport csatlakozás**. Több szinkronizálási szűrő csoportok, például egy, a biztonsági csoportok (`securityEnabled EQUAL True`) és egy terjesztési csoportok (`securityEnabled EQUAL False`).

![Szinkronizálási szabály szerkesztőben tartalmazó lap ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulescopingfilterout.png)

Ez a szabály melyik csoportok kell építhető ki az Azure AD azonosítására szolgál. Terjesztési csoportok mail az Azure ad-vel szinkronizálandó engedélyezve kell lennie, de a biztonsági csoportok egy e-mailt nincs szükség.

#### <a name="join-rules"></a>Csatlakozás a szabályok
A harmadik szakasz hogyan objektumokat a kapcsolódási térbe kapcsolódnak-e a metaverzumban található objektumok konfigurálására szolgál. A szabály ellenőrizte korábban nem rendelkezik, ehelyett meg kívánja csatlakozás szabályok beállításra **a az AD-felhasználó csatlakozás**.

![Szinkronizálási szabály szerkesztő csatlakozott szabályai lap ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulejoinrules.png)

Az illesztési szabály a tartalom a telepítési varázsló megfelelő beállítástól függ. Egy bejövő forgalomra vonatkozó szabály a kiértékelés kezdődik-e az adatforrás kapcsolódási térbe objektumára és feladatütemezési értékeli az illesztés szabályok szereplő valamennyi csoport. Ha egy adatforrás-objektum tartozik a metaverzumba, az illesztési szabályok egyikének használatával pontosan egy objektum kereséséhez, az objektumok kapcsolódnak-e. Ha az összes szabály értékelése, és nincs egyezés, a kapcsolat típusa, a leírás oldalon szolgál. Ha ez a konfiguráció beállítása **rendelkezés**, majd egy új objektum jön létre a célkiszolgálón, a metaverzumba. Új objektum felvételére a metaverzum-hoz is kiépítését **projekt** egy objektumot a metaverzumba.

Az illesztési szabályok csak egyszer kell kiértékelni. Ha egy összekötő terület objektum és a metaverzum-objektum kapcsolódik, akkor maradjanak illesztett mindaddig, amíg a szinkronizálási szabály hatóköre továbbra is eleget.

Szinkronizálási szabályok kiértékelésekor meghatározott illesztési szabályok csak egy szinkronizálási szabály hatókörében kell lennie. Ha több szinkronizálási szabályait illesztési szabályok egy adott objektum található, hiba történt. Ezért az ajánlott eljárás, hogy csak egy szinkronizálási szabály van definiálva, ha több szinkronizálási szabályok hatókörébe az objektum illesztési. Az Azure AD Connect szinkronizálási szolgáltatás out-of-box konfigurációjában, ezek a szabályok alapján a neve található és azokat a Word **csatlakozás** a név végén. Nélkül a megadott illesztési szabályok szinkronizálási szabály az attribútumfolyamok érvényes, ha egy másik szinkronizálási szabály az objektumok összekapcsolódva, vagy egy új objektumot a célzott kiépítve.

Ha a fenti kép tekinti meg, akkor láthatja, hogy a szabály próbál csatlakozni, **objectSID** rendelkező **msExchMasterAccountSid** (Exchange) és **msRTCSIP-OriginatorSid** (Lync). Ez az felel meg az elvártnak a fiók-erőforrás erdő topológiájában. Ugyanaz a szabály az összes erdőben található meg. A feltételezi, hogy minden erdőben vagy egy fiókot, vagy az erőforrás erdő lehet. Ez a konfiguráció is működik, ha fiókokat, amelyek az adott erdő live, és nem kell csatlakoztatni.

#### <a name="transformations"></a>Transformations
Transzformációs szakaszából határozza meg az összes attribútumfolyamok, amelyek érvényesek a célobjektum objektumok tartományhoz csatlakoztatott és a hatókör szűrő teljesül-e. Ha visszalép, hogy a **a az AD-felhasználó AccountEnabled** szinkronizálási szabály található a következő átalakítások:

![Átalakítások szinkronban szabály szerkesztő lap ](./media/active-directory-aadconnectsync-understanding-default-configuration/syncruletransformations.png)

Kontextusba helyezni ezt a konfigurációt, fiók-erőforrás-erdő telepítés, várhatóan található engedélyezett fióknak az erdő és a letiltott fiók az erőforráserdőben Exchange és a Lync beállításokkal. Az éppen megtekintett szinkronizálási szabályának tartalmazza a bejelentkezéshez szükséges attribútumok és ezek az attribútumok az erdőben kell haladjanak ahol van egy engedélyezett fiók. Ezek attribútumfolyamok alkotják, több szinkronizálási szabály.

Átalakítás is rendelkezik különböző: állandó, közvetlen és kifejezés.

* Az adatáramlás mindig forgalomáramlás szoftveresen kötött érték. A fenti esetben mindig beállítja az értéket **igaz** a metaverzum-attribútum neve **accountEnabled**.
* A közvetlen áramlását mindig zajlik a attribútum számára a target attribútummal, mint a forrás-van.
* A harmadik adatfolyam típusú kifejezés, és lehetővé teszi a speciális konfigurációk.

A kifejezés nyelve megegyezik a VBA (Visual Basic for Applications), ezért a Microsoft Office élménye rendelkező személyek vagy VBScript felismeri a formátuma. Attribútumok szögletes zárójelbe [attributeName] parancsfájlblokkjában találhatók. Attribútumnevek és függvény nevek kis-és nagybetűket, de a szinkronizálási szabályok szerkesztő kiértékeli a kifejezést, és adjon meg egy figyelmeztetés, ha a kifejezés nem érvényes. Összes kifejezés szerint van megadva, a beágyazott függvények külön sorba. A konfigurációs nyelvi hatványa megjelenítéséhez ez pwdLastSet, de további megjegyzésekkel szúrja be a folyamatot:

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

Lásd: [ismertetése deklaratív kiépítés kifejezések](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) attribútumfolyamokhoz kifejezés nyelvi további információt.

### <a name="precedence"></a>Sorrend
Most nézett néhány egyes szinkronizálási szabályait, de a szabályok együttműködik a konfigurációban. Bizonyos esetekben egy attribútumérték van hozzájárult több szinkronizálási szabályok az azonos target attribútuma. Attribútumsorrend ebben az esetben az határozza meg, melyik attribútum wins szolgál. Tegyük fel nézze meg a sourceAnchor. Fontos tudni az Azure AD bejelentkezés attribútum erre az attribútumra. Az Attribútumfolyam találhat meg két különböző szinkronizálási szabályok ezt az attribútumot **a az AD-felhasználó AccountEnabled** és **a az AD-felhasználó közös**. Szinkronizálási szabály elsőbbséget, mert a sourceAnchor attribútum van hozzájárult engedélyezett fióknak az erdő első több objektumot csatoltak a metaverzum-objektum esetén. Ha nincsenek engedélyezett fiókok, akkor a szinkronizálási motor által a catch – minden szinkronizálási szabály **a az AD-felhasználó közös**. Ez a konfiguráció biztosítja, hogy még a fiók, továbbra is a sourceAnchor.

![Bejövő szinkronizálási szabály](./media/active-directory-aadconnectsync-understanding-default-configuration/syncrulesinbound.png)

A szinkronizálási szabályok precedenciáját állítja be a csoportok a telepítési varázsló. A csoportban található összes szabály ugyanazon névvel rendelkeznek, de különböző csatlakoztatott könyvtárak csatlakoznak. A telepítési varázsló lehetővé teszi a szabály **a az AD-felhasználó csatlakozás** legmagasabb élvez és azt megismétli over összes AD könyvtárakban csatlakoztatva. Majd folytatja a következő csoportok szabályok egy előre meghatározott sorrendben. Egy csoporton belül a szabályok hozzáadása történik meg a varázsló az összekötők hozzáadott sorrendben. Ha egy másik összekötő a varázsló használatával ad hozzá, a szinkronizálási szabályok rendezi újra, és az új összekötő szabályok utolsó egészül ki minden egyes csoport.

### <a name="putting-it-all-together"></a>A teljes kép
Most már tudjuk elegendő szinkronizálási szabályokat kell a konfigurációt a különböző szinkronizálási szabályait működésének megismerése. Ha egy felhasználó és az attribútumok a metaverzumba hozzájáruló tekinti meg, a szabályok vonatkoznak a következő sorrendben:

| Name (Név) | Megjegyzés |
|:--- |:--- |
| Az ad-felhasználó illesztési |Összekötő metaverzum-es összekötő terület objektumok szabály. |
| Az ad-– UserAccount engedélyezve |Bejelentkezés az Azure AD szükséges attribútumok és az Office 365. Azt szeretnénk, ha ezek az attribútumok az engedélyezett fiókkal. |
| Az ad-felhasználó közös az Exchange-ből |A globális címlista attribútumot. Feltételezzük, hogy a data quality célszerű az erdőben, ahol találtunk, amelyeknek a felhasználó postaládájából. |
| Az ad-felhasználó közös |A globális címlista attribútumot. Abban az esetben, ha a postaláda nem található, a többi illesztett objektum járulhat attribútum értéke. |
| Az AD-felhasználó Exchange-ből |Ha a rendszer észlelte az Exchange csak létezik. Összes infrastruktúra Exchange attribútum azt zajlik. |
| Az ad-felhasználó Lync |Ha a rendszer észlelte a Lync csak létezik. Összes infrastruktúra Lync attribútum azt zajlik. |

## <a name="next-steps"></a>További lépések
* További információk a konfigurációs modell [ismertetése deklaratív kiépítés](active-directory-aadconnectsync-understanding-declarative-provisioning.md).
* További információk az a kifejezés nyelv [ismertetése deklaratív kiépítés kifejezések](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md).
* Továbbra is, hogyan működik a out-of-box konfiguráció olvasása [felhasználók és névjegyek](active-directory-aadconnectsync-understanding-users-and-contacts.md)
* Lásd: how to gyakorlati módosítja a deklaratív kiépítés használatával [hogyan lehet módosítani az alapértelmezett konfiguráció](active-directory-aadconnectsync-change-the-configuration.md).

**Áttekintő témakör**

* [Azure AD Connect szinkronizálása: megértéséhez, valamint a szinkronizálás testreszabása](active-directory-aadconnectsync-whatis.md)
* [Helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md)

