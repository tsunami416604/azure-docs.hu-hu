---
title: 404-es állapotkódot adja vissza az Azure CDN-végpontok hibaelhárítása |} A Microsoft Docs
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
ms.openlocfilehash: 66ee211856bb451caad7af02103aa306d76e8f97
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799235"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>404-es állapotkódot adja vissza az Azure CDN-végpontok hibaelhárítása
Ez a cikk lehetővé teszi az Azure Content Delivery Network (CDN) végpontok, amelyek 404-es HTTP-állapotkódok vissza kapcsolatos hibaelhárítás.

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a [az MSDN Azure és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik megoldásként is fájl is egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) válassza **támogatás kérése**.

## <a name="symptom"></a>Jelenség
Létrehozott egy CDN-profil és a egy végpontot, de a tartalom nem úgy tűnik, hogy a CDN-en elérhető. Felhasználók, akik megpróbálnak csatlakozni a tartalom a CDN URL-CÍMEN keresztül kap egy HTTP 404 állapotkódot. 

## <a name="cause"></a>Ok
Van több lehetséges oka lehet, többek között:

* A fájl eredetében nem látható a CDN-re.
* A végpont helytelenül van konfigurálva, így a CDN-t, és tekintse meg a megfelelő helyen.
* A gazdagép visszautasítja az állomásfejléc a CDN-ből.
* A végpont nem volt időm propagálása a CDN teljes.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
> [!IMPORTANT]
> Miután létrehozta a CDN-végpont, nem azonnal lesz elérhető, amely szükséges idő a regisztráció propagálásához a CDN-en keresztül:
> - A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
> - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
> - A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 90 percen belül fejeződik be. 
> 
> Ha ebben a dokumentumban leírt lépéseket, és továbbra is 404-es válaszkódot kap, várja meg, néhány óra alatt újra támogatási jegy megnyitása előtt.
> 
> 

### <a name="check-the-origin-file"></a>Ellenőrizze az eredeti fájl
Először győződjön meg arról, hogy a gyorsítótár-fájl érhető el a forráskiszolgáló és a nyilvánosan elérhető-e az interneten. A leggyorsabb módja, hogy egy privát vagy inkognitó munkamenetet, és tallózással keresse meg a fájlt közvetlenül a nyisson meg egy böngészőt. Írja be vagy illessze be az URL-címet a címsorba, és győződjön meg arról, hogy a fájl várt eredmény. Tegyük fel például, egy fájlt egy Azure Storage-fiókban lévő elérhető-e a https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Ha sikeresen betöltheti a fájl tartalmát, a teszt továbbítja.

![Sikerült!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Amíg ez a leggyorsabb és legegyszerűbb módja, ellenőrizze a fájl nyilvánosan elérhető, a szervezet bizonyos hálózati konfigurációk teheti jelennek meg, hogy egy fájlt érhető el nyilvánosan amikor, sőt, csak látják a felhasználók a hálózat (még akkor is, ha vannak tárolva Azure-ra). Győződjön meg arról, hogy ez nem ez a helyzet, tesztelje a fájl egy külső böngészővel, például a mobileszközök, amelyek a vállalati hálózaton, vagy az Azure-beli virtuális gép nincs csatlakoztatva.
> 
> 

### <a name="check-the-origin-settings"></a>A forrás beállításainak ellenőrzése
Miután meggyőződött a fájl nyilvánosan elérhető az interneten, ellenőrizze a forrás beállításainak. Az a [az Azure Portal](https://portal.azure.com), keresse meg a CDN-profilra, és válassza ki a végpont dolgozik. A létrejövő a **végpont** lapon, válassza ki a forrás.  

![Végpont oldala, amelyen kiemelve forrása](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

A **forrás** lap jelenik meg. 

![Forrás lap](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Forrás típusa és az állomásnév
Ellenőrizze, hogy az értékét a **forrása típusa** és **forrás gazdaneve** helyes-e. Ebben a példában https:\/cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, az URL-cím állomásnév részét van *cdndocdemo.blob.core.windows.net*, amely helyességéről. Mivel Azure Storage, a Web App és a Felhőszolgáltatás források legördülő lista értékét a **forrás gazdaneve** mezőt, helytelen helyesírása nem probléma. Azonban ha egy egyéni forrás használ, győződjön meg arról, hogy helyesen írta-e be az állomásnevet.

#### <a name="http-and-https-ports"></a>A HTTP és HTTPS-portok
Ellenőrizze a **HTTP** és **HTTPS-portok**. A legtöbb esetben 80-as és 443-as helyesek, és a módosítások nem lesz szükség.  Azonban ha a forráskiszolgáló egy másik porton figyel, amely kell itt képviseli. Ha nem biztos benne, tekintse meg a forrásfájl URL-CÍMÉT. A HTTP és HTTPS-specifikációk 80-as és 443-as az alapértelmezett beállításokat használja. A példa URL-címben https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, a port nincs megadva, így a rendszer feltételezi a rendszer az alapértelmezett 443-as és a beállítások helyesek.  

Előfordulhat azonban, hogy az URL-cím, az eredeti fájl, amely korábban, a tesztelt van http:\//www.contoso.com:8080/file.txt. Megjegyzés: a *: 8080-as* végén található a hostname szegmens részét. A 8080-as porton található a www-kiszolgáló kapcsolódáshoz a böngésző arra utasítja a szám meglévő\.contoso.com, ezért kell megadnia *8080-as* a a **HTTP-port** mező. Fontos megjegyezni, hogy ezeket a portbeállításokat hatással vannak, melyik porton csak a végpontot használja a forrás lévő információk lekéréséhez.

> [!NOTE]
> **Az Azure CDN Akamai Standard** végpontok nem teszik lehetővé a teljes TCP-porttartomány források.  A nem engedélyezett forrásportok listáját lást: [Azure CDN from Akamai Allowed Origin Ports](/previous-versions/azure/mt757337(v=azure.100)) (Az Akamai Azure CDN engedélyezett forrásportjai).  
> 
> 

### <a name="check-the-endpoint-settings"></a>Ellenőrizze a végpont beállításait
Az a **végpont** lapon válassza ki a **konfigurálása** gombra.

![Végpont lap konfigurálása gomb kiemelésével](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

A CDN-végpont **konfigurálása** lap jelenik meg.

![Lapjának konfigurálása](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokollok
A **protokollok**, győződjön meg arról, hogy az ügyfelek által használt protokollt, van-e kiválasztva. Mivel ugyanazt a protokollt, amelyet az ügyfél a forrás eléréséhez használt, fontos a forrásportok az előző szakaszban megfelelően konfigurálva van. A CDN-végpont csak az alapértelmezett HTTP és HTTPS-porton (80-as és 443-as), függetlenül a forrásportok figyel.

Http-képzeletbeli példában térjen:\//www.contoso.com:8080/file.txt.  Felejtse el, mert a megadott Contoso *8080-as* mint a HTTP-portot, de azt is feltételezzük, a megadott *44300* a HTTPS-port.  Ha egy végpont nevű *contoso*, a CDN-végpont gazdanevére lenne *contoso.azureedge.net*.  A http-kérelem:\//contoso.azureedge.net/file.txt HTTP-kérést, így a végpontot használja HTTP a 8080-as porton is lekérheti azt a forrásból.  HTTPS, a https-kapcsolaton keresztül egy biztonságos kérelmet: \/ /contoso.azureedge.net/file.txt, okozna a végpontot a HTTPS használatára 44300 porton, a fájlt a forrásból lekérésekor.

#### <a name="origin-host-header"></a>Forrás állomásfejléce
A **forrás állomásfejléce** minden egyes kérelemmel a forrásnak küldött állomásfejléc-érték.  A legtöbb esetben ez legyen ugyanaz, mint a **forrás gazdaneve** azt korábban ellenőrizte.  Ebben a mezőben helytelen értéket általában nem okoznak a 404-es állapotot, de nagy eséllyel eredményez a többi 4xx állapot, attól függően, a forrás vár.

#### <a name="origin-path"></a>Forrás elérési útvonala
Végül, hogy ellenőrizze a **forrás elérési útja**.  Alapértelmezés szerint ez üres a.  Ez a mező csak akkor ajánlott, ha a forrás-ban üzemeltetett erőforrások a CDN-en elérhetővé tenni kívánt a hatókörét leszűkítheti szeretné.  

Példa végpontját szerettünk volna összes erőforrást a tárfiókban elérhető, így **forrás elérési útja** üres.  Ez azt jelenti, hogy HTTPS-kérelem:\//cdndocdemo.azureedge.net/publicblob/lorem.txt eredményez a végpontról kapcsolatot kérő cdndocdemo.core.windows.net */publicblob/lorem.txt*.  Hasonlóképpen, egy HTTPS-kérelem:\//cdndocdemo.azureedge.net/donotcache/status.png eredményez a kérelmező végpont */donotcache/status.png* a forrásból.

De mi a teendő, ha nem szeretné a CDN használata a forrás minden útvonalhoz?  Tegyük fel, hogy csak szeretné tenni a *publicblob* elérési útja.  Ha azt adja meg */publicblob* a a **forrás elérési útja** mező, amely újraindítja a végpontot a Beszúrás */publicblob* előtt minden kérelemnél jutó kerül sor.  Ez azt jelenti, hogy a kérelem https:\//cdndocdemo.azureedge.net/publicblob/lorem.txt valójában már érvénybe az URL-címet, a kérelem részét */publicblob/lorem.txt*, és a hozzáfűző */publicblob* elejére. Ennek hatására a kérelmet */publicblob/publicblob/lorem.txt* a forrásból.  Ha az elérési út nem oldható fel egy fájl, a forrás visszaküldi a 404-es állapot.  Ebben a példában lorem.txt megszerezni a helyes URL ténylegesen lenne https:\//cdndocdemo.azureedge.net/lorem.txt.  Vegye figyelembe, hogy nem tartalmazza a */publicblob* elérési út esetén az összes, mert a kérelem URL-címrészt */lorem.txt* , és hozzáadja a végpontot */publicblob*, ez pedig */publicblob/lorem.txt* a kérés átadódik az a forrás.

