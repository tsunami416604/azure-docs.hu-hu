---
title: Üzembe helyezési architektúra – Azure dedikált HSM-be |} A Microsoft Docs
description: Alapvető tervezési szempontok Azure dedikált HSM-architektúra részeként használatakor
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
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048554"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Az Azure Dedicated HSM üzembe helyezési architektúrája

Az Azure, a dedikált HSM titkosítási kulcs tárolása az Azure biztosít. Ez megfelel a szigorú biztonsági követelmények. Ügyfelek számára az Azure dedikált HSM-be, ha azok:

* Meg kell felelnie a FIPS 140-2-3. szint tanúsítvánnyal
* A HSM kizárólagos hozzáféréssel rendelkeznek megkövetelése
* azok az eszközök teljes körű felügyeletet kell rendelkeznie.

A HSM-EK vannak elosztva a Microsoft-adatközpontok és a is könnyen üzembe helyezhető párban egy magas rendelkezésre állású megoldás alapjául szolgáló. Akkor is is üzembe helyezhetők rugalmas vészhelyreállítási megoldás-régiók között. A dedikált HSM-mel elérhető a régiók jelenleg:

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

Ezek a régiók mindegyike rendelkezik két független adatközpontban vagy a legalább két független rendelkezésre állási zónában üzembe helyezett HSM állványt. Délkelet-Ázsia három rendelkezésre állási zónák és USA 2. keleti régiójában két rendelkezik. Európa, Ázsia és a dedikált HSM szolgáltatást kínáló USA között összesen nyolc régióban van. Az Azure-régiókkal kapcsolatos további információkért tekintse meg a hivatalos [Azure-régiók információit](https://azure.microsoft.com/global-infrastructure/regions/).
A dedikált HSM-alapú megoldások tervezési tényezőket helye/késésű, magas rendelkezésre állást, és egyéb elosztott alkalmazások támogatása.

## <a name="device-location"></a>Eszközhely

Optimális HSM-eszköz helyét a titkosítási műveleteket végző alkalmazásokhoz legközelebb eső közelében van. Egy számjegyű ezredmásodperc régión belüli késés várható. Lehet, hogy a régiók közötti késés ennél magasabb 5-10-szerese.

## <a name="high-availability"></a>Magas rendelkezésre állás

Magas rendelkezésre állás, egy ügyfél egy régióban két olyan hardveres biztonsági modulokhoz konfigurált Gemalto szoftver segítségével magas rendelkezésre állású párban kell használnia. A központi telepítési típus kulcsok rendelkezésre állását biztosítja, ha egy adott eszköz során meggátolja, hogy kulcsfontosságú műveletek feldolgozása probléma lép fel. Is jelentősen csökken annak a kockázata, például power ellátási cseréje a hibajavítási karbantartás során. Fontos egy megtervezése és regionális szintű hiba bármilyen típusú tárfiókot. Regionális meghibásodások akkor fordulhat elő, ha például árvizek, földrengések vagy a hurrikánok természeti katasztrófák. Ilyen típusú események kell szüntethető meg egy másik régióban hardveres biztonsági modulokhoz kiépítése. Együtt egy másik régióban üzembe helyezett eszközök előfordulhat, hogy párosítva Gemalto szoftver konfigurálás révén. Ez azt jelenti, hogy a minimális telepítés magas rendelkezésre állású és vészhelyreállítási rugalmas megoldás négy hardveres biztonsági modulokhoz, két régióban is. Helyi redundanciát és régiók közötti redundancia használható kiindulópontként további HSM eszköz központi telepítéseket támogatja a késés, a kapacitás és a más alkalmazás-specifikus követelmények teljesítése érdekében adhat hozzá.

## <a name="distributed-application-support"></a>Elosztott alkalmazások támogatása

Dedikált HSM eszközök általában telepített kulcstároló és a kulcs adatbeolvasási műveletekkel igénylő alkalmazások támogatásához. Dedikált HSM eszközök 10 partíciók a független alkalmazástámogatási rendelkezik. Eszköz helye alapján kell a szolgáltatást használó alkalmazások holisztikus.

## <a name="next-steps"></a>Következő lépések

Üzembe helyezési architektúrája határozza meg, miután Gemalto biztosítja az architektúra megvalósítása a legtöbb konfigurációs tevékenységeket. Ez magában foglalja a eszközkonfiguráció, valamint az alkalmazás integrációs feladatokhoz. További információkért használja a [Gemalto ügyfélszolgálati](https://supportportal.gemalto.com/csm/) portálját, és töltse le az adminisztrációs és konfigurációs útmutatókat. A Microsoft partner hely integrációs útmutatóiban különféle rendelkezik.
Javasoljuk, hogy a szolgáltatás, például a magas rendelkezésre állással és biztonsággal kapcsolatos összes főbb fogalmakat például megértsük device Provisioning Service vagy alkalmazás-tervezés és üzembe helyezés előtt.
További fogalom szintű témakörök:

* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Támogathatóság](supportability.md)
* [Monitorozás](monitoring.md)
