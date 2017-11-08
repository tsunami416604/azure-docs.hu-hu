---
title: "A teljesítményadatok vagy ütemezés alapján automatikus skálázás Azure-erőforrások |} Microsoft Docs"
description: "Az automatikus skálázási beállítás az app service-csomag metrikaadatokat és ütemezés létrehozása"
author: anirudhcavale
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: ancav
ms.custom: mvc
ms.openlocfilehash: 3a85e288fa6f7d6c7138b7fea8319bd8dee01c2c
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="create-an-autoscale-setting-for--azure-resources-based-on-performance-data-or-a-schedule"></a>Egy Azure-erőforrások teljesítményadatokat vagy egy ütemezés alapján automatikus skálázási beállítás létrehozása

Automatikus skálázás beállításai lehetővé teszik, hogy az előre definiált feltételekre alapozva szolgáltatás példányának telepítése és törlése. Ezek a beállítások a portálon keresztül is létrehozható. Ez a módszer létrehozásához és konfigurálásához az automatikus skálázási beállítás böngészőalapú felhasználói felületet biztosít. 

Ebben az oktatóanyagban a tartalma 
> [!div class="checklist"]
> * A Web App és az App Service-csomag létrehozása
> * Méretezés és a bővített kapacitású szabályainak kap a webes alkalmazás kérelmek száma alapján automatikus skálázás konfigurálása
> * A kibővített művelet indíthat, és tekintse meg a példányok száma növelése
> * Aktiválhatja a skálázási művelet, és tekintse meg a példányok száma csökkentése
> * Az erőforrások törlése

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-a-web-app-and-app-service-plan"></a>A Web App és az App Service-csomag létrehozása
Kattintson a **új** lehetőséget a bal oldali navigációs panelről

Keresse meg és jelölje ki a *webalkalmazás* elemet, majd kattintson **létrehozása**

Válassza ki az alkalmazás neve például *MyTestScaleWebApp*. Hozzon létre egy új erőforráscsoportot * contoso.com "és az erőforráscsoport a elhelyezése.

Néhány percen belül az erőforrások kell építhető ki. Ebben az oktatóanyagban további része a Web App, és megfelelő App Service-csomag használata.

    ![Create a new app service in the portal](./media/monitor-tutorial-autoscale-performance-schedule/Web-App-Create.png)

## <a name="navigate-to-autoscale-settings"></a>Navigáljon az automatikus skálázási beállításokat
1. A bal oldali navigációs ablakból válassza ki a **figyelő** lehetőséget. Ha az oldal betöltése válassza ki a **automatikus skálázás** fülre.
2. Az itt felsorolt automatikus skálázás támogató az előfizetéshez tartozó erőforrások listáját. Az App Service-csomag az oktatóanyag korábbi részében létrehozott azonosításához, és kattintson rá.

    ![Navigáljon az automatikus skálázási beállításokat](./media/monitor-tutorial-autoscale-performance-schedule/monitor-blade-autoscale.png)

3. Az automatikus skálázási beállítás, kattintson a **automatikus skálázás engedélyezéséhez** gomb

A következő néhány lépést súgó kitöltötte a következő képen a következőképpen néznek automatikus skálázás képernyő:

   ![Automatikus skálázási beállítás mentése](./media/monitor-tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

 ## <a name="configure-default-profile"></a>Alapértelmezett profil konfigurálása
1. Adjon meg egy **neve** az automatikus skálázási beállítás
2. Az alapértelmezett profil a ellenőrizze, hogy a **méretezési módban** "Egy adott példányszám méret" értékre van állítva
3. A példányok száma beállítása **1**. Ezzel a beállítással biztosíthatja, hogy ha nincs más profil aktív, vagy az érvényben, az alapértelmezett profil visszatér a példányok száma 1.

  ![Navigáljon az automatikus skálázási beállításokat](./media/monitor-tutorial-autoscale-performance-schedule/autoscale-setting-profile.png)


## <a name="create-recurrance-profile"></a>Recurrance profil létrehozása

1. Kattintson a **méretezési feltétel hozzáadása** alatt az alapértelmezett profil

2. Szerkessze a **neve** kell lennie, hétfőtől péntekig profilhoz a profil

3. Győződjön meg arról a **méretezési módban** "Metrika alapuló méretezési" értékre van állítva

4. A **korlátok példány** állítsa be a **minimális** "1", mint a **maximális** '2' és a **alapértelmezett** '1'. Ez a beállítás biztosítja, hogy ez a profil does nem automatikus skálázás 1-nél kevesebb példányt, vagy 2-nél több példány service-csomag. A profil nem rendelkezik elegendő adatot a döntést, ha azt használja a példányok alapértelmezett száma (ebben az esetben: 1).

5. A **ütemezés**, válassza a "adott napokra Repeat"

6. Állítsa be a profilhoz, amellyel hétfőtől péntekig, ismételje meg a 09:00 csendes-óceáni TÉLI csendes 18:00-óceáni TÉLI számára. Ez a beállítás biztosítja, hogy ezt a profilt csak aktív és alkalmazandó Reggel 9 a 18: 00, hétfőtől péntekig. Minden egyéb esetben a "Default" profil pedig az automatikus skálázási beállítás által használt profil.

## <a name="create-a-scale-out-rule"></a>Kibővített szabály létrehozása

1. Az a hétfőtől péntekig profilhoz

2. Kattintson a **szabály hozzáadása** hivatkozás

3. Állítsa be a **metrika forrás** kell egyéb erőforrásokat. Állítsa be a **erőforrástípus** "Alkalmazásszolgáltatások", és a **erőforrás** , ebben az oktatóanyagban korábban létrehozott webalkalmazás.

4. Állítsa be a **összesítési idő** , "Összes", a **metrika neve** "Kérelmek", és a **idő felbontása statisztika** "összegként

5. Állítsa be a **operátor** "Nagyobb, mint", mint a **küszöbérték** "10" és a **időtartam** "5" perc.

6. Válassza ki a **művelet** "Növekedése számolva", mint a **száma példány** "1", és a **lassú le** "5" perc

7. Kattintson a **Hozzáadás** gomb

Ez a szabály biztosítja, hogy ha a webalkalmazás több mint 10 kéréseket fogad 5 percen belül, vagy kevesebb, mint egy további példányt hozzáadódik az App Service-csomag terhelés kezelésére.

   ![Kibővített szabály létrehozása](./media/monitor-tutorial-autoscale-performance-schedule/Scale-Out-Rule.png)

## <a name="create-a-scale-in-rule"></a>A skálázási szabály létrehozása
Azt javasoljuk, hogy mindig a méretezési a szabályt egy kibővített szabály kísérő. Ha mindkettő biztosítja, hogy az erőforrások nem több mint törlődnek. Azt jelenti, hogy kiépítéshez képest, hogy fut, mint a jelenlegi terhelés kezeléséhez szükséges további példányokat. 

1. Az a hétfőtől péntekig profilhoz

2. Kattintson a **szabály hozzáadása** hivatkozás

3. Állítsa be a **metrika forrás** kell egyéb erőforrásokat. Állítsa be a **erőforrástípus** "Alkalmazásszolgáltatások", és a **erőforrás** , ebben az oktatóanyagban korábban létrehozott webalkalmazás.

4. Állítsa be a **összesítési idő** , "Összes", a **metrika neve** "Kérelmek", és a **idő felbontása statisztika** , az "Average"

5. Állítsa be a **operátor** "Kisebb, mint", mint a **küszöbérték** "5", és a **időtartam** "5" perc.

6. Válassza ki a **művelet** "Csökkentése számolva", mint a **száma példány** "1", és a **lassú le** "5" perc

7. Kattintson a **Hozzáadás** gomb

    ![A skálázási szabály létrehozása](./media/monitor-tutorial-autoscale-performance-schedule/Scale-In-Rule.png)

8. **Mentés** automatikus skálázási beállítás

    ![Automatikus skálázási beállítás mentése](./media/monitor-tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="trigger-scale-out-action"></a>Eseményindító kibővített művelet
Az automatikus skálázási beállítás most hozott létre a kibővített feltétel indításához a Web App kell rendelkeznie a 10-nél több kérelmek 5 percen belül.

1. Nyisson meg egy böngészőablakot, és keresse meg azt a webalkalmazást, ebben az oktatóanyagban korábban létrehozott. Találhatja meg az URL-címet a webalkalmazás az Azure portálon lépjen a webes alkalmazás-erőforrást, és kattintson a a **Tallózás** gomb a "Overview" lapon.

2. Gyors egymásutánban az oldal újratöltéséhez 10 szintnél mélyebben

3. A bal oldali navigációs ablakból válassza ki a **figyelő** lehetőséget. Ha az oldal betöltése válassza ki a **automatikus skálázás** fülre.

4. A listában jelölje ki az App Service-csomag az oktatóanyag során használt

5. Az automatikus skálázási beállítás, kattintson a **futtatási előzményei** lap

6. Megjelenik egy adott idő alatt a példányok száma, az App Service-csomag tükröző diagram

7. Néhány perc múlva a példányok száma kell értéke 1, 2.

8. A a diagram tekintse meg a tevékenység naplóbejegyzéseket minden méretezési művelet által az automatikus skálázási beállítás.

## <a name="trigger-scale-in-action"></a>Eseményindító skálázási művelet
A skála a feltétel az automatikus skálázási beállítás eseményindítók, ha nincs 5-nél kevesebb azt a webalkalmazást egy meghatározott időtartamra vonatkozóan 10 perc. 

1. A webalkalmazáshoz küldési kérelmek nem biztosítására

2. Az Azure-portál betöltése

3. A bal oldali navigációs ablakból válassza ki a **figyelő** lehetőséget. Ha az oldal betöltése válassza ki a **automatikus skálázás** fülre.

4. A listában jelölje ki az App Service-csomag az oktatóanyag során használt

5. Az automatikus skálázási beállítás, kattintson a **futtatási előzményei** lap

6. Adott idő alatt a példányok száma, az App Service-csomag tükröző diagramot láthatja.

7. Néhány perc múlva a példányok száma kell elvetni 2, 1. A folyamat legalább 100 percet vesz igénybe.  

8. A a diagram vannak a megfelelő set tevékenység minden egyes skálázási művelet által az automatikus skálázási beállításhoz tartozó naplóbejegyzések

    ![A skála műveletek megtekintése](./media/monitor-tutorial-autoscale-performance-schedule/Scale-In-Chart.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

1. Az Azure-portálon a bal oldali menüben kattintson a **összes erőforrás** , és válassza ki az ebben az oktatóanyagban a létrehozott webalkalmazás.

2. Az erőforrás lapján kattintson a **törlése**, írja be a törlésének megerősítése **Igen** a szövegmezőbe, majd **törlése**.

3. Ezután válassza ki az App Service-csomag erőforrást, majd **törlése**

4. Írja be a törlésének megerősítése **Igen** a szövegmezőbe, majd **törlése**.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban meg  
> [!div class="checklist"]
> * A Web App és az App Service-csomag létrehozása
> * A skálázási szabályok konfigurált automatikus skálázási, és a kérelmek száma alapján kibővítési kapott webalkalmazás
> * A kibővített művelet indított és figyelt növelése példányainak száma
> * A skálázási művelet indított és figyelt csökkentése példányainak száma
> * Az erőforrások karbantartása


Automatikus skálázás beállításaival kapcsolatos további tudnivalókért továbbra is a a [automatikus skálázás áttekintése](monitoring-overview-autoscale.md).

> [!div class="nextstepaction"]
> [A figyelési adatok archiválása](monitor-tutorial-archive-monitoring-data.md)
