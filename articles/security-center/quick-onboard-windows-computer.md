---
title: Windows rendszerű számítógépek felvétele az Azure Security Centerbe
description: Ez a rövid útmutató bemutatja, hogyan építheti ki a Log Analytics-ügynököt Windows rendszerű számítógépen.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: 2840b6636c2b511ab57a8bae8adf411f4d8a27d2
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435931"
---
# <a name="quickstart-onboard-windows-computers-to-azure-security-center"></a>Rövid útmutató: Windows rendszerű számítógépek felvétele az Azure Security Centerbe
Miután az Azure-előfizetések üzembe helyezést, engedélyezheti a Security Center az Azure-on kívül futó erőforrások, például a helyszínen vagy más felhők, a Log Analytics-ügynök kiépítése.

Ez a rövid útmutató bemutatja, hogyan telepítheti a Log Analytics-ügynököt Windows rendszerű számítógépre.

## <a name="prerequisites"></a>Előfeltételek
A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).

A rövid útmutató megkezdése előtt rendelkeznie kell a Security Center Standard tarifacsomagjával. A frissítési utasításokért lásd az [Azure-előfizetés a Security Center Standard verziójába történő felvételét](security-center-get-started.md) ismertető szakaszt. Kipróbálhatja a Security Center Standard díjmentesen. További részletekért tekintse át az [árképzést ismertető oldalt](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="add-new-windows-computer"></a>Új, Windows rendszerű számítógép hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://azure.microsoft.com/features/azure-portal/).
2. A **Microsoft Azure** menüben válassza a **Security Center** elemet. Megnyílik a **Security Center – Áttekintés** képernyő.

   ![Security Center – Áttekintés][2]

3. A Security Center főmenüjében válassza az **Első lépések** elemet.
4. Válassza a **Bevezetés** lapot.

   ![Bevezetés][3]

5. Kattintson az **Új nem Azure-beli számítógépek hozzáadása** pont alatt található **Konfigurálás** elemre. Megjelenik a Log Analytics-munkaterületek listája. Ha van ilyen, a lista tartalmazza azt az alapértelmezett munkaterületet is, amelyet a Security Center hozott létre, amikor az automatikus kiépítés engedélyezve volt. Válassza ki ezt a munkaterületet, vagy egy másik használni kívánt munkaterületet.

    ![Nem Azure-beli számítógép hozzáadása](./media/quick-onboard-windows-computer/non-azure.png)

   A megnyíló **Direct Agent** panel egy hivatkozást tartalmaz az ügynök konfigurálásához szükséges munkaterület-azonosító Windows-ügynökének és kulcsának letöltéséhez.

6. Válassza ki a számítógép processzortípusának megfelelő **Windows-ügynök letöltése** hivatkozást a telepítési fájl letöltéséhez.

7. Kattintson a **Munkaterület-azonosító** jobb oldalán található Másolás ikonra, és illessze be az azonosítót a Jegyzettömbbe.

8. Kattintson az **Elsődleges kulcs** jobb oldalán található Másolás ikonra, és illessze be az azonosítót a Jegyzettömbbe.

## <a name="install-the-agent"></a>Az ügynök telepítése
Most telepíteni kell a letöltött fájlt a célszámítógépre.

1. Másolja a fájlt a célszámítógépre, és futtassa a telepítőt.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.
3. A **Licencfeltételek** oldalon olvassa el és fogadja el a licencet, majd kattintson az **Elfogadom** gombra.
4. A **Célmappa** lapon fogadja el az alapértelmezett telepítési mappát, vagy adjon meg egy másikat, majd kattintson a **Tovább** gombra.
5. **Az ügynök telepítésének beállításai** lapon csatlakoztassa az ügynököt az Azure Log Analyticshez, majd kattintson a **Tovább** gombra.
6. Az **Azure Log Analytics** lapon illessze be az előző folyamat során a Jegyzettömbbe másolt **Munkaterület-azonosítót** és a **Munkaterületkulcsot (Elsődleges kulcs)**.
7. Ha a gépnek egy Azure Government-felhőbeli Log Analytics-munkaterületnek kell jelentenie, válassza az **Azure US Government** lehetőséget az **Azure Cloud** legördülő listából. Ha a számítógépnek egy proxykiszolgálón keresztül kell kommunikálnia a Log Analytics szolgáltatással, kattintson a **Speciális** gombra, majd adja meg a proxykiszolgáló URL-címét és portszámát.
8. A szükséges konfigurációs beállítások megadása után kattintson a **Tovább** gombra.

   ![Az ügynök telepítése][5]

9. A **Telepítésre kész** oldalon ellenőrizze a beállításokat, majd kattintson a **Telepítés** elemre.
10. **A konfigurálás sikeresen befejeződött** lapon kattintson a **Befejezés** gombra.

Ha elkészült, a **Log Analytics ügynök** megjelenik a **Vezérlőpulton.** Áttekintheti a konfigurációt, és ellenőrizheti, hogy az ügynök megfelelően csatlakozik-e.

Az ügynök telepítésével és konfigurálásával kapcsolatos további információkat tekintse meg a [Windows rendszerű számítógépek csatlakoztatását](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard) ismertető részt.

Mostantól egy helyről felügyelheti az Azure-beli virtuális gépeket és a nem Azure-beli számítógépeket. A **Számítás** területen áttekintheti az összes virtuális gépet, számítógépet és javaslatot. Minden oszlop egy javaslatcsoportot képvisel. A szín a virtuális gép vagy számítógép aktuális biztonsági állapotát jelöli az adott javaslatra vonatkozóan. A Security Center emellett a biztonsági riasztásokban megjeleníti a számítógépekhez tartozó észleléseket is.

  ![Számítás panel][6]

A **Számítás** panelen kétféle ikon található:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Nem Azure-beli számítógép

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure VM

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, eltávolíthatja az ügynököt a Windows rendszerű számítógépről.

Az ügynök eltávolítása:

1. Nyissa **meg a Vezérlőpultot**.
2. Nyissa meg a **Programok és szolgáltatások** részt.
3. A **Programok és szolgáltatások csoportban**válassza a Log **Analytics-ügynök** lehetőséget, majd kattintson **az Eltávolítás gombra.**

## <a name="next-steps"></a>További lépések
Ebben a rövid útmutatóban kiépítette a Log Analytics-ügynököt egy Windows-számítógépen. Ha többet szeretne megtudni a Security Center használatáról, tekintse meg a biztonsági szabályzat konfigurálásával és az erőforrások biztonságának felmérésével foglalkozó oktatóanyagot is.

> [!div class="nextstepaction"]
> [Oktatóanyag: Biztonsági szabályzatok meghatározása és értékelése](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
