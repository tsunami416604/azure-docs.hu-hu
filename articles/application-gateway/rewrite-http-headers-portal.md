---
title: HTTP-kérelmek és válaszfejlécek Azure Application gatewayjel – Azure portal újraírási |} A Microsoft Docs
description: Az Azure portal használata az Azure Application Gateway a HTTP-fejléceket, a kérelmek és válaszok az átjárón áthaladó újraírási konfigurálása
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/10/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 9d52114f5d01beca53ed48ee2114dc98cc20f3a2
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682424"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>HTTP-kérelmek és válaszfejlécek Azure Application gatewayjel – Azure portal újraírása

Ez a cikk bemutatja, hogyan konfigurálhatja az Azure portal használatával egy [Application Gateway v2 szintű Termékváltozatot](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) újraírási a HTTP-fejléceket, a kérelmek és válaszok.

> [!IMPORTANT]
> Az Application Gateway automatikus skálázású és zónaredundáns termékváltozata jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. A részleteket lásd: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="before-you-begin"></a>Előkészületek

Szüksége lesz egy Application Gateway v2 a v1-Termékváltozat nem támogatott Termékváltozat, mivel a fejléc újraírási képessége. Ha nem rendelkezik a v2 szintű Termékváltozatot, hozzon létre egy [Application Gateway v2 szintű Termékváltozatot](<https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps>) megkezdése előtt.

## <a name="what-is-required-to-rewrite-a-header"></a>Mi szükséges egy fejléc újraírása

HTTP-fejléc újraírási konfigurálásához meg kell:

1. A szükséges a http-fejlécek újraírási új objektumokat hozhat létre:

   - **Művelet újraírási**: a kérés és a kérés üzenetfejlécének mezői újraírási kívánt és az eredeti fejlécek kell írni az új érték adható meg. Ha szeretné társítani egy vagy több újraírási feltétel egy újraírási művelet.

   - **A feltétel újraírási**: Egy opcionális konfigurációs. újraírási feltétel kerül, ha a program a HTTP (S)-kérelmek és válaszok tartalmát értékeli. A újraírási feltétel társított újraírási művelet végrehajtásához a döntés-e a HTTP (S) kérelem vagy válasz párosítva a újraírási feltétel alapul. 

     Ha egynél több feltételek társított egy műveletet, majd a művelet lesz végrehajtva csak akkor, ha a feltételek mindegyike teljesül, azaz, egy logikai és művelet történik.

   - **Újraírási szabályt**: újraírási szabályt tartalmaz több újraírási művelet – újraírási feltétel kombinációi.

   - **Feladatütemezési szabály**: segít megállapítani, hogy a sorrendet, amelyben a különböző újraírási szabályok végrehajtásra kerülhetnek. Ez akkor hasznos, ha több újraírási szabályok újraírási készlet. A szabály feladatütemezési alacsonyabb értékkel rendelkező átírási szabály lekérdezi először hajtott végre. Ha megadja a ugyanaz a szabály során, az két újraírási szabályok a végrehajtás sorrendje nem determinisztikus lesz.

   - **Set újraírási**: több újraírási szabályok, amely hozzá lesz rendelve egy kérelem-útválasztási szabályt tartalmaz.

2. Az útválasztási szabályainak beállítása átírása csatolni kell adnia. Ennek az oka az átfogalmazás konfigurációs van csatolva a forrás figyelőt az útválasztási szabályt. Egy alapszintű útválasztási szabályt használatakor a fejléc újraírási konfigurációs társítva a forrás-figyelő és egy globális fejléce módosítsa úgy. -Alapú útválasztási szabály használata esetén a fejléc újraírási konfiguráció az URL-Címtérkép van definiálva. Ezért csak vonatkozik egy helyet a megadott elérési út területéhez.

Több http-fejléc újraírási csoportok hozhat létre, és minden egyes újraírási set több kérésfigyelőt is alkalmazható. Azonban csak az egyik értéke egy adott hallgató újraírási is alkalmazhat.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókjával.

## <a name="configure-header-rewrite"></a>Fejléc újraírási konfigurálása

Ebben a példában az átirányítási URL-cím lesz módosítjuk, a háttér-alkalmazás által küldött http-válasz location fejlécébe újraírásával. 

1. Válassza ki **összes erőforrás**, majd válassza ki az application gateway.

2. Válassza ki **újraírja** a bal oldali menüből.

3. Kattintson a **+ set Újraírási**. 

   ![Újraírási készlet hozzáadása](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. Adja meg annak a újraírási-készlet nevét, és társíthatja azt egy útválasztási szabályt:

   - Adja meg a beállított átírása nevét a **neve** szövegmezőbe.
   - Válassza ki egy vagy több szabály szerepel a **tartozó útválasztási szabályok** listája. Ezeket a szabályokat, amelyek nem lettek társítva más újraírási csoportok csak választhat. A szabályok, amelyek már hozzá lettek rendelve, és az egyéb újraírási készletek szürkén jelennek meg.
   - Kattintson a Tovább gombra.
   
     ![Név és a társítás hozzáadása](media/rewrite-http-headers-portal/name-and-association.png)

5. Hozzon létre egy újraírási szabályt:

   - Kattintson a **+ Hozzáadás újraírási szabályt**.![ Átírási szabály hozzáadása](media/rewrite-http-headers-portal/add-rewrite-rule.png)
   - Adjon meg egy nevet a Újraírási szabályt névmezőbe szereplő újraírási szabályt, és adjon meg egy szabály sorozatot.![Adja hozzá a szabály nevét](media/rewrite-http-headers-portal/rule-name.png)

6. Ebben a példában a location fejlécet azt fogja újraírási csak akkor, ha az "azurewebsites.net" hivatkozást tartalmaz. Ehhez a kiértékelését, hogy a válasz location fejlécébe tartalmaz azurewebsites.net feltétel hozzáadása:

   - Kattintson a **+ feltétel hozzáadása** majd kattintson a szakasz a a **Ha** bontsa ki azt. az utasítások![ Adja hozzá a szabály nevét](media/rewrite-http-headers-portal/add-condition.png)

   - Válassza ki **HTTP-fejléc** származó a **ellenőrzéséhez változó típusa** legördülő listából. 

   - Válassza ki **fejléc típus** , **válasz**.

   - Mivel ebben a példában a location fejlécet, amely éppen egy közös fejlécének, válassza ki azt kiértékelése **közös fejlécének** választógombot, a **fejlécnév**.

   - Válassza ki **hely** származó a **közös fejlécének** legördülő listából.

   - Válassza ki **nem** , a **kis-és nagybetűket** beállítás.

   - Válassza ki **egyenlő (=)** származó a **operátor** legördülő listából.

   - Adja meg a reguláris kifejezési minta. Ebben a példában használjuk a minta `(https?):\/\/.*azurewebsites\.net(.*)$` .

   - Kattintson az **OK** gombra.

     ![Location fejlécet módosítása](media/rewrite-http-headers-portal/condition.png)

7. A location fejlécet újraírási művelet hozzáadásához:

   - Válassza ki **beállítása** , a **művelettípus**.

   - Válassza ki **válasz** , a **fejléc típus**.

   - Válassza ki **közös fejlécének** , a **fejlécnév**.

   - Válassza ki **hely** származó a **közös fejlécének** legördülő listából.

   - Adja meg a fejléc értéke. Ebben a példában használjuk `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` a fejléc értékeként. Ez a művelet felülírja *azurewebsites.net* a *contoso.com* location fejlécébe.

   - Kattintson az **OK** gombra.

     ![Location fejlécet módosítása](media/rewrite-http-headers-portal/action.png)

8. Kattintson a **létrehozás** készlet létrehozása az újraírási.

   ![Location fejlécet módosítása](media/rewrite-http-headers-portal/create.png)

9. A felhasználóregisztráció nézet az átfogalmazás beállítása. Győződjön meg arról, hogy megtalálható-e a fent létrehozott újraírási set újraírási készletek listájában.

   ![Location fejlécet módosítása](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>További lépések

További információ a konfiguráció szükséges elvégeznie néhány gyakoribb felhasználási kapcsolatban lásd: [közös fejlécének újraírási forgatókönyvek](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

   
