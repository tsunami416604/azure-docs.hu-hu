---
title: "Az Azure Security Center gyors üzembe helyezés – előkészítéséről a Linux rendszerű számítógépek a Security Center |} Microsoft Docs"
description: "A gyors üzembe helyezés bemutatja, hogyan bevezetni a Security Center Linux rendszerű számítógépeket."
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
ms.openlocfilehash: 365afd2199b1b8b2e70d882f6a729384edbdffbc
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2018
---
# <a name="quickstart-onboard-linux-computers-to-azure-security-center"></a>Gyors üzembe helyezés: Az Azure Security Center előkészítésére Linux rendszerű számítógépeket
Miután bevezetésében Azure-előfizetését, engedélyezheti a Security Center Linux erőforrások futó kívül Azure, például a helyszíni vagy más felhők kiépítése a Linux-ügynök által.

A gyors üzembe helyezés a Linux-ügynök telepítése egy Linux-számítógép látható.

## <a name="prerequisites"></a>Előfeltételek
A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).

A gyors üzembe helyezés megkezdése előtt kell a Security Center Standard tarifacsomagot. Lásd: [Onboard a Security Center Standard Azure-előfizetése](security-center-get-started.md) frissítésével kapcsolatos utasításokért. A Security Center Standard ingyenesen megpróbálhatja az első 60 nap.

## <a name="add-new-linux-computer"></a>Új Linux-számítógép hozzáadása

1. Jelentkezzen be az [Azure Portalra](https://azure.microsoft.com/features/azure-portal/).
2. A **Microsoft Azure** menüben válassza a **Security Center** elemet. **A Security Center – áttekintés** nyílik meg.

 ![A Security Center áttekintése][2]

3. Az a Security Center fő menüben válassza a **speciális biztonsági bevezetési**.
4. Válassza ki **kívánt-Azure számítógépek hozzáadása**.
   ![Érheti el a speciális biztonsági][3]

5. A **adja hozzá az új-Azure számítógépek**, a Naplóelemzési munkaterület listája látható. A listán, ha alkalmazható, alapértelmezett munkaterület létrehozásakor meg a Security Center által automatikus kiépítés engedélyezése. Válassza ki a munkaterület vagy egy másik munkaterület szeretné használni.

    ![-Azure számítógép hozzáadása][4]

6.  A a **közvetlen ügynök** lap **töltse le és előkészítésére ÜGYNÖK a LINUX**, jelölje be a **másolási** gomb másolása a *wget* parancsot.

7.  Nyissa meg a Jegyzettömböt, és illessze be a parancsot. Mentse ezt a fájlt olyan helyre, amely elérhető a Linux rendszerű számítógépről is lehet.

## <a name="install-the-agent"></a>Az ügynök telepítése

1.  A Linux rendszerű számítógépen nyissa meg a korábban mentett fájlt. Válassza ki a teljes tartalom, másolása, nyisson meg egy terminál konzolt, és illessze be a parancsot.
2.  A telepítés befejeződése után ellenőrizheti, hogy a *omsagent* futtatásával telepítve van a *pgrep* parancsot. A parancs visszatér a *omsagent* azonosítója (PID) (Folyamatazonosító) alább látható módon:

  ![Az ügynök telepítése][5]

A naplókat a Security Center-ügynök Linux helyen találhatók: */var/opt/microsoft/omsagent/<workspace id>/log/*

  ![Az ügynök naplók][6]

Némi várakozás után eltarthat 30 percet, a Security Center az új Linux rendszerű számítógép fog megjelenni.

Most figyelheti a Azure virtuális gépek és az-Azure számítógépek egy helyen. A **számítási**, a virtuális gépek és a számítógépek, valamint javaslatokat áttekintését rendelkezik. Minden oszlop egy javaslatok jelöl. A szín jelöli a virtuális gép vagy a számítógép aktuális biztonsági állapotával kapcsolatos, amelyekre érvényes. A Security Center is Felfed, bármely észlelések ezeken a számítógépeken, a biztonsági riasztásokat.

  ![Számítási panel][7] képviselt ikonok két típusa van a **számítási** panel:

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) A nem Azure-számítógép

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Amikor már nincs szükség, akkor eltávolíthatja az ügynök a Linux-számítógép.

Az ügynök eltávolításához:

1. A Linux-ügynök letöltése [univerzális parancsfájl](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) arra a számítógépre.
2. A kötegfájl .sh futtassa a *--kiürítése* argumentum a számítógépen, amely teljesen eltávolítja az ügynököt és konfigurációja.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

## <a name="next-steps"></a>További lépések
A gyors üzembe helyezési létesített az ügynök a Linux rendszerű számítógépen. További információt a Security Center használata, továbbra is az oktatóanyag egy biztonsági házirend konfigurálása és az erőforrások biztonsági értékeléséhez.

> [!div class="nextstepaction"]
> [Oktatóanyag: Határozza meg, és felmérheti a biztonsági házirendek](tutorial-security-policy.md)

<!--Image references-->
[1]: ./media/quick-onboard-linux-computer/portal.png
[2]: ./media/quick-onboard-linux-computer/overview.png
[3]: ./media/quick-onboard-linux-computer/onboard-windows-computer.png
[4]: ./media/quick-onboard-linux-computer/add-computer.png
[5]: ./media/quick-onboard-linux-computer/pgrep-command.png
[6]: ./media/quick-onboard-linux-computer/logs-for-agent.png
[7]: ./media/quick-onboard-linux-computer/compute.png
