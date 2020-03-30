---
title: Az Azure CDN-végpontok hibaelhárítása - 404-es állapotkód
description: 404-es válaszkódok elhárítása az Azure CDN-végpontokkal.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: b588a1eb-ab69-4fc7-ae4d-157c3e46f4a8
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: c332c6712cdf057491e3039854aa1a29bd54196f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083120"
---
# <a name="troubleshooting-azure-cdn-endpoints-that-return-a-404-status-code"></a>A 404-es állapotkódot visszaadó Azure CDN-végpontok hibaelhárítása
Ez a cikk lehetővé teszi az Azure Content Delivery Network (CDN) 404 HTTP-válasz állapotkódokat visszaadó végpontjaival kapcsolatos problémák elhárítását.

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel [az MSDN Azure-ban és a Veremtúlcsordulás fórumain.](https://azure.microsoft.com/support/forums/) Azt is megteheti, hogy egy Azure-támogatási incidenst is benyújthat. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és válassza **a Támogatás beszerezni lehetőséget.**

## <a name="symptom"></a>Hibajelenség
Létrehozott egy CDN-profilt és egy végpontot, de úgy tűnik, hogy a tartalom nem érhető el a CDN-en. Azok a felhasználók, akik a CDN URL-címén keresztül próbálnak hozzáférni a tartalomhoz, HTTP 404-es állapotkódot kapnak. 

## <a name="cause"></a>Ok
Számos oka lehet, többek között:

* A fájl eredete nem látható a CDN számára.
* A végpont helytelenül van konfigurálva, ami miatt a CDN rossz helyen keres.
* Az állomás elutasítja az állomásfejlécet a CDN-ből.
* A végpontnak nem volt ideje a CDN-ben propagálni.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
> [!IMPORTANT]
> A CDN-végpont létrehozása után nem lesz azonnal elérhető, mivel a regisztráció nak időre van szükség a CDN-en keresztül történő propagálásához:
> - A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
> - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
> - A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 90 percen belül fejeződik be. 
> 
> Ha elvégzi a jelen dokumentumlépéseit, és még mindig 404-es válaszokat kap, fontolja meg, hogy várjon néhány órát, hogy újra ellenőrizze a támogatási jegy megnyitása előtt.
> 
> 

### <a name="check-the-origin-file"></a>A forrásfájl ellenőrzése
Először ellenőrizze, hogy a gyorsítótárazandó fájl elérhető-e az eredeti kiszolgálón, és nyilvánosan elérhető-e az interneten. Ennek leggyorsabb módja, ha privát vagy inkognitóban nyit meg egy böngészőt, és közvetlenül a fájlhoz böngészik. Írja be vagy illessze be az URL-címet a címmezőbe, és ellenőrizze, hogy az a várt fájlt eredményezi-e. Tegyük fel például, hogy rendelkezik egy fájllal\/egy Azure Storage-fiókban, amely https-en érhető el: /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt. Ha sikeresen be tudja tölteni a fájl tartalmát, akkor megfelel a teszten.

![Sikerült!](./media/cdn-troubleshoot-endpoint/cdn-origin-file.png)

> [!WARNING]
> Bár ez a leggyorsabb és legegyszerűbb módja annak, hogy ellenőrizze, hogy a fájl nyilvánosan elérhető-e, a szervezet egyes hálózati konfigurációi azt a látszatot tehetik ki, hogy egy fájl nyilvánosan elérhető, ha valójában csak a hálózat felhasználói számára látható (még akkor is, ha a Azure). Annak érdekében, hogy ez nem így van,, tesztelje a fájlt egy külső böngészővel, például egy mobileszközzel, amely nem csatlakozik a szervezet hálózatához, vagy egy virtuális gép az Azure-ban.
> 
> 

### <a name="check-the-origin-settings"></a>A kezdőor beállításainak ellenőrzése
Miután meggyőződött arról, hogy a fájl nyilvánosan elérhető az interneten, ellenőrizze a származási beállításokat. Az [Azure Portalon](https://portal.azure.com)keresse meg a CDN-profilját, és válassza ki a hibaelhárítási végpontot. Az eredményül kapott **végpontlapon** válassza ki az origát.  

![Végpontoldal kiemelt kezdőponttal](./media/cdn-troubleshoot-endpoint/cdn-endpoint.png)

Megjelenik **az Origin** oldal. 

![Forrás oldal](./media/cdn-troubleshoot-endpoint/cdn-origin-settings.png)

#### <a name="origin-type-and-hostname"></a>Eredet típusa és állomásneve
Ellenőrizze, hogy az **Origin típus** és az Origin **állomásnév** értékei helyesek-e. Ebben a példában\/https: /cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, az URL-cím állomásnév része *cdndocdemo.blob.core.windows.net*, ami helyes. Mivel az Azure Storage, a Web App és a Cloud Service eredete legördülő listaértéket használ az **Origin állomásnév** mezőhöz, a helytelen helyesírás nem jelent problémát. Ha azonban egyéni eredeteredetet használ, győződjön meg arról, hogy a gazdagépnév helyesen van beírva.

#### <a name="http-and-https-ports"></a>HTTP- és HTTPS-portok
Ellenőrizze a HTTP- és **HTTPS-portokat.** **HTTP** A legtöbb esetben a 80 és a 443 helyes, és nem lesz szükség módosításokra.  Ha azonban az eredeti kiszolgáló egy másik porton figyel, akkor azt itt kell képviselni. Ha nem biztos benne, tekintse meg az eredeti fájl URL-címét. A HTTP és HTTPS specifikációk a 80-as és a 443-as portot használják alapértelmezettként. A példa URL-címében https:\//cdndocdemo.blob.core.windows.net/publicblob/lorem.txt, a port nincs megadva, így az alapértelmezett 443 a 443-as értéket feltételezi, és a beállítások helyesek.  

Tegyük fel azonban, hogy a korábban tesztelt\/eredeti fájl URL-címe http: /www.contoso.com:8080/file.txt. Figyelje meg a *8080-as* részt a hostname szegmens végén. Ez a szám arra utasítja a böngészőt, hogy a\.8080-as portot használja a www contoso.com webkiszolgálóhoz való csatlakozáshoz, ezért meg kell adnia a *8080-at* a **HTTP port** mezőben. Fontos megjegyezni, hogy ezek a portbeállítások csak azt befolyásolják, hogy a végpont milyen portot használ az információ lekéréséhez az origóból.

> [!NOTE]
> **Az Akamai-végpontok Azure CDN standard** szolgáltatása nem engedélyezi a teljes TCP-porttartományt az originok számára.  A nem engedélyezett forrásportok listáját lást: [Azure CDN from Akamai Allowed Origin Ports](/previous-versions/azure/mt757337(v=azure.100)) (Az Akamai Azure CDN engedélyezett forrásportjai).  
> 
> 

### <a name="check-the-endpoint-settings"></a>A végpont beállításainak ellenőrzése
A **Végpont** lapon kattintson a **Beállítás** gombra.

![Végpontlap kiemelt beállítással](./media/cdn-troubleshoot-endpoint/cdn-endpoint-configure-button.png)

Megjelenik a CDN-végpont **konfigurálása** lapja.

![Lap konfigurálása](./media/cdn-troubleshoot-endpoint/cdn-configure.png)

#### <a name="protocols"></a>Protokollok
**Protokollok**esetén ellenőrizze, hogy az ügyfelek által használt protokoll ki van-e jelölve. Mivel az ügyfél által használt protokoll az eredeti eléréséhez használt protokoll, fontos, hogy az előző szakaszban megfelelően konfigurálják az eredeti portokat. A CDN-végpont csak az alapértelmezett HTTP- és HTTPS-portokon (80 és 443) figyel, függetlenül az eredeti portoktól.

Térjünk vissza a mi hipotetikus példa http:\//www.contoso.com:8080/file.txt.  Ahogy emlékezni fog, contoso meg *8080* HTTP-portként, de tegyük fel azt is, hogy a *44300-at* jelölték meg HTTPS-portként.  Ha *contoso*nevű végpontot hoznak létre, a CDN-végpont állomásneve *contoso.azureedge.net*lesz.  Http:/contoso.azureedge.net/file.txt\/egy HTTP-kérelem, így a végpont http-t használ n a 8080-as porton, hogy lekérje azt az origóból.  A HTTPS protokollon keresztüli biztonságos kérelem https:\//contoso.azureedge.net/file.txt esetén a végpont https-t használ a 44300-as porton, amikor a fájlt az eredetiforrásból kéri.

#### <a name="origin-host-header"></a>Forrás állomásfejléce
Az **Origin állomásfejléc** az a gazdafejléc-érték, amelyet minden kéréssel az orisztának küldenek.  A legtöbb esetben ennek meg kell egyeznie a korábban ellenőrzött **Origin állomásnévvel.**  Ebben a mezőben a helytelen érték általában nem 404 állapotot okoz, de valószínűleg más 4xx állapotokat is okoz, attól függően, hogy az origó mire számít.

#### <a name="origin-path"></a>Forrás elérési útvonala
Végül ellenőriznünk kell **az Eredet útvonalát.**  Alapértelmezés szerint ez üres.  Ezt a mezőt csak akkor használja, ha szűkíteni szeretné a CDN-en elérhetővé tenni kívánt eredeti legforrásként tárolt erőforrások hatókörét.  

A példavégpontban azt akartuk, hogy a tárfiók összes erőforrása elérhető legyen, így az **Origin elérési útja** üresen maradt.  Ez azt jelenti, hogy\/a https: /cdndocdemo.azureedge.net/publicblob/lorem.txt-ra irányuló kérés kapcsolatot eredményez a végpontról a */publicblob/lorem.txt*kérésre kérő cdndocdemo.core.windows.net.  Hasonlóképpen a https:\//cdndocdemo.azureedge.net/donotcache/status.png kérelem a */donotcache/status.png* az eredetitől a végpontot kéri.

De mi van, ha nem szeretné használni a CDN minden utat a származási?  Tegyük fel, hogy csak a nyilvános blob elérési útját akarta *felfedni.*  Ha a */publicblob* értéket az **Origin elérési út** mezőjébe adjuk, akkor a végpont beszúrása */publicblob* lesz, mielőtt minden kérés az origóra érkezne.  Ez azt jelenti, hogy\/a https: /cdndocdemo.azureedge.net/publicblob/lorem.txt kérelem most már ténylegesen az URL-cím kérésrészét, a */publicblob/lorem.txt,* és hozzáfűzi */publicblob* az elejéhez. Ennek eredményeképpen a */publicblob/publicblob/lorem.txt* fájl ra vonatkozó kérelem jelenik meg az eredetitől.  Ha ez az elérési út nem oldódik fel egy tényleges fájlba, az origó 404-es állapotot ad vissza.  Ebben a példában a lorem.txt fájl beolvasásához a helyes URL-cím valójában https:\//cdndocdemo.azureedge.net/lorem.txt.  Vegye figyelembe, hogy egyáltalán nem vesszük fel a */publicblob* elérési utat, mert az URL-cím kérésrésze */lorem.txt,* és a végpont hozzáadja a */publicblob*kapcsolót, ami azt eredményezi, hogy *a /publicblob/lorem.txt* az eredetnek átadott kérelem.

