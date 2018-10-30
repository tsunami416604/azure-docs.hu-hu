---
title: Az SAP Hana az Azure-ban (nagyméretű példányok) SMT kiszolgáló beállítása |} A Microsoft Docs
description: Hogyan állítható be SMT server, SAP Hana az Azure-ban (nagyméretű példányok).
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
ms.openlocfilehash: d9fe644b7cc7d1a13cb9ed2f7016f25b3e346dfb
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233174"
---
# <a name="set-up-smt-server-for-suse-linux"></a>SUSE Linux SMT kiszolgáló beállítása
Nagy példányok, SAP HANA nincs közvetlen kapcsolódás az internethez. Nem egy ilyen egység regisztrálja az operációs rendszer szolgáltatót, és a letöltés és frissítések alkalmazása egy egyszerű folyamatot. SUSE Linux-megoldás, hogy egy Azure virtuális gépen SMT kiszolgáló beállítása. Az Azure virtuális hálózat, amely kapcsolódik a HANA nagyméretű példányok az a virtuális gép működtetéséhez. Az ilyen az SMT kiszolgálóval a nagyméretű HANA-példány egység sikerült regisztrálása és a frissítések letöltése. 

A SUSE rendszeren további dokumentációjáért lásd: a [előfizetés felügyeleti eszköze SLES 12 SP2](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf). 

A feladatot választják HANA nagyméretű példányokhoz SMT kiszolgáló telepítésével előfeltételei a következők:

- Egy Azure virtuális hálózatra, amelyhez a HANA nagyméretű példány ExpressRoute-kapcsolatcsoporthoz csatlakozik.
- SUSE-fiók, amely egy szervezet társítva van. A szervezet érvényes SUSE-előfizetéssel kell rendelkeznie.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>SMT kiszolgáló telepítése egy Azure virtuális gépen

Első lépésként jelentkezzen be a [SUSE ügyfél Center](https://scc.suse.com/).

Lépjen a **szervezet** > **szervezeti hitelesítő adataival**. Az adott szakaszt keresse meg a szükséges hitelesítő adatokat az SMT kiszolgáló beállításához.

Ezután telepítse a SUSE Linux virtuális gép az Azure virtuális hálózatban. A virtuális gép üzembe helyezéséhez hajtsa végre a SLES 12 SP2 image z galerie Azure (válassza ki saját SUSE kép). A telepítési folyamat során nem ad meg egy DNS-nevet, és ne használja a statikus IP-címeket.

![Képernyőkép a virtuális gép üzembe helyezésének SMT kiszolgáló](./media/hana-installation/image3_vm_deployment.png)

A telepített virtuális gépek kisebb, és kapott a belső IP-címet 10.34.1.4 Azure virtuális hálózatban. A virtuális gép neve *smtserver*. A telepítés után a kapcsolat a HANA nagyméretű példányok egység vagy egységek be van jelölve. Attól függően, hogyan vannak rendszerezve névfeloldás szüksége lehet a nagyméretű HANA-példány egységek megoldás konfigurálása a stb/a gazdagépeket, az Azure virtuális gépen. 

Adjon hozzá egy lemezt a virtuális géphez. Használja ezt a lemezt, amely tárolja a frissítéseket, és lehet, hogy a rendszerindító lemez maga túl kicsi. Itt a lemez van csatlakoztatva /srv/www/htdocs, az alábbi képernyőképen látható módon. Egy 100 GB-os lemezt elegendőnek kell lennie.

![Képernyőkép a virtuális gép üzembe helyezésének SMT kiszolgáló](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Jelentkezzen be a nagyméretű HANA-példány egység vagy egységek, Hosts karbantartása, és ellenőrizze, hogy az Azure virtuális gépen, amely már szabadna futtatni a SMT kiszolgáló a hálózaton keresztül érhető el.

Az ellenőrzés után jelentkezzen be az Azure virtuális gépen, amely az SMT kiszolgáló kell futnia. Jelentkezzen be a virtuális gép a putty-t használ, ha ez a parancssorozat futtassa a bash ablakban:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

Indítsa újra a bash aktiválása a beállításokat. Indítsa el YAST.

Csatlakozzon a virtuális gép (smtserver) a SUSE-helyhez.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

A SUSE-helyre a virtuális gép csatlakoztatása után az smt csomagok telepítéséhez. A következő paranccsal putty az smt csomagok telepítéséhez.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


A YAST eszközzel is a smt csomagok telepítéséhez. YAST, lépjen a **Szoftverkarbantartást**, és keresse meg a smt. Válassza ki **smt**, amely automatikusan vált, amennyiben az yast2-smt.

![A YAST SMT képernyőképe](./media/hana-installation/image5_smt_in_yast.PNG)


Fogadja el a kijelölést a smtserver telepíthető. A telepítés befejezése után nyissa meg a SMT kiszolgáló konfigurációját. Adja meg a szervezeti hitelesítő adatokkal a korábban kapott SUSE-ügyfél központban. Az Azure virtuális gép állomásneve is adja meg az SMT kiszolgáló URL-címet. Ebben a bemutatóban rendelkezik https://smtserver.

![Képernyőkép az SMT kiszolgálókonfiguráció](./media/hana-installation/image6_configuration_of_smtserver1.png)

Most tesztelje a kapcsolatot a SUSE-ügyfél Center működik-e. Ahogyan az alábbi képernyőképen Ez a bemutató esetben látható, működött.

![A kapcsolat tesztelése a SUSE-ügyfél központ képernyőképe](./media/hana-installation/image7_test_connect.png)

Az SMT telepítő elindulása után adja meg a jelszót. Mivel a szolgáltatás új telepítést, az alábbi képernyőképen látható módon meg kell határozni, hogy a jelszó.

![Képernyőkép a database jelszavát meghatározása](./media/hana-installation/image8_define_db_passwd.PNG)

A következő lépés, hogy hozzon létre egy tanúsítványt.

![Képernyőkép a SMT Server tanúsítvány létrehozása](./media/hana-installation/image9_certificate_creation.PNG)

A konfiguráció végén is igénybe vehet néhány percet a szinkronizálási ellenőrzés futtatásához. A telepítés után a SMT kiszolgáló konfigurációját, a címtár-adattárat a csatlakoztatási pont /srv/www/htdocs alatt keresse meg és. Is találhatók a tárház egyes alkönyvtárak. 

Indítsa újra az SMT kiszolgáló és a hozzá kapcsolódó szolgáltatások, az alábbi parancsokkal.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>SMT szerveren-csomagok letöltése

Miután az összes szolgáltatás újra lesz indítva, jelölje ki a megfelelő csomagok SMT felügyeleti YAST használatával. A csomag kijelölése az operációs rendszer lemezképét, a nagyméretű HANA-példányt kiszolgáló függ. A csomag kijelölése nem függ a SLES kiadás vagy a SMT kiszolgálón futó virtuális gép verziója. A következő képernyőképen látható egy példa a kiválasztására szolgáló képernyő megjelenítéséhez.

![Képernyőkép a csomagok kiválasztása](./media/hana-installation/image10_select_packages.PNG)

Ezután indítsa el a kezdeti másolatot készít az SMT kiszolgáló beállítása a select csomagok. Ezt a példányt a rendszerhéj akkor aktiválódik, a parancs smt tükör használatával.

![Képernyőkép letöltése a csomagok SMT kiszolgálóra](./media/hana-installation/image11_download_packages.PNG)

A csomagok kell másolja be azokat a címtárakat, a csatlakoztatási pont /srv/www/htdocs alatt létrejön. A folyamat eltarthat egy óráig vagy tovább függően a kiválasztott csomagok számát. Ez a folyamat befejezését követően helyezze át az SMT ügyfél beállítása. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>A nagyméretű HANA-példány egységek az SMT ügyfél beállítása

Az ügyfél vagy ügyfelek ebben az esetben a nagyméretű HANA-példány egységek. A SMT kiszolgáló telepítését a parancsfájl clientSetup4SMT.sh átmásolhatja az Azure virtuális gépen. Parancsfájl keresztül a nagyméretű HANA-példány egység, példányt szeretne csatlakozni az SMT kiszolgálóhoz. Indítsa el a szkriptet a -h beállítással, és nevezze el a paramétert a SMT kiszolgáló. Ebben a példában a neve a következő *smtserver*.

![Képernyőkép az SMT ügyfél konfigurálása](./media/hana-installation/image12_configure_client.PNG)

Lehetőség, hogy az ügyfél által a kiszolgálóról a tanúsítvány a betöltés sikeres, de a regisztráció meghiúsul, az alábbi képernyőképen látható módon.

![Képernyőkép az ügyfél-regisztrációs hiba](./media/hana-installation/image13_registration_failed.PNG)

Ha a regisztráció meghiúsul, tekintse meg [SUSE támogatja a dokumentum](https://www.suse.com/de-de/support/kb/doc/?id=7006024), és ott futtatása leírt lépéseket.

> [!IMPORTANT] 
> A kiszolgáló neveként adja meg a virtuális gép nevét (ebben az esetben *smtserver*), anélkül, hogy a teljesen minősített tartománynevét. 

Miután ezeket a lépéseket, futtassa a következő parancsot a nagyméretű HANA-példány egység:

```
SUSEConnect –cleanup
```

> [!Note] 
> Várjon néhány percet után ezt a lépést. Ha clientSetup4SMT.sh azonnal futtatja, előfordulhat, hogy hibaüzenetet kap.

Ha szeretné javítani a lépéseket, a SUSE-cikk alapján problémát tapasztal, indítsa újra a nagyméretű HANA-példány egységen clientSetup4SMT.sh. Most, hogy sikeresen befejeződik.

![Képernyőkép a regisztráció sikeres ügyfélművelet](./media/hana-installation/image14_finish_client_config.PNG)

Konfigurálta az SMT ügyfél, a nagyméretű HANA-példány egység telepítette az Azure-beli virtuális gépen az SMT-kiszolgálóhoz való csatlakozáshoz. Most már veheti "mentése a zypper használatával" vagy "a zypper használatával" HANA nagyméretű példányok az operációs rendszer frissítéseinek telepítése, illetve további csomagokat telepíteni. Csak a SMT kiszolgálón előzőleg letöltött frissítések kérheti le.

## <a name="next-steps"></a>További lépések
- [HANA-telepítés HLI](hana-example-installation.md).











