---
title: A Windows Felügyeleti központ integrálása az Azure Security Centerrel | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan integrálható az Azure Security Center a Windows Felügyeleti központtal
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5467794bf246fab4ff7ded9c445dbeee0c4093b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79139623"
---
# <a name="integrate-azure-security-center-with-windows-admin-center"></a>Az Azure Security Center integrálása a Windows Felügyeleti központtal

A Windows Felügyeleti központ a Windows-kiszolgálók felügyeleti eszköze. Ez egy egyetlen hely a rendszergazdák számára, hogy hozzáférjenek a leggyakrabban használt felügyeleti eszközök többségéhez. A Windows Felügyeleti központon belül közvetlenül beviheti a helyszíni kiszolgálókat az Azure Security Centerbe. Ezután közvetlenül a Windows Felügyeleti központ felhasználói felületén tekintheti meg a biztonsági javaslatok és riasztások összegzését.

> [!NOTE]
> Az Azure-előfizetésnek és a kapcsolódó Log Analytics-munkaterületnek egyaránt engedélyeznie kell a Security Center standard szintjét a Windows Felügyeleti központ integrációjának engedélyezéséhez.
> A standard szint ingyenes az első 30 napban, ha korábban nem használta az előfizetésés munkaterület. További információt [az árképzési információs oldalon](security-center-pricing.md)talál.
>

Ha sikeresen beszállt egy kiszolgálóra a Windows Felügyeleti központból az Azure Security Centerbe, a következőket teheti:

* Biztonsági riasztások és javaslatok megtekintése a Windows Felügyeleti központ Biztonsági központ bővítményében
* A Biztonsági központban található Windows Felügyeleti központ felügyelt kiszolgálóinak biztonsági állapotának megtekintése és további részletes információk beolvasása az Azure Portalon (vagy API-n keresztül)

E két eszköz kombinálásával a Security Center egyetlen ablaktáblává válik, amely az összes biztonsági információt megtekinti, függetlenül az erőforrástól: a Windows Felügyeleti központ által felügyelt, helyszíni kiszolgálók, a virtuális gépek és a további PaaS-munkaterhelések védelme érdekében.

## <a name="onboarding-windows-admin-center-managed-servers-into-security-center"></a>Bevezetés a Windows Felügyeleti központ felügyelt kiszolgálóinak a Biztonsági központba

1. A Windows Felügyeleti központban válassza ki az egyik kiszolgálót, és az **Eszközök** ablaktáblán válassza ki az Azure Security Center bővítményt:

    ![Az Azure Security Center bővítmény a Windows Felügyeleti központban](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Ha a kiszolgáló már be van táblázva a Security Center be, a beállítási ablak nem jelenik meg.

1. Kattintson **a Bejelentkezés az Azure-ba gombra, és állítsa be a set (Bejelentkezés az Azure-ba) gombra.**
    ![A Windows Felügyeleti központ bővítményének bevezetése az Azure Security Center beszállásához](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Kövesse az utasításokat a kiszolgáló biztonsági központhoz való csatlakoztatásához. Miután megadta a szükséges részleteket, és megerősítette, a Security Center elköveti a szükséges konfigurációs módosításokat annak érdekében, hogy az alábbiak mindegyike teljesüljön:
    * Egy Azure Gateway regisztrálva van.
    * A kiszolgálórendelkezik egy munkaterülettel, amelynek jelentenie kell, és egy társított előfizetés.
    * A Security Center szabványos szintű Log Analytics-megoldása engedélyezve van a munkaterületen. Ez a megoldás a Security Center standard szintű szolgáltatásait biztosítja *az erre* a munkaterületre jelentést tevő összes kiszolgálóhoz és virtuális géphez.
    * A Security Center normál szintű díjszabása a virtuális géphez engedélyezve van az előfizetésben.
    * A Microsoft Monitoring Agent (MMA) telepítve van a kiszolgálón, és úgy van beállítva, hogy jelentsen a kijelölt munkaterületnek. Ha a kiszolgáló már jelentést tesz egy másik munkaterületnek, akkor úgy van beállítva, hogy az újonnan kiválasztott munkaterületnek is jelentsen.

    > [!NOTE]
    > Az ajánlások megjelenése a bevezetés után eltarthat egy ideig. Valójában a szerver tevékenységétől függően előfordulhat, hogy nem *kap* figyelmeztetést. Ha tesztriasztásokat szeretne létrehozni a riasztások megfelelő működéséhez, kövesse [a riasztásérvényesítési eljárás utasításait.](security-center-alert-validation.md)


## <a name="viewing-security-recommendations-and-alerts-in-windows-admin-center"></a>Biztonsági javaslatok és riasztások megtekintése a Windows Felügyeleti központban

Miután beszállt, megtekintheti a riasztásokat és javaslatokat közvetlenül az Azure Security Center területén a Windows Felügyeleti központ. Kattintson egy javaslatra vagy egy riasztásra az Azure Portalon való megtekintéséhez. Itt további információkat kaphat, és megtudhatja, hogyan orvosolhatja a problémákat.

[![A Biztonsági központ javaslatai és figyelmeztetései a Windows Felügyeleti központban láthatómódon](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="viewing-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>A Biztonsági központ Windows Felügyeleti központ által kezelt kiszolgálóira vonatkozó biztonsági javaslatok és riasztások megtekintése
Az Azure Security Centerből:

* Az összes Windows Felügyeleti központ kiszolgálóra vonatkozó biztonsági javaslatok megtekintéséhez nyissa meg a **Compute & Apps alkalmazást,** és kattintson a **virtuális gépek és számítógépek** fülre.

    [![A Windows Felügyeleti központ felügyelt kiszolgálóira vonatkozó biztonsági javaslatok megtekintése](media/windows-admin-center-integration/viewing-recommendations-wac.png)](media/windows-admin-center-integration/viewing-recommendations-wac.png#lightbox)

* Az összes Windows Felügyeleti központ kiszolgálóbiztonsági riasztásának megtekintéséhez nyissa meg a **Biztonsági riasztások ablakot.** Kattintson **a Szűrő gombra,** és győződjön meg arról, hogy **csak** a "Nem Azure" van kiválasztva:

    ![Biztonsági riasztások szűrése a Windows Felügyeleti központ felügyelt kiszolgálóira](./media/windows-admin-center-integration/filtering-alerts-to-non-azure.png)

    [![Biztonsági riasztások megtekintése a Windows Felügyeleti központ felügyelt kiszolgálóihoz](media/windows-admin-center-integration/viewing-alerts-wac.png)](media/windows-admin-center-integration/viewing-alerts-wac.png#lightbox)