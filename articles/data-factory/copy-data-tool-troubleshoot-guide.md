---
title: A Azure Data Factory Adatok másolása eszközének hibáinak megoldása
description: Ismerje meg, hogyan lehet elhárítani a Azure Data Factory Adatok másolása eszközével kapcsolatos problémákat.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: yexu
ms.openlocfilehash: 428b8956b4a86009c946d9d8e9e9ba1c58d31403
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87388374"
---
# <a name="troubleshoot-copy-data-tool-in-azure-data-factory"></a>A Azure Data Factory Adatok másolása eszközének hibáinak megoldása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a Azure Data Factory Adatok másolása eszközének gyakori hibaelhárítási módszereit vizsgálja.

## <a name="common-errors-and-messages"></a>Gyakori hibák és üzenetek

### <a name="error-code-unable-to-validate-in-copy-data-tool"></a>Hibakód: nem lehet érvényesíteni Adatok másolása eszközön

- **Tünetek**: adatok másolása eszköz első lépéseként a "nem sikerült érvényesíteni" figyelmeztető üzenet jelenik meg.
- **Okok**: Ez akkor fordulhat elő, ha a harmadik féltől származó cookie-k le vannak tiltva.
- **Megoldás**: 
    - Az Internet Explorer vagy a Microsoft Edge böngésző használata.
    - Ha Chrome böngészőt használ, kövesse az alábbi utasításokat a cookie-k hozzáadásához a *microsoftonline.com* és a *Windows.net*.
        1.  Nyissa meg a Chrome böngészőt.
        2.  Kattintson a csavarkulcsra vagy három sorra a jobb oldalon (a Google Chrome testreszabása és szabályozása).
        3.  Kattintson a **Beállítások** elemre.
        4.  Keresse meg a **cookie-kat** , vagy az **Adatvédelem** területen válassza a speciális beállítások lehetőséget.
        5.  Válassza a **tartalom beállításai**lehetőséget.    
        6.  A cookie-kat úgy kell beállítani, hogy engedélyezzék a helyi adatértékek **beállítását (ajánlott)**.
        7.  Kattintson a **kivételek kezelése**lehetőségre. Az **állomásnév minta** területen adja meg a következőt, és győződjön meg arról, hogy az **Engedélyezés** a viselkedési készlet.
            - login.microsoftonline.com
            - login.windows.net
        8.  Zárjuk be a böngészőt, és indítsa újra a programot.
    - Ha Firefox böngészőt használ, a cookie-k hozzáadásához kövesse az alábbi utasításokat.
        1. A Firefox menüben válassza az **eszközök**  >  **Beállítások**menüpontot.
        2. Az **adatvédelmi**  >  **Előzmények**területen láthatja, hogy az aktuális beállítás az **Előzmények egyéni beállításait használja**.
        3. A **harmadik féltől származó cookie-k elfogadásakor**előfordulhat, hogy a jelenlegi beállítás **soha nem**fog megjelenni, majd a jobb oldalon található **kivételek** lehetőségre kattintva adja hozzá a következő helyeket.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  Zárjuk be a böngészőt, és indítsa újra a programot. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>Hibakód: nem lehet megnyitni a bejelentkezési oldalt, és meg kell adni a jelszót

- **Tünetek**: adatok másolása eszköz átirányítja a bejelentkezési oldalra, de a bejelentkezési oldal nem jelenik meg sikeresen.
- **Okok**: Ez a probléma akkor fordulhat elő, ha a hálózati környezetet az Office-hálózatról a otthoni hálózatra módosította. Vannak gyorsítótárak a böngészőkben. 
- **Megoldás**: 
    1.  Zárjuk be a böngészőt, és próbálkozzon újra. Ha a probléma továbbra is fennáll, folytassa a következő lépéssel.   
    2.  Ha Internet Explorer böngészőt használ, próbálja meg privát módban megnyitni (nyomja le a CTRL + SHIFT + "P" billentyűkombinációt). Ha Chrome böngészőt használ, próbálja megnyitni az inkognitóban módban (nyomja meg a CTRL + SHIFT + "N" billentyűkombinációt). Ha a probléma továbbra is fennáll, folytassa a következő lépéssel. 
    3.  Próbáljon másik böngészőt használni. 


## <a name="next-steps"></a>További lépések

További hibaelhárítási segítségért próbálja ki ezeket az erőforrásokat:
*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videók](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&egy kérdés oldal](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Data Factory Stack Overflow fóruma](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Az ADF-leképezés adatforgalmának teljesítmény-útmutatója](concepts-data-flow-performance.md)
