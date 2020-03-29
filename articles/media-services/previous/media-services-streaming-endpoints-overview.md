---
title: Az Azure Media Services streamelési végpontjának áttekintése | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt az Azure Media Services streamelési végpontjairól.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 95d8d819aa1b418b4a7ec736cef64cb989f7e37b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74885636"
---
# <a name="streaming-endpoints-overview"></a>Végpontok streamelése – áttekintés  

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

A Microsoft Azure Media Services (AMS) szolgáltatásban a **streamelési végpont** olyan streamelési szolgáltatást jelöl, amely további terjesztés céljából közvetlenül képes tartalmat biztosítani egy ügyféllejátszó alkalmazásnak vagy egy tartalomkézbesítési hálózatnak (CDN). A Media Services zökkenőmentes Azure CDN-integrációt is biztosít. A StreamingEndpoint szolgáltatás kimenő adatfolyama lehet egy élő közvetítés, igény szerinti videó vagy az eszköz fokozatos letöltése a Media Services-fiókban. Minden Azure Media Services-fiók tartalmaz egy alapértelmezett StreamingEndpoint. További StreamingEndpoints lehet létrehozni a fiók alatt. A StreamingEndpoints két verziója létezik, az 1.0 és a 2.0. 2017. január 10-től kezdődően az újonnan létrehozott AMS-fiókok tartalmazzák a **2.0-s verziójú alapértelmezett** StreamingEndpoint-ot. A fiókhoz hozzáadott további streamelési végpontok is a 2.0-s verziólesz. Ez a változás nem érinti a meglévő számlákat; a meglévő StreamingEndpoints pontok 1.0-s verziójúak lesznek, és frissíthetők a 2.0-s verzióra. Ezzel a módosítással viselkedés, számlázás és funkcióváltozások lesznek (további információkért lásd az alább dokumentált **Streamelési típusok és verziók** című szakaszt).

Az Azure Media Services a következő tulajdonságokkal egészült ki a Streamelési végpont entitással: **CdnProvider**, **CdnProfile**, **StreamingEndpointVersion**. Ezeknek a tulajdonságoknak a részletes áttekintését [ebben](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)a témakörben találja. 

Amikor létrehoz egy Azure Media Services-fiókot, egy alapértelmezett szabványos streamelési végpont jön létre a **Leállított** állapotban. Az alapértelmezett streamelési végpont nem törölhető. Attól függően, hogy az Azure CDN rendelkezésre állása a megcélzott régióban, alapértelmezés szerint az újonnan létrehozott alapértelmezett streamelési végpont is tartalmazza a "StandardVerizon" CDN-szolgáltató integrációja. 
                
> [!NOTE]
> Az Azure CDN-integráció letiltható a streamelési végpont indítása előtt. A `hostname` és a streamelési URL ugyanaz marad, függetlenül attól, hogy engedélyezi-e a CDN-t.

Ez a témakör áttekintést nyújt a streamelési végpontok által biztosított fő funkciókról.

## <a name="naming-conventions"></a>Elnevezési konvenciók

Az alapértelmezett végpont esetében:`{AccountName}.streaming.mediaservices.windows.net`

Minden további végpontesetén:`{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Streamelési típusok és verziók

### <a name="standardpremium-types-version-20"></a>Standard/Premium típusok (2.0-s verzió)

A Media Services 2017 januári kiadásátantól kezdve két streamelési típussal rendelkezik: **Standard** (előzetes verzió) és **Prémium**. Ezek a típusok a "2.0" streamelési végpontverzió részét képezik.


|Típus|Leírás|
|--------|--------|  
|**Standard**|Az alapértelmezett streamelési végpont **egy standard** típusú, a streamelési egységek módosításával prémium típusra módosítható.|
|**Prémium** |Ez a beállítás olyan professzionális forgatókönyvekhez alkalmas, amelyek magasabb skálát vagy vezérlést igényelnek. A streamelési egységek módosításával **prémium** típusúra léphet.<br/>Dedikált streamelési végpontok elszigetelt környezetben élnek, és nem versenyeznek az erőforrásokért.|

Azoknak az ügyfeleknek, akik nagy internetes közönségnek szeretnének tartalmat biztosítani, javasoljuk, hogy engedélyezze a CDN-t a streamelési végponton.

További információkért lásd a [Streamelési típusok összehasonlítása](#comparing-streaming-types) következő szakaszban.

### <a name="classic-type-version-10"></a>Klasszikus típus (1.0-s verzió)

Azoknak a felhasználóknak, akik a 2017. **Classic** Ez a típus az "1.0" streamelési végpontverzió része.

Ha az **"1.0"** streamelési végpont >=1 prémium szintű streamelési egység (SU) rendelkezik, prémium szintű streamelési végpont lesz, és további konfigurációs lépések nélkül biztosítja az összes AMS-funkciót (csakúgy, mint a **Standard/Premium** típus).

>[!NOTE]
>**Klasszikus** streamelési végpontok (verzió "1.0" és 0 SU), korlátozott funkciókat biztosít, és nem tartalmaz SLA.Classic streamendpoints (version "1.0" and 0 SU), provides limited features and doesn't include a SLA. Javasoljuk, hogy a jobb élmény érdekében térjen át a **Standard** típusra, és használjon olyan funkciókat, mint a dinamikus csomagolás vagy titkosítás, valamint a **Standard** típushoz kapcsolódó egyéb funkciók. A **Standard** típusra való átgréshez nyissa meg az [Azure Portalt,](https://portal.azure.com/) és válassza **a Standard tiltás**lehetőséget. Az áttelepítéssel kapcsolatos további információkért tekintse meg az [áttelepítési](#migration-between-types) szakaszt.
>
>Ne feledje, hogy ez a művelet nem állítható vissza, és árképzési hatással jár.
>
 
## <a name="comparing-streaming-types"></a>Adatfolyam-típusok összehasonlítása

### <a name="versions"></a>Verziók

|Típus|StreamingEndpointVersion|Léptékegységek|Tartalomkézbesítési hálózat (CDN)|Számlázás|
|--------------|----------|-----------------|-----------------|-----------------|
|Klasszikus|1.0|0|NA|Ingyenes|
|Normál streamelési végpont (előzetes verzió)|2.0|0|Igen|Fizetett|
|Prémium streamelési egységek|1.0|>0|Igen|Fizetett|
|Prémium streamelési egységek|2.0|>0|Igen|Fizetett|

### <a name="features"></a>Szolgáltatások

Szolgáltatás|Standard|Prémium
---|---|---
Teljesítmény |Akár 600 Mbps, és sokkal nagyobb hatékony átviteli sebesség, ha a CDN-t használja.|200 Mbps streamelési egységenként (SU). CdN-használat esetén sokkal nagyobb hatékony átviteli hatállyal rendelkezhet.
Tartalomkézbesítési hálózat (CDN)|Azure CDN, harmadik fél TŐL származó CDN vagy nincs CDN.|Azure CDN, harmadik fél TŐL származó CDN vagy nincs CDN.
A számlázás arányosan történik| Napi|Napi
Dinamikus titkosítás|Igen|Igen
Dinamikus csomagolás|Igen|Igen
Méretezés|Az automatikus skálázás a megcélzott átviteli ig.|További streamelési egységek.
IP-szűrés/G20/Egyéni állomás <sup>1</sup>|Igen|Igen
Progresszív letöltés|Igen|Igen
Ajánlott használat |A streamelési forgatókönyvek túlnyomó többségéhez ajánlott.|Professzionális használat. 

<sup>1</sup> Csak akkor használható közvetlenül a streamelési végponton, ha a CDN nincs engedélyezve a végponton.<br/>

Az SLA-val kapcsolatos információkat lásd: [Árképzés és SLA.](https://azure.microsoft.com/pricing/details/media-services/)

## <a name="migration-between-types"></a>A típusok közötti áttelepítés

Feladó | Művelet | Műveletek
---|---|---
Klasszikus|Standard|A jelentkezés szükségessége
Klasszikus|Prémium| Méretezés (további streamelési egységek)
Standard/Prémium|Klasszikus|Nem érhető el(Ha a végpont verzióstreamelési verziója 1.0. A scaleunits beállításával klasszikusra válthat, ha a scaleunits -t "0"-ra állítja)
Standard (CDN-nel/anélkül)|Prémium azonos konfigurációkkal|Az **elindított** állapotban engedélyezett. (az Azure portalon keresztül)
Prémium (CDN-nel/anélkül)|Szabvány azonos konfigurációkkal|Az **elindított** állapotban engedélyezett (az Azure Portalon keresztül)
Standard (CDN-nel/anélkül)|Prémium különböző config|Leállított **stopped** állapotban engedélyezett (az Azure Portalon keresztül). Futó állapotban nem engedélyezett.
Prémium (CDN-nel/anélkül)|Standard különböző config|Leállított **stopped** állapotban engedélyezett (az Azure Portalon keresztül). Futó állapotban nem engedélyezett.
1.0-s verzió SU >= 1 CDN-nel|Standard/Premium CDN nélkül|Leállított állapotban **engedélyezett.** Nem engedélyezett az **elindított** állapotban.
1.0-s verzió SU >= 1 CDN-nel|Cdn-nel/anélkül|Leállított állapotban **engedélyezett.** Nem engedélyezett az **elindított** állapotban. Az 1.0-s verziójú CDN törlésre kerül, és újat hoz létre és indít el.
1.0-s verzió SU >= 1 CDN-nel|Prémium CDN-nel/anélkül|Leállított állapotban **engedélyezett.** Nem engedélyezett az **elindított** állapotban. Klasszikus CDN törlésre kerül, és újat hoz létre, és elindult.

## <a name="next-steps"></a>További lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

