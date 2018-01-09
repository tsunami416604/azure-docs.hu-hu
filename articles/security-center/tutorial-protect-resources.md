---
title: "Az Azure Security Center útmutató – az Azure Security Center az erőforrások védelme |} Microsoft Docs"
description: "Ez az oktatóanyag bemutatja, hogyan beállítása csak virtuális gép időben hozzáférési házirend és az Alkalmazásvezérlési házirendet."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/08/2018
ms.author: terrylan
ms.openlocfilehash: f0a32f90e68101f805a52427fab2d5bb29b94939
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Oktatóanyag: Az Azure Security Center az erőforrások védelme
A Security Center korlátozza a lehetősége, hogy a fenyegetések blokkolja a rosszindulatú tevékenységhez hozzáférést és az alkalmazás használatával. Csak az idő a virtuális gép (VM) hozzáférés csökkenti a lehetősége, hogy a támadások azáltal, hogy a virtuális gépek állandó hozzáférés megtagadja. Ehelyett kívánja megadni a ellenőrzött és naplózott hozzáférést a virtuális gépek csak szükség esetén. Adaptív alkalmazás vezérlők segítségével virtuális gépeket alakuljanak kártevő szoftverek elleni vezérlése, mely alkalmazások futtathatja a virtuális gépek. A Security Center gépi tanulási módszerekkel elemzi a virtuális gépen futó folyamatokat, és az így szerzett információk alapján segít az engedélyezési szabályok alkalmazásában.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Beállítása csak virtuális gép időben hozzáférés-házirend
> * Egy alkalmazás-vezérlési házirend beállítása

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
A szolgáltatások ebben az oktatóanyagban szereplő lépések, a Security Center a Standard tarifacsomag kell lennie. Próbálja meg biztonsági központ szabványos ingyenesen az első 60 nap. A gyors üzembe helyezés [Onboard a Security Center Standard Azure-előfizetése](security-center-get-started.md) végigvezeti a Standard frissítése.

## <a name="manage-vm-access"></a>VM-hozzáférés kezelése
JIT VM hozzáférés segítségével az Azure virtuális gépeken, támadásoknak való kitettség csökkentése során könnyen hozzá lehet férni a virtuális gépekhez, szükség esetén csatlakoztassa a bejövő forgalom zárolását.

JIT VM hozzáférés előzetes verzió van.

Szolgáltatásfelügyelet portjai nem kell mindig kapcsolódniuk kell megnyitni. Csak szükségük lehet megnyitva, amikor csatlakoznak a virtuális Gépet, például felügyeleti vagy karbantartási feladatok elvégzésére. Ha JIT engedélyezve van, a Security Center kezelőportjai való hozzáférés korlátozása, így nem tudja megcélozni a támadók hálózati biztonsági csoport (NSG) szabályokat alkalmaz a.

1. A Security Center főmenü válassza **közvetlenül a virtuális gép elérhető** alatt **speciális FELHŐ védelmi**.

  ![Virtuális gépek igény szerinti elérése][1]

  **Csak a virtuális gép elérhető** információt nyújt a virtuális gépek állapotát:

  - **Konfigurált** -virtuális gépek, amelyek csak a virtuális gép elérhető támogatására vannak konfigurálva.
  - **Ajánlott** -csak a virtuális gép elérhető támogatható, de nincs beállítva a virtuális gépeket.
  - **Nincs javaslat** – ennek oka, hogy egy virtuális Gépet nem lehet ajánlott okozhatják:

    - Hiányzik az NSG - a csak időbeli megoldáshoz szükségesek egy NSG-t kell.
    - Klasszikus VM - VM elérhető csak a Security Center jelenleg csak virtuális gépek Azure Resource Manager használatával telepített.
    - Ebbe a kategóriába tartozó egyéb - egy virtuális gép van Ha pedig csak a megoldás ki van kapcsolva a biztonsági szabályzatban az előfizetés vagy az erőforráscsoportot, vagy időpontban a virtuális gép hiányzik egy nyilvános IP-cím, és nem rendelkezik egy NSG.

2. Jelöljön ki egy ajánlott virtuális Gépet, majd **JIT engedélyezése az 1 virtuális gép** beállítása csak idő házirendben ezt a virtuális gépet:

  Mentheti az alapértelmezett portok, amely a Security Center javasolja, vagy adja hozzá, és engedélyezze a csak szeretné új port konfigurálása idő megoldásban. Ebben az oktatóanyagban adjuk hozzá egy portot kiválasztásával **Hozzáadás**.

  ![Konfiguráció hozzáadása][2]

3. A **Hozzáadás portkonfigurációjának**, azonosítja:

  - A port
  - A protokolltípus
  - Engedélyezett IP-címek - forrás IP-címtartományok engedélyezett is elérheti az engedélyezett kérésre
  - Kérések maximális idő - maximális időszak, hogy egy adott port használatával megnyitható

4. Válassza ki **OK** mentéséhez.

## <a name="harden-vms-against-malware"></a>Virtuális gépek alakuljanak kártevő szoftverek elleni
Adaptív alkalmazás vezérlők segítségével határozhatók meg, alkalmazásokat, amelyek futhatnak az beállított erőforráscsoportokkal, olyan készlete, amely többek között a más előnyöket segít a virtuális gépek kártevő szoftverek elleni korlátozhatják. A Security Center gépi tanulási módszerekkel elemzi a virtuális gépen futó folyamatokat, és az így szerzett információk alapján segít az engedélyezési szabályok alkalmazásában.

Adaptív alkalmazások ellenőrzése jelenleg előzetes verzióban érhető. Ez a funkció a Windows-alapú gépek csak érhető el.

1. Térjen vissza a Security Center főmenübe. A **speciális FELHŐ védelmi**, jelölje be **adaptív alkalmazás vezérlők**.

   ![Adaptív alkalmazásvezérlők][3]

  A **erőforráscsoportok** szakasz tartalmaz a három lappal:

  - **Konfigurált**: erőforrás listája csoportosítja az alkalmazás-vezérléssel konfigurált virtuális gépeket tartalmazó.
  - **Ajánlott**: mely alkalmazások vezérlő ajánlott erőforráscsoportok listáját.
  - **Nincs javaslat**: erőforrás listája csoportok virtuális gépeket tartalmazó alkalmazás vezérlő javaslatokkal nélkül. például olyanokat, amelyeken az alkalmazások folyamatosan változnak, és még nem értek el egy stabil állapotot.

2. Válassza ki a **ajánlott** lapon vezérlő ajánlásokkal erőforráscsoportok listáját.

  ![Alkalmazás vezérlő javaslatok][4]

3. Válasszon egy erőforráscsoportot megnyitásához a **alkalmazás vezérlési szabályok létrehozása** lehetőséget. A **Select VMs** (Virtuális gépek kiválasztása) felületen tekintse át a javasolt virtuális gépek listáját, és törölje a jelölést azok mellől, amelyeket nem szeretne bevonni az alkalmazásvezérlés hatókörébe. **Az engedélyezési szabályokhoz tartozó folyamatok kiválasztása** felületen tekintse át a javasolt alkalmazások listáját, és törölje a jelölést azok mellől, amelyeket nem szeretne alkalmazni. A lista a következőket tartalmazza:

  - **NÉV**: az alkalmazás teljes elérési útja
  - **FOLYAMATOK**: alkalmazások található minden elérési útja
  - **KÖZÖS**: "Yes" azt jelzi, hogy végrehajtotta-e ezeket a folyamatokat, ez az erőforráscsoport a legtöbb virtuális gépeken
  - **KIHASZNÁLHATÓ**: figyelmeztető ikon azt jelzi, ha az alkalmazások használható egy támadó megkerülése alkalmazások engedélyezése. Érdemes áttekinteni ezeket az alkalmazásokat az engedélyezésük előtt.

4. A kiválasztások befejezése után válassza a **létrehozása**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Más quickstarts és a gyűjtemény-kra vonatkozó a gyors üzembe helyezés épül. Ha azt tervezi, a későbbi quickstarts és oktatóanyagok munka folytatásához, továbbra is fut, a Standard csomagot, és tartsa be az Automatikus kiépítés engedélyezve van. Ha nem tervezi a folytatáshoz, vagy visszatérhet az ingyenes csomagot szeretne:

1. Térjen vissza a Security Center főmenübe, és válassza ki **biztonsági házirend**.
2. Válassza ki az előfizetés vagy való visszatéréshez szabad beállítani kívánt szabályzatot. **Biztonsági házirend** nyílik meg.
3. A **csoportházirend-összetevők**, jelölje be **tarifacsomag**.
4. Válassza ki **szabad** módosítása a Standard szint az ingyenes csomagba.
5. Kattintson a **Mentés** gombra.

Ha szeretne automatikus kiépítés letiltása:

1. Térjen vissza a Security Center főmenübe, és válassza ki **biztonsági házirend**.
2. Válassza ki az előfizetést, amelyet meg kíván automatikus kiépítés letiltása.
3. A **biztonsági szabályzat – adatgyűjtés**, jelölje be **ki** alatt **bevezetési** automatikus kiépítés letiltása.
4. Kattintson a **Mentés** gombra.

>[!NOTE]
> Automatikus kiépítés letiltása nem a Microsoft Monitoring Agent eltávolítása Azure virtuális gépeken, ha az ügynök van kiépítve. Az automatikus létesítési korlátok biztonsági megfigyelés letiltása az erőforrások.
>

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta, hogyan legyen korlátozva a lehetősége, hogy a fenyegetések által:

> [!div class="checklist"]
> * Konfigurálása csak a virtuális gép elérhető adja meg a házirend szabályozza, és naplózza a virtuális gépek csak szükség esetén a hozzáférést
> * Szabályozhatja, hogy mely alkalmazások futtathatja a virtuális gépek vezérlők adaptív alkalmazás-házirend konfigurálása

A következő oktatóanyag tájékozódhat az válaszol-biztonsági incidensekre továbblépés.

> [!div class="nextstepaction"]
> [Oktatóanyag: Válaszolni biztonsági incidensek](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
