---
title: "Egy szolgáltatás számára a piactér létrehozását bemutató útmutatónak |} Microsoft Docs"
description: "Részletes utasításokra van szüksége, hogy hogyan hozhatja létre, hitelesíthet és az adatok szolgáltatás telepítése az Azure piactéren vásárolhat."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 96194198-6991-43b4-918e-ee337e250339
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: c0c9362f1c2e15c947aaaf7187f3383ad243140f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="data-service-publishing-guide-for-the-azure-marketplace"></a>Útmutató az Azure piactér a közzétételi szolgáltatás
> [!IMPORTANT]
> **Jelenleg dolgozunk már nem bevezetési bármely új adatszolgáltatás közzétevők. Új dataservices nem get jóváhagyott listaelem.** Ha egy SaaS-üzleti AppSource a közzétenni kívánt alkalmazás további információt talál [Itt](https://appsource.microsoft.com/partners). Ha egy infrastruktúra-szolgáltatási alkalmazások vagy fejlesztői szolgáltatás szeretne közzétenni az Azure piactérről, további információt talál [Itt](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

Az 1. lépésben befejezése után [fiók létrehozása és nyilvántartási](marketplace-publishing-accounts-creation-registration.md), interaktív azt le a a [általános jellegű](marketplace-publishing-pre-requisites.md) és [műszaki követelményeiben](marketplace-publishing-data-service-creation-prerequisites.md) egy adatszolgáltatás ajánlat Azure piactéren. Most végigvezetjük meg a olyan adatszolgáltatás ajánlat létrehozásának a lépései a [közzétételi Portáljára] [ link-pubportal] az Azure piactérről.

## <a name="1----login-to-the-publishing-portal"></a>1.    Jelentkezzen be a közzétételi portálon.
Ugrás a [https://publish.windowsazure.com](https://publish.windowsazure.com.)

**Közzétételi Portáljára bejelentkezni első alkalommal használja ugyanazt a fiókot, amellyel a vállalati értékesítő profil sikeresen regisztrálva lett fejlesztői központban.**  (Később is hozzáadhat a vállalat bármely alkalmazott a közzétételi portálon co-rendszergazdaként).

Kattintson a **közzététele egy Data Services** Ha ez az első bejelentkezés a közzétételi portált csempéje.

## <a name="2----choose-data-services-in-the-navigation-menu-on-the-left-side"></a>2.    Válasszon **adatszolgáltatások** a bal oldali navigációs menü.
  ![rajz](media/marketplace-publishing-data-service-creation/pubportal-main-nav.png)

## <a name="3----create-a-new-data-service"></a>3.    Egy új szolgáltatás létrehozása
Töltse ki a cím az új adatok szolgáltatás kínálnak, és kattintson a "+" jobb.

  ![rajz](media/marketplace-publishing-data-service-creation/step-3.png)

## <a name="4----review-the-sub-menu-under-the-newly-created-data-service-in-the-navigation-menu"></a>4.    Tekintse át az almenü az újonnan létrehozott szolgáltatás a navigációs menü alatti.
Kattintson a **forgatókönyv** lapra, és tekintse át az összes szükséges lépéseken, amelyekkel megfelelően közzététele az Azure piactéren adatszolgáltatás.

> [!TIP]
> Mindig hivatkozásokra a "Forgatókönyv" lapon vagy az adatszolgáltatás ajánlat almenü bal oldalán a lapok használatának.
> 
> 

## <a name="5----create-a-new-plan"></a>5.    Hozzon létre egy új tervet.
### <a name="offers-plans-transactions"></a>Ajánlatokat, tervek, tranzakciók.
Minden egyes ajánlat rendelkezhet több terveket, de rendelkeznie kell legalább egy (1) terv. Amikor a végfelhasználók előfizetni ajánlatát előfizet az ajánlat terv egyike. Minden csomag határozza meg, hogyan fogja tudni a szolgáltatás használata a végfelhasználók számára.

Azure Piactér jelenleg csak a havi előfizetési tranzakció alapú modellt támogatják adatszolgáltatások, azaz a végfelhasználók havi díj alapján a meghatározott csomag azokat az előfizetett ára fizet, és képes a tranzakció határozzák meg a terv minden hónap száma lesz.

Minden tranzakció általában definiálva az adatszolgáltatás ad vissza rekordok száma, a szolgáltatásnak küldött lekérdezés alapján. Az alapértelmezett érték 100. Minden egyes lekérdezés vissza tranzakciók száma szám lesz a rekordok 100 és a legközelebbi egész számra kerekítve.

Feladata Azure piactér szolgáltatási réteg (mérő) száma minden egyes lekérdezés által felhasznált tranzakciók figyelésére.

> [!IMPORTANT]
> Végfelhasználók számára, amely tranzakció elérte a hónap során le lesz tiltva, továbbra is használja a szolgáltatás a havi előfizetési ciklus végéig.
> 
> A szolgáltatáscsomag vagy a tervek egyikét is (de nem kell) tranzakciók korlátlan számú tartalmazza.
> 
> 

### <a name="create-a-plan"></a>Hozzon létre egy csomagot.
1. Kattintson a **"+"** mellett a "hozzáadása egy új tervet".
2. A lehetőségek közül választhat: **korlátlan** vagy **korlátozott** a terv használata.  Ha korlátozott majd biztosítanak tranzakciók száma a terv lehetővé teszi egy hónap felhasználását.
   
    ![rajz](media/marketplace-publishing-data-service-creation/step-5.1.png)  
   
    Portál közzétételi is javasolhatnak "Terv azonosítója", amely a felhasználói felületen a terv neve a végfelhasználók számára kommunikációhoz használt, és a terv azonosításához a piactéren szolgáltatás által is használt. Ha azt szeretné, a "terv azonosítója" módosíthatja.
   
   > [!NOTE]
   > A "terv azonosítója" minden ajánlat hatókörén belül egyedinek kell lennie. Mivel sok egyéb-azonosítók a közzétételi Portal megtervezése azonosító szerepel az első közzététel az éles, és nem lesz képes ennek az azonosítónak módosítása után lesz zárolva.
   > 
   > 
3. A kiválasztott elfogadásához kattintson.
4. Majd kérni fogja néhány további kérdést az újonnan létrehozott terv kapcsolatban.
   
    ![rajz](media/marketplace-publishing-data-service-creation/step-5.2.png)

| Kérdés | Többszörösére. |
| --- | --- |
| **Ezt a tervet az ingyenes és rendelkezésre álló világszerte?** |Egy teljesen ingyenes-az-kell fizetni tervet is létrehozhat. Ha ez az ajánlat – kizárólag tervezése az azt jelenti, hogy közzétételekor "Szabad kínálnak" a piactéren. Ha csak az egyik (kevés) terv, az informatikai biztosít lehetőséget nyújtanak további információt a szolgáltatás egy viszonylag kis mennyiségű tranzakciók havonkénti végfelhasználók számára.  Ha a válasz "Yes", akkor nincs további kérdéseket kell adnia. |

> [!NOTE]
> A végfelhasználók a fizetős csomagokban mindig verzióra frissítené.
> 
> 

| Kérdés | Többszörösére. |
| --- | --- |
| **Ingyenes próbaverzió érhető el?** |"Nem próbaverzió" minden választhat, vagy adjon meg egy lehetőség, hogy a terv "Hónapig" használata. Közzétevők, például a beállítás használatához az ingyenes egy hónapos tartozó ajánlat előnyeinek megismerése lehetőséget nyújt a végfelhasználók számára. |

> [!IMPORTANT]
> Végfelhasználók csak akkor tudja egy ingyenes próbaverzióra vásárolni, ha az általuk létrehozott fizetési eszközt pl. hitelkártya, nagyvállalati szerződésben.
> 
> Az ingyenes próbaverzióval az egy hónap után Azure piactér indul el díjszabási ügyfelek az ár, az előfizetés napjától kivéve, ha az ügyfél által kezdeményezett előfizetés megszakítását. Nincsenek különleges értesítés a végfelhasználók számára biztosított.
> 
> 

| Kérdés | Többszörösére. |
| --- | --- |
| **A terv szükséges beszerzési egy promóciós kódot?** |Közzétevők van korlátozhatják a Service-csomagok révén egy különleges kódot, "A Promocode" nevű adott ügyfélnek. Csak a Promocode amelyekről végfelhasználók fog tudni a terv előfizetni. Ha úgy dönt, hogy a "nem", akkor beleegyezik abba, hogy mindenki a régióban, amennyiben az ajánlat nem érhető el (lásd: [piactér Marketing Content útmutató](marketplace-publishing-push-to-staging.md) további részletekért) fogja tudni a terv előfizetni. Nincsenek további kérdések meg kell adnia. |
| **A terv a bárki, aki nem rendelkezik érvényes promóciós kódot is elrejthetik?** |Ha az előző kérdésre adott válasz "Yes" a közzétevő rendelkezik egy lehetőség, hogy teljesen távolítsa el a terv jelennek meg a piactér felhasználói Felületét. Azt jelenti, ügyfelek nem fogják látni az ajánlat részleteit megjelenítő oldalon a tervet. Végfelhasználók számára, amely megkapja a promocode, azok megvásárlását fog tudni arra a promocode használatával. |

## <a name="6----create-your-marketplace-marketing-content"></a>6.    A tartalom marketing piactér létrehozása
Adja meg a szükséges információkat arról, hogyan **Marketing, árazás, támogatást és kategóriák** lapok látogasson el a [piactér Marketing Content útmutató](marketplace-publishing-push-to-staging.md) vagyis minden összetevőihez közzé az Azure piactéren.  

## <a name="7----connect-your-offer-to-your-service-sql-azure-based-or-web-service-based"></a>7.    Az ajánlat kapcsolódik a szolgáltatáshoz (SQL Azure-alapú vagy webszolgáltatás-alapú).
Kattintson a **adatszolgáltatások** almenü.

A lap felső részén a meg kell adnia annak az ajánlat **Namespace**.  

  ![rajz](media/marketplace-publishing-data-service-creation/step-7.png)

Az alábbi kérdés fog elláthat hogyan a közzétevő teszi közzé az újonnan létrehozott ajánlat az Azure piactéren. (További információ: a [Data Services műszaki előfeltétel útmutató](marketplace-publishing-data-service-creation-prerequisites.md)).

  ![rajz](media/marketplace-publishing-data-service-creation/step-7.2.png)

**Közzététel az adatbázis-alapú szolgáltatás**

Kattintson a **adatbázis**. A következő lap jelenik meg:

  ![rajz](media/marketplace-publishing-data-service-creation/step-7.3.png)

Egy CSDL-hozzárendelés létrehozása az SQL Azure Database alapuló adatkészlet:

  ![rajz](media/marketplace-publishing-data-service-creation/step-7.4.png)

Majd minden táblához

  ![rajz](media/marketplace-publishing-data-service-creation/step-7.5.png)

  ![rajz](media/marketplace-publishing-data-service-creation/step-7.6.png)

Ha a webszolgáltatás

  ![rajz](media/marketplace-publishing-data-service-creation/step-7.7.png)

> [!IMPORTANT]
> Olvasási [leképezése egy meglévő webes CSDL keresztül OData szolgáltatás](marketplace-publishing-data-service-creation-odata-mapping.md) részletes útmutatásért és példákért CSDL webes szolgáltatás létrehozásához.
> 
> 

## <a name="next-steps"></a>Következő lépések
Most, hogy létrehozta az adatszolgáltatás ajánlatot, győződjön meg arról, hogy elvégezte-e a utasításait a [piactér Marketing Content útmutató](marketplace-publishing-push-to-staging.md) előtt előre [tesztelése a szolgáltatás átmeneti](marketplace-publishing-data-service-test-in-staging.md).

## <a name="see-also"></a>Lásd még:
* [Első lépések: Hogyan ajánlat közzététele az Azure piactéren](marketplace-publishing-getting-started.md)
* Ha a teljes OData-megfeleltetési folyamat és a rendeltetés megválaszolásával, olvassa el ebben a cikkben [szolgáltatás OData megfeleltetése](marketplace-publishing-data-service-creation-odata-mapping.md) áttekintheti a definíciókat, és a utasításokat.
* Ha érdekli tanulási és az adott csomópont, és a paraméterek ismertetése, olvassa el ebben a cikkben [szolgáltatás OData leképezési Adatcsomópontokat](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) definíciók és magyarázatokat, példák és a nagybetűk használatát a környezetben.
* Ha érdekli példák megtekintésével, olvassa el ebben a cikkben [adatok szolgáltatás OData leképezési példák](marketplace-publishing-data-service-creation-odata-mapping-examples.md) mintakód és kód szintaxis és a környezetben.

[link-pubportal]:https://publish.windowsazure.com
