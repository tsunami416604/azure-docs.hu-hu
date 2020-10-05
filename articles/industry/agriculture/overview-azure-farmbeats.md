---
title: Mi az Azure FarmBeats?
description: Áttekintést nyújt az Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 12bb0b0098b5108bf780b88fc42b86861ea6fcdc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87439554"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Az Azure FarmBeats áttekintése (előzetes verzió)

Az Azure FarmBeats egy, az Azure Marketplace-en elérhető üzleti célú ajánlat. Lehetővé teszi a mezőgazdasági adathalmazok összesítését a szolgáltatók között. Az Azure FarmBeats lehetővé teszi a mesterséges intelligencia (AI) vagy gépi tanulási (ML) modellek összeolvasztott adatkészleteken alapuló összeállítását. Az Azure FarmBeats használatával a mezőgazdasági vállalkozások a legfontosabb értékekre összpontosítanak, nem pedig az adatkezelés nem differenciált nagy mennyiségű emelését.

> [!NOTE]
> Az Azure FarmBeats jelenleg nyilvános előzetes verzióban érhető el. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Az Azure-FarmBeats szolgáltatói szerződés nélkül biztosítjuk. Támogatásért használja az [Azure FarmBeats támogatási fórumát](https://aka.ms/farmbeatssupport) .

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

## <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)

A rendszergazdák az előre definiált szerepkörök egyikével meghatározhatják az Azure FarmBeats hozzáférési szabályait. A szerepkörök határozzák meg, hogy az alkalmazás mely területeihez férhet hozzá a felhasználók, és milyen műveleteket végezhetnek el. Az Azure FarmBeats kétféle szerepkört biztosít a felhasználók és a partnerek számára.

### <a name="user-roles"></a>Felhasználói szerepkörök

A rendszergazdák két felhasználói szerepkör alapján adhatnak [hozzá és kezelhetnek felhasználókat](manage-users-in-azure-farmbeats.md) , és meghatározhatják hozzáférési szintjeiket: rendszergazda és csak olvasható.

### <a name="partner-roles"></a>Partneri szerepkörök

A rendszergazdák több partnert is hozzáadhatnak adatszolgáltatóként az Azure FarmBeats. A következő összefoglalja a FarmBeats-ben elérhető partneri szerepköröket és azok engedélyeit:

| Partner típusa    |   Műveletek  | Hatókör |
| ---- | -------- | -------- |
| Érzékelő partner  |   Létrehozás, olvasás, frissítés <br/> <br/> Olvasás, frissítés | DeviceModel, eszköz, SensorModel, érzékelő <br/> <br/> ExtendedType |
| Képekkel rendelkező partner  |   Létrehozás, olvasás, frissítés <br/> <br/> Olvasás, frissítés <br/> <br/> Olvasás | Jelenet, SceneFile <br/> <br/> ExtendedType <br/> <br/> Farm |
| Időjárási partner * <br/> <br/>  (* Hamarosan érkezik) |   Létrehozás, olvasás, frissítés <br/> <br/> Olvasás, frissítés <br/> <br/> Olvasás | WeatherDataModel, WeatherDataLocation, JobType <br/> <br/> ExtendedType <br/> <br/> Farm |

## <a name="resources"></a>További források

Az Azure FarmBeats díjmentesen vehető igénybe, és csak a használt Azure-erőforrásokért kell fizetnie. Az alábbi források segítségével többet tudhat meg az ajánlatról:

- Tekintse meg a legújabb Azure FarmBeats híreket, ha meglátogatja az [Azure FarmBeats blogját](https://aka.ms/farmbeatsblog).
- Kérjen segítséget az [Azure FarmBeats támogatási fórumának](https://aka.ms/farmbeatssupport)kérdéseivel.
- Küldjön visszajelzést az [Azure FarmBeats visszajelzési fórumának](https://aka.ms/farmbeatsfeedback)funkcióinak közzétételével vagy szavazásával.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure FarmBeats telepítése](install-azure-farmbeats.md)
