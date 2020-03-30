---
title: Blockchain alkalmazásverzió - Azure Blockchain Workbench
description: Alkalmazásverziók használata az Azure Blockchain Workbench Előzetes verzióban.
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 45219790cf0cd064e0fcd456e262b2f93aa03ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74323910"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Az Azure Blockchain Workbench Előzetes alkalmazásverzió-verziója

Az Azure Blockchain Workbench Preview alkalmazás több verzióját is létrehozhatja és használhatja. Ha ugyanannak az alkalmazásnak több verziója van feltöltve, a verzióelőzmények elérhetők, és a felhasználók kiválaszthatják, hogy melyik verziót kívánják használni.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* A Blockchain Workbench üzembe helyezése. További információ: [Azure Blockchain Workbench deployment](deploy.md) a telepítéssel kapcsolatos részletekért
* Telepített blockchain alkalmazás a Blockchain Workbench-ben. Lásd: [Blockchain-alkalmazás létrehozása az Azure Blockchain Workbench alkalmazásban](create-app.md)

## <a name="add-an-app-version"></a>Alkalmazásverzió hozzáadása

Új verzió hozzáadásához töltse fel az új konfigurációs és intelligens szerződésfájlokat a Blockchain Workbench-be.

1. A webböngészőben keresse meg a Blockchain Workbench webcímét. A Blockchain `https://{workbench URL}.azurewebsites.net/` Workbench webcím megkeresésével kapcsolatos további tudnivalókért például a [Blockchain Workbench webes URL-címe](deploy.md#blockchain-workbench-web-url)
2. Jelentkezzen be [blockchain munkaterület-rendszergazdaként.](manage-users.md#manage-blockchain-workbench-administrators)
3. Válassza ki a másik verzióval frissíteni kívánt blockchain alkalmazást.
4. Válassza **a Version hozzáadása**lehetőséget. Megjelenik **a Verzió hozzáadása** ablaktábla.
5. Válassza ki a feltöltendő új verziószerződés-konfigurációt és szerződéskódfájlokat. A konfigurációs fájl automatikusan érvényesítésre kerül. Az alkalmazás telepítése előtt javítsa ki az érvényesítési hibákat.
6. Válassza **a Verzió hozzáadása lehetőséget** az új blockchain alkalmazásverzió hozzáadásához.

    ![Új verzió hozzáadása](media/version-app/add-version.png)

A blockchain alkalmazás telepítése eltarthat néhány percig. A telepítés befejezése után frissítse az alkalmazáslapot. Az alkalmazás kiválasztása és a **Korábbi verziógomb** kiválasztása megjeleníti az alkalmazás verzióelőzményeit.

> [!IMPORTANT]
> Az alkalmazás korábbi verziói le vannak tiltva. A korábbi verziók at egyenként újra engedélyezheti.
>
> Előfordulhat, hogy újra tagokat kell hozzáadnia az alkalmazásszerepkörökhöz, ha az új verzióban módosították az alkalmazásszerepköröket.

## <a name="using-app-versions"></a>Alkalmazásverziók használata

Alapértelmezés szerint az alkalmazás legújabb engedélyezett verziója a Blockchain Workbench-ben használatos. Ha egy alkalmazás korábbi verzióját szeretné használni, először ki kell választania a verziót az alkalmazáslapról.

1. A Blockchain Workbench alkalmazás szakaszban jelölje be a használni kívánt szerződést tartalmazó alkalmazás jelölőnégyzetet. Ha a korábbi verziók engedélyezve vannak, a korábbi verziók gombja elérhető.
2. Válassza a **Korábbi verziók** gombot.
3. A korábbi verziók ablaktáblán válassza ki az alkalmazás verzióját a *Módosítás dátuma* oszlopban lévő hivatkozás kiválasztásával.

    ![Korábbi verzió kiválasztása](media/version-app/use-version.png)

    Létrehozhat új szerződéseket, vagy műveleteket végrehajthat a korábbi verziószerződéseken. Az alkalmazás verziója az alkalmazás neve után jelenik meg, és egy figyelmeztetés jelenik meg a régebbi verzióval kapcsolatban.

## <a name="next-steps"></a>További lépések

* [Az Azure Blockchain Workbench hibaelhárítása](troubleshooting.md)
