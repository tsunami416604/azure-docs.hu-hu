---
title: Nagyméretű fájlok letöltési optimalizálása az Azure CDN-nel
description: Ez a cikk bemutatja, hogyan nagy fájlok letöltéseket is lehet optimalizálni.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: magattus
ms.openlocfilehash: 9793348b47763e6de10992b9a8a4606fc532cc4d
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094020"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Nagyméretű fájlok letöltési optimalizálása az Azure CDN-nel

Fájl méretét az interneten keresztül kézbesített tartalom miatt a továbbfejlesztett funkciókért, továbbfejlesztett grafikus és multimédiás tartalom növekedjen tovább. Ennek a növekedésnek határozzák meg számos tényező: szélessávú behatolási, nagyobb költségkímélő tárolóeszközök, nagy felbontású videó, és az internethez csatlakozó eszközökről (IoT) széles körű növekedését. A nagy méretű fájlok gyors és hatékony mechanizmus fontos élvezetesebbé és a zökkenőmentes felhasználói élményt nyújt.

Nagy fájlok kézbesítése több esetleges problémát is rendelkezik. Először egy nagyméretű fájl letöltése átlagos ideje jelentős lehet, mert az alkalmazások előfordulhat, hogy töltse le az összes adat egymás után. Alkalmazások bizonyos esetekben előfordulhat, hogy töltse le a egy fájlt, mielőtt az első rész az utolsó része. Ha csak kisebb mennyiségű fájl van szükség, vagy a felhasználó felfüggeszti a letöltés, a letöltés sikertelen lehet. A letöltés is előfordulhat, hogy később, amíg követően az content delivery network (CDN) a forráskiszolgálóról a teljes fájlt kéri le. 

Második a felhasználó számítógépén, és a fájl a késés, ahol megtekinthetik a tartalmat a sebesség határozza meg. Ezenkívül a hálózati torlódás és a kapacitás problémák is érintik a átviteli sebesség. Kiszolgálók és a felhasználók nagyobb távolságát hozhat létre a csomagvesztés az történik, ami csökkenti a minőségi további lehetőségeket. Minőségi csökkenését okozza korlátozott átviteli sebesség és a megnövekedett csomagvesztés növelheti a várakozási idő a fájl letöltése befejeződik. 

Harmadik sok nagy fájlt nem lett kézbesítve teljes egészében. Előfordulhat, hogy a felhasználók egy felénél keresztül letöltés megszakítása, vagy csak az első néhány perc alatt egy hosszú MP4 videó megtekintése. Ezért szoftverek és a szállítási médiatársaságoknak szeretné, hogy csak a kért fájl része. Hatékony megoszlását a kért csökkenti a kimenő forgalomért a forráskiszolgálóról. Hatékony terjesztési is csökkenti, a memória és az i/o-nyomás a forráskiszolgálón. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>A Microsoft a nagy méretű fájlok az Azure CDN kézbesítési optimalizálás

**Az Azure CDN Standard a Microsoft** végpontok egy korlát nélküli fájl mérete nagy fájlok kézbesítése. További szolgáltatások gyorsabb, hatékonyabb nagy fájlok kézbesítése alapértelmezés szerint be vannak kapcsolva.

### <a name="object-chunking"></a>Darabolás objektum 

**Az Azure CDN Standard a Microsoft** nevű objektum darabolás módszerrel. Nagy fájlok van szükség, amikor a CDN kisebb darabokra a fájlt a forrásból kérdezi le. A CDN POP-kiszolgáló teljes vagy bájttartomány-fájl kérelmet kap, miután a CDN peremhálózati kiszolgáló lekéri a fájlt a forrásból a 8 MB méretű adattömböket. 

Az adattömbök megérkezik az CDN Edge-ben, miután a gyorsítótárba, és azonnal szolgálja ki a felhasználó számára. A CDN majd prefetches a következő adattömbök párhuzamosan. A lehívott biztosítja, hogy a tartalom marad egy adattömb előre a felhasználót, ami csökkenti a késést. Ez a folyamat folytatódik, amíg a teljes fájl letöltése (ha szükséges), az összes bájttartományok érhetők el (ha szükséges), vagy az ügyfél megszakítja a kapcsolatot. 

Bájttartomány-kéréssel kapcsolatos további információkért lásd: [RFC 7233](https://tools.ietf.org/html/rfc7233).

A CDN gyorsítótárazza a minden olyan adattömböket, fogadásuk. A teljes fájlt nem kell a CDN gyorsítótára a gyorsítótárban. A fájl vagy bájt címtartományok esetében további kérések szolgáltatás szolgálja ki a CDN-gyorsítótárból. Ha nem összes adatrészletének a CDN-en lettek gyorsítótárazva, előzetes betöltési adattömbök kérhet az eredeti szolgál. Az optimalizálás támaszkodik a forráskiszolgáló képességét bájttartomány-kérelmek; támogatásához Ha a forráskiszolgáló nem támogatja a bájttartomány-kérelmek, az optimalizálás nem elég hatékony. 

### <a name="conditions-for-large-file-optimization"></a>Nagyméretű fájlok optimalizálása feltételei
Nagyméretű fájlok optimalizálása funkcióit **Azure CDN Standard a Microsoft** vannak kapcsolva alapértelmezés szerint az általános webes kézbesítési optimalizálás típus használatakor. A fájl maximális mérete nincs korlátozva van.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Optimalizálja a szállítási a nagy méretű fájlok az Azure CDN a Verizontól

**Az Azure CDN Standard verizon** és **verizon Azure CDN Premium** végpontok egy korlát nélküli fájl mérete nagy fájlok kézbesítése. További szolgáltatások gyorsabb, hatékonyabb nagy fájlok kézbesítése alapértelmezés szerint be vannak kapcsolva.

### <a name="complete-cache-fill"></a>Teljes gyorsítótár kitöltése

A teljes gyorsítótárat kitöltési funkciója lehetővé teszi, hogy a CDN-t, kérje le a fájl a gyorsítótárba egy kezdeti kérelem félbehagyott vagy elveszett. 

Teljes gyorsítótár kitöltés esetén a leghasznosabb nagy eszközök. Általában felhasználók ne töltse le azokat elejétől a végéig. Progresszív letöltés használnak. Az alapértelmezett viselkedést kényszeríti a peremhálózati kiszolgáló kezdeményezéséhez egy háttérbeli adatbeolvasás az eszköz a forráskiszolgálóról. Ezt követően az objektumot a helyi gyorsítótárban a peremhálózati kiszolgáló van. Miután a teljes objektum a gyorsítótárban, a peremhálózati kiszolgáló teljesíti a CDN-nek a gyorsítótárazott objektumot bájttartomány-kérelmeket.

Alapértelmezés szerint a szabályok motoron keresztül letiltható **verizon Azure CDN Premium**.

### <a name="peer-cache-fill-hot-filing"></a>Társ-gyorsítótárazás töltse ki a gyakran használt adatok rétegére bejelentés

Az alapértelmezett társ gyorsítótár kitöltés ritkáról gyakori elérésű készítő szolgáltatás kifinomult szellemi tulajdont képező algoritmust használ. További edge sávszélesség alapján a kiszolgálók gyorsítótárazást használ, és összesített kérelmek metrikák nagy, Nagyon népszerű objektumok az ügyfelek kérelmeinek teljesítéséhez. Ez a funkció olyan helyzet, amelyben további kérések nagy számú felhasználó származási kiszolgálónak küldött megakadályozza. 

### <a name="conditions-for-large-file-optimization"></a>Nagyméretű fájlok optimalizálása feltételei

Nagyméretű fájlok optimalizálása funkcióit **Azure CDN Standard verizon** és **verizon Azure CDN Premium** vannak kapcsolva alapértelmezés szerint az általános webes kézbesítési optimalizálás típus használatakor. A fájl maximális mérete nincs korlátozva van. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Akamai Azure CDN Standard nagy fájlok továbbításának optimalizálása

**Az Azure CDN Akamai Standard** profil végpontok kínálnak a szolgáltatása, amely biztosít nagy méretű fájlok hatékonyan felhasználóknak szerte a világon, ipari méretekben. A funkció csökkenti a késéseket, mivel ez csökkenti a szerverek terhelését.

A nagyméretű fájlok optimalizálása típusa szolgáltatás bekapcsolása a hálózatoptimalizálás, valamint a konfigurációk, így nagy méretű fájlok, gyorsabban és esetében bekapcsolható a rugalmasság több. Az általános webes kézbesítés **Azure CDN Akamai Standard** végpontok gyorsítótárazza a fájlok csak 1,8 GB alatt, és a (Nincs gyorsítótárazás) alagút fájlokat is legfeljebb 150 GB-ig. Nagyméretű fájlok optimalizálása gyorsítótárak legfeljebb 150 GB-os fájlok.

Nagyméretű fájlok optimalizálása bizonyos feltételek teljesülése esetén érvényes. Ilyen feltétel lehet az hogyan működik, a forráskiszolgáló és a méret és a fájltípusok közül a kért. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>A nagy méretű fájlok kézbesítésének optimalizálása az Akamai CDN végpont konfigurálása

Konfigurálhatja a **Azure CDN Akamai Standard** végpont készregyártás az Azure Portalon nagyméretű fájlok optimalizálása érdekében. Ehhez a REST API-k vagy az ügyfél SDK-k valamelyikét is használhatja. A következő lépések bemutatják az Azure Portalon, az a folyamat egy **Azure CDN Akamai Standard** profil:

1. Egy új végponton, hozzáadásához az Akamai **CDN-profil** lapon jelölje be **végpont**.

    ![Új végpont](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. Az a **optimalizált** legördülő listában válassza **nagyméretű fájl letöltése**.

    ![Kiválasztott nagyméretű fájlok optimalizálása](./media/cdn-large-file-optimization/cdn-large-file-select.png)


Miután létrehozta a CDN-végpontot, a nagyméretű fájlok optimalizálása az összes fájl, amely megfelel bizonyos feltételeknek vonatkozik. Az alábbi szakasz ismerteti a folyamatot.

### <a name="object-chunking"></a>Darabolás objektum 

A nagyméretű fájlok optimalizálása **Azure CDN Akamai Standard** nevű objektum darabolás módszerrel. Nagy fájlok van szükség, amikor a CDN kisebb darabokra a fájlt a forrásból kérdezi le. A CDN POP-kiszolgáló teljes vagy bájttartomány-fájl kérelmet kap, miután ellenőrzi-e a fájl típusa az optimalizálás támogatott. Akkor is ellenőrzi, hogy a fájl típusa megfelel-e a fájl mérete követelményeknek. Ha a fájl mérete 10 MB-nál nagyobb, a CDN peremhálózati kiszolgáló lekéri a fájlt a forrásból a 2 MB méretű adattömböket. 

Az adattömbök megérkezik az CDN Edge-ben, miután a gyorsítótárba, és azonnal szolgálja ki a felhasználó számára. A CDN majd prefetches a következő adattömbök párhuzamosan. A lehívott biztosítja, hogy a tartalom marad egy adattömb előre a felhasználót, ami csökkenti a késést. Ez a folyamat folytatódik, amíg a teljes fájl letöltése (ha szükséges), az összes bájttartományok érhetők el (ha szükséges), vagy az ügyfél megszakítja a kapcsolatot. 

Bájttartomány-kéréssel kapcsolatos további információkért lásd: [RFC 7233](https://tools.ietf.org/html/rfc7233).

A CDN gyorsítótárazza a minden olyan adattömböket, fogadásuk. A teljes fájlt nem kell a CDN gyorsítótára a gyorsítótárban. A fájl vagy bájt címtartományok esetében további kérések szolgáltatás szolgálja ki a CDN-gyorsítótárból. Ha nem összes adatrészletének a CDN-en lettek gyorsítótárazva, előzetes betöltési adattömbök kérhet az eredeti szolgál. Az optimalizálás támaszkodik a forráskiszolgáló képességét bájttartomány-kérelmek; támogatásához Ha a forráskiszolgáló nem támogatja a bájttartomány-kérelmek, az optimalizálás nem elég hatékony.

### <a name="caching"></a>Gyorsítótárazás
Nagyméretű fájlok optimalizálása más alapértelmezett gyorsítótár-lejárati idővel az általános webes kézbesítés használ. Közötti pozitív gyorsítótárazását és gyorsítótárazása, HTTP-válaszkódot alapján különbözteti meg. Ha a forráskiszolgáló adja meg a lejárati időt egy cache-control-n keresztül, vagy a fejléccel a válaszban lejár, a CDN figyelembe veszi ezt az értéket. Ha a forrás nem ad meg, és a fájl megfelel a optimalizálási típus típusát és méretét feltételeinek, a CDN-t használja az alapértelmezett értékeket a nagyméretű fájlok optimalizálása. Ellenkező esetben a CDN-t általános webes kézbesítés használ alapértelmezett értékeket.


|    | Általános webes | Nagyméretű fájlok optimalizálása 
--- | --- | --- 
Gyorsítótárazás: pozitív <br> HTTP 200, 203, 300, <br> 301, 302 és 410 | 7 nap |1 nap  
Gyorsítótárazás: negatív <br> HTTP 204, 305, 404-ES, <br> és a 405-ös | None | 1 másodperc 

### <a name="deal-with-origin-failure"></a>Foglalkoznak, forrása

Általános webes kézbesítés két perc, a nagyméretű fájlok optimalizálása típushoz két másodperces forrás olvasási-időtúllépés hossza nő. Ezzel a növekedéssel számlák gazdaprogram időtúllépése kapcsolat elkerülése érdekében a nagyobb méretűek.

Ha a kapcsolat időkorlátja lejár, a CDN újrapróbálkozik többször a "504 – átjáró időtúllépése" hiba történt az ügyfél küldése előtt. 

### <a name="conditions-for-large-file-optimization"></a>Nagyméretű fájlok optimalizálása feltételei

A következő táblázatban megtalálható, a nagyméretű fájlok optimalizálása teljesítendő feltételek:

Állapot | Értékek 
--- | --- 
Támogatott fájltípusok | 3g, 2, 3gp, az ASP, avi, bz2, dmg, exe, f4v, flv, <br> GZ, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> MPEG, mpg, mts, pkg, qt, erőforrás-kezelő, swf, tar, <br> TGZ, wdp, webm, webp, wma, wmv, zip  
Fájlok minimális mérete | 10 MB 
Maximális fájlméret | 150 GB 
Forrás kiszolgáló jellemzői | Támogatnia kell a bájttartomány-kérelmek 

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

Vegye figyelembe a következő további szempontok optimalizálási típus:

- A csonkolási folyamat hoz létre az eredeti kiszolgálóra további kérések. Azonban a forrás által nyújtott adatok teljes mennyisége nem sokkal kisebb. Csonkolási eredményez, a CDN gyorsítótárazási jellemzők jobb.

- Memória- és i/o-nyomás csökken a forráson, mert a fájl kisebb darabokra lépnek érvénybe.

- Az adattömbök is a CDN gyorsítótárazza nincsenek további kérelmek jutó mindaddig, amíg a tartalom lejár, vagy a gyorsítótárból ki van zárva.

- Felhasználók is kérést tartományt a CDN, amelyeket kell kezelni, mint bármely normál fájlt. Optimalizálás vonatkozik, csak akkor, ha egy érvényes fájltípust, és a bájttartomány mérete 10 MB-ot és 150 GB-os között. Ha a kért fájl átlagos mérete 10 MB-nál kisebb, használja inkább az általános webes kézbesítés.

