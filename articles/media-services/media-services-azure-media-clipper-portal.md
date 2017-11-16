---
title: "Kövesse a portálon, az Azure Media Webmontázskészítő |} Microsoft Docs"
description: "Azure Media Webmontázskészítő az Azure portálról használatával videóklipeket létrehozása"
services: media-services
keywords: "Klip; subclip; kódolás; adathordozó"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: faaae7edbc2fb62ae219dd963f405e7246c982d4
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Az Azure Media Webmontázskészítő videóklipeket létrehozása a portálon
Azure Media Webmontázskészítő a portálon hozhat létre videóklipeket eszközökből, a media services partnerek. A kezdéshez navigáljon a portálon a media services-fiókhoz. Ezután válassza ki a **Subclip** fülre.

Az a **Subclip** lapon elvégezheti videóklipeket összeállítása megkezdéséhez. A portálon a Webmontázskészítő egyféle sávszélességű MP4, a többszörös sávszélességű MP4 és az élő archívumot, amelyek közzététele egy érvényes streamelési locator tölti be. Közzé nem tett eszközök nincsenek betöltve.

## <a name="producing-clips"></a>Videóklipeket elkészítése
Klip létrehozásához áthúzása egy eszköz, a klip felület. Ha a jel alkalommal ismert, manuálisan megadhatja azokat a felületén. Ellenkező esetben az eszköz lejátszás vagy húzza a playhead kívánt be van jelölve a Keresés és az idő. Ha be van jelölve a vagy a végpont egyszerre nem áll rendelkezésre, a klip elejétől kezdődik, vagy továbbra is a bemeneti eszköz végén kulcsattribútumokkal.

Keresse meg a keret-pontosság/GOP-pontosságának, használja a keret-továbbításra/GOP-továbbító vagy keret-visszamenőlegesen/GOP-hátra gombokat. A Kivágás adategységek ellen, áthúzása adategységek a klip felületén, az eszköz kijelölés panelről. Válassza ki, és újra sorrendbe állítja a felületen, a kívánt sorrendbe eszközök. Az eszköz ablaktáblát eszköz időtartama, típusa és feloldási metaadatok biztosít minden eszközhöz. Több eszköz együtt hozzáfűzésével, vegye figyelembe az egyes bemeneti fájlok a forrás feloldásához. Ha a forrás-megoldások eltérő, alacsonyabb feloldási bemeneti feloldása a legnagyobb felbontást eszköz a megfelelő van upscaled. A Kivágás-feladat eredményének megtekintéséhez válassza ki a kijelölt megjelölés idők az Előnézet gombra és a klip betöltött szerepe.

## <a name="producing-dynamic-manifest-filters"></a>Dinamikus jegyzék szűrők elkészítése
[Dinamikus jegyzék szűrők](https://azure.microsoft.com/blog/dynamic-manifest/) jegyzék attribútumok és az eszköz ütemterv alapján a szabályok ismertetik. Ezek a szabályok meghatározzák, hogyan kezeli a streamvégpontján a a kimeneti lista (jegyzékfájl). A szűrő módosítása, mely szegmensek átvitt lejátszás használható. A szűrők a Webmontázskészítő által létrehozott helyi szűrők és a forrás-eszközre jellemző. Eltérően megjelenített videóklipeket szűrők nem új eszközök, és nem szükséges egy kódolási feladat létrehozásához. Is gyorsan létrehozható keresztül a [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) vagy [REST API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest), azonban csak GOP pontos. Az adatfolyamként történő kódolású eszközök általában két másodpercen GOP méretet kell.

Dinamikus jegyzék szűrő létrehozásához válassza ki a dinamikus jegyzék szűrőt a Speciális beállítások menüből vágást módot. Követheti, hogy ugyanazt a folyamatot, a szűrő létrehozásához klip létrehozásához. Szűrők csak egyetlen eszköz elleni állítható elő.

## <a name="submitting-clipping-jobs"></a>Kivágás feladatok elküldése
Ha a klip létrehozásának befejezése után, válassza ki a kezdeményezése a küldési feladat gombra, a megfelelő Kivágás feladat vagy a dinamikus jegyzék hívás.