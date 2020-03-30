---
title: Mi az Azure FarmBeats
description: Áttekintést nyújt az Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: b067b18985905b226287f9dd10ad4b937fab6df1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76767971"
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

## <a name="resources"></a>Források

Az Azure FarmBeats-t díjmentesen kínáljuk, és csak a használt Azure-erőforrásokért kell fizetnie. Az alábbi források segítségével többet tudhat meg az ajánlatról:

- Tájékozódjon az Azure FarmBeats legfrissebb híreiről az [Azure FarmBeats blogunk](https://aka.ms/farmbeatsblog)felkeresésével.
- Kérjen segítséget az Azure [FarmBeats támogatási fórumán](https://aka.ms/farmbeatssupport)közzétett kérdésekkel.
- Visszajelzésküldéset az [Azure FarmBeats visszajelzési fórumunkon](https://aka.ms/farmbeatsfeedback)közzétett vagy megszavazott funkcióötlettel.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure FarmBeats telepítése](install-azure-farmbeats.md)
