---
title: "404-es állapotot visszaadó Azure CDN-végpontok hibaelhárítása |} Microsoft Docs"
description: "404-es válaszkódot az Azure CDN-végpontok hibaelhárítása."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f59fbd18413fb44026d8c92b7f6940ed2f8a00a8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-cdn-endpoints-returning-404-statuses"></a>404-es állapotot visszaadó CDN-végpontok hibaelhárítása
Ez a cikk segít elhárítása [CDN-végpontok](cdn-create-new-endpoint.md) 404-es hibát ad vissza.

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a [az MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl is az Azure támogatási incidens. Lépjen a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , majd kattintson a **támogatás**.

## <a name="symptom"></a>Jelenség
Létrehozta a CDN-profil és egy végpontot, de a tartalom úgy tűnik, hogy a CDN elérhetőnek lennie.  Próbálja meg elérni a tartalom a CDN URL-CÍMEN keresztül felhasználó megkapja a HTTP 404-es állapotkód esetében. 

## <a name="cause"></a>Ok
Van több lehetséges oka, beleértve:

* A fájl eredetében nem látható a CDN
* A végpont helytelenül van konfigurálva, a CDN, és keresse meg a megfelelő helyen, amely
* A gazdagép visszautasítja az állomásfejléc a CDN
* A végpont nem átvették a módosítást a CDN teljes propagálása

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
> [!IMPORTANT]
> A CDN-végpont létrehozása után azt nem azonnal elérhetővé válik való használatra, a regisztráció CDN propagálásához időt vesz igénybe.  Az <b>Akamai Azure CDN</b> típusú profilok propagálása általában egy percen belül befejeződik.  A <b>Verizon Azure CDN</b> típusú profilok propagálása általában 90 percen belül befejeződik, ám egyes esetekben több időt is igénybe vehet.  Ha befejezte a jelen dokumentumban leírt lépések, és továbbra is 404-es válaszkódot kap, várja meg, ellenőrizze újra a támogatási jegy megnyitása előtt néhány óra.
> 
> 

### <a name="check-the-origin-file"></a>Ellenőrizze az eredeti fájl
Először azt kell győződjön meg arról, hogy a fájl azt szeretnénk, gyorsítótárazott elérhetők legyenek a forrás és nyilvánosan elérhető.  Ez a leggyorsabb módja egy része az InPrivate-vagy Incognito munkamenetben nyisson meg egy böngészőt, és keresse meg a fájlt közvetlenül.  Csak írja be vagy illessze be az URL címet, és tekintse meg, ha a, amely a fájl várt okoz.  Például van egy Azure Storage-fiók esetén érhető el: a másolatból fogom `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`.  Ahogy látja, a vizsgálat sikeresen továbbítja.

![Sikerült!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Bár ez a leggyorsabb és legegyszerűbb módja, ellenőrizze a nyilvánosan elérhető a fájl, a szervezet bizonyos hálózati konfigurációk adhat meg, hogy a fájl esetén nyilvánosan elérhető a tényt, csak a hálózata (még akkor is, ha a felhasználók számára látható érhet helyezkedik el az Azure-ban).  Ha egy külső böngészőt, ahonnan tesztelheti, például az olyan mobileszköz, amely nem kapcsolódik a vállalati hálózathoz, vagy az Azure virtuális gép, amely lenne a legjobb.
> 
> 

### <a name="check-the-origin-settings"></a>A forrás beállításainak ellenőrzése
Most, hogy azt ellenőrzése a fájl nyilvánosan elérhető az interneten, a forrás beállításainak kell ellenőrizzük.  Az a [Azure Portal](https://portal.azure.com), keresse meg a CDN-profilt, majd kattintson a végpont meg elhárítani.  A kapott a **végpont** panelen kattintson a forrás.  

![A kijelölt forrás végpont panelről](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

A **származási** panel jelenik meg. 

![Forrás panel](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Forrása típusa és az állomásnév
Ellenőrizze a **forrása típusa** javítsa ki, és ellenőrizze a **forrásállomásnév**.  A példában `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, az URL-cím hostname része `cdndocdemo.blob.core.windows.net`.  Mint az ezen a képernyőfelvételen látható helyes.  Azure Storage, a Web App és a felhőalapú szolgáltatás források – a **forrásállomásnév** mező értéke egy legördülő menüben, így azt nem kell aggódnia a helyesírás-megfelelően.  Azonban ha egy egyéni forrás használ, van *feltétlenül kritikus* helyesen adta meg az állomásnevet!

#### <a name="http-and-https-ports"></a>A HTTP és HTTPS-portok
A másik műveletet annak ellenőrzésére, itt a **HTTP** és **HTTPS-portok**.  A legtöbb esetben 80-as és 443-as helyesek, és nem lesz szükség.  Azonban ha az eredeti kiszolgálóra egy másik portot figyeli, amely kell itt magát.  Ha nem biztos benne, csak tekintse meg az eredeti fájl URL-CÍMÉT.  A HTTP és HTTPS specifikációk 80-as és 443-as portot adja meg az alapértelmezett beállításokat. Az URL-címben `https://cdndocdemo.blob.core.windows.net/publicblob/lorem.txt`, a port nincs megadva, ezért az alapértelmezett 443-as feltételezi, és a beállítások helyesek.  

Azonban mondja ki az URL-cím, az eredeti fájl, amely korábban tesztelt érték `http://www.contoso.com:8080/file.txt`.  Megjegyzés: a `:8080` állomásnév szegmens végén.  A böngésző-port használatára, amely közli `8080` csatlakozni a webkiszolgáló `www.contoso.com`, ezért meg kell adnia a 8080-as a **HTTP-port** mező.  Fontos megjegyezni, hogy ezeket a portbeállításokat csak hatással milyen portját a végpont adatbeolvasás a forrásból.

> [!NOTE]
> Az **Akamai Azure CDN** típusú végpontok esetén a források számára nem áll rendelkezésre a teljes TCP-porttartomány.  A nem engedélyezett forrásportok listáját lást: [Azure CDN from Akamai Allowed Origin Ports](https://msdn.microsoft.com/library/mt757337.aspx) (Az Akamai Azure CDN engedélyezett forrásportjai).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Ellenőrizze a végpont beállításait
Vissza a **végpont** panelen kattintson a **konfigurálása** gombra.

![Konfigurálás gomb végpont panelről](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

A végpont **konfigurálása** panel jelenik meg.

![Konfigurálja a panelen](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokollok
A **protokollok**, győződjön meg arról, hogy az ügyfelek által használt protokollt, és van-e kiválasztva.  Ugyanazt a protokollt az ügyfél által használt lesznek, fontos, hogy a forrásportok az előző szakaszban megfelelően konfigurálva van a forrás eléréséhez használt.  A végpont csak az alapértelmezett HTTP és HTTPS-portok (80-as és 443-as), függetlenül attól, a forrásportok figyel.

Most térjen vissza a képzeletbeli példában `http://www.contoso.com:8080/file.txt`.  Felejtse el, a Contoso megadott `8080` mint a HTTP porttal, de tegyük is fel azokat a megadott `44300` a HTTPS-portjaként működik.  Ha nevű végpont hozza létre őket `contoso`, a CDN-végpont állomásnevéhez lenne `contoso.azureedge.net`.  A kérelem `http://contoso.azureedge.net/file.txt` egy HTTP-kérelem van, így a végpont szeretné használni a HTTP a 8080-as porton lekéréséhez a forrásból.  A HTTPS PROTOKOLLOKON keresztül biztonságos kérelmet `https://contoso.azureedge.net/file.txt`, okozna a végpont a HTTPS protokoll használatát a porton 44300 amikor beolvasni a fájlt a forrásból.

#### <a name="origin-host-header"></a>Forrás állomásfejlécét
A **forrás állomásfejlécét** minden egyes kérelemmel a forrásnak küldött állomásfejléc-érték.  A legtöbb esetben ez legyen ugyanaz, mint a **forrásállomásnév** azt korábban ellenőrzése.  Ebben a mezőben értékkel általában nem okoz a 404-es állapotot, de okozhat a többi 4xx állapot, attól függően, hogy a forrás vár.

#### <a name="origin-path"></a>Forrás elérési útvonalának
Végül ellenőrizze azt a **forrás elérési útvonalának**.  Alapértelmezés szerint ez üres az.  Ez a mező csak akkor ajánlott, ha azt szeretné, hogy elérhetők a CDN a forrás-kiszolgálón futó erőforrások behatárolni szeretné.  

Például a saját végpont, az Excel összes erőforrást a storage-fiókom elérhetőek, így I balra **forrás elérési útvonalának** üres.  Ez azt jelenti, hogy a kérelmek `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` eredményez a végpontok közötti kapcsolatra `cdndocdemo.core.windows.net` , amely a kérelmek `/publicblob/lorem.txt`.  Hasonlóképpen, a kérelem `https://cdndocdemo.azureedge.net/donotcache/status.png` eredményez a végpont a kért `/donotcache/status.png` a forrásból.

De mi történik, ha nem szeretnék használni a CDN minden a forrás elérési útja?  I. Tegyük fel például csak kívánta teszi közzé a `publicblob` elérési útja.  Ha a megadott */publicblob* a saját **forrás elérési útvonalának** mezőben, amely újraindítja a végpont beszúrása */publicblob* előtt minden kérelem benyújtásától és a forrás.  Ez azt jelenti, hogy a kérelem `https://cdndocdemo.azureedge.net/publicblob/lorem.txt` most ténylegesen érvénybe az URL-címet, a kérés részét `/publicblob/lorem.txt`, és a hozzáfűző `/publicblob` elején. Az eredmény kérelmet `/publicblob/publicblob/lorem.txt` a forrásból.  Ha a, hogy elérési útja nem oldható fel egy fájl, a forrás 404-es állapotot ad vissza.  A helyes URL-cím lekéréséhez ebben a példában lorem.txt ténylegesen lenne `https://cdndocdemo.azureedge.net/lorem.txt`.  Vegye figyelembe, hogy nem magában foglalja a */publicblob* elérési utat, mert a kérelem része az URL-cím `/lorem.txt` , és hozzáadja a végpont `/publicblob`, így a `/publicblob/lorem.txt` a kérés átadódik az eredeti.

