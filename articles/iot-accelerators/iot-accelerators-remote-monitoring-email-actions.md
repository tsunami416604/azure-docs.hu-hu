---
title: E-mail-művelet belül távoli figyelés – Azure |} A Microsoft Docs
description: Ez az útmutató bemutatja, hogyan e-mail-művelet hozzáadása egy új vagy meglévő szabályt.
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: 693da06ce14a4854d5db49f588fa29d791060166
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58108103"
---
# <a name="add-an-email-action"></a>E-mail-művelet hozzáadása

E-mail műveletek segítségével ellenőrizze, hogy ne maradjon le riasztásokat. E-mail-művelet adhat hozzá, a meglévő szabályokat, vagy ha létrehoz egy új szabályt.

Ez az útmutató a lépések elvégzéséhez, a távoli figyelési megoldásgyorsító telepített példányát az Azure-előfizetésben kell.

Hozzon létre vagy módosítsa egy szabályt, rendelkeznie kell egy [ **rendszergazdai**, vagy rendelkezik megfelelő engedélyekkel a](iot-accelerators-remote-monitoring-rbac.md).

## <a name="edit-an-existing-rule"></a>Meglévő szabály szerkesztése

Kövesse az alábbi lépéseket egy e-mail-művelet hozzáadása egy meglévő szabály:

1. Keresse meg a távoli figyelési megoldás.

1. Az a **irányítópult**, keresse meg a **szabályok** oldalon:

    ![Szabályok lap](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. A jelölőnégyzet bejelölésével módosíthatja, és kattintson a meglévő szabály mellett **szerkesztése** tetején. Egy szerkeszthető **szabály** panel jelenik meg.

1. Az a **művelet** szakaszban, a ritkáról **engedélyezett E-mail** való **a**.

1. Az első alkalommal e-mail-művelet a megoldásgyorsító engedélyeznie kell [jelentkezzen be az Outlook](#outlook).

1. Adjon meg egy e-mail-címet a címzett mezőbe, és nyomja le az **Enter** kulcs minden e-mail-cím hozzáadása:

    ![Cím bejegyzés](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Adja meg az e-mail tárgyát.

1. Egyszerű szövegként adja meg az e-mail címzettjeit további megjegyzéseket. Használhatja a HTML-formázását, ha Ön [e-mailt sablon szerkesztése](#htmledit).

1. Győződjön meg arról, hogy a **szabály állapota** értékre van állítva **engedélyezve**.

1. Kattintson az **Alkalmaz** gombra.

## <a name="create-a-new-rule"></a>Új szabály létrehozása

E-mail-művelet hozzáadása, ha létrehoz egy új szabályt az alábbi lépéseket követve:

1. Keresse meg a távoli figyelési megoldás.

1. Az a **irányítópult**, keresse meg a **szabályok** oldalon:

    ![Szabályok lap](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Kövesse a [hozzon létre egy szabályt szakaszt](iot-accelerators-remote-monitoring-automate.md#create-a-rule). A következő lépéseit a [egy olyan speciális szabályt](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) szakasz addig a pontig, ahol beállíthat egy **súlyossági szintet**. Ne kattintson **alkalmaz** még.

1. Az a **művelet** szakaszban, a ritkáról **engedélyezett E-mail** való **a**.

1. Az első alkalommal e-mail-művelet a megoldásgyorsító engedélyeznie kell [jelentkezzen be az Outlook](#outlook).

1. Adjon meg egy e-mail-címet a címzett mezőbe, és nyomja le az **Enter** kulcs minden e-mail-cím hozzáadása:

    ![Cím bejegyzés](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Adja meg az e-mail tárgyát.

1. Egyszerű szövegként adja meg az e-mail címzettjeit további megjegyzéseket. Használhatja a HTML-formázását, ha Ön [e-mailt sablon szerkesztése](#htmledit).

1. Győződjön meg arról, hogy a **szabály állapota** értékre van állítva **engedélyezve**.

1. Kattintson az **Alkalmaz** gombra.

Az e-mail-művelet a szabály engedélyezve van. A művelet akkor aktiválódik, amikor új e-mail küldése a címzetteknek.

## Jelentkezzen be az Outlook <a name="outlook"></a>

Az e-mail-művelet engedélyezi a megoldásgyorsító az első alkalommal, be kell jelentkeznie az Outlook. Ez a művelet állítja be az e-mail-fiók, amely az e-mailben értesítést küld.

> [!NOTE]
> Hozzon létre egy adott Outlook-fiókot csak a megoldás gyorsító értesítések kell, és ezt a fiókot használja, amikor engedélyezi az első e-mail-művelet.

### <a name="contributor-role-outlook-setup"></a>Közreműködői szerepkör Outlook beállítása

Ha valaki van a **közreműködői** szerepkör az előfizetésben a megoldásgyorsító üzembe helyezve, az alkalmazás nem rendelkezik megfelelő engedélyekkel, állítsa be, és ellenőrzi az e-mailes műveletek a webes felhasználói felületen.

A Kezdés előtt hozzon létre egy Outlook-e-mail-értesítések küldése a megoldásgyorsító használandó fiókot.

A következő lépések bemutatják, hogyan állíthatja be, és manuálisan ellenőrizze az e-mail-műveletek:

1. Lépjen az [Azure Portalra](https://portal.azure.com).

1. Keresse meg a megoldásgyorsító erőforráscsoportját.

1. Kattintson a **Office 365-összekötő**:

    ![API-kapcsolat](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector.png)

1. Kattintson a szalagcímre az engedélyezési folyamat megkezdéséhez:

    ![engedélyezés](./media/iot-accelerators-remote-monitoring-email-actions/connector.png)

1. Kattintson a **engedélyezése**. Kéri, hogy jelentkezzen be. A bejelentkezéshez használt fióknak kell lennie az e-mail-cím az alkalmazás használatával küldjön értesítő:

    ![Gomb engedélyezése](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Kattintson a **mentése** alján. Az engedélyezési lesz sikeres, ha a fejléc olyan szűnt.

1. Az e-mail-cím, amelyről az értesítéseket küldeni a módosításához kattintson **szerkesztése API-kapcsolat**.

    ![e-mail-cím módosítása](./media/iot-accelerators-remote-monitoring-email-actions/editemail.png)

### <a name="owner-role-outlook-setup"></a>Tulajdonosi szerepkör Outlook beállítása

Ha valaki van a **tulajdonosa** szerepkör az előfizetésben a megoldásgyorsító üzembe helyezve, az alkalmazás ellenőrizheti az e-mailes műveletek megfelelően be van állítva a webes felhasználói felületen.

A Kezdés előtt hozzon létre egy Outlook-e-mail-értesítések küldése a megoldásgyorsító használandó fiókot.

A következő lépések segítségével jelentkezzen be, és állítsa be az e-mail-műveletek:

1. Ide kattintva jelentkezzen be az Outlook. Ekkor átkerül az Azure Portalon:

   ![Jelentkezzen be az Outlook](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Kattintson a **engedélyezése**. Kéri, hogy jelentkezzen be. A bejelentkezéshez használt fióknak kell lennie az e-mail-cím az alkalmazás használatával küldjön értesítő:

1. Kattintson a **Save** (Mentés) gombra. Térjen vissza a megoldásgyorsító, és frissítse az oldalt.

1. Az e-mail-értesítés sikeresen konfigurálta, a következő üzenet jelenik meg:

   ![A sikeres Outlook jelentkezzen be](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## Testre szabhatja az e-mail HTML <a name="htmledit"></a>

A-beépített, a távoli figyelési megoldásgyorsító biztosít egy alapszintű HTML-sablont a művelet e-maileket. Az e-mail-sablont az e-mail-művelet beállítások értékeit használja. Íme egy példa e-mailt:

![e-mail-példa](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate.png)

A következő lépések bemutatják, hogyan HTML formátumú e-mailt sablon szerkesztéséhez. Például további információval, vagy adja hozzá az egyéni lemezképek:

1. Klónozza a Java vagy a számítógép távoli figyelés .NET GitHub-adattárát:

1. Keresse meg az e-mail sablont helyére:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. Adja hozzá, vagy távolítsa el a paramétereket a sablonban, hogy testre szabta az üzenetet. Adja hozzá, távolítsa el, vagy igény szerint cserélje le a hívást:

    Például a .NET-kódot:  `emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Például a Java kódban:  `this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. A sablonban szereplő paraméterekkel formájában `${...}`. Egy paraméter törléséhez törölje a kért sor. Adjon hozzá egy paramétert, adjon hozzá egy sort beszúrni a értékkel.

1. Képek vagy egyéni szöveg hozzáadása, frissítése közvetlenül a EmailTemplate.HTML fájlt.

## <a name="throttling"></a>Throttling

A távoli figyelési megoldásgyorsító Outlook használatával e-mail értesítések küldéséhez. Az Outlook korlátozza az e-mailekhez [30 e-mailek száma 1 percenként](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits). Fogadásakor az e-mail-ügyfélprogramokat is előfordulhat, hogy szabályozás küldött e-mailekben percenkénti számát. Ellenőrizze a megadott levelezési ügyfeléről korlátozások. E-mail értesítési szabály beállításakor a szabály kiszámítsa átlagos értékek, legalább egy perces időszak alatt, és nem azonnali értékeket használja:

![Átlagszámítás](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>További lépések

Ez az útmutató az e-mail-művelet hozzáadása a távoli figyelési megoldás egy új vagy meglévő szabály mutatott. Az útmutató is kimutatta, és a HTML-kódot, amely meghatározza az üzenet formátuma szerkesztése.

A javasolt következő lépésre további [riasztások és segítése az Eszközproblémák](iot-accelerators-remote-monitoring-maintain.md).
