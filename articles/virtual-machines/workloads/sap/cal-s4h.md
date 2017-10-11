---
title: "SAP S/4HANA vagy egy Azure virtuális gépen BW/4HANA telepítése |} Microsoft Docs"
description: "SAP S/4HANA vagy BW/4HANA egy Azure virtuális Gépen lévő telepítése"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: 4788fa14a6c49d39b5a3096a69b6738f4a5d8cca
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>SAP S/4HANA vagy BW/4HANA Azure telepítéséhez
Ez a cikk ismerteti a felhő készülék teszteléshez (SAP CAL) 3.0 telepítését az S/4HANA az Azure-on. Más SAP HANA-alapú megoldások, például a BW/4HANA, központi telepítéséhez hajtsa végre a lépéseket.

> [!NOTE]
Az SAP-CAL kapcsolatos további információkért látogasson el a [felhő készülék teszteléshez](https://cal.sap.com/) webhelyet. SAP is rendelkezik egy blog a [SAP felhő készülék könyvtár 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).

> [!NOTE]
2017. május 29. frissítésétől mellett a kevésbé előnyben részesített klasszikus üzembe helyezési modellel, az Azure Resource Manager telepítési modell segítségével telepítheti az SAP-CAL. Azt javasoljuk, hogy az új központi telepítési Resource Manager modellt használja, és figyelmen kívül hagyhatja a klasszikus üzembe helyezési modellben.

## <a name="step-by-step-process-to-deploy-the-solution"></a>A megoldás részletes folyamata

A képernyőfelvételek a következő folyamat bemutatja, hogyan telepítse az S/4HANA Azure az SAP-CAL segítségével. A folyamat más megoldások, például a BW/4HANA ugyanúgy működik.

A **megoldások** lap néhány mutatja be a rendelkezésre álló SAP HANA-CAL-alapú megoldások Azure-on. **SAP S/4HANA 1610 FPS01, Fully-Activated készülék** a középső sor:

![SAP CAL megoldások](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Az SAP-CAL, hozzon létre egy fiókot
1. Jelentkezzen be az SAP-CAL először, használja az SAP-felhasználó vagy más SAP regisztrált felhasználó. Majd adja meg egy SAP naptár fiókja Azure készülékek telepítendő az SAP-CAL által használt. A fiók-definícióban kell:

    a. (Erőforrás-kezelő vagy klasszikus) Azure telepítési modell kiválasztása.

    b. Adja meg az Azure-előfizetéshez. Egy SAP naptár fiókja csak egyetlen előfizetéssel is hozzárendelhető. Ha egynél több előfizetéssel, hozzon létre egy másik SAP naptár fiókja szeretné.

    c. Az SAP-CAL engedélyt üzembe helyezés az Azure-előfizetéshez.

    > [!NOTE]
    A következő lépések bemutatják, hogyan a Resource Manager üzembe helyezések SAP CAL-fiók létrehozása. Ha már rendelkezik egy SAP naptár fiókja, amely csatolva van a klasszikus üzembe helyezési modellel, *kell* lépések SAP CAL új fiók létrehozása. Az új SAP CAL fiókot kell üzembe helyezni a Resource Manager modellt.

2. Hozzon létre egy új SAP naptár fiókja. A **fiókok** lapon látható három lehetősége az Azure-bA: 

    a. **A Microsoft Azure (klasszikus)** a klasszikus üzembe helyezési modellel, és már nem elsődleges.

    b. **A Microsoft Azure** az új erőforrás-kezelő telepítési modell.

    c. **Windows Azure 21Vianet által működtetett** szabályozó beállítást a kínai, amely a klasszikus üzembe helyezési modellt használ.

    A Resource Manager modellt a telepítéséhez válassza ki a **Microsoft Azure**.

    ![SAP CAL fiókadatok](./media/cal-s4h/s4h-pic-2a.png)

3. Adja meg az Azure **előfizetés-azonosító** , amely az Azure portálon található.

   ![SAP CAL fiókok](./media/cal-s4h/s4h-pic3c.png)

4. Az SAP-CAL való üzembe helyezés az Azure-előfizetés az Ön által definiált engedélyezésére, kattintson a **engedélyezés**. A következő lap a böngészőlapon jelenik meg:

   ![Az Internet Explorer cloud services – bejelentkezési](./media/cal-s4h/s4h-pic4c.png)

5. Ha egynél több felhasználó szerepel a listán, válassza ki a Microsoft-fiókkal, amely csatolva van a kijelölt Azure-előfizetés coadministrator kell. A következő lap a böngészőlapon jelenik meg:

   ![Az Internet Explorer felhőalapú szolgáltatások megerősítése](./media/cal-s4h/s4h-pic5a.png)

6. Kattintson a **elfogadása**. Ha az engedélyezési sikeres, az SAP-CAL fiók definíció újra jeleníti meg. Rövid idő múlva üzenet jelzi, hogy az engedélyezési folyamat sikeres volt-e.

7. Az újonnan létrehozott SAP naptár fiókja hozzárendelése a felhasználóhoz, adja meg a **Felhasználóazonosító** jobbra, majd a szövegmezőben **Hozzáadás**.

   ![Fiók és felhasználói társítása](./media/cal-s4h/s4h-pic8a.png)

8. A fiók társítása a felhasználót, hogy jelentkezzen be az SAP-CAL segítségével, kattintson a **felülvizsgálati**. 
 
9. A felhasználó és az újonnan létrehozott SAP naptár fiókja közötti társítás létrehozásához kattintson a **létrehozása**.

   ![Felhasználói és SAP CAL fiók társítása](./media/cal-s4h/s4h-pic9b.png)

Sikeresen létrehozott egy SAP naptár fiókja, amely képes:

- A Resource Manager telepítési modellt használja.
- SAP rendszerek üzembe helyezés az Azure-előfizetéshez.

Most már megkezdheti, az S/4HANA üzembe helyezés felhasználói előfizetését az Azure-ban.

> [!NOTE]
A folytatás előtt megállapításához, hogy rendelkezik-e az Azure core kvóták Azure H sorozatú virtuális gépekhez. Az SAP-CAL időpontjában, Azure virtuális gépek H-sorozat használja a központi telepítésére az SAP HANA-alapú megoldások némelyike. Az Azure-előfizetéshez esetleg nincs bármely H-sorozat core kvóták a H-adatsorozathoz. Ha igen, szükség lehet egy legalább 16 H-sorozat magszámra vonatkozó kvóta eléréséhez Azure ügyfélszolgálatához.

> [!NOTE]
Amikor telepít egy megoldást az SAP-CAL az Azure-on, előfordulhat, hogy csak egy Azure-régió, választhat. Üzembe helyezés az SAP-CAL által javasolt más Azure-régiók, a naptár-előfizetés vásárlása SAP kell. Is szükség lehet a naptár fiók engedélyezve van az Azure-régiók eredetileg javasolt eltérő továbbítására SAP rendelkező.

### <a name="deploy-a-solution"></a>A megoldás üzembe helyezéséhez

Helyezzünk üzembe egy megoldást a **megoldások** az SAP-CAL oldalán. Az SAP-CAL van két feladatütemezések központi telepítéséhez:

- Egy alapszintű sorozatot, amely egy lap segítségével határozza meg a rendszer
- Egy speciális részeként, amely lehetővé teszi az egyes lehetőségek a Virtuálisgép-méretek 

Bemutatjuk a központi telepítési itt alapvető elérési útja.

1. Az a **fiókadatok** lapon kell:

    a. Válassza ki a SAP CAL fiókot. (Olyan fiókot használjon, amely a Resource Manager üzembe helyezési modellel telepítését társítva van.)

    b. Adjon meg egy példány **neve**.

    c. Válassza ki az Azure **régió**. Az SAP-CAL javasol egy régiót. Ha egy másik Azure-régióban van szüksége, és egy SAP naptár-előfizetés nem rendelkezik, egy SAP CAL előfizetés sorrendben szeretné.

    d. Adjon meg egy fő **jelszó** nyolc vagy kilenc karaktereket a megoldáshoz. A jelszót a rendszergazdák a különböző összetevők használja.

   ![SAP CAL Basic mód: Példány létrehozása](./media/cal-s4h/s4h-pic10a.png)

2. Kattintson a **létrehozása**, és kattintson a párbeszédpanelen megjelenik, **OK**.

   ![SAP CAL támogatott Virtuálisgép-méretek](./media/cal-s4h/s4h-pic10b.png)

3. Az a **titkos kulcs** párbeszédpanel, kattintson a **tárolására** a titkos kulcs tárolása az SAP-CAL. Jelszavas védelem a titkos kulcs használatához kattintson **letöltése**. 

   ![SAP CAL titkos kulcs](./media/cal-s4h/s4h-pic10c.png)

4. Olvassa el az SAP-CAL **figyelmeztetés** üzenet, és kattintson a **OK**.

   ![SAP CAL figyelmeztetés](./media/cal-s4h/s4h-pic10d.png)

    Most már a központi telepítés akkor történik meg. Némi várakozás után attól függően, hogy méretét és összetettségét (az SAP-CAL biztosít becsült érték) megoldás állapota jelenik meg az aktív és használatra kész.

5. A virtuális gépek egy erőforráscsoporthoz tartozik, a más társított erőforrásokkal rendelkező gyűjtött megkereséséhez nyissa meg az Azure-portálon: 

   ![SAP CAL objektumok az új portál telepítése](./media/cal-s4h/sapcaldeplyment_portalview.png)

6. Az SAP-CAL-portál, az állapot jelenik meg **aktív**. A megoldás való kapcsolódáshoz kattintson **Connect**. Különböző beállítások a különböző összetevők csatlakozni ehhez a megoldáshoz belül vannak telepítve.

   ![SAP CAL-példányok](./media/cal-s4h/active_solution.png)

7. Kattintson a használata előtt a egyikére kapcsolódni a telepített rendszereken, **Getting Started Guide**. 

   ![Kapcsolódjon ahhoz a példányhoz](./media/cal-s4h/connect_to_solution.png)

    A dokumentáció a felhasználók számára a csatlakozási módszer nevet. A jelszavak azoknak a felhasználóknak a fő jelszót, amelyet megadott, a telepítési folyamat elején értékre van beállítva. A dokumentáció több működési másoknak szerepel a listában a jelszavukat, amelyek segítségével jelentkezzen be a telepített rendszer. 

    Például ha használja a SAP grafikus felhasználói felület, amely előre telepítve van a Windows távoli asztali gépen, a S/4 rendszer nézhet ki:

   ![SM50 előtelepített SAP grafikus felhasználói felületen](./media/cal-s4h/gui_sm50.png)

    Vagy ha a DBACockpit használja, a példány nézhet ki:

   ![SM50 DBACockpit SAP grafikus felhasználói felületen](./media/cal-s4h/dbacockpit.png)

Néhány órán belül egy megfelelő SAP S/4 készülék a rendszer az Azure-ban.

Ha egy SAP naptár-előfizetés vásárolta, SAP Azure teljes mértékben támogatja az SAP-CAL környezeteit. A támogatási várólista BC-VCM-CAL.







