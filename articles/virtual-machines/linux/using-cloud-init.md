---
title: Az Azure-beli Linux rendszerű virtuális gépek Cloud-init-támogatásának áttekintése | Microsoft Docs
description: A Cloud-init képességeinek áttekintése Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/11/2019
ms.author: danis
ms.openlocfilehash: b0300dd91876b651015ae78c53dbc1e72bf8dd68
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285693"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Cloud-init támogatás az Azure-beli virtuális gépekhez
Ez a cikk ismerteti a [Cloud-init](https://cloudinit.readthedocs.io) számára elérhető támogatást a virtuális gép (VM) vagy virtuálisgép-méretezési csoportok konfigurálásához az Azure üzembe helyezési idején. Ezek a felhő-init parancsfájlok az első rendszerindítás során futnak az Azure-beli erőforrások kiépítés után.  

## <a name="cloud-init-overview"></a>A cloud-init áttekintése
A [cloud-init](https://cloudinit.readthedocs.io) egy széles körben használt módszer a Linux rendszerű virtuális gépek első indításkor való testreszabásához. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. Mivel a Cloud-init a kezdeti rendszerindítási folyamat során hívásra kerül, nincs szükség további lépésekre vagy ügynökökre a konfiguráció alkalmazásához.  A `#cloud-config` fájlok megfelelő formázásáról további információt a [Cloud-init dokumentációs webhelyén](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)talál.  a `#cloud-config` fájlok Base64 kódolású szövegfájlok.

A cloud-init különböző disztribúciókon is működik. Például nem kell az **apt-get install** vagy a **yum install** használatával telepítenie a csomagokat. Ehelyett megadhatja a telepítendő csomagok listáját. A cloud-init automatikusan a natív csomagkezelő eszközt használja a kiválasztott disztribúcióhoz.

Aktívan dolgozunk a támogatott linuxos disztribúciós partnereinkkel, hogy elérhetők legyenek a Cloud-init-lemezképek az Azure piactéren. Ezek a lemezképek a felhő-init üzembe helyezések és konfigurációk zökkenőmentesen működnek a virtuális gépekkel és a virtuálisgép-méretezési csoportokkal. Az alábbi táblázat az Azure platformon elérhető aktuális Cloud-init-lemezképek elérhetőségét vázolja fel:

| Gyártó/kiadó | Ajánlat | SKU (Cikkszám) | Verzió | Cloud-init Ready |
|:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |18.04-LTS |legutóbbi |igen | 
|Canonical |UbuntuServer |16.04-LTS |legutóbbi |igen | 
|Canonical |UbuntuServer |14.04.5-LTS |legutóbbi |igen |
|CoreOS |CoreOS |Stable |legutóbbi |igen |
|OpenLogic 7,7 |CentOS |7-CI |7.7.20190920 |előzetes verzió |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |igen |
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 |előzetes verzió |
    
Jelenleg Azure Stack nem támogatja a RHEL 7. x és CentOS 7. x kiépítési folyamatát a Cloud-init használatával.

* A RHEL 7,6, a Cloud-init csomag esetében a támogatott csomag a következő: *18.2 -1. el7 _ 6.2* 
* A RHEL 7,7 (előzetes verzió), a Cloud-init csomag esetében az előzetes csomag a következő: *18,5 -3. el7*
* A CentOS 7,7 (előzetes verzió), a Cloud-init csomag esetében az előzetes csomag a következő: *18,5 -3. el7. CentOS*

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Mi a különbség a Cloud-init és a Linux-ügynök (WALA) között?
A WALA egy Azure platform-specifikus ügynök, amely virtuális gépek kiépítésére és konfigurálására, valamint az Azure-bővítmények kezelésére szolgál. A virtuális gépeknek a Linux-ügynök helyett a Cloud-init használatára való konfigurálásának feladatát fejlesztjük annak érdekében, hogy a meglévő felhő-init-ügyfelek használhassák a jelenlegi Felhőbeli init-parancsfájlokat.  Ha meglévő beruházásai vannak a Cloud-init parancsfájlokban a linuxos rendszerek konfigurálásához, **nincs szükség további beállításokra** . 

Ha nem tartalmazza az Azure CLI `--custom-data` kapcsolót a kiépítési időpontban, a WALA a virtuális gép kiépítéséhez szükséges minimális virtuálisgép-kiépítési paramétereket, valamint az alapértelmezett beállításokkal hajtja végre a telepítést.  Ha a Cloud-init `--custom-data` kapcsolóra hivatkozik, bármit is tartalmaz az egyéni adataiban (egyéni beállítások vagy teljes parancsfájl), felülbírálja a WALA alapértelmezett értékeit. 

A virtuális gépek WALA-konfigurációi az idő korlátozásával működnek a virtuális gép maximális kiépítési ideje alatt.  A virtuális gépekre alkalmazott felhő-init konfigurációk nem rendelkeznek időbeli korlátozásokkal, és nem eredményezi, hogy a központi telepítés időtúllépés miatt sikertelen lesz. 

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Cloud-init-alapú virtuális gép üzembe helyezése
A Cloud-init-alapú virtuális gép üzembe helyezése olyan egyszerű, mint a felhő-init engedélyezett terjesztésre való hivatkozás az üzembe helyezés során.  A Linux-alapú terjesztők számára a Cloud-init engedélyezését és integrálását kell választania az alapszintű Azure közzétett lemezképekben. Miután megerősítette, hogy a telepíteni kívánt lemezkép a Cloud-init engedélyezve van, az Azure CLI használatával telepítheti a lemezképet. 

A rendszerkép üzembe helyezésének első lépéseként hozzon létre egy erőforráscsoportot az az [Group Create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
A következő lépés egy, a *Cloud-init. txt* nevű fájl létrehozása a jelenlegi rendszerhéjban, majd a következő konfiguráció beillesztése. Ebben a példában hozza létre a fájlt a Cloud Shell nem a helyi gépen. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor cloud-init.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. A **Nano** Editor használatához válassza a #1 lehetőséget. Ügyeljen arra, hogy megfelelően másolja ki a teljes cloud-init-fájlt, különösen az első sort:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Nyomja le az `ctrl-X` billentyűkombinációt a fájl kilépéséhez, írja be a `y` parancsot a fájl mentéséhez, majd a `enter` gombbal erősítse meg a fájl nevét a kilépéskor.

Utolsó lépésként hozzon létre egy virtuális gépet az az [VM Create](/cli/azure/vm) paranccsal. 

A következő példa létrehoz egy *centos74* nevű virtuális gépet, és SSH-kulcsokat hoz létre, ha azok még nem léteznek az alapértelmezett kulcs helyén. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  Használja a `--custom-data` paramétert a cloud-init konfigurációs fájl megadásához. Adja meg a *cloud-init.txt* konfiguráció teljes elérési útját, ha az aktuális munkakönyvtáron kívülre mentette. A következő példa egy *centos74*nevű virtuális gépet hoz létre:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

A virtuális gép létrehozása után az Azure CLI az üzemelő példányra vonatkozó információkat jeleníti meg. Jegyezze fel a `publicIpAddress` értékét. Ez a cím használható a virtuális gép eléréséhez.  Időbe telik a virtuális gép létrehozása, a telepítendő csomagok és az alkalmazás elindítása. Néhány háttérfeladat azután is tovább fut, hogy az Azure CLI visszairányítja Önt a parancssorhoz. A felhő-init naplók megtekintéséhez SSH-t használhat a virtuális gépre, és a hibaelhárítás szakaszban leírt lépéseket követve tekintheti meg. 

## <a name="troubleshooting-cloud-init"></a>A Cloud-init hibaelhárítása
A virtuális gép üzembe helyezése után a Cloud-init a `--custom-data` által meghatározott összes modulon és parancsfájlon fut a virtuális gép konfigurálása érdekében.  Ha a konfiguráció hibáit vagy kihagyásait szeretné elhárítani, keresse meg a modul nevét (`disk_setup` vagy `runcmd` például) a Cloud-init log-helyen a **/var/log/Cloud-init.log**-ben.

> [!NOTE]
> Nem minden modul meghibásodása végzetes felhő-init általános konfigurációs hibát eredményez. Ha például az `runcmd` modult használja, ha a parancsfájl meghibásodik, a Cloud-init továbbra is jelentést készít a kiépítés sikerességéről, mert a runcmd modul végre lett hajtva.

A Cloud-init naplózással kapcsolatos további részletekért tekintse meg a [Cloud-init dokumentációját](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) . 

## <a name="next-steps"></a>Következő lépések
A Cloud-init példákat a konfigurációs változásokról a következő dokumentumokban talál:
 
- [További linuxos felhasználó hozzáadása egy virtuális géphez](cloudinit-add-user.md)
- [Csomagkezelő futtatása a meglévő csomagok frissítéséhez az első rendszerindításkor](cloudinit-update-vm.md)
- [Virtuális gép helyi gazdagépének módosítása](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, konfigurációs fájlok frissítése és kulcsok behelyezése](tutorial-automate-vm-deployment.md)
