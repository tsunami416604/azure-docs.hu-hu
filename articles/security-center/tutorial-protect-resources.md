---
title: Access & alkalmazásvezérlők oktatóanyaga – Azure Security Center
description: Ez az oktatóanyag bemutatja, hogyan konfigurálhat egy just-in-time virtuálisgép-hozzáférési szabályzatot és egy alkalmazásvezérlő szabályzatot.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: 4b40b7c6f755eb2107a09b1b881ea33fa2187f29
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73686331"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Oktatóanyag: Erőforrások védelme az Azure Security Centerrel
A Security Center korlátozza a fenyegetéseknek való kitettségét azzal, hogy hozzáférés- és alkalmazásvezérlőket használ a kártékony tevékenységek blokkolására. Just-in-time (JIT) virtuális gép (VM) hozzáférés csökkenti a támadások nak való kitettséget azáltal, hogy lehetővé teszi a virtuális gépekhez való állandó hozzáférés megtagadását. Az állandó hozzáférés helyett szabályozott és naplózott hozzáférést biztosít a virtuális gépekhez – csak akkor, ha szükség van rá. Az adaptív alkalmazásvezérlők segítenek felvértezni a virtuális gépeket a kártevők ellen azáltal, hogy szabályozzák, mely alkalmazások futhatnak rajtuk. A Security Center gépi tanulási módszerekkel elemzi a virtuális gépen futó folyamatokat, és az így szerzett információk alapján segít az engedélyezési szabályok alkalmazásában.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Just-in-time virtuálisgép-hozzáférési szabályzat konfigurálása
> * Alkalmazásvezérlési szabályzat konfigurálása

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyagban ismertetett funkciók végrehajtásához a Security Center Standard tarifacsomagjával kell rendelkeznie. Kipróbálhatja a Security Center Standard-ot díjmentesen. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/). [Az Azure-előfizetés a Security Center Standard verziójába való felvételével](security-center-get-started.md) foglalkozó rövid útmutató végigvezeti azokon a lépéseken, amelyekkel frissíthet a Standard verzióra.

## <a name="manage-vm-access"></a>Virtuális gépekhez való hozzáférés kezelése
A JIT virtuálisgép-hozzáférés segítségével zárolhatja az Azure-beli virtuális gépek bejövő forgalmát, csökkentve a támadásoknak való kitettséget, miközben szükség esetén könnyű hozzáférést biztosít a virtuális gépekhez való csatlakozáshoz.

A felügyeleti portoknak nem kell mindig nyitva lenniük. Csak addig kell nyitva lenniük, amíg Ön csatlakozik a virtuális géphez, például azért, hogy felügyeleti vagy karbantartási feladatokat végezzen. Ha a just-in-time engedélyezve van, a Security Center a Hálózati biztonsági csoport (NSG) szabályait használja, amelyek korlátozzák a felügyeleti portokhoz való hozzáférést, így a támadók nem célozhatnak rájuk.

1. A Security Center főmenüjében válassza **a Just-in-time virtuális gép-hozzáférést** **az ADVANCED CLOUD DEFENSE**területen.

   ![Virtuális gépek igény szerinti elérése][1]

   **Just-in-time virtuális gép-hozzáférés** a virtuális gépek állapotáról nyújt információt:

   - **Konfigurálva** – virtuális gépek, amelyek úgy vannak konfigurálva, hogy támogassák a virtuális gép elérését.
   - **Ajánlott** – virtuális gépek, amelyek támogatják a virtuális gép-hozzáférést, de nincsenek konfigurálva.
   - **No recommendation** (Nincs javaslat) – A virtuális gépek a következő okokból kerülhetnek ebbe a kategóriába:

     - Hiányzó NSG - A just-in-time megoldás hoz egy NSG kell a helyén.
     - Klasszikus virtuális gép – Security Center just-in-time virtuális gép-hozzáférés jelenleg csak az Azure Resource Manager en keresztül telepített virtuális gépeket támogatja.
     - Egyéb – A virtuális gép ebben a kategóriában, ha a just-in-time megoldás ki van kapcsolva az előfizetés vagy az erőforrás-csoport biztonsági házirendje, vagy hogy a virtuális gép hiányzik egy nyilvános IP-cím, és nem rendelkezik nsg-vel.

2. Válasszon ki egy ajánlott virtuális gép, és kattintson **a JIT engedélyezése 1 virtuális gépen** a virtuális gép just-in-time házirendjének konfigurálásához:

   Mentheti a Security Center által javasolt alapértelmezett portokat, vagy hozzáadhat és konfigurálhat egy új portot, amelyen engedélyezni szeretné a just-in-time megoldást. Ebben az oktatóanyagban adjunk hozzá egy portot az **Add** (Hozzáadás) paranccsal.

   ![Portkonfiguráció hozzáadása][2]

3. Az **Add port configuration** (Portkonfiguráció hozzáadása) területen a következőket kell megadni:

   - A port
   - A protokolltípus
   - Engedélyezett forrás IP-címek – Olyan IP-címtartományok, amelyek jóváhagyott kérelem esetén hozzáférést kaphatnak.
   - Kérelem maximális időtartama – Az a maximális időtartam, ameddig egy port nyitva lehet.

4. A mentéshez kattintson az **OK** gombra.

## <a name="harden-vms-against-malware"></a>Virtuális gépek kártevők elleni felvértezése
Az adaptív alkalmazásvezérlők segítségével meghatározhatja a konfigurált erőforráscsoportokon futtatható alkalmazások csoportját, ami többek között segít felvértezni virtuális gépeit a kártevők ellen. A Security Center gépi tanulási módszerekkel elemzi a virtuális gépen futó folyamatokat, és az így szerzett információk alapján segít az engedélyezési szabályok alkalmazásában.

1. Térjen vissza a Security Center főmenüjébe. Az **ADVANCED CLOUD DEFENSE** (SPECIÁLIS FELHŐVÉDELEM) területen válassza ki az **Adaptive application controls** (Adaptív alkalmazásvezérlők) elemet.

   ![Adaptív alkalmazásvezérlők][3]

   A **Resource groups** (Erőforráscsoportok) szakaszban három lap található:

   - **Konfigurálva**: Az alkalmazásvezérléssel konfigurált virtuális gépeket tartalmazó erőforráscsoportok listája.
   - **Ajánlott**: Azon erőforráscsoportok listája, amelyekhez az alkalmazásvezérlés ajánlott.
   - **Nincs javaslat:** Alkalmazásvezérlési javaslatok nélkül virtuális gépeket tartalmazó erőforráscsoportok listája. például olyanokat, amelyeken az alkalmazások folyamatosan változnak, és még nem értek el egy stabil állapotot.

2. Válassza ki a **Recommended** (Ajánlott) lapot azon erőforráscsoportok listájáért, amelyeken javasolt bevezetni az alkalmazásvezérlést.

   ![Alkalmazásvezérlési javaslatok][4]

3. Jelölje ki az egyik erőforráscsoportot a **Create application control rules** (Alkalmazásvezérlési szabályok létrehozása) lehetőség megnyitásához. A **Select VMs** (Virtuális gépek kiválasztása) felületen tekintse át a javasolt virtuális gépek listáját, és törölje a jelölést azok mellől, amelyeket nem szeretne bevonni az alkalmazásvezérlés hatókörébe. A Folyamatok kiválasztása engedélyezési **szabályokhoz**listában tekintse át az ajánlott alkalmazások listáját, és törölje a jelet a nem kívánt alkalmazás jelölőnégyzetből. A lista a következőket tartalmazza:

   - **NÉV**: A teljes alkalmazás elérési útja
   - **FOLYAMATOK**: Hány alkalmazás található minden útvonalon belül
   - **COMMON**: Az "Igen" azt jelzi, hogy ezeket a folyamatokat az erőforráscsoport legtöbb virtuális gépén végrehajtották.
   - **EXPLOITABLE** (Kihasználható): Egy figyelmeztető ikon jelzi, ha az alkalmazás használatával az esetleges támadók megkerülhetik az alkalmazásengedélyezési rendszert. Érdemes áttekinteni ezeket az alkalmazásokat az engedélyezésük előtt.

4. Ha végzett a kiválasztással, válassza a **Create** (Létrehozás) lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A gyűjtemény részét képező többi rövid útmutató és oktatóanyag erre a rövid útmutatóra épül. Ha azt tervezi, hogy továbbra is együttműködik a további rövid útmutatók és oktatóanyagok, továbbra is fut a Standard szint, és tartsa automatikus kiépítése engedélyezve. Ha nem folytatja a munkát, vagy vissza szeretne térni az Ingyenes szintre:

1. Térjen vissza a Security Center főmenüjébe, és válassza a **Biztonsági szabályzat** elemet.
2. Válassza ki az előfizetést vagy a szabályzatot, amelyet vissza szeretne állítani Ingyenes állapotba. Megnyílik a **Biztonsági szabályzat** képernyő.
3. A **SZABÁLYZAT ÖSSZETEVŐI** alatt válassza a **Tarifacsomag** lehetőséget.
4. Válassza az **Ingyenes** lehetőséget az előfizetés Standard szintről Ingyenes szintre való módosításához.
5. Kattintson a **Mentés** gombra.

Ha le szeretné tiltani az automatikus kiépítést:

1. Térjen vissza a Biztonsági központ főmenüjéhez, és válassza a **Biztonsági házirend**lehetőséget.
2. Válassza ki azt az előfizetést, amelynél le szeretné tiltani az automatikus kiépítést.
3. Az automatikus kiépítés letiltásához a **Biztonsági szabályzat – Adatgyűjtés** területen válassza a **Ki** lehetőséget az **Előkészítés** elemnél.
4. Kattintson a **Mentés** gombra.

>[!NOTE]
> Az automatikus kiépítés letiltása nem távolítja el a Microsoft Monitoring Agentet azon Azure-beli virtuális gépekről, ahol az ügynök üzembe lett helyezve. Az automatikus kiépítés letiltása korlátozza az erőforrások biztonsági monitorozását.
>

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan korlátozhatja a fenyegetéseknek való kitettségét a következőkkel:

> [!div class="checklist"]
> * Just-in-time virtuálisgép-hozzáférési szabályzat konfigurálása, amely csak szükség esetén biztosítja a virtuális gépek ellenőrzött és auditált hozzáférését
> * Adaptív alkalmazásvezérlési szabályzat konfigurálása, amely szabályozza, mely alkalmazások futhatnak a virtuális gépeken

Folytassa a következő oktatóanyaggal, amely a biztonsági incidensekre való válaszadást ismerteti.

> [!div class="nextstepaction"]
> [Oktatóanyag: Reagálás a biztonsági incidensekre](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
