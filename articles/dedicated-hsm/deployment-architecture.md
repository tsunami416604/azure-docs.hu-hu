---
title: Telepítési architektúra - Azure dedikált HSM | Microsoft dokumentumok
description: Alapvető tervezési szempontok az Azure dedikált HSM alkalmazásarchitektúra részeként történő használatakor
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 89e3bf95a6b048e5e97cfb151ef9302b70eac1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048554"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Az Azure Dedicated HSM üzembe helyezési architektúrája

Az Azure dedikált HSM kriptográfiai kulcstárolást biztosít az Azure-ban. Megfelel a szigorú biztonsági követelményeknek. Az ügyfelek számára előnyös az Azure Dedikált HSM használata, ha:

* Meg kell felelnie a FIPS 140-2 Level 3 minősítésnek
* Megkövetelik, hogy kizárólagos hozzáféréssel rendelkezhessenek a HSM-hez
* teljes ellenőrzéssel kell rendelkezniük az eszközeik felett

A HSM-ek a Microsoft adatközpontjaiközött vannak elosztva, és könnyen kiépíthetők eszközpárként egy magas rendelkezésre állású megoldás alapjaként. Emellett a régiók között is üzembe helyezhetők egy katasztrófa-rugalmas megoldás. A jelenleg rendelkezésre álló dedikált HSM-mel rendelkező régiók a következők:

* USA keleti régiója
* USA 2. keleti régiója
* USA nyugati régiója
* USA déli középső régiója
* Délkelet-Ázsia
* Kelet-Ázsia
* Közép-India
* Dél-India
* Kelet-Japán
* Nyugat-Japán
* Észak-Európa
* Nyugat-Európa
* Az Egyesült Királyság déli régiója
* Az Egyesült Királyság nyugati régiója
* Közép-Kanada
* Kelet-Kanada
* Kelet-Ausztrália
* Délkelet-Ausztrália

Ezek a régiók mindegyike hsm állványok üzembe helyezett vagy két független adatközpontok, vagy legalább két független rendelkezésre állási zónák. Délkelet-Ázsiában három rendelkezésre állási zóna, az USA keleti része 2 pedig kettő. Európa, Ázsia és az USA összesen nyolc régiója kínálja a dedikált HSM szolgáltatást. Az Azure-régiókról az [Azure-régiókkal kapcsolatos](https://azure.microsoft.com/global-infrastructure/regions/)hivatalos információkban talál.
Bármely dedikált HSM-alapú megoldás tervezési tényezői a hely/késés, a magas rendelkezésre állás és más elosztott alkalmazások támogatása.

## <a name="device-location"></a>Eszközhely

Az optimális HSM-eszközhely a kriptográfiai műveleteket végző alkalmazások közelében van. A régión belüla késés várhatóan egyszámjegyű ezredmásodperc. A régióközi késés ennél 5–10-szer magasabb lehet.

## <a name="high-availability"></a>Magas rendelkezésre állás

A magas rendelkezésre állás elérése érdekében az ügyfélnek két HSM-eszközt kell használnia egy olyan régióban, amely a Gemalto szoftvert használja magas rendelkezésre állású párként. Az ilyen típusú központi telepítés biztosítja a kulcsok rendelkezésre állását, ha egyetlen eszköz nél kondició megakadályozza a kulcsműveletek feldolgozását. Jelentősen csökkenti a kockázatot a törés/javítás karbantartása, például a tápegység cseréje során. Fontos, hogy a formatervezési minta figyelembe vegye a regionális szintű hibák minden fajtáját. Regionális szintű hibák akkor fordulhatnak elő, ha természeti katasztrófák, például hurrikánok, árvizek vagy földrengések vannak. Az ilyen típusú eseményeket csökkenteni kell a HSM-eszközök kiépítésével egy másik régióban. Egy másik régióban telepített eszközök párosíthatók a Gemalto szoftverkonfiguráción keresztül. Ez azt jelenti, hogy a magas rendelkezésre állású és katasztrófa-elhárítási megoldás minimális üzembe helyezése négy HSM-eszköz két régióban. A régiók közötti helyi redundancia és redundancia kiindulási alapként használható további HSM-eszköztelepítések hozzáadásához a késés, a kapacitás vagy más alkalmazásspecifikus követelmények teljesítéséhez.

## <a name="distributed-application-support"></a>Elosztott alkalmazás támogatása

A dedikált HSM-eszközök általában olyan alkalmazások támogatására vannak telepítve, amelyeknek kulcstárolási és kulcslekéréses műveleteket kell végrehajtaniuk. A dedikált HSM-eszközök 10 partícióval rendelkeznek a független alkalmazástámogatáshoz. Az eszköz helyének a szolgáltatás használatához szükséges összes alkalmazás holisztikus nézetén kell alapulnia.

## <a name="next-steps"></a>További lépések

A telepítési architektúra meghatározása után a legtöbb konfigurációs tevékenységet az architektúra megvalósításához a Gemalto biztosítja. Ez magában foglalja az eszköz konfigurációját, valamint az alkalmazásintegrációs forgatókönyveket. További információkért használja a [Gemalto ügyfélszolgálati](https://supportportal.gemalto.com/csm/) portálját, és töltse le a felügyeleti és konfigurációs útmutatókat. A Microsoft partnerwebhelye számos integrációs útmutatóval rendelkezik.
Javasoljuk, hogy a szolgáltatás minden kulcsfontosságú fogalma, például a magas rendelkezésre állás és a biztonság, például, jól érthető, mielőtt az eszköz kiépítése vagy az alkalmazás tervezése és üzembe helyezése.
További koncepciószintű témák:

* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
* [Megfigyelő](monitoring.md)
