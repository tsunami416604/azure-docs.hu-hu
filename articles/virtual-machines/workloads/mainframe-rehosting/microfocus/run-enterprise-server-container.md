---
title: A Micro Focus Enterprise Server 4,0 futtatása egy Docker-tárolóban az Azure Virtual Machines
description: A Micro Focus Enterprise Servert az Azure Virtual Machines Docker-tárolójában futtatva helyezheti át az IBM z/OS mainframe-munkaterheléseket.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "61488464"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>A Micro Focus Enterprise Server 4,0 futtatása az Azure-beli Docker-tárolóban

A Micro Focus Enterprise Server 4,0 egy Docker-tárolóban is futtatható az Azure-ban. Ez az oktatóanyag bemutatja, hogyan. A vállalati kiszolgáló Windows CICS (Customer Information Control System) acctdemo bemutatóját használja.

A Docker a hordozhatóságot és az elkülönítést adja az alkalmazásokhoz. Például exportálhat egy Docker-rendszerképet egy Windows rendszerű virtuális gépről egy másikra, vagy egy adattárból egy Windows Serverre a Docker használatával. A Docker-rendszerkép az új helyen fut ugyanazzal a konfigurációval, anélkül, hogy telepítenie kellene a vállalati kiszolgálót. A rendszerkép részét képezi. A licencelési megfontolások továbbra is érvényben vannak.

Ez az oktatóanyag telepíti a **Windows 2016 Datacenter-t a containers** Virtual Machine (VM) szolgáltatással az Azure piactéren. Ez a virtuális gép tartalmaz **Docker-18.09.0**. Az alábbi lépések bemutatják, hogyan helyezheti üzembe a tárolót, hogyan futtathatja, majd egy 3270-emulátorral csatlakozhat hozzá.

## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt tekintse át az alábbi előfeltételeket:

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

- A Micro Focus szoftver és egy érvényes licenc (vagy próbaverziós licenc). Ha már meglévő Micro Focus-ügyfelet használ, forduljon a Micro Focus képviselőjéhez. Ellenkező esetben [próbaverziót igényelhet](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

     > [!NOTE]
     > A Docker demonstrációs fájljai a vállalati kiszolgáló 4,0-es verziójában találhatók. Ez az oktatóanyag az\_ENT\_Server\_dockerfiles\_4,0 Windows. zip protokollt használja. Ugyanazon a helyen érheti el, mint a vállalati kiszolgáló telepítési fájlját, vagy a kezdéshez lépjen a *Micro Focus* elemre.

- A [vállalati kiszolgáló és a nagyvállalati fejlesztő](https://www.microfocus.com/documentation/enterprise-developer/#")dokumentációja.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Védje az adathordozót az ENT\_server\_dockerfiles\_4,0\_Windows. zip fájlból. Az ES-Docker-Prod-XXXXXXXX. mflic licencelési fájl biztonságossá tétele (a Docker-lemezképek létrehozásához szükséges).

2. Hozza létre a virtuális gépet. Ehhez nyissa meg Azure Portal, válassza az **erőforrás létrehozása** lehetőséget a bal felső sarokban, majd a szűrés a *Windows Serveren*. Az eredmények között válassza a **Windows Server 2016 Datacenter – tárolók**lehetőséget.

     ![Azure Portal keresési eredmények](media/01-portal.png)

3. A virtuális gép tulajdonságainak konfigurálásához válassza a példány részletei lehetőséget:

    1. Válassza ki a virtuális gép méretét. Ebben az oktatóanyagban érdemes egy **standard DS2\_v2** virtuális gépet használni 2 vCPU és 7 GB memóriával.

    2. Válassza ki azt a **régiót** és **erőforráscsoportot** , amelyet központilag telepíteni szeretne.

    3. A **rendelkezésre állási beállításokhoz**használja az alapértelmezett beállítást.

    4. A **Felhasználónév**mezőbe írja be a használni kívánt rendszergazdai fiókot és a jelszót.

    5. Győződjön meg arról, hogy a 3389-es **RDP-port** nyitva van. Csak a portot nyilvánosan elérhetővé kell tenni, hogy be lehessen jelentkezni a virtuális gépre. Ezután fogadja el az összes alapértelmezett értéket, majd kattintson a **felülvizsgálat + létrehozás**gombra.

     ![Virtuális gép panel létrehozása](media/container-02.png)

4. Várjon, amíg a telepítés befejeződik (néhány perc). Egy üzenet jelzi, hogy a virtuális gép létrejött.

5. A virtuális gép **Áttekintés** paneljére kattintva nyissa meg az **erőforrást** .

6. A jobb oldalon kattintson a **kapcsolat** gombra. A jobb oldalon megjelenik a **Kapcsolódás virtuális géphez** lehetőség.

7. Az **RDP-fájl letöltése** gombra kattintva töltse le az RDP-fájlt, amely lehetővé teszi a virtuális géphez való csatolást.

8. Miután a fájl letöltése befejeződött, nyissa meg a fájlt, és írja be a virtuális géphez létrehozott felhasználónevet és jelszót.

     > [!NOTE]
     > A bejelentkezéshez ne használja vállalati hitelesítő adatait. (Az RDP-ügyfél feltételezi, hogy ezeket szeretné használni. Nem.)

9.  Válassza a **további lehetőségek**lehetőséget, majd válassza ki a virtuális gép hitelesítő adatait.

Ezen a ponton a virtuális gép RDP-kapcsolaton keresztül fut és csatlakoztatva van. Bejelentkezett, és készen áll a következő lépésre.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Homokozó könyvtár létrehozása és a zip-fájl feltöltése

1.  Hozzon létre egy könyvtárat a virtuális gépen, ahol feltöltheti a demót és a licenceket tartalmazó fájlokat. Például **C:\\homokozó**.

2.  Töltse **fel\_az\_ENT\_Server\_Dockerfiles 4,0 Windows. zip** és az **es-Docker-Prod-XXXXXXXX. mflic** fájlt a létrehozott könyvtárba.

3.  Bontsa ki a zip-fájl tartalmát a kinyerési folyamat által létrehozott **ENT\_Server\_\_dockerfiles 4,0\_Windows** könyvtárba. Ez a könyvtár egy információs fájlt (. html és. txt fájlt) és két alkönyvtárat, **EnterpriseServer** és **példákat**tartalmaz.

4.  Másolja az **es-Docker-Prod-XXXXXXXX. mflic** parancsot a C\\:\\sandbox\_ENT\_kiszolgáló\_dockerfiles\_4,0\\Windows EnterpriseServer és C\\:\\sandbox\_ENT\_Server\_dockerfiles\_4,0\\Windows\\példák CICS könyvtárak.

> [!NOTE]
> Győződjön meg arról, hogy mindkét könyvtárba másolja a licencelési fájlt. A Docker-Build lépéshez szükségük van a lemezképek megfelelő licenccel való elvégzésére.

## <a name="check-docker-version-and-create-base-image"></a>Docker-verzió keresése és alaprendszerkép létrehozása

> [!IMPORTANT]
> A megfelelő Docker-rendszerkép létrehozása két lépésből álló folyamat. Először hozza létre a vállalati kiszolgáló 4,0 alaprendszerképét.Ezután hozzon létre egy másik rendszerképet az x64 platformhoz. Bár létrehozhat egy x86-os (32 bites) rendszerképet, a 64 bites rendszerképet is használhatja.

1. Nyisson meg egy parancssort.

2. Győződjön meg arról, hogy a Docker telepítve van. A parancssorba írja be a következőt:

    ```
        docker version
    ```

     Például a verzió 18.09.0 volt.

3. A könyvtár módosításához írja be a következőt: **CD \sandbox\ ent_server_dockerfiles_4.0_windows \enterpriseserver**.

4. A kezdeti alaprendszerkép létrehozási folyamatának megkezdéséhez írja be a következőt: **Bld. bat IacceptEULA** . Várjon néhány percet a folyamat futtatásához. Az eredmények között figyelje meg a két létrehozott rendszerképet – egyet az x64-re és egyet az x86-hoz:

     ![Képek megjelenítése Parancsablak](media/container-04.png)

5. A CICS bemutató utolsó rendszerképének létrehozásához váltson a CICS könyvtárba úgy, hogy beírja **a\\CD\\sandbox\_ENT\_kiszolgáló\_dockerfiles\_4,0\\Windows\\példák CICS**.

6. A rendszerkép létrehozásához írja be a következőt: **Bld. bat x64**. Várjon néhány percet, amíg a folyamat elindul, és az üzenet azt jelzi, hogy a rendszerkép létrejött.

7. Írja be a **Docker-rendszerképeket** a virtuális gépre telepített összes Docker-rendszerkép listájának megjelenítéséhez. Győződjön meg arról, hogy a **Focus/es-acctdemo** az egyik.

     ![Az es-acctdemo rendszerkép megjelenítése Parancsablak](media/container-05.png)

## <a name="run-the-image"></a>A rendszerkép futtatása 

1.  A vállalati kiszolgáló 4,0-es és a acctdemo-alkalmazás indításához írja be a parancssorba a következőt:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Telepítsen egy 3270-es terminál-emulátort, például a [x3270](http://x3270.bgp.nu/) -t, és csatlakoztassa a 9040-es porton keresztül a futó rendszerképhez.

3.  Szerezze be a acctdemo tároló IP-címét, így a Docker DHCP-kiszolgálóként működhet az általa kezelt tárolók esetében:

    1.  A futó tároló AZONOSÍTÓjának beolvasása. Írja be a **Docker PS** parancsot a parancssorba, és jegyezze fel az azonosítót (ebben a példában a**22a0fe3159d0** ). Mentse a következő lépéshez.

    2.  A acctdemo tároló IP-címének lekéréséhez használja az előző lépés tároló-AZONOSÍTÓját az alábbiak szerint:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Például:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Jegyezze fel a acctdemo-rendszerkép IP-címét. Például a következő kimenetben található 172.19.202.52.

     ![IP-címet megjelenítő Parancsablak](media/container-06.png)

5. Csatlakoztassa a lemezképet az emulátor használatával. Konfigurálja az emulátort a acctdemo-rendszerkép és a 9040-es port használatára. Itt a **172.19.202.52:9040**. A tiéd hasonló lesz. Megnyílik a **Bejelentkezés a CICS** képernyőn.

    ![Bejelentkezés a CICS képernyőre](media/container-07.png)

6. Jelentkezzen be a CICS régióba úgy, hogy beírja a **jelszót**a **userid** és a **SYSAD** **SYSAD** .

7. Törölje a képernyőt az emulátor kiosztás használatával. A x3270 válassza a **kiosztás** menüpontot.

8. A acctdemo alkalmazás indításához írja be a következőt: **acct**. Megjelenik az alkalmazás kezdeti képernyője.

     ![Fiók bemutató képernyője](media/container-08.png)

9. Kísérletezzen a megjelenítési fiókok típusaival. Írja be például a **D** értéket a kérelemhez, a **11111** pedig a **fiókhoz**. A kipróbálható egyéb számlaszámok 22222, 33333 stb.

     ![Fiók bemutató képernyője](media/container-09.png)

10. A vállalati kiszolgáló felügyeleti konzoljának megjelenítéséhez nyissa meg a parancssort, és írja be a következőt **: Start http: 172.19.202.52:86**

     ![Vállalati kiszolgáló felügyeleti konzolja](media/container-010.png)

Ennyi az egész! Most egy CICS-alkalmazást futtat és felügyel egy Docker-tárolóban.

## <a name="next-steps"></a>További lépések

- [A Micro Focus Enterprise Server 4,0 és Enterprise Developer 4,0 telepítése az Azure-ban](./set-up-micro-focus-azure.md)
- [Nagyszámítógépes alkalmazásmigrálás](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
