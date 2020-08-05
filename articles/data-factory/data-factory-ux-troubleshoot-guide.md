---
title: Azure Data Factory UX – problémamegoldás
description: Ismerje meg, hogyan lehet elhárítani Azure Data Factory UX-problémákat.
services: data-factory
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: 9a96c385c25eea1c5e217665f162de402a73e558
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567948"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>A Azure Data Factory UX problémáinak elhárítása
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a Azure Data Factory UX gyakori hibaelhárítási módszereit vizsgálja.

## <a name="adf-ux-not-loading"></a>Az ADF UX nem töltődik be

> [!NOTE]
> A Azure Data Factory UX hivatalosan támogatja a Microsoft Edge és a Google Chrome böngészőt. Más böngészők használata váratlan vagy nem dokumentált viselkedéshez vezethet.

### <a name="third-party-cookies-blocked"></a>A harmadik féltől származó cookie-k blokkolva

Az ADF UX böngésző-cookie-kat használ a felhasználói munkamenet megőrzéséhez, és lehetővé teszi az interaktív fejlesztési és monitorozási élmények használatát. Lehetséges, hogy a böngésző blokkolja a harmadik féltől származó cookie-kat, mert inkognitóban-munkamenetet használ, vagy ha engedélyezve van az ad blocker. A harmadik féltől származó cookie-k blokkolása problémákat okozhat a portál betöltésekor, például egy üres lapra irányítja át, https://adf.azure.com/accesstoken.html vagy figyelmeztető üzenetet kap arról, hogy a harmadik féltől származó cookie-k le vannak tiltva. A probléma megoldásához engedélyezze a harmadik féltől származó cookie-k beállításait a böngészőben a következő lépések végrehajtásával:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Az összes cookie engedélyezése

1. Keresse fel a **Chrome://Settings/Cookies** a böngészőben.
1. Jelölje be **az összes cookie engedélyezése** lehetőség: ![ Chrome-Allow-all-Cookies](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Frissítse az ADF UX-et, és próbálkozzon újra.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Csak a cookie-k használatának engedélyezése az ADF UX számára
Ha nem kívánja engedélyezni az összes cookie-t, igény szerint egyszerűen engedélyezheti az ADF UX használatát:
1. Látogasson el a **Chrome://Settings/Cookies**.
1. Kattintson az **Add (Hozzáadás** ) lehetőségre a helyek területen **, amelyek mindig cookie-kat HASZNÁLHATNAK** ![ Az ADF UX az engedélyezett helyekhez](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Adja hozzá a **ADF.Azure.com** -webhelyet, és kattintson a **minden cookie** lehetőségre, majd a Mentés elemre. ![Az összes cookie engedélyezése az ADF UX-helyről](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Frissítse az ADF UX-et, és próbálkozzon újra.


### <a name="microsoft-edge"></a>Microsoft Edge

1. Keresse fel a **Edge://Settings/Content/Cookies** a böngészőben.
1. Győződjön meg arról, hogy engedélyezi a **helyek számára a cookie-k adatmentését és olvasását** , és hogy a **harmadik féltől származó cookie-k** letiltása beállítás le van tiltva az ![ összes cookie engedélyezése](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Frissítse az ADF UX-et, és próbálkozzon újra.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Csak a cookie-k használatának engedélyezése az ADF UX számára

Ha nem kívánja engedélyezni az összes cookie-t, igény szerint egyszerűen engedélyezheti az ADF UX használatát:

1. Látogasson el a **Edge://Settings/Content/Cookies**.
1. Az **Engedélyezés** szakaszban kattintson a **Hozzáadás** gombra, és adja hozzá a **ADF.Azure.com** helyet. ![Az ADF UX hozzáadása az engedélyezett helyekhez](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Frissítse az ADF UX-et, és próbálkozzon újra.

## <a name="next-steps"></a>Következő lépések

További hibaelhárítási segítségért próbálja ki ezeket az erőforrásokat:

* [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
* [Data Factory Stack Overflow fóruma](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
* [Azure-videók](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft Q&egy kérdés oldal](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
