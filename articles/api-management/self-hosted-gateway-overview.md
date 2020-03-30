---
title: Saját üzemeltetésű Azure API Management átjáró – áttekintés | Microsoft dokumentumok
description: Ismerje meg, hogyan segíti a szervezetek az API-k kezelését a hibrid és többfelhős környezetekben.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 415f0e209e607a863d715b1a66a2435603a662f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513717"
---
# <a name="self-hosted-api-management-gateway-overview"></a>Saját üzemeltetésű API Management átjáró – áttekintés

Ez a cikk bemutatja, hogy a saját üzemeltetésű átjárófunkció hogyan teszi lehetővé a hibrid és a többfelhős API-kezelést, bemutatja a magas szintű architektúrát, és kiemeli az alapvető képességeit.

> [!NOTE]
> A saját üzemeltetésű átjárófunkció előzetes verzióban érhető el. Az előzetes verzió során a saját üzemeltetésű átjáró csak a Fejlesztői és prémium szintű csomagokban érhető el további díj nélkül. A fejlesztői szint egyetlen saját üzemeltetésű átjáró-telepítésre korlátozódik.

## <a name="hybrid-and-multi-cloud-api-management"></a>Hibrid és többfelhős API-kezelés

A saját üzemeltetésű átjárófunkció kibővíti az API Management támogatását a hibrid és többfelhős környezetekben, és lehetővé teszi a szervezetek számára, hogy hatékonyan és biztonságosan kezeljék a helyszíni és a felhők közötti API-kat egyetlen API Management szolgáltatásból az Azure-ban.

A saját üzemeltetésű átjáró, az ügyfelek a rugalmasságot, hogy üzembe helyezheti az API Management átjáró összetevő egy konténeres verziója ugyanazon a környezetben, ahol az API-k at üzemeltetik. Az összes saját üzemeltetésű átjáróaz API Management szolgáltatásból történik, amelyekkel össze vannak egyeztetve, így biztosítva az ügyfelek számára a láthatóságot és az egységes felügyeleti élményt az összes belső és külső API-ban. Az átjárók az API-k közelébehelyezése lehetővé teszi az ügyfelek számára az API-forgalom optimalizálását és a biztonsági és megfelelőségi követelmények teljesítését.

Minden API Management szolgáltatás a következő kulcsfontosságú összetevőkből áll:

-   Az API-ként elérhető felügyeleti sík az Azure Portalon, a PowerShellen és más támogatott mechanizmusokon keresztül konfigurálható.
-   Az átjáró (vagy adatsík) felelős az API-kérelmek proxyzásáért, a szabályzatok alkalmazásáért és a telemetriai adatok gyűjtéséért
-   Fejlesztői portál, amelyet a fejlesztők az API-k felderítésére, megtanulására és fedélzeti használatára használnak

Alapértelmezés szerint ezek az összetevők az Azure-ban vannak üzembe helyezve, így az összes API-forgalom (az alábbi képen folyamatos fekete nyilakként jelenik meg) az Azure-on keresztül áramlik, függetlenül attól, hogy az API-kat megvalósító háttérrendszerek hol vannak telepítve. A modell működési egyszerűsége a megnövekedett késés, megfelelőségi problémák és bizonyos esetekben további adatátviteli díjak rovására történik.

![API-forgalom saját üzemeltetésű átjárók nélkül](media/self-hosted-gateway-overview/without-gateways.png)

Az önkiszolgáló átjárók üzembe helyezése ugyanabban a környezetben, mint a háttérszintű API-implementációk, és hozzáadásuk az API Management szolgáltatáshoz lehetővé teszi, hogy az API-forgalom közvetlenül a háttérszintű API-khoz áramoljon, ami javítja a késést, optimalizálja az adatátviteli költségeket, és lehetővé teszi, hogy a szervezeten belüli összes API-k egyetlen felügyeleti és felderítési pontjának előnyeit, függetlenül attól, hogy a megvalósítások hol találhatók.

![API-forgalom saját üzemeltetésű átjárókkal](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Csomagolás és jellemzők

A saját üzemeltetésű átjáró a felügyelt átjáró minden API Management szolgáltatás részeként az Azure-ba telepített, funkcionálisan egyenértékű verziója. A saját üzemeltetésű átjáró linuxos Docker-tárolóként érhető el a Microsoft Container Registry rendszeréből. Üzembe helyezhető a Docker, a Kubernetes vagy bármely más, asztali, kiszolgálófürtön vagy felhőalapú infrastruktúrán futó tárolóvezénylési megoldásra.

> [!IMPORTANT]
> A felügyelt átjáróban elérhető egyes funkciók még nem érhetők el előzetes verzióban. Leginkább: Jelentkezzen be az Event Hub-házirendbe, a Service Fabric-integráció, az alsóbb rétegbeli HTTP/2. Nincs terv, hogy a beépített gyorsítótár elérhetővé a saját üzemeltetésű átjáró.

## <a name="connectivity-to-azure"></a>Kapcsolódás az Azure-hoz

A saját üzemeltetésű átjáró kimenő TCP/IP-kapcsolatot igényel az Azure-hoz a 443-as porton. Minden saját üzemeltetésű átjáróegyetlen API Management szolgáltatáshoz kell társítható, és a felügyeleti síkon keresztül konfigurálva van. Az önkiszolgáló átjáró az Azure-ral való kapcsolatot használja:

-   Állapotának jelentése szívveréses üzenetek percenként küldésével
-   Rendszeresen ellenőrzi (10 másodpercenként) és alkalmazza a konfigurációs frissítéseket, amikor azok rendelkezésre állnak
-   Kérésnaplók és metrikák küldése az Azure Monitornak, ha erre van konfigurálva
-   Események küldése az Application Insightsba, ha ez be van állítva

Ha megszakad az Azure-ral való kapcsolat, a saját üzemeltetésű átjáró nem tudja fogadni a konfigurációs frissítéseket, jelenteni az állapotát, vagy feltölteni a telemetriai adatokat.

A saját üzemeltetésű átjáró úgy van kialakítva, hogy "sikertelen statikus", és képes túlélni az Azure-ral való kapcsolat ideiglenes elvesztését. A helyi konfigurációbiztonsági biztonsági másolat bekapcsolt állapotával vagy anélkül is telepíthető. Az előbbi esetben a saját üzemeltetésű átjárók rendszeresen menti a konfiguráció biztonsági másolatát a tárolóhoz vagy podhoz csatlakoztatott állandó kötetre.

Ha a konfigurációbiztonsági mentés ki van kapcsolva, és megszakad az Azure-ral való kapcsolat:

-   A futó, saját üzemeltetésű átjárók továbbra is a konfiguráció memórián belüli másolatával fognak működni
-   A leállított saját üzemeltetésű átjárók nem tudnak elindulni

Ha a konfigurációbiztonsági mentés be van kapcsolva, és megszakad az Azure-ral való kapcsolat:

-   A futó, saját üzemeltetésű átjárók továbbra is a konfiguráció memórián belüli másolatával fognak működni
-   A leállított saját üzemeltetésű átjárók a konfiguráció biztonsági másolatának használatát kezdik

A kapcsolat visszaállításakor a kimaradás által érintett minden saját üzemeltetésű átjáró automatikusan újra csatlakozik a társított API Management szolgáltatáshoz, és letölti az összes olyan konfigurációs frissítést, amely akkor történt, amikor az átjáró "offline" volt.

## <a name="next-steps"></a>További lépések

-   [A témakör további hátterét tanulmányban talál.](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Saját üzemeltetésű átjáró üzembe helyezése a Docker-be](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [Saját üzemeltetésű átjáró üzembe helyezése a Kubernetes számára](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
