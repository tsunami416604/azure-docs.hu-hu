---
title: A cloud-init támogatása az Azure-ban Linux rendszerű virtuális gépek áttekintése |} A Microsoft Docs
description: A cloud-init képességeit a Microsoft Azure-ban – áttekintés
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
ms.openlocfilehash: 0f7660e8534a74eabe32611c4c01ae5587af7cee
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188873"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>A cloud-init támogatása az Azure virtual machines
Ez a cikk ismerteti, hogy létezik a támogatási [a cloud-init](https://cloudinit.readthedocs.io) konfigurálása a virtuális gép (VM) vagy virtuálisgép-méretezési csoportok (VMSS) kiépítés ideje az Azure-ban. Ezen a cloud-init parancsfájlok futtatása az első rendszerindításkor az Azure-ban kiépített erőforrások után.  

## <a name="cloud-init-overview"></a>A cloud-init áttekintése
A [cloud-init](https://cloudinit.readthedocs.io) egy széles körben használt módszer a Linux rendszerű virtuális gépek első indításkor való testreszabásához. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. A cloud-init nevezzük az első rendszerindítás során, mert nincsenek további lépéseket vagy szükséges ügynökök a alkalmazni a konfigurációt.  További információt a megfelelő formázása a `#cloud-config` fájlokat, tekintse meg a [a cloud-init dokumentációs oldalának](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` fájlok Base64 kódolású szöveges fájlok.

A cloud-init különböző disztribúciókon is működik. Például nem kell az **apt-get install** vagy a **yum install** használatával telepítenie a csomagokat. Ehelyett megadhatja a telepítendő csomagok listáját. A cloud-init automatikusan a natív csomagkezelő eszközt használja a kiválasztott disztribúcióhoz.

 Aktívan dolgozunk a támogatott Linux disztribúció partnereink ahhoz, hogy engedélyezve van a cloud-init lemezkép érhető el az Azure Marketplace-en. Ezeket a lemezképeket készítsen a cloud-init központi telepítések és konfigurációkat és a virtuális gépek és a virtuális gép méretezési csoportok (VMSS) zökkenőmentesen működjön. Az alábbi táblázat ismerteti az aktuális engedélyezve van a cloud-init lemezképek rendelkezésre állás az Azure platformon:

| Közzétevő | Ajánlat | SKU | Verzió | a cloud-init kész |
|:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |18.04-LTS |legújabb |igen | 
|Canonical |UbuntuServer |17.10 |legújabb |igen | 
|Canonical |UbuntuServer |16.04-LTS |legújabb |igen | 
|Canonical |UbuntuServer |14.04.5-LTS |legújabb |igen |
|CoreOS |CoreOS |Stable |legújabb |igen |
|OpenLogic |CentOS |7-CI |legújabb |előzetes verzióban |
|RedHat |RHEL |7-RAW-CI |legújabb |előzetes verzióban |

Jelenleg az Azure Stack nem támogatja az RHEL 7.4 és a cloud-init használata CentOS 7.4 üzembe.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Mi a különbség a cloud-init és a Linuxos ügynök (WALA)?
WALA használt üzembe helyezhető és a virtuális gépek konfigurálása és kezelése az Azure-bővítményeket az Azure platform-specifikus ügynök. Továbbfejlesztjük a feladat konfigurálása a cloud-init használata helyett a Linux-ügynök annak érdekében, hogy a cloud-init meglévő ügyfelek az aktuális a cloud-init-parancsfájlok használata virtuális gépek.  Rendelkezik egy meglévő beruházásait a cloud-init-parancsprogramok a Linux rendszerek konfigurálása, ha nincsenek **nincs szükség további beállításokra** az engedélyezésükhöz. 

Ha nem adja meg az Azure CLI `--custom-data` kapcsoló, a kiépítés ideje, WALA vesz igénybe a minimális virtuális gép kiépítése a virtuális gép és az alapértelmezett értékekkel telepítésének befejezéséhez szükséges paramétereket.  Ha a cloud-init `--custom-data` váltson, akármilyen területen is szerepel az egyéni adatok (az egyes beállítások vagy teljes szkript) felülbírálja az WALA alapértelmezett értékeket. 

Virtuális gépek WALA konfigurációk idő korlátozott a kiépítés ideje legnagyobb virtuális gépen működik.  A cloud-init konfigurációt virtuális gépeken alkalmazott korlátozásai nem rendelkeznek, és nem okoz közbeni időtúllépés miatt meghiúsult a sikertelen központi telepítés. 

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Virtuális gép üzembe helyezése a cloud-init engedélyezve van.
Engedélyezve van a cloud-init virtuális gép üzembe helyezése rendkívül egyszerű, üzembe helyezés során a cloud-init-kompatibilis terjesztési hivatkozik.  Linux terjesztési maintainers kell engedélyezni, és a cloud-init integrálása az Azure alapszintű közzétett képek. Miután meggyőződött számára telepíteni kívánja a lemezképet a cloud-init engedélyezve, a rendszerkép üzembe helyezése az Azure CLI használatával. 

A lemezkép központi telepítése az első lépés az, hogy hozzon létre egy erőforráscsoportot a [az csoport létrehozása](/cli/azure/group#az_group_create) parancsot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
A következő lépés az, hogy hozzon létre egy fájlt az aktuális felületen, *cloud-init.txt* , és illessze be a következő konfigurációt. Ebben a példában a Cloud shellben, nem a helyi gépén hozzon létre a fájlt. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor cloud-init.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Válassza ki a használandó #1 a **nano** szerkesztő. Ügyeljen arra, hogy megfelelően másolja ki a teljes cloud-init-fájlt, különösen az első sort:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Nyomja le az `ctrl-X` lépjen ki a fájlt, írja be a következőt `y` menteni a fájlt, és `enter` , erősítse meg a fájl nevét, a kilépés után.

Az utolsó lépés az, hogy a virtuális gép létrehozása a [az virtuális gép létrehozása](/cli/azure/vm#az_vm_create) parancsot. 

A következő példában létrehozunk egy nevű virtuális Gépet *centos74* és SSH-kulcsokat hoz létre, ha azok még nem léteznek a kulcsok alapértelmezett helyén. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  Használja a `--custom-data` paramétert a cloud-init konfigurációs fájl megadásához. Adja meg a *cloud-init.txt* konfiguráció teljes elérési útját, ha az aktuális munkakönyvtáron kívülre mentette. A következő példában létrehozunk egy nevű virtuális Gépet *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

A virtuális gép létrejött, az Azure CLI információkat jelenít meg az üzemelő példány adott. Jegyezze fel a `publicIpAddress` értékét. Ez a cím használható a virtuális gép eléréséhez.  A virtuális gép létrehozása, a csomagok telepítése és az alkalmazás elindítása némi időt vesz igénybe. Néhány háttérfeladat azután is tovább fut, hogy az Azure CLI visszairányítja Önt a parancssorhoz. A virtuális géppel is SSH és a hibaelhárítási szakaszban ismertetett lépéseket használhatja a cloud-init-naplók megtekintéséhez. 

## <a name="troubleshooting-cloud-init"></a>A cloud-init hibaelhárítása
A virtuális gép van kiépítve, a cloud-init modult keresztül fog futni, és a parancsfájl meghatározott `--custom-data` annak érdekében, hogy a virtuális gép konfigurálása.  Ha hibáinak elhárítása az esetleges hibákat vagy berendezésével a konfigurációból van szüksége, keresse meg a modul neve kell (`disk_setup` vagy `runcmd` például) található a cloud-init napló - **/var/log/cloud-init.log**.

> [!NOTE]
> Nem minden modul hibája eredményezi egy végzetes a cloud-init általános konfigurációs hiba. Például a `runcmd` modult, ha a parancsfájl futása sikertelen, a cloud-init továbbra is jelentést kiépítés sikeres volt, mert a runcmd modul végrehajtása.

A cloud-init naplózási további részletekért tekintse meg a [a cloud-init-dokumentáció](http://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>További lépések
A cloud-init konfigurációs módosítások példákat az alábbi dokumentumokban talál:
 
- [További Linux-felhasználó hozzáadása virtuális Géphez](cloudinit-add-user.md)
- [Az első rendszerindításkor a meglévő csomagokat frissíteni Csomagkezelő futtatása](cloudinit-update-vm.md)
- [Módosítsa a virtuális gép helyi gazdagépnév](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, a konfigurációs fájlokat és a kulcsok beszúrása](tutorial-automate-vm-deployment.md)
