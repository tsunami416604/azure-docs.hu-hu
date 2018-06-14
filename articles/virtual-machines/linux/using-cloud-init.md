---
title: Linux virtuális gépek az Azure-felhő inicializálás támogatását áttekintése |} Microsoft Docs
description: A Microsoft Azure felhőbe inicializálás funkcióiról
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: fbb6fc15663570d9b9470fc7d4de3c8eb30de9d9
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
ms.locfileid: "29763145"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Virtuális gépek Azure cloud inicializálás támogatása
Ez a cikk ismerteti, hogy létezik a támogatási [felhő inicializálás](https://cloudinit.readthedocs.io) konfigurálhat egy virtuális gép (VM) vagy a virtuális gép méretezési beállítja (VMSS) kiépítési idő az Azure-ban. Ezen felhő inicializálás parancsfájlok futtatása az első betöltés után az erőforrásokat az Azure-ban kiépített.  

## <a name="cloud-init-overview"></a>A cloud-init áttekintése
A [cloud-init](https://cloudinit.readthedocs.io) egy széles körben használt módszer a Linux rendszerű virtuális gépek első indításkor való testreszabásához. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. Neve a rendszerindítási folyamat során a felhő inicializálás, mert nincsenek további lépéseket vagy a konfiguráció alkalmazásához szükséges ügynökök.  Helyes formátumban módjáról további információ a `#cloud-config` fájlok, tekintse meg a [felhő-inicializációs dokumentációs oldalát](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` fájlok Base64 kódolású szöveget fájlok.

A cloud-init különböző disztribúciókon is működik. Például nem kell az **apt-get install** vagy a **yum install** használatával telepítenie a csomagokat. Ehelyett megadhatja a telepítendő csomagok listáját. A cloud-init automatikusan a natív csomagkezelő eszközt használja a kiválasztott disztribúcióhoz.

 Folyamatosan dolgozunk a Linux distro hitelesített Partnereinkkel együtt kell biztosítani a felhő inicializálás engedélyezve képek érhető el az Azure piactéren. Ezeket a lemezképeket fogja elérhetővé teheti a felhő inicializálás és konfigurációk zökkenőmentesen működjön együtt a virtuális gépek és Virtuálisgép-méretezési készletek (VMSS). Az alábbi táblázat ismerteti az Azure platformon aktuális felhő inicializálás engedélyezve képek elérhetőségét:

| Közzétevő | Ajánlat | SKU | Verzió | felhő inicializálás kész
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |16.04-LTS |legújabb |igen | 
|Canonical |UbuntuServer |14.04.5-LTS |legújabb |igen |
|CoreOS |CoreOS |Stable |legújabb |igen |
|OpenLogic |CentOS |7-CI |legújabb |előzetes verzió |
|RedHat |RHEL |7-RAW-CI |legújabb |előzetes verzió |

Jelenleg Azure verem nem támogatja az RHEL 7.4 és felhő inicializálás használatával CentOS 7.4 kiépítését.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Mi az a felhő inicializálás és a Linux ügynök (WALA) közötti különbség?
WALA kiépítéséhez és virtuális gépek és az Azure-bővítmények kezelésére használt Azure platform-specifikus ügynök. Azt is javítja az felhő inicializálás használata helyett a Linux-ügynök annak érdekében, hogy a meglévő felhőalapú inicializálás ügyfelek az aktuális felhő inicializálás parancsfájlok használata virtuális gépek konfigurációs feladatának.  Ha már rendelkezik már meglévő befejtetések felhő inicializálás parancsfájljainak konfigurálása a Linux rendszerek, **nincs szükség további beállításokra** és lehetővé teszi. 

Ha nem adja meg az Azure parancssori felület `--custom-data` kapcsolóval a kiépítés idő, WALA időt vesz igénybe, a minimális Virtuálisgép-létrehozásnál a virtuális gép kiépítéséhez, és az alapértelmezett értékekkel a telepítés befejezéséhez szükséges paraméter.  Ha a felhő inicializálás `--custom-data` kapcsolni, függetlenül az egyéni adatait (egyéni beállítások vagy teljes körű parancsfájl) szerepel a WALA alapértelmezett felülbírálja. 

A virtuális gépek WALA konfigurációk idő korlátozott belül a maximális Virtuálisgép-létrehozásnál idő működéséhez.  Felhő inicializálás konfiguráció alkalmazása a virtuális gépek nem rendelkeznek a megadott, és nem okoz a központi telepítés meghiúsulását által közbeni időtúllépés miatt meghiúsult. 

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Virtuális gép üzembe helyezése a felhő inicializálás engedélyezve van.
A felhő inicializálás engedélyezett virtuális gépek telepítése használata éppolyan egyszerű meg a hivatkozó, a felhő inicializálás engedélyezett terjesztési üzembe helyezése során.  Linux terjesztési maintainers engedélyezése és a felhő inicializálás integrálja az alapszintű Azure-közzétett rendszerképek választania kell. Miután meggyőződött a telepíteni kívánt lemezkép felhő inicializálás engedélyezve, a lemezkép központi telepítését az Azure parancssori felület használatával. 

A lemezkép központi telepítése az első lépés az, hogy hozzon létre egy erőforráscsoportot a a [az csoport létrehozása](/cli/azure/group#az_group_create) parancsot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
A következő lépés az, hogy az aktuális rendszerhéjban nevű fájl létrehozása *felhő-init.txt* , majd illessze be a következő konfigurációt. Hozzon létre a fájl ebben a példában a felhő rendszerhéj nem a helyi számítógépen. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor cloud-init.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Válassza ki a használandó #1 a **nano** szerkesztő. Ügyeljen arra, hogy megfelelően másolja ki a teljes cloud-init-fájlt, különösen az első sort:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Nyomja le az ENTER `ctrl-X` lépjen ki a fájlt, írja be a következőt `y` menteni a fájlt, és `enter` a kilépés után a fájlnév megerősítéséhez.

Az utolsó lépést, ha a virtuális gép és a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) parancsot. 

Az alábbi példakód létrehozza a virtuális gépek nevű *centos74* és SSH-kulcsok létrehozása, ha még nem léteznek a kulcs alapértelmezett helye. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  Használja a `--custom-data` paramétert a cloud-init konfigurációs fájl megadásához. Adja meg a *cloud-init.txt* konfiguráció teljes elérési útját, ha az aktuális munkakönyvtáron kívülre mentette. Az alábbi példakód létrehozza a virtuális gépek nevű *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

A virtuális gép létrehozásakor az Azure parancssori felület információit jeleníti meg a telepítésére jellemző. Jegyezze fel a `publicIpAddress` értékét. Ez a cím használható a virtuális gép eléréséhez.  A virtuális Gépet létrehozni, a csomagok telepítése és az alkalmazás elindításához némi időt vesz igénybe. Néhány háttérfeladat azután is tovább fut, hogy az Azure CLI visszairányítja Önt a parancssorhoz. SSH is be a virtuális Gépet, és a hibaelhárítási szakaszban leírt lépéseket a felhő inicializálás naplók megtekintéséhez. 

## <a name="troubleshooting-cloud-init"></a>Hibaelhárítási felhő inicializálás
Amennyiben a virtuális gép van kiépítve, felhőalapú inicializálás a modulok keresztül fog futni, és meghatározott parancsfájl `--custom-data` ahhoz, hogy a virtuális gép konfigurálása.  Ha kell elhárítania esetleges hibák vagy mulasztбs miatt a konfigurációról, akkor keresse meg a modul neve (`disk_setup` vagy `runcmd` például) található a felhőben inicializálás napló - **/var/log/cloud-init.log**.

> [!NOTE]
> Nem minden modul hibája eredményezi egy végzetes felhő inicializálás általános hiba. Használata esetén például a `runcmd` modul, a parancsfájl futása sikertelen, ha felhő inicializálás még tartozik kiépítés sikeres volt, mert a runcmd modul végrehajtása.

A felhő inicializálás naplózási további részletekért tekintse meg a [felhő inicializálás dokumentáció](http://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>További lépések
Konfigurációs módosítások inicializálás felhőben példákért lásd a következő dokumentumokat:
 
- [Egy további Linux-felhasználó hozzáadása a virtuális gépek](cloudinit-add-user.md)
- [Futtassa a Csomagkezelő első indításakor meglévő csomagok frissítése](cloudinit-update-vm.md)
- [Módosítsa a virtuális gép helyi állomásnév](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítését, a konfigurációs fájlokat és a kulcsok beszúrása](tutorial-automate-vm-deployment.md)
