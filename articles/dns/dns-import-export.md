---
title: Tartományzóna-fájl importálása és exportálása – Azure CLI
titleSuffix: Azure DNS
description: Megtudhatja, hogy miként importálhat és exportálhat DNS-zónafájlt az Azure DNS-be az Azure CLI használatával
services: dns
author: rohinkoul
ms.service: dns
ms.date: 4/3/2019
ms.author: rohink
ms.topic: conceptual
ms.openlocfilehash: a5c2fdde564eba2d95e7f14f4d47e4d381739d5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365168"
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli"></a>DNS-zónafájl importálása és exportálása az Azure CLI-vel

Ez a cikk bemutatja, hogyan importálhatja és exportálhatja a DNS-zónafájlokat az Azure DNS-hez az Azure CLI használatával.

## <a name="introduction-to-dns-zone-migration"></a>Bevezetés a DNS-zónák áttelepítésének bevezetésébe

A DNS-zónafájl olyan szövegfájl, amely a zónában található összes DNS-rekord részleteit tartalmazza. Ez egy szabványos formátumot követ, így alkalmas a DNS-rekordok DNS-rendszerek közötti átvitelére. A zónafájl használata gyors, megbízható és kényelmes módja a DNS-zóna azure DNS-be vagy azure DNS-ből való átvitelének.

Az Azure DNS támogatja a zónafájlok importálását és exportálását az Azure parancssori felületén (CLI) keresztül. A zónafájl-importálás jelenleg **nem** támogatott az Azure PowerShell vagy az Azure Portalon keresztül.

Az Azure CLI egy platformfüggetlen parancssori eszköz, amely az Azure-szolgáltatások kezelésére szolgál. Windows, Mac és Linux platformokon érhető el az [Azure letöltések lapjáról.](https://azure.microsoft.com/downloads/) A platformfüggetlen támogatás fontos a zónafájlok importálásához és exportálásához, mivel a leggyakoribb névkiszolgálói szoftver, a [BIND](https://www.isc.org/downloads/bind/)általában Linuxon fut.

## <a name="obtain-your-existing-dns-zone-file"></a>A meglévő DNS-zónafájl beszerzése

Mielőtt importálna egy DNS-zónafájlt az Azure DNS-be, be kell szereznie a zónafájl másolatát. A fájl forrása attól függ, hogy a DNS-zóna jelenleg hol van tárolva.

* Ha a DNS-zónát egy partnerszolgáltatás (például tartományregisztráló, dedikált DNS-szolgáltató vagy alternatív felhőszolgáltató) üzemelteti, a szolgáltatásnak lehetővé kell tennie a DNS-zónafájl letöltését.
* Ha a DNS-zóna Windows DNS-en található, a zónafájlok alapértelmezett mappája a **%systemroot%\system32\dns**. Az egyes zónafájlok teljes elérési útja a DNS-konzol **Általános** lapján is megjelenik.
* Ha a DNS-zónát a BIND használatával üzemelteti, a zónafájl helye az egyes zónákhoz a **BIND nevű**BIND konfigurációs fájlban van megadva.

## <a name="import-a-dns-zone-file-into-azure-dns"></a>DNS-zónafájl importálása az Azure DNS-be

Zónafájl importálása új zónát hoz létre az Azure DNS-ben, ha még nem létezik. Ha a zóna már létezik, a zónafájlban lévő rekordkészleteket egyesíteni kell a meglévő rekordkészletekkel.

### <a name="merge-behavior"></a>Egyesítési viselkedés

* Alapértelmezés szerint a meglévő és az új rekordkészletek egyesülnek. Az egyesített rekordhalmazon belül a rekordok duplikáltak.
* A rekordhalmazok egyesítésekor a már meglévő rekordsorozatok élő ideje (TTL).
* A SoA(- hitelesítés) paramétereinek kezdete (kivéve) `host`mindig az importált zónafájlból származik. Hasonlóképpen, a zóna csúcsán beállított névkiszolgálói rekord esetében a TTL mindig az importált zónafájlból származik.
* Az importált CNAME rekord nem helyettesíti a meglévő CNAME rekordot ugyanazzal a névvel.  
* Ha ütközés keletkezik egy CNAME rekord és egy másik, azonos nevű, de eltérő típusú rekord között (függetlenül attól, hogy melyik létezik vagy új), a meglévő rekord megmarad. 

### <a name="additional-information-about-importing"></a>További információ az importálásról

A következő megjegyzések további technikai részleteket tartalmaznak a zóna importálási folyamatáról.

* Az `$TTL` irányelv nem kötelező, és támogatott. Ha `$TTL` nincs utasítás megadva, a kifejezett TTL nélküli rekordok importálása 3600 másodperces alapértelmezett TTL értékre van állítva. Ha ugyanabban a rekordkészletben két rekord különböző TTL-eket ad meg, a függvény az alacsonyabb értéket használja.
* Az `$ORIGIN` irányelv nem kötelező, és támogatott. Ha `$ORIGIN` nincs beállítva, a parancssorban megadott zónanév (plusz a végződő ".").
* A `$INCLUDE` `$GENERATE` és az irányelvek nem támogatottak.
* Ezek a bejegyzéstípusok támogatottak: A, AAAA, CAA, CNAME, MX, NS, SOA, SRV és TXT.
* A SOA-rekordot az Azure DNS automatikusan létrehozza, amikor egy zónát hoz létre. Zónafájl importálásakor a rendszer a paraméter *kivételével* a `host` zónafájlból veszi az összes SOA paramétert. Ez a paraméter az Azure DNS által megadott értéket használja. Ennek az az oka, hogy ennek a paraméternek az Azure DNS által biztosított elsődleges névkiszolgálóra kell hivatkoznia.
* A zóna csúcsán beállított névkiszolgálói rekordot az Azure DNS is automatikusan hozza létre a zóna létrehozásakor. A program csak a rekordhalmaz TTL-ét importálja. Ezek a rekordok tartalmazzák az Azure DNS által biztosított névkiszolgálóneveket. A rekordadatokat nem írják felül az importált zónafájlban lévő értékek.
* A nyilvános előzetes verzió során az Azure DNS csak egykarakterláncos TXT-rekordokat támogat. A többsoros TXT rekordok összefűzése és 255 karakterre csonkolása.

### <a name="cli-format-and-values"></a>CLI formátum és értékek

A DNS-zóna importálására az Azure CLI parancs formátuma a következő:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Értékek:

* `<resource group>`Az Azure DNS-ben a zóna erőforráscsoportjának neve.
* `<zone name>`a zóna neve.
* `<zone file name>`Az importálandó zónafájl elérési útja/neve.

Ha ilyen nevű zóna nem létezik az erőforráscsoportban, akkor az Ön számára jön létre. Ha a zóna már létezik, az importált rekordkészletek egyesülnek a meglévő rekordkészletekkel. 

### <a name="step-1-import-a-zone-file"></a>1. lépés Zónafájl importálása

A **zónához**contoso.com zónafájl importálása.

1. Ha még nem rendelkezik ilyen, létre kell hoznia egy Erőforrás-kezelő erőforráscsoportot.

    ```azurecli
    az group create --group myresourcegroup -l westeurope
    ```

2. Ha a zóna **contoso.com** importálja a **contoso.com.txt** fájlból egy új DNS-zónába `az network dns zone import`a **myresourcegroup**erőforráscsoportban, futtassa a parancsot .<BR>Ez a parancs betölti a zónafájlt, és elemzi azt. A parancs parancsokat hajt végre az Azure DNS-szolgáltatása a zóna és a zóna összes rekordkészletének létrehozásához. A parancs a konzolablakban lévő előrehaladást, valamint az esetleges hibákat és figyelmeztetéseket jelenti. Mivel a rekordkészletek sorozatban jönnek létre, a nagy zónafájlok importálása eltarthat néhány percig.

    ```azurecli
    az network dns zone import -g myresourcegroup -n contoso.com -f contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>2. lépés A zóna ellenőrzése

A DNS-zóna ellenőrzéséhez a fájl importálása után az alábbi módszerek bármelyikét használhatja:

* A rekordokat a következő Azure CLI paranccsal listázhatja:

    ```azurecli
    az network dns record-set list -g myresourcegroup -z contoso.com
    ```

* A rekordokat az Azure CLI `az network dns record-set ns list`paranccsal is felsorolhatja.
* A rekordok `nslookup` névfeloldásának ellenőrzésére használható. Mivel a zóna még nincs delegálva, explicit módon meg kell adnia a megfelelő Azure DNS-névkiszolgálókat. A következő minta bemutatja, hogyan lehet beolvasni a zónához rendelt névkiszolgálóneveket. Ez azt is bemutatja, hogyan lehet `nslookup`lekérdezni a "www" rekordot a használatával.

    ```azurecli
    az network dns record-set ns list -g myresourcegroup -z contoso.com  --output json 
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

Miután meggyőződött arról, hogy a zóna megfelelően lett importálva, frissítenie kell a DNS-delegálást, hogy az Azure DNS-névkiszolgálóira mutasson. További információt a [DNS-delegálás frissítése](dns-domain-delegation.md)című témakörben talál.

## <a name="export-a-dns-zone-file-from-azure-dns"></a>DNS-zónafájl exportálása az Azure DNS-ből

A DNS-zóna exportálására az Azure CLI parancs formátuma a következő:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Értékek:

* `<resource group>`Az Azure DNS-ben a zóna erőforráscsoportjának neve.
* `<zone name>`a zóna neve.
* `<zone file name>`Az exportálandó zónafájl elérési útja/neve.

A zónaimportáláshoz ugyanúgy, először is be kell jelentkeznie, ki kell választania az előfizetést, és konfigurálnia kell az Azure CLI-t az Erőforrás-kezelő mód használatához.

### <a name="to-export-a-zone-file"></a>Zónafájl exportálása

Ha a meglévő Azure DNS-zóna **contoso.com** exportálni a **myresourcegroup** erőforráscsoportban lévő erőforráscsoportban a **contoso.com.txt** fájlba (az aktuális mappában) futtassa a parancsot. `azure network dns zone export` Ez a parancs meghívja az Azure DNS-szolgáltatást a zónában lévő rekordhalmazok számbavételére, és exportálja az eredményeket egy BIND-kompatibilis zónafájlba.

```azurecli
az network dns zone export -g myresourcegroup -n contoso.com -f contoso.com.txt
```

## <a name="next-steps"></a>További lépések

* További információ a dns-zónában lévő [rekordkészletek és rekordok kezeléséről.](dns-getstarted-create-recordset-cli.md)

* Ismerje meg, hogyan [delegálhatja tartományát az Azure DNS-re.](dns-domain-delegation.md)
