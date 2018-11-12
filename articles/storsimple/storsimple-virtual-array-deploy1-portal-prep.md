---
title: A StorSimple Virtual Array portál előkészítő |} A Microsoft Docs
description: A StorSimple virtual array üzembe az első oktatóanyag magában foglalja a előkészítése az Azure Portalon
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/14/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2d87642b93d58d92660a2df71f2561ffe502315a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51257265"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>A StorSimple Virtual Array üzembe helyezése – előkészítése az Azure Portalon

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)
## <a name="overview"></a>Áttekintés

Ez a teljes telepítéséhez a virtuális tömb egy fájlkiszolgáló vagy a Resource Manager modellel iSCSI-kiszolgáló szükséges az üzembehelyezési oktatóanyagok a sorozat első cikkében. Ez a cikk ismerteti az előkészítés, létrehozása és konfigurálása a StorSimple-Eszközkezelő szolgáltatás a virtuális tömb kiépítése előtt szükséges. Ez a cikk is hivatkozik egy üzembehelyezési konfigurációs ellenőrzőlista és konfigurációs előfeltételek.

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. Azt javasoljuk, hogy megkezdése előtt ellenőrizze az üzembehelyezési konfigurációs ellenőrzőlista. A portál előkészítése kevesebb mint 10 percet vesz igénybe.

Az ebben a cikkben közzétett adatokat a központi telepítést a StorSimple Virtual Arrayt, az Azure portal és a Microsoft Azure Government Cloud vonatkozik.

### <a name="get-started"></a>Bevezetés
Az üzembe helyezést megvalósító munkafolyamat a portál előkészítése, egy virtuális tömböt a a virtualizált környezet kiépítése és a telepítés befejezése áll. Ismerkedés a StorSimple Virtual Array telepítési egy fájlkiszolgáló vagy iSCSI-kiszolgálót, tekintse meg a következő táblázatos kell.

#### <a name="deployment-articles"></a>Telepítési cikkek

A StorSimple Virtual Array üzembe helyezéséhez a az előírt feladatütemezés a következő cikkekben talál.

| **#** | **Ebben a lépésben** | **Ezt megteheti...** | **És ezeket a dokumentumokat.** |
| --- | --- | --- | --- |
| 1. |**Állítsa be az Azure Portalon** |Létrehozhat és konfigurálhat egy StorSimple Virtual Array kiépítése előtt a StorSimple-Eszközkezelő szolgáltatáshoz. |[A portál előkészítése](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**A virtuális tömb** |A Hyper-V üzembe helyezése, és csatlakozzon a StorSimple Virtual Array állomás operációs rendszert futtató Hyper-V Windows Server 2012 R2, Windows Server 2012 vagy Windows Server 2008 R2 rendszeren. <br></br> <br></br> VMware-ről üzembe helyezése, és csatlakozzon a StorSimple Virtual Array a gazdagép operációs rendszert futtató VMware ESXi 5.0-s, 5.5 és 6.0.<br></br> |[A Hyper-V virtuális tömb létrehozása](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Virtuális tömb létrehozása VMware-ben](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**Állítsa be a virtuális tömb** |A fájlkiszolgáló hajtsa végre a kezdeti beállítás, Regisztráljon a StorSimple fájlkiszolgáló és az eszköz beállításának befejezése. Ezután üzembe helyezheti az SMB-megosztásokat. <br></br> <br></br> Az iSCSI-kiszolgáló hajtsa végre a kezdeti beállítás, a StorSimple iSCSI-kiszolgáló regisztrálása és az eszköz beállításának befejezése. Az iSCSI-kötet helyezhet majd. |[Állítsa be a virtuális tömb fájlkiszolgálóként](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Állítsa be a virtuális tömb iSCSI-kiszolgálóként](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Most megkezdheti az Azure Portal beállítását.

## <a name="configuration-checklist"></a>Konfigurációs ellenőrzőlista

A konfigurációs ellenőrzőlista azokat az információkat kell gyűjtenie a StorSimple Virtual Array az a szoftver konfigurálása előtt. Az információk időben előkészítése leegyszerűsíti a folyamatot a környezetben a StorSimple eszköz üzembe helyezésének. Attól függően, hogy a StorSimple Virtual Array egy fájlkiszolgáló vagy iSCSI-kiszolgáló van telepítve, kell egyet a következő ellenőrzőlisták.

* Töltse le a [StorSimple Virtual Array fájl kiszolgáló konfigurációs ellenőrzőlista](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Töltse le a [StorSimple Virtual Array iSCSI-kiszolgáló konfigurációs ellenőrzőlista](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Előfeltételek

Itt találja az adatközponti hálózathoz, a StorSimple-Eszközkezelő szolgáltatás és a StorSimple Virtual Array konfigurációs előfeltételeit.

### <a name="for-the-storsimple-device-manager-service"></a>A StorSimple-eszközkezelő szolgáltatás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Rendelkezik Microsoft-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
* Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
* A Microsoft Azure-előfizetésében engedélyezni kell a StorSimple-Eszközkezelő szolgáltatáshoz.

### <a name="for-the-storsimple-virtual-array"></a>A StorSimple virtuális tömb

Mielőtt telepít egy virtuális tömböt, ellenőrizze, hogy:

* A Hyper-V a Windows Server 2008 R2 vagy újabb rendszerű gazdagép rendszere vagy VMware-alapú (ESXi 5.0-s, 5.5 és 6.0-s), amely hozzáféréssel rendelkezik egy kiépítéséhez használt eszköz.
* A gazdagép rendszere viszont rendelnie a virtuális tömb telepítéséhez a következőket:
  
  * Legalább 4 mag.
  * Legalább 8 GB RAM. Ha azt tervezi, konfigurálja a virtuális tömb fájlkiszolgálóként, akkor 8 GB 2 millió fájlokat támogatja. 16 GB RAM, 2 – 4 millió fájl támogatásához szükséges.
  * Egy hálózati adapter.
  * 500 GB-os virtuális lemez a rendszer adatokat.

### <a name="for-the-datacenter-network"></a>Adatközponti hálózat

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* A hálózat, az adatközpontban a StorSimple-eszköz hálózati követelményeinek megfelelően van konfigurálva. További információkért lásd: a [StorSimple Virtual Array rendszerkövetelményei](storsimple-ova-system-requirements.md).
* A StorSimple Virtual Array rendelkezik egy dedikált 5 MB/s internetes sávszélesség (vagy még több) mindenkor. Ez a sávszélesség nem osztható meg más alkalmazásokkal.

## <a name="step-by-step-preparation"></a>Részletes előkészítése

Használja az alábbi részletes útmutatás a portál előkészítése a StorSimple-Eszközkezelő szolgáltatásban.

## <a name="step-1-create-a-new-service"></a>1. lépés: Új szolgáltatás létrehozása

A StorSimple-Eszközkezelő szolgáltatás egyetlen példánya több StorSimple Virtual Arrayt kezelheti. Az alábbi lépések végrehajtásával hozza létre a StorSimple-eszközkezelő szolgáltatás egy példányát. Ha rendelkezik egy meglévő StorSimple-Eszközkezelő szolgáltatás kezelése a virtuális tömböket, kihagyhatja ezt a lépést, és folytassa [2. lépés: Szolgáltatásregisztrációs kulcs lekérése](#step-2-get-the-service-registration-key).

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> Ha nem engedélyezte a tárfiók automatikus létrehozását a szolgáltatással, akkor legalább egy tárfiókot létre kell hoznia, miután sikeresen létrehozott egy szolgáltatást.
> 
> * Ha nem hozott létre automatikusan egy tárfiókot, a részletes utasításokat az [Új tárfiók konfigurálása a szolgáltatáshoz](#optional-step-configure-a-new-storage-account-for-the-service) című szakaszban tekintheti meg.
> * Ha engedélyezte a tárfiók automatikus létrehozását, folytassa a [2. lépés: Szolgáltatásregisztrációs kulcs lekérése](#step-2-get-the-service-registration-key) című szakasszal.
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>2. lépés: Szolgáltatásregisztrációs kulcs lekérése

Ha a StorSimple-eszközkezelő szolgáltatás működik és elérhető, le kell kérnie a szolgáltatásregisztrációs kulcsot. Ezzel a kulccsal regisztrálhatja és csatlakoztathatja StorSimple eszközét a szolgáltatáshoz.

Hajtsa végre a következő lépéseket az [Azure Portalon](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> A szolgáltatás regisztrációs kulcsával regisztrálható a StorSimple-Eszközkezelő az összes olyan eszköz, szeretne regisztrálni a StorSimple-Eszközkezelő szolgáltatásban szolgál.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>3. lépés: A virtuális tömb lemezképének letöltése

Miután a szolgáltatás regisztrációs kulcsával, szüksége lesz a virtuális tömb létrehozása a fogadó rendszeren a megfelelő virtuális tömb lemezképének letöltése. A virtuális tömb lemezképeket adott operációs rendszer és letölthető a gyors üzembe helyezés lap az Azure Portalon.

> [!IMPORTANT]
> A StorSimple Virtual Array futó szoftver csak a StorSimple-Eszközkezelő szolgáltatással használható.
> 
> 

Hajtsa végre a következő lépéseket az [Azure Portalon](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>Virtuális tömb lemezképének lekérési

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). 
2. Az Azure Portalon kattintson a **Tallózás > StorSimple-Eszközkezelők**.
3. Válasszon ki egy meglévő StorSimple-Eszközkezelő szolgáltatást. Az a **StorSimple-Eszközkezelő** panelen kattintson a **gyors üzembe helyezés**. 
4. A kép, töltse le a Microsoft Download Center kívánt megfelelő hivatkozásra kattintva. A rendszerképfájlok mérete körülbelül 4,8 GB.
   
   * VHDX Hyper-v a Windows Server 2012 és újabb verziók
   * VHD Hyper-v a Windows Server 2008 R2 és újabb verziók
   * VMDK a VMWare ESXi 5.0-s, 5.5-ös vagy 6.0
5. Töltse le a fájlt egy helyi meghajtóra, csomagolja ki, és jegyezze fel, hol található a kicsomagolt fájl.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Nem kötelező lépés: a szolgáltatás egy új tárfiók konfigurálása

Ez a lépés nem kötelező, és csak akkor, ha nem engedélyezte a tárfiók automatikus létrehozását a szolgáltatással kell végrehajtani.

Ha az Azure storage-fiók létrehozása egy másik régióban van szüksége, tekintse meg [storage-fiók létrehozása](../storage/common/storage-quickstart-create-account.md) részletes útmutatásait.

Hajtsa végre a következő lépéseket a [az Azure portal](https://ms.portal.azure.com/) egy meglévő Microsoft Azure storage-fiók hozzáadása a StorSimple-Eszközkezelő szolgáltatás oldalon.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>A tárfiók-hitelesítő adat hozzáadása, amely rendelkezik az Azure-előfizetéshez a Device Manager szolgáltatás

1. Keresse meg a Device Manager szolgáltatáshoz, válassza ki, és kattintson rá duplán. Ekkor megnyílik a **áttekintése** panelen.
2. Válassza ki **Tárfiók hitelesítő adatainak** belül a **konfigurációs** szakaszban.
3. Kattintson a **Hozzáadás** parancsra.
4. Az a **tárfiók hozzáadása** panelen tegye a következőket:
   
    1. A **előfizetés**válassza **aktuális**.
   
    2. Adja meg az Azure storage-fiók nevét.
   
    3. Válassza ki **engedélyezése** a StorSimple-eszköz és a felhő közötti hálózati kommunikációhoz biztonságos csatornát létrehozni. Válassza ki **letiltása** csak akkor, ha a magánfelhő belül működik.
   
    4. Kattintson a **Hozzáadás** parancsra. A storage-fiók sikeres létrehozása után értesítést kap.<br></br>
   
     ![Egy meglévő tárfiók-hitelesítő adat hozzáadása](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Következő lépés

A következő lépés, hogy a StorSimple Virtual Array üzembe helyezhet egy virtuális gépet. A gazdagép operációs rendszerétől függően az alábbi témakörökben tekintheti meg a részletes utasításokat:

* [A Hyper-V-ben a StorSimple Virtual Array üzembe helyezése](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [VMware-ben a StorSimple Virtual Array üzembe helyezése](storsimple-virtual-array-deploy2-provision-vmware.md)

