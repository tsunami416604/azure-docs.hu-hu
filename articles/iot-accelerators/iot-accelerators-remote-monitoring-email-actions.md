---
title: E-mail-művelet a távoli figyelésen belül – Azure | Microsoft Docs
description: Ez a útmutató bemutatja, hogyan adhat hozzá e-mail-műveleteket egy új vagy meglévő szabályhoz.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: c192ba73da0cfaf1832b6a1e572bd71b250a976b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168367"
---
# <a name="add-an-email-action"></a>E-mail művelet hozzáadása

Az e-mail-műveletek segítenek meggyőződni arról, hogy soha nem hiányzik a riasztás. Hozzáadhat egy e-mail-műveletet egy meglévő szabályhoz, vagy új szabályt is létrehozhat.

A jelen útmutató lépéseinek végrehajtásához a távoli figyelési megoldás gyorsító üzembe helyezett példányára van szükség az Azure-előfizetésében.

Szabály létrehozásához vagy módosításához [ **rendszergazdának**kell lennie, vagy rendelkeznie kell a megfelelő engedélyekkel](iot-accelerators-remote-monitoring-rbac.md).

## <a name="edit-an-existing-rule"></a>Meglévő szabály szerkesztése

Az alábbi lépéseket követve adhat hozzá egy e-mail-műveletet egy meglévő szabályhoz:

1. Navigáljon a távoli figyelési megoldáshoz.

1. Az **irányítópulton**navigáljon a **szabályok** lapra:

    ![Szabályok lap](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Kattintson a meglévő szabály melletti jelölőnégyzetre, majd kattintson a felül található **Szerkesztés** gombra. Megjelenik egy szerkeszthető **szabály** panel.

1. A **művelet** szakaszban kapcsolja be az **e-maileket engedélyezve** **a következőre:.**

1. Amikor először engedélyez egy e-mail-műveletet a megoldás-gyorsító alkalmazásban, be kell [jelentkeznie az Outlookba](#outlook).

1. Írjon be egy e-mail-címet a Címzett mezőbe, és nyomja le az **ENTER** billentyűt minden olyan e-mail cím esetében, amelyet hozzá szeretne adni:

    ![Címterület](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Adja meg az e-mail tárgyát.

1. Adja meg az e-mail-címzettek további megjegyzéseit egyszerű szövegként. Ha [szerkeszti az e-mail-sablont](#htmledit), HTML-formázást is használhat.

1. Győződjön meg arról, hogy a **szabály állapota** **engedélyezve**értékre van állítva.

1. Kattintson az **Alkalmaz** gombra.

## <a name="create-a-new-rule"></a>Új szabály létrehozása

Új szabály létrehozásakor kövesse az alábbi lépéseket egy e-mail-művelet hozzáadásához:

1. Navigáljon a távoli figyelési megoldáshoz.

1. Az **irányítópulton**navigáljon a **szabályok** lapra:

    ![Szabályok lap](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Kövesse a [szabály létrehozása szakasz](iot-accelerators-remote-monitoring-automate.md#create-a-rule)lépéseit. Kövesse a [speciális szabály létrehozása](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) című szakasz lépéseit egészen addig a pontig, ahol a **súlyossági szintet**beállította. Az **alkalmazás** még nem kattint.

1. A **művelet** szakaszban kapcsolja be az **e-maileket engedélyezve** **a következőre:.**

1. Amikor először engedélyez egy e-mail-műveletet a megoldás-gyorsító alkalmazásban, be kell [jelentkeznie az Outlookba](#outlook).

1. Írjon be egy e-mail-címet a Címzett mezőbe, és nyomja le az **ENTER** billentyűt minden olyan e-mail cím esetében, amelyet hozzá szeretne adni:

    ![Címterület](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Adja meg az e-mail tárgyát.

1. Adja meg az e-mail-címzettek további megjegyzéseit egyszerű szövegként. Ha [szerkeszti az e-mail-sablont](#htmledit), HTML-formázást is használhat.

1. Győződjön meg arról, hogy a **szabály állapota** **engedélyezve**értékre van állítva.

1. Kattintson az **Alkalmaz** gombra.

Az e-mail-művelettel rendelkező szabály már engedélyezve van. A művelet minden indításakor új e-mailt küld a címzetteknek.

## Bejelentkezés az Outlookba<a name="outlook"></a>

Amikor első alkalommal engedélyez egy e-mail-műveletet a megoldás-gyorsító alkalmazásban, be kell jelentkeznie az Outlookba. Ez a művelet beállítja az e-mail-értesítéseket küldő e-mail-fiókot.

> [!NOTE]
> Hozzon létre egy adott Outlook-fiókot csak a megoldás-gyorsító értesítéseire, és használja ezt a fiókot az első e-mail-művelet engedélyezésekor.

### <a name="contributor-role-outlook-setup"></a>Közreműködői szerepkör Outlook telepítője

Ha az előfizetés **közreműködői** szerepkörében valaki a megoldás-gyorsító üzembe helyezését állította be, akkor az alkalmazás nem rendelkezik megfelelő engedélyekkel az e-mail-műveletek beállításához és ellenőrzéséhez a webes felhasználói felületen.

Mielőtt elkezdené, hozzon létre egy Outlook-fiókot, amellyel e-mailes értesítéseket küldhet a megoldás-gyorsító szolgáltatásból.

Az alábbi lépések bemutatják, hogyan állíthatja be és ellenőrizheti az e-mail műveleteket manuálisan:

1. Lépjen az [Azure Portalra](https://portal.azure.com).

1. Navigáljon a megoldás-gyorsító erőforráscsoporthoz.

1. Kattintson a **Office 365-összekötőre**:

    ![API-kapcsolatok](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector1.png)

1. Az engedélyezési folyamat megkezdéséhez kattintson a szalagcímre:

    ![engedélyezik](./media/iot-accelerators-remote-monitoring-email-actions/connector1.png)

1. Kattintson az **Engedélyezés**gombra. A rendszer felszólítja, hogy jelentkezzen be. A bejelentkezéshez használt fióknak annak az e-mail-címnek kell lennie, amelyet az alkalmazás az e-mailes értesítések küldéséhez használ:

    ![Engedélyezés gomb](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Kattintson a **Save (Mentés** ) gombra a lap alján. Az engedélyezés sikeres lesz, ha a szalagcím eltűnik.

1. Ha módosítani szeretné az e-mail-címet, amelyről az értesítéseket küldik, kattintson az **API-kapcsolatok szerkesztése**lehetőségre.

    ![e-mail módosítása](./media/iot-accelerators-remote-monitoring-email-actions/editemail1.png)

### <a name="owner-role-outlook-setup"></a>Tulajdonosi szerepkör Outlook telepítője

Ha az előfizetésben lévő **tulajdonosi** szerepkörben valaki a megoldás-gyorsító üzembe helyezését állította be, az alkalmazás ellenőrizheti, hogy az e-mail-műveletek helyesen lettek-e beállítva a webes felhasználói felületen.

Mielőtt elkezdené, hozzon létre egy Outlook-fiókot, amellyel e-mailes értesítéseket küldhet a megoldás-gyorsító szolgáltatásból.

A következő lépések segítséget nyújtanak a bejelentkezéshez és az e-mail-műveletek beállításához:

1. Kattintson ide az Outlookba való bejelentkezéshez. A Azure Portal:

   ![Bejelentkezés az Outlookba](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Kattintson az **Engedélyezés**gombra. A rendszer felszólítja, hogy jelentkezzen be. A bejelentkezéshez használt fióknak annak az e-mail-címnek kell lennie, amelyet az alkalmazás az e-mailes értesítések küldéséhez használ:

1. Kattintson a **Save** (Mentés) gombra. Térjen vissza a megoldás-gyorsító oldalára, és frissítse az oldalt.

1. Ha sikeresen konfigurálta az e-mail-értesítést, a következő üzenet jelenik meg:

   ![Sikeres Outlook-bejelentkezés](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## Az e-mail HTML testreszabása<a name="htmledit"></a>

A távoli figyelési megoldás gyorsítása beépített, alapszintű HTML-sablont biztosít a műveleti e-mailekhez. Az e-mail sablon az e-mail művelet beállításaiban szereplő értékeket használja. Íme egy példa e-mailben:

![e-mail példa](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate1.png)

A következő lépések bemutatják, hogyan szerkesztheti a HTML e-mail-sablont. Például több információt is megadhat, vagy hozzáadhat egyéni lemezképeket:

1. Klón vagy a Java vagy a .NET távoli monitorozási GitHub-tárháza:

1. Navigáljon az e-mail sablon helyéhez:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. Az üzenet testreszabásához hozzáadhat vagy eltávolíthat bármilyen paramétert ebben a sablonban. A hívásokat igény szerint is hozzáadhatja, eltávolíthatja vagy lecserélheti:

    Például a .NET-kódban: `emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Például a Java-kódban: `this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. A sablonban szereplő paraméterek `${...}`formáját alkotják. Egy paraméter törléséhez törölje a szükséges sort. Paraméter hozzáadásához adjon hozzá egy sort a beszúrandó értékkel.

1. Képek vagy egyéni szöveg hozzáadásához közvetlenül frissítse a EmailTemplate. HTML fájlt.

## <a name="throttling"></a>Szabályozás

A távoli figyelési megoldás gyorsítása az Outlook használatával küld e-mail-értesítéseket. Az Outlook az e-mailek számát [1 percenként 30 e-mail](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits)-címre korlátozza. Az e-maileket fogadó e-mail-ügyfélprogramok a percenként fogadott e-mailek számát is szabályozhatja. A korlátozásokkal kapcsolatban érdeklődjön az e-mail ügyfélprogramban. Ha e-mail-értesítést állít be egy szabályhoz, a szabálynak legalább egy percen belül ki kell számítania az átlagos értékeket, és nem kell azonnali értékeket használnia:

![Átlagos számítás](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>Következő lépések

Ez az útmutató bemutatja, hogyan adhat hozzá e-mail-műveleteket egy új vagy egy meglévő szabályhoz egy távoli figyelési megoldáson belül. Az útmutató emellett azt is bemutatja, hogyan szerkesztheti az üzenet formátumát meghatározó HTML-kódot.

A javasolt következő lépés a [riasztások használatának és az eszközök problémáinak elhárítása](iot-accelerators-remote-monitoring-maintain.md).
