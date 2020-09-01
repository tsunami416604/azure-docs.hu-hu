---
title: Frissítés telepítése Azure Stack Edge GPU-eszközön | Microsoft Docs
description: Útmutató a frissítések alkalmazásához a Azure Portal és helyi webes felhasználói felülettel Azure Stack Edge GPU-eszközhöz
services: databox
author: alkohli
ms.service: databox
ms.topic: article
ms.date: 06/12/2020
ms.author: alkohli
ms.openlocfilehash: 0e973e41493b8e84f31a82d5379a01a0e851a48d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084118"
---
# <a name="update-your-azure-stack-edge-with-gpu"></a>Azure Stack Edge frissítése a GPU-val 

Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek a frissítés telepítéséhez a Azure Stack Edge-ben a GPU-n keresztül a helyi webes felületen és a Azure Portal keresztül. A szoftverfrissítések vagy gyorsjavítások alkalmazásával megőrizheti Azure Stack peremhálózati eszközének naprakészen tartását. 

> [!IMPORTANT]
> - Az **2008** -es frissítés megfelel az eszközön **2.1.1328.1904** szoftver verziójának. A frissítéssel kapcsolatos további információkért nyissa meg a [kibocsátási megjegyzéseket](azure-stack-edge-gpu-2008-release-notes.md).
>
> - Ne feledje, hogy frissítés vagy gyorsjavítás telepítése újraindítja az eszközt. A frissítés végrehajtásához egymás után két frissítést kell alkalmaznia. Először alkalmazza az eszköz szoftverfrissítéseket, majd Kubernetes a frissítéseket. Mivel az Azure Stack Edge egyetlen csomópontos eszköz, a folyamatban lévő I/O-műveletek megszakadnak, és az eszközön az eszköz szoftverfrissítés-frissítése akár 30 percet is igénybe vehet.

Az eszközön a frissítések telepítéséhez először konfigurálnia kell a frissítési kiszolgáló helyét. A frissítési kiszolgáló konfigurálása után a frissítéseket a Azure Portal felhasználói felületén vagy a helyi webes felületen keresztül is alkalmazhatja.

Ezeket a lépéseket az alábbi szakaszokban ismertetjük.

## <a name="configure-update-server"></a>Frissítési kiszolgáló konfigurálása

1. A helyi webes kezelőfelületen lépjen a **konfigurációs**  >  **frissítési kiszolgáló**elemre. 
   
    ![Frissítések konfigurálása](./media/azure-stack-edge-gpu-install-update/configure-update-server-1.png)

2. A **frissítési kiszolgáló típusának kiválasztása**területen a legördülő listából válassza ki a Microsoft Update kiszolgáló (alapértelmezett) vagy a Windows Server Update Services lehetőséget.  
   
    Ha a Windows Server Update Services frissítését, akkor a kiszolgáló URI-JÁT kell megadnia. Az URI-kiszolgáló a kiszolgálóhoz csatlakozó összes eszközön telepíti a frissítéseket.

    ![Frissítések konfigurálása](./media/azure-stack-edge-gpu-install-update/configure-update-server-2.png)
    
    A WSUS-kiszolgáló a frissítések felügyeletére és terjesztésére szolgál a felügyeleti konzolon keresztül. A WSUS-kiszolgáló a szervezeten belüli többi WSUS-kiszolgáló frissítési forrása is lehet. A frissítés forrásaként szolgáló WSUS-kiszolgálót felsőbb rétegbeli kiszolgálónak nevezik. A WSUS implementációjában a hálózaton legalább egy WSUS-kiszolgálónak képesnek kell lennie csatlakozni a Microsoft Updatehoz az elérhető frissítési információk beszerzéséhez. Rendszergazdaként a hálózati biztonság és konfiguráció alapján határozhatja meg, hogy hány más WSUS-kiszolgáló csatlakozik közvetlenül a Microsoft Updatehoz.
    
    További információ: [Windows Server Update Services (WSUS)](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus)

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Javasoljuk, hogy telepítse a frissítéseket a Azure Portalon keresztül. Az eszköz naponta egyszer automatikusan megkeresi a frissítéseket. Ha a frissítések elérhetők, megjelenik egy értesítés a portálon. Ezután letöltheti és telepítheti a frissítéseket. 

> [!NOTE]
> Győződjön meg arról, hogy az eszköz kifogástalan állapotban van, és az állapota **online** állapotba kerül, mielőtt folytatná a frissítések telepítését.

1. Ha a frissítések elérhetők az eszközhöz, egy értesítés jelenik meg. Válassza ki az értesítést, vagy a felső menüsorban **frissítse az eszközt**. Ez lehetővé teszi az eszköz szoftverfrissítések alkalmazását.

    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-1.png)

2. A **Device Updates (eszközök frissítése** ) panelen győződjön meg arról, hogy áttekintette a kibocsátási megjegyzések új szolgáltatásaihoz társított licencfeltételeket.

    Választhat, hogy **letölti és telepíti** a frissítéseket, vagy csak **letölti** a frissítéseket. Ezután később is telepítheti ezeket a frissítéseket.

    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-2a.png)    

    Ha le szeretné tölteni és telepíteni kívánja a frissítéseket, ellenőrizze, hogy a frissítések telepítése automatikusan megtörtént-e a letöltés befejeződése után.

    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-2b.png)

3. A frissítések letöltése megkezdődik. Ekkor megjelenik egy értesítés arról, hogy a letöltés folyamatban van.

    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-3.png)

    Egy értesítési szalagcím is megjelenik a Azure Portalban. Ez a letöltési folyamatra utal. 

    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-4.png)

    Kiválaszthatja ezt az értesítést, vagy az **eszköz frissítése** lehetőség kiválasztásával megtekintheti a frissítés részletes állapotát.

    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-5.png)   


4. A letöltés befejezését követően az értesítési szalagcím frissíti a befejezést. Ha úgy dönt, hogy letölti és telepíti a frissítéseket, a telepítés automatikusan elindul.

    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-6.png)

    Ha úgy döntött, hogy csak a frissítések letöltését választotta, válassza ki az értesítést az **eszköz frissítései** panel megnyitásához. Válassza a **Telepítés** lehetőséget.
  
    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-7.png)

5. Ekkor megjelenik egy értesítés arról, hogy a telepítés folyamatban van.

    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-8.png)

    A portál egy tájékoztató riasztást is megjelenít, amely jelzi, hogy a telepítés folyamatban van. Az eszköz offline állapotba kerül, és karbantartási üzemmódban van.
    
    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-9.png)

6. Mivel ez egy 1 csomópontos eszköz, az eszköz a frissítések telepítése után újra fog indulni. Az újraindítás során a kritikus riasztás azt jelzi, hogy az eszköz szívverése megszakadt.

    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-10.png)

    Válassza ki a riasztást a megfelelő eszköz eseményének megtekintéséhez.
    
    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-11.png)


7. Az újraindítás után az eszköz ismét a karbantartási módba kerül, és megjelenik egy tájékoztató riasztás, amely azt jelzi, hogy.

    Ha a felső menüsávban az **eszköz frissítése** lehetőséget választja, láthatja a frissítések előrehaladását.   

8. A frissítések telepítése után az eszköz állapota **online** állapotra frissül. 

    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-14.png)

    A felső menüsávban válassza az **eszközök frissítései**lehetőséget. Ellenőrizze, hogy a frissítés telepítése sikeres volt-e, és hogy az eszköz szoftverének verziója megfelel-e.

    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-15.png)

9. Ekkor megjelenik egy értesítés arról, hogy a frissítések elérhetők. Ezek a Kubernetes-frissítések. Válassza ki az értesítést, vagy válassza a felső parancssáv **eszköz frissítése** elemét.

    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-16.png)

10. Töltse le a Kubernetes frissítéseit. Láthatja, hogy a csomag mérete eltérő az előző frissítési csomaghoz képest.

    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-17.png)

    A telepítés folyamata megegyezik az eszköz frissítéseivel. Először letölti a frissítéseket.

    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-18.png)    
    
11. A frissítések letöltése után telepítheti a frissítéseket. 

    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-19.png)

    A frissítések telepítése után az eszköz karbantartási módba kerül. Az eszköz nem indítja újra a Kubernetes frissítéseit. 

    Ha a Kubernetes frissítése sikeresen megtörtént, a szalagcím-értesítés eltűnik, mert nincs szükség további frissítésekre. Az eszközön már az eszköz szoftver-és Kubernetes legújabb verziója érhető el.

    ![Szoftverfrissítés utáni szoftververzió](./media/azure-stack-edge-gpu-install-update/portal-update-20.png)


## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

A helyi webes felhasználói felület használata két lépésből áll:

* A frissítés vagy a gyorsjavítás letöltése
* A frissítés vagy a gyorsjavítás telepítése

Ezeket a lépéseket a következő szakaszokban részletesen ismertetjük.


### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

A frissítés letöltéséhez hajtsa végre a következő lépéseket. A frissítést a Microsoft által biztosított helyről vagy a Microsoft Update katalógusból töltheti le.


Az alábbi lépések végrehajtásával töltse le a frissítést a Microsoft Update-katalógusból.

1. Indítsa el a böngészőt, és navigáljon a gombra [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

    ![Keresés a katalógusban](./media/azure-stack-edge-gpu-install-update/download-update-1.png)

2. A Microsoft Update-katalógus keresőmező mezőjébe írja be a letölteni kívánt frissítés gyorsjavításának vagy használati feltételeinek a tudásbázisát (KB). Adja meg például az **Azure stack Edge**kifejezést, majd kattintson a **Keresés**gombra.
   
    A frissítési lista **Azure stack Edge 2006**-ként jelenik meg.
   
    ![Keresés a katalógusban](./media/azure-stack-edge-gpu-install-update/download-update-2b.png)

4. Válassza a **Letöltés** lehetőséget. Két fájl tölthető le *SoftwareUpdatePackage.exe* és *Kubernetes_Package.exe* utótagokkal, amelyek megfelelnek az eszköz szoftverfrissítések és a Kubernetes frissítéseinek. Töltse le a fájlokat a helyi rendszer egyik mappájába. A mappát átmásolhatja egy olyan hálózati megosztásra is, amely elérhető az eszközről.

### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

A frissítés vagy a gyorsjavítás telepítése előtt győződjön meg az alábbiakról:

 - A frissítés vagy a gyorsjavítás helyileg, a gazdagépen vagy egy hálózati megosztáson keresztül érhető el.
 - Az eszköz állapota Kifogástalan, ahogy a helyi webes felhasználói felület **Áttekintés** lapján látható.

   ![eszköz frissítése](./media/azure-stack-edge-gpu-install-update/local-ui-update-1.png) 

Az eljárás végrehajtása körülbelül 20 percet vesz igénybe. A frissítés vagy a gyorsjavítás telepítéséhez hajtsa végre a következő lépéseket.

1. A helyi webes kezelőfelületen lépjen a **karbantartási**  >  **szoftverfrissítés**elemre. Jegyezze fel a futtatott szoftver verzióját. 
   
   ![eszköz frissítése](./media/azure-stack-edge-gpu-install-update/local-ui-update-2.png)

2. Adja meg a frissítési fájl elérési útját. Ha hálózati megosztásra helyezi, tallózással is megkeresheti a frissítési telepítési fájlt. Válassza ki *SoftwareUpdatePackage.exe* utótaggal rendelkező szoftverfrissítési fájlt.

   ![eszköz frissítése](./media/azure-stack-edge-gpu-install-update/local-ui-update-3a.png)

3. Válassza az **Alkalmaz** lehetőséget. 

   ![eszköz frissítése](./media/azure-stack-edge-gpu-install-update/local-ui-update-4.png)

4. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra a folytatáshoz. Mivel az eszköz egyetlen csomópontos eszköz, a frissítés alkalmazása után az eszköz újraindul, és leállási idő van. 
   
   ![eszköz frissítése](./media/azure-stack-edge-gpu-install-update/local-ui-update-5.png)

5. A frissítés elindul. Az eszköz sikeres frissítése után újraindul. A helyi felhasználói felület ebben az időtartamban nem érhető el.
   
6. Az újraindítás után a rendszer a **bejelentkezési** oldalra kerül. Annak ellenőrzéséhez, hogy az eszköz szoftvere frissítve lett-e, a helyi webes felületen lépjen a **karbantartási**  >  **szoftverfrissítés**lapra. Ebben a példában a **2.0.1257.1591**jelenik meg.

   ![eszköz frissítése](./media/azure-stack-edge-gpu-install-update/local-ui-update-6.png) 

7. Most frissíti a Kubernetes szoftver verzióját. Ismételje meg a fenti lépéseket. Adja meg a Kubernetes frissítési fájljának elérési útját az *Kubernetes_Package.exe* utótaggal.  

   <!--![update device](./media/azure-stack-edge-gpu-install-update/local-ui-update-7.png)--> 

8. Válassza az **Alkalmaz** lehetőséget. 

   ![eszköz frissítése](./media/azure-stack-edge-gpu-install-update/local-ui-update-8.png)

9. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra a folytatáshoz. 

10. A Kubernetes frissítésének sikeres telepítése után a rendszer nem módosítja a megjelenő szoftvereket a **karbantartási**  >  **szoftverfrissítés**során. 


## <a name="next-steps"></a>Következő lépések

További információ [az Azure stack Edge felügyeletéről](azure-stack-edge-manage-access-power-connectivity-mode.md).
