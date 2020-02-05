---
title: Munkafolyamatok létrehozása a IoT Central-összekötővel Azure Logic Appsban | Microsoft Docs
description: A munkafolyamatok elindításához, illetve a munkafolyamatokban lévő eszközök létrehozásához, frissítéséhez és törléséhez használja a Azure Logic Apps IoT Central-összekötőt.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 3625d7374090bca73e2f054f7da4b58e951e7719
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990266"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Munkafolyamatok létrehozása a IoT Central-összekötővel Azure Logic Apps

*Ez a témakör az építők és a rendszergazdákra vonatkozik.*

A Azure Logic Apps használatával olyan automatizált skálázható munkafolyamatokat hozhat létre, amelyek a Cloud Services és a helyszíni rendszerek különböző alkalmazásait és szolgáltatásait integrálják. Azure Logic Apps számos különböző Azure-szolgáltatással, Microsoft-szolgáltatással és más szolgáltatott szoftveres (SaaS) termékkel rendelkező összekötővel rendelkezik. A Azure Logic Apps IoT Central-összekötőjét használva elindíthatja a munkafolyamatokat, amikor egy szabály aktiválódik a IoT Central. Az IoT Central-összekötő műveleteivel eszköz létrehozásához, az eszköz tulajdonságainak és beállításainak frissítéséhez, illetve az eszköz törléséhez is használhatja a műveleteket.

Használhatja a IoT Central-összekötőt Microsoft Flow. Mind a Azure Logic Apps, mind a Microsoft Flow tervező-első integrációs szolgáltatás, de kissé eltérő célközönséget céloz meg. A flow bármely irodai feldolgozó számára lehetővé teszi a szükséges üzleti munkafolyamatok felépítését. Logic Apps az informatikai szakemberek számára lehetővé teszi az adatkapcsolathoz szükséges integrációk összeállítását. A folyamat összehasonlítása és [Logic apps.](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs) Ismerje meg, hogyan hozhat létre munkafolyamatokat az IoT Central-összekötővel Microsoft Flow [itt](howto-add-microsoft-flow.md).

## <a name="prerequisites"></a>Előfeltételek

- Standard díjszabási csomag használatával létrehozott alkalmazás
- Egy Azure-fiók és-előfizetés logikai alkalmazások létrehozásához és kezeléséhez

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Munkafolyamat elindítása szabály indításakor

Ebből a szakaszból megtudhatja, hogyan küldhet üzenetet a Microsoft Teams szolgáltatásba egy szabály indításakor. A munkafolyamatot úgy is beállíthatja, hogy más összekötőket használjon olyan dolgok elvégzéséhez, mint például az Event hub-esemény küldése, új Azure DevOps-munkaelem létrehozása vagy új sor beszúrása az SQL Serverbe.

1. Első lépésként [hozzon létre egy szabályt a IoT Central](howto-create-telemetry-rules.md). Miután mentette a szabály feltételeit, válassza ki a **Azure Logic apps** csempét új műveletként. Válassza **a létrehozás lehetőséget Azure Portal**. A Azure Portal egy új logikai alkalmazás létrehozásához. Előfordulhat, hogy be kell jelentkeznie az Azure-fiókjába.

1. Adja meg a szükséges adatokat egy új logikai alkalmazás létrehozásához. Kiválaszthat egy Azure-előfizetést, amellyel kiépítheti az új logikai alkalmazást. Nem kell ugyanazt az előfizetést megadnia, amelyet a IoT Central alkalmazás a ben hozott létre. Kattintson a **Létrehozás** gombra.

    ![Logikai alkalmazás létrehozása Azure Portal](./media/howto-build-azure-logic-apps/createinazureportal.png)

1. A logikai alkalmazás sikeres létrehozása után a rendszer automatikusan megnyitja a Logic Apps Designer alkalmazást. Válassza az **üres logikai alkalmazás**lehetőséget. 

    ![Üres logikai alkalmazás létrehozása](./media/howto-build-azure-logic-apps/blanklogicapp.png)

1. A tervezőben keressen rá a "IOT Central" kifejezésre, és válassza ki, hogy mikor induljon el **egy szabály** . Jelentkezzen be az összekötőbe azzal a fiókkal, amelybe bejelentkezett a IoT Central alkalmazásba.

    ![Bejelentkezés IoT Central-összekötőbe](./media/howto-build-azure-logic-apps/addtrigger.png)

1. A sikeres bejelentkezés után meg kell jelennie a mezők megjelenítésének. Válassza ki az **alkalmazást** és a **szabályt** a legördülő listából.

    ![Alkalmazás és szabály kiválasztása](./media/howto-build-azure-logic-apps/pickappandrule.png)

1. Adjon hozzá egy új műveletet. Keressen rá a **post Message Teams** kifejezésre, és válassza a Microsoft Teams Connector **üzenet közzététele** elemét. Jelentkezzen be az összekötőbe a Microsoft Teams szolgáltatásban használt fiókkal.

1. A műveletben válassza ki a **csapatot** és a **csatornát**. Adja meg az **üzenet** mezőt, hogy mit szeretne mondani az egyes üzenetekhez. A *dinamikus tartalmat* a IoT Central szabályból is felhasználhatja, így a fontos információk, például az eszköz neve és az időbélyege is áthalad az értesítésre.
    > [!NOTE]
    > Jelölje be a **további** szöveg megjelenítése a dinamikus tartalom ablakban a szabályt kiváltó mérési és tulajdonságértékek beszerzéséhez.

    ![Logic app szerkesztési művelet dinamikus ablaktáblával megnyitva](./media/howto-build-azure-logic-apps/buildworkflow.png)

1. Ha elkészült a művelet szerkesztésével, válassza a **Mentés**lehetőséget.

1. Ha visszatér a IoT Central alkalmazáshoz, akkor ez a szabály Azure Logic Apps műveletet fog látni a műveletek területen.

A munkafolyamatot bármikor elindíthatja az IoT Central-összekötő használatával a Azure Portal Logic Apps. Ezután kiválaszthatja, hogy melyik IoT Central alkalmazáshoz és melyik szabályhoz csatlakozhat, és így is ugyanúgy működik. Minden alkalommal nem szükséges elindítani a IoT Central szabályok lapról.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>Eszköz létrehozása, frissítése és törlése egy munkafolyamatban

Amikor létrehoz egy munkafolyamatot a logikai alkalmazásban, hozzáadhat egy műveletet az IoT Central-összekötő használatával. Az elérhető műveletek az eszköz **létrehozása**, az **eszköz frissítése**és az **eszköz törlése**.

> [!NOTE]
> Az **eszköz frissítéséhez** és az **eszköz törléséhez**szüksége lesz a frissíteni vagy törölni kívánt meglévő eszköz azonosítójára. A IoT Central eszköz AZONOSÍTÓját a **Device Explorerban** érheti el

![IoT Central Device Explorer-eszköz azonosítója](./media/howto-build-azure-logic-apps/iotcdeviceid.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan használhatók a Microsoft Flow a munkafolyamatok létrehozásához, a javasolt következő lépés az [eszközök kezelése](howto-manage-devices.md).
