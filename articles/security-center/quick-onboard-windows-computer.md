---
title: Azure Security Center rövid útmutató – Windows rendszerű számítógépek felvétele a Security Centerbe | Microsoft Docs
description: Ez a rövid útmutató bemutatja, hogyan telepítheti a Microsoft Monitoring Agent szolgáltatást egy Windows rendszerű számítógépre.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2018
ms.author: terrylan
ms.openlocfilehash: b872153278810ce92f19c7c71fe473a2b77def35
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="quickstart-onboard-windows-computers-to-azure-security-center"></a>Rövid útmutató: Windows rendszerű számítógépek felvétele az Azure Security Centerbe
Az Azure-előfizetései felvétele után a Microsoft Monitoring Agent kiépítésével engedélyezheti a Security Centert az Azure-on kívül, például a helyszínen vagy más felhőkben futó erőforrások számára.

Ez a rövid útmutató bemutatja, hogyan telepítheti a Microsoft Monitoring Agent szolgáltatást egy Windows rendszerű számítógépre.

## <a name="prerequisites"></a>Előfeltételek
A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).

A rövid útmutató megkezdése előtt rendelkeznie kell a Security Center Standard tarifacsomagjával. A frissítési utasításokért lásd az [Azure-előfizetés a Security Center Standard verziójába történő felvételét](security-center-get-started.md) ismertető szakaszt. Az első 60 napban díjmentesen próbálhatja ki a Security Center Standard verzióját.

## <a name="add-new-windows-computer"></a>Új, Windows rendszerű számítógép hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://azure.microsoft.com/features/azure-portal/).
2. A **Microsoft Azure** menüben válassza a **Security Center** elemet. Megnyílik a **Security Center – Áttekintés** képernyő.

 ![Security Center – Áttekintés][2]

3. A Security Center főmenüjében válassza a **Speciális biztonsági megoldás előkészítése** elemet.
4. Válassza a **Nem Azure-beli számítógépek hozzáadása** lehetőséget.

   ![Speciális biztonsági megoldás előkészítése][3]

5. Az **Új nem Azure-beli számítógépek hozzáadása** megjeleníti a Log Analytics-munkaterületek listáját. Ha van ilyen, a lista tartalmazza azt az alapértelmezett munkaterületet is, amelyet a Security Center hozott létre, amikor az automatikus kiépítés engedélyezve volt. Válassza ki ezt a munkaterületet, vagy egy másik használni kívánt munkaterületet.

    ![Nem Azure-beli számítógép hozzáadása][4]

  A megnyíló **Direct Agent** panel egy hivatkozást tartalmaz az ügynök konfigurálásához szükséges munkaterület-azonosító Windows-ügynökének és kulcsának letöltéséhez.

6.  Válassza ki a számítógép processzortípusának megfelelő **Windows-ügynök letöltése** hivatkozást a telepítési fájl letöltéséhez.

7.  Kattintson a **Munkaterület-azonosító** jobb oldalán található Másolás ikonra, és illessze be az azonosítót a Jegyzettömbbe.

8.  Kattintson az **Elsődleges kulcs** jobb oldalán található Másolás ikonra, és illessze be az azonosítót a Jegyzettömbbe.

## <a name="install-the-agent"></a>Az ügynök telepítése
Most telepíteni kell a letöltött fájlt a célszámítógépre.

1. Másolja a fájlt a célszámítógépre, és futtassa a telepítőt.
2. Az **Üdvözöljük** lapon kattintson a **Tovább** gombra.
3. A **Licencfeltételek** oldalon olvassa el és fogadja el a licencet, majd kattintson az **Elfogadom** gombra.
4. A **Célmappa** lapon fogadja el az alapértelmezett telepítési mappát, vagy adjon meg egy másikat, majd kattintson a **Tovább** gombra.
5. **Az ügynök telepítésének beállításai** lapon csatlakoztassa az ügynököt az Azure Log Analyticshez, majd kattintson a **Tovább** gombra.
6. Az **Azure Log Analytics** lapon illessze be az előző folyamat során a Jegyzettömbbe másolt **Munkaterület-azonosítót** és a **Munkaterületkulcsot (Elsődleges kulcs)**.
7. Ha a gépnek egy Azure Government-felhőbeli Log Analytics-munkaterületnek kell jelentenie, válassza az **Azure US Government** lehetőséget az **Azure Cloud** legördülő listából.  Ha a számítógépnek egy proxykiszolgálón keresztül kell kommunikálnia a Log Analytics szolgáltatással, kattintson a **Speciális** gombra, majd adja meg a proxykiszolgáló URL-címét és portszámát.
8. A szükséges konfigurációs beállítások megadása után kattintson a **Tovább** gombra.

  ![Az ügynök telepítése][5]

9. A **Telepítésre kész** oldalon ellenőrizze a beállításokat, majd kattintson a **Telepítés** elemre.
10. **A konfigurálás sikeresen befejeződött** lapon kattintson a **Befejezés** gombra.

Ennek végeztével a **Microsoft Monitoring Agent** megjelenik a **Vezérlőpulton**. Áttekintheti a konfigurációt, és ellenőrizheti, hogy az ügynök megfelelően csatlakozik-e.

Az ügynök telepítésével és konfigurálásával kapcsolatos további információkat tekintse meg a [Windows rendszerű számítógépek csatlakoztatását](../log-analytics/log-analytics-agent-windows.md#install-the-agent-using-setup-wizard) ismertető részt.

Mostantól egy helyről felügyelheti az Azure-beli virtuális gépeket és a nem Azure-beli számítógépeket. A **Számítás** területen áttekintheti az összes virtuális gépet, számítógépet és javaslatot. Minden oszlop egy javaslatcsoportot képvisel. A szín a virtuális gép vagy számítógép aktuális biztonsági állapotát jelöli az adott javaslatra vonatkozóan. A Security Center emellett a biztonsági riasztásokban megjeleníti a számítógépekhez tartozó észleléseket is.

  ![Számítás panel][6]

A **Számítás** panelen kétféle ikon található:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) Nem Azure-beli számítógép

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure VM

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, eltávolíthatja az ügynököt a Windows rendszerű számítógépről.

Az ügynök eltávolítása:

1. Nyissa meg **Vezérlőpultot**.
2. Nyissa meg a **Programok és szolgáltatások** részt.
3. A **Programok és szolgáltatások** alatt válassza ki a **Microsoft Monitoring Agent** elemet, és kattintson az **Eltávolítás** parancsra.

## <a name="next-steps"></a>További lépések
Ez a rövid útmutatóban kiépítette a Microsoft Monitoring Agent szolgáltatást egy Windows rendszerű számítógépen. Ha többet szeretne megtudni a Security Center használatáról, tekintse meg a biztonsági szabályzat konfigurálásával és az erőforrások biztonságának felmérésével foglalkozó oktatóanyagot is.

> [!div class="nextstepaction"]
> [Oktatóanyag: Biztonsági szabályzatok meghatározása és értékelése](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/onboard-windows-computer.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
