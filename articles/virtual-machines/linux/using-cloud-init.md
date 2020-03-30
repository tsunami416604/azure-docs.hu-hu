---
title: Linuxos virtuális gépek felhőalapú támogatásának áttekintése az Azure-ban
description: A felhőalapú init-képességek áttekintése a virtuális gépek azure-beli kiépítési idő beállítása kor.
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
ms.date: 01/23/2019
ms.author: danis
ms.openlocfilehash: 1f0395956fa6977be5d1d6f4f4faf06b84c094d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79465039"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>felhőalapú init támogatás virtuális gépekhez az Azure-ban
Ez a cikk ismerteti a támogatást, amely a [cloud-init](https://cloudinit.readthedocs.io) egy virtuális gép (VM) vagy virtuálisgép-méretezési csoportok az Azure-ban kiépítési idő használatával. Ezek a felhőalapú init konfigurációk az azure-beli erőforrások kiépítése után az első rendszerindításkor futnak.  

Virtuálisgép-kiépítés az a folyamat, ahol az Azure átadja a virtuális gép létrehozása paraméterértékeket, például a hostname, felhasználónév, jelszó, stb, és azokat elérhetővé a virtuális gép, ahogy elindul. A "kiépítési ügynök" fogja használni ezeket az értékeket, konfigurálja a virtuális gép, és jelentést tesz vissza, ha elkészült. 

Az Azure két üzembe építési [ügynökfelhő-init](https://cloudinit.readthedocs.io)és az [Azure Linux-ügynök (WALA)](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)támogatja.

## <a name="cloud-init-overview"></a>felhőalapú init – áttekintés
[Cloud-init](https://cloudinit.readthedocs.io) egy széles körben használt megközelítés testre egy Linux virtuális gép, mert elindul az első alkalommal. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. Mivel a cloud-init a kezdeti rendszerindítási folyamat során van meghívva, nincsenek további lépések vagy szükséges ügynökök a konfiguráció alkalmazásához.  A `#cloud-config` fájlok vagy más bemenetek megfelelő formázásáról a [felhőalapú dokumentációs webhelyen olvashat bővebben.](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)  `#cloud-config`fájlok szöveges fájlok kódolt base64.

a felhőalapú init a disztribúciók között is működik. Például nem kell az **apt-get install** vagy a **yum install** használatával telepítenie a csomagokat. Ehelyett megadhatja a telepítendő csomagok listáját. A cloud-init automatikusan a natív csomagkezelő eszközt használja a kiválasztott kivezetéshez.

Aktívan együttműködünk a jóváhagyott Linux disztribúciós partnereinkkel annak érdekében, hogy felhőalapú init-kompatibilis lemezképek álljanak rendelkezésre az Azure piactéren. Ezek a lemezképek teszik a felhő-init központi telepítések és konfigurációk zökkenőmentesen működnek a virtuális gépek és a virtuális gép méretezési készletek. Kezdetben együttműködünk a jóváhagyott Linux disztribúciós partnerekkel és az upstream-el, hogy biztosítsuk a felhőalapú init funkciókat az Azure operációs rendszerével, majd a csomagokat frissítik és nyilvánosan elérhetővé teszik a disztribúciós csomagtárolókban. 

Két szakasza van annak, hogy a felhőalapú init elérhetőlegyen a jóváhagyott Linux disztribúciós operációs rendszer az Azure-on, a csomagtámogatás, majd a rendszerkép-támogatás:
* A "cloud-init csomag támogatása az Azure-ban" dokumentumok, amelyek felhő-init csomagok tól támogatott vagy előzetes verzióban, így használhatja ezeket a csomagokat az operációs rendszer egy egyéni lemezkép.
* "image cloud-init ready" dokumentumok, ha a kép már be van állítva a felhő-init használatára.


### <a name="canonical"></a>Canonical
| Kiadó / Verzió| Ajánlat | SKU | Verzió | kép felhő-init kész | felhőalapú init csomag támogatása az Azure-ban|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Kanonikus 18,04 |UbuntuServer |18.04-LTS |legújabb |igen | igen |
|Kanonikus 16,04|UbuntuServer |16.04-LTS |legújabb |igen | igen |
|Kanonikus 14,04|UbuntuServer |14.04.5-LTS |legújabb |igen | igen |

### <a name="rhel"></a>RHEL
| Kiadó / Verzió | Ajánlat | SKU | Verzió | kép felhő-init kész | felhőalapú init csomag támogatása az Azure-ban|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |igen | igen - támogatás a csomag verzió: *18.2-1.el7_6.2*|
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 | igen (vegye figyelembe, hogy ez egy előnézeti kép, és ha az összes RHEL 7.7 kép támogatja a felhő-init, ez 2020 közepén eltávolításra kerül, értesítést kap) | igen - támogatás a csomag verzió: *18.5-3.el7*|
|RedHat 7,7 |RHEL |7-RAW | n/a| nem - kép frissítések teljes április végén 2020| igen - támogatás a csomag verzió: *18.5-3.el7*|
|RedHat 7,7 |RHEL |7-LVM | n/a| nem - kép frissítések teljes április végéig| igen - támogatás a csomag verzió: *18.5-3.el7*|
|RedHat 7,7 |RHEL |7.7 | n/a| nem - kép frissítések teljes április végéig | igen - támogatás a csomag verzió: *18.5-3.el7*|
|RedHat 7,7 |rhel-byos | rhel-lvm77 | n/a|nem - kép frissítések teljes április végéig  | igen - támogatás a csomag verzió: *18.5-3.el7*|

### <a name="centos"></a>CentOS

| Kiadó / Verzió | Ajánlat | SKU | Verzió | kép felhő-init kész | felhőalapú init csomag támogatása az Azure-ban|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7,7 |CentOS |7-CI |7.7.20190920 |igen (vegye figyelembe, hogy ez egy előnézeti kép, és ha az összes CentOS 7.7 kép támogatja a felhő-init-et, ez 2020 közepén eltávolításra kerül, értesítést kap) | igen - támogatás csomag verzió: *18.5-3.el7.centos*|

* CentOS 7.7 képeket, hogy lesz felhő-init engedélyezve kell frissíteni itt március 2020 

### <a name="oracle"></a>Oracle

| Kiadó / Verzió | Ajánlat | SKU | Verzió | kép felhő-init kész | felhőalapú init csomag támogatása az Azure-ban|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7,7 |Oracle-Linux |77-ci |7.7.01| előnézeti kép (vegye figyelembe, hogy ez egy előnézeti kép, és ha az összes Oracle 7.7 kép támogatja a felhő-init-et, ez 2020 közepén eltávolításra kerül, értesítést kap) | Nem, előzetes verzióban a csomag: *18.5-3.0.1.el7*

### <a name="debian--suse-sles"></a>Debian & SuSE SLES
Jelenleg azon dolgozunk, hogy megtekintsük a támogatást, és 2020 februárjában és márciusában várjuk a frissítéseket.

Jelenleg az Azure Stack támogatja a felhőalapú init-kompatibilis lemezképek kiépítését.


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Mi a különbség a felhő-init és a Linux Agent (WALA)?
A WALA egy Azure-platformspecifikus ügynök, amely a virtuális gépek kiépítésére és konfigurálására, valamint [az Azure-bővítmények kezelésére](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)szolgál. 

Bővítjük azt a feladatot, hogy a virtuális gépeket úgy konfiguráljuk, hogy a Linux-ügynök helyett felhőalapú initet használjanak annak érdekében, hogy a meglévő felhőalapú init-ügyfelek használhassák az aktuális felhőalapú init-parancsfájljaikat, vagy új ügyfeleket, hogy kihasználhassák a felhőalapú init konfigurációs funkciók előnyeit. Ha már rendelkezik a linuxos rendszerek konfigurálására szolgáló felhőinit szkriptekbe történő befektetésekkel, **nincs szükség további beállításokra a** felhőalapú init folyamatengedélyezéséhez. 

a cloud-init nem tudja feldolgozni az Azure-bővítményeket, így a WALA-nak továbbra is szüksége van a lemezképre a bővítmények feldolgozásához, de le kell tiltania a kiépítési kódját, a jóváhagyott Linux disztribúciós lemezképekhez, amelyeket a felhő-init által isznak, wala-val rendelkeznek telepítve van, és megfelelően kell beállítani.

Virtuális gép létrehozásakor, ha nem tartalmazza az `--custom-data` Azure CLI-kapcsoló kiépítésekor, cloud-init vagy WALA veszi a minimális virtuális gép létesítési paraméterek kiépítése a virtuális gép, és töltse ki a központi telepítés az alapértelmezett.  Ha a felhő-init konfigurációa `--custom-data` a kapcsolóval, amit az egyéni adatok tartalmaznak lesz elérhető a felhő-init, amikor a virtuális gép elindul.

A virtuális gépekre alkalmazott felhőalapú init konfigurációk nem rendelkeznek időkorlátokkal, és nem okoznak üzembe helyezési problémákat az időtúllépés miatt. Ez nem igaz a WALA, ha módosítja a WALA alapértelmezett feldolgozása egyéni adatok, nem haladhatja meg a teljes virtuális gép kiépítése időengedményt 40mins, ha igen, a virtuális gép létrehozása sikertelen lesz.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Felhőalapú init-kompatibilis virtuális gép telepítése
Egy felhő-init-kompatibilis virtuális gép üzembe helyezése olyan egyszerű, mint egy felhőalapú init-alapú disztribúcióra való hivatkozás a központi telepítés során.  Linux disztribúció-karbantartók kell választani, hogy engedélyezze és integrálja a felhő-init az alap Azure közzétett képek. Miután megerősítette, hogy a telepíteni kívánt lemezkép felhőalapú init engedélyezve van, használhatja az Azure CLI a lemezkép üzembe helyezéséhez. 

A lemezkép üzembe helyezésének első lépése egy erőforráscsoport létrehozása az [az csoport létrehozása](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
A következő lépés egy fájl létrehozása az aktuális rendszerhéjban, amelynek neve *cloud-init.txt,* és illessze be a következő konfigurációt. Ebben a példában hozza létre a fájlt a Cloud Shell nem a helyi gépen. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor cloud-init.txt` parancsot a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez. Válassza ki #1 a **nanoszerkesztő** használatához. Ügyeljen arra, hogy megfelelően másolja ki a teljes cloud-init-fájlt, különösen az első sort:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Nyomja `ctrl-X` meg a fájlból való kilépéshez írja be `y` a fájlt, és nyomja meg `enter` a fájl nevének megerősítéséhez kilépéskor.

Az utolsó lépés az, hogy hozzon létre egy virtuális gép az [az vm create](/cli/azure/vm) paranccsal. 

A következő példa létrehoz egy *centos74* nevű virtuális gép, és sSH-kulcsokat hoz létre, ha azok még nem léteznek az alapértelmezett kulcshelyen. Ha konkrét kulcsokat szeretné használni, használja az `--ssh-key-value` beállítást.  Használja a `--custom-data` paramétert a cloud-init konfigurációs fájl megadásához. Adja meg a *cloud-init.txt* konfiguráció teljes elérési útját, ha az aktuális munkakönyvtáron kívülre mentette. A következő példa létrehoz egy *centos74*nevű virtuális gép:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

A virtuális gép létrehozása után az Azure CLI a központi telepítésre vonatkozó információkat jeleníti meg. Jegyezze fel a `publicIpAddress` értékét. Ez a cím használható a virtuális gép eléréséhez.  Némi időt vesz igénybe a virtuális gép létrehozása, a csomagok telepítése, és az alkalmazás indításához. Néhány háttérfeladat azután is tovább fut, hogy az Azure CLI visszairányítja Önt a parancssorhoz. SSH-t a virtuális gépbe, és a hibaelhárítási szakaszban ismertetett lépéseket a felhő-init naplók megtekintéséhez. 

## <a name="troubleshooting-cloud-init"></a>A felhőalapú init hibáinak elhárítása
A virtuális gép kiépítése után a cloud-init végigfut az összes modulon és a virtuális gép `--custom-data` konfigurálásához definiált parancsfájlon.  Ha a konfigurációból származó hibákat vagy hiányosságokat el kell hárítania, `runcmd` meg kell keresnie a modul nevét (vagy`disk_setup` például) a **/var/log/cloud-init.log**könyvtárnaplóban.

> [!NOTE]
> Nem minden modulhiba eredményez végzetes felhő-init általános konfigurációs hibát. Például a `runcmd` modul használatával, ha a parancsfájl sikertelen, cloud-init továbbra is jelenti kiépítése sikerült, mert a runcmd modul végrehajtása.

A felhőalapú init naplózásról a [cloud-init dokumentációjában](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) olvashat bővebben. 

## <a name="next-steps"></a>További lépések
A felhőalapú init konfigurációs módosításokra vonatkozó példákat a következő dokumentumokban talál:
 
- [További Linux-felhasználó hozzáadása a virtuális géphez](cloudinit-add-user.md)
- [Csomagkezelő futtatása meglévő csomagok frissítéséhez az első rendszerindításkor](cloudinit-update-vm.md)
- [A virtuális gép helyi állomásnevének módosítása](cloudinit-update-vm-hostname.md) 
- [Alkalmazáscsomag telepítése, konfigurációs fájlok frissítése és kulcsok befecskendezése](tutorial-automate-vm-deployment.md)
 
