---
title: "Fájlok feltöltése Azure Media Services-fiókba az Azure StorSimple-ből | Microsoft Docs"
description: "Ez a cikk rövid áttekintést nyújt az Azure StorSimple Data Managerről. A cikk olyan oktatóanyagokra is hivatkozik, amelyekből megtudhatja, hogyan gyűjthet ki adatokat a StorSimple-ből, és töltheti fel azokat objektumként egy Azure Media Services-fiókba."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 1dd09328-262b-43ef-8099-73241b49a925
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/27/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 866bc2c05acafa5ce6bf535bfce2de577cf98c0b
ms.lasthandoff: 03/31/2017


---
# <a name="upload-files-into-an-azure-media-services-account-from-azure-storsimple"></a>Fájlok feltöltése Azure Media Services-fiókba az Azure StorSimple-ből

Ez a cikk rövid áttekintést nyújt az Azure StorSimple Data Managerről. A cikk olyan oktatóanyagokra is hivatkozik, amelyekből megtudhatja, hogyan gyűjthet ki adatokat a StorSimple-ből, és töltheti fel azokat objektumként egy Azure Media Services- (AMS-) fiókba.

> 
> [!NOTE]
> Az Azure StorSimple Data Manager jelenleg privát előzetes verzióban érhető el. 
> 

## <a name="overview"></a>Áttekintés

A Media Services szolgáltatásban a digitális fájlok feltöltése egy adategységbe történik. Az Adategység tartalmazhat videót, hangot, képeket, miniatűröket, szövegsávokat és feliratfájlokat (valamint mindezen fájlok metaadatait). A fájlok feltöltése után a tartalom a felhőben lesz biztonságosan tárolva további feldolgozás és adatfolyam-továbbítás céljából.

Az [Azure StorSimple](https://docs.microsoft.com/azure/storsimple/) a felhőalapú tárolót a helyszíni tároló kiterjesztéseként használja, és automatikusan rétegzi az adatokat a helyszíni és a felhőalapú tárolók közt. A StorSimple-eszköz deduplikálja és tömöríti az adatokat, mielőtt a felhőbe küldené azokat, így nagyon hatékony eszköz a nagyméretű fájlok a felhőbe továbbításához. A [StorSimple Data Manager](../storsimple/storsimple-data-manager-overview.md) szolgáltatás nyújtotta API-k segítségével az adatok kinyerhetők a StorSimple-ből, és megjeleníthetők AMS-objektumokként.

## <a name="get-started"></a>Bevezetés

1. [Hozzon létre egy Media Services-fiókot](media-services-portal-create-account.md), amelybe az objektumokat át szeretné vinni.
2. Regisztráljon a Data Manager előzetes verziójára a [StorSimple Data Manager](../storsimple/storsimple-data-manager-overview.md) című cikkben leírtak szerint.
3. Hozzon létre egy StorSimple Data Manager-fiókot.
4. Hozzon létre egy adatátalakítási feladatot, amely a futtatásakor adatokat gyűjt egy StorSimple-eszközről, és továbbítja azokat objektumokként egy AMS-fiókba. 

    Amikor a feladat futni kezd, a rendszer létrehoz egy tárolási sort. A sorba az átalakított blobokkal kapcsolatos üzenetek kerülnek, amint a blobok elkészültek. A sor neve megegyezik a feladatdefiníció nevével. A sor használatával megállapíthatja, hogy mikor készül el egy adott objektum, és meghívhatja és futtathatja rajta a kívánt Media Services-műveletet. Például a sor használatával aktiválhat egy Azure Functions-függvényt, amely tartalmazza a szükséges Media Services-kódot.

## <a name="see-also"></a>Lásd még:

[Feladatok aktiválása a .Net SDK használatával a Data Managerben](../storsimple/storsimple-data-manager-dotnet-jobs.md)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Következő lépések

Most már kódolhatja a feltöltött adategységeket. További információ: [Encode Assets](media-services-portal-encode.md) (Adategységek kódolása).

