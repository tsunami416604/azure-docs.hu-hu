---
title: E-mail művelet a távfigyelésen belül - Azure | Microsoft dokumentumok
description: Ez az útmutató bemutatja, hogyan vehet fel e-mail műveletet egy új vagy meglévő szabályhoz.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: c192ba73da0cfaf1832b6a1e572bd71b250a976b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168367"
---
# <a name="add-an-email-action"></a>E-mail művelet hozzáadása

Az e-mailes műveletek segítenek abban, hogy soha ne maradjon le a riasztásokról. E-mail műveletet hozzáadhat egy meglévő szabályhoz, vagy amikor új szabályt hoz létre.

Az útmutató lépései végrehajtásához szüksége van a távfigyelési megoldás gyorsító üzembe helyezett példányára az Azure-előfizetésben.

Szabály létrehozásához vagy módosításához [ **rendszergazdának**kell lennie, vagy megfelelő engedélyekkel kell rendelkeznie.](iot-accelerators-remote-monitoring-rbac.md)

## <a name="edit-an-existing-rule"></a>Meglévő szabály szerkesztése

Az alábbi lépésekkel e-mailműveletet adhat egy meglévő szabályhoz:

1. Nyissa meg a távoli figyelési megoldást.

1. Az **Irányítópulton**keresse meg a **Szabályok** lapot:

    ![Szabályok lap](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Kattintson a módosítani kívánt meglévő szabály melletti jelölőnégyzetre, majd a lap tetején kattintson a **Szerkesztés** gombra. Megjelenik egy szerkeszthető **szabálypanel.**

1. A **Művelet szakaszban** kapcsolja be az **E-mail bekapcsolva** értékre való **bekapcsolót.**

1. Amikor először engedélyez egy e-mail műveletet a megoldásgyorsítóban, be kell [jelentkeznie az Outlook ba.](#outlook)

1. Írjon be egy e-mail címet a címzett mezőbe, és nyomja le az **Enter** billentyűt minden egyes e-mail címhez a következőhöz:

    ![Címbejegyzés](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Adja meg az e-mail tárgyát.

1. Adja meg az e-mail címzettjeinek további jegyzeteit egyszerű szövegként. A HTML-formázást az [e-mail sablon szerkesztése](#htmledit)esetén is használhatja.

1. Győződjön meg arról, hogy a **szabály állapota** Engedélyezve értékre van **állítva.**

1. Kattintson az **Alkalmaz** gombra.

## <a name="create-a-new-rule"></a>Új szabály létrehozása

Új szabály létrehozásakor az alábbi lépésekkel vehet fel e-mail műveletet:

1. Nyissa meg a távoli figyelési megoldást.

1. Az **Irányítópulton**keresse meg a **Szabályok** lapot:

    ![Szabályok lap](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Kövesse a [szabály létrehozása szakasz lépéseit.](iot-accelerators-remote-monitoring-automate.md#create-a-rule) A speciális szabály [létrehozása](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) szakaszlépéseit addig a pontig kövesse, ahol **a Súlyossági szintet**be kell állítania. Még ne kattintson **az Alkalmaz gombra.**

1. A **Művelet szakaszban** kapcsolja be az **E-mail bekapcsolva** értékre való **bekapcsolót.**

1. Amikor először engedélyez egy e-mail műveletet a megoldásgyorsítóban, be kell [jelentkeznie az Outlook ba.](#outlook)

1. Írjon be egy e-mail címet a címzett mezőbe, és nyomja le az **Enter** billentyűt minden egyes e-mail címhez a következőhöz:

    ![Címbejegyzés](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. Adja meg az e-mail tárgyát.

1. Adja meg az e-mail címzettjeinek további jegyzeteit egyszerű szövegként. A HTML-formázást az [e-mail sablon szerkesztése](#htmledit)esetén is használhatja.

1. Győződjön meg arról, hogy a **szabály állapota** Engedélyezve értékre van **állítva.**

1. Kattintson az **Alkalmaz** gombra.

Az e-mail művelettel rendelkező szabály most már engedélyezve van. Minden alkalommal, amikor a művelet aktiválódik, egy új e-mailt küld a címzetteknek.

## <a name="sign-in-to-outlook"></a>Bejelentkezés az Outlookba<a name="outlook"></a>

Amikor először engedélyez egy e-mail műveletet a megoldásgyorsítóban, be kell jelentkeznie az Outlookba. Ez a művelet beállítja az e-mail-értesítéseket küldő e-mail fiókot.

> [!NOTE]
> Létre kell hoznia egy adott Outlook-fiókot csak a megoldásgyorsító értesítésekhez, és ezt a fiókot kell használnia az első e-mail művelet engedélyezésekor.

### <a name="contributor-role-outlook-setup"></a>Közreműködői szerepkör Az Outlook beállítása

Ha valaki az előfizetés **közreműködői** szerepkörében üzembe helyezte a megoldásgyorsítót, az alkalmazás nem rendelkezik megfelelő engedélyekkel a webes felhasználói felületen keresztül végzett e-mail műveletek beállításához és ellenőrzéséhez.

Mielőtt elkezdené, hozzon létre egy Outlook-fiókot, amelynek segítségével e-mail értesítéseket küldhet a megoldásgyorsítóból.

A következő lépések bemutatják, hogyan állíthatja be és ellenőrizheti manuálisan az e-mail műveleteket:

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)

1. Keresse meg a megoldásgyorsító erőforráscsoportját.

1. Kattintson az **office365-összekötőre:**

    ![API-kapcsolat](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector1.png)

1. Az engedélyezési folyamat megkezdéséhez kattintson a szalagerre:

    ![engedélyezés](./media/iot-accelerators-remote-monitoring-email-actions/connector1.png)

1. Kattintson **az Engedélyezés gombra.** A rendszer kéri a bejelentkezést. A bejelentkezéshez használt fióknak az az e-mail címnek kell lennie, amelyet az alkalmazás az e-mail értesítések küldéséhez használ:

    ![Engedélyezés gomb](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Kattintson alul a **Mentés** gombra. Az engedélyed sikeres lesz, ha a transzparens eltűnik.

1. Ha módosítani szeretné azt az e-mail címet, amelyről az értesítéseket küldik, kattintson az **API-kapcsolat szerkesztése gombra.**

    ![e-mail módosítása](./media/iot-accelerators-remote-monitoring-email-actions/editemail1.png)

### <a name="owner-role-outlook-setup"></a>Tulajdonosi szerepkör Az Outlook beállítása

Ha valaki a **tulajdonosi** szerepkörben az előfizetésben üzembe helyezte a megoldásgyorsítót, az alkalmazás ellenőrizheti, hogy az e-mail műveletek megfelelően vannak-e beállítva a webes felhasználói felületen keresztül.

Mielőtt elkezdené, hozzon létre egy Outlook-fiókot, amelynek segítségével e-mail értesítéseket küldhet a megoldásgyorsítóból.

Az alábbi lépések segítenek a bejelentkezésben és a levelezési műveletek beállításában:

1. Ide kattintva bejelentkezik az Outlookba. Az Azure Portalra kerül:

   ![Bejelentkezés az Outlookba](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. Kattintson **az Engedélyezés gombra.** A rendszer kéri a bejelentkezést. A bejelentkezéshez használt fióknak az az e-mail címnek kell lennie, amelyet az alkalmazás az e-mail értesítések küldéséhez használ:

1. Kattintson a **Mentés** gombra. Térjen vissza a megoldásgyorsítóhoz, és frissítse az oldalt.

1. Ha sikeresen konfigurálta az e-mail értesítést, a következő üzenet jelenik meg:

   ![Sikeres Bejelentkezés az Outlookban](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## <a name="customize-the-email-html"></a>Az e-mail HTML-fájljának testreszabása<a name="htmledit"></a>

A remote monitoring megoldásgyorsító beépített html-sablont biztosít a műveletalapú e-mailekhez. Az e-mail sablon az e-mail művelet beállításaiból származó értékeket használ. Íme egy példa e-mail:

![példa e-mailre](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate1.png)

A következő lépések bemutatják, hogyan szerkesztheti a HTML-alapú e-mail sablont. Felvehet például további információkat, vagy egyéni képeket adhat hozzá:

1. Klónozza a Java vagy a .NET Remote Monitoring GitHub-tárházat:

1. Keresse meg az e-mail sablon helyét:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. Az üzenet testreszabásához a sablon bármely paraméterét hozzáadhatja vagy eltávolíthatja. A hívásokat szükség szerint is hozzáadhatja, eltávolíthatja vagy kicserélheti:

    Például a .NET kódban:`emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Például a Java-kódban:`this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. A sablon paraméterei a `${...}`. Paraméter törléséhez törölje a szükséges sort. Paraméter hozzáadásához adjon hozzá egy sort a beszúrandó értékkel.

1. Képek vagy egyéni szöveg hozzáadásához frissítse közvetlenül az EmailTemplate.HTML fájlt.

## <a name="throttling"></a>Throttling

A Távfigyelés megoldásgyorsító az Outlook segítségével küldete el az e-mail értesítéseket. Az Outlook korlátozza az [1 percenként 30 e-mailre](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits)küldött e-mailek számát. Az e-maileket fogadó e-maileket fogadó e-maileket is szabályozhatja a percenként fogadott e-mailek száma. Ellenőrizze a konkrét levelezőprogramot a korlátozásokról. Amikor e-mail értesítést állít be egy szabályhoz, a szabálynak legalább egy perc alatt kell kiszámítania az átlagértékeket, és nem kell azonnali értékeket használnia:

![Átlagszámítás](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>További lépések

Ez az útmutató bemutatja, hogyan adhat hozzá egy e-mail műveletet egy új vagy meglévő szabályhoz egy távoli figyelési megoldáson belül. Az útmutató azt is megmutatta, és hogyan kell szerkeszteni a HTML, amely meghatározza az üzenet formátumát.

A javasolt következő lépés a riasztások használatának és az [eszközproblémák megoldásának megismerése.](iot-accelerators-remote-monitoring-maintain.md)
