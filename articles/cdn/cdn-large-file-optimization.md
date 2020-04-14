---
title: Nagy fájlletöltésoptimalizálás az Azure CDN-nel
description: Ez a cikk bemutatja, hogyan optimalizálható a nagy fájlletöltések.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: allensu
ms.openlocfilehash: 28b3c4faf62bcd9f9495810927ece03e2dadc1fc
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260530"
---
# <a name="large-file-download-optimization-with-azure-cdn"></a>Nagy fájlletöltésoptimalizálás az Azure CDN-nel

Az interneten keresztül szállított tartalom fájlmérete a továbbfejlesztett funkcióknak, a továbbfejlesztett grafikáknak és a multimédiás tartalmaknak köszönhetően folyamatosan nő. Ezt a növekedést számos tényező hajtja: szélessávú penetráció, nagyobb olcsó tárolóeszközök, a nagy felbontású videók széles körű növekedése és az internethez kapcsolódó eszközök (IoT). A nagy fájlok gyors és hatékony kézbesítési mechanizmusa kritikus fontosságú a zökkenőmentes és élvezetes fogyasztói élmény biztosításához.

A nagy fájlok kézbesítése számos kihívással is rendelkezik. Először is, egy nagy fájl letöltésének átlagos ideje jelentős lehet, mivel előfordulhat, hogy az alkalmazások nem töltik le egymás után az összes adatot. Bizonyos esetekben előfordulhat, hogy az alkalmazások letöltik a fájl utolsó részét az első rész előtt. Ha csak kis mennyiségű fájlt kér, vagy a felhasználó szünetelteti a letöltést, a letöltés sikertelen lehet. A letöltés is elhalasztható, amíg a tartalomszolgáltató hálózat (CDN) lenem kéri a teljes fájlt az eredeti kiszolgálóról. 

Másodszor, a felhasználó gépe és a fájl közötti késés határozza meg a tartalom megtekintésének sebességét. Emellett a hálózati torlódások és a kapacitásproblémák is befolyásolják az átviteli kapacitást. A kiszolgálók és a felhasználók közötti nagyobb távolságok további lehetőségeket teremtenek a csomagvesztésre, ami csökkenti a minőséget. A korlátozott átviteli forgalom és a megnövekedett csomagvesztés által okozott minőségcsökkenés növelheti a fájl letöltésének várakozási idejét. 

Harmadszor, sok nagy fájlok nem szállítják teljes egészében. A felhasználók a hosszú MP4-videó első néhány percét törölhetik, vagy csak az első néhány percet nézhetik meg. Ezért a szoftver- és médiakézbesítési vállalatok csak a kért fájlrészt szeretnék kézbesíteni. A kért részek hatékony elosztása csökkenti a forráskiszolgálóról érkező kimenő forgalmat. A hatékony elosztás csökkenti a memória és az I/O nyomást az eredeti kiszolgálón. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-microsoft"></a>Optimalizálja a nagy fájlok kézbesítését az Azure CDN-nel a Microsofttól

**A Microsoft-végpontok Azure CDN Standard** szolgáltatása nagy méretű fájlokat biztosít a fájlméret korlátozása nélkül. A további szolgáltatások alapértelmezés szerint be vannak kapcsolva, hogy a nagy fájlok kézbesítése gyorsabb legyen.

### <a name="object-chunking"></a>Objektum darabolása 

**A Microsoft Azure CDN Standard nevű** technikáját használja. Ha nagy méretű fájlt kér, a CDN a fájl kisebb darabjait olvassa le az eredetiforrásból. Miután a CDN POP-kiszolgáló teljes vagy bájttartományú fájlkérelmet kapott, a CDN peremhálózati kiszolgáló 8 MB-os adattömbökben kéri a fájlt az eredetiforrástól. 

Miután az adattömb megérkezik a CDN szélén, a gyorsítótárba, és azonnal kiszolgálta a felhasználónak. A CDN ezután a következő adattömböt párhuzamosan előkéri. Ez az előzetes betöltés biztosítja, hogy a tartalom egy adattömböt maradjon a felhasználó előtt, ami csökkenti a késést. Ez a folyamat addig folytatódik, amíg a teljes fájlt le nem tölti (ha szükséges), az összes bájttartomány elérhető (ha szükséges), vagy az ügyfél meg nem szakítja a kapcsolatot. 

A bájttartomány-kérelemmel kapcsolatos további információkért [lásd: RFC 7233](https://tools.ietf.org/html/rfc7233).

A CDN gyorsítótárazza az adattömböket, amint azok beérkeznek. A teljes fájlt nem kell gyorsítótárazni a CDN-gyorsítótárban. A fájl- vagy bájtostartományokra vonatkozó további kérelmeket a CDN-gyorsítótárból szolgálják ki. Ha nem az összes adattömb van gyorsítótárazva a CDN-en, az előzetes betöltés az eredeti adattömbök kérésére szolgál. Ez az optimalizálás az eredeti kiszolgáló azon képességén alapul, hogy támogatja a bájttartomány-kérelmeket; ha az eredeti kiszolgáló nem támogatja a bájttartomány-kérelmeket, ez az optimalizálás nem hatékony. 

### <a name="conditions-for-large-file-optimization"></a>A nagyfájl-optimalizálás feltételei
A **Microsoft Azure CDN Standard** nagyfájl-optimalizálási funkciói alapértelmezés szerint be vannak kapcsolva, ha az általános webes kézbesítésoptimalizálási típust használja. A maximális fájlméretnem korlátozva van.


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-from-verizon"></a>Optimalizálja a nagy fájlok kézbesítését az Azure CDN-nel a Verizontól

**A Verizon Azure CDN Standard és** **a Verizon-végpontok Azure CDN Premium szolgáltatása** nagy fájlokat biztosít a fájlméret korlátozása nélkül. A további szolgáltatások alapértelmezés szerint be vannak kapcsolva, hogy a nagy fájlok kézbesítése gyorsabb legyen.

### <a name="complete-cache-fill"></a>Teljes gyorsítótár-kitöltés

Az alapértelmezett teljes gyorsítótár-kitöltési szolgáltatás lehetővé teszi, hogy a CDN behúzzon egy fájlt a gyorsítótárba, amikor egy kezdeti kérés torkollata megszűnik vagy elveszik. 

A teljes gyorsítótár-kitöltés a nagy eszközök esetében a leghasznosabb. A felhasználók általában nem töltik le őket az elejétől a végéig. Progresszív letöltést használnak. Az alapértelmezett viselkedés arra kényszeríti a peremhálózati kiszolgálót, hogy kezdeményezze az eszköz háttérbeolvasását az eredeti kiszolgálóról. Ezt követően az eszköz a peremhálózati kiszolgáló helyi gyorsítótárában található. Miután a teljes objektum a gyorsítótárban van, a peremhálózati kiszolgáló teljesíti a gyorsítótárba helyezett objektum CDN-re vonatkozó bájttartomány-kéréseit.

Az alapértelmezett viselkedés letiltható a Szabályok motoron keresztül a **Verizon Azure CDN Premium szolgáltatásában.**

### <a name="peer-cache-fill-hot-filing"></a>A társgyorsítótár kitöltése a benyújtás forró betöltése

Az alapértelmezett társgyorsítótár-kitöltési adatbetöltési szolgáltatás kifinomult, saját algoritmust használ. További peremhálózati gyorsítótárazási kiszolgálókat használ a sávszélesség és az összesített kérelmek metrikái alapján a nagy, rendkívül népszerű objektumok ügyfélkérelmeinek teljesítéséhez. Ez a szolgáltatás megakadályozza, hogy nagy számú további kérés torkig kerüljen a felhasználó eredeti kiszolgálójára. 

### <a name="conditions-for-large-file-optimization"></a>A nagyfájl-optimalizálás feltételei

A **Verizon azure CDN Standard** szolgáltatásának nagyfájloptimalizálási funkciói alapértelmezés szerint be vannak kapcsolva az általános webes kézbesítésoptimalizálási optimalizálási típus használatakor. **Azure CDN Premium from Verizon** A maximális fájlméretnem korlátozva van. 


## <a name="optimize-for-delivery-of-large-files-with-azure-cdn-standard-from-akamai"></a>Optimalizálja a nagy fájlok kézbesítését az Akamai Azure CDN Standard szolgáltatásával

**Az Akamai profilvégpontok Azure CDN Standard** szolgáltatása olyan funkciót kínál, amely nagy fájlokat biztosít hatékonyan a felhasználók számára a világ minden táján, méretarányosan. A szolgáltatás csökkenti a késéseket, mert csökkenti az eredeti kiszolgálók terhelését.

A nagy fájloptimalizálási típus funkció bekapcsolja a hálózati optimalizálást és konfigurációkat, hogy a nagy fájlok gyorsabbak és gyorsabban reagáljanak. Az **Akamai** végpontok azure CDN Standard szolgáltatással történő általános webes kézbesítéscsak 1,8 GB alatti fájlokat gyorsítótáraz, és legfeljebb 150 GB-os gyorsítótárazási (nem gyorsítótár) fájlokat tud gyorsítótárazni. A nagy fájloptimalizálás akár 150 GB-ig is gyorsítótárazza a fájlokat.

A nagy fájloptimalizálás akkor hatékony, ha bizonyos feltételek teljesülnek. A feltételek között szerepel az eredeti kiszolgáló működése, valamint a kért fájlok mérete és típusa. 

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-delivery-of-large-files"></a>Konfiguráljon egy Akamai CDN-végpontot a nagy fájlok kézbesítésének optimalizálásához

Konfigurálhatja az **Azure CDN Standard akamai** végpontról a nagy fájlok kézbesítésének optimalizálásához az Azure Portalon keresztül. Ehhez használhatja a REST API-kat vagy az ügyfél SDK-kat is. A következő lépések az Azure Portalon keresztül mutatják be a folyamatot **egy Akamai-profilból származó Azure CDN-szabvány esetén:**

1. Új végpont hozzáadásához az Akamai **CDN-profillapon** válassza **a Végpont**lehetőséget.

    ![Új végpont](./media/cdn-large-file-optimization/cdn-new-akamai-endpoint.png)    
 
2. Az **Optimalizált legördülő** listában válassza a **Nagy fájl letöltése**lehetőséget.

    ![Nagyfájloptimalizálás kiválasztva](./media/cdn-large-file-optimization/cdn-large-file-select.png)


A CDN-végpont létrehozása után a nagy fájloptimalizálást alkalmazza az összes olyan fájlra, amely megfelel bizonyos feltételeknek. A következő szakasz ezt a folyamatot ismerteti.

### <a name="object-chunking"></a>Objektum darabolása 

Nagy fájloptimalizálás **az Azure CDN Standard aakamai** egy úgynevezett objektum darabolási technikát használ. Ha nagy méretű fájlt kér, a CDN a fájl kisebb darabjait olvassa le az eredetiforrásból. Miután a CDN POP-kiszolgáló teljes vagy bájttartományú fájlkérelmet kapott, ellenőrzi, hogy a fájltípus támogatott-e az optimalizáláshoz. Azt is ellenőrzi, hogy a fájltípus megfelel-e a fájlméret-követelményeknek. Ha a fájlmérete meghaladja a 10 MB-ot, a CDN peremhálózati kiszolgáló 2 MB-os adattömbökben kéri a fájlt az eredeti forrástól. 

Miután az adattömb megérkezik a CDN szélén, a gyorsítótárba, és azonnal kiszolgálta a felhasználónak. A CDN ezután a következő adattömböt párhuzamosan előkéri. Ez az előzetes betöltés biztosítja, hogy a tartalom egy adattömböt maradjon a felhasználó előtt, ami csökkenti a késést. Ez a folyamat addig folytatódik, amíg a teljes fájlt le nem tölti (ha szükséges), az összes bájttartomány elérhető (ha szükséges), vagy az ügyfél meg nem szakítja a kapcsolatot. 

A bájttartomány-kérelemmel kapcsolatos további információkért [lásd: RFC 7233](https://tools.ietf.org/html/rfc7233).

A CDN gyorsítótárazza az adattömböket, amint azok beérkeznek. A teljes fájlt nem kell gyorsítótárazni a CDN-gyorsítótárban. A fájl- vagy bájtostartományokra vonatkozó további kérelmeket a CDN-gyorsítótárból szolgálják ki. Ha nem az összes adattömb van gyorsítótárazva a CDN-en, az előzetes betöltés az eredeti adattömbök kérésére szolgál. Ez az optimalizálás az eredeti kiszolgáló azon képességén alapul, hogy támogatja a bájttartomány-kérelmeket; ha az eredeti kiszolgáló nem támogatja a bájttartomány-kérelmeket, ez az optimalizálás nem hatékony.

### <a name="caching"></a>Gyorsítótárazás
A nagyfájl-optimalizálás az általános webes kézbesítéstől eltérő alapértelmezett gyorsítótárazási-lejárati időket használ. Különbséget tesz a pozitív gyorsítótárazás és a negatív gyorsítótárazás között a HTTP-válaszkódok alapján. Ha az eredeti kiszolgáló gyorsítótár-vezérlőn keresztül alejárati időt adja meg, vagy a válaszfejlécben lejár, a CDN tiszteletben tartja ezt az értéket. Ha az origó nem adja meg, és a fájl megegyezik az optimalizálási típus típusának és méretfeltételeinek, a CDN az alapértelmezett értékeket használja a nagy fájloptimalizáláshoz. Ellenkező esetben a CDN az alapértelmezett beállításokat használja az általános webes kézbesítéshez.


|    | Általános web | Nagyméretű fájlok optimalizálása 
--- | --- | --- 
Gyorsítótárazás: Pozitív <br> HTTP 200, 203, 300, <br> 301, 302 és 410 | 7 nap |1 nap  
Gyorsítótárazás: Negatív <br> HTTP 204, 305, 404, <br> és 405 | None | 1 másodperc 

### <a name="deal-with-origin-failure"></a>A származás hibájának kezelése

Az eredeti leolvasási időtúltávolság az általános webes kézbesítés esetén két másodpercről két percre nő a nagy fájloptimalizálási típus esetében. Ez a növekedés a nagyobb fájlméreteket teszi ki, hogy elkerülje a korai időtúllépési kapcsolatot.

Amikor egy kapcsolat túllépi az időtúltöltést, a CDN többször újrapróbálkozik, mielőtt "504 – Átjáró időtúllépés" hibaüzenetet küldne az ügyfélnek. 

### <a name="conditions-for-large-file-optimization"></a>A nagyfájl-optimalizálás feltételei

Az alábbi táblázat felsorolja a nagyfájlok optimalizálásához teljesítendő feltételeket:

Állapot | Értékek 
--- | --- 
Támogatott fájltípusok | 3g2, 3gp, asf, avi, bz2, dmg, exe, f4v, flv, <br> gz, hdp, iso, jxr, m4v, mkv, mov, mp4, <br> mpeg, mpg, mts, pkg, qt, rm, swf, kátrány, <br> tgz, WDP, webm, webp, wma, wmv, zip  
Minimális fájlméret | 10 MB 
Maximális fájlméret | 150 GB 
Az eredeti kiszolgáló jellemzői | Támogatnia kell a bájttartomány-kérelmeket 

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

Vegye figyelembe az optimalizálási típus következő további szempontjait:

- Az adattömbök további kéréseket hoznak létre az eredeti kiszolgálószámára. Az eredetből származó adatok teljes mennyisége azonban sokkal kisebb. A darabolás jobb gyorsítótárazási jellemzőket eredményez a CDN-nél.

- A memória és az I/O nyomás csökken az eredeti, mert a fájl kisebb darabjai szállítják.

- A CDN gyorsítótárazott adattömbök esetében nincsenek további kérelmek az eredetre, amíg a tartalom le nem jár, vagy ki nem van zárva a gyorsítótárból.

- A felhasználók tartománykérelmeket intézhetnek a CDN-hez, amelyeket a rendszer úgy kezel, mint bármely más normál fájlt. Az optimalizálás csak akkor érvényes, ha érvényes fájltípus, és a bájttartomány 10 MB és 150 GB között van. Ha a kért átlagos fájlméret kisebb, mint 10 MB, használja inkább az általános webes kézbesítést.

