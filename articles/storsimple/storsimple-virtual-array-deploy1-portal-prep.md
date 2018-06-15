---
title: A StorSimple virtuális tömb portál prep |} Microsoft Docs
description: A StorSimple virtuális tömb telepítendő első oktatóanyaga, amely magában foglalja a előkészítése az Azure-portálon
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
ms.openlocfilehash: 6685c5ab7768176a0c8e7084c8512d5345732d9a
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
ms.locfileid: "24526553"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>A StorSimple virtuális tömb telepítése – előkészítése az Azure-portálon

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)
## <a name="overview"></a>Áttekintés

Ez a teljesen telepítsen az adott virtuális tömb, egy fájl vagy a Resource Manager modellt használja az iSCSI-kiszolgáló szükséges az üzembehelyezési oktatóanyagok a sorozat első cikkében. Ez a cikk ismerteti a előkészületek létrehozása és konfigurálása a StorSimple eszköz Manager szolgáltatás virtuális tömb kiépítése előtt. Ez a cikk is kapcsolja ki egy üzembehelyezési konfigurációs ellenőrzőlista és konfigurációs előfeltételek.

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. Ajánlott áttekinteni a üzembehelyezési konfigurációs ellenőrzőlista megkezdése előtt. A portál előkészítése kisebb, mint 10 percet vesz igénybe.

Ez a cikk a közzétett adatokat a központi telepítést a StorSimple virtuális tömbök, az Azure portál és a Microsoft Azure Government felhő vonatkozik.

### <a name="get-started"></a>Bevezetés
A telepítési munkafolyamat előkészítése a portálon, a virtualizált környezetben egy virtuális tömb kiépítés, és a telepítés befejezése áll. Az egy fájl vagy iSCSI-kiszolgáló, a StorSimple virtuális tömb telepítésének első lépései, tekintse meg az alábbi táblázatos erőforrások kell.

#### <a name="deployment-articles"></a>Telepítési cikkek

A StorSimple virtuális tömb telepítéséhez tekintse meg a következő cikkekben által előírt sorrendben.

| **#** | **Ebben a lépésben** | **Ezt megteheti...** | **És használja ezeket a dokumentumokat.** |
| --- | --- | --- | --- |
| 1. |**Állítsa be az Azure-portálon** |Létrehozhat és konfigurálhat egy StorSimple virtuális tömb kiépítése előtt a StorSimple Device Manager szolgáltatáshoz. |[Készítse elő a portálon](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**A virtuális tömb kiépítése** |A Hyper-V kiépíteni, és kapcsolódjon a StorSimple virtuális tömb állomás operációs rendszert futtató Hyper-V a Windows Server 2012 R2, Windows Server 2012 vagy Windows Server 2008 R2 rendszeren. <br></br> <br></br> A VMware kiépíteni, és egy StorSimple virtuális tömbhöz a gazdagépen operációs rendszert futtató VMware ESXi 5.0, 5.5 vagy 6.0 kapcsolódjon.<br></br> |[A Hyper-V egy virtuális tömb kiépítése](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [VMware-ben a virtuális tömb kiépítése](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**A virtuális tömb beállítása** |A fájlkiszolgáló hajtsa végre a kezdeti beállítás regisztrálása a StorSimple fájlkiszolgáló és az eszköz beállításának befejezése. SMB-megosztások majd oszthat. <br></br> <br></br> Az iSCSI-kiszolgáló hajtsa végre a kezdeti beállítás, a StorSimple iSCSI-kiszolgáló regisztrálása és az eszköz beállításának befejezése. Az iSCSI-kötetek majd oszthat. |[Fájlkiszolgáló virtuális tömb beállítása](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Állítsa be a tömb virtuális iSCSI-kiszolgálóként](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Most már megkezdheti beállítása az Azure-portálon.

## <a name="configuration-checklist"></a>Konfigurációs ellenőrzőlista

A konfigurációs ellenőrzőlistát a kell összegyűjtenie, mielőtt konfigurálná a szoftver a StorSimple virtuális tömb a információkat ismerteti. Az információk időben előkészítése segít felgyorsítják az adott környezetben a StorSimple eszköz üzembe. Attól függően, hogy a StorSimple virtuális tömb van telepítve egy fájl vagy iSCSI-kiszolgáló, egyike szükséges a következő ellenőrzőlista.

* Töltse le a [StorSimple virtuális tömb fájl kiszolgáló konfigurációs ellenőrzőlista](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf).
* Töltse le a [StorSimple virtuális tömb iSCSI kiszolgáló konfigurációs ellenőrzőlista](http://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf).

## <a name="prerequisites"></a>Előfeltételek

Itt megtalálja az Ön a StorSimple eszköz kezelő szolgáltatás, a StorSimple virtuális tömb és az Adatközpont hálózati konfigurációs előfeltételeit.

### <a name="for-the-storsimple-device-manager-service"></a>A StorSimple-eszközkezelő szolgáltatás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Rendelkezik Microsoft-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
* Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
* A Microsoft Azure-előfizetés engedélyezni kell a StorSimple eszköz Manager szolgáltatáshoz.

### <a name="for-the-storsimple-virtual-array"></a>A StorSimple virtuális tömb

Egy virtuális tömb telepítése előtt győződjön meg arról, hogy:

* Hozzáférést egy adott gazdarendszeren futó Windows Server 2008 R2 vagy újabb Hyper-V vagy VMware (ESXi 5.0, 5.5 vagy 6.0), amely rendelkezik egy kiépítéséhez használt eszköz.
* A gazdagép rendszere tudni jelölt ki a virtuális tömb telepítéséhez a következőket:
  
  * Legalább 4 mag.
  * Legalább 8 GB RAM-mal. Ha szeretné konfigurálni a virtuális tömb fájlkiszolgálóként, a 8 GB 2 millió fájlokat támogatja. 16 GB RAM, 2 – 4 millió fájlok támogatásához szükséges.
  * Egy hálózati csatoló.
  * 500 GB-os virtuális lemez Rendszeradatok.

### <a name="for-the-datacenter-network"></a>Az Adatközpont-hálózat

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Az Adatközpont hálózati van konfigurálva, a StorSimple eszköz hálózatkezelési követelményei szerint. További információkért lásd: a [StorSimple virtuális tömb rendszerkövetelmények](storsimple-ova-system-requirements.md).
* A StorSimple virtuális tömb nem rendelkezik egy dedikált 5 MB/s internetes sávszélességet (vagy több) mindenkor. A sávszélesség nem lehet megosztva egyéb alkalmazásokkal.

## <a name="step-by-step-preparation"></a>Részletes előkészítése

Az alábbi részletes útmutatás segítségével készítse elő a portál a StorSimple eszköz Manager szolgáltatáshoz.

## <a name="step-1-create-a-new-service"></a>1. lépés: Új szolgáltatás létrehozása

Egyetlen példány futhat a StorSimple Device Manager szolgáltatás több StorSimple virtuális tömbök kezelheti. Az alábbi lépések végrehajtásával hozza létre a StorSimple-eszközkezelő szolgáltatás egy példányát. Ha egy meglévő StorSimple Device Manager szolgáltatást a virtuális tömbök kezeléséhez, a lépés kihagyása és Ugrás [2. lépés: Szolgáltatásregisztrációs kulcs lekérése](#step-2-get-the-service-registration-key).

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

Hajtsa végre a következő lépéseket a [Azure-portálon](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> A szolgáltatás regisztrációs kulcsának használt összes a StorSimple Device Manager igénylő eszközök regisztrálása a StorSimple Device Manager szolgáltatásban való regisztrálása.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>3. lépés: Töltse le a virtuális tömb kép

Miután a szolgáltatás regisztrációs kulcsának, szüksége lesz letölti a megfelelő virtuális tömb lemezképet, a rendszer a gazdagép egy virtuális tömb kiépítéséhez. A virtuális tömb lemezképek operációs rendszer adott és tölthető le: az Azure-portálon az első lépések lap.

> [!IMPORTANT]
> A StorSimple virtuális tömb futó szoftver csak a StorSimple Device Manager szolgáltatás is használhatók.
> 
> 

Hajtsa végre a következő lépéseket a [Azure-portálon](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>Virtuális tömb képlista

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). 
2. Az Azure portálon kattintson **Tallózás > StorSimple eszköz kezelői**.
3. Válasszon ki egy meglévő StorSimple Device Manager szolgáltatást. Az a **StorSimple Device Manager** panelen kattintson a **gyors üzembe helyezés**. 
4. A Microsoft Download Center a letölteni kívánt kép megfelelő hivatkozásra kattintva. A kép mérete körülbelül 4.8 GB.
   
   * A Hyper-V a Windows Server 2012 és újabb verziók VHDX
   * Virtuális merevlemez Hyper-v a Windows Server 2008 R2 és újabb verziók
   * A VMWare ESXi 5.0, 5.5 vagy 6.0 VMDK
5. Töltse le és csomagolja ki a fájlt egy helyi meghajtóra, a jegyezze fel, ahol a tömörítetlen fájl, így.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Nem kötelező lépés: a szolgáltatás egy új tárfiók konfigurálása

Ez a lépés nem kötelező, és csak akkor, ha nem engedélyezte a tárfiók automatikus létrehozását a szolgáltatással történjen.

Ha az Azure storage-fiók létrehozása egy másik régióban van szüksége, tekintse meg [a storage-fiók létrehozása](../storage/common/storage-create-storage-account.md#create-a-storage-account) részletes útmutatásait.

Hajtsa végre a következő lépéseket a [Azure-portálon](https://ms.portal.azure.com/) egy meglévő Microsoft Azure storage-fiók hozzáadása a StorSimple Device Manager szolgáltatás oldalon.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>A tárolási fiók hitelesítő adatait, amely rendelkezik az Eszközkezelő szolgáltatásként az azonos Azure-előfizetés hozzáadása

1. Keresse meg az Eszközkezelő szolgáltatás, válassza ki, és kattintson rá duplán. Ekkor megnyílik a **áttekintése** panelen.
2. Válassza ki **Tárfiók hitelesítő adatainak** belül a **konfigurációs** szakasz.
3. Kattintson az **Add** (Hozzáadás) parancsra.
4. Az a **tárfiók hozzáadása** panelen tegye a következőket:
   
    1. A **előfizetés**, jelölje be **aktuális**.
   
    2. Adja meg az Azure storage-fiók nevét.
   
    3. Válassza ki **engedélyezése** a StorSimple eszköz és a felhő közötti hálózati kommunikációhoz biztonságos csatornát létrehozni. Válassza ki **letiltása** csak akkor, ha magánfelhőben tevékenykedik.
   
    4. Kattintson az **Add** (Hozzáadás) parancsra. Értesítés jelenik meg a tárfiók sikeres létrehozása után.<br></br>
   
     ![Adja hozzá egy meglévő storage-fiók hitelesítő adatait](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Következő lépés

A következő lépés, hogy osszon ki a virtuális gépek a StorSimple virtuális tömbhöz. Attól függően, hogy a gazda operációs rendszer a részletes ismertetését lásd:

* [A Hyper-V egy StorSimple virtuális tömb kiépítése](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Kiépíteni a StorSimple virtuális tömb VMware-ben](storsimple-virtual-array-deploy2-provision-vmware.md)

