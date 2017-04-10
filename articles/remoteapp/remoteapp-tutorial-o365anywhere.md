---
title: "Azonos Office 365-élmény bármely Azure RemoteAppot használó eszközön | Microsoft Docs"
description: "Ismerje meg, hogyan oszthatja meg bármelyik Office 365-alkalmazást a felhasználóival az Azure RemoteApp segítségével."
services: remoteapp
documentationcenter: 
author: guscatalano
manager: mbaldwin
editor: 
ms.assetid: 0c971ce9-7d45-4cfb-9737-15b6706047e8
ms.service: remoteapp
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 11/23/2016
ms.author: guscatal;elizapo
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 584c781c97097cda3c1455ade05cba8659f11073
ms.lasthandoff: 03/31/2017


---
# <a name="get-the-same-office-365-experience-on-any-device-with-azure-remoteapp"></a>Azonos Office 365-élmény bármely Azure RemoteApp szolgáltatást használó eszközön
> [!IMPORTANT]
> Az Azure RemoteApp 2017. augusztus 31-ét követően megszűnik. A részletekért olvassa el a [bejelentést](https://go.microsoft.com/fwlink/?linkid=821148).
> 
> 

Ez a cikk ismerteti, hogy hogyan helyezheti üzembe az Office 365-alkalmazásokat a vállat bármelyik eszközén. A felhasználók ugyanolyan képességeket kaphatnak és ugyanolyan felhasználói élményben lehet részük Android-, Apple- és Windows-eszközökön.

Ezt az Azure RemoteApp használatával érheti el az Office 365 méretezhető virtuális gépeken való üzemeltetésével az Azure-ban, amelyekhez a felhasználók csatlakozhatnak. A virtuális gépek ezen készletét „felhőalapú gyűjteménynek” nevezik.

## <a name="create-a-cloud-collection"></a>Felhőalapú gyűjtemény létrehozása
Miután létrehozott egy Azure-fiókot, először navigáljon a **RemoteAppra** a bal oldalon található hivatkozásra kattintva.
![Az Azure RemoteApp megjelenítése az Azure Portalon](./media/remoteapp-tutorial-o365anywhere/1-menu.png)

Ezután alul kattintson a **new** (új) , majd a gyűjtemény „gyors létrehozása” elemre. Adjon meg egy nevet, a régiót, az előfizetését, a díjcsomagot és az általunk biztosított „Office Professional 2013” rendszerképet.
![Párbeszédpanel létrehozása](./media/remoteapp-tutorial-o365anywhere/2-quickcreate.png)

Miután kitölti az űrlapot, elindul a gyűjtemény létrehozásának folyamata. Ez akár egy órát is igénybe vehet.

![Várakozás](./media/remoteapp-tutorial-o365anywhere/3-waiting.png)

Miután a folyamat befejeződik, a következőhöz hasonlóan fog kinézni. Ha a **Publishing** (Közzététel) elemre kattint, láthatja, hogy a legtöbb Office-alkalmazás már közzé van téve.
![Létrehozott gyűjtemény](./media/remoteapp-tutorial-o365anywhere/4-done.png)

![Közzétett alkalmazások](./media/remoteapp-tutorial-o365anywhere/5-publish.png)

Ezen a ponton a **User Access** (Felhasználói hozzáférés) elemre kattintva felvehet több felhasználót, akik hozzáférhetnek a gyűjteményhez.
![Felhasználói hozzáférés konfigurálása](./media/remoteapp-tutorial-o365anywhere/6-user.png)

Most próbáljon meg csatlakozni az Office 365-höz.

## <a name="connect-to-office-365"></a>Csatlakozás az Office 365-höz
Nyissa meg a [https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/) webhelyet, görgessen a lap aljára, és a **Download clients** (Ügyfelek letöltése) elemre kattintva telepítse az Azure RemoteApp-ügyfelet az épp használt eszközre. Az alábbi képernyőképek a Windowsra vonatkoznak.

Miután elindul az alkalmazás, a rendszer felkéri, hogy jelentkezzen be Microsoft-fiókjával (korábban „Live ID”). Egyelőre használja ugyanazt, mint amit az Azure-fiókhoz használt. Amikor bejelentkezik, egy értesítés jelenik meg az új meghívókról, kattintson rá, ekkor az alábbihoz hasonló listának kell megjelennie. Kattintson arra a meghívóra, amelyben az Azure-fiók tulajdonosának e-mail-címe szerepel.

![Új meghívó](./media/remoteapp-tutorial-o365anywhere/7-araclient.png)

Így néz ki, ha vannak új meghívók.

![Egy alkalmazás elfogadása](./media/remoteapp-tutorial-o365anywhere/8-invitation.png)

Miután elfogadja a meghívót, az összes Office-alkalmazás látható az Azure RemoteApp-ügyfélen.

![Alkalmazások listája](./media/remoteapp-tutorial-o365anywhere/9-work.png)

Ha bármelyik alkalmazásra kattint, az alkalmazás elindul az Azure virtuális gépen, és készen áll a használatra. Jó munkát!

![indítás](./media/remoteapp-tutorial-o365anywhere/10-arastart.png)

![powerpoint](./media/remoteapp-tutorial-o365anywhere/11-pp.png)


