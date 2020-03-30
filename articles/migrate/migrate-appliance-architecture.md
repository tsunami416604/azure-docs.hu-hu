---
title: Azure Migrate berendezés architektúra
description: Áttekintést nyújt az Azure Áttelepítési készülék kiszolgáló felmérésés áttelepítése során használt.
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: d55d123bb056b46b5e78dd8ac836eeaf9b42fe70
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389018"
---
# <a name="azure-migrate-appliance-architecture"></a>Azure Migrate berendezés architektúra

Ez a cikk ismerteti az Azure Migrate berendezés architektúráját és folyamatait. Az Azure Migrate készülék egy könnyű berendezés, amely a helyszínen üzembe helyezett, a virtuális gépek és a fizikai kiszolgálók felderítésére az Azure-ba való migráláshoz. 

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek

Az Azure Migrate készülék a következő forgatókönyvekben használatos.

**Forgatókönyv** | **Eszköz** | **Alkalmazási cél** 
--- | --- | ---
**VMware VM értékelés** | Azure Áttelepítés:Kiszolgálófelmérés | Fedezze fel a VMware virtuális gépeket.<br/><br/> Fedezze fel a gépalkalmazásokat és függőségeket.<br/><br/> Gyűjtse össze a gép metaadatait és a teljesítménymetaadatait, és küldje el az Azure-ba.
**VMware virtuális gép áttelepítése (ügynök nélküli)** | Azure áttelepítése:Kiszolgáló áttelepítése | Fedezze fel a VMware virtuális gépeket<br/><br/>  VMware virtuális gépek replikálása [ügynök nélküli áttelepítéssel.](server-migrate-overview.md)
**Hyper-V VM értékelés** | Azure Áttelepítés:Kiszolgálófelmérés | Fedezze fel a Hyper-V virtuális gépeket.<br/><br/> Gyűjtse össze a gép metaadatait és a teljesítménymetaadatait, és küldje el az Azure-ba.
**Fizikai gép** |  Azure Áttelepítés:Kiszolgálófelmérés |  Fedezze fel a fizikai szervereket.<br/><br/> Gyűjtse össze a gép metaadatait és a teljesítménymetaadatait, és küldje el az Azure-ba.

## <a name="appliance-components"></a>A készülék alkatrészei

A készülék számos alkatrészből áll.

- **Felügyeleti alkalmazás:** Ez egy webes alkalmazás a felhasználói bevitel hez a berendezés üzembe helyezése során. Az Azure-ba való áttelepítéshez szükséges gépek értékelésekor használatos.
- **Felderítési ügynök**: Az ügynök összegyűjti a gép konfigurációs adatait. Az Azure-ba való áttelepítéshez szükséges gépek értékelésekor használatos. 
- **Értékelő ügynök**: Az ügynök teljesítményadatokat gyűjt. Az Azure-ba való áttelepítéshez szükséges gépek értékelésekor használatos.
- **DRA-ügynök:** VM-replikáció vezénylése, és koordinálja a replikált gépek és az Azure közötti kommunikációt. Csak akkor használatos, ha a VMware virtuális gépek replikálása az Azure-ba ügynök nélküli áttelepítés használatával.
- **Átjáró**: Replikált adatokküldése az Azure-ba. Csak akkor használatos, ha a VMware virtuális gépek replikálása az Azure-ba ügynök nélküli áttelepítés használatával.
- **Automatikus frissítési szolgáltatás**: Frissíti a készülék összetevőit (24 óránként fut).



## <a name="appliance-deployment"></a>A berendezés üzembe helyezése

- Az Azure Migrate készülék beállítható egy sablon t [a Hyper-V](how-to-set-up-appliance-hyper-v.md) vagy [a VMware](how-to-set-up-appliance-vmware.md) vagy egy PowerShell script telepítő [vmware/Hyper-V](deploy-appliance-script.md), és [a fizikai kiszolgálók.](how-to-set-up-appliance-physical.md) 
- A készüléktámogatási követelmények és a telepítési előfeltételek a [készülék támogatási mátrixában](migrate-appliance.md)vannak összefoglalva.


## <a name="appliance-registration"></a>A készülék regisztrációja

A berendezés beállítása során regisztrálja a készüléket az Azure Migrate, és a táblázatban összefoglalt műveletek bekövetkeznek.

**Művelet** | **Részletek** | **Engedélyek**
--- | --- | ---
**Forrásszolgáltatók regisztrálása** | Ezek az erőforrás-szolgáltatók regisztrálva vannak a készülék beállítása során kiválasztott előfizetésben: Microsoft.OffAzure, Microsoft.Migrate és Microsoft.KeyVault.<br/><br/> Az erőforrás-szolgáltató regisztrálása úgy konfigurálja az előfizetést, hogy működjön együtt az erőforrás-szolgáltatóval. | Az erőforrás-szolgáltatók regisztrálásához közreműködői vagy tulajdonosi szerepkörre van szüksége az előfizetéshez.
**Azure AD-alkalmazáskommunikáció létrehozása** | Az Azure Migrate létrehoz egy Azure Active Directory (Azure AD) alkalmazást a készüléken futó ügynökök és az Azure-on futó megfelelő szolgáltatásaik közötti kommunikációhoz (hitelesítés és engedélyezés).<br/><br/> Ez az alkalmazás nem rendelkezik jogosultságokkal az Azure Resource Manager-hívások hoz, vagy RBAC-hozzáférés bármely erőforráson. | Az alkalmazás létrehozásához szüksége van ezekre az [engedélyekre](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) az Azure Migrate számára.
**Azure AD-alkalmazások létrehozása-kulcstároló** | Ez az alkalmazás csak a VMware virtuális gépek ügynök nélküli áttelepítéséhez jön létre az Azure-ba.<br/><br/> Kizárólag a felhasználó ügynök nélküli áttelepítési előfizetésében létrehozott kulcstartó eléréséhez használható.<br/><br/> RBAC-hozzáféréssel rendelkezik az Azure key vault (az ügyfél bérlője által létrehozott), amikor a felderítés a készülékről kezdeményezett. | Az alkalmazás létrehozásához szüksége van ezekre az [engedélyekre](tutorial-prepare-vmware.md#assign-permissions-to-register-the-appliance) az Azure Migrate számára.



## <a name="collected-data"></a>Összegyűjtött adatok

Az ügyfél által az összes telepítési forgatókönyvhöz gyűjtött adatokat a [készülék támogatási mátrixa](migrate-appliance.md)foglalja össze.

## <a name="discovery-and-collection-process"></a>Felderítési és gyűjtési folyamat

![Architektúra](./media/migrate-appliance-architecture/architecture.png)

A készülék a következő folyamat tal kommunikál a vCenter-kiszolgálókkal és a Hyper-V gazdagépekkel/fürttel.

1. **Felderítés megkezdése**:
    - Amikor elindítja a discovery a Hyper-V készülék, kommunikál a Hyper-V állomások WinRM portok 5985 (HTTP) és 5986 (HTTPS).
    - Amikor elindítja a felderítést a VMware-készüléken, alapértelmezés szerint kommunikál a vCenter-kiszolgálóval a 443-as TCP-porton. Ha a vCenter-kiszolgáló egy másik porton figyel, konfigurálhatja azt a készülék webalkalmazásban.
2. **Metaadatok és teljesítményadatok gyűjtése:**
    - A készülék egy közös információs modell (CIM) munkamenetet használ a Hyper-V VM adatok gyűjtésére a Hyper-V állomásról az 5985-ös és az 5986-os porton.
    - A készülék alapértelmezés szerint a 443-as porttal kommunikál, hogy vmware vm-adatokat gyűjtsön a vCenter Server kiszolgálóról.
3. **Adatok küldése:** A készülék elküldi az összegyűjtött adatokat az Azure Migrate Server Assessment és az Azure Migrate Server Migration ssl-porton keresztül 443. A készülék az interneten keresztül csatlakozhat az Azure-hoz, vagy használhatja az ExpressRoute-ot nyilvános/Microsoft-társviszony-létesítéssel.
    - A teljesítményadatok hoz a készülék valós idejű kihasználtsági adatokat gyűjt.
        - A VMware teljesítményadatait 20 másodpercenként, a Hyper-V esetében pedig 30 másodpercenként gyűjtjük a teljesítményadatokat az egyes teljesítménymutatók esetében.
        - Az összegyűjtött adatok at göngyölítve 10 percig egyetlen adatpontot hozlétre.
        - A maximális kihasználtsági érték az összes 20/30 másodperces adatpont közül van kiválasztva, és elküldi az Azure-nak az értékelés kiszámításához.
        - Az értékelési tulajdonságokban megadott percentilis érték (50th/90th/95th/99th) alapján a tízperces pontok növekvő sorrendben vannak rendezve, és a megfelelő percentilis érték az értékelés kiszámításához használatosak.
    - Server Migration esetén a készülék megkezdi a virtuális gép adatok gyűjtését, és replikálja azOkat az Azure-ba.
4. **Felmérheti és áttelepítheti:** Most már létrehozhat értékeléseket a készülék által az Azure Migrate Server Assessment használatával gyűjtött metaadatokból. Emellett a VMware virtuális gépek áttelepítése is megkezdheti az Azure Áttelepítési kiszolgáló áttelepítése használatával ügynök nélküli virtuális gép replikációjának vezénylése.





## <a name="appliance-upgrades"></a>A készülék frissítései

A készülék frissítése az Azure Migrate ügynökök a készüléken futó frissítéseket. Ez automatikusan megtörténik, mert az automatikus frissítés alapértelmezés szerint engedélyezve van a készüléken. Az alapértelmezett beállítás módosításával manuálisan frissítheti az ügyintézőket.

A beállításjegyzékben az automatikus frissítést úgy kapcsolja ki, hogy a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance "AutoUpdate" billentyűt 0-ra (Duplaszó) állítja.

 

## <a name="next-steps"></a>További lépések

[Tekintse át](migrate-appliance.md) a készülék támogatási mátrixát.

