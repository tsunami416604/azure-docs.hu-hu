---
title: Mi az Azure FarmBeats
description: Áttekintést nyújt az Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 38f9f85a7e961d426b66a24bb4a5c63f9f0301da
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80638066"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Az Azure FarmBeats áttekintése (előzetes verzió)

Az Azure FarmBeats egy olyan vállalkozáson belüli ajánlat, amely elérhető az Azure Piactéren. Lehetővé teszi a mezőgazdasági adatkészletek szolgáltatók közötti összesítését. Az Azure FarmBeats lehetővé teszi, hogy mesterséges intelligencia (AI) vagy gépi tanulási (ML) modelleket hozzon létre olvasztott adatkészletek alapján. Az Azure FarmBeats használatával a mezőgazdasági vállalkozások az alapvető értéknövelésekre összpontosíthatnak az adattervezés differenciálatlan nehéz emelése helyett.

> [!NOTE]
> Az Azure FarmBeats jelenleg nyilvános előzetes verzióban érhető el. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Az Azure FarmBeats szolgáltatásszint-szerződés nélkül biztosított. Támogatáshoz használja az [Azure FarmBeats fórumot.](https://aka.ms/FarmBeatsMSDN )

![Projekt Farm Beats](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Az Azure FarmBeats előzetes verziójával a következőket teheti:

- A mezőgazdasági üzem egészségi állapotának felmérése a vegetációs index és a vízindex alapján műholdas felvételek alapján.
- Ajánlásokat kaphat arra vonatkozóan, hogy hány talajnedvesség-érzékelőt kell használni, és hová kell elhelyezni őket.
- Kövesse nyomon a farmok körülményeit a különböző gyártók érzékelői által gyűjtött földi adatok megjelenítésével.
- Kap a talaj nedvesség térkép alapján a fúziós műholdas és érzékelő adatok.
- Az AI/ML modellek összesített adatkészletekre való felépítésével használható információkhoz juthat.
- Építse fel vagy bővítse digitális mezőgazdasági megoldását a mezőgazdasági egészségügyi tanácsadással.

## <a name="datahub"></a>Datahub (Adatközpont)

Az Azure FarmBeats Datahub egy API-réteg, amely lehetővé teszi a különböző mezőgazdasági adatkészletek összesítését, normalizálását és környezetnyelvi vétését a szolgáltatók között. Az Azure FarmBeats segítségével a következőket kaphatja:
- Két érzékelőszolgáltató, [a Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/), [teralitikus](https://teralytic.com/), [Pessl Instruments](https://metos.at/) **érzékelőadatai**
- **Műholdas képek az** Európai Űrügynökség [Sentinel-2](https://sentinel.esa.int/web/sentinel/home) műholdküldetéséről
- **Drone képek** három drone képszolgáltatók [senseFly](https://www.sensefly.com/) , [SlantRange](https://slantrange.com/) , [DJI](https://dji.com/)

A Datahub bővíthető API-platformként lett tervezve. Sokkal több szolgáltatóval dolgozunk együtt az Azure FarmBeats szolgáltatással való integráción, így több választási lehetősége van a megoldás létrehozása során.

## <a name="accelerator"></a>Gyorsító

Az Azure FarmBeats accelerator egy minta webalkalmazás, amely a Datahub ra épül. A gyorssegéd ugrás-elindítja a felhasználói felület és a modell fejlesztése. Az Azure FarmBeats gyorsító az Azure FarmBeats API-kat használja. A bevitt érzékelőadatokat diagramként és modellkimenetként ábrázolja térképként. A gyorsító val gyorsan létrehozhat egy farmot, és könnyen lekaphat egy vegetációs indextérképet vagy egy érzékelőelhelyezési térképet az adott farmhoz.

## <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)

A rendszergazda hozzáférési szabályokat definiálhat az Azure FarmBeats-hez az előre definiált szerepkörök egyikének használatával. A szerepkörök határozzák meg, hogy a felhasználó milyen területeken férhet hozzá a felhasználóhoz, és milyen műveleteket hajthat végre. Az Azure FarmBeats-ben kétféle szerepkör létezik – a felhasználók és a partnerek számára.

### <a name="user-roles"></a>Felhasználói szerepkörök

A [rendszergazda felhasználókat vehet fel és kezelhet,](manage-users-in-azure-farmbeats.md) és hozzáférési szintjüket két felhasználói szerepkör alapján határozhatja meg: rendszergazdaés írásvédett.

### <a name="partner-roles"></a>Partneri szerepkörök

A rendszergazda több partnert is hozzáadhat adatszolgáltatóként az Azure FarmBeats-hez. A farmbeats-ben rendelkezésre álló partnerszerepköröket és azok engedélyeit összegezve:

| Partner típusa    |   Műveletek  | Hatókör |
| ---- | -------- | -------- |
| Érzékelő partner  |   Létrehozás, olvasás, frissítés <br/> <br/> Olvasás, frissítés | DeviceModel, Eszköz, SensorModel, Érzékelő <br/> <br/> Kiterjesztett típus |
| Képi partner  |   Létrehozás, olvasás, frissítés <br/> <br/> Olvasás, frissítés <br/> <br/> Olvasás | Jelenet, Jelenetfájl <br/> <br/> Kiterjesztett típus <br/> <br/> Farm |
| Képi partner  |   Létrehozás, olvasás, frissítés <br/> <br/> Olvasás, frissítés <br/> <br/> Olvasás | WeatherDataModel, WeatherDataLocation, Feladattípus <br/> <br/> Kiterjesztett típus <br/> <br/> Farm |

## <a name="resources"></a>További források

Az Azure FarmBeats-t díjmentesen kínáljuk, és csak a használt Azure-erőforrásokért kell fizetnie. Az alábbi források segítségével többet tudhat meg az ajánlatról:

- Tájékozódjon az Azure FarmBeats legfrissebb híreiről az [Azure FarmBeats blogunk](https://aka.ms/farmbeatsblog)felkeresésével.
- Kérjen segítséget az Azure [FarmBeats támogatási fórumán](https://aka.ms/farmbeatssupport)közzétett kérdésekkel.
- Visszajelzésküldéset az [Azure FarmBeats visszajelzési fórumunkon](https://aka.ms/farmbeatsfeedback)közzétett vagy megszavazott funkcióötlettel.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure FarmBeats telepítése](install-azure-farmbeats.md)
