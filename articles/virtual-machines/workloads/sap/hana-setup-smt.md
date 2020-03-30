---
title: Az SAP HANA SMT-kiszolgálójának beállítása az Azure-ban (nagy példányok) | Microsoft dokumentumok
description: SMT-kiszolgáló beállítása az SAP HANA az Azure-ban (nagy példányok).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 699a8a69621642d07d3547c07bb20c0d32ca7686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616991"
---
# <a name="set-up-smt-server-for-suse-linux"></a>SMT-kiszolgáló beállítása SUSE Linuxhoz
Az SAP HANA nagy példányai nem rendelkeznek közvetlen internetkapcsolattal. Ez nem egy egyszerű folyamat regisztrálni egy ilyen egységet az operációs rendszer szolgáltató, és a letöltés és a frissítések alkalmazása. A SUSE Linux megoldása egy SMT-kiszolgáló beállítása egy Azure virtuális gépen. A virtuális gép üzemeltetése egy Azure virtuális hálózat, amely csatlakozik a HANA nagy példány. Egy ilyen SMT-kiszolgáló esetén a HANA nagypéldány-egység regisztrálhatja és letöltheti a frissítéseket. 

A SUSE-ról további dokumentációt az [SLES 12 SP2 előfizetés-kezelő eszközében talál.](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf) 

A HANA nagy példányok feladatát teljesítő SMT-kiszolgáló telepítésének előfeltételei a következők:

- Egy Azure virtuális hálózat, amely csatlakozik a HANA nagy példány ExpressRoute-kapcsolat.
- Szervezethez társított SUSE-fiók. A szervezetnek érvényes SUSE-előfizetéssel kell rendelkeznie.

## <a name="install-smt-server-on-an-azure-virtual-machine"></a>SMT-kiszolgáló telepítése Azure-beli virtuális gépen

Először jelentkezzen be a [SUSE ügyfélközpontba.](https://scc.suse.com/)

Nyissa meg a **Szervezet** > **szervezeti adatai lehetőséget.** Ebben a szakaszban meg kell találnia az SMT-kiszolgáló beállításához szükséges hitelesítő adatokat.

Ezután telepítsen egy SUSE Linux virtuális gépet az Azure virtuális hálózatába. A virtuális gép üzembe helyezéséhez vegyen egy SLES 12 SP2 katalógus képet az Azure-ból (válassza a BYOS SUSE-lemezképet). A központi telepítési folyamat során ne adjon meg DNS-nevet, és ne használjon statikus IP-címeket.

![Képernyőkép az SMT-kiszolgáló virtuálisgép-telepítéséről](./media/hana-installation/image3_vm_deployment.png)

Az üzembe helyezett virtuális gép kisebb, és a belső IP-címet az Azure 10.34.1.4-es virtuális hálózatában kapta. A virtuális gép neve *smtserver*. A telepítés után a hana nagy példány egység vagy egységek kapcsolata van bejelölve. Attól függően, hogy hogyan rendszerezte a névfeloldást, előfordulhat, hogy konfigurálnia kell a HANA nagy példányegységeinek megoldását az Azure virtuális gép etc/hosts-ben. 

Lemez hozzáadása a virtuális géphez. Ezzel a lemezzel tartja a frissítéseket, és maga a rendszerindító lemez is túl kicsi lehet. Itt a lemez a /srv/www/htdocs kapcsolóhoz van csatlakoztatva, ahogy az a következő képernyőképen látható. Egy 100 GB-os lemez elegendő.

![Képernyőkép az SMT-kiszolgáló virtuálisgép-telepítéséről](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

Jelentkezzen be a HANA nagy példány egységvagy egységek, karbantartása /etc/hosts, és ellenőrizze, hogy elérheti az Azure virtuális gép, amely állítólag az SMT-kiszolgáló a hálózaton keresztül.

Az ellenőrzés után jelentkezzen be az Azure virtuális gép, amely az SMT-kiszolgáló futtatásához. Ha gitt et használ a virtuális gépbe való bejelentkezéshez, futtassa ezt a parancssorozatot a bash ablakban:

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

A beállítások aktiválásához indítsa újra a bash-t. Ezután indítsa el a YAST-t.

Csatlakoztassa a virtuális gép (smtserver) a SUSE-helyhez.

```
smtserver:~ # SUSEConnect -r <registration code> -e s<email address> --url https://scc.suse.com
Registered SLES_SAP 12.2 x86_64
To server: https://scc.suse.com
Using E-Mail: email address
Successfully registered system.
```

Miután a virtuális gép csatlakozott a SUSE-helyhez, telepítse az smt csomagokat. Az smt csomagok telepítéséhez használja a következő gitt parancsot.

```
smtserver:~ # zypper in smt
Refreshing service 'SUSE_Linux_Enterprise_Server_for_SAP_Applications_12_SP2_x86_64'.
Loading repository data...
Reading installed packages...
Resolving package dependencies...
```


Az smt csomagok telepítéséhez a YAST eszközzel is telepítheti. A YAST-ban nyissa meg a Szoftverkarbantartás ( **Szoftverkarbantartás**) című részt, és keresse meg az smt-t. Válassza a **smt**lehetőséget, amely automatikusan yast2-smt-re vált.

![Képernyőkép az SMT-ről a YAST-ben](./media/hana-installation/image5_smt_in_yast.PNG)


Fogadja el az smtserveren történő telepítéshez való kijelölést. A telepítés befejezése után lépjen az SMT-kiszolgáló konfigurációjára. Adja meg a korábban bekért SUSE ügyfélközpont szervezeti hitelesítő adatait. Adja meg az Azure virtuálisgép-állomásnevét az SMT-kiszolgáló URL-címeként is. Ebben a bemutatóban https:\//smtserver.

![Az SMT-kiszolgáló konfigurációjának képernyőképe](./media/hana-installation/image6_configuration_of_smtserver1.png)

Most ellenőrizze, hogy működik-e a SUSE Customer Centerhez való kapcsolat. Mint látható a következő screenshot, ebben a bemutató esetben, ez nem működik.

![Képernyőkép a SUSE Customer Centerhez való csatlakozás teszteléséről](./media/hana-installation/image7_test_connect.png)

Az SMT beállítás megkezdése után adjon meg egy adatbázis-jelszót. Mivel ez egy új telepítés, meg kell határoznia ezt a jelszót az alábbi képernyőképen látható módon.

![Képernyőkép: az adatbázis jelszavának megadása](./media/hana-installation/image8_define_db_passwd.PNG)

A következő lépés egy tanúsítvány létrehozása.

![Képernyőkép: tanúsítvány létrehozása az SMT-kiszolgálóhoz](./media/hana-installation/image9_certificate_creation.PNG)

A konfiguráció végén eltarthat néhány percig a szinkronizálási ellenőrzés futtatása. Az SMT-kiszolgáló telepítése és konfigurálása után a könyvtártár-tártárat a /srv/www/htdocs/ csatlakoztatási pont alatt találja. Vannak is néhány alkönyvtárak alatt repo. 

Ezekkel a parancsokkal indítsa újra az SMT-kiszolgálót és a hozzá kapcsolódó szolgáltatásokat.

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

## <a name="download-packages-onto-smt-server"></a>Csomagok letöltése Az SMT kiszolgálóra

Az összes szolgáltatás újraindítása után válassza ki a megfelelő csomagokat az SMT-kezelés ben a YAST használatával. A csomag kiválasztása a HANA nagy példánykiszolgáló operációsrendszer-lemezképéttől függ. A csomag kiválasztása nem függ az SLES-kiadástól vagy az SMT-kiszolgálót futtató virtuális gép verziójától. A következő képernyőképen egy példa látható a kiválasztási képernyőre.

![Képernyőkép a csomagok kijelöléséről](./media/hana-installation/image10_select_packages.PNG)

Ezután indítsa el a kiválasztott csomagok kezdeti másolatát a beállított SMT-kiszolgálóra. Ez a másolat az smt-mirror paranccsal aktiválódik a rendszerhéjban.

![Képernyőkép a csomagok SMT-kiszolgálóra való letöltéséről](./media/hana-installation/image11_download_packages.PNG)

A csomagokat át kell másolni a /srv/www/htdocs csatlakoztatási pont alatt létrehozott könyvtárakba. Ez a folyamat a kiválasztott csomagok számától függően egy órát vagy többet is igénybe vehet. A folyamat befejezése után lépjen az SMT-ügyfél beállítására. 

## <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>Az SMT-ügyfél beállítása HANA nagy példányegységeken

Az ügyfél vagy az ügyfelek ebben az esetben a HANA nagy példány egységek. Az SMT-kiszolgáló beállítása átmásolta a parancsfájlclientSetup4SMT.sh az Azure virtuális gépbe. Másolja át a parancsfájlt az SMT-kiszolgálóhoz csatlakozni kívánt HANA nagypéldány-egységre. Indítsa el a parancsfájlt a -h kapcsolóval, és adja meg paraméterként az SMT-kiszolgáló nevét. Ebben a példában a név *smtserver*.

![Képernyőkép az SMT-ügyfél konfigurálásáról](./media/hana-installation/image12_configure_client.PNG)

Lehetséges, hogy a tanúsítvány kiszolgálóról az ügyfél általi terhelése sikeres, de a regisztráció sikertelen, ahogy az a következő képernyőképen látható.

![Az ügyfél regisztrációs hibájának képernyőképe](./media/hana-installation/image13_registration_failed.PNG)

Ha a regisztráció sikertelen, olvassa el a [SUSE támogatási dokumentumcímű témakört,](https://www.suse.com/de-de/support/kb/doc/?id=7006024)és futtassa az ott leírt lépéseket.

> [!IMPORTANT] 
> A kiszolgáló nevéhez adja meg a teljesen minősített tartománynév nélküli virtuális gép nevét (ebben az esetben *smtserver).* 

A lépések futtatása után futtassa a következő parancsot a HANA nagypéldány-egységen:

```
SUSEConnect –cleanup
```

> [!Note] 
> Várjon néhány percet a lépés után. Ha azonnal futtatja clientSetup4SMT.sh, hibaüzenet jelenhet meg.

Ha olyan problémát tapasztal, amelyet a SUSE-cikk lépései alapján kell megoldania, indítsa újra a clientSetup4SMT.sh a HANA nagy példány egységen. Most sikeresen be kell fejeznie.

![Képernyőkép az ügyfélregisztráció sikerességéről](./media/hana-installation/image14_finish_client_config.PNG)

A HANA nagypéldány-egység SMT-ügyfélszolgáltatását úgy konfigurálta, hogy csatlakozzon az Azure virtuális gépben telepített SMT-kiszolgálóhoz. Most már a "zypper up" vagy a "zypper in" műveleteket is igénybe vehet az operációs rendszer frissítéseinek telepítéséhez a HANA nagy példányokhoz, vagy további csomagok telepítéséhez. Az SMT-kiszolgálón korábban letöltött frissítéseket csak letöltheti.

## <a name="next-steps"></a>További lépések
- [HANA telepítés HLI](hana-example-installation.md).











