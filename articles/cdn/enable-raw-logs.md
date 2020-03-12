---
title: HTTP nyers naplók Azure CDN
description: Ez a cikk a Azure CDN HTTP nyers naplókat ismerteti.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2020
ms.author: allensu
ms.openlocfilehash: 79ced4df8df12bf2ef1fbe0075b53d02fafb2aff
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129847"
---
# <a name="azure-cdn-http-raw-logs"></a>HTTP nyers naplók Azure CDN
A nyers naplók részletes információkat biztosítanak a naplózáshoz és a hibaelhárításhoz fontos műveletekről és hibákról. A nyers naplók eltérnek a tevékenység naplóitól. A Tevékenységnaplók biztosítják az Azure-erőforrásokon végzett műveletek láthatóságát. A nyers naplók az erőforrás műveleteinek rekordját adják meg.

> [!IMPORTANT]
> A HTTP nyers naplók szolgáltatás a Microsoft Azure CDN számára érhető el.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="configuration"></a>Konfiguráció

Nyers naplók konfigurálása a Azure CDNhoz a Microsoft profilból: 

1. A Azure Portal menüben válassza ki az **összes erőforrást** >>  **\<a-CDN-Profile >** .

2. A **figyelés**területen válassza a **diagnosztikai beállítások**elemet.

3. Válassza a **+ diagnosztikai beállítások hozzáadása**elemet.

    ![CDN diagnosztikai beállítás](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > A nyers naplók csak a profil szintjén érhetők el, míg az összesített HTTP-állapotkódok naplói a végpont szintjén érhetők el.

4. A **diagnosztikai beállítások**területen adja meg a diagnosztikai **beállítások neve**alatt a diagnosztikai beállítás nevét.

5. Válassza ki a **naplót** , és állítsa be a megőrzési napokat.

6. Válassza ki a **célhely részleteit**. A cél beállításai a következők:
    * **Küldés Log Analytics**
        * Válassza ki az **előfizetést** és a **log Analytics munkaterületet**.
    * **Archiválás egy Storage-fiókba**
        * Válassza ki az **előfizetést** és a **Storage-fiókot**.
    * **Stream az Event hub-ba**
        * Válassza ki az **előfizetést**, az **Event hub-névteret**, az **Event hub nevét (nem kötelező)** és az **Event hub-szabályzat nevét**.

    ![CDN diagnosztikai beállítás](./media/cdn-raw-logs/raw-logs-02.png)

7. Kattintson a **Mentés** gombra.

## <a name="raw-logs-properties"></a>Nyers naplók tulajdonságai

A Microsoft szolgáltatásból Azure CDN jelenleg a nyers naplókat (óránként kötegelt feldolgozással) biztosítja. A nyers naplók egyedi API-kérelmeket biztosítanak minden egyes bejegyzéshez a következő sémával: 

| Tulajdonság              | Leírás                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | A bejárati ajtó által kiszolgált kérést azonosító egyedi hivatkozási sztring, amely az ügyfélnek X-Azure-ref fejlécként is elküldve. Egy adott kérelem hozzáférési naplóiban található adatok kereséséhez szükséges. |
| HttpMethod            | A kérelem által használt HTTP-metódus.                                                                                                                                                                     |
| httpVersion           | A kérelem vagy a kapcsolatok típusa.                                                                                                                                                                   |
| RequestUri            | A fogadott kérelem URI-ja.                                                                                                                                                                         |
| RequestBytes          | A HTTP-kérelem üzenetének mérete bájtban, beleértve a kérések fejléceit és a kérelem törzsét.                                                                                                   |
| ResponseBytes         | A háttér-kiszolgáló által válaszként küldött bájtok.                                                                                                                                                    |
| UserAgent             | Az ügyfél által használt böngésző típusa.                                                                                                                                                               |
| Ügyfélip              | Annak az ügyfélnek az IP-címe, amely a kérelmet elvégezte.                                                                                                                                                  |
| Eltelt idő             | A művelet végrehajtásának időtartama (ezredmásodpercben).                                                                                                                                            |
| SecurityProtocol      | A kérelem által használt TLS/SSL protokoll verziója vagy Null, ha nincs titkosítás.                                                                                                                           |
| Végpont              | A CDN-végpont gazdagépe a szülő CDN-profil alatt van konfigurálva.                                                                                                                                   |
| Háttérbeli gazdagép neve     | Annak a háttér-gazdagépnek vagy-forrásnak a neve, ahol a kérelmeket küldik.                                                                                                                                |
| Elküldve a Origin shieldbe | Ha az értéke igaz, az azt jelenti, hogy a rendszer a lekérést a peremhálózati pop helyett a forrás pajzs gyorsítótárából választta. A Origin Shield egy szülő gyorsítótár, amely a gyorsítótár találati arányának növelésére szolgál.                                       |
| HttpStatusCode        | A proxy által visszaadott HTTP-állapotkód.                                                                                                                                                        |
| HttpStatusDetails     | Az eredményül kapott állapot a kérelemben. A karakterlánc értékének jelentése az állapot-hivatkozási táblában található.                                                                                              |
| Pop                   | A felhasználói kérésre válaszoló Edge-pop. A pop-rövidítések a megfelelő metrók repülőtéri kódjai.                                                                                   |
| Gyorsítótár állapota          | Azt jelzi, hogy az objektumot a gyorsítótárból adták-e vissza, vagy a forrásból származik-e.                                                                                                             |

## <a name="next-steps"></a>További lépések
Ebben a cikkben engedélyezte a HTTP nyers naplókat a Microsoft CDN szolgáltatáshoz.

A Azure CDNről és a jelen cikkben említett egyéb Azure-szolgáltatásokról további információt a következő témakörben talál:

* [Elemzés](cdn-log-analysis.md) Azure CDN használati minták.

* További információ a [Azure monitorról](https://docs.microsoft.com/azure/azure-monitor/overview).

* [Log Analytics konfigurálása Azure monitorban](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).