---
title: "Az Azure CDN szabályok motor szolgáltatások |} Microsoft Docs"
description: "Az Azure CDN referenciadokumentációt szabályok motor egyezés feltételek és a szolgáltatások."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: ec2555df27f4b709d06b660bf161f741e5b86ea6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cdn-rules-engine-features"></a>Az Azure CDN szabályok motor-funkciók
Ez a témakör részletes leírását tartalmazza az elérhető szolgáltatások az Azure Content Delivery Network (CDN) [szabálymotor](cdn-rules-engine.md).

A szabály harmadik része a szolgáltatást. A szolgáltatás feltételek egyeznek kérelem típusú alkalmazandó művelet típusa határozza meg.

## <a name="access"></a>Hozzáférés

Ezek a szolgáltatások célja, hogy a tartalomhoz való hozzáférés szabályozása.


Név | Cél
-----|--------
Hozzáférés megtagadása | Meghatározza, hogy minden kérésnél 403 Tiltott választ utasítja el.
Jogkivonat hitelesítési | Meghatározza, hogy jogkivonat-alapú hitelesítési kérelemre alkalmazandó.
Jogkivonat hitelesítési Megtagadás kódot | Határozza meg a választ, amely visszatér egy felhasználói kérelem miatt jogkivonat-alapú hitelesítés megtagadása.
Jogkivonat hitelesítési nagybetűket URL-címe | Meghatározza, hogy jogkivonat-alapú hitelesítés által készített URL-cím összehasonlítás legyen kis-és nagybetűket.
Jogkivonat hitelesítési paraméter | Meghatározza, hogy van-e a jogkivonat-alapú hitelesítés lekérdezési karakterlánc paraméter neve.

### <a name="deny-access"></a>Hozzáférés megtagadása
**Cél**: meghatározza, hogy minden kérésnél 403 Tiltott választ utasítja el.

Érték | eredménye
------|-------
Engedélyezve| Hatására az összes kérelmet, amelyek teljesítik a megfelelő – 403 Tiltott választ ad vissza lesznek utasítva.
Letiltva| Visszaállítja az alapértelmezett viselkedés. Az alapértelmezett viselkedés a forrás nyomtatókiszolgálón visszaadott válasz típusának meghatározása.

**Alapértelmezett viselkedés**: letiltva

> [!TIP]
   > Egy lehetséges a funkció használata társítsa a kérelem fejléc egyezik feltételt letiltja a HTTP-hivatkozó kérelmei által használt beágyazott hivatkozások a tartalmakhoz való hozzáférést.

### <a name="token-auth"></a>Jogkivonat hitelesítési
**Cél:** határozza meg, hogy jogkivonat-alapú hitelesítési kérelemre alkalmazandó.

Ha a jogkivonat-alapú hitelesítés engedélyezve van, adjon meg egy titkosított jogkivonatot, és a token által meghatározott követelményeknek csak kérelmek lesz érvényes.

Token értékeinek titkosítására és visszafejtésére használt titkosítási kulcs az elsődleges kulcs és a biztonsági mentés kulcs jogkivonat hitelesítési lapján található beállítások határozzák meg. Ne feledje, hogy a titkosítási kulcsok platform-specifikus.

Érték | eredménye
------|---------
Engedélyezve | A kért tartalom jogkivonat-alapú hitelesítéssel védi. Csak ad meg egy érvényes tokent, és megfeleljenek az ügyfelek kérelmeinek szembeni szerződéses kötelezettségeket. FTP-tranzakciók jogkivonat-alapú hitelesítés nem tartoznak.
Letiltva| Visszaállítja az alapértelmezett viselkedés. Az alapértelmezés lesz annak meghatározásához, hogy egy kérelem védve legyenek a jogkivonat-alapú hitelesítés konfigurálásának engedélyezése.

**Alapértelmezés:** letiltva.

###<a name="token-auth-denial-code"></a>Jogkivonat hitelesítési Megtagadás kódot
**Cél:** határozza meg, amikor a jogkivonat-alapú hitelesítés miatt megtagadva a kérés egy felhasználó számára visszaadott válasz típusú.

A rendelkezésre álló válaszkódot alább láthatók.

Válaszkód|Válasz neve|Leírás
----------------|-----------|--------
301|Végleg áthelyezése|Ez az állapot kód jogosulatlan felhasználók hely fejlécben megadott URL-címre irányítja át.
302|Található|Ez az állapot kód jogosulatlan felhasználók hely fejlécben megadott URL-címre irányítja át. Ezzel az állapotkóddal az iparági szabványos módjáról irányítja át a felhasználókat a rendszer.
307|Ideiglenes átirányítás|Ez az állapot kód jogosulatlan felhasználók hely fejlécben megadott URL-címre irányítja át.
401|Nem engedélyezett|Ez az állapot kód kombinálás a WWW-Authenticate válaszfejléc lehetővé teszi egy felhasználót a hitelesítéshez.
403|Tiltott|Ez az a szabványos 403-as tiltott állapot üzenet egy jogosulatlan felhasználó által látható, amikor megpróbál hozzáférni a védett tartalmakat.
404|A fájl nem található|Ez az állapot kód azt jelzi, hogy a HTTP-ügyfél képes kommunikálni a kiszolgálóval volt, de a kért tartalomhoz nem található.

#### <a name="url-redirection"></a>Átirányítási URL-címe

Ez a szolgáltatás URL-cím átirányítását egy felhasználó által definiált URL-is támogatja, amikor beállítják, hogy egy 3xx állapot kóddal tér vissza. A felhasználó által definiált URL-cím adható meg az alábbi lépések elvégzésével:

1. Válassza ki a hitelesítés megtagadása Tokenkódot szolgáltatás 3xx válaszkód.
2. "Hely" Válassza ki a kötelező fejlécet beállítást.
3. A választható állomásfejléc-érték beállítása a kívánt URL-címre.

Ha az egy URL-címe nincs definiálva a következő 3xx állapotkódot, majd a szabványos válasz lap egy 3xx status kód visszakerül a felhasználó.

Átirányítási URL-címe esetén csak 3xx válaszkódot alkalmazható.

A választható állomásfejléc-érték a beállítás lehetővé teszi, alfanumerikus karaktereket, idézőjelek és szóközöket.

#### <a name="authentication"></a>Authentication

Ez a funkció támogatja a funkció a WWW-Authenticate fejléc szerepeljen válaszol a jogkivonat-alapú hitelesítés által védett tartalom jogosulatlan kérelmet. Ha a WWW-Authenticate fejléc már be lett állítva a "basic" a konfigurációt, majd a jogosulatlan felhasználók bekéri a fiók hitelesítő adatait.

A fenti konfigurációban érheti el az alábbi lépések elvégzésével:

1. Jelöljön ki a "401-es", a hitelesítés megtagadása Tokenkódot szolgáltatás válaszkódja.
2. Válassza ki a "WWW-Authenticate" a Fejlécnév nem kötelező beállítás.
3. A Fejlécérték nem kötelező beállítás "alapszintű."

A WWW-Authenticate fejléc értéke csak a 401-es válaszkódot.

### <a name="token-auth-ignore-url-case"></a>Jogkivonat hitelesítési nagybetűket URL-címe
**Cél:** meghatározza, hogy jogkivonat-alapú hitelesítés által készített URL-cím összehasonlítás legyen kis-és nagybetűket.

Ez a szolgáltatás által érintett paraméterei a következők:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Érvényes értékek a következők:

Érték|eredménye
---|----
Engedélyezve|Hatására a peremhálózati kiszolgáló esetben figyelmen kívül hagyja a jogkivonat-alapú hitelesítési paraméterek URL-címek összehasonlításakor.
Letiltva|Visszaállítja az alapértelmezett viselkedés. Az alapértelmezett viselkedés van URL-cím összehasonlításhoz Token hitelesítés érdekében kis-és nagybetűket.

**Alapértelmezés:** letiltva.
 
### <a name="token-auth-parameter"></a>Jogkivonat hitelesítési paraméter
**Cél:** határozza meg, hogy van-e a jogkivonat-alapú hitelesítés lekérdezési karakterlánc paraméter neve.

Kapcsolatos információkat:

- Az érték opció meghatározza a lekérdezési karakterlánc paraméterének neve, amelyen keresztül a jogkivonat adható meg.
- A beállítás értéke nem állítható be "ec_token."
- Győződjön meg arról, hogy a meghatározott érték beállítás neve URL-cím csak érvényes karaktereket tartalmaz.

Érték|eredménye
----|----
Engedélyezve|Az érték a beállítás határozza meg a lekérdezési karakterlánc paraméterének neve, amelyen keresztül a jogkivonatok definiálni kell.
Letiltva|A jogkivonat a kérelem URL-címében nem definiált lekérdezési karakterlánc paraméterként adható meg.

**Alapértelmezés:** letiltva. A jogkivonat a kérelem URL-címében nem definiált lekérdezési karakterlánc paraméterként adható meg.

## <a name="caching"></a>Gyorsítótárazás

Ezeket a szolgáltatásokat úgy tervezték, hogy mikor és hogyan a tartalom gyorsítótárazva van testreszabása.

Név | Cél
-----|--------
Sávszélesség-paraméterek | Meghatározza, hogy a sávszélesség-szabályozási paraméterek (például ec_rate és ec_prebuf) aktív legyen.
Sávszélesség-szabályozás | Azelőtt gyorsítja fel a sávszélességet, a peremhálózati kiszolgálókról által biztosított válasz.
Gyorsítótár megkerülése | Meghatározza, hogy a kérelem kihasználhatják a gyorsítótárazási technológiánk.
A Cache-Control fejléc kezelése | Meghatározza a peremhálózati kiszolgáló Cache-Control fejlécek előállítását, aktív külső maximális-életkora szolgáltatás esetén.
Gyorsítótár-kulcs lekérdezési karakterlánc | Meghatározza, hogy a a gyorsítótár-kulcsot tartalmaz, vagy zárja ki a lekérdezési karakterlánc paramétereinek a kérelemhez társított.
Gyorsítótár-kulcs átdolgozás | Felülírja a kérelemhez társított gyorsítótár-kulcs.
Fejezze be a gyorsítótár kitöltés | Meghatározza, hogy mi történik, amikor egy kérelem egy részleges gyorsítótár-tévesztései eredményez az egy biztonsági kiszolgálót.
Fájltípusok tömörítése | Meghatározza a fájlformátumokat, amelyek tömörítése a kiszolgálón.
Alapértelmezett belső maximális életkora | Meghatározza, hogy az alapértelmezett maximális-életkora időközt biztonsági kiszolgálót az eredeti kiszolgáló gyorsítótár ismételt érvényesítése.
Fejléc kezelés lejár | Ha a külső maximális-életkora szolgáltatás aktív szabályozza a Expires fejléc generációját által egy biztonsági kiszolgálót.
Külső maximális-kor | Meghatározza, hogy a peremhálózati kiszolgáló a gyorsítótár ismételt érvényesítése böngészőben maximális-életkora intervallumát.
Kényszerített belső maximális életkora | Határozza meg a biztonsági kiszolgáló az eredeti kiszolgáló gyorsítótár ismételt érvényesítése maximális-életkora intervallumát.
H.264 támogatási (HTTP a progresszív letöltés) | Meghatározza, hogy a tartalom adatfolyamként történő küldéséhez használható H.264 fájlformátumok típusú.
No-Cache kérés elfogadása | Meghatározza, hogy a rendszer egy HTTP-ügyfél no-cache kérelmeket továbbítja az eredeti kiszolgálóra.
Figyelmen kívül hagyja az eredeti No-Cache | Meghatározza, hogy a CDN egyes egy forráskiszolgálóról kiszolgált irányelvek figyelmen kívül hagyja.
Hagyja figyelmen kívül Unsatisfiable tartományok | Meghatározza, hogy a válasz, hogy az ügyfelek számára megjelenített fog, amikor egy kérelem egy 416 kért tartomány nem teljesíthető állapotkódot állítja elő.
Belső maximális elavult | Vezérlők mennyi ideig későbbi, mint a normál lejárati időpont gyorsítótárazott eszköz előfordulhat, hogy szolgálható ki egy biztonsági kiszolgálót, ha a biztonsági kiszolgáló nem tudja kísérelje meg újra az eredeti kiszolgálóra a gyorsítótárazott objektum érvényesítését.
A részleges gyorsítótári megosztása | Azt határozza meg, hogy egy kérelem hozhat létre a részlegesen gyorsítótárazott tartalmat.
Gyorsítótárazott tartalom prevalidate | Meghatározza, hogy a gyorsítótárazott tartalmat legyen abban az esetben jogosult a korai ismételt érvényesítése a TTL lejárata előtt.
Nulla bájtos gyorsítótárban levő fájlok frissítése | Meghatározza, hogy a 0 bájtos gyorsítótár eszköz egy HTTP-ügyfél kérelmet a rendszer hogyan kezelje a peremhálózati kiszolgáló.
Állítsa be a gyorsítótárazható állapotkódok | Meghatározza a gyorsítótárazott tartalom eredményező állapotkódok készletét.
Hiba történt a régi Tartalomkézbesítési | Határozza meg hogy lejárt a gyorsítótárazott tartalmat kézbesíti a rendszer hiba esetén a gyorsítótár ismételt érvényesítése során vagy a felhasználói forráskiszolgálóról a kért tartalom lekérése közben.
Elavult Revalidate közben | Így a peremhálózati kiszolgálóinak kiszolgálására elavult ügyfél számára a kérelmező során kerül sor az ismételt érvényesítése javítja a teljesítményt.
Megjegyzés | A Megjegyzés szolgáltatás lehetővé teszi, hogy a Megjegyzés lehet hozzáadni a szabályban.

###<a name="bandwidth-parameters"></a>Sávszélesség-paraméterek
**Cél:** meghatározza, hogy a sávszélesség-szabályozási paraméterek (például ec_rate és ec_prebuf) aktív legyen.

Sávszélesség-szabályozási paraméter határozza meg, hogy az ügyfél által kért adatátviteli sebesség korlátozott a egyéni mértékben lesz-e.

Érték|eredménye
--|--
Engedélyezve|Lehetővé teszi a sávszélesség-szabályozási kérések tiszteletben peremhálózati kiszolgálókról.
Letiltva|A peremhálózati kiszolgálóinak figyelmen kívül hagyja a sávszélesség-szabályozási paraméterek okoz. A kért tartalmat szolgáltató általában (Ez azt jelenti, hogy a sávszélesség szabályozása nélkül).

**Alapértelmezés:** engedélyezve van.

###<a name="bandwidth-throttling"></a>Sávszélesség-szabályozás
**Cél:** azelőtt gyorsítja fel a sávszélességet, a peremhálózati kiszolgálókról által biztosított válasz.

A következők mindegyikét meg kell határozni, megfelelően állítsa be a sávszélesség-szabályozás.

Beállítás|Leírás
--|--
Kilobájt / másodperc|Ez a beállítás értékre a maximális sávszélesség (Kb / s), amely segítségével a választ.
Prebuf másodpercben|Ez a beállítás értékre peremhálózati kiszolgálókról megvárja a sávszélesség szabályozási másodpercek száma. Ebben az időszakban nem korlátozott sávszélesség az a célja, megakadályozhatja, hogy a media player a sávszélesség-szabályozás miatt szaggatott vagy pufferelési problémákat észlelő.

**Alapértelmezés:** letiltva.

###<a name="bypass-cache"></a>Gyorsítótár megkerülése
**Cél:** határozza meg, hogy a kérelem kihasználhatják a gyorsítótárazási technológiánk.

Érték|eredménye
--|--
Engedélyezve|Hatására az összes kérelmet, az eredeti kiszolgálóra elhagyása, még akkor is, ha a tartalom korábban a peremhálózati kiszolgálóinak kerül a gyorsítótárba.
Letiltva|A gyorsítótár üzletszabályzata előírja a válaszfejlécek definiált gyorsítótár eszközök hatására a peremhálózati kiszolgálóinak.

**Alapértelmezés:**

- **A HTTP nagy:** letiltva

<!---
- **ADN:** Enabled
--->

###<a name="cache-control-header-treatment"></a>Gyorsítótár vezérlő fejléce kezelése
**Cél:** Cache-Control fejlécek generációja meghatározza a peremhálózati kiszolgáló, aktív külső maximális-életkora szolgáltatás esetén.

Az ilyen típusú konfigurációs eléréséhez legkönnyebben helyezhető el a külső maximális életkora és a Cache-Control fejléc-kezelés szolgáltatások ugyanabban az utasításban.

Érték|eredménye
--|--
Felülírása|Biztosítja, hogy a következő műveletek akkor kerül sor:<br/> -Felülírja a Cache-Control-fejlécet az eredeti kiszolgálón állítja elő. <br/>-A Cache-Control-fejlécet, a külső maximális-életkora szolgáltatást, hogy a válasz által előállított ad.
Továbbítása|Biztosítja, hogy a Cache-Control-fejlécet a külső maximális-életkora szolgáltatás által előállított soha nem adja hozzá a válaszhoz. <br/> Ha a forráskiszolgáló Cache-Control fejléc hoz létre, akkor továbbítja a végfelhasználói. <br/> Ha a forráskiszolgáló nem eredményez Cache-Control fejlécet, majd a beállítás hatására a válasz fejléce nem tartalmazza a Cache-Control-fejlécet.
Ha hiányoznak hozzáadása|A Cache-Control fejléc nem érkezett meg a forráskiszolgálóról, ha ezt a beállítást a Cache-Control-fejlécet a külső maximális-életkora szolgáltatás által létrehozott ad hozzá. Ez a beállítás akkor hasznos, annak biztosítására, hogy az összes eszköz hozzá lesz rendelve a Cache-Control-fejlécet.
Eltávolítás| Ez a beállítás biztosítja, hogy a Cache-Control fejléc nem tartalmazza a válasz fejrészét. Ha a Cache-Control fejléc már lett rendelve, akkor a válasz fejrészét a rendszer nélkül.

**Alapértelmezés:** felülírásához.

###<a name="cache-key-query-string"></a>Gyorsítótár-kulcs lekérdezési karakterlánc
**Cél:** beállítja, hogy a gyorsítótár-kulcs tartalmaznak, vagy zárja ki a lekérdezési karakterlánc paramétereinek a kérelemhez társított.

Kapcsolatos információkat:

- Adjon meg egy vagy több lekérdezési karakterlánc paraméter neve. Minden egyes paraméternév kell tagolt, egy szóközzel.
- Ez a szolgáltatás határozza meg, hogy lekérdezési karakterlánc paramétereket tartalmazza vagy kizárja a gyorsítótár-kulcsot. További információ az egyes lehetőségek közül.

Típus|Leírás
--|--
 Tartalmazza|  Azt jelzi, hogy minden egyes megadott paraméter szerepelnie kell a gyorsítótár-kulcsot. Egyedi gyorsítótár-kulcs jön létre minden olyan kérelmet, amely a lekérdezési karakterlánc paraméterként, ez a szolgáltatás definiált egyedi értéket tartalmaz. 
 Az összes  |Azt jelzi, hogy egyedi gyorsítótár-kulcsot hoz létre minden egyes kérelem egy eszköz, amely egy egyedi lekérdezési karakterláncot tartalmaz. Az ilyen típusú konfigurációs általában nem javasolt, mert csekély találatot eredményező gyorsítótárbeli kereséseinek vezethet. Ez megnöveli a terhelést a forrás kiszolgálón, mivel azt kell további kérelmek kiszolgálását. Ez a konfiguráció másolatot készít a gyorsítótár-viselkedést "egyedi-gyorsítótár" a lekérdezési karakterlánc-gyorsítótár oldalon néven ismert. 
 Kizárása | Azt jelzi, hogy csak a megadott paraméterek nem kerülnek bele a gyorsítótár-kulcsot. Minden más lekérdezési karakterlánc paraméter szerepelni fog a gyorsítótár-kulcsot. 
 Az összes kihagyása  |Azt jelzi, hogy az összes lekérdezési karakterlánc paraméterei nem kerülnek bele a gyorsítótár-kulcsot. Ez a konfiguráció másolatot készít az alapértelmezett gyorsítótárazásának "standard-gyorsítótár" a lekérdezési karakterlánc-gyorsítótár oldalon néven ismert. 

HTTP szabálymotor hatványa testreszabása, amelyben lekérdezési karakterláncok gyorsítótárazása megvalósítása módon teszi lehetővé. Megadhatja például, hogy az egyes helyek vagy fájltípusok lekérdezési karakterláncok gyorsítótárazása csak végezhető.

Ha azt szeretné, a lekérdezési karakterlánc a "no-cache" a lekérdezési karakterlánc-gyorsítótár oldalon néven gyorsítótárazásának másolása, majd szüksége lesz egy szabály létrehozására, amely egy URL-cím lekérdezés helyettesítő egyezés feltételt, valamint a Mellőzés gyorsítótár szolgáltatás tartalmaz. A lekérdezés helyettesítő URL-cím egyezik feltétel csillag (*) kell megadni.

#### <a name="sample-scenarios"></a>Mintaforgatókönyvek

A következő példa a szolgáltatáshoz tartozó biztosít egy kérelemmintát és az alapértelmezett gyorsítótár-kulcs:

- **Mintakérelem:** http://wpc.0001.&lt; Tartomány&gt;/800001/Origin/folder/asset.htm?sessionid=1234 & nyelvi = EN & userid = 01
- **Alapértelmezett gyorsítótár-kulcs:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Tartalmazza

Mintakonfiguráció:

- **Típus:** tartalmazza
- **Paraméterek:** nyelv

Ez a fajta konfiguráció a következő lekérdezési karakterlánc paraméter gyorsítótár-kulcsot hoz létre:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Az összes

Mintakonfiguráció:

- **Típus:** az összes

Ez a fajta konfiguráció a következő lekérdezési karakterlánc paraméter gyorsítótár-kulcsot hoz létre:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Kizárása

Mintakonfiguráció:

- **Típus:** kizárása
- **Paraméterek:** sessionid userid

Ez a fajta konfiguráció a következő lekérdezési karakterlánc paraméter gyorsítótár-kulcsot hoz létre:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Az összes kihagyása

Mintakonfiguráció:

- **Típus:** összes kizárása

Ez a fajta konfiguráció a következő lekérdezési karakterlánc paraméter gyorsítótár-kulcsot hoz létre:

    /800001/Origin/folder/asset.htm

###<a name="cache-key-rewrite"></a>Gyorsítótár-kulcs átdolgozás
**Cél:** újraírja a kérelemhez társított gyorsítótár-kulcs.

A gyorsítótár kulcsának pedig a relatív azonosító egy eszköz gyorsítótárazás céljára. Más szóval a kiszolgálóink ellenőrzi, hogy a gyorsítótárazott verzió szerint az elérési út egy eszköz a gyorsítótár-kulcs által definiált konfigurációjának kialakításához.

Ez a szolgáltatás konfigurálása a következő beállításokat is meghatározhat:

Beállítás|Leírás
--|--
Eredeti elérési útja| A relatív elérési út megadása, amelynek gyorsítótárkulcshoz felülíródik kérelmek típusú. Relatív elérési útnak egy alap forrás elérési útvonalának kiválasztásával, és egy Reguláriskifejezés-mintának majd meghatározása definiálhatók.
Új elérési útja|Adja meg az új gyorsítótár-kulcs relatív elérési útja. Relatív elérési útnak egy alap forrás elérési útvonalának kiválasztásával, és egy Reguláriskifejezés-mintának majd meghatározása definiálhatók. A relatív elérési út dinamikusan összeállított változók HTTP használatával
**Alapértelmezés:** egy kérelem gyorsítótár-kulcs határozza meg a kérelem URI-azonosítója.

###<a name="complete-cache-fill"></a>Fejezze be a gyorsítótár kitöltés
**Cél:** határozza meg, mi történik, amikor egy kérelem egy részleges gyorsítótár-tévesztései egy biztonsági kiszolgálót a eredményez.

A részleges gyorsítótár-tévesztései a gyorsítótár állapota az eszköz, amely nem teljesen le vannak töltve egy biztonsági kiszolgálót ismerteti. Ha az eszköz csak részben gyorsítótárában van egy biztonsági kiszolgálót, majd az adott eszköz számára a következő kérés továbbítja újra az eredeti kiszolgálóra.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.
--->
A részleges gyorsítótár-tévesztései rendszerint azért fordul elő, miután a felhasználó megszakít egy letölthető vagy eszközök kizárólag kért tartomány HTTP-kérelmek használatával. Ez rendkívül nagy eszközök esetén a leghasznosabb ahol felhasználók nem általában letöltik azokat (például videók) szolgáltatásra. Ennek köszönhetően ez a funkció alapértelmezés szerint engedélyezve van a HTTP nagy platformon. Le van tiltva az összes többi platformon.

Javasoljuk, hogy az alapértelmezett konfigurációt, a HTTP nagy platform, mivel csökkentik a terhelést, az ügyfél forrás kiszolgálón, és növelje a sebesség, amellyel az ügyfelek a tartalom letöltése.

Mely gyorsítótárában beállítások követi módon, mert ez a funkció nem rendelhető hozzá a következő feltételek egyeznek: peremhálózati Cname, kérelem fejléc literális, kérelem fejléc helyettesítő, URL-cím lekérdezés literális és URL-cím lekérdezés helyettesítő.

Érték|eredménye
--|--
Engedélyezve|Visszaállítja az alapértelmezett viselkedés. Az alapértelmezett viselkedés kényszerítheti a peremhálózati kiszolgáló a forráskiszolgálóról az eszköz a háttérben történő elindítására. Mely után az eszköz a helyi gyorsítótárban a peremhálózati kiszolgáló lesz.
Letiltva|Megakadályozza, hogy egy biztonsági kiszolgálót az az eszköz a háttérben történő végrehajtásához. Ez azt jelenti, hogy az adott régió eszköz a következő kérés hatására egy biztonsági kiszolgálót, hogy a kéréssel az ügyfél eredeti kiszolgálóra.

**Alapértelmezés:** engedélyezve van.

###<a name="compress-file-types"></a>Fájltípusok tömörítése
**Cél:** határozza meg a fájlformátumokat, amelyek tömörítése a kiszolgálón.

Egy fájl formátum használatával az Internet az adathordozó típusát (például Content-Type) adható meg. Internet médiatípus nincs platformfüggetlen metaadatok, amely lehetővé teszi, hogy a kiszolgálóink egy adott eszköz a fájlformátum azonosításához. Közös Internet adathordozó-típusok listája lejjebb tekinthetők meg.

Internet adathordozó-típus|Leírás
--|--
egyszerű szöveg|Egyszerű szöveges fájlokról
text/html| HTML-fájlok
Text/css|Egymásra épülő stíluslap (CSS)
alkalmazás/x-javascript|Javascript
alkalmazás/javascript|Javascript
Kapcsolatos információkat:

- Adjon meg egy szóköz egyenként a határoló többféle adathordozó-típust. 
- Ez a funkció csak ügy módosul, amelynek mérete 1 MB-nál kevesebb eszközök. A kiszolgálók nagyobb eszközök nem lesznek tömörítve.
- Bizonyos típusú tartalmakra, például a video-lemezképek és lejátszása eszközök (például JPG, MP3, MP4 stb.), már tömörített. Ilyen típusú eszközök további tömörítés nem jelentősen romolhat méretét. Ezért ajánlott, hogy nem engedélyezi az ilyen típusú eszközök tömörítést.
- Helyettesítő karakterek, például a csillag, nem támogatottak.
- Ez a funkció hozzáadása egy szabályhoz, előtt győződjön meg arról, hogy állítsa a tömörítési letiltott beállítás a tömörítési oldalon, a platform, amelyhez ez a szabály lesz alkalmazva.

###<a name="default-internal-max-age"></a>Alapértelmezett belső maximális életkora
**Cél:** határozza meg a biztonsági kiszolgáló az eredeti kiszolgáló gyorsítótár ismételt érvényesítése alapértelmezett maximális-életkora intervallumát. Ez azt jelenti mennyi ideig, mielőtt egy biztonsági kiszolgálót adja meg, hogy ellenőrzi, hogy a gyorsítótárazott eszköz megfelel-e az eszköz a forrás kiszolgálón tárolt.

Kapcsolatos információkat:

- Ez a művelet csak akkor kerül sor a válaszok egy forráskiszolgálóról, amely nem társít egy maximális-életkora arra utal, hogy a Cache-Control vagy Expires fejléc található.
- Ez a művelet nem kerül sor eszközök fontosságúnak ítélt nem gyorsítótárazható.
- Ez a művelet nem befolyásolja a peremhálózati kiszolgáló gyorsítótárában revalidations böngészőben. Az ilyen típusú revalidations a Cache-Control vagy Expires fejléc a böngészőre, amely testre szabható a külső maximális-életkora szolgáltatással küldött határozza meg.
- Ez a művelet eredménye nem rendelkezik a válaszfejlécek megfigyelhető hatással, és a tartalmat adott vissza a tartalom peremhálózati kiszolgálókról, de a peremhálózati kiszolgálókról az eredeti kiszolgálóra küldött ismételt érvényesítése forgalom mennyisége hatással lehet.
- Ez a szolgáltatás által konfigurálása:
    - Az állapotkód: egy alapértelmezett belső maximális-életkora alkalmazhassa kiválasztása.
    - Adja meg egy egész számot, és jelölje be a kívánt időegység (például másodperc, perc, óra, stb.). Ez az érték határozza meg az alapértelmezett belső maximális-életkora időköz.

- Beállítást "Ki" időegységét hozzárendel egy alapértelmezett belső maximális-életkora 7 napos időszak, amely nincs hozzárendelve egy maximális-életkora arra utal, hogy a Cache-Control vagy Expires fejléc a kérelmek.
- Mely gyorsítótárában beállítások követi módon, mert ez a funkció nem rendelhető hozzá a következő feltételek egyeznek: 
    - Peremhálózati 
    - CNAME
    - Kérelem fejléc szövegkonstans
    - Kérelem fejléc helyettesítő
    - Kérési módszer
    - Lekérdezés-szövegkonstans URL-címe
    - Lekérdezés helyettesítő URL-címe

**Alapértelmezett érték:** 7 nap

###<a name="expires-header-treatment"></a>Fejléc kezelés lejár
**Cél:** Expires fejléc generációját szabályozza által egy biztonsági kiszolgálót, ha a külső maximális-életkora szolgáltatás aktív.

Az ilyen típusú konfigurációs eléréséhez legkönnyebben helyezhető el a külső maximális életkora és a lejárati fejléc-kezelés szolgáltatások ugyanabban az utasításban.

Érték|eredménye
--|--
Felülírása|Biztosítja, hogy a következő műveletek akkor kerül sor:<br/>-Felülírja az eredeti kiszolgálón állítja elő Expires fejléc.<br/>-A a külső maximális-életkora szolgáltatást, hogy a válasz által előállított Expires fejléc hozzáadása.
Továbbítása|Biztosítja, hogy a a külső maximális-életkora szolgáltatás által előállított Expires fejléc soha nem adja hozzá a válaszhoz. <br/> Ha a forráskiszolgáló egy Expires fejléc hoz létre, akkor továbbítja a végfelhasználói. <br/>Ha a forráskiszolgáló nem hoz egy Expires fejléc, ez a beállítás egy Expires fejléc nem tartalmaz a válaszfejlécet okozhat.
Ha hiányoznak hozzáadása| Egy Expires fejléc nem érkezett meg a forráskiszolgálóról, ha ez a beállítás nagyobb a a külső maximális-életkora szolgáltatás által előállított Expires fejléc. Ez a beállítás akkor hasznos, annak biztosítására, hogy az összes eszköz kioszt egy Expires fejléc.
Eltávolítás| Ellenőrzi, hogy egy Expires fejléc nem tartalmazza a válasz fejrészét. Ha már használja egy Expires fejléc, majd azt fogja lehet üres karaktert törölni a válasz fejrészét a.

**Alapértelmezés:** felülírása

###<a name="external-max-age"></a>Külső maximális-kor
**Cél:** határozza meg a peremhálózati kiszolgáló a gyorsítótár ismételt érvényesítése böngészőben maximális-életkora intervallumát. Ez azt jelenti mennyi ideig, mielőtt egy böngésző adja meg, hogy egy eszköz egy biztonsági kiszolgálót az új verzióhoz tartozó ellenőrizheti.

A funkció engedélyezése hoz létre gyorsítótár-vezérlés: maximális-kor és a peremhálózati kiszolgálókról fejlécek lejár, és küldje el a HTTP-ügyfél. Alapértelmezés szerint ezek a fejlécek felülírja az eredeti kiszolgálóra által létrehozott. Azonban a Cache-Control fejléc kezelését és a lejárati fejléc-kezelés szolgáltatások használható útválasztását ezen viselkedés megváltoztatásához.

Kapcsolatos információkat:

- Ez a művelet nem befolyásolja a peremhálózati kiszolgáló az eredeti kiszolgáló gyorsítótár revalidations. Ilyen típusú revalidations határozza meg a gyorsítótár-Control vagy Expires fejléc kapott a forráskiszolgálóról, és testre szabható az alapértelmezett belső maximális-kor és a kényszerített belső maximális életkora szolgáltatásokkal.
- Adja meg egy egész számot, majd válassza a kívánt időegység (például másodperc, perc, óra, stb.) Ez a szolgáltatás konfigurálása
- Ez a funkció beállítása negatív értékre után a peremhálózati kiszolgálókról küldeni a gyorsítótár-vezérlés: nem-gyorsítótár és a lejárati ideje ameddig az egyes a böngészőnek adott válaszban a múltban van beállítva. Bár a HTTP-ügyfél nem lesz gyorsítótárazza a választ, ez a beállítás nem érinti a peremhálózati kiszolgáló gyorsítótárazza a választ a forráskiszolgálóról lehessen.
- Az attribútum "off" időegységét letiltja ezt a szolgáltatást. A gyorsítótár-Control vagy Expires fejléc gyorsítótárazza a választ az eredeti kiszolgáló továbbítja a böngészőben.

**Alapértelmezés:** kikapcsolása

###<a name="force-internal-max-age"></a>Kényszerített belső maximális életkora
**Cél:** határozza meg a biztonsági kiszolgáló az eredeti kiszolgáló gyorsítótár ismételt érvényesítése maximális-életkora intervallumát. Ez azt jelenti mennyi ideig, mielőtt egy biztonsági kiszolgálót adja meg, hogy ellenőrizheti, hogy a gyorsítótárazott eszköz megfelel-e az eszköz a forrás kiszolgálón tárolt.

Kapcsolatos információkat:

- Ez a funkció felülírja a maximális-életkora időköze a Cache-Control vagy Expires fejléc jön létre egy forráskiszolgálóról van megadva.
- Ez a funkció nem befolyásolja a peremhálózati kiszolgáló gyorsítótárában revalidations böngészőben. Az ilyen típusú revalidations a Cache-Control vagy Expires fejléc küldése a böngészőnek határozza meg.
- Ez a szolgáltatás nem rendelkezik a választ igénylő kézbesíti az egy biztonsági kiszolgálót megfigyelhető hatással. Azonban a peremhálózati kiszolgálókról az eredeti kiszolgálóra küldött ismételt érvényesítése forgalom mennyisége hatással lehet.
- Ez a szolgáltatás által konfigurálása:
    - Válassza az állapotkódot, amely egy belső maximális életkora alkalmazza.
    - Adja meg egy egész számot, és kiválasztja a kívánt időegység (például másodperc, perc, óra, stb.). Ez az érték határozza meg a kérelem maximális-életkora időköz.

- Az attribútum "off" időegységét letiltja ezt a szolgáltatást. Belső maximális-életkora időközönkénti nem rendeli hozzá a kért eszközök. Ha az eredeti fejléc nem tartalmaz a gyorsítótárazási információkat, majd az eszköz a gyorsítótárba fognak kerülni az alapértelmezett belső maximális-életkora funkció az aktív beállításnak megfelelően.
- Mely gyorsítótárában beállítások követi módon, mert ez a funkció nem rendelhető hozzá a következő feltételek egyeznek: 
    - Peremhálózati 
    - CNAME
    - Kérelem fejléc szövegkonstans
    - Kérelem fejléc helyettesítő
    - Kérési módszer
    - Lekérdezés-szövegkonstans URL-címe
    - Lekérdezés helyettesítő URL-címe

**Alapértelmezés:** kikapcsolása

###<a name="h264-support-http-progressive-download"></a>H.264 támogatási (HTTP a progresszív letöltés)
**Cél:** meghatározza, hogy a tartalom adatfolyamként történő küldéséhez használható H.264 fájlformátumok típusú.

Kapcsolatos információkat:

- A kívánt fájlkiterjesztéseket beállítás engedélyezett H.264 fájlnév-kiterjesztések egy szóközökkel elválasztott kötetnevek kulcstulajdonságokat határozza meg. A kívánt fájlkiterjesztéseket beállítás felülírja az alapértelmezett viselkedés. Ha ezt a beállítást, többek között azokat a fájlkiterjesztéseket karbantartásához MP4 és F4V támogatása. 
- Ügyeljen arra, hogy egy időszakot minden fájlnév-kiterjesztés (például .mp4 .f4v) megadásakor.

**Alapértelmezés:** alapértelmezés szerint HTTP progresszív letöltés MP4 és F4V media támogatja.

###<a name="honor-no-cache-request"></a>No-cache kérés elfogadása
**Cél:** határozza meg, hogy egy HTTP-ügyfél által no-cache kérelmeket továbbítja az eredeti kiszolgálóra.

A HTTP-ügyfél üzenetet küld egy gyorsítótárban történik a no-cache kérelem-vezérlés: nem-gyorsítótár és/vagy Pragma:no-gyorsítótár fejléc a következő a HTTP-kérelem.

Érték|eredménye
--|--
Engedélyezve|Lehetővé teszi, hogy egy HTTP-ügyfél no-cache kéri, hogy az eredeti kiszolgálóra továbbítja, és az eredeti kiszolgálóra visszatérhet a válaszfejlécek és a szervezet a peremhálózati kiszolgáló keresztül vissza a HTTP-ügyfél.
Letiltva|Visszaállítja az alapértelmezett viselkedés. Az alapértelmezett viselkedés célja no-cache kérelmeket az eredeti kiszolgálóra történő továbbítását.

Minden éles forgalomhoz lehetőleg Ez a szolgáltatás le van tiltva alapértelmezett állapotában hagyja. Ellenkező esetben származási kiszolgálók fog védelme nem biztosítható a végfelhasználók számára, akik véletlenül indíthat sok no-cache kérelem weblapok frissítésekor vagy a a számos népszerű médialejátszókhoz, amely minden videó kérelemmel no-cache fejléc küldése van kódolva. Ez a funkció azonban bizonyos nem éles átmeneti vagy tesztelés könyvtárak, annak érdekében, hogy a forráskiszolgálóról igény szerinti lekérése friss tartalom alkalmazandó hasznos lehet.

A gyorsítótár egy kérelmet, amelyet továbbítani kell az eredeti kiszolgálóra miatt ez a szolgáltatás számára küldött állapota TCP_Client_Refresh_Miss. A gyorsítótár állapotok jelentést, amely nem érhető el az alapvető jelentéskészítési modul hasonlóan, a gyorsítótár állapot szerint statisztikai információkat nyújt. Ez lehetővé teszi, hogy számát és a továbbított kérelmek százalékos követésére miatt ez a funkció az eredeti kiszolgálóra.

**Alapértelmezés:** letiltva.

###<a name="ignore-origin-no-cache"></a>Figyelmen kívül hagyja az eredeti no-cache
**Cél:** határozza meg, hogy a CDN figyelmen kívül hagyja a következő irányelvek kiszolgált egy forráskiszolgálóról:

- A Cache-Control: titkos
- A Cache-Control: no-tároló
- A Cache-Control: no-cache
- Pragma: no-cache

Kapcsolatos információkat:

- Ez a szolgáltatás konfigurálja, amelynek a fenti irányelvek figyelmen kívül hagyja az állapotkódnak szóközökkel elválasztott kötetnevek listáját meghatározásával.
- Ez a szolgáltatás érvényes állapotkódjai készletét vannak: 200, 203, 300, 301, 302, 305, 307, 400, 401-es, 402, 403-as, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502-es, 503-as, 504, és 505.
- Üres értékre állításával tiltsa le ezt a szolgáltatást.
- Mely gyorsítótárában beállítások követi módon, mert ez a funkció nem rendelhető hozzá a következő feltételek egyeznek: 
    - Peremhálózati 
    - CNAME
    - Kérelem fejléc szövegkonstans
    - Kérelem fejléc helyettesítő
    - Kérési módszer
    - Lekérdezés-szövegkonstans URL-címe
    - Lekérdezés helyettesítő URL-címe

**Alapértelmezés:** az alapértelmezett viselkedést, hogy a fenti irányelvek figyelembe veszi.

###<a name="ignore-unsatisfiable-ranges"></a>Hagyja figyelmen kívül Unsatisfiable tartományok 
**Cél:** meghatározza, hogy az ügyfelek számára megjelenített fog, amikor egy kérelem egy 416 kért tartomány nem teljesíthető állapotkódot állítja elő a választ.

Alapértelmezés szerint ez az állapot kód érték érkezett vissza a megadott bájttartomány-kérelem nem tud teljesíteni egy biztonsági kiszolgálót, és egy If tartományon kívüli kérelmet fejlécmező nem volt megadva.

Érték|eredménye
-|-
Engedélyezve|Megakadályozza, hogy a peremhálózati kiszolgálókról egy 416 kért tartomány nem teljesíthető állapotkód: Érvénytelen bájttartomány-kérelem válaszol. Ehelyett a kiszolgálóink biztosítanak a kért eszköz, és egy 200 OK vissza az ügyfélnek.
Letiltva|Visszaállítja az alapértelmezett viselkedés. Az alapértelmezett viselkedés a 416 kért tartomány nem teljesíthető állapotkód: tiszteletben.

**Alapértelmezés:** letiltva.

###<a name="internal-max-stale"></a>Belső maximális elavult
**Cél:** vezérlők mennyi ideig későbbi, mint a normál lejárati időpont gyorsítótárazott eszköz előfordulhat, hogy szolgálható ki egy biztonsági kiszolgálót, ha a biztonsági kiszolgáló nem tudja kísérelje meg újra az eredeti kiszolgálóra a gyorsítótárazott objektum érvényesítését.

Normális esetben ha egy eszköz korára maximális időtartam lejár, a peremhálózati kiszolgáló elküld ismételt érvényesítése kérelmet az eredeti kiszolgálóra. A forrás server lesz majd vagy 304 válaszol ahhoz, hogy megkapja a biztonsági kiszolgáló friss nem módosított címbérlet a gyorsítótárazott eszköz – a 200 OK biztosításához a peremhálózati kiszolgáló a gyorsítótárban lévő eszköz frissített verzióra.

A biztonsági kiszolgáló nem tud kapcsolatot az eredeti kiszolgálóra ilyen ismételt érvényesítése közben, a belső maximális elavult funkció szabályozza, hogy és mennyi ideig, a peremhálózati kiszolgáló továbbra is szolgál a most már elavult eszköz-e.

Vegye figyelembe, hogy ez alatt az időtartam alatt kezdődik, amikor az eszköz maximális-életkora lejár, nem a sikertelen ismételt érvényesítése esetén. A maximális idő, amikor egy eszköz sikeres ismételt érvényesítése nélkül szolgáltatható ezért maximális-kor és a max elavult kombinációja által megadott idő. Például ha egy eszköz került a gyorsítótárba, 9:00, a maximális-életkora 30 perc és 15 percenként maximális elavult, majd egy sikertelen ismételt érvényesítése kísérlet 9:44 eredményeznének fogadása az elavult gyorsítótárazott adategységhez, amíg egy sikertelen ismételt érvényesítése kísérlet 9:46 eredményeznének egy 504 fogadó felhasználó felhasználó átjáró időtúllépése.

Bármely ezt a szolgáltatást felváltotta gyorsítótár konfigurált érték-vezérlés: kell-kísérelje meg újra érvényesítését, vagy a gyorsítótár-vezérlés: proxy-kísérelje meg újra az eredeti kiszolgálóra kapott fejlécek érvényesítését. Ha ezeket a fejléceket fogadásakor. a forráskiszolgálóról, ha egy eszköz kezdetben gyorsítótárazza, majd a biztonsági kiszolgáló nem teljesíti a gyorsítótárazott elavult eszköz. Ebben az esetben ha a biztonsági kiszolgáló nem tudja kísérelje meg újra érvényesítését az eredeti amikor az eszköz maximális-életkora időköz lejárt, akkor a peremhálózati kiszolgáló vissza 504-es számú átjáró időtúllépése.

Kapcsolatos információkat:

- Ez a szolgáltatás által konfigurálása:
    - Az állapotkódot, amelynek maximális elavult alkalmazandó kiválasztása.
    - Adja meg egy egész számot, és jelölje be a kívánt időegység (például másodperc, perc, óra, stb.). Ez az érték határozza meg a belső maximális elavult alkalmazni fogja őket.

- Az attribútum "off" időegységét letiltja ezt a szolgáltatást. A gyorsítótárazott eszköz nem tudja megjeleníteni a normál lejárati ideje túl.
- Mely gyorsítótárában beállítások követi módon, mert ez a funkció nem rendelhető hozzá a következő feltételek egyeznek: 
    - Peremhálózati 
    - CNAME
    - Kérelem fejléc szövegkonstans
    - Kérelem fejléc helyettesítő
    - Kérési módszer
    - Lekérdezés-szövegkonstans URL-címe
    - Lekérdezés helyettesítő URL-címe

**Alapértelmezés:** két percen belül megkezdődik

###<a name="partial-cache-sharing"></a>A részleges gyorsítótári megosztása
**Cél:** határozza meg, hogy egy kérelem hozhat létre a részlegesen gyorsítótárazott tartalmat.

A részleges gyorsítótári felhasználhatja az az adott tartalomhoz új kérelmeinek teljesítéséhez, amíg a kért tartalom gyorsítótárazva van, teljes mértékben.

Érték|eredménye
-|-
Engedélyezve|Kérelmek hozhat létre a részlegesen gyorsítótárazott tartalmat.
Letiltva|Kérelmek csak hozhat létre a kért tartalom egy teljes mértékben gyorsítótárazott verziója.

**Alapértelmezés:** letiltva.

###<a name="prevalidate-cached-content"></a>Gyorsítótárazott tartalom prevalidate
**Cél:** meghatározza, hogy a gyorsítótárazott tartalmat legyen abban az esetben jogosult a korai ismételt érvényesítése a TTL lejárata előtt.

Adja meg az idő során, amely jogosult a korai ismételt érvényesítése lesz a kért tartalom TTL lejárta előtt.

Kapcsolatos információkat:

- Élettartam lejárt kiválasztása "Off" időegységét van szüksége a gyorsítótárazott tartalom követően kerül sor ismételt érvényesítése. Idő nem adható meg, és figyelmen kívül.

**Alapértelmezés:** ki. Ismételt érvényesítése kerül sor a gyorsítótárazott tartalom TTL lejárata után.

###<a name="refresh-zero-byte-cache-files"></a>Nulla bájt gyorsítótárban levő fájlok frissítése
**Cél:** határozza meg a 0 bájtos gyorsítótár eszköz egy HTTP-ügyfél kérelmet a rendszer hogyan kezelje a peremhálózati kiszolgáló.

Érvényes értékek a következők:

Érték|eredménye
--|--
Engedélyezve|Hatására a peremhálózati kiszolgáló refetch az eszköz a forráskiszolgálóról.
Letiltva|Visszaállítja az alapértelmezett viselkedés. Az alapértelmezett viselkedés be érvényes gyorsítótár eszközöket kérelem kiszolgálásához.
Ez a funkció nincs szükség a megfelelő gyorsítótárazás és a továbbítási, de hasznos megoldás lehet. Például a dinamikus tartalom generátorokat származási kiszolgálókon véletlenül eredményezhet küldi el a peremhálózati kiszolgálóinak 0 bájtos válaszokat. Ilyen típusú válaszokat a rendszer jellemzően gyorsítótárzza a peremhálózati kiszolgáló. Ha tudja, hogy, hogy a 0 bájtos válasz soha nem egy érvényes válasz 

az ilyen tartalmat majd ezt a szolgáltatást előfordulhat, hogy ilyen típusú eszközök nem lehetséges a kiszolgálása az ügyfelek számára.

**Alapértelmezés:** letiltva.

###<a name="set-cacheable-status-codes"></a>Állítsa be a gyorsítótárazható állapotkódok
**Cél:** meghatározza a gyorsítótárazott tartalom eredményező állapotkódok készletét.

Alapértelmezés szerint gyorsítótárazás csak a 200 OK válaszok engedélyezték.

Adja meg a kívánt állapotkódok szóközökkel elválasztott kötetnevek készlete.

Kapcsolatos információkat:

- A forrás No-Cache figyelmen kívül hagyása funkció engedélyezéséhez. Ha ez a funkció nincs engedélyezve, majd 200 OK válaszok előfordulhat, hogy nem kerülnek a gyorsítótárba.
- Ez a szolgáltatás érvényes állapotkódjai készletét vannak: 203, 300, 301, 302, 305, 307, 400, 401-es, 402, 403-as, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502-es, 503-as, 504, és 505.
- Ez a funkció nem használható a 200 OK állapotkód eredményező válaszok gyorsítótárazásának letiltása.

**Alapértelmezés:** csak 200 OK állapotkód eredményező válaszok esetében a gyorsítótárazás engedélyezve van.
###<a name="stale-content-delivery-on-error"></a>Hiba történt a régi Tartalomkézbesítési
**Cél:** 

Határozza meg hogy lejárt a gyorsítótárazott tartalmat kézbesíti a rendszer hiba esetén a gyorsítótár ismételt érvényesítése során vagy a felhasználói forráskiszolgálóról a kért tartalom lekérése közben.

Érték|eredménye
-|-
Engedélyezve|Hiba előfordulásakor egy egy eredeti kiszolgálóhoz való kapcsolódáskor elavult tartalom a kérelmező kiszolgálása között.
Letiltva|A forráskiszolgáló hiba a kérelmező rendszer továbbítja.

**Alapértelmezés:** letiltva

###<a name="stale-while-revalidate"></a>Elavult Revalidate közben
**Cél:** kiszolgálására elavult tartalom a kérelmező, amíg ismételt érvényesítése akkor történik meg peremhálózati kiszolgálókról engedélyezésével javítja a teljesítményt.

Kapcsolatos információkat:

- A szolgáltatás működését a kiválasztott időegység függően változik.
    - **Időegység:** adja meg az az időtartam, és jelölje ki a idő (például másodperc, perc, óra, stb.) elavult tartalomkézbesítési engedélyezése. Az ilyen típusú telepítés lehetővé teszi, hogy a CDN és a, hogy mennyi idő kérhet kiterjesztése tartalom érvényesítése a következő képlettel megkövetelése előtt:**TTL** + **elavult közben kísérelje meg újra érvényesítését idő** 
    - **OFF:** Select "Kikapcsolva" ismételt érvényesítése előtt kérelmet megkövetelése az elavult tartalom kezdeményezheti.
        - Ne adjon meg az időtartam, mert törlendő, és figyelmen kívül hagyja.

**Alapértelmezés:** ki. Ismételt érvényesítése szükséges, mielőtt a kért tartalom szolgálhatók ki.

###<a name="comment"></a>Megjegyzés
**Cél:** lehetővé teszi, hogy a Megjegyzés a szabályban lehet hozzáadni.

Kiegészítő információt nyújt az általános célú miért egy adott feltétel felel meg, vagy a szolgáltatás hozzáadva a szabály egy szabály vagy a funkció használatát is.

Kapcsolatos információkat:

- Legfeljebb 150 karakter adható meg.
- Csak alfanumerikus karaktereket használjon.
- Ez a funkció nem befolyásolja a szabály viselkedését. Csupán célja, hogy adjon meg egy olyan területre, ahol megadhatja a későbbi használatra és, amely segíthet a szabály hibaelhárítási.
 
## <a name="headers"></a>Fejlécek

Ezek a Funkciók hozzáadása, módosítása és a kérés vagy válasz fejlécek törlése tervezték.

Név | Cél
-----|--------
Kor válaszfejléc | Határozza meg, hogy egy kora válaszfejléc lesz a válaszként küldött a kérelmező.
Gyorsítótár válaszfejlécek hibakeresése | Határozza meg, hogy a válasz magukban az X-EK-Debug válaszfejléc, amely információt nyújt a gyorsítótár-házirend a kért objektum.
Ügyfél fejléc módosítása | Felülírja, hozzáfűzi vagy fejléc töröl egy kérelmet.
Módosítsa az ügyfél válaszfejléc | Felülírja, hozzáfűzi vagy fejléc töröl egy választ.
Ügyfél IP-egyéni fejléc beállítása | Lehetővé teszi, hogy fel kell venni a kérésre egyéni fejléc a kérelmet küldő ügyfél IP-címét.

###<a name="age-response-header"></a>Kor válaszfejléc
**Cél**: határozza meg, hogy egy kora válaszfejléc lesz a válaszként küldött a kérelmező.
Érték|eredménye
--|--
Engedélyezve | A Korszűrő válaszfejléc szerepelni fog a választ küldött a kérelmező.
Letiltva | A Korszűrő válaszfejléc nem kerülnek be a választ küldött a kérelmező.

**Alapértelmezett viselkedés**: letiltva.

###<a name="debug-cache-response-headers"></a>Gyorsítótár válaszfejlécek hibakeresése
**Cél:** határozza meg, hogy a válasz magukban az X-EK-Debug válaszfejléc, amely információt nyújt a gyorsítótár-házirend a kért objektum.

Hibakeresési gyorsítótári választ fejlécek fog szerepelni a válasz a következő két teljesülése esetén:

- A hibakeresési gyorsítótár válasz fejlécek funkció engedélyezve van a kívánt kérésre.
- A fenti kérelem a debug gyorsítótár válaszfejlécek a válaszban szereplő csoportját határozza meg.

Gyorsítótár válasz fejlécek kérheti többek között a következő fejléc és a kívánt irányelvek a kérelemben szereplő Debug:

X-EK-Debug: _Directive1_,_Directive2_,_DirectiveN_

**Példa**

X-EK-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Érték|eredménye
-|-
Engedélyezve|Hibakeresési gyorsítótár válaszfejlécek kérelmek visszaadható egy választ, amely az X-EK-Debug fejlécet tartalmaz.
Letiltva|Az X-EK-Debug válaszfejléc nem kerülnek be a válasz.

**Alapértelmezés:** letiltva.

###<a name="modify-client-response-header"></a>Módosítsa az ügyfél válaszfejléc
**Cél:** minden kérelmet tartalmaz [kérelem fejlécei]() , amely írják le. Ez a szolgáltatás következő lehetőségek közül választhat:

- Hozzáfűzendő, vagy az fejléc rendelt érték. Ha a megadott kérelemfejlécet nem létezik, majd ezt a szolgáltatást fog vegye fel a kérelmet.
- A kérelem fejléc törlése.

Az eredeti kiszolgálóra továbbított kérelmeket Ez a szolgáltatás által végrehajtott módosítások fogja tartalmazni.

A következő műveletek valamelyikét hajthatja végre, a fejléc:

Beállítás|Leírás|Példa
-|-|-
Hozzáfűzés|A megadott értékét a rendszer hozzáadja a meglévő kérelem fejléc értékének végéhez.|**A kérelem fejléc értéke (ügyfél):**érték1 <br/> **Kérelem fejléc értéke (HTTP szabálymotor):** érték2 <br/>**Új kérelem fejléc értéke:** Value1Value2
Felülírása|A kérelem fejléc értéke lesz a megadott értékre.|**A kérelem fejléc értéke (ügyfél):**érték1 <br/>**Kérelem fejléc értéke (HTTP szabálymotor):** érték2 <br/>**Új kérelem fejléc értéke:** érték2 <br/>
Törlés|Törli a megadott kérelemfejlécet.|**A kérelem fejléc értéke (ügyfél):**érték1 <br/> **Ügyfél fejléc konfigurációjának módosítása:** törölje a szóban forgó kérelemfejlécet. <br/>**Eredmény:** a megadott kérelemfejlécet a rendszer nem továbbítja az eredeti kiszolgálóra.

Kapcsolatos információkat:

- Győződjön meg arról, hogy a meghatározott a beállítás értéke a kívánt kérelemfejléc pontos egyezést.
- Eset nem veszi figyelembe fejléc azonosítása céljából. Például a Cache-Control fejléc neve a következő lehetőségek bármelyikét segítségével képes azonosítani:
    - a cache-control
    - A CACHE-CONTROL
    - a cachE-Control
- A fejléc neve megadásakor csak alfanumerikus karaktereket, kötőjelek és aláhúzásjelek használja.
- A fejléc törlése megakadályozza azt egy forrás-kiszolgáló peremhálózati kiszolgálókról történő továbbítását.
- A következő fejlécek fenntartva, ezért ezt a funkciót nem lehet módosítani:
    - továbbított
    - állomás
    - keresztül
    - Figyelmeztetés
    - x-továbbított-számára
    - Az összes fejléc neve az "x-EK" vannak fenntartva.

###<a name="modify-client-response-header"></a>Módosítsa az ügyfél válaszfejléc
Minden válasz tartalmaz [válaszfejlécek]() , amely írják le. Ez a szolgáltatás következő lehetőségek közül választhat:

- Hozzáfűzendő, vagy az egy válaszfejléc rendelt érték. Ha a megadott kérelemfejlécet nem létezik, majd a szolgáltatás felveszi a válaszhoz.
- A válasz egy válaszfejléc törlése.

Alapértelmezés szerint válasz fejléce értékek meg vannak határozva az eredeti kiszolgálóra és a peremhálózati kiszolgálókról.

A következő műveletek egyikét a válaszfejléc hajtható végre:

Beállítás|Leírás|Példa
-|-|-
Hozzáfűzés|A megadott értékét a rendszer hozzáadja a meglévő kérelem fejléc értékének végéhez.|**Válasz állomásfejléc-érték (ügyfél):**érték1 <br/> **Válasz állomásfejléc-érték (HTTP szabálymotor):** érték2 <br/>**Új válasz állomásfejléc-érték:** Value1Value2
Felülírása|A kérelem fejléc értéke lesz a megadott értékre.|**Válasz állomásfejléc-érték (ügyfél):**érték1 <br/>**Válasz állomásfejléc-érték (HTTP szabálymotor):** érték2 <br/>**Új válasz állomásfejléc-érték:** érték2 <br/>
Törlés|Törli a megadott kérelemfejlécet.|**A kérelem fejléc értéke (ügyfél):** érték1 <br/> **Ügyfél kérelem fejléc konfigurációjának módosítása:** törölje a szóban forgó válaszfejlécet. <br/>**Eredmény:** a megadott válaszfejlécet a rendszer nem továbbítja a kérelmezőnek.

Kapcsolatos információkat:

- Győződjön meg arról, hogy a meghatározott a beállítás értéke a kívánt választ fejléc pontos egyezést. 
- Eset nem veszi figyelembe fejléc azonosítása céljából. Például a Cache-Control fejléc neve a következő lehetőségek bármelyikét segítségével képes azonosítani:
    - a cache-control
    - A CACHE-CONTROL
    - a cachE-Control
- A fejléc törlése megakadályozzák a igénylő lesznek továbbítva.
- A következő fejlécek fenntartva, ezért ezt a funkciót nem lehet módosítani:
    - fogadja el-kódolás
    - kora
    - kapcsolat
    - tartalom kódolása
    - tartalom hosszúságú
    - tartalom tartományon.
    - Dátum
    - kiszolgáló
    - pótkocsi
    - Transfer-encoding
    - Frissítés
    - eltérő
    - keresztül
    - Figyelmeztetés
    - Az összes fejléc neve az "x-EK" vannak fenntartva.

###<a name="set-client-ip-custom-header"></a>Ügyfél IP-egyéni fejléc beállítása
**Cél:** ad hozzá egy egyéni fejlécet, amely azonosítja a kérést küldő ügyfél IP-címet a kérelmet.

A fejléc neve beállítás határozza meg az ügyfél IP-cím tárolására szolgáló egyéni kérelemfejléc nevét.

Ez a funkció lehetővé teszi, hogy az ügyfél eredeti kiszolgálóra megállapítása a ügyfél IP-címek egyéni fejléc keresztül. Ha a kérés van kiszolgálása a gyorsítótárból, majd az eredeti kiszolgálóra nem figyelmeztet az ügyfél IP-cím. Ezért ajánlott, hogy ez a szolgáltatás használható ADN vagy eszközök, amelyek nem kerülnek a gyorsítótárba.

Győződjön meg arról, hogy a megadott fejléc neve nem egyezik a következő bármelyike:

- Standard kérelem nevével. Szokásos fejlécben neveinek listáját található [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Fenntartott nevek:
    - továbbítja a
    - állomás
    - eltérő
    - keresztül
    - Figyelmeztetés
    - x-továbbított-számára
    - Az összes fejléc neve az "x-EK" vannak fenntartva.
 
## <a name="logs"></a>Logs

Ezek a szolgáltatások úgy tervezték, hogy testre szabhatja a nyers naplófájlok tárolt adatokat.

Név | Cél
-----|--------
1. egyéni mező | Meghatározza, hogy a formátum és a tartalom, amely a nyers naplófájl egyéni napló mezője lesz hozzárendelve.
Napló lekérdezési karakterlánc | Meghatározza, hogy egy lekérdezési karakterlánc belépési naplók URL-cím mellett kell tárolni.

###<a name="custom-log-field-1"></a>1. egyéni mező
**Cél:** meghatározza, hogy a formátum és a tartalom, amely a nyers naplófájl egyéni napló mezője lesz hozzárendelve.

Az egyéni mező mögött fő célja a segítségével meghatározhatja, melyik kérés engedélyezése és válasz térközkaraktert fogja tárolni a rendszernapló fájljaiban.

Alapértelmezés szerint az egyéni mező neve "x-ec_custom-1." Azonban ez a mező nevét szabható a [nyers naplózási beállításai oldal]().

A formázás, hogy adja meg a kérés- és válaszfejlécekről használata javasolt van megadva.

Fejléc típusa|Formátumban|Példák
-|-|-
Fejléc|%{[RequestHeader]()}[i]() | A(z) % {elfogadja-kódolás} i <br/> {Hivatkozó} i <br/> A(z) % {engedélyezési} i
Válaszfejléc|%{[ResponseHeader]()}[o]()| A(z) % {kora} o <br/> A(z) % {content-Type} o <br/> A(z) % {cookie-k} o

Kapcsolatos információkat:

- Egy egyéni napló mező fejlécmezők és egyszerű szöveg tetszőleges kombinációját tartalmazhatja.
- Az ebben a mezőben érvényes karakterek a következők: alfanumerikus (0-9, a – z és A-Z), kötőjeleket, kettőspontokat, pontosvesszővel kell elválasztani, aposztrófot, vesszővel válassza el egymástól, időszakok, aláhúzásjeleket, egyenlőségjelet, kerek zárójeleket tartalmazhatnak, zárójelek és szóközöket. A százalékos szimbólum és a kapcsos zárójelek csak akkor vannak engedélyezve, ha egy fejlécmező meghatározására szolgál.
- A megadott fejléc mezők helyesírás meg kell egyeznie a kívánt kérelem/válasz-fejléc nevét.
- Ha azt szeretné, több fejléc adhatja meg, majd javasoljuk, hogy az elválasztó ezzel jelezheti minden fejléc. Használhat például rövidítése minden fejléc. Szintaxis lejjebb tekinthetők meg.
    - AE: % {elfogadja-kódolás} i A: % {engedélyezési} i ki: % {Content-Type} o 

**Alapértelmezett érték:** -

###<a name="log-query-string"></a>Napló lekérdezési karakterlánc
**Cél:** határozza meg, hogy egy lekérdezési karakterlánc belépési naplók URL-cím mellett kell tárolni.

Érték|eredménye
-|-
Engedélyezve|A hozzáférési napló URL-címek rögzítésekor lehetővé teszi, hogy a lekérdezési karakterláncok tárolására. Ha egy URL-címe nem tartalmazhat lekérdezési karakterláncot, majd ezt a beállítást nem lesz hatása.
Letiltva|Visszaállítja az alapértelmezett viselkedés. Az alapértelmezett viselkedés URL-címek egy hozzáférési napló rögzítésekor a lekérdezési karakterláncok figyelmen kívül.

**Alapértelmezés:** letiltva.

<!---
## Optimize

These features determine whether a request will undergo the optimizations provided by Edge Optimizer.

Name | Purpose
-----|--------
Edge Optimizer | Determines whether Edge Optimizer can be applied to a request.
Edge Optimizer – Instantiate Configuration | Instantiates or activates the Edge Optimizer configuration associated with a site.

###Edge Optimizer
**Purpose:** Determines whether Edge Optimizer can be applied to a request.

If this feature has been enabled, then the following criteria must also be met before the request will be processed by Edge Optimizer:

- The requested content must use an edge CNAME URL.
- The edge CNAME referenced in the URL must correspond to a site whose configuration has been activated in a rule.

This feature requires the ADN platform and the Edge Optimizer feature.

Value|Result
-|-
Enabled|Indicates that the request is eligible for Edge Optimizer processing.
Disabled|Restores the default behavior. The default behavior is to deliver content over the ADN platform without any additional processing.

**Default Behavior:** Disabled
 

###Edge Optimizer - Instantiate Configuration
**Purpose:** Instantiates or activates the Edge Optimizer configuration associated with a site.

This feature requires the ADN platform and the Edge Optimizer feature.

Key information:

- Instantiation of a site configuration is required before requests to the corresponding edge CNAME can be processed by Edge Optimizer.
- This instantiation only needs to be performed a single time per site configuration. A site configuration that has been instantiated will remain in that state until the Edge Optimizer – Instantiate Configuration feature that references it is removed from the rule.
- The instantiation of a site configuration does not mean that all requests to the corresponding edge CNAME will automatically be processed by Edge Optimizer. The Edge Optimizer feature determines whether an individual request will be processed.

If the desired site does not appear in the list, then you should edit its configuration and verify that the Active option has been marked.

**Default Behavior:** Site configurations are inactive by default.
--->

## <a name="origin"></a>Forrás

Ezek a szolgáltatások úgy tervezték, hogy szabályozza, hogy a CDN hogyan kommunikál az eredeti kiszolgálóra.

Név | Cél
-----|--------
Életben tartási kérelmek maximális száma | Keep-Alive kapcsolat kérelmek maximális számát határozza meg, mielőtt le van zárva.
Proxy különleges fejlécek | Meghatározza, hogy a rendszer továbbítja a egy biztonsági kiszolgálót az eredeti kiszolgálóra CDN-specifikus kérelemfejléc készletét.


###<a name="maximum-keep-alive-requests"></a>Életben tartási kérelmek maximális száma
**Cél:** életben tartási kapcsolat kérelmek maximális számát határozza meg, mielőtt le van zárva.

A kérelmek maximális számát beállítása alacsony értékre nem ajánlott, és teljesítménycsökkenést eredményezhet.

Kapcsolatos információkat:

- Adja meg ennek az értéknek egész számként.
- Nem tartalmaznak vesszőt és pontot a megadott értékkel.

**Alapértelmezett érték:** 10 000 kérelem

###<a name="proxy-special-headers"></a>Proxy különleges fejlécek
**Cél:** meghatározza a készletét [CDN-specifikus kérelemfejléc]() , hogy a rendszer továbbítja a egy biztonsági kiszolgálót az eredeti kiszolgálóra.

Kapcsolatos információkat:

- Minden egyes CDN-specifikus fejléc definiált ezt a szolgáltatást a rendszer az eredeti kiszolgálóra továbbítja.
- CDN-specifikus fejléc megakadályozása eltávolítja a ezen a listán az eredeti kiszolgálóra lesznek továbbítva.

**Alapértelmezés:** összes [CDN-specifikus kérelemfejléc]() az eredeti kiszolgálóra továbbítja.

## <a name="specialty"></a>Speciális

Ezeket a funkciókat biztosítanak speciális funkcióit, amelyek csak haladó felhasználóknak használják.

Név | Cél
-----|--------
Kérelmeznék HTTP-metódus | Határozza meg, hogy a hálózat gyorsítótárazható további HTTP-metódus.
Kérelmeznék kérelem törzse mérete | Határozza meg a küszöbérték meghatározásához, hogy a FELADÁS egy vagy több válasz gyorsítótárazható.

###<a name="cacheable-http-methods"></a>Kérelmeznék HTTP-metódus
**Cél:** határozza meg, hogy a hálózat gyorsítótárazható további HTTP-metódus.

Kapcsolatos információkat:

- Ez a szolgáltatás azt feltételezi, hogy GET válaszok mindig gyorsítótárazza. Ennek eredményeképpen az első HTTP-metódus nem lehet megtalálható ez a funkció beállítása során.
- Ez a funkció csak a POST HTTP-metódus támogatja. POST válasz úgy, hogy ez a funkció gyorsítótárazás engedélyezése: POST 
- Alapértelmezés szerint csak kérelmeket amelynek törzs 14 Kb-nál kisebb a gyorsítótárba fognak kerülni. A szolgáltatással kérelmeznék kérelem törzse mérete törzs kérések maximális méretének beállítása.

**Alapértelmezés:** csak GET válaszok a gyorsítótárba fognak kerülni.

###<a name="cacheable-request-body-size"></a>Kérelmeznék kérelem törzse mérete

**Cél:** határozza meg a küszöbérték meghatározásához, hogy a FELADÁS egy vagy több válasz gyorsítótárazható.

Ez a küszöbérték megadása a kérelem maximális mérete határozza meg. Egy nagyobb kérelemtörzset tartalmazó kérelmek nem gyorsítótárazza.

Kapcsolatos információkat:

- A szolgáltatás csak akkor alkalmazható, ha a POST válaszok jogosultak a gyorsítótárazás. A szolgáltatással kérelmeznék HTTP módszerek POST kérelem gyorsítótárazás engedélyezéséhez.
- A kérelem törzsében van figyelembe venni:
    - x-www-form-urlencoded értékek
    - Egyedi gyorsítótár-kulcs biztosítása
- A nagy méretű kérelem maximális mérete meghatározása hatással lehetnek az adatok kézbesítési teljesítményére.
    - **Javasolt érték:** 14 Kb
    - **Minimális érték:** 1 Kb

**Alapértelmezés:** 14 Kb
 
## <a name="url"></a>URL-CÍME

Ezek a szolgáltatások lehetővé kérést átirányítja vagy egy másik URL-re írni.

Név | Cél
-----|--------
Átirányítások követése | Azt határozza meg, hogy az a hely egy ügyfél eredeti kiszolgáló által visszaadott fejlécében megadott állomásnév kérelmek átirányíthatók.
Az átirányítási URL-címe | A hely fejléce kérelmek irányítja át.
URL-cím átdolgozás  | Felülírja a kérelem URL-CÍMÉT.

###<a name="follow-redirects"></a>Átirányítások követése
**Cél:** határozza meg, hogy az a hely egy ügyfél eredeti kiszolgáló által visszaadott fejlécében megadott állomásnév kérelmek átirányíthatók.

Kapcsolatos információkat:

- Kérelmek csak átirányíthatók szegélyhez ugyanahhoz a platformhoz megfelelő CNAME.

Érték|eredménye
-|-
Engedélyezve|Kérelmek átirányíthatók.
Letiltva|Kérelmek nem irányítja át.

**Alapértelmezés:** letiltva.
###<a name="url-redirect"></a>Az átirányítási URL-címe
**Cél:** átirányítja a felhasználókat a hely fejléce kérelmeket.

Ez a szolgáltatás konfigurációját igényli, a következő beállítások megadása:

Beállítás|Leírás
-|-
Kód|Válassza ki a kérelmezőnek visszaadott válaszának kódja.
Forrás & minta| Ezek a beállítások megadása a kérelem URI-minta, amely azonosítja a típusát, hogy előfordulhat, hogy átirányítja kérelem. A rendszer átirányítja az URL-cím megfelel a következő feltételek közül mind egyetlen kérelmeket: <br/> <br/> **Forrás (vagy a tartalom-hozzáférési pont):** válasszon, amely azonosítja az eredeti kiszolgálóra relatív elérési utat. Ez az a "/XXXX/" szakaszban és a végpont neve. <br/> **Forrás (minta):** egy mintát, amely azonosítja a kérelmek relatív elérési utat kell megadni. A reguláris kifejezési minta definiálnia kell egy elérési utat, amely elindítja közvetlenül után a korábban kiválasztott tartalom-hozzáférési pont (lásd fent). <br/> -Győződjön meg arról, hogy a kérelem URI (Ez azt jelenti, hogy a forrás & mintát) korábban megadott feltételek nem ütközik a szolgáltatáshoz tartozó bármely egyezés egyiknek. <br/> -Adjon meg egy minta; a mintát üres értéket használja, ha az összes karakterlánc teljesül.
Cél| Határozza meg azt az URL-címet, amelyre a fenti kérelmek irányítja. <br/> Az URL-cím használatával dinamikusan össze: <br/> -A reguláris kifejezési minta <br/>-HTTP változók <br/> Helyettesítse be a cél minta $ használatával a forrás mintában rögzített értékek _n_  ahol  _n_  azonosítja a sorrendet, amelyben rögzítésének értéket. $1 például közben $2 második értékét jelöli az adatforrás a mintában rögzített első értékét jelöli. <br/> 
Ajánlott egy abszolút URL-CÍMÉT használja. Egy relatív URL-cím használatát előfordulhat, hogy CDN URL-címének átirányítása elérési út érvénytelen.

**Mintaforgatókönyv**

Ebben a példában a CNAME URL-címet, amely a CDN alap URL-cím él átirányítása bemutatjuk: http://marketing.azureedge.net/brochures

Kérelmek jogosult irányítja át a alap peremhálózati CNAME URL-cím: http://cdn.mydomain.com/resources

Az URL-cím átirányítást a következő konfigurációs keresztül valósítható meg:![](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Kulcs mutat:**

- Az átirányítási URL-cím szolgáltatást határozza meg a kérelem URL-címek, amelyek irányítja. Ennek eredményeképpen további egyezés feltételek esetén nincs szükség. Bár a egyeznek az állapot "Always" van definiálva, csak az ügyfél "marketing" eredeti "brosúrák" mappájába pont kérelmek irányítja. 
- Minden egyező kérések a szélén CNAME URL-címet a cél-beállítás irányítja. 
    - A minta #1. forgatókönyv: 
        - Mintakérelem (CDN URL): http://marketing.azureedge.net/brochures/widgets.pdf 
        - Kérelem URL-CÍMÉT (után átirányítási): http://cdn.mydomain.com/resources/widgets.pdf  
    - A minta #2. forgatókönyv: 
        - Mintakérelem (peremhálózati CNAME URL): http://marketing.mydomain.com/brochures/widgets.pdf 
        - Kérelem URL-CÍMÉT (után átirányítási): http://cdn.mydomain.com/resources/widgets.pdf mintaforgatókönyv
    - A minta #3. forgatókönyv: 
        - Mintakérelem (peremhálózati CNAME URL): http://brochures.mydomain.com/campaignA/final/productC.ppt 
        - Kérelem URL-CÍMÉT (után átirányítási): http://cdn.mydomain.com/resources/campaignA/final/productC.ppt  
- A kérelem rendszer (% {séma}) változó lett kihasználhatók a cél beállítás. Ez biztosítja, hogy a kérelem sémát az átirányítást követően változatlan marad.
- A rögzítette a kérelem URL-szegmensek lesz hozzáfűzve az új URL-cím segítségével "$1."
 
###<a name="url-rewrite"></a>URL-cím átdolgozás
**Cél:** újraírja a kérelem URL-CÍMÉT.

Kapcsolatos információkat:

- Ez a szolgáltatás konfigurációját igényli, a következő beállítások megadása:

Beállítás|Leírás
-|-
 Forrás & minta | Ezek a beállítások határozzák meg a kérelem URI-minta, amely azonosítja a típusát, hogy előfordulhat, hogy be kell írni kérelem. URL-cím megfelel a következő feltételek közül mind egyetlen kérelmek felülíródik: <br/>     - **Forrás (vagy a tartalom-hozzáférési pont):** válasszon, amely azonosítja az eredeti kiszolgálóra relatív elérési utat. Ez az a "/XXXX/" szakaszban és a végpont neve. <br/> - **Forrás (minta):** egy mintát, amely azonosítja a kérelmek relatív elérési utat kell megadni. A reguláris kifejezési minta definiálnia kell egy elérési utat, amely elindítja közvetlenül után a korábban kiválasztott tartalom-hozzáférési pont (lásd fent). <br/> Győződjön meg arról, hogy a kérelem URI (Ez azt jelenti, hogy a forrás & mintát) korábban megadott feltételek nem ütközik a egyezés feltételek, a szolgáltatáshoz tartozó definiálva. Adjon meg egy minta; a mintát üres értéket használja, ha az összes karakterlánc teljesül. 
 Cél  |Adja meg, amelyhez a fenti kérelmek felülíródik által relatív URL-címe: <br/>    1. A tartalom-hozzáférési pont, amely azonosítja az eredeti kiszolgálóra kiválasztása. <br/>    2. Annak meghatározása, egy relatív elérési út használatával: <br/>        -A reguláris kifejezési minta <br/>        -HTTP változók <br/> <br/> Helyettesítse be a cél minta $ használatával a forrás mintában rögzített értékek _n_  ahol  _n_  azonosítja a sorrendet, amelyben rögzítésének értéket. $1 például közben $2 második értékét jelöli az adatforrás a mintában rögzített első értékét jelöli. 
 Ez a funkció lehetővé teszi, hogy az URL-cím újraírása egy hagyományos átirányítás végrehajtása nélkül a peremhálózati kiszolgálókról. Ez azt jelenti, hogy a kérelmező megkapja a azonos válaszkód a, mintha csak egy átírt URL-CÍMÉT kellett lett kérve.

**Példa: 1. forgatókönyv**

Ebben a példában a CNAME URL-címet, amely a CDN alap URL-cím él átirányítása bemutatjuk: http://marketing.azureedge.net/brochures/

Kérelmek jogosult irányítja át a alap peremhálózati CNAME URL-cím: http://MyOrigin.azureedge.net/resources/

Az URL-cím átirányítást a következő konfigurációs keresztül valósítható meg:![](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Mintaforgatókönyv 2**

Ebben a példában a nagybetűs kisbetűssé reguláris kifejezésekkel URL-cím-CNAME él átirányítása bemutatjuk.

Az URL-cím átirányítást a következő konfigurációs keresztül valósítható meg:![](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)


**Kulcs mutat:**

- Az URL-újraíró szolgáltatást határozza meg a kérelem URL-címek, amelyek felülíródik. Ennek eredményeképpen további egyezés feltételek esetén nincs szükség. Bár a egyeznek az állapot "Always" van definiálva, csak azok a kérelmek, amelyek a felhasználói "marketing" eredeti "brosúrák" mappa felülíródik.

- A rögzítette a kérelem URL-szegmensek lesz hozzáfűzve az új URL-cím segítségével "$1."



###<a name="compatibility"></a>Kompatibilitás

Ez a funkció tartalmazza a megfelelő feltételek, amelyeknek teljesülniük kell ahhoz olyan kérésekre alkalmazható. Ütköző feltételek beállítása elkerülése érdekében ez a funkció nem kompatibilis a következő feltételek egyeznek:

- SZÁMOT
- CDN-forrása
- Ügyfél IP-címe
- Ügyfél forrása
- Kérelem séma
- URL-cím elérési út könyvtár
- URL-cím elérési út bővítmény
- URL-cím elérési út fájlnév
- URL-cím elérési út szövegkonstans
- URL-cím elérési út Regex
- URL-cím elérési út helyettesítő karakter
- Lekérdezés-szövegkonstans URL-címe
- URL-cím lekérdezési paraméter
- Lekérdezés Regex URL-címe
- Lekérdezés helyettesítő URL-címe


## <a name="next-steps"></a>Következő lépések
* [Szabályok motor referencia](cdn-rules-engine-reference.md)
* [Szabályok motor feltételes kifejezések](cdn-rules-engine-reference-conditional-expressions.md)
* [Szabályok motor egyezés feltételek](cdn-rules-engine-reference-match-conditions.md)
* [A szabályok használata alapértelmezett HTTP működés felülbírálata](cdn-rules-engine.md)
* [Az Azure CDN áttekintése](cdn-overview.md)
