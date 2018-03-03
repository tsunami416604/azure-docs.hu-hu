---
title: "Az Azure-készletben DNS |} Microsoft Docs"
description: DNS az Azure Stackben
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 394abe5295af4ed99e48d50b5886ac93af87e875
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="dns-in-azure-stack"></a>DNS az Azure Stackben

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az Azure verem az alábbi DNS-szolgáltatásokat tartalmazza:
* DNS-állomásnév feloldása támogatása
* DNS-zónák és API-rekordok létrehozása és kezelése

## <a name="support-for-dns-hostname-resolution"></a>DNS-állomásnév feloldása támogatása
Megadhatja, hogy a DNS tartománynév-címke a nyilvános IP-erőforráshoz, létrehoz egy leképezést *domainnamelabel.location*. a nyilvános IP-cím az Azure-készletben cloudapp.azurestack.external felügyelt DNS-kiszolgálók.  

Ha például létrehozhat egy nyilvános IP-erőforrás a **contoso** , a tartománynév-címke a helyi Azure verem helyen, a teljesen minősített tartománynevét (FQDN) **contoso.local.cloudapp.azurestack.external**megkeresi a nyilvános IP-címet az erőforrás. Ez a teljes tartománynév használatával hozzon létre egy CNAME rekordot a nyilvános IP-cím Azure verem mutató egyéni tartományt.

> [!IMPORTANT]
> Minden egyes tartománynév-címke létrehozása az Azure-verem helyére belül egyedinek kell lennie.

Ha a nyilvános IP-cím, a portál használatával hoz létre, néz ki:

![Nyilvános IP-cím létrehozása](media/azure-stack-whats-new-dns/image01.png)

Ez a konfiguráció akkor hasznos, ha egy elosztott terhelésű erőforrás egy nyilvános IP-címet hozzárendelni kívánt. Például lehetséges, hogy a terheléselosztó-webalkalmazások kérelmek feldolgozásához. A terhelés mögött terheléselosztó egy webhelyen található egy vagy több virtuális gépeken. Az elosztott terhelésű webhely most már megtekintheti a DNS-név, nem pedig egy IP-cím alapján.

## <a name="create-and-manage-dns-zones-and-records-using-api"></a>DNS-zónák és API-rekordok létrehozása és kezelése
Hozzon létre, és a DNS-zónák és Azure verem rekordok kezelése.  

Azure verem Azure, például egy DNS szolgáltatást biztosít az Azure DNS API-k konzisztensek API-k használatával.  Azure verem DNS tartományt üzemeltet, kezelheti az egyéb Azure-szolgáltatások, a DNS-rekordokat a ugyanazokat a hitelesítő adatokat, API-k, eszközök, számlázási és támogatási szolgálathoz. 

Nyilvánvaló okokból Azure verem DNS-infrastruktúrájának tömörebb Azure-nál. Ebből kifolyólag a hatókör, méretezés és teljesítmény függ az Azure Alkalmazásveremben üzembe és a környezetben, ahol központilag telepítették.  Igen többek között a teljesítményt, a rendelkezésre állási, a globális terjesztési és a magas rendelkezésre állású (HA) eltérőek lehetnek telepítésről a másikra.

## <a name="comparison-with-azure-dns"></a>Az Azure DNS összehasonlítása
Azure-készletben DNS hasonlít az Azure DNS két fő kivételekkel:
* **Nem támogatja a AAAA-rekord**

    Azure verem nem támogatja a AAAA típusú rekordot, mert az Azure verem nem támogatja az IPv6-címeket.  Ez az DNS Azure és az Azure-verem kulcs eltérése.
* **Nincs több-bérlős**

    Eltérően Azure, a DNS szolgáltatás Azure verem nincs több-bérlős. Így mindegyik bérlő nem hozható létre a azonos DNS-zónát. Csak az első előfizetés, amely megpróbálja a zóna létrehozása sikeres, és ezt követő kérelem sikertelen lesz.  Ez nem egy ismert probléma, és Azure és az Azure DNS-verem kulcs eltérése. A probléma feloldásra kerül egy későbbi kiadásban.

Emellett néhány hogyan Azure verem DNS megvalósítja-e a címkék, a metaadatok, az ETag-EK és a korlátok kisebb különbségek vannak.

Az alábbi információk Azure verem DNS vonatkozik, és kis mértékben eltér az Azure DNS-ben. Azure DNS szolgáltatással kapcsolatos további tudnivalókért lásd: [DNS-zónák, és rögzíti](../../dns/dns-zones-records.md) a Microsoft Azure dokumentációjában található.

### <a name="tags-metadata-and-etags"></a>Címkék, a metaadatok és az ETag-EK

**Címkék**

Az Azure verem DNS támogatja az Azure Resource Manager címkék használatával a DNS-zóna erőforrás. Nem támogatja címkék a DNS-rekordhalmazok, bár a "metadata" alternatív támogatja a DNS-rekord állítja be a következő leírtak szerint.

**Metadata**

Rekordhalmaz címkék alternatívájaként Azure verem DNS-ben támogatja a ellátása megjegyzésekkel rekordhalmazok "metadata" használatával. Címkék hasonló, metaadatok lehetővé teszi minden rekordhalmaz név-érték párok társítani. Például ez lehet hasznos minden rekordhalmaz céljának rögzítéséhez. Címkék, ellentétben a metaadatok nem használható az Azure számlázásának szűrt megjelenítésére szolgáló, és nem adható meg az Azure Resource Manager-házirend.

**ETag-EK**

Tegyük fel, hogy a két személynek vagy két folyamatok próbálja módosítani a DNS-rekord egy időben. Melyik wins? És a győztes nem tudja, hogy azok már felül más által létrehozott módosításokat?

Az Azure verem DNS ETag-EK ugyanahhoz a erőforráshoz egyidejű változtatások biztonságosan kezelésére használja. ETag-EK az Azure Resource Manager "Címke" külön. Minden DNS-erőforrásrekordok (zóna vagy rekordhalmaz) rendelkezik egy ETag-gel társítva. Erőforrás lekérése, amikor a rendszer is lekéri az Etag. Egy erőforrás frissítésekor kiválaszthatja továbbítására vissza az Etag, Azure verem DNS is ellenőrizze, hogy az Etag a kiszolgáló megfelel. Minden egyes frissítés egy erőforráshoz eredményez a helyreállítás alatt Etag, mert az egy Etag nem egyezik azt jelzi, egyidejű módosítás történt. ETag-EK is használhatja új erőforrás létrehozása annak biztosítására, hogy az erőforrás nem létezik.

Alapértelmezés szerint Azure verem DNS PowerShell zónák egyidejű módosításai és rekordkészletek ETag-EK használja. A választható *-felülírási* kapcsoló Etag ellenőrzések letiltásához használható, amely esetben minden egyidejű végrehajtott módosításokat a rendszer felülírja.

Az Azure verem DNS REST API szintjén ETag-EK megadott HTTP-fejlécek használatával. A következő táblázatban azok viselkedését adja:

| Fejléc | Viselkedés|
|--------|---------|
| Nincs   | A PUT mindig sikeres (nincs Etag ellenőrzése)|
| If-match| PUT csak akkor sikeres, ha erőforrás létezik, és az Etag megfelel|
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
