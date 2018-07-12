---
title: DNS az Azure Stackben |} A Microsoft Docs
description: DNS-sel az Azure Stackben
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: mabrigg
ms.openlocfilehash: 8459a5f88bf660ac460f778f67618e9805afa29d
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970705"
---
# <a name="using-dns-in-azure-stack"></a>DNS-sel az Azure Stackben

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack a következő tartománynévrendszer (DNS) funkciókat támogatja:

* DNS-állomásnév feloldása
* DNS-zónák és -rekordjait az API létrehozására és kezelésére

## <a name="support-for-dns-hostname-resolution"></a>DNS-állomásnév feloldása támogatása

Megadhat egy DNS-tartománynév címkét a nyilvános IP-erőforrások. Használja az Azure Stack *domainnamelabel.location*. a címke nevét és a maps, hogy a nyilvános IP-cím az Azure Stackben cloudapp.azurestack.external felügyelt DNS-kiszolgálók.

Például, ha létrehoz egy nyilvános IP-erőforráshoz az **contoso** egy tartománynévcímkét a helyi Azure Stack-helyen, mint a [teljesen minősített tartománynevét](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN)  **contoso.local.cloudapp.azurestack.external** feloldja az erőforrás nyilvános IP-címét. Ez a teljes tartománynév használatával hozzon létre egy CNAME rekordot a nyilvános IP-címre az Azure Stackben, egyéni tartományt.

A névfeloldással kapcsolatos további tudnivalókért tekintse meg a [DNS-feloldás](https://docs.microsoft.com/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) cikk.

> [!IMPORTANT]
> Minden egyes tartománynév címkéje hoz létre az Azure Stack helyen belül egyedinek kell lennie.

A következő képernyő-rögzítési mutat be a **nyilvános IP-cím létrehozása** egy nyilvános IP-címet, a portál használatával létrehozására szolgáló párbeszédpanelen.

![Nyilvános IP-cím létrehozása](media/azure-stack-whats-new-dns/image01.png)

**Példaforgatókönyv**

Rendelkezik egy terheléselosztó-webalkalmazások kérelmek feldolgozásához. A terhelés mögött terheléselosztó egy vagy több virtual machines szolgáltatásban futó webhelyek. Az elosztott terhelésű webhely IP-cím helyett egy DNS-név használatával érheti el.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>DNS-zónák és -rekordjait az API létrehozása és kezelése

Hozzon létre, és kezelheti a DNS-zónák és -rekordok az Azure Stackben.

Az Azure Stack az Azure, például a DNS szolgáltatást nyújt, API-k konzisztensek legyenek az Azure DNS API-k használatával.  A tartományok Azure Stack DNS-ben üzemelteti, DNS-rekordjait a azonos hitelesítő adatokkal, API-k és eszközök használatával is kezelheti. Használja ugyanazt a számlázás és támogatják, mint a többi Azure-szolgáltatásokat is.

Az Azure Stack DNS-infrastruktúra képes legyen tömörebb, mint az Azure. A méret- és az Azure Stack központi DNS hatókör, méretezést és teljesítményt befolyásolja. Ez azt is jelenti, hogy teljesítmény, a rendelkezésre állási, a globális terjesztés és a magas rendelkezésre állású telepítésről a másikra változhat.

## <a name="comparison-with-azure-dns"></a>Az Azure DNS összehasonlítása

DNS az Azure DNS az Azure Stackben hasonlít, de jelentős kivételek ismernie kell.

* **Nem támogatja a AAAA típusú rekord**

    Az Azure Stack nepodporuje AAAA típusú rekord, mert az Azure Stack nem támogatja az IPv6-címek.  Ez a fő különbség a DNS az Azure-ban és az Azure Stackben.
* **Már nem több-bérlős**

    A DNS-szolgáltatás, az Azure Stackben ne legyen több-bérlős. Mindegyik bérlő ugyanazt a DNS-zóna nem hozható létre. Csak az első előfizetés, amely megpróbálja a zóna létrehozása sikeres volt, és a későbbi kérelmeket.  Ez nem egy ismert probléma, és a fő különbség az Azure és az Azure Stack DNS. A probléma feloldásra kerül egy későbbi kiadásban.
* **A címkék, metaadatokat és ETag**

    Hogyan kezeli az Azure Stack a címkéket, metaadatok, ETag és korlátokat kisebb különbségek vannak.

Azure DNS szolgáltatással kapcsolatos további tudnivalókért lásd: [DNS-zónák és rekordok](../../dns/dns-zones-records.md).

### <a name="tags-metadata-and-etags"></a>A címkék, metaadatokat és ETag

**Címkék**

Az Azure Stack DNS támogatja az Azure Resource Manager-címkék használatát a DNS-zóna erőforrás. Nem támogatja a címkék a DNS-rekordhalmazok, bár, alternatív "metaadatok" támogatja a DNS-rekord állítja be a következő mértékét.

**Metaadatok**

Rekordhalmaz címkék helyett az Azure Stack DNS támogatja a jegyzetkészítés rekordhalmazok "metaadatok" segítségével. Címkék hasonlóan, metaadatok lehetővé teszi minden rekordhalmaz név-érték párok társítani. Például ez lehet hasznos, jegyezze fel minden rekordhalmaz célját. Ellentétben a címkék és a metaadatok nem használható az Azure-elszámolások szűrt nézetét biztosít, és a egy Azure Resource Manager-szabályzatban nem adható meg.

**ETag**

Tegyük fel, hogy a két személynek vagy két folyamatok próbálja meg módosítani a DNS-rekord egy időben. Melyik wins? És a győztes nem tudja, hogy azok már módosításait mások által létrehozott felülírja?

Az Azure Stack DNS ETag ugyanarra az erőforrásra egyidejű módosításokat biztonságosan kezelésére használja. ETag nem azonosak az Azure Resource Manager "Címkék". Minden DNS-erőforrás (zóna vagy rekordhalmaz) egy hozzá társított ETag címkével rendelkezik. Minden alkalommal, amikor egy erőforrást a rendszer lekéri, az ETag címkéje is lekéri. Egy erőforrás frissítésekor kiválaszthatja adja át vissza az ETag címke, az Azure Stack DNS-ben is ellenőrizze, hogy az Etag a kiszolgáló megfelel. Minden frissítés egy erőforráshoz újragenerálása folyamatban van az Etag eredményez, mivel az Etag nem egyezik azt jelzi, egyidejű változás történt. ETag is segítségével új erőforrás létrehozásakor győződjön meg arról, hogy az erőforrás még nem létezik.

Alapértelmezés szerint az Azure Stack DNS PowerShell használatával ETag egyidejű letiltása a zónák és -rekordhalmazok. A választható *-felülírása* kapcsoló használható Etag ellenőrzések letiltásához, ebben az esetben minden egyidejű bekövetkezett változásokat a rendszer felülírja.

Az Azure Stack DNS REST API a szintjén ETag megadott HTTP-fejlécek használata. Viselkedésük van megadva a következő táblázatban:

| Fejléc | Viselkedés|
|--------|---------|
| None   | PUT mindig sikeres (nincs Etag-ellenőrzések)|
| IF-match| PUT csak akkor sikeres, ha az erőforrás létezik, és Etagje megegyezik|
| IF-match *| PUT csak akkor sikeres, ha az erőforrás létezik|
| IF-none-match *| PUT csak akkor sikeres, ha az erőforrás nem létezik.|

### <a name="limits"></a>Korlátok

Az alábbi alapértelmezett korlátok vonatkoznak az Azure Stack DNS használata esetén:

| Erőforrás| Alapértelmezett korlát|
|---------|--------------|
| Zónák előfizetésenként| 100|
| Zónánként rekordhalmazok| 5000|
| Rekordok száma rekordhalmaz| 20|

## <a name="next-steps"></a>További lépések

[IDN formátumú tartománynevek az Azure Stack bemutatása](azure-stack-understanding-dns.md)
