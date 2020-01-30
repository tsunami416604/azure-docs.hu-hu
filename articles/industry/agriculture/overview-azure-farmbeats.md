---
title: Mi az Azure FarmBeats?
description: Áttekintést nyújt az Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: b067b18985905b226287f9dd10ad4b937fab6df1
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76767971"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Az Azure FarmBeats áttekintése (előzetes verzió)

Az Azure FarmBeats egy, az Azure Marketplace-en elérhető üzleti célú ajánlat. Lehetővé teszi a mezőgazdasági adathalmazok összesítését a szolgáltatók között. Az Azure FarmBeats lehetővé teszi a mesterséges intelligencia (AI) vagy gépi tanulási (ML) modellek összeolvasztott adatkészleteken alapuló összeállítását. Az Azure FarmBeats használatával a mezőgazdasági vállalkozások a legfontosabb értékekre összpontosítanak, nem pedig az adatkezelés nem differenciált nagy mennyiségű emelését.

> [!NOTE]
> Az Azure FarmBeats jelenleg nyilvános előzetes verzióban érhető el. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Az Azure-FarmBeats szolgáltatói szerződés nélkül biztosítjuk. Támogatásért használja az [Azure FarmBeats fórumot](https://aka.ms/FarmBeatsMSDN ) .

![A Project Farm veri](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Az Azure FarmBeats előzetes verziójában a következőket teheti:

- A farm állapotának felmérése a vegetációs index és a vízindex használatával, a műholdképek alapján.
- Javaslatokat kaphat arról, hogy hány nedvesség-érzékelőt használ, és hová helyezheti őket.
- Nyomon követheti a farm feltételeit, ha a különböző gyártóktól származó érzékelők által gyűjtött alapvető adatokat jeleníti meg.
- A talaj nedvességtartalmának leképezése a műhold és az érzékelő adategyesítése alapján.
- Az összesített adathalmazok alapján az AI/ML modellek kiépítése révén hasznosítható elemzéseket nyerhet.
- A farm Health Advisor szolgáltatásával kiépítheti vagy kiegészítheti a digitális mezőgazdaság megoldását.

## <a name="datahub"></a>Datahub

Az Azure FarmBeats Datahub egy API-réteg, amely lehetővé teszi különböző mezőgazdasági adatkészletek összesítését, normalizálása és contextualization a szolgáltatók között. Az Azure FarmBeats a következőket érheti el:
- **Érzékelői adatok** két érzékelő szolgáltató [Davis-eszközökről](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), [Pessl Instruments-eszközökről](https://metos.at/)
- **Műhold-rendszerképek** az Európai Űrügynökség [Sentinel-2 műhold-](https://sentinel.esa.int/web/sentinel/home) missziójáról
- **Drone-képek** három drone-rendszerképekből [a senseFly](https://www.sensefly.com/) , a [SlantRange](https://slantrange.com/) , a [DJI](https://dji.com/)

A Datahub bővíthető API-platformként lett kialakítva. Több szolgáltatóval dolgozunk együtt az Azure FarmBeats való integrációval, így a megoldás kialakítása során több lehetőség is van.

## <a name="accelerator"></a>Accelerator

Az Azure FarmBeats Accelerator egy minta webalkalmazás, amely a Datahub-re épül. A Gyorssegéd-ugrás elindítja a felhasználói felületet és a modell fejlesztését. Az Azure FarmBeats Accelerator Azure FarmBeats API-kat használ. Térképként jeleníti meg a betöltött érzékelő adatokat diagramok és modellek kimenetként. A Gyorssegéd használatával például gyorsan létrehozhatja a farmokat, és könnyen elvégezheti a vegetációs index térképét vagy az érzékelő elhelyezési térképét.

## <a name="resources"></a>Segédanyagok és eszközök

Az Azure FarmBeats díjmentesen vehető igénybe, és csak a használt Azure-erőforrásokért kell fizetnie. Az alábbi források segítségével többet tudhat meg az ajánlatról:

- Tekintse meg a legújabb Azure FarmBeats híreket, ha meglátogatja az [Azure FarmBeats blogját](https://aka.ms/farmbeatsblog).
- Kérjen segítséget az [Azure FarmBeats támogatási fórumának](https://aka.ms/farmbeatssupport)kérdéseivel.
- Küldjön visszajelzést az [Azure FarmBeats visszajelzési fórumának](https://aka.ms/farmbeatsfeedback)funkcióinak közzétételével vagy szavazásával.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure FarmBeats telepítése](install-azure-farmbeats.md)
