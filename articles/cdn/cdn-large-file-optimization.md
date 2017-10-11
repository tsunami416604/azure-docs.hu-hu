---
title: "Az Azure Content Delivery Network keresztül nagy méretű fájlok letöltési optimalizálása"
description: "Nagy fájlok letöltése, tekintse meg a mélység optimalizálása"
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: v-semcev
ms.openlocfilehash: 7a5d5d1d0de24ebb0a5115ede1e572f38454bd78
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="large-file-download-optimization-via-the-azure-content-delivery-network"></a>Az Azure Content Delivery Network keresztül nagy méretű fájlok letöltési optimalizálása

Az interneten keresztül kézbesíteni tartalom méretének egyre nő, fejlettebb funkciókat, továbbfejlesztett grafikus és multimédiás tartalom miatt. A növekedési célja a számos tényező közé tartoznak: szélessávú behatolást vagy a biztonság, a nagyobb alacsony költségű tárolóeszközök, a széles körű nő az nagy felbontású videók és internetkapcsolattal rendelkező eszközök (IoT). A nagy fájlok gyors és hatékony mechanizmus alapvető fontosságú élvezetesebbé és zökkenőmentes felhasználói élményt nyújtsanak.

Nagy fájlok kézbesítését számos kihívást rendelkezik. Először töltse le a nagy fájlok átlagos idő jelentős lehet mivel az alkalmazások előfordulhat, hogy nem töltik le az összes adat egymás után. Alkalmazások bizonyos esetekben előfordulhat, hogy töltse le a az első rész előtt a fájl utolsó része. Amikor egy fájl csak egy kis mennyiségű van szükség, vagy a felhasználó megszakítja a letöltési, a letöltés sikertelen lehet. A letöltés is előfordulhat, hogy később, amíg követően az content delivery network (CDN) a teljes fájl lekéri a forráskiszolgálóról. 

Második a felhasználó és a fájl közötti késés meghatározza, hogy a sebesség, amellyel akkor megtekintheti a tartalmat. Hálózati torlódás és a kapacitás problémák emellett is átviteli hatással vannak. Kiszolgálók és a felhasználók nagyobb távolságát létrehozása csomagvesztés megtörténik, ami csökkenti a minőségi további lehetőségeit. A minőségi csökkenése korlátozott átviteli okozta, és nagyobb csomagvesztés növelheti a várakozási idő befejezéséhez fájl letöltéséhez. 

Harmadik sok nagy fájlt nem érkeznek meg teljes egészében. Felhasználók a előfordulhat, hogy egy másik keresztül letölthető megszakítja, vagy tekintse meg a csak egy hosszú MP4 videó első néhány percig. Ezért szoftverét és adathordozóit kézbesítési vállalatok kézbesíteni akarja csak a kért fájl része. Hatékony megoszlását a kért csökkenti a kimenő forgalmat a forráskiszolgálóról. Hatékony terjesztési is csökkenti, a memória és a forrás kiszolgálón i/o-terhelés. 

Az Azure Content Delivery Network Akamai egy szolgáltatás, amely továbbítja a nagy fájlok hatékonyan felhasználók léptékű világszerte kínál. A szolgáltatással csökken késések fordulnak elő, mivel csökkenti a terhelést a forrás-kiszolgálókon. A szolgáltatás az IP-címek Akamai Standard érhető el.

## <a name="configure-a-cdn-endpoint-to-optimize-delivery-of-large-files"></a>A CDN-végpontok optimalizálja a nagy fájlok kézbesítését konfigurálása

CDN-végpont optimalizálja a nagy fájlok az Azure-portálon kézbesítési konfigurálhatja. Ehhez a REST API-k vagy az ügyfél SDK-k bármelyikét is használja. Az alábbi műveletek ismertetik a folyamatot az Azure-portálon.

1. Egy új végpont hozzáadásához a **CDN-profil** lapon jelölje be **végpont**.

    ![Új végpont](./media/cdn-large-file-optimization/01_Adding.png)  
 
2. Az a **optimalizálva** legördülő listában válassza **nagy méretű fájlok letöltési**.

    ![Kijelölt nagy méretű fájlok optimalizálása](./media/cdn-large-file-optimization/02_Creating.png)


A CDN-végpont létrehozása után a nagy méretű fájlok optimalizálás feltételeknek megfelelő összes fájl vonatkozik. Az alábbi szakasz ismerteti a folyamatot.

## <a name="optimize-for-delivery-of-large-files-with-the-azure-content-delivery-network-from-akamai"></a>Kézbesítési nagy fájlok, az Azure Content Delivery Network Akamai optimalizálás

A nagy méretű fájlok optimalizálási típusa szolgáltatás hálózatoptimalizálás és konfigurációkat nagyméretű fájlokat képes biztosítani a gyorsabb és több responsively bekapcsolása. Általános webes kézbesítési Akamai a gyorsítótárba helyezi azt a fájlok csak 1,8 GB alá, és a alagút (nem a gyorsítótárban) fájlokat is legfeljebb 150 GB. Nagy méretű fájlok optimalizálási gyorsítótárak fájlok legfeljebb 150 GB.

Nagy méretű fájlok optimalizálási bizonyos feltételek teljesülése esetén hatékony. Feltételei közé tartozik az eredeti kiszolgálóra működését és a mérete, és a fájltípusok közül a kért. Mielőtt e témákban részleteinek kapjuk, tisztában kell az optimalizálás működése. 

### <a name="object-chunking"></a>Az objektum adattömbösítő 

Az Azure Content Delivery Network Akamai objektum adattömbösítő elnevezésű technikát használja. Ha van szükség a nagy fájlok, a CDN lekérdezi kisebb kódrészletek a fájlt a forrásból. A CDN-kiszolgáló peremhálózati/POP teljes vagy bájttartomány fájl kérelmet kap, miután ellenőrzi, hogy a fájl típusa az optimalizálás esetén támogatott. Azt is ellenőrzi, hogy a fájl típusa megfelel-e a fájl mérete követelményeinek. Ha a fájl mérete 10 MB-nál nagyobb, a CDN peremhálózati kiszolgáló lekéri a fájlt a forrásból, a 2 MB méretű adattömböket. 

Miután a rendszer a CDN peremhálózati megérkezik, gyorsítótárazott, és azonnal a felhasználó számára szolgáltatott. A CDN majd prefetches a következő adatrészlet párhuzamosan. A lehívott biztosítja, hogy a tartalom marad egy adatrészlet előre a felhasználó, amely csökkenti a késést. Ez a folyamat folytatódik, amíg a teljes fájl letöltése (ha szükséges), minden bájttartományok érhetők el (ha szükséges), vagy az ügyfél megszakítja a kapcsolatot. 

További információ a bájttartomány kérés: [RFC 7233](https://tools.ietf.org/html/rfc7233).

A gyorsítótárazza a CDN bármely adattömböket, érkezett. A teljes fájl gyorsítótárazni a CDN-gyorsítótár nem rendelkezik. A fájl vagy bájt tartományok későbbi kérelmek szolgáltatott a CDN-gyorsítótárból. Ha nem az adattömböket gyorsítótárazza a CDN, előzetes betöltési a forrásból adattömbök kérésére szolgál. Az optimalizálás bájttartomány kérelmek támogatásához az eredeti kiszolgálóra képességét támaszkodik. _Ha a forráskiszolgáló bájttartomány kérelmek nem támogatja, az optimalizálás nem hatékony._ 

### <a name="caching"></a>Gyorsítótárazás
Nagy méretű fájlok optimalizálási más alapértelmezett gyorsítótár lejárati idővel az általános webes kézbesítési használja. Pozitív és negatív gyorsítótárazást a HTTP válaszkódot alapján közötti különbséget tesz. Ha a forráskiszolgáló keresztül a cache-control lejárati időt határozza meg, vagy egy fejléc a következő a válasz lejár, a a CDN eleget tegyen ezt az értéket. Ha a forrás nem ad meg, és a fájl megfelel a optimalizálási típus típusú és bármekkora méretű feltételeinek, a CDN nagy méretű fájlok optimalizálási az alapértelmezett értékeket használ. A CDN egyébként általános webes kézbesítésre használja az alapértelmezett értékeket.


|    | Általános webes | Nagy méretű fájlok optimalizálása 
--- | --- | --- 
Gyorsítótárazás: pozitív <br> 200-AS, 203, 300, HTTP <br> 301, 302, és 410 | 7 nap |1 nap  
Gyorsítótárazás: negatív. <br> HTTP 204, 305, 404, <br> és 405 | None | 1 másodperc 

### <a name="deal-with-origin-failure"></a>Az eredeti hiba kezelésére

A forrás olvasás-időtúllépés növekszik általános webes kézbesítésre nagy optimalizálási fájltípushoz tartozó két percre két másodperc. Ezzel a növekedéssel számlák korai időtúllépés kapcsolatot elkerülése érdekében a nagyobb méretűek.

Ha a kapcsolat időtúllépés miatt megszakadt, a CDN újrapróbálja többször az ügyfél egy "504 - átjáró időtúllépése" hibaüzenet küldése előtt. 

### <a name="conditions-for-large-file-optimization"></a>Nagy méretű fájlok optimalizálási feltételei

Az alábbi táblázat a nagy méretű fájlok optimalizálási teljesítését feltételek csoportja:

Az állapot | Értékek 
--- | --- 
Támogatott fájltípusok | 3g, 2, 3gp, az ASP, avi, bz2, dmg, exe, f4v, flv, <br> GZ, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> MPEG, mpg, mts, pkg, qt, erőforrás-kezelő, swf, bont, <br> TGZ, wdp, webm, webp, wma, wmv, zip  
Minimális mérete | 10 MB 
Maximális fájlméret | 150 GB 
Forrás server jellemzői | Támogatnia kell a bájttartomány kérelmek 

## <a name="optimize-for-delivery-of-large-files-with-the-azure-content-delivery-network-from-verizon"></a>Kézbesítési nagy fájlok, az Azure Content Delivery Network verizon optimalizálás

Az Azure Content Delivery Network verizon nagy fájlok nélkül a maximális fájlméret nyújt. További szolgáltatások nagy fájlok kézbesítését gyorsabb végrehajtásához alapértelmezés szerint be vannak kapcsolva.

### <a name="complete-cache-fill"></a>Teljes gyorsítótár kitöltés

Az alapértelmezett teljes gyorsítótár kitöltés szolgáltatás lehetővé teszi, hogy a CDN és lekéréses a gyorsítótárba egy fájlt, egy kezdeti kérelem elhagyott vagy elveszett. 

Teljes gyorsítótár kitöltés esetén a leghasznosabb nagy eszközök. Általában felhasználók ne töltsön le őket kezdetétől a végéig. Progresszív letöltés használnak. Az alapértelmezett viselkedés kényszeríti a peremhálózati kiszolgáló a forráskiszolgálóról az eszköz a háttérben történő elindítására. Ezt követően az eszköz a peremhálózati kiszolgáló helyi gyorsítótárában van. Miután a teljes objektum a gyorsítótárban, a peremhálózati kiszolgáló teljesíti a CDN a gyorsítótárazott objektum bájttartomány kéréseket.

Az alapértelmezett viselkedés a Verizon Premium szinten lévő szabályok motoron keresztül letiltható.

### <a name="peer-cache-fill-hot-filing"></a>Társ-gyorsítótárazás töltse ki a közbeni bejelentés

Az alapértelmezett társközi gyorsítótár kitöltés közbeni bejelentés szolgáltatás kifinomult jogvédett algoritmust használ. További peremhálózati gyorsítótár-alapú sávszélesség kiszolgálók használ, és összesítés kéri a mérni kívánt nagy, nagy népszerű objektumok az ügyfelek kérelmeinek teljesítéséhez. Ez a szolgáltatás megakadályozza, hogy olyan helyzet, amelyben számos további kérelmet küldött a felhasználó eredeti kiszolgálóra. 

### <a name="conditions-for-large-file-optimization"></a>Nagy méretű fájlok optimalizálási feltételei

Az optimalizálási funkciók a Verizon alapértelmezés szerint vannak kapcsolva. Nincsenek nem határoz meg a maximális fájlméretet. 

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

Vegye figyelembe a következő további szempontok optimalizálási típus.
 
### <a name="azure-content-delivery-network-from-akamai"></a>Akamai Azure Tartalomkézbesítési hálózat

- A csonkolási folyamat hoz létre az eredeti kiszolgálóra további kérelmeket. Azonban a forrásból kézbesíteni adatok teljes mennyisége nem sokkal kisebb. Csonkolási eredményez jobb a CDN a gyorsítótárazási jellemzőit.

- Memória és I/O nyomás csökkennek forráson, mert a fájl kisebb kódrészletek érkeznek.

- A gyorsítótárazza a CDN: adattömböt nincsenek további kérelmek és a forrás addig, amíg a tartalom lejárati vagy a gyorsítótárból ki van zárva.

- Felhasználók tartomány kérelmek tehet a CDN-t, és van, mint bármely normál fájl kezeli azokat. Optimalizálás csak akkor, ha egy érvényes fájltípust és a 10 MB és 150 GB között van a bájttartomány vonatkozik. Ha a kért átlagos fájl mérete 10 MB-nál kisebb, érdemes általános webes kézbesítési használja helyette.

### <a name="azure-content-delivery-network-from-verizon"></a>Verizon Azure Tartalomkézbesítési hálózat

Általános webes optimalizálási típusú biztosíthat nagy méretű fájlt.
