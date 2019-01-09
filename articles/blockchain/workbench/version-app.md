---
title: Azure Blockchain Workbench használatával a Blockchain alkalmazás verziószámozása
description: Hogyan használható az alkalmazás verziószáma az Azure Blockchain Workbench alkalmazásban.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 1/8/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: c4c5dcf9c83b79158e3459e79db6dd066d982fc8
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54108257"
---
# <a name="azure-blockchain-workbench-application-versioning"></a>Az Azure Blockchain Workbench alkalmazás verziószámozása

Hozzon létre, és az Azure Blockchain Workbench alkalmazás több verzióját használja. Ugyanannak az alkalmazásnak több verziója feltöltésekor a rendszer, ha egy korábbi verziók érhető el, és a felhasználók eldönthetik, melyik verzióját szeretnék használni.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* A Blockchain Workbench üzembe helyezés. További információkért lásd: [Azure Blockchain Workbench üzembe helyezési](deploy.md) részleteket az üzembe helyezés
* Egy telepített blockchain alkalmazás a Blockchain Workbenchet. Lásd: [a blockchain-alkalmazások létrehozása az Azure Blockchain Workbench használatával](create-app.md)

## <a name="add-an-app-version"></a>Egy alkalmazás verzió hozzáadása

Az új verzió hozzáadása, töltse fel az új konfigurációt és intelligens szerződés fájlok Blockchain Workbenchet.

1. Keresse meg a Blockchain Workbenchet webhely címét egy webböngészőben. Ha például `https://{workbench URL}.azurewebsites.net/` információk megkereséséhez a Blockchain Workbenchet webcím: [Blockchain Workbench webes URL-címe](deploy.md#blockchain-workbench-web-url)
2. Jelentkezzen be egy [Blockchain Workbenchet rendszergazda](manage-users.md#manage-blockchain-workbench-administrators).
3. Válassza ki a blockchain-alkalmazást, egy másik verziójával frissíti.
4. Válassza ki **Hozzáadás verzió**. A **Hozzáadás verzió** ablaktáblán jelenik meg.
5. Válassza ki az új verzió szerződés konfigurációt, és a szerződés kódfájlok feltölteni. A konfigurációs fájl automatikusan érvényesítve lesz. Javítsa az esetleges érvényesítési hibákat, az alkalmazás telepítése előtt.
6. Válassza ki **Hozzáadás verzió** hozzáadni az új blockchain-application verziót.

    ![Új verzió hozzáadása](media/version-app/add-version.png)

A blockchain-alkalmazás központi telepítésének pár percet is igénybe vehet. Amikor a telepítés befejeződött, frissítse az alkalmazás lapot. Az alkalmazás kiválasztása, és kiválasztja a **korábbi verziók** gombra kattint, az alkalmazás korábbi verzióinak jeleníti meg.

> [!IMPORTANT]
> Az alkalmazás korábbi verzióinak le vannak tiltva. Külön-külön engedélyezheti újra korábbi verziók.
>
> Szükség lehet, ha újra hozzá tagokat az alkalmazás-szerepkörök módosításai az alkalmazás-szerepkörök, az új verzióban.

## <a name="using-app-versions"></a>Alkalmazás-verziók használata

Alapértelmezés szerint az alkalmazás legújabb engedélyezett verziójának Blockchain Workbenchet használatos. Ha szeretne egy alkalmazás korábbi verzióját használja, először az alkalmazás oldalán válassza ki a verziót szeretné.

1. A Blockchain Workbench alkalmazás szakaszban jelölje be a alkalmazást, amely tartalmazza a használni kívánt szerződést. Ha a korábbi verziók engedélyezve vannak, a verzió előzmények gomb érhető el.
2. Válassza ki a **korábbi verziók** gombra.
3. A verzió-előzmények ablaktábláján válassza az alkalmazás verziója található hivatkozásra kattintva a *módosítás dátuma* oszlop.

    ![Válassza ki az előző verzió](media/version-app/use-version.png)

    Hozzon létre új szerződéseket, vagy korábbi verzió szerződések műveleteket. Az alkalmazás verziója jelenik meg az alkalmazás nevét a következő, és a régebbi verzióval kapcsolatos figyelmeztetés jelenik meg.

## <a name="next-steps"></a>További lépések

* [Hibaelhárítás az Azure Blockchain Workbench használatával](troubleshooting.md)
