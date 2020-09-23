---
title: Windows felügyeleti központ-kiszolgálók védetté Azure Security Center
description: Ez a cikk ismerteti, hogyan integrálható a Azure Security Center a Windows felügyeleti központba
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: memildin
ms.openlocfilehash: 36f519ce41ccfbfb48ca696ed2a61c6131a75998
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906325"
---
# <a name="protect-windows-admin-center-resources-with-security-center"></a>A Windows felügyeleti központ erőforrásainak védetté Security Center

A Windows felügyeleti központ egy felügyeleti eszköz a Windows-kiszolgálókhoz. A rendszergazdák egyetlen helyen érhetik el a leggyakrabban használt felügyeleti eszközök többségét. A Windows felügyeleti központban közvetlenül a helyszíni kiszolgálókat Azure Security Centerba helyezheti. Ezután megtekintheti a biztonsági javaslatok és riasztások összefoglalását közvetlenül a Windows felügyeleti központ felületén.

> [!NOTE]
> Az Azure-előfizetéshez és a hozzá tartozó Log Analytics munkaterülethez az Azure Defender engedélyezése szükséges ahhoz, hogy engedélyezze a Windows felügyeleti központ integrációját.
> Ha korábban még nem használta az előfizetést és a munkaterületet, az Azure Defender ingyenes az első 30 napban. További információkért tekintse meg [a díjszabási információkat ismertető oldalt](security-center-pricing.md).
>

Ha sikeresen felkészített egy kiszolgálót a Windows felügyeleti központból a Azure Security Centerba, a következőket teheti:

* Biztonsági riasztások és javaslatok megtekintése a Windows felügyeleti központ Security Center bővítményében
* Megtekintheti a biztonsági helyzeteket, és további részletes információkat kérhet le a Windows felügyeleti központ felügyelt kiszolgálóiról Security Center belül a Azure Portal (vagy egy API-n keresztül)

Ennek a két eszköznek a kombinálásával a Security Center lesz az egyetlen üvegtábla, amely az összes biztonsági információt megtekintheti, bármi is legyen az erőforrás: a Windows felügyeleti központ felügyelt helyszíni kiszolgálók, a virtuális gépek és a további Pásti munkaterhelések védelme.

## <a name="onboard-windows-admin-center-managed-servers-into-security-center"></a>Windows felügyeleti központ által felügyelt kiszolgálók előkészítése Security Center

1. A Windows felügyeleti központban válassza ki az egyik kiszolgálót, és az **eszközök** ablaktáblán válassza ki a Azure Security Center bővítményt:

    ![Azure Security Center bővítmény a Windows felügyeleti központban](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Ha a kiszolgáló már bekerült a Security Centerba, akkor a beállítás ablak nem jelenik meg.

1. Kattintson **a bejelentkezés az Azure-ba és a beállítás**elemre.
    ![Windows felügyeleti központ bővítmény bevezetése Azure Security Center](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. A kiszolgáló Security Centerhoz való összekapcsolásához kövesse az utasításokat. Miután megadta a szükséges adatokat, és megerősítette, Security Center végrehajtja a szükséges konfigurációs módosításokat, hogy a következők mindegyike igaz legyen:
    * Egy Azure-átjáró regisztrálva van.
    * A kiszolgálónak van egy munkaterülete, amelyről jelentést szeretne készíteni, és egy hozzá tartozó előfizetést.
    * Security Center Log Analytics megoldás engedélyezve van a munkaterületen. Ez a megoldás biztosítja az Azure Defender szolgáltatásait a munkaterületnek jelentő *összes* kiszolgáló és virtuális gép számára.
    * Az Azure Defender for Servers szolgáltatás engedélyezve van az előfizetésben.
    * A Log Analytics ügynök telepítve van a kiszolgálón, és úgy van konfigurálva, hogy a kijelölt munkaterületre jelentsen. Ha a kiszolgáló már jelentést készít egy másik munkaterületre, úgy van konfigurálva, hogy az újonnan kiválasztott munkaterületre is jelentsen.

    > [!NOTE]
    > A javaslatok megjelenése után eltarthat egy ideig. Valójában a kiszolgálói tevékenységtől függően előfordulhat, hogy *nem kap* riasztásokat. A riasztások tesztelésére szolgáló tesztelési riasztások létrehozásához kövesse a riasztás- [ellenőrzési eljárás](security-center-alert-validation.md)utasításait.


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>Biztonsági javaslatok és riasztások megtekintése a Windows felügyeleti központban

A bevezetést követően közvetlenül a Windows felügyeleti központ Azure Security Center területén tekintheti meg a riasztásokat és a javaslatokat. Egy javaslatra vagy egy riasztásra kattintva megtekintheti őket a Azure Portalban. Itt további információkhoz juthat, és megtudhatja, hogyan javíthatja a problémákat.

[![A Windows felügyeleti központban megtekintett javaslatok és riasztások Security Center](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>A Windows felügyeleti központ felügyelt kiszolgálóira vonatkozó biztonsági javaslatok és riasztások megtekintése Security Center
Azure Security Center:

* Ha meg szeretné tekinteni a Windows felügyeleti központ összes kiszolgálójának biztonsági javaslatait, nyissa meg az [adatkészletet](asset-inventory.md) és a szűrőt a vizsgálni kívánt gép típusára. Válassza a **virtuális gépek és számítógépek** lapot.

* A Windows felügyeleti központ összes kiszolgálójára vonatkozó biztonsági riasztások megtekintéséhez nyissa meg a **biztonsági riasztásokat**. Kattintson a **szűrő** lehetőségre, és győződjön meg arról, hogy **csak** "nem Azure" van kiválasztva:

    :::image type="content" source="./media/windows-admin-center-integration/filtering-alerts-by-environment.png" alt-text="Biztonsági riasztások szűrése Windows felügyeleti központ által felügyelt kiszolgálókon" lightbox="./media/windows-admin-center-integration/filtering-alerts-by-environment.png":::