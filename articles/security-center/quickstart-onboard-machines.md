---
title: Nem Azure-beli gépek összekapcsolásának Azure Security Center
description: Ismerje meg, hogyan csatlakoztathatók a nem Azure-beli gépek Security Center
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 82b8161f92f337002e9d8bbdc45cd53d5921fc00
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91280674"
---
#  <a name="connect-your-non-azure-machines-to-security-center"></a>Nem Azure-beli gépek összekapcsolásának Security Center

A Security Center a nem Azure-számítógépek biztonsági állapotát is képes monitorozni, azonban ehhez elő kell készítenie ezeket az erőforrásokat. A nem Azure-beli számítógépeket az **első lépések** lapról vagy a **leltárból** is hozzáadhatja az alább leírtak szerint.

## <a name="add-non-azure-computers"></a>Nem Azure-beli számítógépek hozzáadása 

1. A Security Center menüjében nyissa meg az **első lépések** lapot.
1. Válassza a **Bevezetés** lapot.

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="Első lépések lap a kezdeti lépések oldalon" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. A **nem Azure-beli kiszolgálók hozzáadása**alatt válassza a **Konfigurálás** lehetőséget.

    > [!TIP]
    > Megnyithatja a gépek hozzáadása lehetőséget is a **leltár** lap **nem Azure-beli kiszolgálók hozzáadása** gombjával.

    Megjelenik a Log Analytics-munkaterületek listája. Ha van ilyen, a lista tartalmazza azt az alapértelmezett munkaterületet is, amelyet a Security Center hozott létre, amikor az automatikus kiépítés engedélyezve volt. Válassza ki ezt a munkaterületet, vagy egy másik használni kívánt munkaterületet.

    Hozzáadhat számítógépeket egy meglévő munkaterülethez, vagy létrehozhat egy új munkaterületet. 

1. Ha új munkaterületet szeretne létrehozni, válassza az  **Új munkaterület létrehozása**lehetőséget.

1. A munkaterületek listájából válassza a **kiszolgálók hozzáadása** lehetőséget a megfelelő munkaterülethez.
    Megjelenik az **ügynökök kezelése** lap.

    Innen válassza ki az alábbi megfelelő eljárást a bevezetéshez használt gépek típusától függően:

    - [A Azure Stack virtuális gépek előkészítése](#onboard-your-azure-stack-vms)
    - [A Linux rendszerű gépek előkészítése](#onboard-your-linux-machines)
    - [Windows rendszerű gépek előkészítése](#onboard-your-windows-machines)


### <a name="onboard-your-azure-stack-vms"></a>A Azure Stack virtuális gépek előkészítése
Azure Stack virtuális gépek hozzáadásához szüksége lesz az **ügynökök kezelése** oldalon található információkra, valamint a **Azure monitor, a frissítés és a konfiguráció kezelése** virtuálisgép-bővítmény konfigurálására a Azure stack futó virtuális gépeken.
1. Az **ügynökök kezelése** lapon másolja a munkaterület- **azonosítót** és az **elsődleges kulcsot** a Jegyzettömbbe.
1. Jelentkezzen be **Azure stack** -portálra, és nyissa meg a **virtuális gépek** lapot.
1. Válassza ki a Security Center védelemmel ellátni kívánt virtuális gépet.
    >[!TIP]
    > A virtuális gépek Azure Stackon való létrehozásával kapcsolatos információkért tekintse meg ezt a rövid útmutatót [a Windows rendszerű virtuális gépekről](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) , illetve [a Linux rendszerű virtuális gépekről](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal).
1. Válassza a **Bővítmények** lehetőséget. Megjelenik a virtuális gépen telepített virtuálisgép-bővítmények listája.
1. Válassza a **Hozzáadás** lapot. Az **új erőforrás** menü megjeleníti az elérhető virtuálisgép-bővítmények listáját.
1. Válassza ki a **Azure monitor, a frissítés és a konfiguráció kezelése** bővítményt, és válassza a **Létrehozás**lehetőséget. Megnyílik a **telepítési bővítmény** konfigurációja lap.
    >[!NOTE]
    > Ha nem látja a piactéren felsorolt **Azure monitor, frissítés és konfigurálás felügyeleti** bővítményt, forduljon a Azure stack-kezelőhöz, hogy elérhető legyen.
1. A **bővítmény** konfigurálása lapon illessze be az előző lépésben a Jegyzettömbbe másolt **munkaterület-azonosítót** és a **munkaterület kulcsát (elsődleges kulcs)** .
1. A konfiguráció befejezése után kattintson **az OK gombra**. A bővítmény állapota a **kiépítés sikerességének**megfelelően jelenik meg. Akár egy óráig is eltarthat, amíg a virtuális gép megjelenik Security Center.


### <a name="onboard-your-linux-machines"></a>A Linux rendszerű gépek előkészítése
Linuxos gépek hozzáadásához szüksége lesz a WGET parancsra az **ügynökök kezelése** lapon.
1. Az **ügynökök kezelése** lapon másolja a **WGET** parancsot a Jegyzettömbbe. Olyan helyre mentse a fájlt, amelyet el lehet érni a Linux rendszerű számítógépről.
1. A Linux rendszerű számítógépen nyissa meg a fájlt a WGET paranccsal. Válassza ki a teljes tartalmat, és másolja és illessze be egy terminál-konzolba.
1. A telepítés befejezésekor ellenőrizheti, hogy a *omsagent* telepítve van-e a *pgrep* parancs futtatásával. A parancs visszaküldi a *omsagent* PID-t.
    Az ügynök naplói a következő címen találhatók: */var/opt/Microsoft/omsagent/ \<workspace id> /log/* az új Linux-gép a Security Centerban akár 30 percet is igénybe vehet.


### <a name="onboard-your-windows-machines"></a>Windows rendszerű gépek előkészítése
Windows rendszerű gépek hozzáadásához szüksége lesz az **ügynökök kezelése** oldalon található információkra, és le kell töltenie a megfelelő ügynök fájlját (32/64 bites).
1. Válassza ki a számítógép processzortípusának megfelelő **Windows-ügynök letöltése** hivatkozást a telepítési fájl letöltéséhez.
1. Az **ügynökök kezelése** lapon másolja a munkaterület- **azonosítót** és az **elsődleges kulcsot** a Jegyzettömbbe.
1. Másolja a letöltött telepítőfájlt a célszámítógépen, és futtassa.
1. Kövesse a telepítési varázslót (**következő**, **Elfogadom**, **tovább**, **tovább**).
    1. Az **Azure log Analytics** lapon illessze be a Jegyzettömbbe másolt **munkaterület-azonosítót** és a **munkaterület kulcsát (elsődleges kulcs)** .
    1. Ha a számítógépnek Azure Government felhőben Log Analytics munkaterületre kell jelentenie, válassza az **Azure US government** lehetőséget az **Azure Cloud** legördülő listából.
    1. Ha a számítógépnek egy proxykiszolgálón keresztül kell kommunikálnia a Log Analytics szolgáltatással, kattintson a **Speciális** gombra, majd adja meg a proxykiszolgáló URL-címét és portszámát.
    1. Ha megadta az összes konfigurációs beállítást, válassza a **tovább**lehetőséget.
    1. A **telepítésre kész** oldalon tekintse át az alkalmazandó beállításokat, majd válassza a **telepítés**lehetőséget.
    1. A **Konfigurálás sikeresen befejeződött** lapon válassza a **Befejezés**lehetőséget.

Ha elkészült, a **log Analytics ügynök** megjelenik a **Vezérlőpulton**. Áttekintheti a konfigurációt, és ellenőrizheti, hogy az ügynök megfelelően csatlakozik-e.

Az ügynök telepítésével és konfigurálásával kapcsolatos további információkért lásd: [Windows rendszerű számítógépek összekapcsolása](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).


## <a name="verifying"></a>Ellenőrzése
Gratulálunk! Az Azure-és a nem Azure-beli gépeket most már egyetlen helyen láthatja. Nyissa meg a [tárgyieszköz-leltár lapot](asset-inventory.md) , és a megfelelő erőforrástípusok alapján szűrje a szűrőt. Ez a két ikon megkülönbözteti a típusokat:

  ![icon1](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Nem Azure-beli gép

  ![icon2](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM


## <a name="next-steps"></a>Következő lépések

Ezen az oldalon bemutatjuk, hogyan adhat hozzá a nem Azure-beli gépeket Azure Security Centerhoz. Az állapotuk figyeléséhez használja a leltári eszközöket a következő oldalon leírtak szerint:

- [Erőforrások megismerése és kezelése az eszközök leltározásával és felügyeleti eszközeivel](asset-inventory.md)