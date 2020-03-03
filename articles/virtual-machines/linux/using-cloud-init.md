---
title: Az Azure-beli Linux rendszerű virtuális gépek Cloud-init támogatásának áttekintése
description: A Cloud-init képességeinek áttekintése a virtuális gép konfigurálásához az Azure-ban kiépített időpontban.
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
ms.openlocfilehash: 0309d9a794a978c736ffc4689c46565ee8fb5b00
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226688"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Cloud-init támogatás az Azure-beli virtuális gépekhez
Ez a cikk ismerteti a [Cloud-init](https://cloudinit.readthedocs.io) számára elérhető támogatást a virtuális gép (VM) vagy virtuálisgép-méretezési csoportok konfigurálásához az Azure üzembe helyezési idején. Ezek a Cloud-init konfigurációk az első rendszerindítás során futnak az Azure-erőforrások kiépítése után.  

A virtuális gépek üzembe helyezése az a folyamat, amelyben az Azure leállítja a virtuális gépet, így például az állomásnév, a Felhasználónév, a jelszó stb., és elérhetővé teheti őket a virtuális gép számára, ahogy elindul. A "kiépítési ügynök" felhasználja ezeket az értékeket, konfigurálja a virtuális gépet, és visszaküldi a jelentést, amikor a művelet befejeződött. 

Az Azure két üzembe helyezési ügynököt támogat a [Cloud-init](https://cloudinit.readthedocs.io)és az [Azure Linux Agent (Wala)](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux)számára.

## <a name="cloud-init-overview"></a>Cloud-init – áttekintés
a [Cloud-init](https://cloudinit.readthedocs.io) egy széles körben használt módszer a Linux RENDSZERű virtuális gépek első indításakor való testre szabására. A cloud-init használatával csomagokat telepíthet és fájlokat írhat, vagy beállíthatja a felhasználókat és a biztonságot. A cloud-init nevezzük az első rendszerindítás során, mert nincsenek további lépéseket vagy szükséges ügynökök a alkalmazni a konfigurációt.  A `#cloud-config`-fájlok és más adatbevitelek megfelelő formázásáról további információt a [Cloud-init dokumentációs webhelyén](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)talál.  `#cloud-config` a fájlok Base64 kódolású szövegfájlok.

a Cloud-init a disztribúciók között is működik. Például nem kell az **apt-get install** vagy a **yum install** használatával telepítenie a csomagokat. Ehelyett megadhatja a telepítendő csomagok listáját. a Cloud-init automatikusan a natív csomagkezelő eszközt használja a kiválasztott disztribúcióhoz.

Aktívan dolgozunk a támogatott Linux disztribúció partnereink ahhoz, hogy engedélyezve van a cloud-init lemezkép érhető el az Azure Marketplace-en. Ezek a lemezképek a felhő-init üzembe helyezések és konfigurációk zökkenőmentesen működnek a virtuális gépekkel és a virtuálisgép-méretezési csoportokkal. Kezdetben a támogatott Linux-disztribúciós partnerekkel és felsőbb réteggel együttműködve biztosítjuk, hogy a Cloud-init functions az Azure-beli operációs rendszerrel, a csomagok frissítése és nyilvánosan elérhetővé tétele a disztribúciós csomag adattárakban történik. 

A Cloud-init két fázisban érhető el az Azure-beli támogatott Linux-disztribúciós operációs rendszerhez, a csomagok támogatásához, majd a rendszerkép támogatásához:
* a Cloud-init csomag támogatása az Azure-ban olyan dokumentumokon, amelyeket a Cloud-init csomagok már támogatnak vagy előzetes verzióként, így ezeket a csomagokat az operációs rendszer egyéni rendszerképében is használhatja.
* "rendszerkép-felhő – init Ready" dokumentumok, ha a rendszerkép már konfigurálva van a Cloud-init használatára.


### <a name="canonical"></a>Canonical
| Közzétevő/verzió| Ajánlat | SKU | Verzió | rendszerkép-felhő – init Ready | Cloud-init csomag támogatása az Azure-ban|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical 18,04 |UbuntuServer |18.04-LTS |Legújabb |igen | igen |
|Canonical 16,04|UbuntuServer |16.04-LTS |Legújabb |igen | igen |
|Canonical 14,04|UbuntuServer |14.04.5-LTS |Legújabb |igen | igen |

### <a name="rhel"></a>RHEL
| Közzétevő/verzió | Ajánlat | SKU | Verzió | rendszerkép-felhő – init Ready | Cloud-init csomag támogatása az Azure-ban|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |igen | igen – a csomag verziószámának támogatása: *18.2-1. el7_6.2*|
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 | Igen (vegye figyelembe, hogy ez egy előnézeti kép, és ha az összes RHEL 7,7-lemezkép támogatja a Cloud-init-et, akkor a rendszer a 2020-es Mid-t fogja törölni, és értesítést kap | igen – a csomag verziójának támogatása: *18,5 -3. el7*|
|RedHat 7,7 |RHEL |7 – NYERS | n/a| nem a rendszerkép frissítései a február 2020| igen – a csomag verziójának támogatása: *18,5 -3. el7*|
|RedHat 7,7 |RHEL |7-LVM | n/a| nem a rendszerkép frissítései a február 2020| igen – a csomag verziójának támogatása: *18,5 -3. el7*|
|RedHat 7,7 |RHEL |7,7 | n/a| nem a rendszerkép frissítései a február 2020 | igen – a csomag verziójának támogatása: *18,5 -3. el7*|
|RedHat 7,7 |RHEL – BYOS | RHEL – lvm77 | n/a|nem a rendszerkép frissítései a február 2020  | igen – a csomag verziójának támogatása: *18,5 -3. el7*|

### <a name="centos"></a>CentOS

| Közzétevő/verzió | Ajánlat | SKU | Verzió | rendszerkép-felhő – init Ready | Cloud-init csomag támogatása az Azure-ban|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7,7 |CentOS |7-CI |7.7.20190920 |Igen (vegye figyelembe, hogy ez egy előnézeti kép, és ha az összes CentOS 7,7-lemezkép támogatja a Cloud-init-t, a rendszer a 2020-es Mid-t fogja törölni, és értesítést kap) | igen – a csomag verziójának támogatása: *18,5 -3. el7. CentOS*|

* CentOS 7,7 lemezképek, amelyek a Cloud-init engedélyezve lesznek, itt frissíthető február 2020 

### <a name="oracle"></a>Oracle

| Közzétevő/verzió | Ajánlat | SKU | Verzió | rendszerkép-felhő – init Ready | Cloud-init csomag támogatása az Azure-ban|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7,7 |Oracle-Linux |77 – CI |7.7.01| előzetes rendszerkép (Megjegyzés: ez egy előzetes rendszerkép, és az összes Oracle 7,7-lemezkép támogatja a Cloud-2020 init-t | nem, az előzetes verzióban a csomag a következőket eredményezi: *18,5-3.0.1. el7*

### <a name="debian--suse-sles"></a>Debian & SuSE SLES
Jelenleg a támogatás előzetes verziójával dolgozunk, a frissítések várhatóan február és március 2020.

Jelenleg Azure Stack támogatni fogja a Cloud-init-kompatibilis lemezképek kiépítési folyamatát.


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Mi a különbség a Cloud-init és a Linux-ügynök (WALA) között?
A WALA egy Azure platform-specifikus ügynök, amely virtuális gépek kiépítésére és konfigurálására, valamint az [Azure-bővítmények](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)kezelésére szolgál. 

A virtuális gépeknek a Linux-ügynök helyett a Cloud-init használatára való konfigurálásának feladatát fejlesztjük annak érdekében, hogy a meglévő felhő-init-ügyfelek használhassák a jelenlegi felhőalapú init-parancsfájlokat, vagy új ügyfeleket használjanak a Cloud-init konfigurációs funkcióinak kihasználásához. Ha meglévő beruházásai vannak a Cloud-init parancsfájlokban a linuxos rendszerek konfigurálásához, **nincs szükség további beállításokra** a Cloud-init folyamat engedélyezéséhez. 

a Cloud-init nem tudja feldolgozni az Azure-bővítményeket, ezért a lemezképben továbbra is szükség van a bővítmények feldolgozására, de a kiépítési kód le lesz tiltva, a támogatott Linux-disztribúciók által a Cloud-init által kiépített lemezképek esetében. a telepítése és a telepítése megfelelően történik.

Virtuális gép létrehozásakor, ha nem tartalmazza az Azure CLI `--custom-data` kapcsolót a kiépítési időpontban, a Cloud-init vagy a WALA a virtuális gép kiépítéséhez szükséges minimális virtuálisgép-kiépítési paramétereket veszi igénybe, és az alapértelmezett beállításokkal végzi el a telepítést.  Ha a `--custom-data` kapcsolóval hivatkozik a Cloud-init konfigurációra, a virtuális gép indításakor a felhő-init szolgáltatásban az egyéni adataiban található beállítások is elérhetők lesznek.

a virtuális gépekre alkalmazott felhő-init konfigurációk nem rendelkeznek időbeli korlátozásokkal, és nem eredményezi, hogy a központi telepítés időtúllépés miatt sikertelen lesz. Ez nem igaz a WALA esetében, ha megváltoztatja a WALA alapértelmezett értékeit az egyéni adat feldolgozásához, nem lépheti túl a virtuális gépek kiépítési idejének teljes 40mins, ha igen, a virtuális gép létrehozása sikertelen lesz.

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
 
