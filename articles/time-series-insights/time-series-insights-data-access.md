---
title: Eléréséhez és kezeléséhez az Azure Time Series Insights biztonságának konfigurálása |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan konfigurálhat egy felügyeleti hozzáférés és az engedélyek szabályzatok és az adat-hozzáférési szabályzatok az Azure Time Series Insights secure.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: c9bddf8e20524433b31793e277efd954a5d1320e
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423374"
---
# <a name="grant-data-access-to-a-time-series-insights-environment-using-azure-portal"></a>Adathozzáférés biztosítása egy Time Series Insights-környezethez az Azure Portal segítségével

Ez a cikk ismerteti a Time Series Insights-hozzáférési szabályzatok két típusú.

## <a name="video"></a>Videó: 

### <a name="in-this-video-we-cover-creating-and-managing-access-policies-within-time-series-insights-br"></a>Ez a videó ismerteti a Time Series Insights belül hozzáférési szabályzatok létrehozása és kezelése. </br>

> [!VIDEO https://www.youtube.com/embed/5zTIdyHMJW8]

A Time Series Insights-környezetek két különböző típusú hozzáférési házirenddel rendelkeznek:

* Felügyeleti hozzáférési házirendek
* Adathozzáférési házirendek

Mindkét házirend különféle engedélyeket biztosít az Azure Active Directory rendszerbiztonsági tagjai (felhasználók és alkalmazások) számára egy adott környezetre vonatkozóan. A rendszerbiztonsági tagok (felhasználók és alkalmazások) a környezetet tartalmazó előfizetéshez társított active directory (más néven az Azure-bérlő) kell tartoznia.

A felügyeleti hozzáférési házirendek a környezet konfigurálásához kapcsolódó engedélyeket biztosítanak, például:
*   A környezet létrehozása vagy törlése, eseményforrások, referencia-adatkészletek; valamint
*   Az adathozzáférési házirendek felügyelete.

Az adathozzáférési házirendek a következőkhöz biztosítanak engedélyeket: adatlekérdezések kiadása, referenciaadatok módosítása a környezetben, valamint a környezethez társított mentett lekérdezések és perspektívák megosztása.

A házirendek két típusa lehetővé teszi a környezet felügyeletéhez történő hozzáférés és a környezetben található adatokhoz való hozzáférés teljes mértékű szétválasztását. Például akkor lehet, hogy a környezet tulajdonosa/létrehozója eltávolítják az adatelérés környezet beállításával. Ezenkívül felhasználók és szolgáltatások, hogy mely adatokat olvasni a környezet nyújtható nem érhető el a környezet konfigurációjától.

## <a name="grant-data-access"></a>Adathozzáférés biztosítása
Adathozzáférés egy felhasználó rendszerbiztonsági tag az alábbi lépéseket követve:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg a Time Series Insights-környezet. Típus **Time Series** a a **keresési** mezőbe. Válassza ki **Time Series Environment** a keresési eredmények között. 

3. Válassza ki az Azure Time Series Insights környezetet a listából.
   
4. Válassza ki **az adathozzáférési házirendek**, majd **+ Hozzáadás**.
  ![A Time Series Insights-forrás felügyelete – környezet](media/data-access/getstarted-grant-data-access1.png)

5. Válassza ki **felhasználó kiválasztása**.  Keresse meg a felhasználói név vagy e-mail cím a hozzáadni kívánt felhasználó található. Kattintson a **kiválasztása** a kijelölés megerősítéséhez. 

   ![A Time Series Insights-forrás felügyelete – hozzáadás](media/data-access/getstarted-grant-data-access2.png)

6. Válassza ki **szerepkör kiválasztása**. Válassza ki a felhasználó a megfelelő hozzáférés-szerepkör:
   - Válassza ki **közreműködői** szeretné-e, hogy a felhasználó módosíthatja a referenciaadatok és megosztás a mentett lekérdezéseket és perspektívákat a környezet más felhasználóival. 
   - Ellenkező esetben válassza **olvasó** lehetővé teszik a felhasználói adatokat lekérdezni a környezetben, és személyes (nem megosztott) lekérdezéseket mentéséhez a környezetben.

   Válassza ki **Ok** szerepkör kiválasztásának megerősítéséhez.

   ![A Time Series Insights-forrás felügyelete – felhasználó kiválasztása](media/data-access/getstarted-grant-data-access3.png)

8. Válassza ki **Ok** a a **felhasználói szerepkör kiválasztása** lapot.

   ![A Time Series Insights-forrás felügyelete – szerepkör kiválasztása](media/data-access/getstarted-grant-data-access4.png)

9. A **az adathozzáférési házirendek** lap felsorolja a felhasználók és a szerepkör minden felhasználóhoz.

   ![A Time Series Insights-forrás felügyelete – eredmények](media/data-access/getstarted-grant-data-access5.png)

## <a name="provide-guest-access-to-a-user-from-another-aad-tenant"></a>Vendég elérést biztosíthat a felhasználók egy másik AAD-bérlő

"Vendég" nem egy felügyeleti szerepkör; egy kifejezés, amely van meghívót kapott a következőként egyik bérlőből a másikba. Miután a Vendég fiók a bérlő címtárba kapott meghívót, veheti fel a azonos hozzáférés-vezérlést, mint bármely más, vagy felügyeleti hozzáférési jogot a TSI-környezet a hozzáférés-vezérlés (IAM) paneljén, vagy hozzáférést biztosítani az adatokhoz a alkalmazni a környezet az adathozzáférési házirendek panelen keresztül. Az AAD-bérlő vendéghozzáférés további információkért lásd: Ez [dokumentum](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator).

Vendég hozzáférést biztosítani más bérlők egy AAD-felhasználót a Time Series Insights-környezet az alábbi lépéseket követve:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg a Time Series Insights-környezet. Típus **Time Series** a a **keresési** mezőbe. Válassza ki **Time Series Environment** a keresési eredmények között.

3. Válassza ki az Azure Time Series Insights környezetet a listából.

4. Válassza ki **az adathozzáférési házirendek**, majd + **meghívása**.

    ![A Time Series Insights-forrás felügyelete – felhasználó meghívása](media/data-access/getstarted-grant-data-access6.png)

5. Adja meg a felhasználó e-mail címe, akinél szeretné meghívni. Vegye figyelembe, hogy ez legyen az aad-vel kapcsolatos e-mailt. Opcionálisan megadhat egy személyes üzenetet a meghíváshoz.

    ![A Time Series Insights-forrás felügyelete – felhasználó kiválasztása](media/data-access/getstarted-grant-data-access7.png)

6. Megjelenik egy megerősítő buborékra a képernyőn jelennek meg.

    ![A Time Series Insights-forrás felügyelete – felhasználó megerősítése](media/data-access/getstarted-grant-data-access8.png)

7. Válassza ki **felhasználó kiválasztása**. Keresse meg a vendégfelhasználót csak meghívót, keresse meg a hozzáadni kívánt felhasználó e-mail-címét. Kattintson a **kiválasztása** a kijelölés megerősítéséhez.
  
    ![A Time Series Insights-forrás felügyelete – felhasználó megerősítése](media/data-access/getstarted-grant-data-access9.png)

8. Válassza ki **szerepkör kiválasztása**. Válassza ki a megfelelő hozzáférési szerepkört, a Vendég felhasználó:

    * Válassza ki **közreműködői** szeretné-e, hogy a felhasználó módosíthatja a referenciaadatok és megosztás a mentett lekérdezéseket és perspektívákat a környezet más felhasználóival.

    * Ellenkező esetben válassza **olvasó** lehetővé teszik a felhasználói adatokat lekérdezni a környezetben, és személyes (nem megosztott) lekérdezéseket mentéséhez a környezetben.

    Válassza ki **Ok** szerepkör kiválasztásának megerősítéséhez.

    ![A Time Series Insights-forrás felügyelete – szerepkör kiválasztása](media/data-access/getstarted-grant-data-access10.png)

9. Válassza ki **Ok** a a **felhasználói szerepkör kiválasztása** lapot.

10. A **az adathozzáférési házirendek** most már a lap felsorolja a vendégfelhasználót és az egyes vendég felhasználók a szerepkört.

    ![A Time Series Insights-forrás felügyelete – szerepkör megerősítése](media/data-access/getstarted-grant-data-access11.png)

11. Most a vendégfelhasználó elvégzendő lépéseket eléréséhez az Azure-ban található a környezetben csak bérlői meg kell hívni őket. Először is szükségük fogadja el a meghívást, az imént elküldött. Ezt a meghívót az 5. lépésében meghívót címre küldött e-mailen keresztül. Kattintson az "Első lépésként" fogadására.

    ![A Time Series Insights-forrás felügyelete – felhasználó meghívása](media/data-access/getstarted-grant-data-access12.png)

12. Ezután a vendégfelhasználó kell fogadja el a rendszergazda szervezeti tartozó engedélyeket.

    ![A Time Series Insights-forrás felügyelete – -engedélyek elfogadását](media/data-access/getstarted-grant-data-access13.png)

13. Ha a Vendég felhasználó jelentkezik be az e-mail-cím meghívót, és, fogadja el a meghívást, azok insights.azure.com címének lesz. Egyszer, fog gombra kell kattintani a profilképet, az a képernyő jobb felső sarkában lévő e-mailek mellett az. 

    ![A Time Series Insights-forrás felügyelete – -engedélyek elfogadását](media/data-access/getstarted-grant-data-access14.png)

14. Ezután válassza ki a meghívott felhasználónak az Azure bérlő a directory legördülő menüből. Ez az a bérlőt, amelyhez a meghívót őket. 

    ![A Time Series Insights-forrás felügyelete – -engedélyek elfogadását](media/data-access/getstarted-grant-data-access15.png)

15. Végül amikor a vendégfelhasználót a bérlőhöz választja, akkor fogja látni a Time Series Insights-környezet, amely csak a megadott őket a hozzáférést. Most kell rendelkezniük a 8. lépésében megadott szerepkörhöz társított összes funkciót.

## <a name="next-steps"></a>További lépések
* Ismerje meg, [Event Hub-eseményforrás hozzáadása Azure Time Series Insights-környezete](time-series-insights-how-to-add-an-event-source-eventhub.md).
* [Események küldése](time-series-insights-send-events.md) esemény forrását.
* A környezet megtekintése a [Time Series Insights explorer](https://insights.timeseries.azure.com).
