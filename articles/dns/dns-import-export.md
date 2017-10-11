---
title: "Importálni és exportálni egy tartományi zóna fájlt az Azure DNS-Azure CLI 1.0 használatával |} Microsoft Docs"
description: "Megtudhatja, hogyan importálhat és exportálhat egy DNS-zónafájlját az Azure DNS-Azure CLI 1.0 használatával"
services: dns
documentationcenter: na
author: georgewallace
manager: timlt
ms.assetid: f5797782-3005-4663-a488-ac0089809010
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2016
ms.author: gwallace
ms.openlocfilehash: d6d3fa7aa0e8b2462b3a6b4b66d3d87ab5535314
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="import-and-export-a-dns-zone-file-using-the-azure-cli-10"></a>Importálni és exportálni egy DNS-zónafájlját az Azure CLI 1.0 használatával 

Ez a cikk bemutatja, hogyan importálása és exportálása a DNS-zóna fájlok az Azure DNS az Azure CLI 1.0 használatával.

## <a name="introduction-to-dns-zone-migration"></a>DNS-zóna áttelepítés

A DNS-zónafájlját egy szövegfájlt, amely minden tartománynévrendszer (DNS) rekordot a zónában részleteit tartalmazza. Ez azt jelenti, szabványos formátumban, így a megfelelő DNS-rekordok átvitele a DNS-rendszerek között. Zóna fájllal módja a gyors, megbízható és kényelmes átvitele egy DNS-zónát, vagy abból az Azure DNS-ben.

Az Azure DNS támogatja, importálása és exportálása a zóna fájlok az Azure parancssori felület (CLI) használatával. Zóna fájl importálás **nem** jelenleg támogatott Azure PowerShell vagy az Azure-portálon keresztül.

Az Azure CLI 1.0 Azure-szolgáltatások kezelésére használt platformfüggetlen parancssori eszközzel. A Windows, Mac és Linux rendszerek érhető el a [Azure letöltési oldalon](https://azure.microsoft.com/downloads/). Többplatformos támogatást fontos történő importálására és exportálására zóna fájlokat, mert a leggyakrabban használt név kiszolgálószoftver [kötési](https://www.isc.org/downloads/bind/), jellemzően futó Linux.

> [!NOTE]
> Jelenleg az Azure parancssori felület két verziója. CLI1.0 Node.js alapul, és rendelkezik az "azure" kezdve parancsok.
> CLI2.0 "az" kezdve parancsok rendelkezik, és a Python alapul. Zóna fájl importálása verziójával is támogatott, amíg azt javasoljuk, CLI1.0 parancsok ezen a lapon ismertetett módon.

## <a name="obtain-your-existing-dns-zone-file"></a>A meglévő DNS-zónafájlját beszerzése

Mielőtt egy DNS-zónafájlját az Azure DNS importálja, kell hozzájutni a zóna fájlt. A fájl forrásában attól függ, amelyben a DNS-zóna jelenleg üzemel.

* Ha a DNS-zóna (például a tartományregisztráló, dedikált DNS-szolgáltató vagy alternatív felhőszolgáltatóként) egy partner szolgáltatás működteti, a service töltse le a DNS-zónafájlját lehetőséget kell biztosítania.
* Ha a DNS-zónát a Windows DNS-üzemelteti, az alapértelmezett mappa, a zóna fájlok: **%systemroot%\system32\dns**. A fájl teljes elérési útját minden zóna is jeleníti meg a **általános** lapon, a DNS-konzol.
* Ha a DNS-zóna BIND használatával, a hely a zónák zónafájl meg van adva a BIND konfigurációs fájl **named.conf**.

> [!NOTE]
> GoDaddy letöltésének zóna fájlok formátuma némileg nem szabványos. A probléma megoldására ezeket a fájlokat zóna Azure DNS-ben való importálása előtt kell.
>
> Az RDATA minden DNS-rekord a DNS-nevek teljesen minősített nevek vannak megadva, de nem rendelkeznek a záró "." Ez azt jelenti, hogy azok más relatív nevek DNS rendszer értelmezi. Módosítania kell a zóna fájl hozzáfűzése a záró "." nevüket előtt az Azure DNS importálja azokat.
>
> Például a CNAME rekord "www 3600 CNAME contoso.com" módosítani kell a "www 3600 CNAME contoso.com."
> (az a záró ".").

## <a name="import-a-dns-zone-file-into-azure-dns"></a>Egy DNS-zóna fájlt importálja az Azure DNS

Új zóna az Azure DNS-zóna fájlok importálása hoz, ha egy nem létezik. Ha a zóna már létezik, a rekordhalmazok a(z) egyesíthető kell a meglévő rekordhalmazok.

### <a name="merge-behavior"></a>Viselkedés egyesítése

* Alapértelmezés szerint a meglévő és új rekordhalmazok egyesítődnek. Egyesített rekordhalmaz belüli azonos rekordokat deszerializálni duplikált.
* Másik lehetőségként megadásával a `--force` beállítás, az importálási folyamat cserél meglévő rekordhalmazok az új rekordhalmazok. Meglévő rekordhalmazok, amely nem rendelkezik egy megfelelő rekordot a zóna importált fájl nem lesznek eltávolítva.
* Rekordhalmazok egyesítésekor a rendszer mennyi ideig élettartam (TTL) elérésű, korábban létező rekordkészletek szolgál. Ha `--force` van használt, az az új rekord készlet élettartam.
* Kezdő hatóság (SOA) paraméterek (kivéve `host`) az importált zóna fájlból, függetlenül attól, hogy mindig készít `--force` szolgál. Ehhez hasonlóan a névkiszolgáló-rekord a zóna felső pontja beállított, az a TTL-t mindig használatban van az importált zóna fájlból.
* Az importált CNAME rekord nem helyettesíti az meglévő CNAME rekord ugyanazzal a névvel kivéve, ha a `--force` paraméter meg van adva.
* Ha egy olyan CNAME rekordot, és egy másik rekord azonos, de eltérő típusú (függetlenül attól, amely meglévő vagy új) közötti ütközés lép fel, a meglévő rekord marad. Ez az független alkalmazásának `--force`.

### <a name="additional-information-about-importing"></a>További információt a importálása

Az alábbi megjegyzések adja meg, a zóna további technikai részleteiért importálási folyamat.

* A `$TTL` direktíva nem kötelező, és támogatja azt. Ha nem `$TTL` irányelv kap, a rekordok egy explicit TTL nélkül olyan importált értékre állítva egy alapértelmezett élettartam 3600 másodperc. Ha két rekordok rekord ugyanazokat a különböző TTLs adja meg, az alacsonyabb értéket használja.
* A `$ORIGIN` direktíva nem kötelező, és támogatja azt. Ha nem `$ORIGIN` beállítva, a használt alapértelmezett érték megadva a parancssorban a zóna nevét (és a záró ".").
* A `$INCLUDE` és `$GENERATE` nem támogatott.
* Ezek a rekordok típusok támogatottak: A, AAAA, CNAME, MX, NS, SOA, SRV és TXT.
* A SOA-rekord automatikusan létrejön Azure DNS-zóna létrehozásakor. Egy zóna fájl importálásakor összes SOA paraméter készít a zóna fájlból *kivéve* a `host` paraméter. Ez a paraméter az Azure DNS által nyújtott értéket használja. Ennek az az oka ennek a paraméternek az Azure DNS által nyújtott elsődleges névkiszolgálóra kell hivatkoznia.
* A névkiszolgáló-rekord a zóna felső pontja beállított is automatikusan hozza létre Azure DNS-ben a zóna létrehozásakor. A rekordhalmaz csak a Élettartammal importálva van. Ezeket a rekordokat az Azure DNS által nyújtott névkiszolgálói neveket tartalmaz. A rekord adatait nem írják az importált zónafájl szereplő értékeket.
* Nyilvános előzetes Azure DNS TXT rekordok csak egyetlen-karakterlánc támogatja. Karakterláncsoros TXT rekord összefűzendő és csonkolja a 255 karakter hosszú lehet.

### <a name="cli-format-and-values"></a>Parancssori felület formátuma és értékei

A DNS-zónák importálására szolgáló Azure CLI parancs formátuma:

```azurecli
azure network dns zone import [options] <resource group> <zone name> <zone file name>
```

Értékek:

* `<resource group>`az Azure DNS-zóna az erőforráscsoport neve.
* `<zone name>`a zóna neve van.
* `<zone file name>`a zóna fájl, importálandók/útvonalnév van.

Ha egy zónát a név nem létezik az erőforráscsoporthoz tartozik, az Ön létrejön. Ha a zóna már létezik, az importált rekordhalmazok egyesítve lesznek az meglévő rekordhalmazok. Felülírja a meglévő rekordkészleteket, használja a `--force` lehetőséget.

A zóna formátuma ténylegesen importálása nélkül, amellyel a `--parse-only` lehetőséget.

### <a name="step-1-import-a-zone-file"></a>1. lépés Zóna fájl importálása

A zóna zóna fájl importálásához **contoso.com**.

1. Jelentkezzen be az Azure-előfizetéshez az Azure CLI 1.0 használatával.

    ```azurecli
    azure login
    ```

2. Válassza ki az előfizetést, ha szeretne létrehozni az új DNS-zónát.

    ```azurecli
    azure account set <subscription name>
    ```

3. Az Azure DNS egy olyan Azure csak erőforrás-kezelő szolgáltatás, így a Resource Manager módra kell állítani az Azure parancssori felület.

    ```azurecli
    azure config mode arm
    ```

4. Az Azure DNS-szolgáltatás használata előtt regisztrálnia kell az előfizetése használja a Microsoft.Network erőforrás-szolgáltató. (Ez a műveletet egyszer kell elvégezni az egyes előfizetésekhez.)

    ```azurecli
    azure provider register Microsoft.Network
    ```

5. Ha egy már nem rendelkezik, akkor is meg kell erőforrás-kezelő erőforráscsoport létrehozása.

    ```azurecli
    azure group create myresourcegroup westeurope
    ```

6. A zóna importálandó **contoso.com** fájlból **contoso.com.txt** be egy új DNS-zóna erőforráscsoportban **myresourcegroup**, futtassa a parancsot `azure network dns zone import`.<BR>Ez a parancs a zóna fájlt, és elemzéséhez. A parancs a zóna létrehozása az Azure DNS szolgáltatásban parancsokat végrehajtása során, és minden rekordot a zónában állítja be. A parancs jelentések folyamatban van a konzolablakban, továbbá az esetleges hibákat vagy figyelmeztetéseket. Rekordhalmazok sorozat jönnek létre, mert nagy zóna fájl importálása néhány percig is tarthat.

    ```azurecli
    azure network dns zone import myresourcegroup contoso.com contoso.com.txt
    ```

### <a name="step-2-verify-the-zone"></a>2. lépés Ellenőrizze a zóna

A fájl importálása után a DNS-zóna ellenőrzéséhez használhatja a következő módszerek egyikét sem:

* A rekord a következő Azure CLI-parancs segítségével jeleníthetők meg:

    ```azurecli
    azure network dns record-set list myresourcegroup contoso.com
    ```

* A PowerShell-parancsmag segítségével listázhatja a rekordok `Get-AzureRmDnsRecordSet`.
* Használhat `nslookup` névfeloldás a rekordok ellenőrzése. Mivel a zóna még nincs delegálása után kell explicit módon adja meg a megfelelő Azure DNS névkiszolgálóit. A következő minta bemutatja, hogyan lehet lekérni a zóna névkiszolgálóinak neveit. Informatikai is bemutatja, hogyan lekérdezni a "www" rekordhalmazhoz használatával `nslookup`.

        C:\>azure network dns record-set show myresourcegroup contoso.com @ NS
        info:Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
        data:Id: /subscriptions/.../resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
        data:Name: @
        data:Type: Microsoft.Network/dnszones/NS
        data:Location: global
        data:TTL : 3600
        data:NS records
        data:Name server domain name : ns1-01.azure-dns.com
        data:Name server domain name : ns2-01.azure-dns.net
        data:Name server domain name : ns3-01.azure-dns.org
        data:Name server domain name : ns4-01.azure-dns.info
        data:
        info:network dns record-set show command OK

        C:\> nslookup www.contoso.com ns1-01.azure-dns.com

        Server: ns1-01.azure-dns.com
        Address:  40.90.4.1

        Name:www.contoso.com
        Addresses:  134.170.185.46
        134.170.188.221

### <a name="step-3-update-dns-delegation"></a>3. lépés DNS-delegálás frissítése

Miután ellenőrizte, hogy a zóna megfelelően importálták-e, kell, hogy az Azure DNS névkiszolgálóit mutasson a DNS-delegálás frissítése. További információkért lásd: a cikk [a DNS-delegálás frissítése](dns-domain-delegation.md).

## <a name="export-a-dns-zone-file-from-azure-dns"></a>Exportálja egy DNS-zónafájlját az Azure DNS-ben

A DNS-zónák importálására szolgáló Azure CLI parancs formátuma:

```azurecli
azure network dns zone export [options] <resource group> <zone name> <zone file name>
```

Értékek:

* `<resource group>`az Azure DNS-zóna az erőforráscsoport neve.
* `<zone name>`a zóna neve van.
* `<zone file name>`a zóna fájl exportálását/útvonalnév van.

A zóna az importáláshoz, akkor először kell bejelentkezni, válassza ki az előfizetés, és konfigurálása az Azure parancssori felület használata a Resource Manager módra.

### <a name="to-export-a-zone-file"></a>Egy zóna fájl exportálása

1. Jelentkezzen be az Azure-előfizetéshez az Azure parancssori felület használatával.

    ```azurecli
    azure login
    ```

2. Válassza ki az előfizetést, ha szeretne létrehozni a DNS-zónát.

    ```azurecli
    azure account set <subscription name>
    ```

3. Az Azure DNS egy olyan Azure csak erőforrás-kezelő szolgáltatás. Az Azure parancssori felület Resource Manager módra kell állítani.

    ```azurecli
    azure config mode arm
    ```

4. A meglévő Azure DNS-zóna exportálása **contoso.com** erőforráscsoportban **myresourcegroup** fájl **contoso.com.txt** (az aktuális mappában), futtassa `azure network dns zone export`. Ez a parancs meghívja az Azure DNS szolgáltatást a zónában rekordhalmazok enumerálása, és a BIND-kompatibilis zóna fájlba exportálhatja az eredményeket.

    ```azurecli
    azure network dns zone export myresourcegroup contoso.com contoso.com.txt
    ```
