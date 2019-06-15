---
title: Az OpenShift Container Platform önállóan felügyelt Piactéri ajánlat az Azure-beli üzembe helyezése |} A Microsoft Docs
description: Telepítse az OpenShift Container Platform önállóan felügyelt Piactéri ajánlat az Azure-ban.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 9b981924dcaf715dd1d05d452b756a40b63f8dac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233096"
---
# <a name="configure-prerequisites"></a>Előfeltételek konfigurálása

Mielőtt a Marketplace-ajánlat használatával helyezhet üzembe önállóan felügyelt OpenShift Tárolóplatform-fürtöt az Azure-ban, néhány előfeltételt kell konfigurálni.  Olvassa el a [OpenShift előfeltételei](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-prerequisites) cikk utasításait követve hozzon létre egy ssh kulcs (nélkül egy hozzáférési kódot), az Azure key vault, a key vault titkos kulcsot és egy egyszerű szolgáltatást.

 
## <a name="deploy-using-the-marketplace-offer"></a>Üzembe helyezés a Marketplace-ajánlat

Azure önállóan felügyelt OpenShift Tárolóplatform-fürt üzembe helyezése a legegyszerűbb módja az, hogy használja a [Azure Marketplace-ajánlat](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.openshift-container-platform?tab=Overview).

Ez a lehetőség akkor a legegyszerűbb, de azt is korlátozott testreszabási lehetőségeket. A Piactéri ajánlat OpenShift Tárolóplatform 3.11.82 telepíti, és az alábbi konfigurációs lehetőségeket tartalmazza:

- **Fő csomópontok**: Írja be a három (3) fő csomópontok konfigurálható példánnyal.
- **Infrastruktúra csomópontok**: Írja be a három (3) infrastruktúra csomópontok konfigurálható példánnyal.
- **Csomópontok**: A szám (1 – 9) között csomópontok és a példány típusát is konfigurálható.
- **Lemez típusa**: Felügyelt lemezeket használnak.
- **Hálózatkezelés**: Támogatja az új vagy meglévő hálózati és egyéni CIDR-tartományt.
- **CNS**: CNS engedélyezhető.
- **Metrikák**: Hawkular metrikákat is engedélyezhetők.
- **Naplózás**: EFK naplózás is engedélyezhető.
- **Az Azure Felhőszolgáltató**: Alapértelmezés szerint engedélyezve lehet letiltani.

Az Azure portal bal felső sarokban kattintson **erőforrás létrehozása**, adja meg a "openshift tárolóplatform" szót a keresőmezőbe, és nyomja le az ENTER billentyűt.

   ![Új erőforrás-keresés](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

Az eredmények lapon nyílik meg, **Red Hat OpenShift Container Platform Self-Managed** a listában. 

   ![Új erőforrás-keresési eredmény](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Kattintson az ajánlat az ajánlat a részletek megtekintéséhez. Ez az ajánlat üzembe helyezéséhez kattintson **létrehozás**. Adja meg a szükséges paramétereket, a felhasználói felületen jelenik meg. Az első képernyője a **alapjai** panelen.

   ![Az ajánlat címe lap](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Alapvető beállítások**

Segítséget a bemeneti paraméterek egyikét, mutasson a ***i*** a paraméter neve mellett.

Adja meg az értékeket a bemeneti paraméterek, és kattintson a **OK**.

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| Virtuális gép rendszergazdai felhasználóneve | A rendszergazda felhasználó összes Virtuálisgép-példányt létrehozni |
| SSH Public Key for Admin User | Nyilvános SSH-kulcs VM - szolgáltatásba való bejelentkezéshez használt jelszó nem lehet |
| Előfizetés | Azure-előfizetés-fürt üzembe helyezése |
| Erőforráscsoport | Hozzon létre egy új erőforráscsoportot, vagy válasszon ki egy meglévő üres erőforráscsoportot a fürt erőforrásai |
| Location egység | Azure-régiót, a fürt üzembe helyezése |

   ![Az ajánlat alapvető beállítások panel](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Infrastruktúra-beállításokat**

Adja meg az értékeket a bemeneti paraméterek, és kattintson a **OK**.

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| OCP fürt nevének előtagja | A fürt csomópontjaihoz tartozó gazdagépnevek konfigurálásához használt előtag. 1 – 20 karakter |
| Fő csomópont mérete | Fogadja el az alapértelmezett virtuális gép méretét, vagy kattintson a **méretének módosítása** jelölje be egy másik Virtuálisgép-méretet.  Válassza ki a terhelésnek megfelelő virtuális gép mérete |
| Infrastruktúra-csomópont mérete | Fogadja el az alapértelmezett virtuális gép méretét, vagy kattintson a **méretének módosítása** jelölje be egy másik Virtuálisgép-méretet.  Válassza ki a terhelésnek megfelelő virtuális gép mérete |
| Alkalmazás-csomópontok száma | Fogadja el az alapértelmezett virtuális gép méretét, vagy kattintson a **méretének módosítása** jelölje be egy másik Virtuálisgép-méretet.  Válassza ki a terhelésnek megfelelő virtuális gép mérete |
| Alkalmazás csomópont mérete | Fogadja el az alapértelmezett virtuális gép méretét, vagy kattintson a **méretének módosítása** jelölje be egy másik Virtuálisgép-méretet.  Válassza ki a terhelésnek megfelelő virtuális gép mérete |
| Megerősített gazdagép mérete | Fogadja el az alapértelmezett virtuális gép méretét, vagy kattintson a **méretének módosítása** jelölje be egy másik Virtuálisgép-méretet.  Válassza ki a terhelésnek megfelelő virtuális gép mérete |
| Új vagy meglévő virtuális hálózat | Hozzon létre egy új virtuális hálózatot (alapértelmezett), vagy használjon egy meglévő virtuális hálózat |
| Válassza ki az alapértelmezett CIDR-beállítások, vagy testre szabhatja a IP-címtartomány (CIDR) | Fogadja el az alapértelmezett CIDR-tartományt, vagy válasszon **egyéni IP-címtartomány** , és adja meg egyéni CIDR-adatait.  Alapértelmezett beállítások hoz létre virtuális hálózatok közötti, 10.0.0.0/14, 10.1.0.0/16, infrastruktúra-főkiszolgálói alhálózat CIDR 10.2.0.0/16 az alhálózatot, és a számítási és cns alhálózat 10.3.0.0/16 |
| Key Vault-erőforráscsoport neve | Az erőforráscsoport, amely tartalmazza a kulcstároló nevét |
| Kulcstartó neve | A Key Vault, amely tartalmazza a titkos kulcsnak a nevét a ssh titkos kulcsot.  Csak alfanumerikus karaktereket és kötőjeleket tartalmazhat engedélyezettek, valamint 3 – 24 karakter hosszúságú lehet |
| Název tajného kódu | A titkos kulcsot tartalmazó nevére az ssh titkos kulcsot.  Csak alfanumerikus karaktereket és kötőjeleket tartalmazhat engedélyezettek |

   ![Az ajánlat infrastruktúra panel](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Méret módosítása**

Egy másik virtuális gép méretének kiválasztásához kattintson ***méretének módosítása***.  A virtuális gép kiválasztása ablak nyílik meg.  Válassza ki, majd kattintson a Virtuálisgép-méretet **kiválasztása**.

   ![Válassza ki a virtuális gép mérete](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Existing Virtual Network**

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| Meglévő virtuális hálózat neve | A meglévő virtuális hálózat neve |
| Fő csomópontok alhálózat neve | Fő csomópontok meglévő alhálózat neve.  Kell tartalmaznia legalább 16 IP-címet, és hajtsa végre az RFC 1918 |
| Alhálózat neve az infrastruktúra csomópontok | Neve a meglévő alhálózatot infra csomópontok.  Legalább 32 IP-címeket tartalmaz, és hajtsa végre az RFC 1918 van szükség |
| Alhálózat neve cns és a számítási csomópontok | Meglévő alhálózat neve cns és a számítási csomópontok.  Legalább 32 IP-címeket tartalmaz, és hajtsa végre az RFC 1918 van szükség |
| A meglévő virtuális hálózatot az erőforráscsoport | A meglévő Vnetet tartalmazó erőforráscsoport neve |

   ![Ajánlat infrastruktúra meglévő virtuális hálózat](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Egyéni IP-címtartomány**

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| A virtuális hálózat címtartománya | A virtuális hálózat címtere CIDR egyéni |
| A fő csomópontot tartalmazó alhálózat címtartománya | Egyéni CIDR fő alhálózat |
| Az infrastruktúra-csomópontokat tartalmazó alhálózat címtartománya | Infrastruktúra-alhálózat CIDR egyéni |
| A számítási és cns csomópontokat tartalmazó alhálózat címtartománya | A számítási és cns csomópontok egyéni CIDR |

   ![Ajánlat infrastruktúra egyéni IP-címtartomány](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform**

Adja meg az értékeket a bemeneti paramétereket, és kattintson a **OK**

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| OpenShift Admin User Password | A kezdeti OpenShift-felhasználó jelszavát.  Ez a felhasználó is lesz a fürt rendszergazdája |
| OpenShift rendszergazdai jelszó megerősítése | Az OpenShift rendszergazdai jelszó megerősítése |
| Red Hat-előfizetés-kezelő felhasználó neve | Felhasználónév hozzáférhetnek a Red Hat-előfizetésüket vagy a szervezet azonosítója.  Ez a hitelesítő adat az RHEL-példányt az előfizetés regisztrálásához használatos, és nem tárolja a rendszer a Microsoft vagy a Red Hat által |
| Red Hat-előfizetés-kezelő felhasználói jelszó | A jelszó hozzáférhetnek a Red Hat-előfizetésüket vagy az aktiválási kulcs.  Ez a hitelesítő adat az RHEL-példányt az előfizetés regisztrálásához használatos, és nem tárolja a rendszer a Microsoft vagy a Red Hat által |
| Red Hat-előfizetés-kezelő OpenShift készlet azonosítója | Készlet azonosítója, amely tartalmazza az OpenShift Tárolóplatform jogosultság. Ellenőrizze, hogy az OpenShift Tárolóplatform, a telepítéshez, a fürt elegendő jogosultság |
| Red Hat-előfizetés-kezelő OpenShift készlet azonosítója Broker / fő csomópontok | Tárolókészlet azonosítója, amely tartalmazza az OpenShift Tárolóplatform jogosultságok Broker / fő csomópontok. Győződjön meg arról, hogy a az OpenShift Tárolóplatform, a telepítéshez, a fürt elegendő jogosultság. Ha nem használ broker / fő készlet azonosítója, adja meg a készlet Azonosítóját az alkalmazás csomópont |
| Az Azure felhőalapú szolgáltatók konfigurálása | Az OpenShift Azure Felhőszolgáltató használatára konfigurálja. Ha állandó kötetek használata az Azure-lemez csatolása szükséges.  Alapértelmezett érték az Igen |
| Az Azure AD egyszerű szolgáltatás ügyfél-azonosító GUID | Az Azure AD egyszerű szolgáltatás ügyfél azonosító GUID – más néven az AppID. Csak akkor szükséges, ha konfigurálása Azure Felhőszolgáltató **Igen** |
| Az Azure AD egyszerű szolgáltatás ügyfél-azonosító titkos | Az Azure AD-szolgáltatásnév Azonosítóját titkos Ügyfélkódja. Csak akkor szükséges, ha konfigurálása Azure Felhőszolgáltató **Igen** |
 
   ![Az ajánlat az OpenShift panel](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**További beállítások**

A további beállítások panel lehetővé teszi, hogy a CNS konfigurációjának glusterfs tároláshoz, naplózás, metrikákat és útválasztó Sub tartományhoz.  Az alapértelmezett nem ezek a beállítások bármelyikét telepítik, és fogja használni, az útválasztó altartomány nip.io tesztelési célokra. CNS engedélyezése három további csatlakoztatott adatlemezekkel, glusterfs podok üzemeltető rendelkező telepíti a három további számítási csomópontok.  

Adja meg az értékeket a bemeneti paramétereket, és kattintson a **OK**

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| Konfigurálja a tároló natív tároló (CNS) | Az OpenShift CNS telepíti a fürt, majd engedélyezze tárolóként. Ha az Azure-szolgáltató le van tiltva lesz alapértelmezés szerint |
| Fürt-naplózás beállítása | A fürt EFK naplózási funkció telepíti.  Infrastruktúra megfelelő gazdagép EFK podok csomópontok mérete |
| A fürt metrikák konfigurálása | Az OpenShift fürtbe telepíti a Hawkular metrikákat.  Infrastruktúra megfelelő gazdagép Hawkular metrikák podok csomópontok mérete |
| Alapértelmezett útválasztó-altartomány | Válassza ki a tesztelési vagy éles környezetben a saját altartomány meg egyéni nipio |
 
   ![Az ajánlat kiegészítő panel](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**További beállítások – további paraméterek**

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| (CNS) A csomópont mérete | Fogadja el az alapértelmezett csomópontméret vagy válasszon **méretének módosítása** új virtuális gép méretének kiválasztásához |
| Adja meg az egyéni altartomány | Az útválasztó az OpenShift fürtön keresztül alkalmazások által használandó egyéni útválasztási tartomány.  Ügyeljen arra, hogy a megfelelő helyettesítő karaktert tartalmazó DNS-bejegyzés létrehozása] |
 
   ![További cns ajánlat telepítése](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**összegzés**

Érvényesítési ezen a ponton annak ellenőrzésére, Processzormagok kvótája elegendő ahhoz, hogy a teljes száma a fürthöz kiválasztott virtuális gép üzembe helyezése történik.  Tekintse át a megadott paramétereket.  Ha a bemeneti adatok elfogadható, kattintson a **OK** folytatásához.

   ![Az ajánlat összefoglalás panelje](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Buy**

Erősítse meg a kapcsolattartási adatokat a vásárlás oldalon, és kattintson a **beszerzési** fogadja el a használati feltételeket, és az OpenShift Tárolóplatform-fürt üzembe helyezésének megkezdéséhez.

   ![Az ajánlat beszerzési panel](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Csatlakozzon az OpenShift-fürthöz

Az üzembe helyezést követően a kimeneti szakaszban az üzemelő példány lekérdezni a kapcsolatot. Csatlakozás a böngészőjében az OpenShift konzol használatával a **OpenShift-konzol URL-címe**. Akkor is SSH, a bástyagazdagép. Következő egy példa, ahol a rendszergazda felhasználóneve clusteradmin, a megerősített nyilvános IP-cím DNS teljes Tartományneve pedig bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Használja a [az csoport törlése](/cli/azure/group) paranccsal eltávolítható az erőforráscsoport, az OpenShift fürt, és az összes kapcsolódó erőforrást, amikor azok már nincs szükség van.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>További lépések

- [Üzembe helyezés utáni feladatok](./openshift-post-deployment.md)
- [Az Azure-ban az OpenShift telepítés hibaelhárítása](./openshift-troubleshooting.md)
- [Ismerkedés az OpenShift Tárolóplatform](https://docs.openshift.com/container-platform)

