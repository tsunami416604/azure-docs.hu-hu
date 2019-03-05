---
title: Hozzon létre munkafolyamatokat az IoT Central-összekötő az Azure Logic Appsben |} A Microsoft Docs
description: Az IoT Central-összekötő az Azure Logic Apps használatával aktiválhatja a munkafolyamatokat és létrehozása, frissítése és törlése eszköz a munkafolyamatokban.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 1/3/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 8fd46bdc5971ce1e9bcee6599dd73f09cc3c00be
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314347"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Hozzon létre munkafolyamatokat az IoT Central-összekötő az Azure Logic Appsben

*Ez a témakör létrehozói és a rendszergazdák vonatkozik.*

Azure Logic Apps használatával hozhat létre automatizált és méretezhető munkafolyamatok, amelyek az alkalmazások és adatok integrálása a cloud services és a helyszíni rendszerek között. Az Azure Logic Apps számos Azure-szolgáltatások, Microsoft-szolgáltatások és egyéb szoftverek A – szolgáltatásként nyújtott (SaaS-) termékek sok olyan összekötővel rendelkezik. Az IoT Central-összekötő használatával az Azure Logic Appsben, is beállíthat munkafolyamatokat az IoT-központ a szabály aktiválásakor. Használhatja a műveletek is az IoT Central-összekötő-eszköz létrehozásához, egy eszköz tulajdonságait és beállítások frissítése vagy eszköz törlése. 

Az IoT Central-összekötő a Microsoft Flow is használhatja. Azure Logic Apps és a Microsoft Flow olyan tervezőközpontú integrációs szolgáltatások, de célközönségekkel némileg eltérő. Flow segítségével bármely irodai dolgozó képes az üzleti munkafolyamatok szükségük van. A Logic Apps lehetővé teszi az informatikai szakemberek számára az adatok eléréséhez szükséges Integrációk készítéséhez. Hasonlítsa össze a Flow és Logic Apps [Itt](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs). Megtudhatja, hogyan hozhat létre az IoT Central-összekötő a Microsoft Flow munkafolyamatok [Itt](howto-add-microsoft-flow.md). 

## <a name="prerequisites"></a>Előfeltételek

- A Pay-As-You-Go application
- Egy Azure-fiókot és előfizetést hozhat létre, és a logikai alkalmazások kezelése

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Amikor egy szabály akkor lesz kiváltva munkafolyamat elindítására

Ez a szakasz bemutatja, hogyan üzenet küldése a Microsoft Teams, amikor egy szabály akkor lesz kiváltva. Konfigurálhatja a munkafolyamatba műveleteket, mint küldése egy eseményt az eseményközpontnak, Azure DevOps új munkaelem létrehozása vagy új sort beszúrni az SQL server más összekötők használatával.

1. Első lépésként [olyan szabályt hoz létre az IoT-központ](howto-create-telemetry-rules.md). Miután mentette a szabály feltételeit, jelölje be a **Azure Logic Apps** új műveletként csempére. Válassza ki **létrehozása az Azure Portalon**. Megnyílik az Azure Portalra egy új logikai alkalmazás létrehozásához. Szükség lehet bejelentkezni az Azure-fiókjával.

1. Adja meg a szükséges, hozzon létre egy új logikai alkalmazást. Kiválaszthatja azokat az új logikai alkalmazás létrehozásához Azure-előfizetés. Nincs kell ugyanabban az előfizetésben az IoT Central-alkalmazást sikeresen létrehozva. Kattintson a **Létrehozás** gombra.

    ![Logikai alkalmazás létrehozása az Azure Portalon](./media/howto-build-azure-logic-apps/createinazureportal.PNG)

1. Miután a logikai alkalmazás sikeresen létrejött, akkor automatikusan a felhasználóregisztráció a Logic Apps Designerben. Válassza ki **üres logikai alkalmazás**. 

    ![Üres logikai alkalmazás létrehozása](./media/howto-build-azure-logic-apps/blanklogicapp.PNG)

1. A tervezőben "iot central" kereshet, és válassza ki a **amikor egy szabály aktiválódik** eseményindító. Jelentkezzen be az összekötőt a fiókkal jelentkezik be az IoT-központ alkalmazás. 

    ![Jelentkezzen be az IoT Central-összekötő](./media/howto-build-azure-logic-apps/addtrigger.PNG)

1. A sikeres bejelentkezést követően megtekintheti a mezők jelennek meg. Válassza ki a **alkalmazás** és **szabály** a kijelölhető.

    ![Válassza ki az alkalmazás és a szabály](./media/howto-build-azure-logic-apps/pickappandrule.PNG)

1. Adjon meg új műveletet. Keresse meg **csapatok üzenet közzététele** válassza **üzenet küldése** a Microsoft Teams-összekötő. Jelentkezzen be az összekötőt, használhatja a Microsoft Teams-fiókkal. 

1. A műveletet, válassza a **csapat** és **csatorna**. Töltse ki a **üzenet** mezőt a választható tegyük fel, hogy minden üzenetet. Megadhat *dinamikus tartalom* az IoT-központ szabályból, mentén fontos információkat, például az eszköz és az időbélyegző-értesítések való átadásához.
    > [!NOTE]
    > Válassza ki a **Továbbiak** a dinamikus tartalom ablakban elolvashatja a mérés és tulajdonság értékei, amely kiváltotta a szabály a szöveget.

    ![Nyissa meg a logikai alkalmazás szerkesztési művelet dinamikus ablaktábla](./media/howto-build-azure-logic-apps/buildworkflow.PNG)

1. Ha elkészült a művelet szerkesztése, válassza ki **mentése**.

1. Ha visszatér az IoT Central alkalmazáshoz, megjelenik ez a szabály rendelkezik egy Azure Logic Apps műveletet az műveletek területen.

Mindig megkezdheti az IoT Central-összekötő az Azure Portalon a Logic Apps munkafolyamat létrehozásához. Ezután választhatja mely IoT Central-alkalmazást, és mely szabályt szeretne csatlakozni, és ugyanúgy továbbra is működni fognak. Hiba esetén nem kell minden alkalommal, amikor az IoT-központ szabályok lapon elindításához.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>Létrehozása, frissítése és a munkafolyamat eszköz törlése

Ha egy munkafolyamatot hoz létre a logikai alkalmazásban, az IoT Central-összekötő segítségével művelet is hozzáadhat. Tapasztalni fogja a **eszköz létrehozásához**, **egy eszköz frissítése**, és **eszköz törlése**.
> [!NOTE]
> A **egy eszköz frissítése** és **eszköz törlése**, frissíteni vagy törölni szeretné a meglévő eszköz Azonosítót kell. Az IoT-központ eszköz Azonosítójának lekéréséhez a böngésző URL-címben. Ez a **nem** megegyezik az eszköz azonosítója megjelenik az adatkezelő oldal.

![IoT-központ device explorer eszköz azonosítója](./media/howto-build-azure-logic-apps/iotcdeviceid.PNG)
  

## <a name="next-steps"></a>További lépések
Most, hogy megtanulhatta, hogyan használhatja a Microsoft Flow munkafolyamatok létrehozásához, a javasolt következő lépésre, hogy [Eszközkezelés](howto-manage-devices.md).
