---
title: Futtassa a Micro fókusz Enterprise Server 4.0-s Docker-tárolóban az Azure-beli virtuális gépek
description: Az IBM z/OS nagyszámítógépes számítási feladatok áthelyezési Micro fókusz vállalati kiszolgáló futtatja a Docker-tárolóban az Azure Virtual machines szolgáltatásban.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58896433"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Futtassa a Micro fókusz Enterprise Server 4.0 Docker-tárolóban az Azure-ban

Az Azure-ban a Docker-tárolóban futtathatja Micro fókusz Enterprise Server 4.0. Ez az oktatóanyag bemutatja, hogyan. A Windows CICS (vevői információk vezérlő rendszer) acctdemo bemutató vállalati kiszolgáló használ.

Docker a hordozhatóság és az elkülönítési hozzá alkalmazásokat. Például egy Docker-rendszerképet is exportálhat egy másik, a futtatásához egy Windows virtuális gép vagy egy tárház egy Windows server, a docker használatával. A Docker-rendszerkép futtatása az új helyen azonos konfigurációjú – Enterprise Server telepítése nélkül. Ez azt a kép részét. Licencelési továbbra is érvényesek.

Ebben az oktatóanyagban telepíti a **Windows 2016 Datacenter tárolókkal** virtuális gép (VM) az Azure Marketplace-ről. Ez a virtuális gép tartalmaz **Docker 18.09.0**. Az alábbi lépések bemutatják, hogyan kell a tároló üzembe helyezése, futtassa, és csatlakoztassa azt egy 3270 emulátort.

## <a name="prerequisites"></a>Előfeltételek

Az első lépések előtt, tekintse meg az Előfeltételek:

- Azure-előfizetés. Ha még nincs előfizetése, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), mielőtt hozzákezd.

- A Micro fókusz szoftvert és a egy érvényes licenccel (vagy próbaidőszakos díjcsomaghoz tartozó licenccel). Ha már fókusz Micro ügyfél, lépjen kapcsolatba a Micro fókusz képviselőjével. Ellenkező esetben [próbaverziója](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

     > [!NOTE]
     > A Docker bemutató fájlokat a mellékelt Enterprise Server 4.0-s verzióját. Ebben az oktatóanyagban ent\_kiszolgáló\_docker-fájlok\_4.0\_windows.zip. Elérheti, hogy elérhető-e a vállalati kiszolgáló telepítési fájl ugyanazon a helyen, vagy lépjen *Micro fókusz* a kezdéshez.

- A dokumentációban [Enterprise Server és a vállalati fejlesztői](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Virtuális gép létrehozása

1. Az adathordozó megvédi a ent a\_kiszolgáló\_docker-fájlok\_4.0\_windows.zip fájlt. Tegye biztonságossá a gen – Docker-éles-XXXXXXXX.mflic licencelési fájlt (a Docker-rendszerképek létrehozásához szükséges).

2. A virtuális gép létrehozásához. Ehhez nyissa meg az Azure Portal webhelyen válassza **erőforrás létrehozása** a bal felső sarokban, majd a szűrés *a windows server*. Az eredmények közül válassza ki **Windows Server 2016 Datacenter – tárolók**.

     ![Az Azure portál keresési eredményei](media/01-portal.png)

3. A virtuális gép tulajdonságainak beállítására, válassza ki a példány részletei:

    1. Válassza ki a virtuális gép méretét. A jelen oktatóanyag esetében fontolja meg egy **Standard DS2\_v2** 2 vcpu-k és 7 GB memóriával rendelkező virtuális Gépet.

    2. Válassza ki a **régió** és **erőforráscsoport** szeretné telepíteni.

    3. A **rendelkezésre állási beállítások**, az alapértelmezett beállítást használja.

    4. A **felhasználónév**, írja be a használni kívánt rendszergazdai fiók és a jelszavát.

    5. Győződjön meg arról, hogy **RDP a 3389-es port** meg nyitva. Csak ezt a portot kell nyilvánosan elérhető, így bejelentkezhet a virtuális géphez. Ezután elfogadhatja az alapértelmezett értékeket, és kattintson a **felülvizsgálat + létrehozása**.

     ![Hozzon létre egy virtuális gép ablaktábla](media/container-02.png)

4. Várjon, amíg az üzembe helyezés befejeződik (néhány perc alatt). Egy üzenet tájékoztatja, hogy a virtuális gép létrejött-e.

5. Kattintson a **erőforrás megnyitása** , nyissa meg a **áttekintése** a virtuális gép paneljén.

6. Kattintson a jobb oldalon a **Connect** gombra. A **csatlakozhat a virtuális gép** lehetőségek a jobb oldalon jelennek meg.

7. Kattintson a **RDP-fájl letöltése** gombra kattintva töltse le az RDP-fájlt, amely lehetővé teszi, hogy csatlakoztassa a virtuális Géphez.

8. Miután a fájl letöltése befejeződött, nyissa meg, és írja be a felhasználónevet és jelszót, amelyet a virtuális gép a.

     > [!NOTE]
     > Ne használja a vállalati hitelesítő adataival bejelentkezni. (Az RDP-ügyfelet feltételezi, hogy érdemes használni ezeket. Nem.)

9.  Válassza ki **több választási**, majd válassza ki a virtuális gép hitelesítő adatait.

Ezen a ponton a virtuális gép fut, és RDP-Kapcsolaton keresztül csatlakoztatott. Be van jelentkezve a, és készen áll a következő lépéssel.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Hozzon létre egy tesztkörnyezet könyvtárat és a zip-fájl feltöltése

1.  Hozzon létre egy könyvtárat a virtuális gép, ahol a bemutató és licenc fájlokat is feltölthet. Ha például **C:\\védőfal**.

2.  Töltse fel **ent\_kiszolgáló\_docker-fájlok\_4.0\_windows.zip** és a **ES – Docker-éles-XXXXXXXX.mflic** fájlt a létrehozott könyvtárba.

3.  Bontsa ki a zip-fájl tartalmát a **ent\_kiszolgáló\_docker-fájlok\_4.0\_windows** a kinyerési folyamat által létrehozott könyvtár. Ez a könyvtár tartalmaz egy információs fájl (a .html és a .txt fájlt), és két alkönyvtárakat, **EnterpriseServer** és **példák**.

4.  Másolás **ES – Docker-éles-XXXXXXXX.mflic** a c:\\védőfal\\ent\_kiszolgáló\_docker-fájlok\_4.0\_windows\\ EnterpriseServer és C:\\védőfal\\ent\_kiszolgáló\_docker-fájlok\_4.0\_windows\\példák\\CICS könyvtárak.

> [!NOTE]
> Ellenőrizze, hogy a licencelési fájl másolása mindkét címtárat. Győződjön meg arról, hogy a képek licencei rendben vannak, a Docker összeállítási lépés szükséges a meglétük.

## <a name="check-docker-version-and-create-base-image"></a>Docker-verziójának ellenőrzéséhez és az alap-rendszerkép létrehozása

> [!IMPORTANT]
> A megfelelő Docker-rendszerkép létrehozása két lépésből áll. Először hozza létre a vállalati Server 4.0 alaplemezkép. Ezután hozzon létre egy másik képet a x64 platform. Bár létrehozhat egy x86 (32 bites) rendszerkép, a 64 bites rendszerképet használja.

1. Nyisson meg egy parancssort.

2. Ellenőrizze, hogy a Docker telepítve van-e. A parancssorba írja be a következőt:

    ```
        docker version
    ```

     Például a verzió 18.09.0 írásakor még ez volt.

3. A könyvtár módosításához írja be a **cd \Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer**.

4. Típus **bld.bat IacceptEULA** számára a kezdeti alaprendszerképet az összeállítási folyamat megkezdéséhez. Várjon néhány percet, amíg a művelet végrehajtásához. Az eredmények között, figyelje meg, hogy a két-lemezkép létrehozása – x64 és a egy x86 az egyik:

     ![A parancs ablak, rajta a képek](media/container-04.png)

5. A végső a CICS bemutató-lemezkép létrehozásához váltson a CICS könyvtár beírásával **cd\\védőfal\\ent\_kiszolgáló\_docker-fájlok\_4.0-s\_windows\\ Példák\\CICS**.

6. A lemezkép létrehozásához írja be a **bld.bat x64**. Várjon néhány percet, amíg a folyamat futtatásához, és az üzenet arról, hogy a rendszerkép lett létrehozva.

7. Típus **docker-rendszerképek** összes telepítve a virtuális gépen a Docker-rendszerképek listájának megjelenítéséhez. Győződjön meg arról, hogy **microfocus/gen-acctdemo** az egyik közülük.

     ![A parancs ablak, rajta a gen-acctdemo kép](media/container-05.png)

## <a name="run-the-image"></a>A rendszerkép 

1.  Vállalati Server 4.0-s és a, a parancssorba írja be az acctdemo alkalmazás elindításához:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Például telepítése 3270 terminálemulátorral [x3270](http://x3270.bgp.nu/) porttal 9040, a lemezképen, hogy fut-e csatolásához vele.

3.  A acctdemo tároló IP-címének lekéréséhez, így a Docker működhet-e az általa felügyelt tárolókat a DHCP-kiszolgálóként:

    1.  Kérje le a futó tároló Azonosítóját. Típus **Docker ps** parancsot a parancssorba, és a Megjegyzés azonosítója (**22a0fe3159d0** ebben a példában). Mentse a következő lépéshez.

    2.  Az IP-cím lekérése a acctdemo tárolóhoz, az alábbiak szerint alkalmazza a Tárolóazonosító az előző lépésből:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Példa:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Vegye figyelembe a acctdemo kép IP-címét. Ha például a cím a következő kimenet 172.19.202.52.

     ![A parancs ablak, rajta az IP-cím](media/container-06.png)

5. Csatlakoztassa a lemezképet, az emulator használatával. Konfigurálja az emulátorban, a címet a acctdemo kép és a port 9040 használja. Itt van **172.19.202.52:9040**. Vásárlóink döntik hasonló lesz. A **CICS való bejelentkezés** képernyőn megnyílik.

    ![Frissítsen CICS képernyőre](media/container-07.png)

6. Jelentkezzen be a CICS régió megadásával **SYSAD** számára a **USERID** és **SYSAD** a a **jelszó**.

7. A képernyő segítségével az emulátort keymap törölje. X3270, válassza a **Keymap** menüpont.

8. Indítsa el a acctdemo kérelmet, írja be a következőt **ACCT**. Az alkalmazás a kezdeti képernyő jelenik meg.

     ![Fiók bemutató képernyő](media/container-08.png)

9. Kísérletezzen a megjelenített fióktípus esetében. Írja be például **D** a kérelem és **11111** számára a **fiók**. Próbálja meg más számlaszámok olyan 22222 33333 és így tovább.

     ![Fiók bemutató képernyő](media/container-09.png)

10. A vállalati kiszolgáló felügyelet konzoljának megjelenítéséhez nyissa meg a parancssort és írja be **http:172.19.202.52:86 indítása**

     ![Enterprise Server felügyeleti konzol](media/container-010.png)

Ennyi az egész! Most már fut, és a Docker-tárolóban CICS alkalmazás kezelése.

## <a name="next-steps"></a>További lépések

- [Micro fókusz Enterprise Server 4.0-s és 4.0-s vállalati fejlesztői telepítése Azure-ban](./set-up-micro-focus-azure.md)
- [A nagyszámítógépes alkalmazások áttelepítése](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
