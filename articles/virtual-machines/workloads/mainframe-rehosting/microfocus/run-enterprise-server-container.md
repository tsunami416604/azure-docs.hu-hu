---
title: Micro Focus Enterprise Server 4.0 futtatása Docker-tárolóban az Azure virtuális gépeken
description: Az IBM z/OS nagyszámítógépes számítási feladatainak újraüzemeltetése a Micro Focus Enterprise Server azure-beli virtuális gépeken lévő Docker-tárolóban való futtatásával.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61488464"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Micro Focus Enterprise Server 4.0 futtatása Docker-tárolóban az Azure-ban

Micro Focus Enterprise Server 4.0 futtatható egy Docker-tárolóban az Azure-ban. Ez a bemutató bemutatja, hogyan. A Windows CICS (Customer Information Control System) acctdemo bemutatót használ az Enterprise Server számára.

A Docker hordozhatóságot és elkülönítést ad az alkalmazásoknak. Például exportálhat egy Docker-lemezképet az egyik Windows virtuális gépről egy másik rendszeren való futtatáshoz, vagy egy tárházból egy Docker-rel rendelkező Windows-kiszolgálóra. A Docker-lemezkép az új helyen fut ugyanazzal a konfigurációval – anélkül, hogy telepítenie kellene az Enterprise Server t. Ez is a kép része. A licenceléssel kapcsolatos szempontok továbbra is érvényesek.

Ez az oktatóanyag telepíti a **Windows 2016 datacenter tárolók** virtuális gép (VM) az Azure Piactérről. Ez a virtuális gép tartalmazza **a Docker 18.09.0.** Az alábbi lépések bemutatják, hogyan telepítheti a tárolót, futtathatja, majd 3270-es emulátorral csatlakozhat hozzá.

## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt tekintse meg az alábbi előfeltételeket:

- Azure-előfizetés. Ha még nem rendelkezik ilyen, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

- A Micro Focus szoftver és egy érvényes licenc (vagy próbalicenc). Ha Ön már meglévő Micro Focus-ügyfél, forduljon a Micro Focus képviselőjéhez. Ellenkező esetben [kérjen próbaverziót.](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)

     > [!NOTE]
     > A Docker bemutatófájlok az Enterprise Server 4.0 részét tartalmazzák. Ez az oktatóanyag\_ent\_\_server\_dockerfiles 4.0 windows.zip fájlt használ. A kezdéshez ugyanonnan érheti el, ahol az Enterprise Server telepítőfájljához hozzáfért, vagy a *Micro Focus* ra.

- Az Enterprise Server és az [Enterprise Developer dokumentációja](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Biztosítsa az adathordozót\_az\_ent\_server\_dockerfiles 4.0 windows.zip fájlból. Biztosítsa az ES-Docker-Prod-XXXXXXXX.mflic licencelési fájlt (a Docker-lemezképek létrehozásához szükséges).

2. Hozza létre a virtuális gép. Ehhez nyissa meg az Azure Portalt, válassza az **Erőforrás létrehozása** a bal felső sarokban lehetőséget, és szűrjön a Windows *Server szerint*. Az eredmények között válassza a **Windows Server 2016 Datacenter lehetőséget – a tárolókkal.**

     ![Az Azure Portal keresési eredményei](media/01-portal.png)

3. A virtuális gép tulajdonságainak konfigurálásához válassza ki a példány részleteit:

    1. Válassza ki a virtuális gép méretét. Ebben az oktatóanyagban fontolja meg egy **szabványos DS2\_virtuális** gép használatát 2 vCPU-val és 7 GB memóriával.

    2. Válassza ki azt a **régiót** és **erőforráscsoportot,** amelybe telepíteni szeretné.

    3. A **Rendelkezésre állási beállításokhoz**használja az alapértelmezett beállítást.

    4. A **Felhasználónév**mezőbe írja be a használni kívánt rendszergazdai fiókot és a jelszót.

    5. Ellenőrizze, hogy a **3389-es RDP-port** nyitva van-e. Csak ezt a portot kell nyilvánosan elérhetővé tenni, így be tud jelentkezni a virtuális gépbe. Ezután fogadja el az összes alapértelmezett értéket, és kattintson a **Véleményezés+ create gombra.**

     ![Virtuálisgép-ablaktábla létrehozása](media/container-02.png)

4. Várjon, amíg a telepítés befejeződik (néhány perc). Egy üzenet azt állítja, hogy a virtuális gép létrejött.

5. Kattintson **az Ugrás az erőforráshoz** gombra a virtuális **gép áttekintése** panelhez.

6. A jobb oldalon kattintson a **Csatlakozás** gombra. A **Csatlakozás a virtuális géphez** beállítások a jobb oldalon jelennek meg.

7. Kattintson az **RDP-fájl letöltése** gombra a virtuális géphez csatolandó RDP-fájl letöltéséhez.

8. Miután a fájl letöltése befejeződött, nyissa meg, és írja be a virtuális géphez létrehozott felhasználónevet és jelszót.

     > [!NOTE]
     > Ne használja a vállalati hitelesítő adatokat a bejelentkezéshez. (Az RDP-ügyfél feltételezi, hogy ezeket szeretné használni. Nem.)

9.  Válassza **a További választási lehetőségek**lehetőséget, majd válassza ki a virtuális gép hitelesítő adatait.

Ezen a ponton a virtuális gép fut, és rdp-n keresztül csatlakozik. Be van jelentkezve, és készen áll a következő lépésre.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Hozzon létre egy sandbox könyvtárat, és töltse fel a zip fájlt

1.  Hozzon létre egy könyvtárat a virtuális gép, ahol feltöltheti a demo és licencfájlokat. Például: **C:\\Sandbox**.

2.  Töltse **fel\_\_a 4.0\_\_windows.zip kiszolgálódockerfiles** fájlt és az **ES-Docker-Prod-XXXXXXXX.mflic** fájlt a létrehozott könyvtárba.

3.  Bontsa ki a zip fájl tartalmát az **ent\_server\_\_dockerfiles 4.0\_windows** könyvtárba, amelyet a kibontási folyamat hozott létre. Ez a könyvtár tartalmaz egy readme fájlt (.html és .txt fájlként) és két alkönyvtárat, **az EnterpriseServer** t és **a Példák at.**

4.  Másolja **az ES-Docker-Prod-XXXXXXXX.mflic** \\fájlt a C:\\Sandbox ent\_server\_\_dockerfiles 4.0\_\_\_\_\\\\\_windows\\EnterpriseServer és C:\\Sandbox\\ent server dockerfiles 4.0 windows Examples CICS könyvtárak.

> [!NOTE]
> Győződjön meg arról, hogy a licencelési fájlt mindkét könyvtárba másolja. A Docker-build lépéshez szükségesek, hogy a rendszerképek megfelelő licenccel rendelkeznek.

## <a name="check-docker-version-and-create-base-image"></a>Ellenőrizze a Docker-verziót, és hozzon létre alaplemezképet

> [!IMPORTANT]
> A megfelelő Docker-rendszerkép létrehozása két lépésből áll. Először hozza létre az Enterprise Server 4.0 alaplemezképet.Ezután hozzon létre egy másik képet az x64 platformhoz. Bár x86-os (32 bites) képet is létrehozhat, használja a 64 bites képet.

1. Nyisson meg egy parancssort.

2. Ellenőrizze, hogy a Docker telepítve van-e. A parancssorba írja be a következőt:

    ```
        docker version
    ```

     A verzió például 18.09.0 volt, amikor ezt írták.

3. A könyvtár módosításához írja be a **cd \Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer parancsot.**

4. Írja be **a bld.bat IacceptEULA típust** a kezdeti alaplemezkép létrehozásának megkezdéséhez. Várjon néhány percet, amíg ez a folyamat fut. Az eredmények között figyelje meg a két létrehozott képet – az egyiket az x64-hez, a másikat pedig az x86-hoz:

     ![Képeket megjelenítő parancsablak](media/container-04.png)

5. A CICS-bemutató végső lemezképének létrehozásához váltson át a CICS könyvtárra a **\\cd\_\\Sandbox\\ent\_server\_dockerfiles\_4.0 windows Examples\\CICS (CICS)** parancs beírásával.

6. A kép létrehozásához írja be a **bld.bat x64 című szöveget.** Várjon néhány percet a folyamat futására, és az üzenet, amely arról szól, hogy a rendszerkép jött létre.

7. Írja be a **docker-lemezképeket** a virtuális gépre telepített összes Docker-lemezkép listájának megjelenítéséhez. Győződjön meg **róla, microfocus / es-acctdemo** az egyik közülük.

     ![Az es-acctdemo képet megjelenítő parancsablak](media/container-05.png)

## <a name="run-the-image"></a>A kép futtatása 

1.  Az Enterprise Server 4.0 és az acctdemo alkalmazás elindításához írja be a parancssorba a következőt:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Telepítsen egy 3270-es terminálemulátort, például [az x3270-et,](http://x3270.bgp.nu/) és használja a 9040-es porton keresztül a futó képhez való csatlakoztatáshoz.

3.  Az acctdemo tároló IP-címének beszerezni, így a Docker DHCP-kiszolgálóként működhet az általa kezelt tárolókhoz:

    1.  A futó tároló azonosítójának beszereznie. Írja be a **Docker ps** parancsot a parancssorba, és jegyezze fel az azonosítót (**22a0fe3159d0** ebben a példában). Tartogassa a következő lépésre.

    2.  Az acctdemo tároló IP-címének lekérni, használja a tároló azonosítóját az előző lépésben az alábbiak szerint:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Példa:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Jegyezze fel az acctdemo-lemezkép IP-címét. A következő kimenetben például a cím 172.19.202.52.

     ![Parancsablak az IP-címmel](media/container-06.png)

5. Szerelje fel a képet az emulátor segítségével. Állítsa be az emulátort az acctdemo lemezkép és a 9040-es port címének használatára. Itt van **172.19.202.52:9040**. A tiéd hasonló lesz. Megnyílik **a Signon to CICS** képernyő.

    ![Signon a CICS képernyőre](media/container-07.png)

6. Jelentkezzen be a CICS-régióba úgy, hogy beírja a **SYSAD-ot** a **USERID** és a **SYSAD** a **jelszóhoz.**

7. Törölje a képernyőt az emulátor kulcstérképével. X3270 esetén válassza a **Keymap** menüt.

8. Az acctdemo alkalmazás elindításához írja be az **ACCT ( ACCT**. Megjelenik az alkalmazás kezdeti képernyője.

     ![Fiók bemutatóképernyője](media/container-08.png)

9. Kísérletezzen a megjelenített fióktípusokkal. Írja be például a **D** betűt a Kérés hez, az **11111-et** pedig a **SZÁMLA mezőbe.** Más számlaszámok, hogy megpróbálja a 22222, 33333, és így tovább.

     ![Fiók bemutatóképernyője](media/container-09.png)

10. Az Enterprise Server Administration konzol megjelenítéséhez lépjen a parancssorra, és írja be a **kezdőképernyőt http:172.19.202.52:86**

     ![Vállalati kiszolgálófelügyelet konzol](media/container-010.png)

Ennyi az egész! Most fut, és egy CICS-alkalmazást kezel egy Docker-tárolóban.

## <a name="next-steps"></a>További lépések

- [A Micro Focus Enterprise Server 4.0 és az Enterprise Developer 4.0 telepítése az Azure-ra](./set-up-micro-focus-azure.md)
- [Nagyszámítógépes alkalmazásmigrálás](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
