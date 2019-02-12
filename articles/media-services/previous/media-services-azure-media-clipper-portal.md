---
title: Az Azure Media Clipperrel használja a portálon |} A Microsoft Docs
description: Készíthet az Azure Media Clipperrel az Azure Portal használatával
services: media-services
keywords: clip;subclip;encoding;media
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 02/08/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 510176331a1b79a1069b42afabb63b6b326951cc
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992312"
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Az Azure Media Clipperrel klip létrehozása a portálon  

Azure Media Clipperrel a portál segítségével az eszközök a media services-fiókok a készíthet. Első lépésként nyissa meg a portálon a media services-fiók. Ezután válassza ki a **Részklip** fülre.

Az a **Részklip** lap, Ön tudja kezdeni a legjobb pillanatokból összeválogatott összeállítása. A portálon a Clipperrel egyszeres átviteli sebességű MP4-fájlnak, többszörös átviteli sebességű MP4-fájlnak és a egy érvényes a streamelési lokátorok a közzétett élő adásokból tölti be. Közzé nem tett eszközök nem töltődnek be.

A Clipperrel jelenleg nyilvános előzetes verzióban érhető el. Az Azure Portalon a Clipperrel eléréséhez nyissa meg a [nyilvános előzetes verziójú szolgáltatásainak weblapján](https://portal.azure.com/?feature.subclipper=true).

Az alábbi ábrán a Clipperrel kezdőlapja a media services-fiók: ![Az Azure Portalon az Azure Media Clipperrel](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Legjobb pillanatokból összeválogatott előállító
Klip létrehozása, áthúzása adategység alakzatot a klipek felületet. Ha be van jelölve-szorosa ismertek, manuálisan megadhatja azokat a felületen. Más esetekben az adategység a lejátszás vagy húzza a playhead kívánt jel a Keresés és az idő. Ha be van jelölve a vagy a végpont egyszerre nem áll rendelkezésre, a klipek elejétől kezdődik, vagy továbbra is a bemeneti objektuma végét jelölik.

Keresse meg, keret-pontossága/Képcsoporttal-pontosságú, használja a keret-továbbítás/Képcsoporttal-továbbító vagy keret-visszamenőlegesen/Képcsoporttal-hátra gombokat. A Kivágás adategységek ellen, áthúzása adategységek a klipek felületen az eszköz kiválasztása panel. Válassza ki, és rendezze át a kívánt sorrendben felületen eszközök. Az eszközintelligencia időkiválasztás panel minden eszköz biztosít az objektum időtartamának, típusa és névfeloldási metaadatokat. Adategységek együtt összetűzésének, vegye figyelembe az egyes bemeneti fájlokat a forrás feloldásához. Ha eltérnek a forrás-megoldások, alacsonyabb feloldási bemeneti van upscaled meg a megoldást a legnagyobb felbontást eszköz kielégítése érdekében. A Kivágás feladat kimenetének megtekintéséhez válassza ki a kijelölt mark idők az Előnézet gombra, és a videoklip lejátszása közben.

## <a name="producing-dynamic-manifest-filters"></a>Előállító dinamikusjegyzék-szűrők
[Dinamikusjegyzék-szűrők](https://azure.microsoft.com/blog/dynamic-manifest/) jegyzékfájl attribútumok és az eszköz ütemterv alapján szabályok leírására. Ezek a szabályok meghatározzák, hogy a tartalomstreameléshez használt streamvégpont hogyan kezeli a kimenet listának (manifest). A szűrő segítségével módosíthatja, mely szegmensek átvitt lejátszás céljából. A szűrők a Clipperrel által létrehozott helyi szűrők és a forrás-eszközre jellemző. Ellentétben a megjelenített klipek szűrők nem új eszközök, és nem igénylik a kódolási feladatokat előállításához. Gyorsan létrehozhatók keresztül a [.NET SDK-val](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) vagy [REST API-val](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest), azonban olyan csak Képcsoporttal pontos. Eszközök streameléshez kódolású rendszerint egy Képcsoporttal méretű két másodpercen kell.

Hozzon létre egy dinamikusjegyzék-szűrő, lépjen a **eszközök** lapra, és válassza ki a kívánt eszközt. Válassza ki a **Részklip** gombot a felső menüben. Válassza ki a dinamikusjegyzék-szűrő a Speciális beállítások menüből kapcsolást módot. Kövesse ugyanezt az eljárást a megjelenített klip létrehozása a szűrő létrehozásához. Szűrők csak az egyetlen eszköz kell mutatni.

Az alábbi ábrán a Clipperrel dinamikus jegyzékfájl szűrő módban az Azure Portalon: ![A dinamikus jegyzékfájl szűrő mód az Azure Portalon az Azure Media Clipperrel](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Klipekkel kapcsolatos feladatok elküldése
Amikor végzett, a klipek összeállítása, válassza ki a kezdeményezése a küldési feladat gombra, a megfelelő kapcsolást feladat vagy a dinamikus jegyzékfájl hívás.

## <a name="next-steps"></a>További lépések
Ismerkedés az Azure Media Clipperrel használatával, olvassa el a [bevezetés](media-services-azure-media-clipper-getting-started.md) részleteivel kapcsolatban, hogyan helyezhet üzembe a widget.
