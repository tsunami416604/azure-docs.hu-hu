---
title: Az Azure-beli Linux rendszerű virtuális gépek Cloud-init támogatásának áttekintése
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
ms.openlocfilehash: d372b94ac0df4cef3c43fab10686e9bf20633bfe
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034250"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Cloud-init támogatás az Azure-beli virtuális gépekhez
Ez a cikk ismerteti a [Cloud-init](https://cloudinit.readthedocs.io) számára elérhető támogatást a virtuális gép (VM) vagy virtuálisgép-méretezési csoportok konfigurálásához az Azure üzembe helyezési idején. Ezek a felhő-init parancsfájlok az első rendszerindítás során futnak az Azure-beli erőforrások kiépítés után.  

## <a name="cloud-init-overview"></a>A cloud-init áttekintése
A [cloud-init](https://cloudinit.readthedocs.io) egy széles körben használt módszer a Linux rendszerű virtuális gépek első indításkor való testreszabásához. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. A cloud-init nevezzük az első rendszerindítás során, mert nincsenek további lépéseket vagy szükséges ügynökök a alkalmazni a konfigurációt.  További információt a megfelelő formázása a `#cloud-config` fájlokat, tekintse meg a [a cloud-init dokumentációs oldalának](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` fájlok Base64 kódolású szöveges fájlok.

A cloud-init különböző disztribúciókon is működik. Például nem kell az **apt-get install** vagy a **yum install** használatával telepítenie a csomagokat. Ehelyett megadhatja a telepítendő csomagok listáját. A cloud-init automatikusan a natív csomagkezelő eszközt használja a kiválasztott disztribúcióhoz.

Aktívan dolgozunk a támogatott Linux disztribúció partnereink ahhoz, hogy engedélyezve van a cloud-init lemezkép érhető el az Azure Marketplace-en. Ezek a lemezképek a felhő-init üzembe helyezések és konfigurációk zökkenőmentesen működnek a virtuális gépekkel és a virtuálisgép-méretezési csoportokkal. Az alábbi táblázat ismerteti az aktuális engedélyezve van a cloud-init lemezképek rendelkezésre állás az Azure platformon:

| Közzétevő | Ajánlat | SKU | Verzió | a cloud-init kész |
|:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |18.04-LTS |Legújabb |igen | 
|Canonical |UbuntuServer |16.04-LTS |Legújabb |igen | 
|Canonical |UbuntuServer |14.04.5-LTS |Legújabb |igen |
|CoreOS |CoreOS |Stable |Legújabb |igen |
|OpenLogic 7,7 |CentOS |7-CI |7.7.20190920 |előzetes verzió |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |igen |
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 |előzetes verzió |
    
Jelenleg Azure Stack nem támogatja a RHEL 7. x és CentOS 7. x kiépítési folyamatát a Cloud-init használatával.

* A RHEL 7,6, a Cloud-init csomag esetében a támogatott csomag a következő: *18.2-1. el7_6.2* 
* A RHEL 7,7 (előzetes verzió), a Cloud-init csomag esetében az előzetes csomag a következő: *18,5 -3. el7*
* A CentOS 7,7 (előzetes verzió), a Cloud-init csomag esetében az előzetes csomag a következő: *18,5 -3. el7. CentOS*

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Mi a különbség a Cloud-init és a Linux-ügynök (WALA) között?
A WALA egy Azure platform-specifikus ügynök, amely virtuális gépek kiépítésére és konfigurálására, valamint az Azure-bővítmények kezelésére szolgál. A virtuális gépeknek a Linux-ügynök helyett a Cloud-init használatára való konfigurálásának feladatát fejlesztjük annak érdekében, hogy a meglévő felhő-init-ügyfelek használhassák a jelenlegi Felhőbeli init-parancsfájlokat.  Ha meglévő beruházásai vannak a Cloud-init parancsfájlokban a linuxos rendszerek konfigurálásához, **nincs szükség további beállításokra** . 

Ha nem tartalmazza az Azure CLI `--custom-data` kapcsolót a kiépítési időpontban, a WALA a virtuális gép kiépítéséhez szükséges minimális virtuálisgép-kiépítési paramétereket, valamint az alapértelmezett beállításokkal hajtja végre a telepítést.  Ha a Cloud-init `--custom-data` kapcsolóra hivatkozik, akkor az egyéni adataiban (egyéni beállítások vagy teljes parancsfájl) felülbírálja a WALA alapértelmezett értékeit. 

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
A fájlból való kilépéshez nyomja le az `ctrl-X` gombot, írja be a `y` parancsot a fájl mentéséhez, majd a `enter` gombra kattintva erősítse meg a fájl nevét kilépéskor.

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
A virtuális gép üzembe helyezése után a Cloud-init a virtuális gép konfigurálásához a `--custom-data`ban definiált összes modulon és parancsfájlon keresztül fut.  Ha a konfiguráció hibáit vagy kihagyásait szeretné elhárítani, keresse meg a modul nevét (`disk_setup` vagy `runcmd` például) a Cloud-init log-helyen a **/var/log/Cloud-init.log**-ben.

> [!NOTE]
> Nem minden modul meghibásodása végzetes felhő-init általános konfigurációs hibát eredményez. Ha például az `runcmd` modult használja, ha a parancsfájl meghibásodik, a Cloud-init továbbra is jelentést készít a kiépítés sikerességéről, mert a runcmd modul végre lett hajtva.

A Cloud-init naplózással kapcsolatos további részletekért tekintse meg a [Cloud-init dokumentációját](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) . 

## <a name="next-steps"></a>Következő lépések
A Cloud-init példákat a konfigurációs változásokról a következő dokumentumokban talál:
 
- [További linuxos felhasználó hozzáadása egy virtuális géphez](cloudinit-add-user.md)
- [Csomagkezelő futtatása a meglévő csomagok frissítéséhez az első rendszerindításkor](cloudinit-update-vm.md)
- [Virtuális gép helyi gazdagépének módosítása](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, konfigurációs fájlok frissítése és kulcsok behelyezése](tutorial-automate-vm-deployment.md)
