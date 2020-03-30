---
title: Portál előkészítése a StorSimple virtuális tömbhöz
description: A StorSimple virtuális tömb üzembe helyezésének első oktatóanyaga magában foglalja az Azure Portal előkészítését
author: alkohli
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f2c7a6f9c1146627f81dc20386c59a91724b245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254533"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>StorSimple virtuális tömb telepítése – Az Azure-portál előkészítése

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)


## <a name="overview"></a>Áttekintés

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Ez az első cikk a telepítési oktatóanyagok sorozatában, amely a virtuális tömb fájlkiszolgálóként vagy iSCSI-kiszolgálóként az Erőforrás-kezelő modell használatával történő teljes telepítéséhez szükséges. Ez a cikk a StorSimple Eszközkezelő szolgáltatás létrehozásához és konfigurálásához szükséges előkészítést ismerteti a virtuális tömb kiépítése előtt. Ez a cikk a központi telepítési konfigurációs ellenőrzőlistára és a konfigurációs előfeltételekre mutató hivatkozásokat is tartalmaz.

A beállítási és konfigurációs folyamat befejezéséhez rendszergazdai jogosultságok szükségesek. Javasoljuk, hogy a telepítés megkezdése előtt tekintse át a központi telepítési konfigurációs ellenőrzőlistát. A portál előkészítése kevesebb mint 10 percet vesz igénybe.

A jelen cikkben közzétett információk a StorSimple virtuális tömbök azure-portálon és a Microsoft Azure Government Cloud ban történő üzembe helyezésére vonatkoznak.

### <a name="get-started"></a>Bevezetés
A központi telepítési munkafolyamat a portál előkészítéséből, a virtualizált környezetben egy virtuális tömb kiépítéséből és a telepítés befejezéséből áll. A StorSimple virtuális tömb fájlkiszolgálóként vagy iSCSI-kiszolgálóként történő üzembe helyezésének első lépéseihez a következő táblázatos erőforrásokra kell hivatkoznia.

#### <a name="deployment-articles"></a>Telepítési cikkek

A StorSimple virtuális tömb üzembe helyezéséhez tekintse meg az alábbi cikkeket az előírt sorrendben.

| **#** | **Ebben a lépésben** | **Te ezt ...** | **És használd ezeket a dokumentumokat.** |
| --- | --- | --- | --- |
| 1. |**Az Azure Portal beállítása** |Hozza létre és konfigurálja a StorSimple Eszközkezelő szolgáltatást a StorSimple virtuális tömb kiépítése előtt. |[A portál előkészítése](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**A virtuális tömb kiépítése** |Hyper-V esetén windows Server 2012 R2, Windows Server 2012 vagy Windows Server 2008 R2 rendszeren a Hyper-V rendszert futtató gazdarendszeren létesítsen és csatlakozzon egy StorSimple virtuális tömbhöz. <br></br> <br></br> VMware esetén a VMware ESXi 5.0, 5.5, 6.0 vagy 6.5 rendszert futtató gazdarendszeren egy StorSimple virtuális tömbhöz való csatlakozás és csatlakozás.<br></br> |[Virtuális tömb kiépítése a Hyper-V-ben](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Virtuális tömb kiépítése a VMware-ben](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**A virtuális tömb beállítása** |A fájlkiszolgálón hajtsa végre a kezdeti telepítést, regisztrálja a StorSimple fájlkiszolgálót, és fejezze be az eszköz telepítését. Ezután üzembe helyezheti az SMB-megosztásokat. <br></br> <br></br> Az iSCSI-kiszolgálón hajtsa végre a kezdeti telepítést, regisztrálja a StorSimple iSCSI-kiszolgálót, és fejezze be az eszköz telepítését. Ezután iSCSI-köteteket építhet ki. |[Virtuális tömb beállítása fájlkiszolgálóként](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Virtuális tömb beállítása iSCSI-kiszolgálóként](storsimple-virtual-array-deploy3-iscsi-setup.md) |

Most megkezdheti az Azure Portal beállítását.

## <a name="configuration-checklist"></a>Konfigurációs ellenőrzőlista

A konfigurációs ellenőrzőlista ismerteti azokat az információkat, amelyeket össze kell gyűjtenie, mielőtt konfigurálná a szoftvert a StorSimple virtuális tömbön. Az adatok idő előtti előkészítése segít egyszerűsíteni a StorSimple eszköz üzembe helyezésének folyamatát a környezetben. Attól függően, hogy a StorSimple virtuális tömb fájlkiszolgálóként vagy iSCSI-kiszolgálóként van-e telepítve, az alábbi ellenőrzőlisták egyikére van szükség.

* Töltse le a [StorSimple virtual array file server konfigurációs ellenőrzőlistáját.](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf)
* Töltse le a [StorSimple virtual array iSCSI-kiszolgáló konfigurációs ellenőrzőlistáját.](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf)

## <a name="prerequisites"></a>Előfeltételek

Itt megtalálja a StorSimple Eszközkezelő szolgáltatás, a StorSimple virtuális tömb és az adatközpont-hálózat konfigurációs előfeltételeit.

### <a name="for-the-storsimple-device-manager-service"></a>A StorSimple-eszközkezelő szolgáltatás esetén

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Rendelkezik Microsoft-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
* Rendelkezik Microsoft Azure Storage-fiókkal és a hozzá szükséges hozzáférési hitelesítő adatokkal.
* A Microsoft Azure-előfizetést engedélyezni kell a StorSimple Eszközkezelő szolgáltatáshoz.

### <a name="for-the-storsimple-virtual-array"></a>A StorSimple virtuális tömbhöz

Virtuális tömb telepítése előtt győződjön meg arról, hogy:

* Hozzáférése van egy Hyper-V rendszert futtató gazdarendszerhez Windows Server 2008 R2 vagy újabb rendszeren, illetve vmware-t (ESXi 5.0, 6.0 vagy 6.5), amelyek egy eszköz létesítéséhez használhatók.
* A gazdarendszer a következő erőforrásokat tudja a virtuális tömb kiépítésére fordítani:
  
  * Legalább 4 mag.
  * Legalább 8 GB RAM. Ha a virtuális tömböt fájlkiszolgálóként kívánja konfigurálni, a 8 GB 2 millió fájlt támogat. Szüksége van 16 GB RAM-ra a 2-4 millió fájl támogatásához.
  * Egy hálózati adapter.
  * 500 GB-os virtuális lemez a rendszeradatokhoz.

### <a name="for-the-datacenter-network"></a>Adatközponti hálózat

Mielőtt hozzákezd, győződjön meg az alábbiakról:

* Az adatközpontban lévő hálózat a StorSimple-eszköz hálózati követelményeinek megfelelően van konfigurálva. További információt a [StorSimple virtuális tömb rendszerkövetelményei című témakörben talál.](storsimple-ova-system-requirements.md)
* A StorSimple virtuális tömb dedikált 5 Mbps-os (vagy több) internet-sávszélességgel rendelkezik. Ez a sávszélesség nem osztható meg más alkalmazásokkal.

## <a name="step-by-step-preparation"></a>Lépésről lépésre történő előkészítés

Az alábbi lépésenkénti útmutatóval előkészítheti a portált a StorSimple Eszközkezelő szolgáltatásra.

## <a name="step-1-create-a-new-service"></a>1. lépés: Új szolgáltatás létrehozása

A StorSimple Eszközkezelő szolgáltatás egyetlen példánya több StorSimple virtuális tömböt is kezelhet. Az alábbi lépések végrehajtásával hozza létre a StorSimple-eszközkezelő szolgáltatás egy példányát. Ha rendelkezik egy meglévő StorSimple Eszközkezelő szolgáltatással a virtuális tömbök kezeléséhez, hagyja ki ezt a lépést, és folytassa [a 2.](#step-2-get-the-service-registration-key)

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
> A szolgáltatás regisztrációs kulcs a StorSimple Eszközkezelő összes eszközregisztrálására szolgál, amelyeknek regisztrálniuk kell a StorSimple Eszközkezelő szolgáltatással.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>3. lépés: A virtuális tömb képének letöltése

Miután rendelkezik a szolgáltatás regisztrációs kulcs, le kell töltenie a megfelelő virtuális tömb lemezképet egy virtuális tömb a gazdarendszer kiépítése. A virtuális tömbrendszer-lemezképek operációs rendszerspecifikusak, és letölthetők az Azure Portal gyorsindítási lapjáról.

> [!IMPORTANT]
> A StorSimple virtuális tömbön futó szoftver csak a StorSimple Eszközkezelő szolgáltatással használható.
> 
> 

Hajtsa végre a következő lépéseket az [Azure Portalon](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>A virtuális tömb képének beszerezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). 
2. Az Azure Portalon kattintson **a Tallózás > StorSimple eszközkezelők**.
3. Válasszon ki egy meglévő StorSimple Eszközkezelő szolgáltatást. A **StorSimple Eszközkezelő** panelen kattintson a **Gyorsindítás gombra.** 
4. Kattintson a Microsoft letöltőközpontból letölteni kívánt képnek megfelelő hivatkozásra. A rendszerképfájlok mérete körülbelül 4,8 GB.
   
   * Virtuális merevlemez Hyper-V-hez Windows Server 2012 és újabb rendszeren
   * Virtuális merevlemez Hyper-V-hez Windows Server 2008 R2 és újabb rendszeren
   * VMDK VMWare ESXi 5.0, 5.5, 6.0 vagy 6.5
5. Töltse le a fájlt egy helyi meghajtóra, csomagolja ki, és jegyezze fel, hol található a kicsomagolt fájl.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Választható lépés: Új tárfiók konfigurálása a szolgáltatáshoz

Ez a lépés nem kötelező, és csak akkor kell végrehajtani, ha nem engedélyezte a tárfiók automatikus létrehozását a szolgáltatással.

Ha egy másik régióban kell létrehoznia egy Azure-tárfiókot, olvassa [el a Tárfiók létrehozása](../storage/common/storage-account-create.md) című témakört a részletes utasításokhoz.

Hajtsa végre az alábbi lépéseket az [Azure Portalon](https://ms.portal.azure.com/) a StorSimple Eszközkezelő szolgáltatáslapján egy meglévő Microsoft Azure-tárfiók hozzáadásához.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Olyan tárfiók hitelesítő adatainak hozzáadása, amelyek ugyanazt az Azure-előfizetést rendelkezik, mint az Eszközkezelő szolgáltatás

1. Nyissa meg az Eszközkezelő szolgáltatást, jelölje ki és kattintson rá duplán. Ez megnyitja az **Áttekintő** panelt.
2. Válassza a **Tárfiók hitelesítő adatai a** Konfiguráció szakaszban válassza ki a **tárfiók** hitelesítő adatait.
3. Kattintson a **Hozzáadás** gombra.
4. A **Tárfiók hozzáadása** panelen tegye a következőket:
   
   1. **Előfizetés esetén**válassza az **Aktuális**lehetőséget.
   
   2. Adja meg az Azure-tárfiók nevét.
   
   3. Az **Engedélyezés** lehetőséget választva biztonságos csatornát hozhat létre a StorSimple-eszköz és a felhő közötti hálózati kommunikációhoz. Válassza **a Letiltás csak** akkor, ha magánfelhőben működik.
   
   4. Kattintson a **Hozzáadás** gombra. Értesítést kap a tárfiók sikeres létrehozása után.<br></br>
   
      ![Meglévő tárfiók hitelesítő adatainak hozzáadása](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Következő lépés

A következő lépés egy virtuális gép kiépítése a StorSimple virtuális tömbhöz. A gazdagép operációs rendszerétől függően az alábbi témakörökben tekintheti meg a részletes utasításokat:

* [StorSimple virtuális tömb kiépítése a Hyper-V-ben](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [StorSimple virtuális tömb kiépítése a VMware-ben](storsimple-virtual-array-deploy2-provision-vmware.md)

