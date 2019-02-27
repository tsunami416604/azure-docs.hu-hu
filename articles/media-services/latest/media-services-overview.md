---
title: Az Azure Media Services v3 áttekintése | Microsoft Docs
description: Ez a cikk általános áttekintését nyújt a Media Services szolgáltatásról, és további információkat biztosító cikkekre mutató hivatkozásokat tartalmaz.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, stream, szórás, élő, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 02/07/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: d6805b3d60e5e30d35acc12c5564498830ddd8fb
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56881727"
---
# <a name="what-is-azure-media-services-v3"></a>Az Azure Media Services v3 ismertetése

Az Azure Media Services egy felhőalapú platform, amellyel olyan megoldások hozhatók létre, amelyek lehetővé teszik többek között a médiaszínvonalú videostreamelést, a hozzáférhetőség és terjesztés javítását vagy tartalmak elemzését. Függetlenül attól, hogy Ön alkalmazásfejlesztő, vagy egy ügyfélszolgálat, kormányzati intézmény vagy szórakoztató vállalat munkatársa, a Media Services olyan alkalmazások létrehozásához nyújt segítséget, amelyek kiváló minőségű multimédiás élményt nyújtanak nagy célközönség számára napjaink legnépszerűbb mobileszközein és böngészőiben. 

## <a name="what-can-i-do-with-media-services"></a>Mire használhatom a Media Services szolgáltatást?

A Media Services lehetővé teszi különböző média-munkafolyamatok létrehozását a felhőben. Az alábbiakban néhány példa látható arra, hogy mit tehet a Media Services használatával.  

* Különböző formátumú videók biztosítása, hogy számos különféle böngészőben és eszközön le lehessen játszani azokat. A különböző ügyfeleknek (mobileszközökre, tévékészülékekre, számítógépekre stb.) biztosított igény szerinti és élő streamek esetén megfelelően kell kódolni és csomagolni a video- és hangtartalmakat. Továbbítására és adatfolyam-e a tartalom megtekintéséhez lásd: [a rövid útmutató: Kódolása és streamelése fájlok](stream-files-dotnet-quickstart.md).
* Élő sportesemények, például focimeccsek, baseballmeccsek, felsőoktatási és középiskolai sportesemények és egyebek streamelése nagy online közönség számára. 
* Nyilvános értekezletek és események, például polgármesteri, képviselőtestületi és törvényhozói testületi ülések közvetítése.
* Rögzített video- vagy hangtartalmak elemzése. A vásárlói elégedettség növelése érdekében például a vállalatok szöveggé alakíthatják a beszédet, és keresési indexeket és irányítópultokat hozhatnak létre. Ezután kinyerhetik a gyakori panaszokkal kapcsolatos adatokat, a panaszok forrását és más kapcsolódó adatokat.
* Előfizetéses videoszolgáltatás létrehozása, és DRM-védelemmel ellátott tartalmak streamelése, ha egy ügyfélnek (például egy filmstúdiónak) korlátoznia kell a szerzői joggal védett munkákhoz való hozzáférést és azok használatát.
* Offline tartalmak biztosítása repülőn, vonaton és autóban való lejátszáshoz. Előfordulhat, hogy egy ügyfélnek le kell töltenie a tartalmat a telefonjára vagy táblagépére a lejátszáshoz, amikor várhatóan nem fognak hálózathoz csatlakozni.
* Oktatási célú e-tanulási videoplatform megvalósítása az Azure Media Services és az [Azure Cognitive Services API-jai](https://docs.microsoft.com/azure/#pivot=products&panel=ai) segítségével a beszéd szövegként történő feliratozása, a több nyelvre való fordítás stb. érdekében. 
* Használja az Azure Media Services együtt [Azure Cognitive Services API-k](https://docs.microsoft.com/azure/#pivot=products&panel=ai) a feliratok és akadálymentes feliratok hozzáadása videókhoz, egy szélesebb közönség számára (például fogyatékkal élők hallási vagy eltérő mentén olvasni kívánt személyek méretformátumok figyelembe vétele nyelv).
* Engedélyezi az Azure CDN nagy méretű méretezési lehetőségek érhetők el az azonnali nagy terhelés (például az események kezdetét) a hatékonyabb hibakezelést eléréséhez. 

## <a name="v3-capabilities"></a>A v3 képességei

A v3 egy egységes API felületen alapul, amely az Azure Resource Manager szolgáltatásra épülő felügyeleti és műveleti funkciókat is biztosít. 

Ez a verzió az alábbi képességeket biztosítja:  

* **Átalakítás**, amellyel a médiafeldolgozási vagy az -elemzési feladatok egyszerű munkafolyamatait határozhatja meg. Az átalakítás a video- és hangfájlok feldolgozásának rögzített folyamata. A későbbiekben ismételten alkalmazhatja a tartalomtárban levő összes fájl feldolgozásához a feladatoknak az átalakítási szolgáltatás számára történő elküldésével.
* **Feladatok** a videók feldolgozásához (kódolás vagy elemzés). A bemenő tartalmak HTTPS- URL- és SAS URL-címekkel vagy az Azure Blob Storage-ban található fájlok elérési útjával határozhatók meg a feladatokban. Az AMS v3 jelenleg nem támogatja a HTTPS URL-címekkel történő darabolásos átviteli kódolást.
* **Értesítések**, amelyek monitorozzák a feladat előrehaladását vagy állapotát, az élő csatornák elindítását/leállítását és a hibaeseményeket. Az értesítések az Azure Event Grid értesítési rendszerével vannak integrálva. Könnyedén előfizethet az Azure Media Services több erőforrásának eseményeire. 
* Az **Azure Resource Manager-sablonokkal** átalakítások, streamelési végpontok, csatornák és egyebek hozhatók létre és helyezhetők üzembe.
* **Szerepköralapú hozzáférés-vezérlés** állítható be az erőforrásszinten, amely lehetővé teszi az adott erőforrásokhoz, például az átalakításokhoz, csatornákhoz és egyebekhez való hozzáférés zárolását.
* **Ügyfél SDK-k** több nyelven: .NET, .NET core, Python, Go, Java és Node.js.

## <a name="naming-conventions"></a>Elnevezési konvenciók

Az Azure Media Services v3 erőforrásneveire is (pl. Adategység, Feladatok, Átalakítások) az Azure Resource Manager elnevezési korlátozásai vonatkoznak. Az Azure Resource Manager szolgáltatásnak megfelelően az erőforrásnevek mindig egyediek. Így bármilyen egyedi azonosító sztringet (pl. GUID-ok) használhat erőforrásnévként. 

A Media Services-erőforrás neve nem tartalmazhatja a következőket: "<", ">", "%", "&", ': ','&#92;','?', '/', "*", "+",".", szimpla idézőjel vagy bármely egyéb vezérlőkarakter. Minden egyéb karakter engedélyezett. Az erőforrásnév maximális hossza 260 karakter. 

Azure Resource Manager elnevezésével kapcsolatos további információkért lásd: [Elnevezési követelményeknek](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) és [elnevezési konvenciók](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="v3-api-design-principles"></a>V3 API-tervezési alapelvek

A v3 API egyik fő tervezési alapelve az API biztonságosabbá tétele. A v3 API-k nem adnak vissza titkos kulcsokat vagy hitelesítő adatokat a **lekérési** vagy **listázási** művelet során. A kulcsok mindig null értékűek, üresek vagy törölve vannak a válaszból. Egy különálló műveleti metódust kell meghívnia a titkos kulcsok vagy hitelesítő adatok lekéréséhez. A különálló műveletekkel különböző RBAC biztonsági engedélyeket állíthat be, ha esetleg valamely API mégis lekér/megjelenít titkos kulcsokat, míg más API-k nem. A hozzáférés RBAC használatával való kezeléséről további információt a [hozzáférés RBAC használatával való kezeléséről](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest) szóló szakaszban talál.

Erre a következők szolgálnak példaként: 

* a rendszer nem ad vissza ContentKey értékeket a StreamingLocator lekérésében, 
* a rendszer nem ad vissza korlátozó kulcsokat a ContentKeyPolicy lekérésében, 
* a rendszer nem adja vissza az URL lekérdezési sztring részét (az aláírás eltávolításához) a feladat HTTP bemeneti URL-jei esetében.

Tekintse meg a [beolvasni a tartalom a fő házirend - .NET](get-content-key-policy-dotnet-howto.md) példa.

## <a name="how-can-i-get-started-with-v3"></a>Hogyan kezdhetem meg a v3 használatát?

Fejlesztési munka a különféle eszközök és SDK-k használata a Media Services v3 API-val kapcsolatos további információkért lásd: [elkezdje](developers-guide.md).

## <a name="v3-content-map"></a>V3-tartalmak térképét

A Media Services v3 tartalmat az alábbi struktúrával (a tartalomjegyzéket is megtalálható) szerint van rendezve:

|Szakaszok| Leírás|
|---|---|
| Áttekintés | A Media Services és mit tehet a szolgáltatás funkcióit mutatja be.|
| Gyors útmutatók | Az új ügyfelek gyorsan kipróbálhatja a Media Services alapvető nap-1 utasítások megjelenítése.|
| Oktatóanyagok | A Media Services leggyakoribb feladatok egy részének megjelenítése forgatókönyv-alapú eljárásokat.|
| Példák | Kódminták mutató hivatkozásokat tartalmaz. |
| Alapelvek | Magyarázatra van szüksége a Media Services v3 kapcsolatos fogalmakat és funkciót tartalmaz. Az alábbi témakörök ismertetik az alapvető fogalmakkal fejlesztés megkezdése előtt át kell tekinteni.<br/><br/>* A felhő feltöltés és tárolás<br/>* Encoding<br/>* A médiaelemzés<br/>* Packaging, delivery, protection<br/>* Élő streaming<br/>* A folyamatos figyelése<br/>* Player ügyfelek<br/><br/>és továbbiakat. |
| Útmutatók | Bemutatják, hogyan lehet egy adott feladat végrehajtásához.|

## <a name="next-steps"></a>További lépések

A [fájlok streamelésével](stream-files-dotnet-quickstart.md) foglalkozó témakörben tekintheti meg, hogy milyen egyszerűen kezdheti meg a videofájlok kódolását és streamelését. 

