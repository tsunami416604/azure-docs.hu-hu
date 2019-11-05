---
title: Saját üzemeltetésű Azure API Management Gateway – áttekintés | Microsoft Docs
description: Ismerje meg, hogyan kezelheti a szervezetek az API-kat a hibrid és a többfelhős környezetekben a saját üzemeltetésű Azure API Management Gateway segítségével.
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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513717"
---
# <a name="self-hosted-api-management-gateway-overview"></a>Saját üzemeltetésű API Management átjáró áttekintése

Ez a cikk azt ismerteti, hogy a saját üzemeltetésű átjáró funkció hogyan teszi lehetővé a hibrid és a többfelhős API-felügyeletet, bemutatja a magas szintű architektúrát, és kiemeli annak alapvető képességeit.

> [!NOTE]
> A saját üzemeltetésű átjáró funkció előzetes verzióban érhető el. Az előzetes verzió ideje alatt a saját üzemeltetésű átjáró csak a fejlesztői és prémium szinteken érhető el, díjmentesen. A fejlesztői réteg egyetlen saját üzemeltetésű átjáróra korlátozódik.

## <a name="hybrid-and-multi-cloud-api-management"></a>Hibrid és többfelhős API Management

A saját üzemeltetésű átjáró szolgáltatás kibővíti a hibrid és a többfelhős környezetek API Management támogatását, és lehetővé teszi, hogy a szervezetek hatékonyan és biztonságosan kezeljék a helyszíni és a felhőben üzemeltetett API-kat az Azure egyetlen API Management szolgáltatásával.

A saját üzemeltetésű átjáróval rendelkező ügyfelek rugalmasan telepíthetik az API Management Gateway összetevő egy tárolós verzióját ugyanarra a környezetbe, ahol az API-kat üzemeltetik. Az összes saját üzemeltetésű átjáró felügyelve van a API Management szolgáltatásból, amelyet összevontak, így biztosítva az ügyfelek számára a belső és külső API-k láthatóságát és egységes felügyeleti élményét. Az átjárók az API-khoz való közelsége lehetővé teszi az ügyfeleknek az API-forgalom forgalmának optimalizálását, valamint a biztonsági és megfelelőségi követelmények kielégítését.

Minden API Management szolgáltatás a következő fő összetevőkből áll:

-   Felügyeleti sík, amely API-ként van kitéve a szolgáltatás konfigurálásához a Azure Portal, a PowerShell és más támogatott mechanizmusok használatával.
-   Az átjáró (vagy az adatsík) feladata az API-kérelmek proxyja, a házirendek alkalmazása és a telemetria begyűjtése.
-   Fejlesztői portál a fejlesztők számára az API-k használatának felderítésére, megismerésére és bevezetésére

Alapértelmezés szerint az összes összetevő üzembe helyezése az Azure-ban történik, így az összes API-forgalom (a lenti képen látható folytonos fekete nyíl) az Azure-on keresztül áramlik, függetlenül attól, hogy az API-kat végrehajtó háttérrendszer hol üzemel. A modell működésének egyszerűsége többek között a késés, a megfelelőségi problémák és bizonyos esetekben a további adatátviteli díjak költségeiből származik.

![API-forgalom saját üzemeltetésű átjárók nélkül](media/self-hosted-gateway-overview/without-gateways.png)

A saját üzemeltetésű átjárók üzembe helyezése a háttérbeli API-implementációkkal megegyező környezetekben, és a API Management szolgáltatásba való felvételük lehetővé teszi, hogy az API-forgalom közvetlenül a háttérbeli API-kra váltson, ami javítja a késést, optimalizálja az adatátviteli költségeket, és engedélyezi a megfelelőség a vállalaton belüli API-k felügyeletének és felderítésének előnyeit is megőrizheti, függetlenül attól, hogy a megvalósításuk hol található.

![API-forgalom önkiszolgáló átjárókkal](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>Csomagolás és szolgáltatások

A saját üzemeltetésű átjáró a felügyelt átjáró egy, az Azure-ban üzembe helyezett, funkcionálisan egyenértékű verziója, amely minden API Management szolgáltatás részeként üzemel. A saját üzemeltetésű átjáró Linux-alapú Docker-tárolóként érhető el a Microsoft Container Registry. Üzembe helyezhető a Docker, a Kubernetes, vagy bármely más, asztali, kiszolgálófürt vagy felhőalapú infrastruktúrán futó tároló-előkészítési megoldás esetében.

> [!IMPORTANT]
> A felügyelt átjáróban elérhető néhány funkció még nem érhető el előzetes verzióban. A legtöbb esetben: Jelentkezzen be az Event hub-szabályzatba, Service Fabric az integrációt, az alárendelt HTTP/2. A saját üzemeltetésű átjáróban nem érhető el a beépített gyorsítótár elérhetővé tételének terve.

## <a name="connectivity-to-azure"></a>Csatlakozás az Azure-hoz

A saját üzemeltetésű átjáróhoz a 443-es porton kimenő TCP/IP-kapcsolat szükséges az Azure-hoz. Minden saját üzemeltetésű átjárót egyetlen API Management szolgáltatáshoz kell társítani, és a felügyeleti síkon keresztül kell konfigurálni. A saját üzemeltetésű átjáró az Azure-hoz való kapcsolódást használja a következőhöz:

-   Az állapot jelentése percenkénti szívverési üzenetek küldésével
-   Rendszeres ellenőrzés (10 másodpercenként) és konfigurációs frissítések alkalmazása, ha elérhetők
-   Kérelmek naplóinak és metrikáinak küldése Azure Monitorre, ha erre van konfigurálva
-   Események küldése Application Insightsre, ha erre van beállítva

Ha az Azure-kapcsolat megszakad, a saját üzemeltetésű átjáró nem tudja fogadni a konfigurációs frissítéseket, jelenteni annak állapotát, vagy fel kell töltenie a telemetria.

A saját üzemeltetésű átjáró úgy lett kialakítva, hogy "nem statikus", és képes túlélni az Azure-hoz való kapcsolódás ideiglenes elvesztését. A szolgáltatás a helyi konfiguráció biztonsági másolatának bekapcsolásával vagy anélkül is üzembe helyezhető. Az előző esetben a saját üzemeltetésű átjárók rendszeresen mentik a konfiguráció biztonsági másolatát a tárolóhoz vagy a podhez csatolt állandó köteten.

Ha a konfiguráció biztonsági mentése ki van kapcsolva, és az Azure-kapcsolat megszakadt:

-   A-t futtató saját üzemeltetésű átjárók továbbra is működni fognak a konfiguráció memóriában tárolt példányával.
-   A saját üzemeltetésű átjárók leállítása nem fog tudni elindulni

Ha a konfigurációs biztonsági mentés be van kapcsolva, és az Azure-kapcsolat megszakadt:

-   A-t futtató saját üzemeltetésű átjárók továbbra is működni fognak a konfiguráció memóriában tárolt példányával.
-   A saját üzemeltetésű átjárók leállítása a konfiguráció biztonsági másolatát fogja használni

Ha a kapcsolat helyreáll, a leállás által érintett összes saját üzemeltetésű átjáró automatikusan újra csatlakozik a társított API Management szolgáltatáshoz, és letölti az összes olyan konfigurációs frissítést, amely az átjáró kapcsolat nélküli üzemmódban történt.

## <a name="next-steps"></a>További lépések

-   [A témakör további hátteréről szóló tanulmány elolvasása](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [Saját üzemeltetésű átjáró üzembe helyezése a Docker-ben](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [Saját üzemeltetésű átjáró üzembe helyezése a Kubernetes-ben](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
