---
title: Az Azure-készletben DNS |} Microsoft Docs
description: Az Azure-készletben a DNS-sel
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
ms.openlocfilehash: 4e854a2751ce366e3ca3a353487f2c972401c248
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34196525"
---
# <a name="using-dns-in-azure-stack"></a>Az Azure-készletben a DNS-sel

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Azure verem támogatja a következő tartománynévrendszer (DNS) funkciókat:

* DNS-állomásnév feloldása
* DNS-zónák és az API-val rekordok létrehozására és kezelésére

## <a name="support-for-dns-hostname-resolution"></a>DNS-állomásnév feloldása támogatása

Megadhatja a DNS tartománynév-címke a nyilvános IP-erőforrások. Használja az Azure verem *domainnamelabel.location*. a címke nevét és azt a nyilvános IP-cím Azure verem maps cloudapp.azurestack.external felügyelt DNS-kiszolgálók.

Például, ha létrehoz egy nyilvános IP-erőforrás a **contoso** a tartománynév-címke a helyi Azure verem helyen, mint a [teljesen minősített tartománynevét](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN)  **contoso.local.cloudapp.azurestack.external** megkeresi a nyilvános IP-címet az erőforrás. Ez a teljes tartománynév használatával hozzon létre egy CNAME rekordot a nyilvános IP-cím Azure verem mutató egyéni tartományt.

A névfeloldás kapcsolatos további tudnivalókért tekintse meg a [DNS-feloldás](https://docs.microsoft.com/en-us/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) cikk.

> [!IMPORTANT]
> Minden egyes tartománynév-címke létrehozása az Azure-verem helyére belül egyedinek kell lennie.

A következő képernyő-rögzítési mutat be a **nyilvános IP-cím létrehozása** párbeszédpanel létrehozásához egy nyilvános IP-címet, a portál használatával.

![Nyilvános IP-cím létrehozása](media/azure-stack-whats-new-dns/image01.png)

**Példa**

Lehetősége van a terheléselosztó-webalkalmazások kérelmek feldolgozásához. A terhelés mögött terheléselosztó egy vagy több virtuális gépeken futó webhely. Az elosztott terhelésű webhely helyett IP-címet a DNS-név használatával érheti el.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>DNS-zónák és az API-rekordok létrehozása és kezelése

Hozzon létre, és a DNS-zónák és Azure verem rekordok kezelése.

Azure verem Azure, például egy DNS szolgáltatást biztosít az Azure DNS API-k konzisztensek API-k használatával.  Az Azure verem DNS-tartományt üzemeltet, kezelheti a DNS-rekordokat a ugyanazon hitelesítő adatokkal, API-k és eszközök használatával. Is használhat ugyanazon számlázási és az egyéb Azure-szolgáltatások támogatják.

Az Azure verem DNS-infrastruktúra tömörebb Azure-nál. A méretét és az Azure-verem üzembe helyezésének helyét érinti, DNS-hatókör, a méretezés és teljesítmény. Ez azt is jelenti, hogy teljesítmény, a rendelkezésre állási, a globális terjesztési és a magas rendelkezésre állású telepítésről a másikra változhat.

## <a name="comparison-with-azure-dns"></a>Az Azure DNS összehasonlítása

Azure verem DNS hasonló, a DNS-ben az Azure-ban, de jelentős kivételek ismernie kell.

* **Nem támogatja a AAAA-rekord**

    Azure verem nem támogatja a AAAA típusú rekordot, mert az Azure verem nem támogatja az IPv6-címeket.  Ez az DNS Azure és az Azure-verem kulcs eltérése.
* **Nincs több-bérlős**

    A DNS-szolgáltatás Azure verem nem több-bérlős. Mindegyik bérlő nem hozható létre a azonos DNS-zónát. Csak az első előfizetés, amely megpróbálja a zóna létrehozása sikeres, és ezt követő kérelem sikertelen lesz.  Ez nem egy ismert probléma, és Azure és az Azure DNS-verem kulcs eltérése. A probléma feloldásra kerül egy későbbi kiadásban.
* **Címkék, a metaadatok és az ETag-EK**

    Hogyan kezeli az Azure verem a címkék, a metaadatok, az ETag-EK és a korlátok kisebb különbségek vannak.

Azure DNS szolgáltatással kapcsolatos további tudnivalókért lásd: [DNS-zónák, és rögzíti](../../dns/dns-zones-records.md).

### <a name="tags-metadata-and-etags"></a>Címkék, a metaadatok és az ETag-EK

**Címkék**

Az Azure verem DNS támogatja az Azure Resource Manager címkék használatával a DNS-zóna erőforrás. Nem támogatja címkék a DNS-rekordhalmazok, bár a "metadata" alternatív támogatja a DNS-rekord állítja be a következő leírtak szerint.

**Metaadatok**

Rekordhalmaz címkék alternatívájaként Azure verem DNS-ben támogatja a ellátása megjegyzésekkel rekordhalmazok "metadata" használatával. Címkék hasonló, metaadatok lehetővé teszi minden rekordhalmaz név-érték párok társítani. Például ez lehet hasznos minden rekordhalmaz céljának rögzítéséhez. Címkék, ellentétben a metaadatok nem használható az Azure számlázásának szűrt megjelenítésére szolgáló, és nem adható meg az Azure Resource Manager-házirend.

**ETag-EK**

Tegyük fel, hogy a két személynek vagy két folyamatok próbálja módosítani a DNS-rekord egy időben. Melyik wins? És a győztes nem tudja, hogy azok már felül más által létrehozott módosításokat?

Az Azure verem DNS ETag-EK ugyanahhoz a erőforráshoz egyidejű változtatások biztonságosan kezelésére használja. ETag-EK az Azure Resource Manager "Címke" külön. Minden DNS-erőforrásrekordok (zóna vagy rekordhalmaz) rendelkezik egy ETag-gel társítva. Erőforrás lekérése, amikor a rendszer is lekéri az Etag. Egy erőforrás frissítésekor kiválaszthatja továbbítására vissza az Etag, Azure verem DNS is ellenőrizze, hogy az Etag a kiszolgáló megfelel. Minden egyes frissítés egy erőforráshoz eredményez a helyreállítás alatt Etag, mert az egy Etag nem egyezik azt jelzi, egyidejű módosítás történt. ETag-EK is használhatja új erőforrás létrehozása annak biztosítására, hogy az erőforrás nem létezik.

Alapértelmezés szerint Azure verem DNS PowerShell zónák egyidejű módosításai és rekordkészletek ETag-EK használja. A választható *-felülírási* kapcsoló Etag ellenőrzések letiltásához használható, amely esetben minden egyidejű végrehajtott módosításokat a rendszer felülírja.

Az Azure verem DNS REST API szintjén ETag-EK megadott HTTP-fejlécek használatával. A következő táblázatban azok viselkedését adja:

| Fejléc | Viselkedés|
|--------|---------|
| None   | A PUT mindig sikeres (nincs Etag ellenőrzése)|
| IF-match| PUT csak akkor sikeres, ha erőforrás létezik, és az Etag megfelel|
| IF-match *| A PUT csak akkor sikeres, ha erőforrás létezik-e|
| IF-none-match *| PUT csak akkor sikeres, ha az erőforrás nem létezik.|

### <a name="limits"></a>Korlátok

A következő alapértelmezett korlátokat alkalmazza, ha Azure verem DNS-sel:

| Erőforrás| Alapértelmezett korlát|
|---------|--------------|
| Zónák előfizetésenként| 100|
| Egyes zónák rekordhalmazok| 5000|
| Rekordok száma rekordkészlete| 20|

## <a name="next-steps"></a>További lépések

[Azure verem IDN bemutatása](azure-stack-understanding-dns.md)
