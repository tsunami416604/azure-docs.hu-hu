---
title: Importálása és exportálása egy tartományi zónafájl az Azure DNS Azure parancssori felületével |} A Microsoft Docs
description: Ismerje meg, hogyan importálása és exportálása a DNS-zónafájl az Azure DNS Azure CLI-vel
services: dns
documentationcenter: na
author: vhorne
manager: timlt
ms.assetid: f5797782-3005-4663-a488-ac0089809010
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2018
ms.author: victorh
ms.openlocfilehash: 37dd51a60d9ec6e018f58e2cfd593490d126670b
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888651"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>Importálása és exportálása a DNS-zónafájl az Azure CLI használatával 

Ez a cikk végigvezeti importálása és exportálása a DNS-zónafájlok az Azure DNS-hez, az Azure CLI használatával.

## <a name="introduction-to-dns-zone-migration"></a>Bevezetés a DNS-zóna áttelepítése

DNS-zónafájl egy szövegfájlt, amelyek minden zónában tartománynévrendszer (DNS) rekord részleteit tartalmazza. Ebből következik, szabványos formátumban, alkalmassá téve azt a DNS-rekordok DNS rendszerek közötti átviteléhez. A zónafájl használata egy gyors, megbízható és kényelmes módon vihetők át a DNS-zónát, vagy onnan máshová az Azure DNS-ben.

Az Azure DNS támogatja a importálásával és exportálásával zónafájlok az Azure parancssori felület (CLI) használatával. A zónafájl importálása van **nem** jelenleg az Azure PowerShell vagy az Azure Portalon keresztül támogatott.

Az Azure CLI az Azure-szolgáltatások kezelésére használt többplatformos parancssori eszköz. Elérhető a Windows, Mac és Linux platformokon a a [Azure letöltések lap](https://azure.microsoft.com/downloads/). Többplatformos támogatást fontos történő importálására és exportálására zónafájlok, mert a leggyakrabban használt név kiszolgálószoftver [kötési](https://www.isc.org/downloads/bind/), általában a Linux rendszeren futó.


## <a name="obtain-your-existing-dns-zone-file"></a>Szerezze be a meglévő DNS-zónafájl

Az Azure DNS DNS-zónafájl importálása, mielőtt kell a zónafájl hozzájutni. A fájl forrásában attól függ, ahol a DNS-zóna jelenleg üzemel.

* Ha a DNS-zóna (például a tartományregisztráló, dedikált DNS-szolgáltató, vagy alternatív felhőszolgáltató) partner szolgáltatás működteti, a szolgáltatás kell lehetővé teszi, hogy töltse le a DNS-zónafájljában.
* Ha a DNS-zóna Windows DNS-ban üzemel, van-e az alapértelmezett mappát, a zóna fájlok **%systemroot%\system32\dns**. Minden zóna fájl teljes elérési útját is látható a **általános** lapján a DNS-konzolt.
* A DNS-zóna-KÖTÉS használatával üzemeltet, ha a zóna fájl minden zóna helye van megadva a KÖTÉS konfigurációs fájlban **named.conf**.

> [!NOTE]
> GoDaddy-től letöltött zónafájlok formátuma kissé nem szabványos. A probléma megoldása, ezek a fájlok zóna Azure DNS-ben való importálása előtt kell.
>
> A REKORDADAT minden DNS-rekord a DNS-nevek teljes nevének beírásában vannak megadva, de nem rendelkezik a záró "." Ez azt jelenti, hogy azok relatív nevek DNS más rendszerekbe való telepítésekor értelmez. A zónafájl hozzáfűzni a megszakítást okozó szerkeszteni szeretné "." Azure DNS-ben történő importálása előtt nevüket.
>
> Ha például a CNAME rekord "www 3600 CNAME contoso.com" kell módosítani a "www 3600 CNAME contoso.com."
> (a záró ".").

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Az Azure DNS DNS-zónafájl importálása

Az Azure DNS-zónafájl importálása az új zóna létrehozza, ha az egyik nem létezik. Ha a zóna már létezik, a rekordhalmazok zónafájljában egyesíthető kell a meglévő rekordhalmazt.

### <a name="merge-behavior"></a>Viselkedés egyesítése

* Alapértelmezés szerint a meglévő és új rekordhalmazok összefésülése megtörténjen. Egyesített rekordhalmaz azonos rekordján megszüntetéséhez duplikált.
* Rekordhalmazok egyesítésekor, amikor az élettartam (TTL), már létező rekordhalmazok idő szolgál.
* Kezdő hatóság (SOA) paraméterek (kivéve a `host`) mindig megnyílik a zóna importált fájlból. Ehhez hasonlóan a névkiszolgálói rekord a zóna legfelső pontján állítsa be, a az élettartam mindig veszi át a importált zónafájlban.
* Az importált CNAME rekord nem cserélje le egy meglévő CNAME-rekord ugyanazzal a névvel.  
* Ha ütközés lép fel egy CNAME-rekordot és a egy másik rekord, azonos nevű, de más típusú (függetlenül attól, amely meglévő vagy új) között, a rendszer megőrzi a meglévő rekord. 

### <a name="additional-information-about-importing"></a>További információ a importálása

Az alábbi megjegyzések adja meg a zóna további technikai részleteiért importálási folyamatot.

* A `$TTL` irányelv nem kötelező, és a támogatott. Ha nem `$TTL` irányelv van megadva, a rekordok egy explicit TTL nélkül importált állítva egy alapértelmezett élettartam 3600 másodperc. Ha a beállított ugyanazon rekord két rekordot adja meg a különböző TTLs, az alacsonyabb értéket használja.
* A `$ORIGIN` irányelv nem kötelező, és a támogatott. Ha nem `$ORIGIN` beállítva, a használt alapértelmezett érték a zóna nevét a parancssorban megadott (és a záró ".").
* A `$INCLUDE` és `$GENERATE` nem támogatott.
* Ezek erőforrásrekord-típusok támogatottak: A, AAAA, CAA, CNAME, MX, NS, SOA, SRV, and TXT.
* A SOA típusú rekord automatikusan létrejön az Azure DNS által a zóna létrehozásakor. Egy zóna fájl importálásakor összes SOA típusú paramétereket a zóna fájlból megnyílik *kivételével* a `host` paraméter. Ez a paraméter az Azure DNS által nyújtott értéket használja. Ennek oka az, ezt a paramétert az Azure DNS által nyújtott elsődleges névkiszolgálóra kell hivatkoznia.
* A névkiszolgáló-rekord zóna felső pontjánál beállítása szintén automatikusan létrejön az Azure DNS által a zóna létrehozásakor. Csak a beállított rekord az élettartam a rendszer importálja. Ezeket a rekordokat az Azure DNS által nyújtott névkiszolgálói neveket tartalmaz. Az importált zóna fájlban szereplő értékek nem felülírja a rekord adatait.
* A nyilvános előzetes verzióban az Azure DNS támogatja a txt típusú rekordok csak egyetlen-karakterlánc. Multistring TXT records are be concatenated and truncated to 255 characters.

### <a name="cli-format-and-values"></a>Parancssori felület formátuma és értékei

Az Azure CLI-paranccsal importálhatja a DNS-zóna formátuma:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Értékek:

* `<resource group>` az a zóna Azure DNS-ben az erőforráscsoport neve.
* `<zone name>` a zóna neve van.
* `<zone file name>` a zóna fájl importálni elérési útja és neve van.

Ha egy ilyen nevű zóna nem létezik az erőforráscsoportban, létrejön az Ön számára. Ha a zóna már létezik, az importált rekordhalmazok egyesítésekor a meglévő rekordhalmazt. 


### <a name="step-1-import-a-zone-file"></a>1. lépés A zónafájl importálása

A zóna egy zónafájl importálása **contoso.com**.

1. Ha Ön nem rendelkezik ilyennel, hozzon létre egy erőforrás-kezelő szeretné.

    ```azurecli
    az group create --group myresourcegroup -l westeurope
    ```

2. A zóna importálása **contoso.com** a fájlból **contoso.com.txt** be egy új DNS-zóna az erőforráscsoportban lévő **myresourcegroup**, a parancs futtatásakor `az network dns zone import` .<BR>Ez a parancs betölti a zóna fájlt, és elemzi azt. A parancs végrehajtása parancsokat a zóna létrehozása az Azure DNS szolgáltatásban, és minden rekordot a zónában állítja be. A parancs jelentések folyamatban van a konzolablakban, valamint az esetleges hibákat vagy figyelmeztetéseket. Rekordhalmazok sorozat jönnek létre, mert egy nagy zónafájl importálása néhány percet igénybe vehet.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>2. lépés A zóna ellenőrzése

Ellenőrizze a DNS-zóna, a fájl importálása után, az alábbi módszerek bármelyikét használhatja:

* A rekordok használatával a következő Azure CLI-paranccsal listázhatja:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* A PowerShell-parancsmag használatával listázhatja a rekordok `Get-AzureRmDnsRecordSet`.
* Használhat `nslookup` a rekordok névfeloldás ellenőrzése. Mivel a zóna még nem delegálta, adja meg a megfelelő Azure DNS névkiszolgálóit, explicit módon kell. A következő minta bemutatja, hogyan kérheti le a zóna névkiszolgálóinak neveit. Ez is bemutatja, hogyan kérdezhet le a "www" rekordhalmazhoz a `nslookup`.

    ```azurecli
    az network dns record-set ns list -g myresourcegroup -z  --output json 
    ```

    ```json
    [
      {
       .......
       "name": "@",
        "nsRecords": [
          {
            "additionalProperties": {},
            "nsdname": "ns1-03.azure-dns.com."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns2-03.azure-dns.net."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns3-03.azure-dns.org."
          },
          {
            "additionalProperties": {},
            "nsdname": "ns4-03.azure-dns.info."
          }
        ],
        "resourceGroup": "myresourcegroup",
        "ttl": 86400,
        "type": "Microsoft.Network/dnszones/NS"
      }
    ]
    ```

    ```cmd
    nslookup www.contoso.com ns1-03.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221
    ```

### <a name="step-3-update-dns-delegation"></a>3. lépés DNS-delegálás frissítése

Miután ellenőrizte, hogy helyesen importálta a zónában, a DNS-delegálás az Azure DNS névkiszolgálóit átirányítása frissíteni szeretné. További információkért tekintse meg a cikket [a DNS-delegálás frissítése](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Az Azure DNS DNS-zónafájl exportálása

Az Azure CLI-paranccsal importálhatja a DNS-zóna formátuma:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Értékek:

* `<resource group>` az a zóna Azure DNS-ben az erőforráscsoport neve.
* `<zone name>` a zóna neve van.
* `<zone file name>` a zóna fájl exportálható elérési útja és neve van.

A zóna importálás, először kell bejelentkezni, válassza ki az előfizetését, és az Azure CLI használata a Resource Manager módra való konfigurálása.

### <a name="to-export-a-zone-file"></a>Exportálja egy zóna

A meglévő Azure DNS-zóna exportálása **contoso.com** erőforráscsoportban **myresourcegroup** a fájl **contoso.com.txt** futtassa (az aktuális mappában), `azure network dns zone export`. Ez a parancs meghívja a rekordhalmazok zónában enumerálása, és exportálhatja az eredményeket egy zóna BIND-kompatibilis fájl, az Azure DNS szolgáltatásban.

    ```
    az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```
