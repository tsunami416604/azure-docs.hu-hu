---
title: "Az Azure Security Center gyors üzembe helyezés – előkészítéséről a Windows rendszerű számítógépek a Security Center |} Microsoft Docs"
description: "A gyors üzembe helyezés bemutatja, hogyan lehet kiépíteni a Windows rendszerű számítógépeken a Microsoft Monitoring Agent."
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
ms.date: 01/07/2018
ms.author: terrylan
ms.openlocfilehash: 50cbbca9181d67bc41632a4650c76b9636a72356
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="quickstart-onboard-windows-computers-to-azure-security-center"></a>Gyors üzembe helyezés: Beépített Windows-számítógépek számára az Azure Security Center
Miután bevezetésében Azure-előfizetését, engedélyezheti a Security Center vonatkozóan Azure,-on kívüli erőforrásaihoz, például a helyszíni vagy a többi felhőből, úgy a Microsoft Monitoring Agent.

A gyors üzembe helyezés jeleníti meg a Microsoft Monitoring Agent telepítése Windows rendszerű számítógépeken.

## <a name="prerequisites"></a>Előfeltételek
A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).

A gyors üzembe helyezés megkezdése előtt kell a Security Center Standard tarifacsomagot. Lásd: [Onboard a Security Center Standard Azure-előfizetése](security-center-get-started.md) frissítésével kapcsolatos utasításokért. A Security Center Standard ingyenesen megpróbálhatja az első 60 nap.

## <a name="add-new-windows-computer"></a>Új Windows-számítógép hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://azure.microsoft.com/features/azure-portal/).
2. A **Microsoft Azure** menüben válassza a **Security Center** elemet. **A Security Center – áttekintés** nyílik meg.

 ![A Security Center áttekintése][2]

3. Az a Security Center fő menüben válassza a **speciális biztonsági bevezetési**.
4. Válassza ki **kívánt-Azure számítógépek hozzáadása**.

   ![Érheti el a speciális biztonsági][3]

5. A **adja hozzá az új-Azure számítógépek**, a Naplóelemzési munkaterület listája látható. A listán, ha alkalmazható, alapértelmezett munkaterület létrehozásakor meg a Security Center által automatikus kiépítés engedélyezése. Válassza ki a munkaterület vagy egy másik munkaterület szeretné használni.

    ![-Azure számítógép hozzáadása][4]

  A **közvetlen ügynök** panel nyílik meg a Windows-ügynökök letöltési hivatkozása, és a kulcsok az használható az ügynök konfigurálásáról a munkaterület azonosítója.

6.  Válassza ki a **Windows-ügynök letöltése** a számítógép processzor típusa a telepítőfájl letöltéséhez vonatkozó hivatkozás.

7.  Jobb oldalán **munkaterület azonosítója**, válassza a Másolás ikonra, és illessze be az azonosítója a Jegyzettömbbe.

8.  Jobb oldalán **elsődleges kulcs**, válassza a Másolás ikonra, és illessze be a kulcsot a Jegyzettömbbe.

## <a name="install-the-agent"></a>Az ügynök telepítése
Most telepíteni kell a letöltött fájl a célszámítógépen.

1. Másolja a fájlt a cél számítógépen, és futtassa a telepítőt.
2. Az a **üdvözlő** lapon jelölje be **következő**.
3. Az a **licencfeltételeket** lapon olvassa el a licencfeltételeket, és válassza ki **elfogadom**.
4. Az a **célmappa** lapon, módosítása vagy a megadott alapértelmezett telepítési mappát, és válassza **következő**.
5. Az a **ügynök telepítésének beállításai** lapon, válassza ki a csatlakoztassa az ügynököt az Azure Naplóelemzés (OMS), majd válassza **következő**.
6. Az a **Azure Naplóelemzés** lapon, és beillesztheti a **munkaterület azonosítója** és **Munkaterületkulcsot (elsődleges kulcs)** , amely az előző eljárásban másolja a Jegyzettömbbe.
7. Ha a számítógép a Naplóelemzési munkaterület Azure Government felhőben kell jelentsenek, válassza ki a **Azure Amerikai Egyesült államokbeli kormányzati** űrlap a **Azure felhőbe** legördülő listából.  Ha a számítógép a Log Analytics szolgáltatás proxykiszolgálón keresztül kommunikálnak, akkor jelölje be **speciális** és URL-címét, és a proxykiszolgáló portszámát.
8. Válassza ki **következő** biztosítása a konfigurációs beállításokat befejezése után.

  ![Az ügynök telepítése][5]

9. Az a **telepítésre kész** lapon ellenőrizze a kiválasztott beállításokat, és válassza ki **telepítése**.
10. Az a **konfigurálása sikeresen befejeződött** lapon jelölje be **Befejezés**

Ennek végeztével a **Microsoft Monitoring Agent** megjelenik a **Vezérlőpulton**. Tekintse át a hiba a konfiguráció, és ellenőrizze, hogy az ügynök csatlakozik.

A telepítése és konfigurálása az ügynök további információkért lásd: [csatlakozás Windows rendszerű számítógépek](../log-analytics/log-analytics-agent-windows.md#install-the-agent-using-setup).

Most figyelheti a Azure virtuális gépek és az-Azure számítógépek egy helyen. A **számítási**, a virtuális gépek és a számítógépek, valamint javaslatokat áttekintését rendelkezik. Minden oszlop egy javaslatok jelöl. A szín jelöli a virtuális gép vagy a számítógép aktuális biztonsági állapotával kapcsolatos, amelyekre érvényes. A Security Center is Felfed, bármely észlelések ezeken a számítógépeken, a biztonsági riasztásokat.

  ![Számítási panel][6]

Ikonok képviselt két típusa van a **számítási** panel:

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) A nem Azure-számítógép

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure VM

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szükség, akkor eltávolíthatja az ügynök a Windows-számítógépen.

Az ügynök eltávolításához:

1. Nyissa meg **Vezérlőpultot**.
2. Nyissa meg a **Programok és szolgáltatások** részt.
3. A **Programok és szolgáltatások** alatt válassza ki a **Microsoft Monitoring Agent** elemet, és kattintson az **Eltávolítás** parancsra.

## <a name="next-steps"></a>További lépések
A gyors üzembe helyezés, a Windows rendszerű számítógépeken a Microsoft Monitoring Agent kiépítve. További információt a Security Center használata, továbbra is az oktatóanyag egy biztonsági házirend konfigurálása és az erőforrások biztonsági értékeléséhez.

> [!div class="nextstepaction"]
> [Oktatóanyag: Határozza meg, és felmérheti a biztonsági házirendek](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/onboard-windows-computer.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
