---
title: "Power BI Embedded kapacitás létrehozása az Azure portálon |} Microsoft Docs"
description: "Ez a cikk végigvezeti a Power BI Embedded kapacitás létrehozása a Microsoft Azure-ban."
services: power-bi-embedded
documentationcenter: 
author: guyinacube
manager: erikre
editor: 
tags: 
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: asaxton
ms.openlocfilehash: bf7eb967760338626cd7e0465b16dc570e5f582a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="create-power-bi-embedded-capacity-in-the-azure-portal"></a>Power BI Embedded kapacitás létrehozása az Azure portálon

Ez a cikk végigvezeti a Power BI Embedded kapacitás létrehozása a Microsoft Azure-ban. A Power BI Embedded leegyszerűsíti a Power BI képességek segítenek hatásos látványelemeket, jelentések és irányítópultok gyorsan hozzáadhatja saját alkalmazásokba.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

> [!VIDEO https://www.youtube.com/embed/aXrvFfg_iSk]

## <a name="before-you-begin"></a>Előkészületek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

* **Azure-előfizetés:** látogasson el [Azure ingyenes próbaverzió](https://azure.microsoft.com/free/) -fiók létrehozása.
* **Az Azure Active Directory:** előfizetését az Azure Active Directory (AAD) bérlő társítani kell. Emellett a ***kell jelentkeznie az Azure-bA egy olyan fiókkal, hogy a bérlő***. Microsoft-fiókok nem támogatottak. További tudnivalókért tekintse meg a hitelesítés és a felhasználói engedélyek.
* **A Power BI bérlői:** legalább egy fiókot az AAD-bérlőben kell feliratkozott a Power bi-ban.
* **Erőforráscsoport:** már van erőforráscsoport használata vagy [hozzon létre egy újat](../azure-resource-manager/resource-group-overview.md).

## <a name="create-a-capacity"></a>Hozzon létre egy kapacitás

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza ki **hozzon létre egy erőforrást** > **adatok + analitika**.

3. A keresőmezőbe, keresse meg a *Power BI Embedded*.

4. Válassza ki a Power BI Embedded belül **létrehozása**.

5. Adja meg a szükséges adatokat, és válassza ki **létrehozása**.

    ![Töltse ki az új kapacitás létrehozásához mezők](media/create-capacity/azure-portal-create-power-bi-embedded.png)

    |Beállítás |Leírás |
    |---------|---------|
    |**Erőforrás neve**|A kapacitás azonosító nevet. A Power BI felügyeleti portálon az Azure portálon kívül az erőforrás neve jelenik meg.|
    |**Előfizetés**|Az előfizetés szeretné létrehozni a kapacitás ellen.|
    |**Erőforráscsoport**|Ez az új kapacitás tartalmazó erőforráscsoportot. Válasszon egy meglévő erőforráscsoportot címet, vagy hozzon létre egy másikra. További információk: [Azure Resource Manager overview](../azure-resource-manager/resource-group-overview.md) (Az Azure Resource Manager áttekintése).|
    |**A Power BI kapacitás rendszergazda**|A Power BI kapacitás rendszergazdák a kapacitás megtekintheti a Power BI felügyeleti portálon és hozzárendelés engedélyeket ad más felhasználók számára. Alapértelmezés szerint a kapacitás rendszergazda fiókja. A kapacitás rendszergazdának a Power BI-bérlő belül kell lennie.|
    |**Hely**|A hely, ahol a Power BI tárolása a bérlő számára. Ez a beállítás nem választható ki egy másik helyen automatikusan megoldódik.|
    |**Tarifacsomag**|Válassza ki az igényeinek megfelelő Termékváltozat (v magos száma és memória mérete).  További információkért lásd: [Power BI Embedded díjszabása](https://azure.microsoft.com/pricing/details/power-bi-embedded/)|

6. Kattintson a **Létrehozás** gombra.

Létrehozási általában tesz egy percet; gyakran csak néhány másodpercig. Ha a kiválasztott **rögzítés az irányítópulton**, keresse meg az irányítópultot az új kapacitás. Vagy keresse meg **minden szolgáltatás** > **Power BI Embedded** megtekintéséhez, hogy készen áll-e a kapacitást.

![A Power BI Embedded kapacitással rendelkező Azure-portál irányítópultjának](media/create-capacity/azure-portal-dashboard.png)

## <a name="next-steps"></a>További lépések

Az új Power BI Embedded kapacitás használatához tallózással keresse meg a Power BI felügyeleti portál munkaterületek hozzárendelni. További információkért lásd: [Kapacitások kezelése a Power BI Premium és a Power BI Embedded szolgáltatásban](https://powerbi.microsoft.com/documentation/powerbi-admin-premium-manage/).

Ha nem szeretné használni, a kapacitás, szüneteltetése számlázási leállást. További információkért lásd: [Pause és indítsa el a Power BI Embedded kapacitás és az Azure-portálon](pause-start.md).

A Power BI-tartalmakat az alkalmazáson belül beágyazás megkezdéséhez tekintse meg a [beágyazása a Power BI irányítópultok, jelentések és csempék](https://powerbi.microsoft.com/documentation/powerbi-developer-embedding-content/).

További kérdései vannak? [Próbálja a kérése a Power BI-Közösség](http://community.powerbi.com/)