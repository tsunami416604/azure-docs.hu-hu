---
title: A telepítő SMT server, SAP Hana az Azure-ban (nagyméretű példányok) hogyan |} A Microsoft Docs
description: Hogyan állíthatja be az Azure-ban (nagyméretű példányos) SAP Hana SMT kiszolgáló.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f387c1afe88f2bba476309b2e2e01942d2b7ae5b
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982625"
---
# <a name="setting-up-smt-server-for-suse-linux"></a>SUSE Linux SMT kiszolgáló beállítása
SAP HANA nagyméretű példányok nem rendelkezik közvetlen kapcsolódás az internethez. Ezért nem egy egyszerű folyamatot egy ilyen egység regisztrálása operációsrendszer-szolgáltatóval, és töltse le és -javítások alkalmazása a. Ha a SUSE Linux, egy megoldás lehet egy Azure-beli virtuális gépen SMT kiszolgáló beállítása. Mivel az Azure virtuális gép a felhőben egy Azure virtuális hálózat, amely kapcsolódik a nagyméretű HANA-példányt szeretne. Az ilyen az SMT kiszolgálóval a nagyméretű HANA-példány egység sikerült regisztrálni és létesít a javítókészletek letöltéséhez. 

SUSE Ez a témakör egy nagyobb útmutató azok [előfizetés felügyeleti eszköze SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

A feladatot választják a nagyméretű HANA-példány egy SMT kiszolgáló telepítésének előfeltétele, mint kell:

- Egy Azure virtuális hálózatot, amelyhez a HANA nagyméretű példány ER-kapcsolatcsoporthoz csatlakozik.
- SUSE-fiók, amely egy szervezet társítva van. Mivel a szervezet rendelkezik néhány érvényes SUSE-előfizetéssel kell.

## <a name="installation-of-smt-server-on-azure-vm"></a>Az Azure virtuális gépen SMT server telepítése

Ebben a lépésben egy Azure-beli virtuális gépen a SMT kiszolgálót telepíti. Az első mérték a bejelentkezni a [SUSE ügyfél Center](https://scc.suse.com/).

Bejelentkezett, lépjen a szervezet a szervezeti hitelesítő adataival-->. Az adott szakaszban keresse meg a szükséges hitelesítő adatokat az SMT kiszolgáló beállításához.

A harmadik lépésben pedig a SUSE Linux virtuális gép telepítése az Azure virtuális hálózat. A virtuális gép üzembe helyezéséhez hajtsa végre a SLES 12 SP2 image z galerie Azure (válassza ki saját SUSE kép). A telepítési folyamat során nem ad meg egy DNS-nevet, és nem használ statikus IP-címek ezen a képernyőfelvételen látható módon

![virtuális gép üzembe helyezésének SMT kiszolgáló](./media/hana-installation/image3_vm_deployment.png)

Az üzembe helyezett virtuális gép egy kisebb méretű virtuális gép volt, és az Azure virtuális hálózat 10.34.1.4 a belső IP-címe van. A virtuális gép nevét smtserver volt. A telepítés után a kapcsolat a HANA nagyméretű példány egység lett érvényesítve. Függ, hogyan vannak rendszerezve névfeloldás szüksége lehet a nagyméretű HANA-példány egységek megoldás konfigurálása a stb/a gazdagépeket, az Azure virtuális Gépen. Adjon hozzá egy további lemezt a virtuális gép, amelyet szeretne használni, amely tárolja a javítások. Lehet, hogy a rendszerindító lemez maga túl kicsi. A bemutatott esetben a lemez az alábbi képernyőképen látható módon /srv/www/htdocs van csatlakoztatva. Egy 100 GB-os lemezt elegendőnek kell lennie.

![virtuális gép üzembe helyezésének SMT kiszolgáló](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Jelentkezzen be a nagyméretű HANA-példány egység, a Hosts fenntartására, és ellenőrizze, hogy az Azure virtuális Gépen, amely már szabadna futtatni a SMT kiszolgáló a hálózaton keresztül érhető el.

Után ez az ellenőrzés sikeresen befejeződött, jelentkezzen be az Azure virtuális Gépen, amely az SMT kiszolgáló kell futnia kell. Jelentkezzen be a virtuális gép a putty-t használ, ha szüksége ezen parancsok sorozatát végrehajtása a bash-ablakban:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Ezek a parancsok végrehajtása után indítsa újra aktiválni a beállításokat a bash. Indítsa el YAST.

Csatlakozzon a virtuális gép (smtserver) a SUSE-helyhez.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Miután a virtuális gép csatlakoztatva van a SUSE-helyhez, a smt csomagok telepítéséhez. A következő paranccsal putty az smt csomagok telepítéséhez.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


YAST eszköz használatával telepítse a smt csomagokat. A YAST lépjen a szoftverfrissítési karbantartás, és keresse meg a smt. Válassza ki az smt, amely automatikusan vált, amennyiben az yast2-smt alább látható módon

![A yast SMT](./media/hana-installation/image5_smt_in_yast.PNG)


Fogadja el a kijelölést a smtserver telepíthető. A telepítést követően nyissa meg a SMT konfigurációjának, és adja meg a szervezeti hitelesítő adatokkal a korábban kapott SUSE-ügyfél központban. Az Azure virtuális gép állomásneve is adja meg az SMT kiszolgáló URL-címet. Ebben a bemutatóban volt https://smtserver jelenik meg a következő ábrák szerint.

![SMT kiszolgáló konfigurációja](./media/hana-installation/image6_configuration_of_smtserver1.png)

Következő lépésként tesztelje a kapcsolatot a SUSE-ügyfél Center működik-e kell. Ahogyan az a következő ábrák a bemutató esetben látható, működött.

![Teszt SUSE ügyfél Center csatlakozni.](./media/hana-installation/image7_test_connect.png)

Egyszer a SMT telepítő elindul, meg kell adnia jelszót. Mivel ez egy új telepítést, határozza meg, hogy a jelszó, ahogyan az a következő ábrák kell.

![Adatbázis jelszavának megadása](./media/hana-installation/image8_define_db_passwd.PNG)

A következő kapcsolati van akkor, ha a tanúsítvány jön létre. A párbeszédpanelen látható a következő módon meg, és lépjen tovább, a lépést.

![A SMT server tanúsítvány létrehozása](./media/hana-installation/image9_certificate_creation.PNG)

Előfordulhat, hogy néhány percet töltött "Futtatás szinkronizálásának ellenőrzése" lépésében a konfiguráció végén. A telepítés után a SMT kiszolgáló konfigurációját, a címtár-adattárat a csatlakoztatási pont /srv/www/htdocs alatt keresse meg és ezen felül az egyes alkönyvtárak tárház alatt. 

Indítsa újra az SMT kiszolgáló és a hozzá kapcsolódó szolgáltatások, az alábbi parancsokkal.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-of-packages-onto-smt-server"></a>A csomagok SMT szerveren letöltése

Után minden szolgáltatás újra lesz indítva, jelölje ki a megfelelő csomagok SMT felügyelet Yast használatával. A csomag kiválasztása attól függ, a nagyméretű HANA-példányt kiszolgáló operációsrendszer-lemezkép, és ne a SLES kiadás vagy a SMT kiszolgálón futó virtuális gép verziója. Az alábbiakban látható egy példa a kiválasztására szolgáló képernyő megjelenítéséhez.

![Csomagok kiválasztása](./media/hana-installation/image10_select_packages.PNG)

Miután végzett a csomag kijelölése, akkor indítsa el a kezdeti másolatot készít az SMT kiszolgáló beállítása a select csomagok. Ezt a példányt a rendszerhéj használatával a parancs smt-tükör alább látható módon aktiválódik


![Töltse le a csomagokat SMT kiszolgálóra](./media/hana-installation/image11_download_packages.PNG)

Ahogyan fent látható, a csomagok kell másolja be azokat a címtárakat, a csatlakoztatási pont /srv/www/htdocs alatt létrejön. Ez a folyamat eltarthat egy ideig. Függő csomagok számát választja, azt is igénybe vehet egy óráig vagy tovább.
Mivel ez a folyamat befejezését követően kell helyezze át az SMT ügyfél telepítése. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>A nagyméretű HANA-példány egységek az SMT ügyfél beállítása

Az ügyféltől/ügyfelektől érkezők ebben az esetben a nagyméretű HANA-példány egységek. A SMT kiszolgáló telepítését a parancsfájl clientSetup4SMT.sh átmásolhatja az Azure virtuális Gépen. Parancsfájl keresztül a nagyméretű HANA-példány egység, példányt szeretne csatlakozni az SMT kiszolgálóhoz. Indítsa el a szkriptet a -h beállítással és adjon neki paraméterként az SMT kiszolgáló nevét. Az ebben a példában smtserver.

![SMT ügyfél konfigurálása](./media/hana-installation/image12_configure_client.PNG)

Előfordulhat, hogy egy olyan forgatókönyvet, ahol az ügyfél által a kiszolgálóról a tanúsítvány betöltése sikeres volt, de a regisztráció sikertelen volt, ahogy az alábbi.

![Ügyfél regisztrálása meghiúsul](./media/hana-installation/image13_registration_failed.PNG)

Ha a regisztráció sikertelen, olvassa el ezt [SUSE támogatja a dokumentum](https://www.suse.com/de-de/support/kb/doc/?id=7006024) , és hajtsa végre az ott ismertetett lépéseket.

> [!IMPORTANT] 
> Kiszolgáló neve, meg kell adnia a virtuális gép, ez megkülönbözteti a kis smtserver, anélkül, hogy a teljes tartománynév a nevét. Csak a virtuális gép neve működését. 

Után a lépések végrehajtása megtörtént, hajtsa végre a következő parancsot a nagyméretű HANA-példány egységen kell

```
SUSEConnect –cleanup
```

> [!Note] 
> A vizsgálatok során mindig becsületessége Várjon pár percet után ezt a lépést. Az azonnali végrehajtás clientSetup4SMT.sh után a korrekciós intézkedéseket, a SUSE-cikkben leírt fejeződött be, hogy a tanúsítvány nem lenne érvényes még üzeneteket. Általában 5 – 10 percet várakozik, és clientSetup4SMT.sh végrehajtása befejeződött, a sikeres ügyfél-konfigurációval.

Ha a probléma akkor szükséges, javítsa ki a lépéseket, a SUSE-cikk alapján történő futtatta, indítsa újra a nagyméretű HANA-példány egységen clientSetup4SMT.sh újra szeretne. Most azt sikeresen be kell alább látható módon.

![Sikeres ügyfél-regisztrációk](./media/hana-installation/image14_finish_client_config.PNG)

Az ebben a lépésben a nagyméretű HANA-példány egység való csatlakozáshoz a SMT kiszolgálón telepítette, az Azure-beli virtuális gépen az SMT ügyfél konfigurálva. Most már elvégezhető "mentése a zypper használatával" vagy "a zypper használatával" az operációs rendszer telepíthetnek nagyméretű HANA-példányokhoz, vagy további csomagokat telepíteni. Egyetértés, hogy csak kap, amely a SMT kiszolgálón előzőleg letöltött javítások.

**Következő lépések**
- Tekintse meg [HLI HANA-telepítés](hana-example-installation.md).











