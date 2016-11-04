---
title: Címtár-integráció az Azure Multi-Factor Authentication és az Active Directory között
description: Ez az Azure Multi-Factor Authentication-oldal leírja, hogyan integrálható az Azure Multi-Factor Authentication-kiszolgáló az Active Directoryval a címtárak szinkronizálása érdekében.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtand

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/04/2016
ms.author: kgremban

---
# Címtár-integráció az Azure MFA-kiszolgáló és az Active Directory között
A Címtár-integráció szakaszban konfigurálhatja a kiszolgálót az Active Directoryval vagy egy másik LDAP-címtárral való integrációhoz.  Konfigurálhatja az attribútumokat, hogy azok megfeleljenek a címtársémának, illetve beállíthatja a felhasználók automatikus szinkronizálását.

## Beállítások
Alapértelmezés szerint az Azure Multi-Factor Authentication-kiszolgáló úgy van konfigurálva, hogy a felhasználókat az Active Directoryból importálja vagy szinkronizálja.  Ezen a lapon felülírhatja az alapértelmezett viselkedést, illetve kötést hozhat létre egy másik LDAP-címtárhoz, egy ADAM-címtárhoz vagy egy adott Active Directory-tartományvezérlőhöz.  Ezenkívül lehetővé teszi LDAP-hitelesítés használatát proxy LDAP-hoz vagy LDAP-kötés használatát RADIUS célként, előhitelesítéshez IIS-hitelesítésnél vagy elsődleges hitelesítéshez a felhasználói portálon.  A következő táblázat ismerteti az egyes beállításokat.

![Beállítások](./media/multi-factor-authentication-get-started-server-dirint/dirint.png)

| Szolgáltatás | Leírás |
| --- | --- |
| Active Directory használata |Válassza az Active Directory használata beállítást, ha az Active Directoryt szeretné alkalmazni importáláshoz és szinkronizáláshoz.  Ez az alapértelmezett beállítás. <br>Megjegyzés: A számítógépnek tartományhoz csatlakoztatva kell lennie, és Önnek be kell jelentkeznie egy tartományfiókkal, hogy az Active Directory integrációja megfelelően működjön. |
| Megbízható tartományok belefoglalása |Jelölje be a Megbízható tartományok belefoglalása jelölőnégyzetet, hogy az ügynök az aktuális tartomány által megbízhatónak tartott tartományokhoz, az erdőben egy másik tartományhoz vagy erdőszintű megbízhatósággal rendelkező tartományokhoz csatlakozzon.  Amikor nem a megbízható tartományokból importál vagy szinkronizál felhasználókat, törölje a jelölőnégyzet jelölését a teljesítmény növeléséhez.  Alapértelmezés szerint be van jelölve. |
| Meghatározott LDAP-konfiguráció használata |Válassza az LDAP használatára vonatkozó beállítást, ha az importáláshoz és szinkronizáláshoz megadott LDAP-beállításokat szeretné alkalmazni. Megjegyzés: az LDAP használatára vonatkozó beállítás kiválasztásakor a felhasználói felület a hivatkozásokat Active Directoryról LDAP-ra módosítja. |
| Szerkesztés gomb |A Szerkesztés gombbal módosíthatja az LDAP aktuális konfigurációs beállításait. |
| Attribútumhatókör-lekérdezések használata |Azt jelzi, hogy a rendszer használ-e attribútumhatókör-lekérdezéseket.  Az attribútumhatókör-lekérdezésekkel hatékony címtárkereséseket lehet végezni, amelyek a rekordokat egy másik rekord attribútumának bejegyzései alapján minősítik.  Az Azure Multi-Factor Authentication-kiszolgáló az attribútumhatókör-lekérdezéseket a biztonsági csoportok tagfelhasználóinak hatékony lekérdezésére használja.   <br>Megjegyzés: Bizonyos esetekben az attribútumhatókör-lekérdezés támogatott, de nem érdemes használni.  Például az Active Directory problémákba ütközhet az attribútumhatókör-lekérdezéskor, ha egy biztonsági csoport tagjai több tartományhoz tartoznak.  Ebben az esetben a jelölőnégyzetet ne jelölje be. |

A következő táblázat az LDAP-konfigurációs beállításokat ismerteti.

| Szolgáltatás | Leírás |
| --- | --- |
| Kiszolgáló |Megadhatja az LDAP-címtárat futtató kiszolgáló állomásnevét vagy IP-címét.  Tartalékkiszolgálót is megadhat pontosvesszővel elválasztva. <br>Megjegyzés: Ha a kötés SSL típusú, általában teljes állomásnév szükséges. |
| Alap DN |Megadhatja az alap címtárobjektum megkülönböztető nevét, ahonnan az összes címtárlekérdezés indulni fog.  Például dc=abc,dc=com. |
| Kötéstípus – Lekérdezések |Válassza ki az LDAP-címtár kereséséhez használandó kötés megfelelő kötéstípusát.  Ezt a rendszer az importálásokhoz, a szinkronizáláshoz és a felhasználónevek feloldásához használja. <br><br>  Névtelen – A rendszer névtelen kötést hajt végre.  Nem használ kötési DN-t és kötésjelszót.  Ez csak akkor fog működni, ha az LDAP-címtár engedélyezi a névtelen kötést és az engedélyek lehetővé teszik a megfelelő rekordok és attribútumok lekérdezését.  <br><br> Egyszerű – A kötési DN-t és a kötésjelszót a rendszer egyszerű szövegként adja át az LDAP-címtárhoz való kötéshez.  Ez csak tesztelési célokra használható, annak ellenőrzésére, hogy a kiszolgáló elérhető-e, és a kötési fiók megfelelő hozzáféréssel rendelkezik-e.  Javasoljuk, hogy ehelyett SSL-t használjon a megfelelő tanúsítvány telepítése után.  <br><br> SSL – A kötési DN-t és a kötésjelszót a rendszer SSL használatával titkosítja az LDAP-címtárhoz való kötéshez.  Ehhez olyan tanúsítványt kell helyileg telepíteni, amelyet az LDAP-címtár megbízhatónak tart.  <br><br> Windows – A kötési DN-nel és a kötésjelszóval a rendszer biztonságos kapcsolatot létesít egy Active Directory-tartományvezérlővel vagy ADAM-címtárral.  Ha a Kötési felhasználónév mezőt üresen hagyja, a kötéshez a rendszer a bejelentkezett felhasználó fiókját használja. |
| Kötéstípus – Hitelesítések |Válassza ki az LDAP-kötés hitelesítéséhez használandó megfelelő kötéstípust.  A kötéstípusok leírását a Kötéstípus – Lekérdezések témakörben találja.  Például ez lehetővé teszi, hogy a lekérdezésekhez Névtelen kötést, míg az LDAP-kötések hitelesítésének védelméhez SSL-kötést használjon. |
| Kötési DN vagy kötési felhasználónév |Megadhatja az LDAP-címtárhoz végrehajtott kötésekhez használt fiók felhasználórekordjának megkülönböztető nevét.<br><br>A kötés megkülönböztető nevét a rendszer csak akkor használja, ha a kötéstípus Egyszerű vagy SSL.  <br><br>Megadhatja az LDAP-címtárhoz végrehajtott kötésekhez használt Windows-fiók felhasználónevét, ha a kötéstípus Windows.  Ha a mezőt üresen hagyja, a kötéshez a rendszer a bejelentkezett felhasználó fiókját használja. |
| Kötésjelszó |Megadhatja az LDAP-címtárhoz végrehajtott kötésekhez használt kötési DN-hez vagy kötési felhasználónévhez tartozó kötésjelszót.  A Multi-Factor Auth-kiszolgáló AdSync szolgáltatása jelszavának beállításához engedélyeznie kell a szinkronizálást, és a szolgáltatásnak futnia kell a helyi gépen.  A jelszó a Windows rendszeren tárolt felhasználónevek és jelszavak között, a Multi-Factor Auth-kiszolgáló AdSync szolgáltatását futtató fiók alatt lesz mentve.  A jelszót a rendszer a Multi-Factor Auth-kiszolgáló felhasználói felületét futtató fiók alatt és a Multi-Factor Auth-kiszolgáló szolgáltatást futtató fiók alatt is elmenti.  <br><br> Megjegyzés: Mivel a jelszót a rendszer csak a helyi kiszolgálón tárolja a Windows rendszeren tárolt felhasználónevek és jelszavak között, ezt a lépést meg kell ismételni minden Multi-Factor Auth-kiszolgálón, amelynek hozzáférésre van szüksége a jelszóhoz. |
| Lekérdezés méretkorlátja |Megadhatja a címtárkeresések által visszaadott felhasználók maximális számára vonatkozó méretkorlátozást.  Ennek a korlátnak meg kell egyeznie az LDAP-címtár konfigurációjával.  Nagy méretű keresések esetén, ahol a lapozás nem támogatott, az importálás és a szinkronizálás a felhasználókat kötegekben próbálja meg lekérni.  Ha az itt megadott méretkorlát nagyobb mint az LDAP-címtárban megadott korlát, előfordulhat, hogy néhány felhasználó kimarad. |
| Teszt gomb |A Teszt gombra kattintva tesztelheti az LDAP-kiszolgálóhoz való kötéseket.  <br><br> Megjegyzés: Az LDAP használatára vonatkozó beállítást nem kell kijelölni a kötések teszteléséhez.  Ez lehetővé teszi a kötések tesztelését az LDAP-konfiguráció használata előtt. |

## Szűrők
A szűrőkkel feltételeket adhat meg a rekordok minősítéséhez címtárkeresések végrehajtásakor.  Szűrők beállításával korlátozhatja a szinkronizálni kívánt objektumok körét.  

![Szűrők](./media/multi-factor-authentication-get-started-server-dirint/dirint2.png)

Az Azure Multi-Factor Authentication az alábbi 3 beállítással rendelkezik.

* **Tárolószűrő** – Megadhatja a címtárkereséskor a tárolórekordok minősítéséhez használt szűrőfeltételeket.  Active Directory és ADAM esetén általában a következő feltétel használható: (|(objectClass=organizationalUnit)(objectClass=container)).  Egyéb LDAP-címtárak esetén a címtársémától függően olyan szűrőfeltételeket érdemes használni, amelyek az egyes tárolóobjektumok egyes típusait minősítik.  <br>Megjegyzés: Ha üresen hagyja, a rendszer alapértelmezés szerint a következő feltételt használja: ((objectClass=organizationalUnit)(objectClass=container)).
* **Biztonsági csoport szűrője** – Megadhatja a címtárkereséskor a biztonságicsoport-rekordok minősítéséhez használt szűrőfeltételeket.  Active Directory és ADAM esetén általában a következő feltétel használható: (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)).  Egyéb LDAP-címtárak esetén a címtársémától függően olyan szűrőfeltételeket érdemes használni, amelyek az egyes biztonságicsoport-objektumok típusait minősítik.  <br>Megjegyzés: Ha üresen hagyja, a rendszer alapértelmezés szerint a következő feltételt használja: (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)).
* **Felhasználószűrő** – Megadhatja a címtárkereséskor a felhasználórekordok minősítéséhez használt szűrőfeltételeket.  Active Directory és ADAM esetén általában a következő feltétel használható: (&(objectClass=user)(objectCategory=person)).  Egyéb LDAP-címtárak esetén a címtársémától függően az (objectClass=inetOrgPerson) vagy valamilyen hasonló szűrőfeltétel használható. <br>Megjegyzés: Ha üresen hagyja, a rendszer alapértelmezés szerint a következő feltételt használja: (&(objectCategory=person)(objectClass=user)).

## Attribútumok
Az attribútumok igény szerint testreszabhatók egy adott címtárhoz.  Ez lehetővé teszi egyéni attribútumok hozzáadását és a szinkronizálás finomhangolását csak a szükséges attribútumokra.  Az egyes attribútummezők értékeként az attribútum a címtársémában meghatározott nevét kell megadni.  További információkat az alábbi táblázatban talál.

![Attribútumok](./media/multi-factor-authentication-get-started-server-dirint/dirint3.png)

| Szolgáltatás | Leírás |
| --- | --- |
| Egyedi azonosító |Megadhatja a tároló-, biztonságicsoport- és felhasználórekordok egyedi azonosítójaként szolgáló attribútum nevét.  Az Active Directoryban ez általában az objectGUID.  Egyéb LDAP-megvalósításokban ez az entryUUID vagy hasonló lehet.  Az alapértelmezett érték az objectGUID. |
| ... (Attribútum kiválasztása) gombok |Mindegyik attribútum mező mellett található egy „...” gomb, amely megjeleníti az Attribútum kiválasztása párbeszédpanelt, ahol kiválaszthat egy attribútumot a listából. <br><br>Attribútum kiválasztása párbeszédpanel.<br><br>Megjegyzés: Az attribútumokat manuálisan is megadhatja, és nem kell egyezniük az attribútumlistán szereplő attribútumokkal. |
| Egyedi azonosító típusa |Válassza ki az egyedi azonosító attribútum típusát.  Az Active Directoryban az objectGUID attribútum típusa GUID.  Egyéb LDAP-megvalósításokban ez ASCII bájttömb vagy Karakterlánc típusú lehet.  Az alapértelmezett érték a GUID. <br><br>Megjegyzés: Fontos, hogy ezt helyesen adja meg, mivel a rendszer a szinkronizált elemekre az egyedi azonosítójuk alapján hivatkozik, és az egyedi azonosító típusának segítségével keresi meg az objektumot a címtárban.  Ha ezt Karakterlánc típusúra állítja, miközben a címtár valójában az értéket ASCII-karakterek bájttömbjeként tárolja, a szinkronizálás nem fog megfelelően működni. |
| Megkülönböztető név |Megadhatja azon attribútum nevét, amely az egyes rekordok megkülönböztető nevét tartalmazza.  Active Directoryban ez általában a distinguishedName.  Egyéb LDAP-megvalósításokban ez az entryDN vagy hasonló lehet.  Az alapértelmezett érték a distinguishedName. <br><br>Megjegyzés: Ha nem létezik csak a megkülönböztető nevet tartalmazó attribútum, az adspath attribútum használható.  Az elérési út „LDAP://<server>/” részét a rendszer automatikusan leválasztja, csak az objektum megkülönböztető nevét hagyva. |
| Tárolónév |Megadhatja azon attribútum nevét, amely a nevet egy tárolórekordban tárolja.  Az attribútum értéke megjelenik a tárolóhierarchiában az Active Directoryból való importáláskor vagy szinkronizált elemek hozzáadásakor.  Az alapértelmezett érték a name. <br><br>Megjegyzés: Ha a különböző tárolók különböző attribútumokat használnak a neveikhez, több tárolónév-attribútumot is megadhat pontosvesszővel elválasztva.  A rendszer a tárolóobjektumokban található első tárolónév-attribútumot használja az objektumok nevének megjelenítéséhez. |
| Biztonsági csoport neve |Megadhatja azon attribútum nevét, amely a nevet egy biztonságicsoport-rekordban tárolja.  Az attribútum értéke megjelenik a Biztonsági csoport listában az Active Directoryból való importáláskor vagy szinkronizált elemek hozzáadásakor.  Az alapértelmezett érték a name. |
| Felhasználók |A következő attribútumok a címtárban lévő felhasználói adatok kereséséhez, megjelenítéséhez, importálásához és szinkronizálásához használhatók. |
| Felhasználónév |Megadhatja azon attribútum nevét, amely a felhasználónevet egy felhasználórekordban tárolja.  A rendszer az attribútum értékét a Multi-Factor Auth-kiszolgáló felhasználóneveként fogja használni.  Egy második attribútumot is megadhat tartalékként.  A második attribútumot a rendszer csak akkor használja, ha az első attribútum nem tartalmaz értéket a felhasználóhoz.  Az alapértelmezett érték a userPrincipalName és az sAMAccountName. |
| Utónév |Megadhatja azon attribútum nevét, amely az utónevet egy felhasználórekordban tárolja.  Az alapértelmezett érték a givenName. |
| Vezetéknév |Megadhatja azon attribútum nevét, amely a vezetéknevet egy felhasználórekordban tárolja.  Az alapértelmezett érték az sn. |
| E-mail-cím |Megadhatja azon attribútum nevét, amely az e-mail-címet egy felhasználórekordban tárolja.  A rendszer a megadott e-mail-címre üdvözlő és tájékoztató e-maileket küld a felhasználónak.  Az alapértelmezett érték a mail. |
| Felhasználói csoport |Megadhatja azon attribútum nevét, amely a felhasználói csoportot egy felhasználórekordban tárolja.  A felhasználói csoportokkal szűrheti a felhasználókat az ügynökben és a jelentésekben a Multi-Factor Auth-kiszolgáló felügyeleti portálján. |
| Leírás |Megadhatja azon attribútum nevét, amely a leírást egy felhasználórekordban tárolja.  A leírás csak keresésekhez használható.  Az alapértelmezett érték a description. |
| Hanghívás nyelve |Megadhatja azon attribútum nevét, amely a felhasználóhoz intézett hanghívásokhoz használt nyelv rövid nevét tartalmazza. |
| SMS szöveg nyelve |Megadhatja azon attribútum nevét, amely a felhasználónak küldött SMS szöveges üzenetekhez használt nyelv rövid nevét tartalmazza. |
| Telefonos alkalmazás nyelve |Megadhatja azon attribútum nevét, amely a felhasználónak küldött telefonos alkalmazásbeli szöveges üzenetekhez használt nyelv rövid nevét tartalmazza. |
| OATH token nyelve |Megadhatja azon attribútum nevét, amely a felhasználónak küldött OATH token szöveges üzeneteihez használt nyelv rövid nevét tartalmazza. |
| Telefonok |A következő attribútumokat a rendszer a felhasználók telefonszámainak importálásához vagy szinkronizálásához használja.  Ha nincs megadva attribútumnév a telefontípushoz, a telefontípus nem lesz elérhető az Active Directoryból való importáláskor vagy szinkronizált elemek hozzáadásakor. |
| Vállalati |Megadhatja azon attribútum nevét, amely a vállalati telefonszámot egy felhasználórekordban tárolja.  Az alapértelmezett érték a telephoneNumber. |
| Otthoni |Megadhatja azon attribútum nevét, amely az otthoni telefonszámot egy felhasználórekordban tárolja.  Az alapértelmezett érték a homePhone. |
| Személyhívó |Megadhatja azon attribútum nevét, amely a személyhívószámot egy felhasználórekordban tárolja.  Az alapértelmezett érték a pager. |
| Mobiltelefon |Megadhatja azon attribútum nevét, amely a mobiltelefonszámot egy felhasználórekordban tárolja.  Az alapértelmezett érték a mobile. |
| Fax |Megadhatja azon attribútum nevét, amely a faxszámot egy felhasználórekordban tárolja.  Az alapértelmezett érték a facsimileTelephoneNumber. |
| IP-telefon |Megadhatja azon attribútum nevét, amely az IP-telefonszámot egy felhasználórekordban tárolja.  Az alapértelmezett érték az ipPhone. |
| Egyéni |Megadhatja azon attribútum nevét, amely egy egyéni telefonszámot egy |
| felhasználórekordban tárol.  Alapértelmezés szerint ez a mező üres. | |
| Mellék |Megadhatja azon attribútum nevét, amely a telefonszámhoz tartozó melléket egy felhasználórekordban tárolja.  A mellék mező értékét a rendszer csak az elsődleges telefonszám mellékeként használja.  Alapértelmezés szerint ez a mező üres. <br><br>Megjegyzés: Ha a Mellék attribútum nincs meghatározva, a mellékek megadhatók a telefonattribútum részeként.  A mellékszám előtt az „x” karakternek kell szerepelnie, hogy elemezhető legyen.  Például az 555-123-4567 x890 érték az 555-123-4567-es telefonszámot jelöli 890-es mellékkel. |
| Alapértelmezések visszaállítása gomb |Kattintson az Alapértelmezések visszaállítása gombra az összes attribútum az alapértelmezett értékre való visszaállításához.  Az alapértelmezett értékek megfelelően működnek a normál Active Directory- vagy ADAM-sémával. |

Az attribútumok szerkesztéséhez egyszerűen kattintson a Szerkesztés gombra az Attribútumok lapon.  Ez megnyit egy ablakot, amely lehetővé teszi az attribútumok szerkesztését.

![Attribútumok szerkesztése](./media/multi-factor-authentication-get-started-server-dirint/dirint4.png)

## Szinkronizálás
A szinkronizálás biztosítja, hogy az Azure Multi-Factor felhasználói adatbázis szinkronizálva legyen az Active Directory vagy egy másik Lightweight Directory Access Protocol- (LDAP-) címtár felhasználóival.  A folyamat hasonló a felhasználók az Active Directoryból való manuális importálásához, azonban rendszeresen lekérdezi az Active Directory-felhasználók és biztonsági csoportok változásait feldolgozásra.  Ezenkívül lehetővé teszi a tárolókból vagy biztonsági csoportokból eltávolított felhasználók letiltását vagy eltávolítását és az Active Directoryból törölt felhasználók eltávolítását.

A Multi-Factor Auth ADSync szolgáltatása egy Windows-szolgáltatás, amely rendeszeresen lekérdezi az Active Directoryt.  Ez nem keverendő össze az Azure AD Sync vagy az Azure AD Connect szolgáltatással.  A Multi-Factor Auth ADSync, bár egy hasonló kódalapra épül, csak az Azure Multi-Factor Authentication-kiszolgálóval működik.  A telepítéskor leállított állapotban van, és a Multi-Factor Auth-kiszolgáló szolgáltatás indítja el, ha futásra van konfigurálva.  Ha többkiszolgálós Multi-Factor Auth-kiszolgálókonfigurációval rendelkezik, a Multi-Factor Auth ADSync csak egy kiszolgálón futtatható.

A Multi-Factor Auth ADSync szolgáltatás a Microsoft által biztosított DirSync LDAP-kiszolgálóbővítményt használja a változások hatékony lekérdezéséhez.  Ennek a DirSync vezérlőhívónak a „directory get changes” jogosultsággal és DS-Replication-Get-Changes kibővített hozzáférés-vezérlési jogosultsággal kell rendelkeznie.  Alapértelmezés szerint ezek a jogosultságok a Rendszergazda és a LocalSystem fiókhoz vannak rendelve a tartományvezérlőkön.  A Multi-Factor Auth AdSync szolgáltatás alapértelmezés szerint LocalSystem fiókként fut.  Ezért a legegyszerűbb, ha a szolgáltatást egy tartományvezérlőn futtatja.  A szolgáltatás futtatható alacsonyabb szintű engedélyekkel rendelkező fiókként, ha úgy konfigurálja, hogy mindig teljes szinkronizálást hajtson végre.  Ez kevésbé hatékony, de kevesebb fiókjogosultságot igényel.

Ha LDAP használatára konfigurálja és az LDAP-címtár támogatja a DirSync vezérlőt, akkor a felhasználók és biztonsági csoportok változásainak lekérdezése ugyanúgy fog működni, mint az Active Directoryval.  Ha az LDAP-címtár nem támogatja a DirSync vezérlőt, akkor a rendszer minden ciklusban teljes szinkronizálást hajt végre.

![Szinkronizálás](./media/multi-factor-authentication-get-started-server-dirint/dirint5.png)

A Szinkronizálás lap egyes beállításairól további információkat az alábbi táblázatban talál.

| Szolgáltatás | Leírás |
| --- | --- |
| Active Directory-szinkronizálás engedélyezése |Ha be van jelölve, a rendszer elindítja a Multi-Factor Auth-kiszolgáló szolgáltatást, hogy rendszeres időközönként lekérdezze az Active Directoryt a változásokért. <br><br>Megjegyzés: Legalább egy szinkronizált elemet hozzá kell adni és egy Szinkronizálás most műveletet végre kell hajtani, mielőtt a Multi-Factor Auth-kiszolgáló szolgáltatás elkezdené a változások feldolgozását. |
| Szinkronizálás gyakorisága |Megadhatja azt az időtartamot, amennyit a Multi-Factor Auth-kiszolgáló szolgáltatás vár a változások lekérdezése és feldolgozása között. <br><br> Megjegyzés: A megadott időköz az egyes ciklusok kezdete között eltelt idő.  Ha a feldolgozási idő meghaladja az időközt, a szolgáltatás azonnal ismét elindítja a lekérdezést. |
| Az Active Directoryban már nem szereplő felhasználók eltávolítása |Ha be van jelölve, a Multi-Factor Auth-kiszolgáló szolgáltatás feldolgozza az Active Directory törölt felhasználóinak törlésjelzőit, és eltávolítja a kapcsolódó Multi-Factor Auth-kiszolgálófelhasználót. |
| Mindig végezzen teljes szinkronizálást |Ha be van jelölve, a Multi-Factor Auth-kiszolgáló szolgáltatás mindig teljes szinkronizálást végez.  Ha nincs bejelölve, a Multi-Factor Auth-kiszolgáló szolgáltatás növekményes szinkronizálást végez csak a módosított felhasználók lekérdezésével.  Alapértelmezés szerint nincs bejelölve. <br><br> Megjegyzés: Ha nincs bejelölve, a növekményes szinkronizálást csak akkor lehet végrehajtani, ha a címtár támogatja a DirSync vezérlőt és a címtárhoz való kötéshez használt fiók rendelkezik a megfelelő engedélyekkel a DirSync növekményes lekérdezések végrehajtásához.  Ha a fiók nem rendelkezik a megfelelő engedélyekkel, vagy több tartományt is érint a szinkronizálás, akkor teljes szinkronizálás végrehajtása ajánlott. |
| Rendszergazdai jóváhagyás megkövetelése több mint X felhasználó letiltása vagy eltávolítása esetén |A szinkronizált elemek konfigurálhatók úgy, hogy letiltsák vagy eltávolítsák azokat a felhasználókat, akik már nem tagjai az elem tárolójának vagy biztonsági csoportjának.  Biztonsági intézkedésként esetlegesen rendszergazdai jóváhagyás lehet szükséges, ha a letiltandó vagy eltávolítandó felhasználók száma meghalad egy küszöbértéket.  Ha be van jelölve, az adott küszöbérték meghaladása esetén jóváhagyás szükséges.  Az alapértelmezett érték az 5, a tartomány 1 és 999 közötti. <br><br> A jóváhagyáshoz a rendszer először egy e-mail-értesítést küld a rendszergazdáknak. Az e-mail-értesítés útmutatást ad a felhasználók letiltásának és eltávolításának áttekintéséhez és jóváhagyásához.  A Multi-Factor Auth-kiszolgáló felhasználói felülete megnyitásakor a szolgáltatás jóváhagyást kér. |

A **Szinkronizálás most** gombbal teljes szinkronizálást futtathat a megadott szinkronizált elemekhez.  Teljes szinkronizálás szükséges szinkronizált elemek hozzáadásakor, módosításakor, eltávolításakor vagy átrendezésekor.  A Multi-Factor Auth AdSync szolgáltatás működésbe lépése előtt is szükséges, mivel beállítja a kezdőpontot, amelyhez viszonyítva a szolgáltatás lekérdezi a növekményes változásokat.  Ha a szinkronizált elemek módosultak és nem hajtott végre teljes szinkronizálást, a szolgáltatás felszólítja a Szinkronizálás most művelet elvégzésére, amikor megnyit egy másik szakaszt vagy bezárja a felhasználói felületet.

Az **Eltávolítás** gombbal a rendszergazda törölhet egy vagy több szinkronizált elemet a Multi-Factor Auth-kiszolgáló szinkronizáltelem-listájából.

> [!WARNING]
> Ha egy szinkronizált elem rekordját eltávolítja, az nem állítható vissza. Ha véletlenül törölte egy szinkronizált elem rekordját, újra hozzá kell adnia azt.
> 
> 

A szinkronizált elemet vagy elemeket a rendszer eltávolította a Multi-Factor Auth-kiszolgálóról.  A Multi-Factor Auth-kiszolgáló szolgáltatás a továbbiakban nem dolgozza fel a szinkronizált elemeket.

A Feljebb és Lejjebb gombbal a rendszergazda módosíthatja a szinkronizált elemek sorrendjét.  A sorrend azért fontos, mert ugyanaz a felhasználó több szinkronizált elemnek is tagja lehet (például egy tárolónak és egy biztonsági csoportnak).  A felhasználóra a szinkronizálás során alkalmazott beállítások a felhasználóhoz társított listának az első szinkronizált eleméből származnak.  Ezért a szinkronizált elemeket prioritási sorrendben kell elhelyezni.

> [!TIP]
> Szinkronizált elemek eltávolítása után teljes szinkronizálást kell végrehajtani.  Szinkronizált elemek átrendezése után teljes szinkronizálást kell végrehajtani.  Kattintson a Szinkronizálás most gombra a teljes szinkronizálás elindításához.
> 
> 

## Multi-Factor Auth-kiszolgálók
További Multi-Factor Auth-kiszolgálókat beállíthat úgy, hogy tartalék RADIUS-proxyként, LDAP-proxyként, illetve IIS-hitelesítési kiszolgálóként szolgáljanak. A szinkronizálási konfiguráció az összes ügynökre érvényes. Azonban csak az egyik ügynökön futhat a Multi-Factor Auth-kiszolgáló szolgáltatás. Ezen a lapon kiválaszthatja a szinkronizálásra engedélyezett Multi-Factor Auth-kiszolgálót.

![Multi-Factor-Auth-kiszolgálók](./media/multi-factor-authentication-get-started-server-dirint/dirint6.png)

<!--HONumber=Sep16_HO4-->


