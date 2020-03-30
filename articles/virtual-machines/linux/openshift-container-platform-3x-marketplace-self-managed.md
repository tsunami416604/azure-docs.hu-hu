---
title: OpenShift Container Platform 3.11 saját felügyelt piactéri ajánlat üzembe helyezése az Azure-ban
description: OpenShift Container Platform 3.11 saját felügyelt piactéri ajánlat üzembe helyezése az Azure-ban.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 235efd746562ea4bd52b9cb57da0d8165d60de02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561320"
---
# <a name="configure-prerequisites"></a>Előfeltételek konfigurálása

Mielőtt a Marketplace-ajánlat használatával üzembe helyezne egy saját felügyelt OpenShift Container Platform 3.11-es fürtöt az Azure-ban, néhány előfeltételt kell konfigurálni.  Olvassa el az [OpenShift előfeltételeiről](https://docs.microsoft.com/azure/virtual-machines/linux/openshift-container-platform-3x-prerequisites) szóló cikket az ssh kulcs (jelszó nélkül), az Azure key vault, a key vault titkos kulcsa és egy egyszerű szolgáltatás létrehozásához.

 
## <a name="deploy-using-the-marketplace-offer"></a>Üzembe helyezés a Marketplace-ajánlat használatával

A saját kezelésű OpenShift Container Platform 3.11-es fürt Azure-ba való üzembe helyezésének legegyszerűbb módja az [Azure Marketplace-ajánlat](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)használata.

Ez a lehetőség a legegyszerűbb, de korlátozott testreszabási képességekkel is rendelkezik. A Marketplace-ajánlat telepíti az OpenShift Container Platform 3.11.82-es platformot, és a következő konfigurációs lehetőségeket tartalmazza:

- **Fő csomópontok:** Három (3) főcsomópont konfigurálható példánytípussal.
- **Infracsomópontok**: Három (3) Infra csomópont konfigurálható példánytípussal.
- **Csomópontok**: A csomópontok száma (1 és 9 között) és a példány típusa konfigurálható.
- **Lemeztípus**: Felügyelt lemezek használatosak.
- **Hálózat :** Új vagy meglévő hálózati és egyéni CIDR-tartomány támogatása.
- **CnS**: CNS engedélyezhető.
- **Mérőszámok**: Hawkular metrikák engedélyezhetők.
- **Naplózás**: Az EFK naplózás engedélyezhető.
- **Azure Cloud Provider**: Alapértelmezés szerint engedélyezve van, letiltható.

Az Azure Portal bal felső részén kattintson az **Erőforrás létrehozása**elemre, írja be a "openshift container platform" kifejezést a keresőmezőbe, és nyomja meg az Enter billentyűt.

   ![Új erőforrás-keresés](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

Az Eredmények lap a **Red Hat OpenShift Container Platform 3.11 saját irányítású** platformmal nyílik meg a listában. 

   ![Új erőforrás-keresési eredmény](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

Kattintson az ajánlatra az ajánlat részleteinek megtekintéséhez. Az ajánlat üzembe helyezéséhez kattintson a **Létrehozás gombra.** Megjelenik a szükséges paraméterek megadásához szükséges felhasználói felület. Az első képernyő az **Alapok** panel.

   ![Ajánlat címoldala](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**Alapvető beállítások**

Ha segítségre van szüksége a bemeneti paraméterek bármelyikéhez, vigye az egérmutatót a paraméterneve melletti ***i*** fölé.

Adja meg a bemeneti paraméterek értékeit, és kattintson az **OK**gombra.

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| Virtuális gép rendszergazdai felhasználóneve | Az összes virtuálisgép-példányon létrehozandó rendszergazdai felhasználó |
| SSH nyilvános kulcs a rendszergazdai felhasználók számára | A virtuális gépbe való bejelentkezéshez használt SSH nyilvános kulcsnem tartalmazhat jelszót |
| Előfizetés | Azure-előfizetés a fürt üzembe helyezéséhez |
| Erőforráscsoport | Új erőforráscsoport létrehozása vagy meglévő üres erőforráscsoport kijelölése fürterőforrásokhoz |
| Hely | Azure-régió a fürt üzembe helyezéséhez |

   ![Ajánlat alapjai penge](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**Infrastruktúra beállításai**

Adja meg a bemeneti paraméterek értékeit, és kattintson az **OK**gombra.

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| OCP-fürt névelőtagja | Az állomásnevek konfigurálásához használt fürtelőtag az összes csomóponthoz. 1 és 20 karakter között |
| Fő csomópont mérete | Fogadja el az alapértelmezett virtuális gép méretét, vagy kattintson a **Méret módosítása** gombra egy másik virtuális gép méretének kiválasztásához.  Válassza ki a megfelelő virtuális gépméretet a munkaterheléshez |
| Infrastruktúracsomópont mérete | Fogadja el az alapértelmezett virtuális gép méretét, vagy kattintson a **Méret módosítása** gombra egy másik virtuális gép méretének kiválasztásához.  Válassza ki a megfelelő virtuális gépméretet a munkaterheléshez |
| Alkalmazáscsomópontok száma | Fogadja el az alapértelmezett virtuális gép méretét, vagy kattintson a **Méret módosítása** gombra egy másik virtuális gép méretének kiválasztásához.  Válassza ki a megfelelő virtuális gépméretet a munkaterheléshez |
| Alkalmazáscsomópont mérete | Fogadja el az alapértelmezett virtuális gép méretét, vagy kattintson a **Méret módosítása** gombra egy másik virtuális gép méretének kiválasztásához.  Válassza ki a megfelelő virtuális gépméretet a munkaterheléshez |
| Bástyaállomás mérete | Fogadja el az alapértelmezett virtuális gép méretét, vagy kattintson a **Méret módosítása** gombra egy másik virtuális gép méretének kiválasztásához.  Válassza ki a megfelelő virtuális gépméretet a munkaterheléshez |
| Új vagy meglévő virtuális hálózat | Új virtuális hálózat létrehozása (alapértelmezett) vagy meglévő virtuális hálózat használata |
| Válassza az Alapértelmezett CIDR-beállítások lehetőséget, vagy szabja testre az IP-tartományt (CIDR) | Fogadja az alapértelmezett CIDR-tartományokat vagy az **Egyéni IP-tartomány** kiválasztása beállítást, és adja meg az egyéni CIDR-adatokat.  Az alapértelmezett beállítások 10.0.0.0/14-es CIDR-rel, 10.1.0.0/16 fő alhálózattal, 10.2.0.0/16-os infraalhálózattal, valamint 10.3.0.0/16-os számítási és knalhálózat-alhálózattal hoznak létre |
| Key Vault erőforráscsoport neve | A Key Vaultot tartalmazó erőforráscsoport neve |
| A kulcstartó neve | A kulcstartó nevét, amely az ssh titkos kulcsot tartalmazza.  Csak alfanumerikus karakterek és kötőjelek megengedettek, és 3 és 24 karakter között lehetnek. |
| Titkos név | Az ssh titkos kulcsot tartalmazó titkos kulcs neve.  Csak alfanumerikus karakterek és kötőjelek engedélyezettek |

   ![Infrastruktúra-panel ajánlata](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**Méret módosítása**

Másik virtuális gépméret kiválasztásához kattintson a ***Méret módosítása gombra.***  Megnyílik a virtuális gép kijelölési ablaka.  Jelölje ki a kívánt virtuális gép méretet, és kattintson a **Kijelölés gombra.**

   ![Virtuális gép méretének kiválasztása](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**Meglévő virtuális hálózat**

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| Meglévő virtuális hálózat neve | A meglévő virtuális hálózat neve |
| Fő csomópontok alhálózati neve | A főcsomópontok meglévő alhálózatának neve.  Legalább 16 IP-címet kell tartalmaznia, és követnie kell az RFC 1918-at |
| Infracsomópontok alhálózati neve | Az infracsomópontok meglévő alhálózatának neve.  Legalább 32 IP-címet kell tartalmaznia, és követnie kell az RFC 1918-at |
| Számítási és cns-csomópontok alhálózati neve | A számítási és cns-csomópontok meglévő alhálózatának neve.  Legalább 32 IP-címet kell tartalmaznia, és követnie kell az RFC 1918-at |
| A meglévő virtuális hálózat erőforráscsoportja | A meglévő virtuális hálózatot tartalmazó erőforráscsoport neve |

   ![Meglévő virtuális hálózat ajánlata](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**Egyéni IP-tartomány**

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| A virtuális hálózat címtartománya | Egyéni CIDR a virtuális hálózathoz |
| A főcsomópontokat tartalmazó alhálózat címtartománya | Egyéni CIDR a fő alhálózathoz |
| Az infrastruktúra-csomópontokat tartalmazó alhálózat címtartománya | Egyéni CIDR infrastruktúra-alhálózathoz |
| A számítási és cns-csomópontokat tartalmazó alhálózat címtartománya | Egyéni CIDR a számítási és cns csomópontokhoz |

   ![Egyéni IP-tartomány kínálása az infrastruktúrára](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**Az OpenShift-tárolóplatform 3.11-es verziója**

Adja meg a bemeneti paraméterek értékeit, és kattintson az **OK gombra.**

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| OpenShift rendszergazdai felhasználói jelszó | A kezdeti OpenShift-felhasználó jelszava.  Ez a felhasználó lesz a fürt rendszergazdája is |
| OpenShift rendszergazdai felhasználói jelszó megerősítése | Az OpenShift rendszergazdai felhasználói jelszavának újraírása |
| Red Hat Subscription Manager felhasználónév | Felhasználónév a Red Hat-előfizetés vagy szervezeti azonosító eléréséhez.  Ez a hitelesítő adat az RHEL-példány regisztrálására szolgál az előfizetésben, és a Microsoft vagy a Red Hat nem tárolja |
| Red Hat Subscription Manager felhasználói jelszó | A Red Hat-előfizetés vagy aktiválási kulcs eléréséhez szükséges jelszó.  Ez a hitelesítő adat az RHEL-példány regisztrálására szolgál az előfizetésben, és a Microsoft vagy a Red Hat nem tárolja |
| Red Hat Előfizetés-kezelő OpenShift készlet azonosítója | OpenShift container platform jogosultságot tartalmazó készletazonosító. Győződjön meg arról, hogy elegendő openshift tárolóplatformmal rendelkezik a fürt telepítéséhez |
| Red Hat Előfizetés-kezelő OpenShift készletazonosító bróker / fő csomópontok | A Broker/ Fő csomópontok OpenShift container platform jogosultságait tartalmazó készletazonosító. Győződjön meg arról, hogy elegendő jogosultsággal rendelkezik az OpenShift tárolóplatformból a fürt telepítéséhez. Ha nem használja a bróker / törzskészlet azonosítóját, adja meg az alkalmazáscsomópontok készletazonosítóját |
| Az Azure felhőszolgáltató konfigurálása | Állítsa be az OpenShift et az Azure Cloud Provider használatára. Ha az Azure lemez csatolása állandó kötetek használatával szükséges.  Az alapértelmezett érték: Igen |
| Az Azure AD Szolgáltatás egyszerű ügyfélazonosítójának GUID azonosítója | Az Azure AD szolgáltatás vezető ügyfélazonosítójának GUID azonosítója – más néven AppID. Csak akkor szükséges, ha az Azure Cloud Provider beállítása **Igen** |
| Az Azure AD Szolgáltatás egyszerű ügyfélazonosítójának titkos titka | Az Azure AD Szolgáltatás elsődleges ügyfélazonosítójának titkos titka. Csak akkor szükséges, ha az Azure Cloud Provider beállítása **Igen** |
 
   ![OpenShift panel ajánlata](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**További beállítások**

A További beállítások panel lehetővé teszi a CNS konfigurálását a glusterfs tároláshoz, naplózáshoz, metrikákhoz és útválasztó-altartományhoz.  Az alapértelmezett beállítás nem telepíti ezeket a beállításokat, és tesztelési célokra nip.io használja az útválasztó altartományaként. A cns engedélyezése három további számítási csomópontot telepít három további csatlakoztatott lemezzel, amelyek glusterfs podokat üzemeltetnek.  

Adja meg a bemeneti paraméterek értékeit, és kattintson az **OK gombra.**

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| Tároló natív tárolójának konfigurálása (CNS) | Telepíti a cns-t az OpenShift fürtben, és engedélyezi tárolóként. Alapértelmezett lesz, ha az Azure-szolgáltató le van tiltva |
| Fürtnaplózás konfigurálása | Az EFK naplózási funkció telepítése a fürtbe.  Az EFK-podok üzemeltetéséhez megfelelő infra csomópontok mérete |
| Metrikák konfigurálása a fürthöz | Hawkular metrikák telepítése az OpenShift-fürtbe.  A Hawkular metrics podok üzemeltetéséhez megfelelő infra csomópontok mérete |
| Alapértelmezett útválasztó-altartomány | Válassza ki a nipio teszteléshez vagy egyéni adja meg a saját aldomain éles |
 
   ![Kínáljon további pengét](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**További beállítások - További paraméterek**

| Bemeneti paraméter | Paraméter leírása |
|-----------------------|-----------------|
| (KN) Csomópont mérete | Fogadja el az alapértelmezett csomópontméretet, vagy válassza a **Méret módosítása** lehetőséget új virtuális gépméret kiválasztásához |
| Adja meg az egyéni altartományt | Az alkalmazások openshift fürt útválasztón keresztüli kiteszik az okat.  Ügyeljen arra, hogy létrehozza a megfelelő helyettesítő DNS-bejegyzést] |
 
   ![Ajánlat további cns telepítése](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**Összefoglalás**

Az ellenőrzés ebben a szakaszban történik, hogy ellenőrizze az alapvető kvóta elegendő a fürthöz kiválasztott virtuális gépek teljes számának üzembe helyezéséhez.  Tekintse át az összes megadott paramétert.  Ha a bemenetek elfogadhatók, a folytatáshoz kattintson az **OK** gombra.

   ![Ajánlat összefoglaló panel](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**Vásárlás**

Erősítse meg a kapcsolattartási adatokat a Vásárlás lapon, és kattintson a **Vásárlás** gombra a használati feltételek elfogadásához és az OpenShift container platformfürt telepítésének megkezdéséhez.

   ![Ajánlat vásárlási panel](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>Csatlakozás az OpenShift fürthöz

Amikor a központi telepítés befejeződik, olvassa be a kapcsolatot a központi telepítés kimeneti szakaszából. Csatlakozzon az OpenShift konzolhoz a böngészővel az **OpenShift konzol URL-címének**használatával. ssh-t is elérhet a Bástya gazdatesthez. Az alábbiakban egy példa látható, amikor a rendszergazdai felhasználónév clusteradmin, és a megerősített nyilvános IP DNS-fqdn bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az [az csoport törlése](/cli/azure/group) paranccsal távolítsa el az erőforráscsoportot, az OpenShift-fürtöt és az összes kapcsolódó erőforrást, ha már nincs rájuk szükség.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>További lépések

- [Telepítés utáni feladatok](./openshift-container-platform-3x-post-deployment.md)
- [Az OpenShift azure-beli telepítésének hibái](./openshift-container-platform-3x-troubleshooting.md)
- [Az OpenShift container platform első lépései](https://docs.openshift.com)
- 