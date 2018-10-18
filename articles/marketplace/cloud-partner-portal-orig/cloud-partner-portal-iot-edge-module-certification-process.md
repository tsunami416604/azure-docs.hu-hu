---
title: Az IoT Edge-modul hitelesítő |} A Microsoft Docs
description: Az IoT Edge-modul igazolnia a Marketplace-en.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: c37ed908b61ca54957affed3f81526353bc3f53b
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389564"
---
# <a name="the-iot-edge-module-certification-process"></a>Az IoT Edge-modul minősítési folyamat

Ez a cikk ismerteti a lépéseket és a egy IoT Edge-modul az Azure piactér közzétételre tanúsítására követelményeinek. 

>[!Note]
>Ez az ideiglenes dokumentum. Az IoT Edge modul piactér létrehozása folyamatban van, párhuzamos, és ez a cikk váltja nyilvános dokumentációt.

## <a name="understanding-an-iot-edge-module"></a>Az IoT Edge-modul ismertetése

A modul terminológia:

-   A **modul rendszerképének** a szoftvert, amely meghatározza egy modult tartalmazó csomag.

-   A **modulpéldány** az adott számítási egységek, a modul rendszerképének futó IoT Edge-eszköz van. A modul példány van indíthatja el az IoT Edge-futtatókörnyezet.

Modulok is tartalmazhat az IoT-modul SDK-t használja a következő kifejezésekkel:

-   A **modul identitás** olyan információk (beleértve a biztonsági hitelesítő adatok) az IoT hubon, az egyes modulpéldány társított tárolt.

-   A **ikermodul** az IoT hubon, egy modul példányt, beleértve a metaadatokat, konfigurációkat és állapotokat állapotinformációinak tartalmazó tárolt JSON-dokumentumok.

-   **SDK-k** egyéni modulok több nyelven is, például a fejlesztéshez használt: C\#, C, Python, Java és Node.JS.

## <a name="the-onboarding-process-for-an-iot-edge-module"></a>A bevezetési folyamat az IoT Edge-modul

Az alábbi képernyőfelvétel bemutatja a egy IoT Edge-modul az Azure piactér nyilvános lesz.

![Minősítési folyamat](./media/cloud-partner-portal-iot-edge-module-certification-process/onboarding-process.png)

### <a name="onboarding-step-details"></a>Előkészítési lépés részletei

-   **1. lépés** -partnereink ajánlatait nyújt az ajánlat típusú **IoT Edge-modul** a Cloud Partner Portalon eszközben az Azure Marketplace csapata által. Rendelkeznie kell egy hivatalos MS-partner a Cloud Partner Portalon eszköz eléréséhez. További információkért lásd: a [kiadó útmutató](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).

-   **2. lépés** – a piactéren automatikusan futtatja a víruskereső és kártevőirtó ellenőrzi. Az IoT-csapat futtathatja saját egyéni automatizált adatfeldolgozási tesztek ezen szakasz során.

-   **3. lépés** – a modul az nyilvános. A Marketplace-en szerepel, és a tároló lekérhetik névtelenül URL-címről. Ha például *marketplace.azurecr.io/module-identifier*.

## <a name="iot-edge-module-certification-criteria"></a>IoT Edge-modul hitelesítési feltételeknek

Az alábbiakban a fő követelményei, amelyeknek teljesülniük kell az IoT Edge-modul hitelesített és az Azure Marketplace-en közzétett.

>[!Note]
>Ezek a követelmények változhatnak. Előzetesen értesítést is, és a rendszer megadott idő, frissítse a tárolókat, hogy a frissített követelményeinek is megfelelnek.

### <a name="technical-requirements"></a>Technikai követelmények

**Kell egy IoT Edge-modul**

-   Jelenleg csak a docker-kompatibilis tárolók IoT Edge-modulok támogatottak. A modul rendszeren kell futnia [Moby](https://mobyproject.org/). 

    >[!Note]
    >A docker-tárolók valószínűleg fog dolgozni Moby, mert Moby Docker az alapul szolgáló nyílt forráskódú projekt.

-   A partner meg kell adnia az alábbi alapértelmezett beállításokkal: 

    -   Alapértelmezett **címke** (amely nem lehet üres.

    -   Alapértelmezett **createOptions** (amely lehet üres.)

    -   Ha az IoT modul SDK-t, egy alapértelmezett **ikereszköz** (amely lehet üres.)

    -   Ha az IoT modul SDK-t, egy alapértelmezett **útvonalak** (amely lehet üres.)

**Eszközplatform-támogatás**

-   Csak azok a rendszerek, amelyek **általánosan elérhető** az IoT Edge 1. rétegbeli (feljegyzett [Azure IoT Edge-támogatás](https://docs.microsoft.com/azure/iot-edge/support)) kell támogatni. Például ez jelenleg azt jelenti, hogy a következő operációs rendszer és architektúra kombinációk támogatása:

    -   Ubuntu Server 18.04 x64

    -   Ubuntu Server 16.04 x64

    -   Raspbian – stretch számára arm32 (armhf)

**A méretezési eszköz**

-   Minden olyan eszközt, az egyenértékű dimenziók (CPU/RAM/Storage/GPU/és stb.) egy Raspberry Pi vagy nagyobb lehet IoT Edge-eszköz. A modul csak az adott dimenziót megkötések belül működik, ha ezeket a korlátozásokat a modul leírásában meg kell adni.

**Alapértelmezett beállítások**

-   Egy modul az alapértelmezett paraméterek beépített támogatott platformonként (operációs rendszer + architektúra) kell kezdődnie.

-   Konfigurációs beállításainak kell jól dokumentált (címkék, a környezeti változók, ikereszköz, útvonalak.) Az eredményablakban részeként a partnerek a modul, amely támogatja az alapszintű HTML-kód vagy egy külső weblapra mutató adhatja meg egy leírást.

**Verziókezelés**

-   Felhasználók kiválasztása a marketplace-ről várható modul automatikus frissítésének szeretne akár Ha szeretne egy pontos verzióját használja, tesztelését képesnek kell lennie. Marketplace-en modulok, az IoT Edge-futtatókörnyezet versioning minta kell követnie. Példa:

    -   A működés közbeni címkéket "1.0", például frissítheti.

    -   Alverzió címkéket, például a "1.3.24-es verzióját", nem lehet frissíteni.

**Telemetria**

-   Az IoT-modul SDK-val modulok be kell állítania a modul egyedi azonosító által a piactéren telemetriai célból hozzárendelt. Ez lehetővé teszi az Azure Marketplace-en futó példányok modul azonosításához. Ez az egyedi azonosító által a piactéren, adatfeldolgozási a tároló neve. Ez az azonosító beállításához használja a következő SDK-k a módszerek:
    - [C\#](https://hub.docker.com/_/mysql/)
    - [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._product_info?view=azure-java-stable)

-   Az modulokat, amelyek nem használják az IoT-modul SDK-t, a kevésbé pontos insights a Cloud Partner Portalon keresztül érhető el. Ha például a letöltések számát.

**Biztonság**

-   Tárolók kell a gazdagép, a lehető legalacsonyabb jogosultsági szintű hozzáféréssel rendelkeznek. Lehet, hogy a "Kiemelt" tárolók nagyon ritkán fordul elő.

-   Partnerek kell tartania a modul biztonságossá tétele, hogy ezek biztosítják a támogatási részeként.

**Frissítések**

-   Partnerek kell tartania a modul, naprakész és megfelelően működik. Ezek értesítést is előre bármely kompatibilitástörő változásokat tervezünk IoT Edge-futtatókörnyezet-e.

**A modul IoT SDK-val**

-   Minősítési előfeltétele többek között az IoT-modul SDK-t nem.
    Azonban például az IoT-modul SDK-val rendelkezhetnek jobb felhasználói élményt. Ha például útválasztás üzeneteket küld a felhőben, és így tovább. Az IoT-modul SDK-val máris a modul futó példányok számának van szükség.

**Szubjektív követelmény**

-   Legalább egy valós használati eset az IoT Edge meg kell felelnie. Például egy WordPress-tároló nem lehet előkészíteni, ha egy ügyfél nem hajlandó vele az IoT Edge-ből.

**Jogi követelmények (AMP házirendből)**

-   A modul a Microsoft Azure Marketplace-en szerződések és szabályzatok meg kell felelnie. További információkért tekintse meg a csatolt Kiadókra vonatkozó szerződése és [részvételi házirend](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).

-   Az Azure Marketplace-en szerződések és szabályzatok kívül is további jogi követelmények egy IoT Edge-modul ajánlattípusra vonatkozó. Ez a funkció jelenleg felülvizsgálat alatt.

-   A modul rendelkeznie kell egy *használati feltételeket tartalmazó fájl* és a egy *adatvédelmi szabályzatát*.

-   A modul is kell rendelkeznie a külső gyártótól származó figyelje meg, ha a modul használja bármely harmadik félnek.

**Támogatási követelmények (AMP házirendből)**

-   Partnerek az IoT Edge-modulok támogatásáért felelős. Biztosítják, hogy IoT Edge-modul leírásában támogatási lehetőségek elérhetők maradjanak, és hogy legalább egy támogatási lehetőséget mindig elérhető lesz. (Tekintse meg a további részletekért és kiadói szerződés szakasz 4.)

**Kategorizálási**

-   Minden IoT Edge-modulok jelenik meg a kategória alatt **eszközök internetes hálózata \>IoT Edge-modul**. A legjobb alkategóriája a termék kiválasztása a partnernek van.
