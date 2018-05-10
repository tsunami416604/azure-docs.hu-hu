---
title: 404-es állapotkód visszaadó Azure CDN-végpontok hibaelhárítása |} Microsoft Docs
description: 404-es válaszkódot az Azure CDN-végpontok hibaelhárítása.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 1cffef5bbda475032ee7ff07188ab0d9d52846ea
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>404-es állapotkód visszaadó Azure CDN-végpontok hibaelhárítása
Ez a cikk elhárítása az Azure Content Delivery Network (CDN) végpontok 404-es HTTP-állapotkódok visszaadó teszi lehetővé.

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a [az MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl is az Azure támogatási incidens. Lépjen a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatás**.

## <a name="symptom"></a>Jelenség
Létrehozta a CDN-profil és egy végpontot, de a tartalom úgy tűnik, hogy a CDN elérhetőnek lennie. Felhasználók, akik az CDN URL-cím a tartalom elérésére irányuló kísérlet egy HTTP 404 állapotkódot kapnak. 

## <a name="cause"></a>Ok
Van több lehetséges oka, beleértve:

* A fájl eredetében nem látható a CDN.
* A végpont helytelenül van konfigurálva, a CDN, és keresse meg a megfelelő helyen, amely.
* A gazdagép visszautasítja a CDN állomásfejlécét.
* A végpont nem átvették a módosítást a CDN teljes propagálására.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
> [!IMPORTANT]
> A CDN-végpont létrehozása után nem azonnal lesz használható, mivel némi időre van a CDN propagálásához a regisztráció:
> - A **Azure CDN Standard Microsoft** -profilok propagálása általában befejezi tíz perc múlva. 
> - A **Azure CDN Standard Akamai** -profilok propagálása általában befejezi egy percen belül. 
> - A **Azure CDN Standard verizon** és **verizon Azure CDN Premium** -profilok propagálása általában befejezi 90 percen belül. 
> 
> Ha befejezte a jelen dokumentumban leírt lépések, és továbbra is 404-es válaszkódot kap, várja meg, ellenőrizze újra a támogatási jegy megnyitása előtt néhány óra.
> 
> 

### <a name="check-the-origin-file"></a>Ellenőrizze az eredeti fájl
Először ellenőrizze, hogy a gyorsítótár fájlt az eredeti kiszolgálón érhetők el, és nyilvánosan elérhető az interneten. A leggyorsabb, amely módja egy magán- vagy incognito munkamenetet, és tallózással keresse meg a fájlt közvetlenül a böngésző megnyitásához. Írja be vagy illessze be az URL címet, és győződjön meg arról, hogy a fájl várt eredményezi. Tegyük fel például, egy Azure Storage-fiókot, https címen érhető el a fájlt:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Ha ez a fájl tartalma sikeresen betölthető, átadja a vizsgálatot.

![Sikerült!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Ez a leggyorsabb és legegyszerűbb módja ellenőrizze a fájl nyilvánosan elérhető, a szervezet bizonyos hálózati konfigurációk teheti jelenik meg, amely nem egy fájl esetén nyilvánosan elérhető a tényt, csak a felhasználók számára látható a hálózat (még akkor is, ha a fut. Azure). Győződjön meg arról, hogy ez nem, tesztelje a fájl egy külső böngészővel, például az olyan mobileszköz, amely nem kapcsolódik a vállalati hálózathoz, vagy egy virtuális gép az Azure-ban.
> 
> 

### <a name="check-the-origin-settings"></a>A forrás beállításainak ellenőrzése
Miután ellenőrizte, hogy a fájl nyilvánosan az interneten érhető el, ellenőrizze a forrás beállításainak. Az a [Azure Portal](https://portal.azure.com), keresse meg a CDN-profilt, és válassza ki a végpontot meg elhárítani. A **végpont** lapon, válassza ki a forrás.  

![A kijelölt forrás végpont lap](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

A **származási** lap jelenik meg. 

![Forrás lap](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Forrása típusa és az állomásnév
Ellenőrizze, hogy az értékek a **forrása típusa** és **forrásállomásnév** helyes-e. Ebben a példában, https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, az URL-cím hostname része *cdndocdemo.blob.core.windows.net*, amely helyességéről. Mivel az Azure tárolás, a Web App és a felhőalapú szolgáltatás források legördülő lista értéket használja a **forrásállomásnév** mezőjét, helytelen helyesírása nem problémát. Azonban ha egy egyéni forrás használ, győződjön meg arról, hogy helyesen írta-e az állomásnevet.

#### <a name="http-and-https-ports"></a>A HTTP és HTTPS-portok
Ellenőrizze a **HTTP** és **HTTPS-portok**. A legtöbb esetben 80-as és 443-as helyesek, és nem lesz szükség.  Azonban ha az eredeti kiszolgálóra egy másik portot figyeli, amely kell itt magát. Ha nem biztos benne, tekintse meg az eredeti fájl URL-CÍMÉT. A HTTP és HTTPS specifikációk 80-as és 443-as portot használja az alapértelmezett beállításokat. Példa URL-címét, https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, a port nincs megadva, ezért az alapértelmezett 443-as feltételezi, és a beállítások helyesek.  

Előfordulhat azonban, hogy az URL-cím, az eredeti fájl, amely korábban tesztelt: http:\//www.contoso.com:8080/file.txt. Megjegyzés: a *: 8080* állomásnév szegmens végén részét. Hogy szám utasítja, hogy a 8080-as port használatával csatlakoznak a webkiszolgálóhoz: www.contoso.com, ezért meg kell adnia *8080* a a **HTTP-port** mező. Fontos megjegyezni, hogy ezeket a portbeállításokat hatással port csak a végpont használatával adatbeolvasás a forrásból.

> [!NOTE]
> **Az Azure CDN Standard Akamai** végpontok nem teszik lehetővé a teljes TCP-porttartomány esetén a források számára.  A nem engedélyezett forrásportok listáját lást: [Azure CDN from Akamai Allowed Origin Ports](https://msdn.microsoft.com/library/mt757337.aspx) (Az Akamai Azure CDN engedélyezett forrásportjai).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Ellenőrizze a végpont beállításait
Az a **végpont** lapon jelölje be a **konfigurálása** gombra.

![A Konfigurálás gombra a kijelölt végpont lap](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

A CDN-végpont **konfigurálása** lap jelenik meg.

![Lapjának konfigurálása](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokollok
A **protokollok**, győződjön meg arról, hogy az ügyfelek által használt protokollt, és van-e kiválasztva. Mivel az ügyfél által használt ugyanazt a protokollt a forrás eléréséhez használt, fontos a forrásportok az előző szakaszban megfelelően konfigurálva van. A CDN-végpont csak az alapértelmezett HTTP és HTTPS-portok (80-as és 443-as), függetlenül attól, a forrásportok figyel.

Most a fenti képzeletbeli példában http-vel való visszatéréshez:\//www.contoso.com:8080/file.txt.  Felejtse el, a Contoso megadott *8080* mint a HTTP porttal, de tegyük is fel azokat a megadott *44300* a HTTPS-portjaként működik.  Ha nevű végpont hozza létre őket *contoso*, a CDN-végpont állomásnevéhez lenne *contoso.azureedge.net*.  A kérelem http:\//contoso.azureedge.net/file.txt egy HTTP-kérelem, így a végpont szeretné használni a HTTP a 8080-as porton lekéréséhez a forrásból.  Biztonságos kérelmet HTTPS https: \/ /contoso.azureedge.net/file.txt, okozna a végpont a HTTPS protokoll használatát 44300 porton történő lekérésekor. a fájlt a forrásból.

#### <a name="origin-host-header"></a>Forrás állomásfejléce
A **forrás állomásfejlécét** minden egyes kérelemmel a forrásnak küldött állomásfejléc-érték.  A legtöbb esetben ez legyen ugyanaz, mint a **forrásállomásnév** azt korábban ellenőrzése.  Ebben a mezőben értékkel általában nem okoz a 404-es állapotot, de okozhat a többi 4xx állapot, attól függően, hogy a forrás vár.

#### <a name="origin-path"></a>Forrás elérési útvonala
Végül ellenőrizze azt a **forrás elérési útvonalának**.  Alapértelmezés szerint ez üres az.  Ez a mező csak akkor ajánlott, ha azt szeretné, hogy elérhetők a CDN a forrás-kiszolgálón futó erőforrások behatárolni szeretné.  

A példa végpont akartunk összes erőforrást a tárfiók van, így **forrás elérési útvonalának** üres.  Ez azt jelenti, hogy a https kérelmek:\//cdndocdemo.azureedge.net/publicblob/lorem.txt eredményezi egy kapcsolatot a végpont és kérő cdndocdemo.core.windows.net */publicblob/lorem.txt*.  Hasonlóképpen, HTTPS-kérelem:\//cdndocdemo.azureedge.net/donotcache/status.png eredményez a végpont a kért */donotcache/status.png* a forrásból.

De mi történik, ha nem kívánja minden a forrás elérési útja a CDN használata?  Tegyük fel például, csak szeretne elérhetővé tenni a *publicblob* elérési útja.  Ha azt adja meg */publicblob* a a **forrás elérési útvonalának** mezőben, amely újraindítja a végpont beszúrása */publicblob* előtt minden kérelemnél eredeti történik.  Ez azt jelenti, hogy a kérelmet https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt ténylegesen most érvénybe az URL-címet, a kérés részét */publicblob/lorem.txt*, és a hozzáfűző */publicblob* elején. Az eredmény kérelmet */publicblob/publicblob/lorem.txt* a forrásból.  Ha a, hogy elérési útja nem oldható fel egy fájl, a forrás 404-es állapotot ad vissza.  A helyes URL-cím lekéréséhez ebben a példában lorem.txt ténylegesen lenne https:\//cdndocdemo.azureedge.net/lorem.txt.  Vegye figyelembe, hogy nem magában foglalja a */publicblob* elérési utat, mert a kérelem része az URL-cím */lorem.txt* , és hozzáadja a végpont */publicblob*, így a */publicblob/lorem.txt* a kérés átadódik az eredeti.

